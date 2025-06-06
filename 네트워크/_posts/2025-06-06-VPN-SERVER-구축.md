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

