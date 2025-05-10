---
layout: post
title: "SwiftUI 실습 5 "
tags:  swiftui OutlineGroup DisclosureGroup
image: "https://github.com/user-attachments/assets/a6079de1-fd59-4ccb-b8e9-1de6e7a94969"
---

지금까지 List 뷰를 목록으로 표현할 때만 사용했다 목록 내에 계층적 정보를 표시하는 방법을 사용해서 탐색을 용이하게 한다 OutlineGroup을 사용한다면 디스클로저 컨트롤(리스트 옆에 화살표가 표시되어 접을 수 있음) 을 이용해서 리스트를 표시 할 수 있고 이와 비슷한 기능으로 DisclosureGroup을 사용할 수도 있다

### &nbsp;

### 기본 리스트 만들기

실습을 하기 위해서 기본 리스트를 만든다 먼저 데이터의 형태를 struct로 정의하고 데이터를 넣는다 그다음 리스트를 출력한다

``` swift
//ContentView

import SwiftUI

struct CarInfo: Identifiable {
    var id = UUID()
    var name: String
    var image: String
    var children: [CarInfo]?
}

let carItems: [CarInfo] = [
    
    CarInfo(name: "Hybrid Cars", image: "leaf.fill", children: [
        CarInfo(name: "Toyota", image: "car.circle", children : [
            CarInfo(name: "Prius", image: "leaf.fill"),
            CarInfo(name: "Highlander Hybrid", image: "leaf.fill"),
            CarInfo(name: "Lexus", image: "car.circle", children: [
                    CarInfo(name: "Lexus RX", image: "leaf.fill"),
                    CarInfo(name: "Lexus NX", image: "leaf.fill")])
        ]),
        CarInfo(name: "Ford", image: "car.circle", children : [
            CarInfo(name: "Fusion Energi", image: "leaf.fill"),
            CarInfo(name: "Escape", image: "leaf.fill"),
            CarInfo(name: "Volvo", image: "car.circle", children: [
                    CarInfo(name: "S90 Hybrid", image: "leaf.fill"),
                    CarInfo(name: "XC90 Hybrid", image: "leaf.fill")])
        ]),
    ]),
    
    CarInfo(name: "Electric Cars", image: "bolt.car.fill", children: [
        CarInfo(name: "Tesla", image: "car.circle", children : [
            CarInfo(name: "Model 3", image: "bolt.car.fill")
        ]),
        CarInfo(name: "Karma", image: "car.circle", children : [
            CarInfo(name: "Revero GT", image: "bolt.car.fill")
        ])
    ])
]

struct CellView: View {
    var item: CarInfo
    
    var body: some View {
        HStack {
            Image(systemName: item.image)
                .resizable()
                .scaledToFit()
                .frame(width:25, height:25)
            Text(item.name)
        }
        .padding()
    }
}


struct ContentView: View {
    
    var body: some View {
        List(carItems, children: \.children) { item in
            CellView(item: item)
        }
    }
}

#Preview {
    ContentView()
}
```

<center>
<img src="https://github.com/user-attachments/assets/a6079de1-fd59-4ccb-b8e9-1de6e7a94969" style="zoom:40%;">
</center>

화면처럼 다른 리스트들과는 다르게 계층적으로 표시됐다 이는 리스트의 매개변수 children을 사용해서 그렇다

`children`: 리스트 항목의 하위 데이터를 나타내는 트리 구조를 만들 때 사용된다 리스트 아이템이 재귀적으로 표시

`selection`: 리스트에서 항목을 선택하거나 다중 선택할 때 사용되며, 선택된 항목을 추적할 수 있다

&nbsp;

### OutlineGroup 사용하기

OutLineGroup을 사용하기 위해서 List부분을 수정했다

``` swift
struct ContentView: View {
    
    var body: some View {
        List{
            ForEach(carItems) { carItem in
                Section(header: Text(carItem.name)) {
                    OutlineGroup(carItem.children ?? [CarInfo]() , children: \.children) { item in
                        CellView(item: item)
                    }
                }
            }
        }.listStyle(InsetListStyle())
    }
}
```

