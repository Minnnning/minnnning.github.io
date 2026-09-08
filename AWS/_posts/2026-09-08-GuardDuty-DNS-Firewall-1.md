---
layout: post
title: "GuardDuty가 피싱 도메인을 잡았는데 NACL IP 차단이 안 먹혔다"
date: 2026-09-08 00:00:00 +0900
categories: [AWS]
tags: [guardduty, route53, dns-firewall, security]
description: "GuardDuty 피싱 도메인 탐지 알럿. IP 차단이 왜 무력했고, Route 53 DNS Firewall로 설정하게된 이유."
image: "https://image.minnnning.kr/images/2026/09/20260908-232235-85d752.webp"
---

갑자기 슬랙에 GuardDuty 알럿이 하나 떴다. 고객사 VPC 안에서 피싱/도박 계열 도메인으로 DNS 질의가 나가고 있다는 내용이었다. finding type은 `Trojan:EC2/PhishingDomainRequest!DNS`, 기본 심각도 High.

일단 급한 대로 해당 인스턴스에서 `iptables`로 목적지 IP를 막았다. NACL로 올려서 막을까 하다가, NACL은 서브넷 전체에 걸리는 거라 잘못 건드리면 정상 트래픽까지 같이 날아갈 것 같아서 호스트 레벨에서 먼저 끊어서 차단 설정을 했다.

그런데 다음 날 아침에 다시 보니 **같은 도메인으로 질의가 계속 나가고 있었다.** 분명 막았는데?

이 글은 그 "분명 막았는데"를 파고들다가 Route 53 Resolver DNS Firewall까지 설정을 진행했다(추가 비용이 생각보다 별로 안나옴).결론부터 말하면 차단 자체보다 **차단을 어느 계층에서 하느냐**, 그리고 **관리형 리스트를 얼마나 믿을 수 있느냐**가 진짜 문제였다.

---

## IP로 막으면 되는 거 아닌가?

딱보고 아 이거 아웃바운드 차단하면 되겠네 파악해서 호스트 레벨 에서 해당 ip를 iptables에서 일단 ip 차단을 진행했다.

근데 안됨... 왜지?

