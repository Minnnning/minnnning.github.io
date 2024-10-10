---
layout: post
title: "AppStorage와 SceneStorage를 사용한 SwiftUI 데이터 지속성"
tags:  swiftui AppStorage SceneStorage
excerpt_image: ""
---

앱을 사용할 때 앱을 다시 시작해도 저장해야하는 데이터들이 존재한다 예를 들어서 사용자의 기본 설정이나 사용자가 마지막으로 접근했을 때 화면으로 복구되기 원할때 유용하다 이런경우에 @AppStorage와 @SceneStorage를 사용할 수 있다

&nbsp;

### @SceneStorage 프로퍼티 래퍼

SceneStorage는 개별 앱 화면 인스턴스의 범위 내에서 소량의 데이터를 저장하는데 사용된다 앱이 실행되는 사이에 화면 상태를 저장하고 복원하는데 주로 사용한다 사용자가 form을 입력하다가 전화등 다른 이유로 화면의 입력이 중단되었을때 입력하던 데이터를 저장시켜 다시 복원 가능하다

``` swift
@SceneStorage("city") var city: String = ""
var body: some View {
  TextEditor(text: $city)
}
```

&nbsp;

### @AppStorage 프로퍼티 래퍼

SceneStorage는 한 화면에 저장된 데이터는 앱 내의 다른 화면에서 접근할 수 없다 @AppStorage는 앱 전체르 통하여 접근 가능하고 사용해야하는 데이터를 저장하는데 사용한다  

``` swift
@AppStorage("mystore") var mytext: String = ""
```

데이터는 디폴트롤 표준 UserDefaults 저장소에 저장된다

앱 그룹에도 저장 할 수 있는데 앱 그룹은 앱이 동일한 그룹 내의 다른 앱 또는 타깃과 데이터를 공유할 수 있게 한다

&nbsp;

### SceneStorage 사용

프로젝트 생성후 content.swift에 작성한다
``` swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        
        TabView {
            SceneStorageView()
                .tabItem { Image(systemName: "1.circle")
                Text("Scene Storage")}
            
            AppStorageView()
                .tabItem { Image(systemName: "2.circle")
                Text("App Storage")}
        }
        .padding()
    }
}

#Preview {
    ContentView()
}
```

SceneStorageView.swift

``` swift
import SwiftUI

struct SceneStorageView: View {
    
    @SceneStorage("myText") var editorText: String = ""
    
    var body: some View {
        TextEditor(text: $editorText)
            .padding()
    }
}
#Preview {
    SceneStorageView()
}
```

위코드를 실행시키고 화면분할을 통해서 2개의 앱을 동시에 띄울 수 있다

<center>
<img src="https://github.com/user-attachments/assets/9130158f-9d41-4791-803e-108aa9f64587" style="zoom:50%;">
</center>

텍스트를 보면 서로 다르게 작성된다 따라서 화면에는 자신만의 저장된 데이터 복사본이 존재한다
&nbsp;

### AppStorage 상용

``` swift
# AppStorageView.swift
import SwiftUI

struct AppStorageView: View {
    
    @AppStorage("mytext") var editorText: String = "sample text"
    
    var body: some View {
        TextField("Enter text", text: $editorText)
    }
}
```

위 코드는 appstorage에 데이터를 저장하는것이다 위랑 똑같이 아이패드에서 앱을 동시에 킨다

<center>
<img src="https://github.com/user-attachments/assets/6737e089-1ff4-40cf-97dc-b55d6581c95d" style="zoom:50%;">
</center>

왼쪽에 작성하면 오른쪽에도 동시에 작성된다 따라서 한곳에 데이터를 저장한다는 것을 알 수 있고 앱을 껏다가 켜도 내가 작성한 글은 그대로 존재한다

&nbsp;

### 커스텀타입 저장하기

위 두개의 프로퍼티 래퍼는 특정 타입의 값만 저장을 허용한다 Bool, Int, Double, String, URL등 타입이 서로 다른것을 저장할려고 한다면 스위프트의 Data객체로 변환해서 저장해야한다

``` swift
struct User {
  var firstName: String
  var LastName: String
}
```

위 형태를 저장해야 할때 User는 지원하는 타입이 아니라 저장할 수 없다 저장하기전 Data인스턴스로 인코딩하고 캡슐화를 진행해야한다 *Encodable, Decodable 프로토콜을 준수*

``` swift
struct User: Encodable, Decodable {
  var firstName: String
  var LastName: String
}
```

&nbsp;

