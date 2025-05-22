---
tags:  flutter
image: "https://github.com/user-attachments/assets/3becfa92-1a22-4d4b-8c70-682c83877836"
---

Mapping 앱을 홍보하기 전에 안드로이드 버전을 만들어야했다(아무래도 사용자들이 아이폰만 있다면 유저수가 더 적어지고 메모를 작성할 사람이 줄어들기 때문이다)

<center>
<img src="https://github.com/user-attachments/assets/3becfa92-1a22-4d4b-8c70-682c83877836" style="zoom:50%;">
</center>

[playstore ](https://play.google.com/store/apps/details?id=com.min.MappingApp&pli=1){:target="_blank"} 먼저 여기서 앱을 다운 받을 수 있다

앱을 처음 만들때는 swift를 공부하기 위해서 시작한 느낌도 있어서 안드로이드 버전을 만들 생각은 안했다 그래서 이번에 안드로이드 버전을 만들때 네이티브로 만들까 크로스 플랫폼으로 만들까 고민을 하다가 빠르게 배울 수 있는 flutter를 이용하기로 했다(이후 하나로 합칠 가능성도 존재하기 때문)

&nbsp;

## 앱 개발 기간

앱 개발은 iOS 버전을 다 개발하고 나서 들어가서 3월 부터 5월 중순까지 **약 2개월 반**이 소요됐다 기존에 디자인과 형식은 이미 만들어 놨기 때문에 iOS보다 시간기 훨씬 적게 들었다 그래도 앱 개발과 동시에 flutter를 학습하느라 시간이 꽤 소요된것 같다

&nbsp;

## 어려웠던점

기존에 개발할때와는 다르게 디자인 부분에서는 시간이 크게 소요되지 않았지만 여러 위젯중에서 이기능을 구현할려면 어떤 위젯을 사용해야하는지 몰라서 직접 기능을 개발하다가 뒤 늦게 위젯을 찾아서 시간이 많이 낭비되었다

그리고 iOS앱을 만들때는 애플 기본 지도를 사용해서 괜찮았지만 여기서는 구글 맵 api를 사용해서 처음으로 키적용하는게 어려웠다(키를 숨겨야함)

앱을 출시할때 카카오 로그인, 구글 로그인 모두 릴리즈 키로 변경해야했었다 아이폰은 이과정이 필요 없었는데 키를 변경하고 해시 적용하고 릴리즈 버전에서도 키를 적용하는게 번거로웠다 **구글 로그인은 나중에 앱 서명키를 구글에서 받아서 해당 sha-1을 oauth에 등록해야 릴리즈 버전에서 로그인이 가능했다**

모든게 다 처음하는거라서 다 어려웠지만 그중 마지막으로 화면 크기에 따른 위젯 크기 설정이 가장 어려웠다 iOS(아이폰은 약 4개 정도? 만 설정하면 됨)와 다르게 안드로이드는 화면 비율이 여러개가 존재해서 해당 화면마다 적응형으로 동작해야한다 아쉽게도 여러 비율을 테스트해보지 못해서 사용자의 화면에 안맞을 수 있다

&nbsp;

## 5월 21일 앱 출시

그렇게 앱개발을 완료하고 5월 21일 교육을 받던 도중에 출시 되었다고 연락이 왔다 앱을 출시하면서 처음 하는 프레임워크라 어려움이 많았지만 내가 생각한 아이디어를 실제로 구현해서 출시했다는 점이 매우 만족스러웠다 이제 다시 iOS버전 업데이트하고 코드 리팩토링을 하면서 flutter앱과 같이 업그레이드 해야겠다

<table><td><center><img alt="" src="https://github.com/user-attachments/assets/623c8664-22c7-49bc-8d36-5895c974508a" style="zoom:30%;" /></center></td><td><center><img alt="" src="https://github.com/user-attachments/assets/4aec4806-1217-4a21-99fb-29f3bef5ac06" style="zoom:30%;" /></center></td></table>

<table><td><center><img alt="" src="https://github.com/user-attachments/assets/23ad6f4c-208b-456e-a609-9f2aeb1519f0" style="zoom:30%;" /></center></td><td><center><img alt="" src="https://github.com/user-attachments/assets/8a100fd3-883a-447f-9d5e-5f2d3a2b53fe" style="zoom:30%;" /></center></td></table>
