---
layout: post
title: "Swift 데이터 타입 (3) 열거형"
date:   2021-11-23 15:35:47 +0900
categories:
tags: 열거형
excerpt: "연관된 항목들을 묶어서 표현함 프로그래머가 정의해준 항목 이외는 추가,제거가 불가능하다"
---

### **열거형**

연관된 항목들을 묶어서 표현함 프로그래머가 정의해준 항목 이외는 추가,제거가 불가능하다

&nbsp;

열거형 사용 용도

​    \- 제한된 선택지

​    \- 정해진 값 이외는 받지 않을때

​    \- 입력값이 한정 되어 있을때

``` swift
//열거형 선언
enum School {
    case primary
    case elementary
    case middle
    case high
    case unversity
}

enum School {
    case primary, elementary, middle, high, university
}
//두 표현은 같다

//열거형 변수 or 상수 선언
var highestLevel : School = School.high //변수에 항목 넣음

highestLevel = .university // 변수 값 변경
```

&nbsp;

&nbsp;

### **원시값**

열거형의 항목 case 자체로도 하나의 값이지만 원시값 RawVlaue 를 가질 수 있다

rawValue 프로퍼티로 원시값을 가져 올 수 있다

```swift
enum School : String {
    case primary = "유치원"
    case elementary = "초등학교"
    case middle = "중학교"
    case high = "고등학교"
    case university
}

print("저의 최종학력은 \(School.high)입니다 ") //저의 최종학력은 high입니다
print("저의 최종학력은 \(School.high.rawValue)입니다 ") //저의 최종학력은 고등학교입니다

print("저의 최종학력은 \(School.university.rawValue)입니다 ") //저의 최종학력은 university입니다 
//원시값이 없으면 항목 이름 그대로 원시값을 갖는다

```

정수형 원시값을 가지는 경우

``` swift
enum number : Int {
    case one = 5
    case two
    case three = 1
    case four = 2
    case five
}

print("\(number.two.rawValue)") //6
print("\(number.five.rawValue)") //3
//정수형 원시값에서 중간에 원시값이 없으면 이전 정수에서 +1을 한 값을 넣는다

```

자동으로 생성된 원시값이 다른 원시값과 같으면 Raw value for enum case is not unique 오류가 뜬다

만약 처음이 비어있으면 0부터 시작한다

&nbsp;

원시값을 통해서 열거형 변수 or 상수를 선언 할 수 있다

``` swift
var 초등학교 = School(rawValue: "초등학교")

var one = number(rawValue: 5)
```

&nbsp;

&nbsp;

### **연관값**

연관값은 각 항목 옆에 소괄호로 묶어서 표현한다

```swift
enum Food {
    case pizza(dough: String, topping: String)
    case pasta(taste: String)
    case chicken(withSauce: Bool)
    case rice
}

var dinner: Food = Food.pizza(dough: "thin", topping: "cheese")
dinner = .chicken(withSauce: true)
```

&nbsp;

열거형안에 열거형 연관값 이용

``` swift
enum PizzaDough {
    case original, thin
}

enum PizzaTopping {
    case cheese, bacon
}

enum PastaTaste {
    case cream, tomato
}


enum Food {
    case pizza(dough: PizzaDough, topping: PizzaTopping)
    case pasta(taste: PastaTaste)
    case chicken(withSauce: Bool)
    case rice

}
var dinner: Food = Food.pizza(dough: PizzaDough.original , topping: PizzaTopping.cheese)
dinner = .pasta(taste: PastaTaste.cream)
```

