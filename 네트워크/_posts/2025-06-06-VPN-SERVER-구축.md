---
layout: post
image: "https://github.com/user-attachments/assets/16611905-7d78-4891-973a-6c836e15722f"
tags: VPN
---

##  VPN 구축기

오토에버 클라우드 교육에서 VPN에 대해서 배워서 VPN을 직접 구축해볼려고 한다

&nbsp;

### 1회차 시도: VirtualBox 내부 네트워크 구성 실패

가장 먼저 떠올린 아이디어는 VirtualBox를 활용하여 VPN 환경을 구축

**구상:**

- **VPN-SERVER VM:** 인터넷 접속을 위해 NAT 어댑터 1개, 내부 VM과의 연결을 위해 "내부 네트워크" 어댑터 2개를 구성
- **클라이언트 VM (내부):** VPN-SERVER와 동일한 "내부 네트워크"로 설정하여 서버와 통신할려 했다
- **클라이언트 VM (외부):** NAT 네트워크로 설정하고, 이 VM을 통해 VPN-SERVER에 접속하여 VPN 기능을 테스트할 계획

<center>
<img src="https://github.com/user-attachments/assets/3310804a-48d0-4e3e-9e62-732b5718c75f" style="zoom:20%;">
</center>

**결과 및 문제점:**

VPN-SERVER와 외부 클라이언트 VM을 모두 NAT 네트워크로 설정했을 때, 둘 다 10.0.2.15라는 동일한 IP 주소를 할당받았지만, 실제로는 서로 다른 NAT 네트워크에 속해 있어 통신이 불가능했다 이후 네트워크 연결 방식을 다양하게 변경해보고 어댑터 설정을 여러 번 시도했지만, 결국 1회차 시도는 실패

&nbsp;

------

### 2회차 시도: VirtualBox 서버와 로컬 PC 클라이언트 연결 실패

1회차 실패를 바탕으로 이번에는 클라이언트와 VPN_SERVER ip를 구분하기 위해서 VirtualBox 서버와 **로컬 PC**를 클라이언트로 연결

**구상:**

- **VPN-SERVER VM (VirtualBox):** 서버 역할을 수행하도록 설정 포트포워딩을 통해서 외부와 통신(NAT 네트워크)
- **클라이언트 (로컬 PC):** 제 실제 PC를 클라이언트로 사용하여 VPN 서버에 접속하고, VPN을 통한 인터넷 사용을 구현할려고 했다

<center>
<img src="https://github.com/user-attachments/assets/aeca9b81-718f-4a75-82ac-cd23a85eb473" style="zoom:20%;">
</center>

**결과 및 문제점:**

VPN 서버 구축을 완료하고, 로컬 PC에서 VPN 서버로 `ping` 테스트를 했을 때는 정상적으로 응답이 왔다 하지만 서버에서 로컬 PC로 `ping`을 시도했을 때는 응답이 안옴 이 문제는 서버와 클라이언트 간의 양방향 통신이 제대로 이루어지지 않음을 의미하며, 2회차 시도 또한 실패

&nbsp;

------

### 3회차 시도: AWS EC2를 활용한 VPN 구축 성공

마지막으로 AWS EC2 인스턴스를 VPN 서버로 활용하는 방법을 시도

이전 vpn은 같은 내부망이어서 확인하는 방법이 첫번째 시도한 방법처럼 내부에 클라이언트가 접근할 수 없는 네트워크에 접속하는것 아니면 아예 다른 리전에 aws ec2인스턴스를 생성해서 달라진 공인 ip를 확인하면 vpn의 성공적인 결과를 확인 할 수 있다

<center>
<img src="https://github.com/user-attachments/assets/16611905-7d78-4891-973a-6c836e15722f" style="zoom:20%;">
</center>

**결과:**

AWS EC2를 이용한 VPN 서버 구축은 **성공** 

&nbsp;

## EC2를 이용한 VPN 서버 구축

### 1. EC2 인스턴스 생성

aws에서 ec2 인스턴스를 생성하고 우분투를 설치한다 ssh 접속을 이용할 예정

이후 고정된 ip를 얻기 위해서 탄력적 ip를 적용시킨다 그럼 ec2 설정은 끝이다

&nbsp;

### 2. 생성한 VM 서버에 Ubuntu 서버 설정

- Ubuntu 업데이트

``` bash
sudo apt update && sudo apt upgrade -y
```

- WireGuard 설치

``` bash
sudo apt install wireguard -y
```

- 키 생성

``` bash
umask 077 # 새로 생성되는 모든 파일과 디렉토리는 기본적으로 소유자에게만 권한이 부여
wg genkey | tee privatekey | wg pubkey > publickey
```

- 서버 설정 파일 작성 (`sudo vi /etc/wireguard/wg0.conf`)

