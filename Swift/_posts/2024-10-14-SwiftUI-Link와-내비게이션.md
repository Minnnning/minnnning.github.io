---
layout: post
title: "SwiftUI Link와 네비게이션"
tags:  swiftui List NavigationStack NavigationLink
image: "https://github.com/user-attachments/assets/7bb92be8-083b-40dc-9504-26eaa027b671"
---

SwiftUI의 List는 수직 방향의 리스트 형태로 정적 동적 데이터를 모두 표현할 수 있고 추가, 삭제, 순서 재정렬 기능을 수행한다 또한 터치했을때 다른 영역으로 이동이 가능한 Navigationstack컴포넌트와 NavigationLink컴포넌트를 이용한다

&nbsp;

## List

``` swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        List {
            HStack {
                Image (systemName: "trash.circle.fill")
                Text("Take out the trash")
            }
            HStack {
                Image (systemName: "person.2.fill")
                Text("Pick up the kids")
            }
            HStack {
                Image(systemName: "car.fill")
                Text ("Wash the car")
            }
        }
    }
}
```

<center>
<img src="https://github.com/user-attachments/assets/606bdc25-3fc5-412c-8ece-33e506da10b4" style="zoom:50%;">
</center>

위 코드처럼 하나의 리스트 항목에 추가로 뷰를 넣을 수 있다

&nbsp;

### 동적 리스트

시간에 따라 변하는 항목을 포함한다면 동적 리스트라고 할 수 있다 이런 변화를 반영할려면 업데이트를 해야한다 이런 타입의 리스트를 지원할려면 표시될 데이터는 **Identifiable** 프로토콜을 따르는 클래스 또는 구조체를 포함해야한다 또한 이 프로토콜을 사용할려면 각 항목을 고유하게 식별할 수 있는 **id**프로퍼티를 가져야한다

``` swift
import SwiftUI

struct ToDoItem: Identifiable {
    var id = UUID()
    var task: String
    var imageName: String
}

struct ContentView: View {
    
    @State var listData: [ToDoItem] = [
        ToDoItem(task: "Take out trash", imageName: "trash.circle.f111"),
        ToDoItem(task: "Pick up the kids", imageName: "person.2.f111"),
        ToDoItem(task: "Wash the car", imageName: "car.fi11")
    ]
    
    var body: some View {
        List(listData) { Item in
            HStack {
                Image (systemName: Item.imageName)
                Text(Item.task)
            }
        }
    }
}
```

<center>
<img src="https://github.com/user-attachments/assets/e09790f0-1594-4bd3-83c6-c3e91fb58cea" style="zoom:50%;">
</center>

이렇게 표시가 된다 만약 여기에 정적인 부분이 추가적으로 들어가야된다면 동적리스트 구문을 **ForEach**구문을 사용하면 된다

``` swift
import SwiftUI

struct ToDoItem: Identifiable {
    var id = UUID()
    var task: String
    var imageName: String
}

struct ContentView: View {
    
    @State var listData: [ToDoItem] = [
        ToDoItem(task: "Take out trash", imageName: "trash.circle.f111"),
        ToDoItem(task: "Pick up the kids", imageName: "person.2.f111"),
        ToDoItem(task: "Wash the car", imageName: "car.fi11")
    ]
    
    var body: some View {
        List {
            Section(header: Text("To Do List")){
                Button(action: {
                    listData.append(ToDoItem(task: "Buy groceries", imageName: "cart.badge.plus"))
                }) {
                    Text("add item")
                }
            }
            
            Section(header: Text("To Do List2")){
                ForEach(listData) { Item in
                    HStack {
                        Image (systemName: Item.imageName)
                        Text(Item.task)
                    }
                }
            }
        }
    }
}
```

<center>
<img src="https://github.com/user-attachments/assets/f4c54615-0e40-4254-a19c-1e1990f70037" style="zoom:50%;">
</center>

정적 부분(버튼 부분)과 동적 데이터가 들어가는 부분을 나누고 또한 Section을 추가해서 리스트 내부에서 구별을 할 수 있도록 했다 사진에서는 add item을 눌러 buy groceries을 추가했다

&nbsp;

### 새로고침 기능

아이폰에서 화면을 맨위로 올리면 현재 화면이 새로 고침된다 리스트에서 이 기능을 활성화 할 수 있다 **.refreshable**을 사용한다

