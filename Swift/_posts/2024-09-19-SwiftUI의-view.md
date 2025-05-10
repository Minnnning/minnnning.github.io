---
layout: post
title: "SwiftUI의 View"
tags: view swiftui
image: "https://github.com/user-attachments/assets/99a264ce-633d-4155-9641-927ffe9d7792"
---

스위프트로 앱을 만들때 swiftui를 이용한다면 계층구조를 가지고 있다 App -> Scene(여러개) -> View(여러개) 이렇게 존재한다 여기서 app은 애플리케이션의 실행중인 각 인스턴스의 시작 및 생명주기를 처리한다 Scene은 하나의 영역 섹션을 나타낸다 일반적으로 전체 장치화면을 차지하는 창형태이다 view는 버튼, 레이블, 텍스트 필드등 사용자 인터페이스의 기본적인 빌딩블록이다

<center>
<img src="https://github.com/user-attachments/assets/99a264ce-633d-4155-9641-927ffe9d7792" style="zoom:40%;">
</center>

&nbsp;

## swiftui의 view

뷰는 view 프로토콜을 따르는 **구조체**로 선언된다 또한 **body** 프로퍼티를 가지고 있어야하면 body안에 뷰가 선언되어 있어야 한다

기본적인 view의 형태

``` swift
import SwiftUI

struct ContentView: View { // view 프로토콜
    var body: some View { // body 프로퍼티
        Text("asdfas")
        VStack {
            Image(systemName: "globe")
                .imageScale(.large)
                .foregroundStyle(.tint)
            Text("Good world!")
        }
        .padding()
    }
}

#Preview { // 미리보기
    ContentView()
}

```

 &nbsp;

### 뷰 추가하기

``` swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        VStack {
            Text("asdfas")
            VStack{
                Image(systemName: "globe")
                    .imageScale(.large)
                    .foregroundColor(.accentColor)
                Text("Good world!")
                HStack{
                    Text("Hello, World!")
                        .font(.headline)
                    Text("Nice daye!")
                }
            }
        }
        .padding()
        Text("asdfas")
    }
}

#Preview {
    ContentView()
}
```

위 코드에 포함된 text, vstack, hstack 모두 하나의 뷰처럼 간주된다 위 코드에서는 반환 구문이 없다 왜냐하면 단일 표현식으로 되어 있기 때문이다 여기에 표현식이 추가된다면 return을 추가해야한다

``` swift
import SwiftUI

struct ContentView: View {
    @State var text = "Hello, World!"
    
    var body: some View {
        var el: String = "qwer"
        if (text.count > 0) {
            el = "asdf"
        }
        return VStack {
            Text(el)
            Image(systemName: "globe")
                .imageScale(.large)
                .foregroundColor(.accentColor)
            Text("Good world!")
            }
        .padding()
    }
}
```

<center>
<img src="https://github.com/user-attachments/assets/51a4c51c-6d3d-4162-88ad-376847c739e7" style="zoom:50%;">
</center>

&nbsp;

### 하위 뷰 만들기

내가 만든 커스텀 뷰가 길어져서 보기 불편하다면 하위 뷰로 나눌수 있다

``` swift
import SwiftUI

struct ContentView: View {
    
    var body: some View {
        VStack {
            Text("asdfas")
                .padding()
            HStack{
                Image(systemName: "globe")
                    .imageScale(.large)
                    .foregroundColor(.accentColor)
                Text("Good world!")
            }
            MyHStackTests() //하위 뷰 이용
        }
        .padding()

    }
}


struct MyHStackTests: View { // 하위 뷰 작성
    var body: some View {
        HStack {
            Text("text1")
            Text("text2")
        }
    }
}
```

<center>
<img src="https://github.com/user-attachments/assets/fd5bda33-7afc-495d-9d26-cf46910e2b9f" style="zoom:50%;">
</center>

&nbsp;

### 프로퍼티의 뷰

하위 뷰를 생성하는 것 말고도 선언부 중 일부를 프로퍼티 값으로 이동하고 참조할 수 있다

``` swift
import SwiftUI

struct ContentView: View {
    
    let MyHStackTests =
            HStack {
                Text("text1")
                Text("text2")
        }
    
    var body: some View {
        VStack {
            Text("asdfas")
                .padding()
            HStack{
                Image(systemName: "globe")
                    .imageScale(.large)
                    .foregroundColor(.accentColor)
                Text("Good world!")
            }
            MyHStackTests
        }
        .padding()

    }
}
```

