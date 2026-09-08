---
layout: post
title: "GuardDuty가 잡을 때마다 DNS Firewall에 자동으로 넣기로 했다"
date: 2026-09-09 00:00:00 +0900
categories: [AWS]
tags: [guardduty, route53, dns-firewall, eventbridge, lambda, cloudformation, security]
description: "GuardDuty 탐지 도메인을 DNS Firewall 커스텀 리스트에 자동 등록하는 구성. 오류 없이 조용히 멈추는 자동화를 어떻게 검증하고 배포했나."
image: "https://image.minnnning.kr/images/2026/09/20260909-000020-86174a.webp"
---

[1편](/posts/GuardDuty-DNS-Firewall-1/)에서 결론이 이렇게 났다. **AWS 관리형 위협 도메인 목록은 이 계열을 커버하지 않는다.** 실측으로 15일간 매치 0건이었고, 문서를 읽어보니 애초에 담기로 한 범위 밖이었다. 그래서 커스텀 도메인 리스트가 사실상 유일한 목록 기반 방어 수단이 됐다.

문제는 그 다음이다. **커스텀 리스트는 누군가 손으로 채워야 한다.** (이걸 수동으로 처리하면 답이 없다)

이 도박 사이트 계열은 정식 도메인이 차단되면 무작위 문자열로 된 미러 도메인을 계속 만들어낸다. 하나 막으면 다음 날 다른 게 뜬다. 그래서 실제 운영은 이런 모양이었다.

```
GuardDuty 알럿 발생
   → 담당자가 확인
   → 도메인 확인
   → DNS Firewall 커스텀 리스트에 수동 등록
   → 다음 미러 도메인 등장 → 반복
```

탐지는 자동인데 **차단만 사람 손을 탄다.** 그러니 야간이나 휴일에 뜬 건은 다음 영업일까지 그대로 열려 있었다. 알림을 아무리 잘 받아도 사람이 붙어야 닫히는 구조면 그 사이 시간은 못 줄인다.

그리고 1편에서 확인한 성질이 여기서 쓸모가 생긴다. **차단을 걸어도 GuardDuty 알럿은 계속 온다.** GuardDuty가 Resolver에 도착한 질의를 독립 스트림으로 보기 때문이다. 그렇다면 그 **알럿을 계속 트리거로 쓸 수 있다**는 뜻이다.

그래서 **GuardDuty가 도메인을 탐지할 때마다, 그 도메인을 커스텀 리스트에 자동으로 집어넣도록 만들었다.**(여기에는 없지만 클라우드 포메이션으로 생성했다.)

---

## 전제 조건 — 이게 안 맞으면 배포해도 안 막힌다

먼저 짚고 갈 게 있다. 이 구성은 **차단 체계를 만드는 게 아니라 이미 있는 차단 체계에 도메인을 넣어주는 것**이다. 그래서 아래가 갖춰져 있지 않으면 스택을 배포해도 아무것도 안 막힌다.

```
1. GuardDuty 활성화 + DNS Logs 켜져 있을 것
2. DNS Firewall 커스텀 도메인 리스트가 생성되어 있을 것
3. 그 도메인 리스트가 BLOCK 액션 규칙에 연결되어 있을 것
4. 그 룰 그룹이 대상 VPC에 연결되어 있을 것
```

그러니까 자동 등록의 목적지는 무조건 커스텀 리스트다.

---

## 동작 방식

전체 흐름은 단순하다.