<center>
<img src="https://github.com/user-attachments/assets/fba8494c-a4d0-42a6-be0b-24f12a4aaa1d" style="zoom:50%;">
</center>

여기서는 첫번째 항목을 가져와 section의 이름으로 설정한다 그뒤에 outlinegroup를 통해서 하위 항목으로 루프를 돈다 

&nbsp;

### DisclosureGroup 사용하기

DisclosureGroup은 간단한 확장/축소 인터페이스가 필요할 때 사용한다

``` swift
// SettingsView
import SwiftUI

struct SettingsView: View {
    @State private var hybridState: Bool = false
    @State private var electricState: Bool = true
    @State private var fuelCellState: Bool = false
    @State private var inversionState: Bool = true
    
    var body: some View {
        Form {
            DisclosureGroup{
                ToggleControl(title: "Hybrid car", state: hybridState)
                ToggleControl(title: "electric car", state: electricState)
                ToggleControl(title: "fuel cell car", state: fuelCellState)
            } label: {
                Label("car fuel type", systemImage: "car")
            }
            DisclosureGroup{
                ColorControl(color: .red, label: "Red")
                ColorControl(color: .blue, label: "Blue")
                ToggleControl(title: "inversion car", state: inversionState)
            } label: {
                Label("car color", systemImage: "scribble.variable")
            }
        }
    }
}

struct ColorControl: View{
    var color: Color
    var label: String
    
    var body: some View{
        HStack{
            Text(label)
            Spacer()
            Rectangle()
                .fill(color)
                .frame(width: 30, height: 30)
        }
        .padding()
        .scaledToFill()
    }
}

struct ToggleControl: View{
    var title: String
    @State var state: Bool
    
    var body: some View{
        Toggle(title, isOn: $state)
            .padding(.leading)
    }
}

#Preview {
    SettingsView()
}
```

<center>
<img src="https://github.com/user-attachments/assets/3a99e666-f02e-45cd-a3ca-83e6eeab09ea" style="zoom:50%;">
</center>

여기서 DisclosureGroup을 2개로 사용해서 나눴다 label을 통해서 접었을때 보이는 이름을 설정할 수 있다

이전에 List와 이번  OutlineGroup, DisclosureGroup 모두 리스트와 비슷한 역할을 한다 하지만 각각 특성이 다르기 때문에 list만 사용한다기 보다 상황에 맞추어 사용해야한다

&nbsp;

### 요약

|          특성           |                    OutlineGroup                     |                      List with children                      |                      DisclosureGroup                      |
| :---------------------: | :-------------------------------------------------: | :----------------------------------------------------------: | :-------------------------------------------------------: |
|      **주요 용도**      |  트리 구조 데이터를 재귀적으로 표현하는 데 특화됨   | 트리 구조뿐만 아니라 일반적인 리스트를 포함한 다양한 데이터 표현 | 단일 그룹 또는 항목의 확장/축소를 간단하게 처리할 때 사용 |
|   **확장/축소 방식**    |         자동으로 트리 구조를 확장/축소 가능         | children을 통해 트리 구조를 표현할 수 있지만, 기본적으로 리스트 구조 |      단일 레벨의 확장/축소만 가능 (트리 구조는 아님)      |
| **유연성 및 추가 기능** | 트리 구조 표현에 최적화되어 있고 리스트 기능이 없음 |  선택, 스와이프, 편집 모드 등 다양한 리스트 관련 기능 제공   |   간단한 레이아웃으로 특정 항목을 확장/축소하는 데 적합   |
| **재귀적 데이터 처리**  |        계층적 트리 구조의 재귀적 처리를 지원        |      재귀적으로 트리 구조 표현 가능 (children 사용 시)       |             재귀적 트리 구조는 지원하지 않음              |
|      **선택 기능**      |                   선택 기능 없음                    |             selection 매개변수를 통해 선택 가능              |                                                           |

 
