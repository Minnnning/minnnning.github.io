---
layout: post
image: "https://github.com/user-attachments/assets/0dbab6f9-9f35-4f14-8427-e7bb55a655af"
tags: DNS
---

## DNS(Domain Name System) 서버란?

DNS는 인터넷의 "전화번호부"와 같다 사람이 기억하기 쉬운 도메인 이름(예: `www.example.com`)을 컴퓨터가 이해하는 IP 주소(예: `192.0.2.1`)로 변환해주는 분산 데이터베이스 시스템이다

- **가독성:** IP 주소 대신 기억하기 쉬운 도메인 이름을 사용할 수 있게 한다

- **유연성:** 웹 서버의 IP 주소가 변경되어도 도메인 이름은 그대로 유지할 수 있다 DNS 레코드만 업데이트하면 됩다

- **분산 시스템:** 전 세계에 분산된 수많은 DNS 서버들이 협력하여 작동한다

&nbsp;

### DNS의 계층적 구조

1. **Root DNS 서버 (`.`):** 인터넷의 최상위에 위치하며, TLD 서버의 위치를 알려줍니다. 전 세계에 13개의 루트 서버 클러스터가 존재
2. **TLD (Top-Level Domain) 서버 (예: `.com`, `.org`, `.net`, `.kr`):** 특정 최상위 도메인에 대한 정보를 관리합니다. 예를 들어, `.com` TLD 서버는 `.com`으로 끝나는 모든 도메인의 Authoritative DNS 서버 위치를 알고 있다
3. **Authoritative DNS 서버 (권한 있는 DNS 서버):** 특정 도메인(예: `example.com`)에 대한 모든 DNS 레코드(A, MX, CNAME 등)를 실제로 저장하고 응답하는 서버 이 서버만이 해당 도메인에 대한 최종적인 정보를 제공할 수 있다
4. **Recursive DNS Resolver (재귀적 DNS 해석기, 또는 Stub Resolver):** 사용자의 컴퓨터나 네트워크 장치(공유기 등)에 설정된 DNS 서버이다 클라이언트로부터 도메인 이름 해석 요청을 받으면, 직접 해당 정보를 가지고 있지 않을 경우 Root DNS 서버부터 시작하여 Authoritative DNS 서버까지 질의를 반복(재귀적 질의)하여 최종 IP 주소를 찾아 클라이언트에게 반환한다 우리가 일반적으로 사용하는 ISP(KT, SKT, LG U+)의 DNS 서버나 Google Public DNS(8.8.8.8), Cloudflare DNS(1.1.1.1) 등이 여기에 해당한다

&nbsp;

### DNS 이름 해석 과정 (예: `www.example.com` 요청 시)

<center>
<img src="https://github.com/user-attachments/assets/0dbab6f9-9f35-4f14-8427-e7bb55a655af" style="zoom:20%;">
</center>

1. **클라이언트 (내 컴퓨터) -> Recursive DNS Resolver:** 클라이언트는 `www.example.com`의 IP 주소를 Recursive DNS Resolver(예: 8.8.8.8)에게 요청
2. **Recursive DNS Resolver -> Root DNS 서버:** Recursive DNS Resolver는 자신의 캐시에 `www.example.com` 정보가 없으면, 먼저 Root DNS 서버에게 `.com` TLD 서버의 IP 주소를 물어본다
3. **Root DNS 서버 -> Recursive DNS Resolver:** Root DNS 서버는 `.com` TLD 서버의 IP 주소를 응답
4. **Recursive DNS Resolver -> TLD 서버:** Recursive DNS Resolver는 `.com` TLD 서버에게 `example.com`의 Authoritative DNS 서버 IP 주소를 물어본다
5. **TLD 서버 -> Recursive DNS Resolver:** `.com` TLD 서버는 `example.com`의 Authoritative DNS 서버 IP 주소를 응답
6. 이후 TLD서버가 Authoritative DNS를 반환해주고 Recursive DNS Resolver가 또 Authoritative DNS에 반복해서 결과를 받는다

&nbsp;

## DNS SERVER 구축

목표는 내 dns 서버에 naver를 질의하면 구글을 반환한다

### 1. EC2 가상머신 생성

가상 머신을 만들고 DNS 서버로 이용하기 위해서 53번 포트는 UDP 허용한다

&nbsp;

### 2. EC2에 BIND9 설치

``` bash
sudo apt install bind9 bind9utils bind9-doc -y
```

BIND9는 가장 널리 사용되는 오픈 소스 DNS 서버 소프트웨어이다 또 bind9utils는 DNS 서버 관리 및 디버깅에 유용한 유틸리티 (예: dig, nslookup)를 포함하고 bind9-doc: BIND9 관련 문서를 제공한다

&nbsp;

### 3. BIND9 설정 파일 편집

**주요 설정 파일 확인:**

- `/etc/bind/named.conf.options`: 전역 DNS 서버 옵션 (포워딩, ACL 등)
- `/etc/bind/named.conf.local`: 로컬에서 관리할 Zone 설정
- `/etc/bind/db.*`: Zone 파일 (실제 DNS 레코드 정보)

&nbsp;

**`named.conf.options` 수정**