``` swift
List {
            // 생략
        }
        .refreshable {
            listData = [
                ToDoItem (task: "Order dinner", imageName: "dollarsign.circle.fill"),
                ToDoItem (task: "Call financial advisor", imageName: "phone.fill"),
                ToDoItem (task: "Sell the kids", imageName: "person.2.fi11")
            ]
        }
```

화면을 맨위로 옮기면 리스트 데이터들이 새로 작성한 데이터로 교체된다

&nbsp;

## NavigationStack과 NavigationLink

리스트에 있는 항목을 터치해서 이동하고 싶다면 리스트를 NavigationStack에 넣고 각행을 NavigationLink으로 감싼다 

``` swift
struct ContentView: View {
    
    @State var listData: [ToDoItem] = [
        ToDoItem(task: "Take out trash", imageName: "trash.circle.f111"),
        ToDoItem(task: "Pick up the kids", imageName: "person.2.f111"),
        ToDoItem(task: "Wash the car", imageName: "car.fi11")
    ]
    
    var body: some View {
        NavigationStack {
            List {
                Section(header: Text("To Do List")){
                    Button(action: {
                        listData.append(ToDoItem(task: "Buy groceries", imageName: "cart.badge.plus"))
                    }) {
                        Text("add item")
                    }
                }
                
                Section(header: Text("To Do List2")){
                    ForEach(listData) { Item in
                        NavigationLink(value: Item.task){
                            HStack {
                                Image (systemName: Item.imageName)
                                Text(Item.task)
                            }
                        }
                    }
                }
            }
            .navigationDestination(for: String.self) { task in
                Text("Selected task = \(task)")
            }
        }
    }
}
```

<table><td><center><img alt="" src="https://github.com/user-attachments/assets/7bb92be8-083b-40dc-9504-26eaa027b671" style="zoom:40%;" /></center></td><td><center><img alt="" src="https://github.com/user-attachments/assets/d4f6658a-9f42-43f4-9a50-c11dcb45b994" style="zoom:30%;" /></center></td></table>

리스트 행을 클릭한다면 해당 리스트가 가진 문자열을 새로운 뷰화면에서 보여준다

&nbsp;

### 네비게이션 경로

NavigationStack은 이름 그대로 스택을 제공한다 NavigationLink로 이동한다면 스택에 **푸시**가 되는것이다 사용자는 이렇게 들어온 뷰를 **pop**할 수 있는데 위 사진에서 보이는 Back버튼을 통해서 pop 할 수 있다 이렇게 사용자가 이동하는 경로는 **네비게이션 경로**라고 한다

``` swift
struct ContentView: View {
    
    @State private var stackPath = NavigationPath() //경로
    
    @State var listData: [ToDoItem] = [
        ToDoItem(task: "Take out trash", imageName: "trash.circle.f111"),
        ToDoItem(task: "Pick up the kids", imageName: "person.2.f111"),
        ToDoItem(task: "Wash the car", imageName: "car.fi11")
    ]
    
    var body: some View {
        NavigationStack(path: $stackPath) {
            List {
                Section(header: Text("To Do List")){
                    Button(action: {
                        listData.append(ToDoItem(task: "Buy groceries", imageName: "cart.badge.plus"))
                    }) {
                        Text("add item")
                    }
                    Text("\(stackPath) items")
                }
                
                Section(header: Text("To Do List2")){
                    ForEach(listData) { Item in
                        NavigationLink(value: Item.task){
                            HStack {
                                Image (systemName: Item.imageName)
                                Text(Item.task)
                            }
                        }
                    }
                }
            }
            .navigationDestination(for: String.self) { task in
                Text("Selected task = \(task)")
                Text("\(stackPath)")
                Text("\(stackPath.count) items")
                Button("Go back") {
                    stackPath.removeLast()
                }
            }
        }
    }
}
```

경로를 stackPath에 저장해서 사용할 수 있다 이를 이용하면 하나씩 되돌아가는것이 아니라 한번에 돌아가거나 원하는 페이지로 한번에 이동할 수 있다

<table><td><center><img alt="" src="https://github.com/user-attachments/assets/830683d8-501f-444a-9ad9-e68616ffe25a" style="zoom:40%;" /></center></td><td><center><img alt="" src="https://github.com/user-attachments/assets/95b5eabf-753b-40d7-93d1-6499837bc74d" style="zoom:35%;" /></center></td></table>