``` bash
[Interface] #서버에서 기본적으로 사용할 인터페이스 
Address = 10.8.0.1/24 #wg0에 할당될 내부(VPN 내부) IP주소
ListenPort = 51820 # WireGuard 서버가 수신 대기할 UDP 포트 번호를 지정
PrivateKey = (서버의 privatekey 내용)

# 클라이언트 설정은 나중에 추가
```

address는 wg0에 할당될 내부(VPN 내부) IP주소로 클라이언트들은 이 주소를 통해서 서버에 접근 (게이트웨이로 사용)

- 포트 열기 (ufw 사용 시)

``` bash
sudo ufw allow 51820/udp
sudo ufw enable
```

- IP 포워딩 활성화(IP 라우팅)

``` bash
echo 'net.ipv4.ip_forward=1' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

net.ipv4.ip_forward를 활성화하는 명령로 이 값을 `1`로 설정하면 시스템이 **라우터**처럼 작동하게 된다 즉, 자신에게 온 패킷이더라도 목적지가 다른 네트워크인 경우, 해당 패킷을 적절한 네트워크 인터페이스를 통해 다른 네트워크로 전달(포워딩)한다

&nbsp;

### 3. 클라이언트에서 WireGuard 설치 및 구성

[WireGuard 앱 다운](https://www.wireguard.com/install/){:target="_blank"}

<center>
<img src="https://github.com/user-attachments/assets/3e0c971f-5884-4e20-8145-f182ca07c193" style="zoom:50%;">
</center>

Add Empty Tunnel을 통해서 연결을 추가한다

클라인언트에서 구성 예시

``` bash
[Interface] # 클라이언트 장치 자체의 WireGuard 터널 인터페이스 설정
PrivateKey = (클라이언트 private key)
Address = 10.8.0.2/24 # 클라이언트 인터페이스(wg0 또는 설정된 이름)에 할당될 VPN 내부 IP 주소
DNS = 8.8.8.8 # 클라이언트 시스템에 설정될 DNS 서버의 IP 주소

[Peer] # 클라이언트가 연결할 WireGuard 서버(피어)에 대한 정보
PublicKey = (서버 public key)
Endpoint = (서버 공인 IP 또는 로컬 IP):51820 # WireGuard 서버의 실제 네트워크 주소# 
AllowedIPs = 0.0.0.0/0 # 서버를 통해 라우팅할 트래픽의 목적지 IP 주소 범위
PersistentKeepalive = 25 # 클라이언트가 서버로 킵얼라이브 패킷을 보내는 주기(초 단위)
```

&nbsp;

### 4. 서버에 클라이언트 정보 추가

``` bash
[Peer]
PublicKey = (클라이언트 public key)
AllowedIPs = 10.8.0.2/32 # 연결될 클라이언트의 주소
```

&nbsp;

### 5.  WireGuard 서비스 시작

- 서버에서 시작

``` bash
sudo systemctl start wg-quick@wg0
sudo systemctl enable wg-quick@wg0
```

&nbsp;

### 6. 동작확인

- **서버에서**

``` bash
sudo wg show
ping 10.8.0.2
```

- **클라이언트에서**  `ping 10.8.0.1` 또는 인터넷 접속 확인

&nbsp;

## 발생 문제

위 과정을 통해서 서버와 클라이언트는 통신이 되었지만 인터넷이 안되었다

서버에서 `sudo iptables -L -n -v`를 통해서 iptables의 정책을 확인하고  현재 `FORWARD` 체인의 기본 정책이 `DROP`으로 설정되어 있고, `ufw-user-forward` 체인에서 별도로 명시적인 `ACCEPT` 규칙이 없는 상태를 파악했다

&nbsp;

### 문제 요약

- VPN 클라이언트는 서버에 연결 가능하지만, 그 이후 인터넷으로는 나가지 못함.
- `iptables`의 `FORWARD` 체인이 `DROP`이며, NAT 설정은 되어 있음에도 **포워딩을 허용하는 규칙이 없음**.
- 즉, 클라이언트의 패킷이 서버를 통과해 외부로 나가는 것이 차단되고 있음.

&nbsp;

### 해결 `ufw`를 통해 포워딩 허용 규칙 추가

``` bash
# wg0 → enX0 포워딩 허용
sudo ufw route allow in on wg0 out on enX0
```

&nbsp;

## 결과

결과적으로 같은 pc를 사용했지만 vpn을 켜고 끔에 따라서 공인 IP주소가 바뀐것을 확인할 수 있다

<table><td><center><img alt="" src="https://github.com/user-attachments/assets/c62a5aac-4751-47c7-b986-dae42e9ae9c0" style="zoom:40%;" /></center></td><td><center><img alt="" src="https://github.com/user-attachments/assets/b011ea8b-8e54-489e-b01c-9daa9071c828" style="zoom:40%;" /></center></td></table>