이 파일은 DNS 서버의 전역적인 동작 방식을 정의한다 우리는 외부 DNS 서버로 쿼리를 전달하는 `forwarders` 설정을 추가한다

``` bash
sudo vi /etc/bind/named.conf.options
```

```txt
options {
    directory "/var/cache/bind";

    // If there is a firewall between you and nameservers you want
    // to talk to, you might need to uncomment the firewall option.
    // firewall { ... };

    // If your ISP provided a nameserver then uncomment and use it.
    // forwarders {
    //      0.0.0.0;
    // };

    // 이 부분을 추가 모르는게 오면 재귀요청
    forwarders {
         8.8.8.8;
         8.8.4.4;
    };

    // 우리 서버가 외부 DNS 서버에게 재귀적 쿼리를 허용할지 여부
    recursion yes;

    allow-query { any; }; // 모든 IP 주소에서 쿼리를 허용

    listen-on { any; }; // 모든 네트워크 인터페이스에서 DNS 쿼리를 수신

};
```

&nbsp;

**`named.conf.local` 수정**

`naver.com`에 대한 포워드 존을 생성한다

``` bash
sudo vi /etc/bind/named.conf.local
```

``` txt
// naver.com에 대한 커스텀 설정
zone "naver.com" {
    type master; // 이 서버가 "naver.com" 도메인의 마스터 서버임을 의미
    file "/etc/bind/db.naver"; // naver.com 도메인의 DNS 레코드 파일 경로를 지정
    allow-update { none; }; // 동적 업데이트를 허용하지 않음
};
```

**`type master;`**은 이 서버가 `naver.com` 도메인에 대한 "권한 있는(authoritative)" 마스터 DNS 서버임을 선언

&nbsp;

**`db.naver` Zone 파일 생성:**

``` bash
sudo vi /etc/bind/db.naver
```

``` txt
$TTL    3600    ; 1 hour
@       IN      SOA     ns1.naver.com. admin.naver.com. (
                          2023010101      ; Serial (YYYYMMDDNN)
                          3600            ; Refresh (1 hour)
                          1800            ; Retry (30 mins)
                          604800          ; Expire (1 week)
                          86400 )         ; Minimum TTL (1 day)

        IN      NS      ns1.naver.com.
ns1     IN      A       {EC2_Public_IP} ; 이 서버의 퍼블릭 IP 주소

@       IN      A       142.250.76.142 ; google.com의 IP 주소 (예시, 실제 IP 확인 후 변경)
www     IN      A       142.250.76.142 ; google.com의 IP 주소 (예시, 실제 IP 확인 후 변경)
```

**`@ IN SOA ns1.naver.com. admin.naver.com. (...)`**: SOA (Start of Authority) 레코드는 해당 Zone에 대한 권한 시작을 나타낸다

- `ns1.naver.com.`: 이 Zone의 프라이머리 네임서버 (반드시 존재하는 실제 서버가 아니어도 된다 이 DNS 서버가 자신을 위한 NS 서버로 동작하는 것으로 가정)
- `admin.naver.com.`: Zone 관리자의 이메일 주소 (`@` 대신 `.` 사용)
- `Serial (YYYYMMDDNN)`: Zone 파일이 변경될 때마다 증가시켜야 하는 일련번호 바꿔야 인식됨
- `Refresh`, `Retry`, `Expire`, `Minimum TTL`: 슬레이브 서버가 마스터 서버에 질의하는 간격 및 캐싱 값

**`IN NS ns1.naver.com.`**: NS (Name Server) 레코드는 이 Zone에 대한 권한 있는 네임서버를 지정 여기서는 `ns1.naver.com`이 이 서버 자신임을 나타낸다

**`@ IN A 142.250.76.142`**: `naver.com` (즉, 도메인 자체)의 A (Address) 레코드 `naver.com` 요청 시 `142.250.76.142` (Google의 IP 주소)으로 응답하도록 설정

**`www IN A 142.250.76.142`**: `www.naver.com`의 A 레코드  `www.naver.com` 요청 시에도 `142.250.76.142`으로 응답하도록 설정 `www` 서브도메인에 대한 요청도 처리하기 위함

&nbsp;

### 4. BIND9 설정 파일 문법 검사 및 서비스 재시작

**설정 파일 문법 검사**

``` bash
sudo named-checkconf
```

<center>
<img src="https://github.com/user-attachments/assets/5970b6b5-458d-4d26-b145-8baed611a9f1" style="zoom:60%;">
</center>

``` bash
sudo named-checkzone naver.com /etc/bind/db.naver
```

<center>
<img src="https://github.com/user-attachments/assets/17399a55-e64e-4ebe-96c5-8964947a7e58" style="zoom:60%;">
</center>

**재시작**

``` bash
sudo systemctl restart bind9
```

&nbsp;

### 테스트

로컬 pc에서 naver을 dig로 요청해서 ip 주소를 확인한다

<center>
<img src="https://github.com/user-attachments/assets/48311958-378c-45d7-9266-104f6c1caae3" style="zoom:50%;">
</center>

dns를 서버를 변경해서 요청한다

<center>
<img src="https://github.com/user-attachments/assets/235b963d-218a-4be7-9ebd-e0d2faf88d74" style="zoom:45%;">
</center>

다른 ip 주소인 구글 ip를 반환하는것을 확인할 수 있다