<center>
<img src="https://github.com/user-attachments/assets/fd5bda33-7afc-495d-9d26-cf46910e2b9f" style="zoom:50%;">
</center>

&nbsp;

###  뷰 변경하기(수정자)

수정자란 뷰의 모양과 동작을 변경하는데 사용하는 것들이다 이전 코드들은 보면 imageScale, padding, font등이 수정자에 해당한다

기본적으로 font에 제공하는 스타일

* Large Title

* Title1, Title2, Title3

* Headline

* Subheadline

* Body

* Callout

* Footnote

* Caption, Caption2

  이것을 이용한다면 사용자의 디바이스 설정에 따라 크기를 자동으로 바꿀 수 있다

&nbsp;

#### 수정자 순서

수정자들이 적용되는 순서가 중요하다 borde와 padding을 적용할때 border() 다음 padding()을 준다면 경계선을 먼저 그리고 패딩을 주기 때문에 글씨 바로 옆에 경계선이 나타난다 따라서 padding을 먼저 주어야 글씨 옆에 여백을 주고 경계선을 그릴수 있다 따라서 수정자가 정상적으로 적용이 안된다면 순서를 생각해 봐야한다

&nbsp;

#### 커스텀 수정자

css에서 클래스나 태그에 따라 클래스를 설정하는것 처럼 수정자를 커스텀 해서 사용할 수 있다 **ViewModifier**프로토콜을 따르는 구조체로 선언해서 사용한다

``` swift
import SwiftUI

struct ContentView: View {
    
    var body: some View {
        VStack {
            Text("asdfas")
                .modifier(StandardTitle())
                .padding()
            HStack{
                Image(systemName: "globe")
                    .imageScale(.large)
                    .foregroundColor(.accentColor)
                Text("Good world!")
            }
        }
        .padding()

    }
}

struct StandardTitle: ViewModifier {
    func body(content: Content) -> some View {
        content
            .font(.title)
            .foregroundColor(.primary)
            .border(Color.primary, width: 1)
    }
}
```

<center>
<img src="https://github.com/user-attachments/assets/c945a192-908f-44a3-a332-6bc61fc40263" style="zoom:50%;">
</center>

&nbsp;

### 이벤트 처리

Button을 통해서 이벤트 처리를 할 때 button 뷰는 버튼의 내용과 함께 클릭이 감지될 때 호출될 메서드도 선언 해야한다

``` swift
import SwiftUI

struct ContentView: View {
    
    var body: some View {
        VStack {
            Text("asdfas")
            Image(systemName: "globe")
                .imageScale(.large)
                .foregroundColor(.accentColor)
            Text("Good world!")
            Button(action: pressed) {
                Text("click")
            }
        }
        .padding()
    }
    
    func pressed() { // 액션 함수 지정
        print("pressed")
    }
}
```

&nbsp;

### 커스텀 컨테이너 뷰

하위 뷰의 한가지 단점은 컨테이너 뷰의 컨텐츠가 정적이라는 것이다 따라서 하위뷰에 포함될 뷰를 동적으로 지정할 수 없다 하지만 **ViewBulider** 클로저 속성을 이용한다면 가능하다

``` swift
import SwiftUI

struct ContentView: View {
    
    var body: some View {
        VStack {
            Text("asdfas")
            Image(systemName: "globe")
                .imageScale(.large)
                .foregroundColor(.accentColor)
            Text("Good world!")
            MyVStack {
                Text("Hello, World1!")
                Text("Hello, World2!")
            }
        }
        .padding()
    }
    
   
}

struct MyVStack<Content: View>: View {
    let content: () -> Content
    init(@ViewBuilder content: @escaping () -> Content) {// ViewBuilder 속성사용
        self.content = content
    }
    
    var body: some View {
        VStack(spacing:15) {
            content()
        }
        .font(.title)
    }
}
```

&nbsp;

### 레이블 뷰

레이블 뷰는 아이콘과 텍스트가 나란히 배치된 형태의 두요소로 구성된다는 점이 다른 뷰들과 다르다 이미지는 sf symbol, 이미지등 사용이 가능하다 

``` swift
import SwiftUI

struct ContentView: View {
    
    var body: some View {
        VStack {
            Text("asdfas")
            Image(systemName: "globe")
                .imageScale(.large)
                .foregroundColor(.accentColor)
            Text("Good world!")
            Label("welcome", systemImage: "person.crop.circle")
        }
        .padding()
    }  
}
```

<center>
<img src="https://github.com/user-attachments/assets/cb8bf7a7-fa5b-4583-a423-6f5d70118dbd" style="zoom:50%;">
</center>
