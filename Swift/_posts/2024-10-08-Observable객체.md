---
layout: post
title: "SwiftUI 실습 2"
tags:  swiftui 
excerpt_image: "https://github.com/user-attachments/assets/e27f7bba-0139-4bc6-b87e-3525700216d1"
---

Observable 객체와 Environment 객체를 이용한 실습이다 Observable 객체는 시간이 지남에 따라 반복적으로 변하는 데이터 값, 동적 데이터를 래핑하는데 사용된다 Environment 객체는 앱 전반적으로 사용하는 데이터를 저장하는데 사용한다

&nbsp;

## Observable 객체 이용하기

1초마다 1씩 증가하는 Observable 객체를 생성한다

``` swift
// TimerData.swift
import Foundation

class TimerData: ObservableObject {
    @Published var timeCount: Int = 0 
    var timer: Timer?
    
    init() {
        timer = Timer.scheduledTimer(timeInterval: 1, target: self, selector: #selector(timerDidFire),userInfo: nil, repeats: true)
    }
    
    @objc func timerDidFire() {
        timeCount += 1
    }
    func resetCount() {
        timeCount = 0
    }
}

```

ObservableObject 프로토콜을 구현하는 것을 선언했고 timer는 1초마다 timerDidFire함수를 호출해서 timeCount를 1씩 증가시켰다 @Published 프로퍼티 래퍼를 선언해서 프로젝트 내에 있는 뷰에서 확인 할 수 있다

``` swift
// ContentView.swift
import SwiftUI

struct ContentView: View {
    @StateObject var timerData: TimerData = TimerData() // 인스턴스 생성
    
    var body: some View {
        NavigationView {
            VStack {
                Text("Tiemr count = \(timerData.timeCount)")
                    .padding()
                Button(action: resetCount){
                    Text("Reset")
                }
            }
        }
    }
    func resetCount() {
        timerData.resetCount()
    }
}

#Preview {
    ContentView()
}

```

이렇게 만들게 된다면 1초마다 1씩 증가하는 timerData값을 화면에 보여준다

<center>
<img src="https://github.com/user-attachments/assets/e27f7bba-0139-4bc6-b87e-3525700216d1" style="zoom:50%;">
</center>

&nbsp;

### 두번째 뷰 추가하기

새로운 파일 SecondView.swift를 생성한다

``` swift
import SwiftUI

struct SecondView: View {
    
    @StateObject var timerData: TimerData // 인스턴스 생성은 안함
    
    var body: some View {
        VStack {
            Text("SecondView")
            Text("Tiemr count2: \(timerData.timeCount)")
                .font(.headline)
        }
        .padding()
    }
}

#Preview {
    SecondView(timerData: TimerData()) // 인스턴스 생성을 여기서 함
}
```

<center>
<img src="https://github.com/user-attachments/assets/f0c61976-e592-42cb-8585-fa50278fcfc9" style="zoom:50%;">
</center>

이렇게 작성한다면 이 뷰는 자신만의 timerData 인스턴스를 가진다 만약 contentView에 있는 인스턴스를 같이 사용할려면 화면을 전달할 때 ObservedObject객체를 SecondView에 전달해줘야한다

&nbsp;

## 동일 객체 사용하도록 수정

먼저 ContentView에서 SecondView로 이동할 수 있도록 연결한다

### 값 전달로 같은 인스턴스 공유

``` swift
import SwiftUI

struct ContentView: View {
    @StateObject var timerData: TimerData = TimerData()
    
    var body: some View {
        NavigationView {
            VStack {
                Text("Tiemr count = \(timerData.timeCount)")
                    .padding()
                Button(action: resetCount){
                    Text("Reset")
                }
                NavigationLink(destination: SecondView(timerData: timerData)){ //인스턴스 전달
                    Text("Go to Second View")
                }
                .padding()
            }
        }
    }
    func resetCount() {
        timerData.resetCount()
    }
}

#Preview {
    ContentView()
}
```

<table><td><center><img alt="" src="https://github.com/user-attachments/assets/6a63a824-0979-4647-afb8-b6a4fea88566" style="zoom:30%;" /></center></td><td><center><img alt="" src="https://github.com/user-attachments/assets/4912cf75-4f1e-4124-9d49-19c1c74350f6" style="zoom:30%;" /></center></td></table>

위 사진처럼 뷰를 넘어가도 똑같이 숫자가 증가하는 것을 확인 할 수 있다

&nbsp;

## Environment 객체 사용하기

Observable 객체는 값을 전달하기 위해서 해당 뷰로 이동할때 값도 전달해줘야 했다 하지만 Environment 객체를 이용한다면 서로 다른 뷰에서 값을 전달하지 않아도 같은 객체를 접근 가능하다

``` swift
//contnetview
import SwiftUI

struct ContentView: View {
    @StateObject var timerData: TimerData = TimerData()
    
    var body: some View {
        NavigationView {
            VStack {
                Text("Tiemr count = \(timerData.timeCount)")
                    .padding()
                Button(action: resetCount){
                    Text("Reset")
                }
                NavigationLink(destination: SecondView()){
                    Text("Go to Second View")
                }
                .padding()
            }
        }
        .environmentObject(timerData) // timerData인스턴스를 뷰 계층에 삽입
    }
    func resetCount() {
        timerData.resetCount()
    }
}

#Preview {
    ContentView()
}
```

값 전달을 지우고 timerData를 뷰 계층에 삽입

``` swift
//secondview
import SwiftUI

struct SecondView: View {
    
    @EnvironmentObject var timerData: TimerData // EnvironmentObject이용
    
    var body: some View {
        VStack {
            Text("SecondView")
            Text("Tiemr count2: \(timerData.timeCount)")
                .font(.headline)
        }
        .padding()
    }
}

#Preview {
    SecondView().environmentObject(TimerData())
}

```

