---
layout: post
title: "SwiftUI에서 TabView 사용하기 "
tags:  swiftui TabView
excerpt_image: "https://github.com/user-attachments/assets/de4e4ae8-e239-4849-a211-7a69fdeb8a15"
---

TabView 컴포넌트는 탭 바에 있는 아이템을 사용자가 선택하거나 페이지 뷰 탭 스타일을 사용할 때 스와이프 동작을 만들어 여러 하위 뷰들 사이를 이동할 수 있게 해준다

&nbsp;

### TabView 생성하기

``` swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        TabView{
            Text("First cotent")
            Text("Second cotent")
            Text("Third cotent")
        }
        .padding()
    }
}

#Preview {
    ContentView()
}
```

<center>
<img src="https://github.com/user-attachments/assets/75ecb38c-246a-46a9-8314-5ec6783809d6" style="zoom:50%;">
</center>

이렇게 작성한다면 첫번째 뷰는 표시되지만 다른 뷰로 이동할 수 있는 방법이 없다 여기서 **PageTabViewStyle( )**을 TabView에 적용한다면 왼쪽 오른쪽 스와이프로 뷰를 이동할 수 있다

``` swift
TabView{
            Text("First cotent")
            Text("Second cotent")
            Text("Third cotent")
        }.tabViewStyle(PageTabViewStyle())
```

&nbsp;

### 탭 아이템 추가하기

위 화면에서는 첫번째 뷰말고 다른 뷰를 사용할 수 있다는 시각적 표시가 없다 **tabItem( )** 수정자를 이용해서 이용가능한 뷰를 화면 아래에 띄워줄 수 있다 탭아이템 구성요소는 Image와 Text로만 구성된다

``` swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        TabView{
            Text("First cotent")
                .tabItem {
                    Image(systemName: "1.circle")
                    Text("First")
                }
            Text("Second cotent")
                .tabItem {
                    Image(systemName: "2.circle")
                    Text("Second")
                }
            Text("Third cotent")
                .tabItem { Image(systemName: "3.circle") }
        }
        .padding()
    }
}
```

<center>
<img src="https://github.com/user-attachments/assets/2a633229-cdcb-4dcc-9b50-e9c7d54dcdd5" style="zoom:50%;">
</center>

&nbsp;

### 태그 추가하기

**.tag( )** 수정자와 selection 상태 프로퍼티를 이용해서 상태를 전달할 수 있다

``` swift
import SwiftUI

struct ContentView: View {
    @State var selection: Int = 100
    
    var body: some View {
        TabView(selection: $selection){
            Text("First cotent \(selection)")
                .tabItem {
                    Image(systemName: "1.circle")
                    Text("First")
                } .tag(100)
            Text("Second cotent \(selection)")
                .tabItem {
                    Image(systemName: "2.circle")
                    Text("Second")
                } .tag(200)
            Text("Third cotent \(selection)")
                .tabItem {
                    Image(systemName: "3.circle")
                    Text("Third")
                }.tag(123141)
        }
        .padding()
    }
}
```

<center>
<img src="https://github.com/user-attachments/assets/de4e4ae8-e239-4849-a211-7a69fdeb8a15" style="zoom:50%;">
</center>