add item 밑에는 현재 저장된 경로를 띄웠고 리스트중 2번째 행을 클릭했을때 결과를 오른쪽에 표시했다 경로 저장 형식과 몇단계까지 들어왔는지 확인할 수 있다 여기서는 1단계라 1로 표시 go back버튼은 경로속에 있는것을 초기화시켜서 돌아간다

`stackPath.append(value)`를 통해서 원하는 곳으로 바로 이동할 수 있다

&nbsp;

### 네비게이션 바 커스텀

``` swift
.navigationBarTitle(Text("To Do List"))
            .navigationBarItems(leading: Button(action: {
                listData.append(ToDoItem(task: "Buy groceries", imageName: "cart.badge.plus"))
            }) {
            Text ("Add")
            })
```

이 부분을 추가해서 네이게이션 바에 이름과 추가 버튼을 생성

<center>
<img src="https://github.com/user-attachments/assets/d0eb8fb4-a3fb-441b-9f87-8c2a256e4b25" style="zoom:50%;">
</center>

&nbsp;

## 편집 기능

위에서는 사용자가 리스트 항목을 추가했었다 편집을 할려면 리스트 순서 변경 삭제 기능이 필요하다 삭제 기능은 각 행에 onDelete()수정자를 추가한다

``` swift
struct ContentView: View {
    
    @State private var stackPath = NavigationPath()
    
    @State var listData: [ToDoItem] = [
        ToDoItem(task: "Take out trash", imageName: "trash.circle.f111"),
        ToDoItem(task: "Pick up the kids", imageName: "person.2.f111"),
        ToDoItem(task: "Wash the car", imageName: "car.fi11")
    ]
    
    var body: some View {
        NavigationStack(path: $stackPath) {
            List {
                Section(header: Text("To Do List")){
                    Button(action: {
                        listData.append(ToDoItem(task: "Buy groceries", imageName: "cart.badge.plus"))
                    }) {
                        Text("add item")
                    }
                }
                
                Section(header: Text("To Do List2")){
                    ForEach(listData) { Item in
                        NavigationLink(value: Item.task){
                            HStack {
                                Image (systemName: Item.imageName)
                                Text(Item.task)
                            }
                        }
                    }.onDelete(perform: deleteItem)
                }
            }
            .navigationDestination(for: String.self) { task in
                Text("Selected task = \(task)")
                Button("Go back") {
                    stackPath.removeLast()
                }
            }
        }
    }
    func deleteItem(at indexSet: IndexSet) {
        listData.remove(atOffsets: indexSet)
    }
}
```

<center>
<img src="https://github.com/user-attachments/assets/4fd976c3-354e-4da2-8bb0-6e1860813a82" style="zoom:50%;">
</center>

항목을 이동시키기 위해서는 onMove()수정자를 사용해야한다 

``` swift
struct ContentView: View {
    
    @State private var stackPath = NavigationPath()
    
    @State var listData: [ToDoItem] = [
        ToDoItem(task: "Take out trash", imageName: "trash.circle.f111"),
        ToDoItem(task: "Pick up the kids", imageName: "person.2.f111"),
        ToDoItem(task: "Wash the car", imageName: "car.fi11")
    ]
    
    var body: some View {
        NavigationStack(path: $stackPath) {
            List {
                Section(header: Text("To Do List")){
                    Button(action: {
                        listData.append(ToDoItem(task: "Buy groceries", imageName: "cart.badge.plus"))
                    }) {
                        Text("add item")
                    }
                }
                
                Section(header: Text("To Do List2")){
                    ForEach(listData) { Item in
                        NavigationLink(value: Item.task){
                            HStack {
                                Image (systemName: Item.imageName)
                                Text(Item.task)
                            }
                        }
                    }.onDelete(perform: deleteItem)
                        .onMove(perform: moveItem)
                }
            }
            .toolbar { EditButton() // 수정 버튼 추가
                        }
            .navigationDestination(for: String.self) { task in
                Text("Selected task = \(task)")
                Button("Go back") {
                    stackPath.removeLast()
                }
            }
        }
    }
    func deleteItem(at indexSet: IndexSet) {
        listData.remove(atOffsets: indexSet)
    }
    func moveItem(from source: IndexSet, to destination: Int) {
        listData.move(fromOffsets: source, toOffset: destination)
    }

}
```

<center>
<img src="https://github.com/user-attachments/assets/6fe76c75-674f-4e03-ac0f-045df37af4e2" style="zoom:50%;">
</center>

오른쪽 위 edit버튼을 누른 뒤 수정이 가능하다