![arch-light](https://image.minnnning.kr/images/2026/09/20260909-001057-8570b6.webp)

```
GuardDuty 악성 도메인 탐지
        ↓
EventBridge 규칙  (DNS 계열 finding만 필터)
        ↓
Lambda
   ├─ 탐지 유형 확인   → 대상 아니면 종료
   ├─ 제외 목록 확인   → 제외 대상이면 종료
   ├─ 중복 등록 확인   → 이미 있으면 종료
   ├─ 도메인 리스트에 등록
   └─ Slack 통보
        ↓
DNS Firewall 차단 적용 (NXDOMAIN 응답)
```

| 단계 | 내용 |
|---|---|
| 1. 탐지 | GuardDuty가 악성 도메인 조회를 탐지하고 finding 생성 |
| 2. 전달 | EventBridge 규칙이 DNS 계열 finding만 걸러 Lambda 호출 |
| 3. 검사 | 탐지 유형 → 제외 목록 → 중복 여부를 순서대로 확인 |
| 4. 등록 | DNS Firewall 커스텀 도메인 리스트에 도메인 추가 |
| 5. 통보 | 등록 결과를 Slack으로 발송 (실패 시에도 발송) |

탐지부터 등록까지 수 분 내에 끝난다. 여기서 걱정했던 게 **GuardDuty 발행 주기**였다. 기본값이 6시간이라 "그럼 최대 6시간 늦게 등록되는 거 아닌가?" 싶었는데, 아니었다.

> **공식문서**
> · [EventBridge notification frequency in GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings_cloudwatch.html) — "GuardDuty sends these notifications in **near real-time** when it generates a finding with a unique finding ID... The notification frequency for newly generated findings is in near real-time. **By default, you can not modify this frequency.**"
> 반면 기존 finding의 후속 발생은 다르다 — "GuardDuty aggregates all subsequent occurrences of a particular finding type that take place within the 6-hour intervals into one single event." (15분 / 1시간 / 6시간 중 선택)

**발행 주기 설정은 "후속 발생"에만 걸린다.** 새 도메인은 곧 새 finding ID라서 near real-time으로 온다. 우리가 잡고 싶은 게 정확히 새 미러 도메인이니까, 주기를 건드릴 필요가 없었다.

---

## 입력 대상은 규칙이 아니라 도메인 리스트다

1편에서 3계층 구조를 봤는데, 자동화를 짤 때 이 구분이 결정적이다.

| 계층 | ID 형식 | 역할 |
|---|---|---|
| Rule Group | `rslvr-frg-` | 규칙 묶음. VPC에 연결 |
| Rule | (이름) | 도메인 리스트와 액션(BLOCK/ALERT) 연결 |
| **Domain List** | **`rslvr-fdl-`** | **실제 도메인이 담기는 목록 — 입력 대상** |

**Lambda는 도메인 리스트에만 쓴다.** 규칙도 룰그룹도 건드리지 않는다. 이렇게 해두면 자동화가 오작동해도 차단 정책 구조 자체는 안 망가진다. 최악의 경우도 "리스트에 이상한 도메인이 하나 들어간 것"이지, 큰 문제는 발생하지 않는다.

배포 파라미터로 `rslvr-frg-`를 넣으면 안 된다는 얘기이기도 하다. 처음에 이거 헷갈려서 룰그룹 ID를 넣고 왜 안 되나 했다.

---

## 사람 검토 없이 차단된다 — 3중 안전장치

이 구성의 본질은 **사람의 승인 없이 차단이 적용된다**는 것이다. 편한 만큼 위험하다. 정상 도메인이 잘못 들어가면 그대로 막힌다. 그래서 오탐 방지 장치를 세 개 넣었다.

| 장치 | 역할 |
|---|---|
| **탐지 유형 한정** | 신뢰도 높은 DNS finding type만 대상. 평판 기반(`Impact:*/*.Reputation`)은 오탐 위험이 커서 제외 |
| **제외 목록** | 담당자가 오탐으로 판단해 지운 도메인의 자동 재등록 차단. 하위 도메인까지 매칭 |
| **중복 등록 방지** | 등록 전 현재 도메인 리스트를 조회해 이미 있으면 건너뜀 |

첫 번째가 좀 재밌다. **우리가 오탐 위험으로 제외한 `Impact:*/*.Reputation` 계열이, 1편에서 본 AWS 관리형 GuardDuty Threat List가 담는 바로 그 6개 타입이다.**

```
AWS 관리형 GuardDuty Threat List가 담는 것
  = 위 6종 탐지에 쓰이는 도메인 중 GuardDuty가 내부 생성한 것만
       ↑ 외부 서드파티 피드에서 온 도메인은 안 들어간다

우리 자동화가 대상에서 빼는 것
  = Impact:*/*.Reputation 계열 전체

우리가 실제로 잡고 싶은 것
  = Trojan:EC2/PhishingDomainRequest!DNS  ← 관리형 목록엔 아예 없는 타입
```

같은 타입을 두고 AWS는 자기 관리형 목록에 넣고, 우리는 자동 등록 대상에서 뺐다. 모순 같지만 이유가 있다. **AWS는 자기 위협 인텔리전스로 검증한 도메인만 목록에 올리지만, 우리 자동화는 finding에 실린 도메인을 그대로 믿고 등록한다.** 신뢰도가 다르니 기준도 달라야 한다.

평판 기반 finding은 "이 도메인이 평판이 안 좋다"는 신호지 "확실히 악성이다"가 아니다. 사람이 보고 판단할 땐 유용한데, 무조건 등록하는 자동화에 물리기엔 근거가 약하다.

### "그럼 그건 관리형 규칙이 대신 막아주나?"

여기서 넘어가기 쉬운 지점이다. 평판 기반을 우리가 뺐으니 관리형 규칙이 그 몫을 맡아준다고 생각하기 쉬운데, **그렇게 기대면 안 된다.** 조건이 두 개 붙는다.

- **내부 생성분만 담긴다.** 같은 Reputation finding이라도 외부 서드파티 피드에서 온 도메인이면 관리형 목록에 없다. **finding은 뜨는데 차단은 안 되는 조합**이 그대로 성립한다
- **관리형 규칙이 BLOCK이어야 한다.** ALERT면 로그만 남기고 통과시킨다. 1편에서 관찰하던 상태가 정확히 그거였다

그리고 결정적으로, 1편의 실측이 **15일간 관리형 매치 0건**이었다. 그 계정에서 관리형 리스트는 실제로 아무것도 잡고 있지 않았다.

그래서 이 구성에서 평판 기반 유형의 위치는 "관리형이 대신 해주는 영역"이 아니라 **"자동으로 막지 않기로 한 영역"**이다. 사람이 알럿을 보고 판단해서 필요하면 직접 등록한다. **자동화가 커버하지 않는 범위를 분명히 해두는 게, 커버한다고 착각하는 것보다 안전하다.**

---

## 오탐이 나면 두 가지를 다 해야 한다

정상 도메인이 잘못 차단됐을 때, **반드시 아래 둘 다 해야 한다.**

```
1. DNS Firewall 도메인 리스트에서 해당 도메인 삭제
2. 제외 목록(SSM 파라미터)에 해당 도메인 추가
```

1번만 하면 어떻게 되냐면 — 그 도메인은 **정상 업무 도메인이라 조회가 계속된다.** 조회가 계속되면 GuardDuty finding이 또 뜨고, 우리 Lambda가 또 등록한다. 지워도 다시 들어오는 무한 루프가 된다.

```
도메인 삭제 → 업무상 조회 계속됨 → finding 재발생 → 자동 재등록 → 또 막힘 → ...
```

이거 실제로 겪으면 꽤 당황스럽다. "분명 지웠는데 왜 또 막혀?"가 된다. **제외 목록에 넣어야 이 반복이 끊긴다.** 운영 문서 맨 앞에 이걸 박아뒀다.

---

## 제외 목록은 일부러 스택 밖에 뒀다

제외 목록은 SSM 파라미터로 관리하는데, **이 파라미터를 CloudFormation 스택에 포함하지 않았다.**

넣으면 편할 것 같지만 함정이 있다. 템플릿에 제외 목록 값을 넣어두면, **스택을 업데이트할 때마다 담당자가 그동안 추가해둔 예외가 템플릿 값으로 되돌아간다.** 운영 중에 쌓인 예외가 배포 한 번에 날아가고, 날아간 순간 그 도메인들이 다시 자동 등록된다. 위에서 본 루프가 한꺼번에 터지는 것이다.

그래서 이렇게 갈랐다.

```
스택 안  →  Lambda, IAM 역할, EventBridge 규칙, 로그 그룹   (코드성 리소스)
스택 밖  →  제외 목록 SSM 파라미터                          (운영 중 쌓이는 상태)
```

스택은 이 파라미터에 **읽기 권한만** 갖는다. 파라미터 자체는 스택 밖에서 따로 만든다.

이건 일반화할 수 있는 원칙 같다. **배포 때마다 초기화되면 안 되는 값은 스택 밖에 둔다.** 운영하면서 사람이 쌓아가는 상태(예외 목록, 임계값 조정, 화이트리스트)가 여기 해당한다. IaC로 다 묶고 싶은 욕심이 나는데, 묶는 순간 그게 배포마다 리셋되는 값이 된다.

---

## 이 자동화는 실패해도 조용하다

여기가 제일 중요한 부분이다. 만들면서 가장 신경 쓴 것도 이거였다.

**이 구성의 주요 실패 모드는 예외나 오류가 아니라, 아무 일도 안 일어나는 것이다.** 아래 셋 다 로그를 남기지 않는다.

```
EventPattern 표기 오류      → 매치가 0건일 뿐, 에러 아님
EventBridge 타깃 미연결     → 이벤트는 오는데 아무 데도 안 감
Lambda 호출 권한 누락       → 호출 시도 자체가 기록 안 됨
```

배포는 성공하고, 콘솔은 초록색이고, CloudWatch 로그 그룹은 비어 있다. **비어 있는 게 "아직 탐지가 없어서"인지 "배선이 끊겨서"인지 구분이 안 된다.** 몇 주 뒤에 사고가 나서야 안 돌고 있었다는 걸 알게 되는 종류의 실패다.

### 제일 잘 걸리는 함정: camelCase vs PascalCase

EventPattern을 쓸 때 finding 구조를 확인하려고 보통 CLI로 실제 finding을 하나 떠본다. 그런데 **표기가 다르다.**

```
EventBridge 페이로드
  detail.service.action.dnsRequestAction.domain     ← camelCase

CLI로 조회한 GetFindings 응답
  Service.Action.DnsRequestAction.Domain            ← PascalCase
```

CLI 출력을 보고 그 구조 그대로 EventPattern을 쓰면 **오류 없이 아무 이벤트도 매치되지 않는다.** EventBridge는 "패턴이 안 맞는다"고 알려주지 않는다. 그냥 조용하다.

문서상 EventBridge의 `detail`은 finding JSON 객체 그대로이고, 실제 페이로드는 camelCase다. 앞서 본 AWS 예시 패턴들도 전부 `detail.severity`, `detail.accountId`, `detail.type` 같은 camelCase다. Lambda 코드는 혹시 몰라서 양쪽 표기를 다 처리하게 짰지만, **EventPattern은 그럴 수가 없다.** 여기만 틀려도 전체가 멈춘다.

### 그래서 DryRun으로 시작한다 (이 설정을 테스트를 위해서 넣음)

`DryRun=true`로 배포해서 아래를 전부 통과한 뒤에 `false`로 바꾼다.

```
1. EventPattern 검증        aws events test-event-pattern  (리소스 생성 불필요)
2. Slack Webhook 단독 확인  curl
3. Lambda 단독 테스트       등록 / 제외 / 유형 필터 각각
4. 실패 통보 경로 확인      일부러 실패시켜서 알림 오는지
5. 종단 배선 확인           create-sample-findings
6. 실등록 + 중복 방지 확인  확인 후 테스트 도메인 삭제
```

1번이 특히 좋다. `aws events test-event-pattern`은 **아무 리소스도 안 만들고** 패턴과 샘플 이벤트를 맞춰본다. 위의 camelCase 함정을 배포 전에 잡을 수 있는 가장 싼 방법이다.

5번 `create-sample-findings`는 GuardDuty가 가짜 finding을 만들어주는 기능인데, 이걸로 EventBridge → Lambda 배선이 실제로 살아있는지 확인한다. 1~4번은 각 부품을 따로 본 거고, 5번이 처음으로 전체를 관통해보는 단계다.

테스트 도메인은 `autoblock-test.invalid`처럼 **RFC 예약 TLD**를 쓴다. `.invalid`는 실제로 해석되지 않기로 예약된 TLD라, 잘못 차단 목록에 남아도 서비스에 아무 영향이 없다. 테스트용으로 진짜 도메인이나 `example.com`을 쓰면 나중에 지우는 걸 잊었을 때 문제가 된다.

---

## Suppression Rule을 걸면 자동화가 조용히 멈춘다

이건 나중에 알았으면 꽤 고생했을 것 같다.

알럿이 계속 오니까(1편에서 본 그 성질) 자연스럽게 "알림 정리 좀 하자"는 얘기가 나온다. 그때 GuardDuty **Suppression Rule(억제 규칙)**을 DNS 계열 finding type에 걸면 안 된다.

> **공식문서**
> · [Suppression rules in GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/findings_suppression-rule.html) — "**Suppressed findings are not sent to** AWS Security Hub CSPM, Amazon Simple Storage Service, Amazon Detective, **or Amazon EventBridge**"
> · [Processing GuardDuty findings with EventBridge](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings_cloudwatch.html) — "For the findings that are **automatically archived with Suppression rules**, the initial and all subsequent occurrences of these findings are ***not* sent to EventBridge.**"

억제 규칙에 걸린 finding은 EventBridge로 아예 발행되지 않는다. **그러면 우리 자동화의 실행 조건 자체가 사라진다.** 그리고 이것도 오류 메시지가 없다. Lambda는 호출이 안 되고, 로그는 비어 있고, 아무도 뭐가 잘못됐는지 모른다.

수동 Archive는 괜찮다.

> · 같은 문서 — "For findings that you **manually archive**, the initial and all subsequent occurrences of these findings (generated after the archiving is complete) **are sent to EventBridge**"

**알림 정리는 반드시 수동 Archive로 한다.** 억제 규칙은 이 자동화가 걸린 finding type에는 걸지 않는다.

---

## 한계 — 최초 1회는 못 막는다

솔직히 **이 구성은 처음 조회되는 도메인을 막지 못한다.**

```
새 미러 도메인 등장
   → 누군가 조회함        ← 이건 그대로 나간다
   → GuardDuty 탐지
   → 자동 등록
   → 이후 조회부터 차단
```

GuardDuty는 조회가 **발생한 뒤에** 탐지하는 방식이라 구조적으로 불가피하다. 그러니까 이건 "완벽한 차단"이 아니라 **재방문 차단 + 수동 등록 부담 제거**가 목적인 구성이다.

그럼 실효가 있느냐. 이 고객사 기준으로 **탐지된 도메인의 재조회 비율이 8할을 넘었다.** 한 번 뜬 도메인은 대체로 다시 조회된다는 뜻이고, 그래서 첫 조회를 못 막아도 대부분의 조회는 막힌다.

---

## 롤백

사람 승인 없이 차단하는 구성이니까, 되돌리는 방법을 먼저 정해뒀다.

| 단계 | 방법 | 영향 |
|---|---|---|
| 즉시 중단 | EventBridge 규칙 Disable | 무중단. 스택 유지되어 복구 가능 |
| 오등록 해제 | 도메인 리스트에서 삭제 + 제외 목록에 추가 | 수 초 내 반영 |
| 전체 철회 | CloudFormation 스택 Delete | 생성 리소스 일괄 삭제 |

즉시 중단이 EventBridge 규칙 Disable인 게 포인트다. **스택을 지우지 않고 자동화만 멈출 수 있다.** 새벽에 뭔가 이상하면 일단 이것만 끄면 된다.

스택을 지워도 **제외 목록 파라미터와 이미 등록된 도메인은 남는다.** 기존 DNS Firewall 규칙, 도메인 리스트, GuardDuty 설정도 스택 생성 대상이 아니니 삭제되지 않는다. 자동화를 걷어내도 차단 상태는 그대로 유지된다는 뜻인데, 이게 의도한 동작이다.

---

## 정리

- 관리형 목록이 커버 안 하니 커스텀 리스트가 유일한 수단인데, 그건 사람이 채워야 한다. **탐지는 자동인데 차단만 수동이면 야간·휴일 공백이 그대로 남는다**
- 차단해도 GuardDuty 알럿이 계속 온다는 1편의 성질이, 여기서 **자동화의 트리거**가 된다
- 쓰기 대상은 **도메인 리스트(`rslvr-fdl-`)**. 규칙도 룰그룹도 건드리지 않으면 오작동해도 정책 구조는 안 깨진다
- 사람 승인 없이 차단되므로 **탐지 유형 한정 / 제외 목록 / 중복 방지** 3중 장치가 필요하다. 평판 기반 finding은 자동 등록에 물리기엔 근거가 약하다
- 자동화에서 뺀 유형을 **관리형 규칙이 대신 막아줄 거라 기대하면 안 된다.** 관리형 목록은 GuardDuty 내부 생성 도메인만 담고, BLOCK으로 켜져 있어야 하며, 실측 커버리지는 0건이었다.
- 오탐 대응은 **삭제 + 제외 등록을 둘 다.** 하나만 하면 재탐지 → 재등록 루프에 빠진다
- **배포마다 초기화되면 안 되는 값은 스택 밖에 둔다.** 운영 중 쌓이는 예외 목록이 대표적이다
- 이 자동화의 실패 모드는 전부 **조용한 무동작**이다. EventPattern 표기 오류, 타깃 미연결, 호출 권한 누락 어느 것도 로그를 안 남긴다. 그래서 `DryRun`으로 시작해 `test-event-pattern` → 부품별 테스트 → `create-sample-findings` 순으로 검증한다
- **EventBridge는 camelCase, CLI 응답은 PascalCase.** 여기 틀리면 오류 없이 0건 매치된다
- **Suppression Rule을 걸면 EventBridge 발행이 끊겨 자동화가 멈춘다.** 알림 정리는 수동 Archive로
- 최초 1회는 못 막는다. 목적은 재방문 차단이고, 재조회 비율이 받쳐줘야 정당화된다

만들면서 제일 크게 배운 건 기능보다 **검증 절차** 쪽이었다. 사람 손을 빼는 자동화는 잘 도는지 확인할 사람도 같이 빠진다. 그래서 "돌고 있음"을 증명하는 방법을 만들어두지 않으면, 안 도는 걸 사고 나서야 알게 된다. `DryRun` 기본값을 `true`로 둔 것도 그래서다. 켜는 건 검증한 사람이 의도적으로 하는 일이어야 한다.

## 참고

- [Processing GuardDuty findings with Amazon EventBridge](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings_cloudwatch.html)
- [Suppression rules in GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/findings_suppression-rule.html)
- [GuardDuty foundational data sources](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_data-sources.html)
- [GuardDuty EC2 finding types](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_finding-types-ec2.html)
- [Managed Domain Lists](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-dns-firewall-managed-domain-lists.html)
- [Managing your own domain lists](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver-dns-firewall-user-managed-domain-lists.html)
- [aws events test-event-pattern](https://docs.aws.amazon.com/cli/latest/reference/events/test-event-pattern.html)
- [RFC 2606 — Reserved Top Level DNS Names](https://datatracker.ietf.org/doc/html/rfc2606)