![images-3](https://image.minnnning.kr/images/2026/09/20260908-232623-b7364c.webp){: style="display: block; margin: 0 auto; max-width: 40%;"}

호스트에서 안 되니 한 계층 올려서 **Network ACL로도 막아봤다.** 서브넷 전체에 걸리는 거라 조심스러웠는데, 어차피 안 되는 거 확인은 해야 했다.

**이것도 안 막혔다.** 그리고 이 지점에서 원인이 잡혔다.

### 알럿은 뜨는데 막을 트래픽이 VPC에 없었다

사내 VPN 게이트웨이가 EC2로 떠 있고 직원들이 여기 붙어서 일하는데, 이 VPN이 **Split Tunnel** 구성이었다. 여기서 트래픽이 두 갈래로 갈린다.

- **DNS 질의는 VPN 인스턴스를 거친다.** VPN이 DNS 서버를 물려주니까 단말의 이름 조회는 VPN을 타고 들어와 Route 53 Resolver까지 간다
- **실제 통신(HTTPS)은 단말에서 인터넷으로 직결된다.** 사내 대역이 아니니까 VPN을 탈 이유가 없다

그러니까 이런 상태였던 거다.

![스크린샷 2026-09-08 23.42.16](https://image.minnnning.kr/images/2026/09/20260908-234221-e1b66c.webp)

```
[GuardDuty 알럿]  ← DNS 질의가 VPC를 지나가니까 Resolver가 보고 탐지함
[막을 트래픽]     ← 실제 통신은 VPC를 안 지나가니까 SG·NACL이 볼 게 없음
```

**질의만으로 알럿이 뜬 것이지, 그 통신이 VPC를 지나간 게 아니었다.** iptables든 NACL이든 "VPC를 지나가는 트래픽"에 거는 규칙인데, 정작 막고 싶은 통신은 그 바깥에 있었다. 규칙이 틀린 게 아니라 **볼 수 있는 대상이 애초에 없었던** 것이다.

알럿이 뜨니까 당연히 트래픽도 VPC 안에 있을 거라고 생각했는게 그게 아니었다. **탐지된 위치와 차단 가능한 위치가 다를 수 있다**는 걸 이때 처음 제대로 인식했다.

그리고 이게 곧바로 답이 되기도 했다. **VPC를 지나는 게 DNS 질의뿐이라면, 우리가 차단 할 수 있는 지점도 거기뿐이다.** 질의 단계에서 막으면 단말은 IP를 못 받고, IP가 없으면 직결 통신 자체를 못한다.

결론적으로 답은 하나였다.

![images-4](https://image.minnnning.kr/images/2026/09/20260909-002742-ad2318.webp){: style="display: block; margin: 0 auto; max-width: 70%;"}

 차단 지점을 IP가 아니라 **DNS 질의 단계**로 옮기는 것.

### AWS에서 아웃바운드를 막을 수 있는 위치

| 위치 | 차단 기준 | 이번 건에 적합한가 |
|---|---|---|
| Security Group | IP/포트 (아웃바운드) | ✗ 대상 트래픽이 VPC를 안 지남 |
| Network ACL | IP/포트 (서브넷 전체) | ✗ 적용해봤으나 동일. 영향 범위만 큼 |
| Network Firewall | 도메인(SNI/Host), IP, Suricata 룰 | △ 되긴 하는데 이 건에는 과함(비싸요) |
| **Route 53 Resolver DNS Firewall** | **도메인 (DNS 질의 시점)** | **○** |
| 호스트 iptables | IP | ✗ 이미 해보고 실패. 실제 통신이 인스턴스를 안 지남 |
| VPN 인스턴스의 로컬 DNS | 도메인 | △ DNS는 지나가니 원리상 가능. 단 AWS 밖에서 인스턴스마다 관리 |

표를 채우면서 앞의 결론이 다시 확인됐다. **위 네 개 중 IP·포트로 거는 것들(SG, NACL, iptables)은 전부 탈락** 걸 대상이 VPC를 안 지나가니까 규칙을 어디에 걸든 마찬가지다.

남는 건 **DNS 질의를 보는 것들**뿐인데, VPN 인스턴스의 로컬 DNS로 막는 것도 이론상 후보다. 질의가 실제로 거길 지나가기 때문이다. 다만 AWS 관리 밖이라 인스턴스가 늘어나면 그만큼 손이 가고, 담당자가 바뀌면 그런 설정이 있다는 것부터 잊힌다. 이번엔 후보에서 뺐다.

DNS Firewall이 맞았던 이유는 단순하다. **VPC 안에서 나가는 DNS 질의는 어차피 전부 Route 53 Resolver(`.2` 주소)를 거친다.** 인스턴스가 몇 대든 상관없이 VPC 하나에 룰그룹을 붙이면 그 아래 전부 걸린다.

---

## DNS Firewall은 3계층이다

콘솔 메뉴가 나뉘어 있어서 뭘 어디에 넣는 건지 헷갈렸다 (WAF랑 비슷하긴하네)

```
VPC
 └─ Rule Group (룰그룹)            ← VPC에 연결(associate)하는 단위
     ├─ Rule (규칙) priority 1     ← 여기에 "동작"이 붙는다 (ALLOW/BLOCK/ALERT)
     │   └─ Domain List            ← 여기에 "도메인"이 들어간다
     ├─ Rule priority 2
     │   └─ AWSManagedDomainsMalwareDomainList
     ├─ Rule priority 3
     │   └─ AWSManagedDomainsBotnetCommandandControl
     └─ Rule priority 4
         └─ AWSManagedDomainsAmazonGuardDutyThreatList
```

- **도메인 리스트**(`rslvr-fdl-`로 시작)는 그냥 도메인 문자열 묶음이다. 동작 개념이 없다.
- **규칙**이 "이 도메인 리스트에 걸리면 뭘 할지"를 정한다.
- **룰그룹**이 규칙들을 묶어서 VPC에 붙는다.

나중에 자동화할 때 이 구분이 중요해진다. 도메인을 추가한다는 건 **규칙을 건드리는 게 아니라 도메인 리스트에만 쓰는 것**이다. (이건 다음 편에서)

규칙의 동작은 세 가지다.

> **공식문서**
> · [Rule actions in DNS Firewall](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-dns-firewall-rule-actions.html)
> — "**Allow** – Stop inspecting the query and permit it to go through."
> — "**Alert** – Stop inspecting the query, permit it to go through, and log an alert for the query in the Route 53 VPC Resolver logs."
> — "**Block** – Discontinue inspection of the query, block it from going to its intended destination, and log the block action for the query in the Route 53 VPC Resolver logs."
> BLOCK 응답은 `NODATA`(성공했는데 줄 답이 없음) / `NXDOMAIN`(그런 도메인 없음) / `OVERRIDE`(커스텀 CNAME으로 돌리기) 중에 고른다.

그래서 실제 적용은 이렇게 했다.

- **탐지된 악성 도메인 7개는 커스텀 리스트에 넣고 즉시 BLOCK** — 이건 이미 GuardDuty가 잡은 거라 관찰할 이유가 없다
- **AWS 관리형 리스트 3종은 ALERT로 걸고 관찰** — 갑자기 BLOCK 걸었다가 정상 도메인이 막히면 그게 더 큰 사고다

관리형 리스트를 바로 BLOCK 안 하는 건 문서에서도 권하는 방식이다.

> **공식문서**
> · [Managed Domain Lists](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-dns-firewall-managed-domain-lists.html) — "As a best practice, before using a Managed Domain List in production, test it in a non-production environment, with the rule action set to `Alert`."

여기까지는 순조로웠다. 문제는 그 다음부터다.

---

## 함정 1 — ALERT는 "알림"이 아니다

이름 때문에 완전히 착각했다. ALERT를 걸어놨으니 뭔가 걸리면 알려주겠지, 라고 생각했는데 아니다.

**ALERT는 통지 기능이 아니라 "쿼리 로그에 기록을 남기는 동작"이다.** 위 인용문에도 그냥 `log an alert for the query in the Route 53 VPC Resolver logs`라고만 되어 있다. SNS로 뭘 보내주지 않는다.

그리고 **Resolver 쿼리 로깅은 기본적으로 꺼져 있다.** 이걸 안 켜놓으면 ALERT를 아무리 걸어놔도 기록될 목적지가 없어서, 뭐가 걸렸는지 알 방법이 없다. CloudWatch에 `FirewallRuleQueryVolume` 지표가 있긴 한데 이건 **건수만** 보여준다. 어떤 도메인이 어느 규칙에 걸렸는지는 안 나온다.

**캐시 히트는 로그에 안 남는다.** 이건 좀 나중에 알았는데, 로그 건수를 실제 질의 횟수로 읽으면 안 된다.

> **공식문서**
> · [Resolver query logging](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-query-logs.html) — "VPC Resolver query logging logs only **unique** queries, not queries that VPC Resolver is able to respond to from the cache."

**비용은 생각보다 안 나온다.** 하루 10만 건대 질의가 나가는 VPC 하나 기준으로 산정해봤더니 수집 + 저장 합쳐서 **월 $2 안팎**이었다. 로깅을 미룰 이유가 딱히 없는 금액이다. 오히려 안 켜놔서 아무것도 모르는 상태의 비용이 훨씬 크다.

---

## 함정 2 — 와일드카드는 맨 앞에서만, 정규식은 없다

고객이 물어본 게 이거였다. "비슷한 패턴 도메인이 계속 나오는데 한 번에 못 막나요?"

탐지된 도메인들이 이런 식이었다.

```
f1w-3030.com
f1w-9738.com
```

`f1w-*.com` 이렇게 등록하면 되겠네, 싶었는데 안 된다.

> **공식문서**
> · [Managing your own domain lists](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-dns-firewall-user-managed-domain-lists.html) — 도메인 명세 요건: "It can optionally **start with** `*` (asterisk)." / "With the exception of the optional starting asterisk and a period, as a delimiter between labels, it must only contain the following characters: `A-Z`, `a-z`, `0-9`, `-` (hyphen)."

**별표는 맨 앞에만 올 수 있다.** `*.example.com`(서브도메인 전부)은 되지만, `f1w-*.com`처럼 중간에 넣는 건 안 된다. 정규식은 당연히 없다.

---

## 함정 3 — 관리형 리스트는 이걸 안 잡는다

처음에는 "관리형 위협 리스트 3종을 ALERT로 걸어놨으니, 2주 뒤에 BLOCK으로 바꾸면 앞으로 비슷한 것들은 알아서 걸리겠지."

**2주 뒤 실측 결과는 매치 0건이었다.**

```
관찰 기간: 15일
VPC 전체 DNS 질의 검사량: 약 200만 건 (일평균 13만 건대)

  관리형 리스트 3종 (priority 2~4)  →  매치 0건
  커스텀 리스트 (priority 1)        →  매치 몇 건 (전부 정상 차단)
```

처음엔 "규칙이 동작 안 하는 거 아니야?"를 의심했다. 그런데 같은 기간에 커스텀 리스트는 정상적으로 몇 건을 잡았고, 검사량 지표도 200만 건 넘게 올라오고 있었다. **미동작이 아니라 진짜로 매치가 없는 것**이었다.

결정적인 증거는 이거였다. 문제의 도메인 하나가 **커스텀 리스트에 등록되기 전**에 질의된 기록이 있었다. 커스텀(priority 1)에 안 걸렸으니 그대로 관리형 규칙(priority 2~4)까지 평가가 내려갔다는 뜻인데, 그날 관리형 매치는 0건이었다. **관리형 리스트가 이 도메인을 그냥 모른다는 게 확인된 것이다.**

### 왜? GuardDuty가 잡았는데 GuardDuty 리스트가 모른다고?

여기서 제일 헷갈렸던 게 `AWSManagedDomainsAmazonGuardDutyThreatList`였다. 이름만 보면 "GuardDuty가 탐지하는 도메인 = 이 리스트"일 것 같지 않은가. 그런데 문서를 읽어보니 아니었다.

> **공식문서**
> · [Managed Domain Lists](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-dns-firewall-managed-domain-lists.html) — Amazon GuardDuty Threat List: "The domains are sourced from the GuardDuty's threat intelligence systems **only, and do not contain domains sourced from external third-party sources.** More specifically, currently this list will only block domains that are **internally generated** and used for following detections in GuardDuty: `Impact:EC2/AbusedDomainRequest.Reputation`, `Impact:EC2/BitcoinDomainRequest.Reputation`, `Impact:EC2/MaliciousDomainRequest.Reputation`, `Impact:Runtime/AbusedDomainRequest.Reputation`, `Impact:Runtime/BitcoinDomainRequest.Reputation`, `Impact:Runtime/MaliciousDomainRequest.Reputation`."

- 이 리스트는 **GuardDuty가 내부적으로 생성한** 도메인만 담는다
- **외부 서드파티 피드에서 온 도메인은 안 들어간다**
- 게다가 **딱 6개 finding 타입**에 대응하는 것만 들어간다

그리고 결정적으로 **이번에 뜬 finding은 `Trojan:EC2/PhishingDomainRequest!DNS`다.** 위 6개는 전부 `Impact:EC2/*.Reputation` 계열이고, 우리 finding은 거기 없다. 애초에 이 리스트가 담기로 한 범위 밖이었던 것이다.

즉 GuardDuty finding이 떴다고 해서 그 도메인이 이 리스트에 있으리라는 보장이 전혀 없다. **GuardDuty의 탐지 범위 ≠ DNS Firewall 관리형 리스트의 차단 범위**다. 이름이 같아서 당연히 연동될 거라고 착각했던 거다.

나머지 관리형 리스트들도 마찬가지다. 이쪽은 출처가 명시돼 있다.

> **공식문서**
> · 같은 문서 — "The AWS Managed Domain Lists source their data from both internal AWS sources as well as **RecordedFuture**, and are continually updated. However, AWS Managed Domain Lists **aren't intended as a replacement for other security controls, such as Amazon GuardDuty**."

문서가 대놓고 "GuardDuty 대체재가 아니다"라고 써놨다. 이걸 미리 읽었으면 바로 자동 차단 기능을 진행했을듯

### 그리고 관리형 리스트는 열어볼 수도 없다

"그럼 우리가 막고 싶은 도메인이 리스트에 있는지 미리 확인하면 되잖아?" — 안 된다.

> **공식문서**
> · 같은 문서 — "AWS Managed Domain Lists **cannot be downloaded or browsed.** To protect intellectual property, you can't view or edit the individual domain specifications within an AWS Managed Domain Lists."

지식재산 보호 + 공격자가 리스트를 보고 우회하는 걸 막기 위해서라고 한다. 이유는 납득이 되는데, 운영하는 입장에선 **커버 여부를 사전에 확인할 방법이 없다**는 뜻이 된다. 결국 ALERT로 걸어놓고 실제 매치가 나오는지 실측하는 것 외에 방법이 없다. (관리형이 그렇지 뭐... WAF도 그렇다.)

**그래서 결론이 "ALERT → BLOCK 전환만으로는 이 문제가 해결되지 않는다"였다.** 관리형은 관리형대로 켜두되(공짜고, 다른 위협은 잡아줄 테니), 이 고객이 실제로 겪는 문제는 커스텀 리스트를 어떻게 채우느냐로 풀어야 한다는 것.

---

## 그래서 막혔나? — 막히긴 하는데 알럿은 계속 뜬다

커스텀 리스트에 BLOCK을 걸고 실제로 테스트했다. 결과가 반반이었다.

```
도메인 조회        →  차단됨 (NXDOMAIN 응답, 접속 불가)      ✓
GuardDuty 알럿     →  그대로 계속 생성됨                      ✗
```

처음엔 "차단이 안 먹혔나?" 싶었는데 아니었다. 실제로 막히긴 한다. 알럿이 안 없어지는 이유는 **GuardDuty가 DNS를 보는 위치**에 있다.

> **공식문서**
> · [GuardDuty foundational data sources](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_data-sources.html) — "When you enable GuardDuty, it immediately starts analyzing your Route53 Resolver DNS query logs **from an independent stream of data.** This data stream is separate from the data provided through the Route 53 Resolver query logging feature."

GuardDuty는 **Resolver에 도착한 질의 자체**를 독립된 스트림으로 본다. 그 질의에 DNS Firewall이 뭐라고 응답하는지와는 상관이 없다.

```
단말 → Route 53 Resolver ─┬─▶ GuardDuty가 질의를 봄  → finding 생성 (계속 뜸)
                          └─▶ DNS Firewall 평가 → BLOCK → NXDOMAIN (차단은 성립)
```

**질의가 발생했다는 사실 자체가 탐지 대상**이고, 차단은 그 다음에 일어나는 일이다. 순서상 알럿을 없앨 방법이 없다.

운영에서 이게 두 가지를 의미한다.

- **알럿 건수로 차단 성공 여부를 판단하면 안 된다.** 확인은 알럿이 아니라 Resolver 쿼리 로그의 BLOCK 기록으로 해야 한다. (그래서 앞에서 로깅을 먼저 켠 게 여기서 또 필요해진다)
- 반대로 이 성질이 **다음 단계의 전제**가 된다. 막아도 알럿이 계속 온다는 건, 그 알럿을 트리거로 삼아 도메인을 계속 주워담을 수 있다는 뜻이다.

참고로 알럿을 조용히 시키겠다고 GuardDuty **Suppression Rule을 DNS 계열 finding에 걸면 안 된다.**(이후 적용할 자동화에 문제가 됨) 억제 규칙으로 자동 아카이브된 finding은 EventBridge로 발행되지 않아서, 그 위에 자동화를 얹어두면 조용히 멈춘다.

---

## 정리

- **탐지된 위치와 차단 가능한 위치는 다를 수 있다.** Split Tunnel에서는 DNS 질의만 VPC를 지나 알럿을 만들고, 실제 통신은 단말에서 직결된다. iptables·NACL이 안 먹힌 건 규칙이 틀려서가 아니라 **볼 트래픽이 VPC에 없어서**였다
- 도박/피싱 계열은 IP를 계속 바꾼다. **IP가 아니라 도메인 단계에서 막는 게 맞다**(여기서는 그게 맞음)
- DNS Firewall은 **룰그룹 → 규칙 → 도메인 리스트** 3계층. 도메인 추가는 도메인 리스트에만 쓰는 일이다
- **ALERT는 통지가 아니라 로그 기록 동작이다.** Resolver 쿼리 로깅이 없으면 아무것도 못 본다. 로그는 소급 안 되니 무조건 먼저 켠다
- 로그 그룹은 **CloudWatch에서 보존기간 정해서 먼저 만들고** 지정한다. Resolver 화면에서 만들면 Never expire
- 와일드카드는 **맨 앞에만**, 정규식 없음. `f1w-*.com` 같은 건 불가
- **관리형 리스트를 GuardDuty 탐지 범위와 동일시하면 안 된다.** GuardDuty Threat List는 GuardDuty 내부 생성 도메인 + 6개 finding 타입만 담는다. 리스트 내용은 열어볼 수도 없어서, 커버 여부는 ALERT 실측으로만 알 수 있다
- **차단해도 GuardDuty 알럿은 계속 뜬다.** GuardDuty는 Resolver에 도착한 질의를 독립 스트림으로 보기 때문. 차단 확인은 알럿이 아니라 쿼리 로그의 BLOCK 기록으로 한다

관리형 리스트에 기대는 게 안 된다는 걸 알았으니, 이제 해결 방법을 찾아야한다. **GuardDuty가 도메인을 탐지할 때마다 커스텀 도메인 리스트에 자동으로 넣는 것.** 다음 편에서 EventBridge + Lambda + CloudFormation 진행 예정

## 참고

- [Route 53 Resolver DNS Firewall](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-dns-firewall.html)
- [Rule actions in DNS Firewall](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-dns-firewall-rule-actions.html)
- [Managed Domain Lists](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-dns-firewall-managed-domain-lists.html)
- [Managing your own domain lists](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-dns-firewall-user-managed-domain-lists.html)
- [Resolver query logging](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-query-logs.html)
- [Values that appear in VPC Resolver query logs](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-query-logs-format.html)
- [Monitoring Resolver DNS Firewall rule groups with Amazon CloudWatch](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/monitoring-resolver-dns-firewall-with-cloudwatch.html)
- [GuardDuty finding types](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_finding-types-active.html)
