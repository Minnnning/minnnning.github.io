---
layout: post
title: "Swift 구조체, 클래스"
date:   2021-12-17 15:35:47 +0900
categories:
tags: 구조체 클래스
excerpt_image: "https://user-images.githubusercontent.com/80758613/160068894-505eec9f-0eda-42ae-92cc-a9ecd253bfdc.jpeg"
excerpt: "데이터를 용도에 맞게 묶어서 표현하고자 할 때 유용하다 구조체 내부에 상수나 변수 또는 함수를 선언하고 이것을 인스턴스를 만들어서 사용할 수 있다 새로운 데이터 타입을 만드는 것과 비슷하다"
---

#### **구조체**

데이터를 용도에 맞게 묶어서 표현하고자 할 때 유용하다 구조체 내부에 상수나 변수 또는 함수를 선언하고 이것을 인스턴스를 만들어서 사용할 수 있다 새로운 데이터 타입을 만드는 것과 비슷하다

**struct** 키워드 사용

```swift
// 선언방법
struct 구조체이름 {
    프로퍼티나 메서드들
}
```

&nbsp;

구조체 정의와 인스턴스 생성 및 초기화

```swift
struct BasicInformation {
    var name: String
    var age: Int
}//구조체 정의

var info1: BasicInformation = BasicInformation(name: "min", age: 24)//초기화
info1.age = 45 //.으로 프로퍼티 접근 가능
print(info1) //BasicInformation(name: "min", age: 45)

let info2: BasicInformation = BasicInformation(name: "young", age: 24)
info2.name = "min" //오류 info2 인스턴스는 상수로 선언되어 변경이 불가능하다
```

###### 멤버와이즈 이니셜라이저: 프로퍼티 이름으로 매개변수를 갖는 이니셜라이저, 사용자정의 이니셜 라이저를 구현하지 않아야 함

프로퍼티 접근은 마침표(.)를 사용해 접근 가능하다 인스턴스가 변수인 경우만 가능

&nbsp;

&nbsp;

****

### **클래스**

클래스 또한 데이터를 묶어서 표현할 때 유용하다 또한 클래스 내에 프로퍼티와 메서드들이 있다

스위프트의 클래스는 부모 클래스가 없더라도 상속 없이 단독 정의 가능

&nbsp;

**class** 키워드 사용

```sw
//클래스 선언
class 클래스 이름: 부모클래스 이름 { //상속이 있는 경우 :과 부모 클래스 이름 
    프로퍼티와 메서드들
}
```

&nbsp;

클래스 정의와 인스턴스 생성과 초기화

```swift
class Person {
    var height: Float = 0.0
    var weight: Float = 0.0
}

var min: Person = Person() //초기화 기본값이 있어서 따로 초깃값을 넣어주지 않음
min.height = 123.22

let young: Person = Person()
young.weight = 231.2 //상수로 선언 되었지만 값변경 가능

```

&nbsp;

클래스도 마찬가지로 마침표(.)로 프로퍼티 접근 가능하다

구조체의 인스턴스가 상수(let)로 선언되었으면 내부 프로퍼티 값 변경이 불가능했다 하지만 위 코드의 마지막을 보면 클래스의 인스턴스가 상수(let)로 선언되어도 내부 프로퍼티를 변경 가능하다 그 이유는

<center><strong>구조체는 값 타입, 클래스는 참조 타입이기 때문</strong></center>

&nbsp;

&nbsp;

### **클래스 인스턴스의 소멸**

클래스는 참조 타입이므로 더 이상 필요가 없으면 메모리에서 해제된다 이 과정을 소멸이라고 하는데 소멸되기 직전에 **deinit 메서드(디이니셜라이저)**가 호출된다

deinit메서드는 클래스당 하나만 구현, 매개변수와 반환값을 가질 수 없다

클래스 내부에 deinit 메서드는 인스턴스가 메모리에서 해제되기 직전에 처리할 코드를 넣어준다 예를 들어 소멸 전 데이터를 저장 or 다른 객체(인스턴스)에 인스턴스의 소멸을 알릴 때

&nbsp;

```swift
class Person {
    var height: Float = 0.0
    var weight: Float = 0.0
    
    deinit {
        print("Person의 인스턴스가 소멸됩니다")
    }
}

var min: Person? = Person()
min = nil //Person의 인스턴스가 소멸됩니다

```

&nbsp;

&nbsp;

### 구조체와 클래스 차이

위에서 보면 구조체와 클래스는 비슷한 점이 많아 보인다

&nbsp;

#### **공통점**

● 값을 저장하기 위해 프로퍼티를 정의할 수 있다

● 기능 실행을 위해 메서드를 정의할 수 있다

● 서브스크립트 문법을 통해 구조체 또는 클래스가 갖는 값(프로퍼티)에 접근하도록 서브스크립트 정의 가능

● 초기화될 때의 상태를 지정하기 위해 이니셜라이저를 정의할 수 있다

● 초기 구현과 더불어 새로운 기능 추가를 위해 익스텐션을 이용해 추가 확장 가능

● 특정 기능을 실현하기 위래 특정 프로토콜을 준수할 수 있다

&nbsp;

#### **차이점**

● **구조체는 값 타입, 클래스는 참조 타입이다**

● 구조체는 상속이 불가능하다

● 타입 캐스팅은 클래스의 인스턴스에만 허용

● 디이니셜라이저는 클래스의 인스턴스에만 활용 가능

● 참조 횟수의 계산은 클래스의 인스턴스에만 적용된다

&nbsp;

&nbsp;

### **값 타입과 참조 타입**

값 타입과 참조 타입의 차이점은 무엇이 전달되는가이다 **값 타입은 전달될 값이 복사**되어 전달되지만 **참조(주소) 타입은 주소가 전달**된다 주소전달은 다른 언어의 포인터 개념과 유사하다

&nbsp;

#### 값 타입의 특징

```swift
struct BasicInformation { //구조체 선언
    let name: String
    var age: Int
}

var minInfo: BasicInformation = BasicInformation(name: "min", age: 24) //이니셜라이징

var friendInfo = minInfo //값을 복사하여 다른 변수에 할당한다 값 타입

print("min's age is \(minInfo.age)")//min's age is 24
print("friend's age is \(friendInfo.age)") //friend's age is 24 복사만 한거라 똑 같이 나옴

friendInfo.age = 12 //friend인스턴스의 프로퍼티 값 변경
print("min's age is \(minInfo.age)") //min's age is 24 변경이 없음
print("friend's age is \(friendInfo.age)") //friend's age is 12 위에서 값을 변경해서 바뀜
```



값 타입은 값이 새로운 변수, 다른곳에 저장되는 느낌

&nbsp;

<center>
<img src="https://user-images.githubusercontent.com/80758613/160068894-505eec9f-0eda-42ae-92cc-a9ecd253bfdc.jpeg" style="zoom:20%;">
</center>

&nbsp;

&nbsp;&nbsp;

#### 참조 타입 특징

```swift
class Person { //클래스 선언
    var height: Int = 0
    var weight: Int = 0
}

var min: Person = Person() //이니셜라이징
var friend = min // 참조타입으로 주소를 복사한다 값복사가 아님

min.height = 177 //min 인스턴스 내부 프로퍼티 변경

print("min's height \(min.height)")//min's height 177
print("friend's height \(friend.height)")//friend's height 177 바꾼적이 없는데 바뀜
```

&nbsp;

참조타입에서는 값이 연동된다라고 생각하면 쉽다 같은 주소를 가진 변수나 상수는 같은 주소를 가르키고 있어 값이 같이 바뀐다

<center>
<img src="https://user-images.githubusercontent.com/80758613/160069522-265a8f9a-afcb-45ad-b761-c00a5724b2e7.jpg" style="zoom:20%;">
</center>


클래스의 인스턴스는 **식별연산자**를 통해서 같은 참조인지 확인 가능하다

&nbsp;

```swift
class Person { //클래스 선언
    var height: Int = 0
    var weight: Int = 0
}
var tom: Person = Person()
var timmy = tom
var jenny: Person = Person()

print(tom === timmy) //true
print(tom === jenny) //false
print(tom !== jenny) //true
```

&nbsp;

&nbsp;

### **구조체와 클래스 선택해서 사용하기**

가이드라인 중 해당하는 것이 하나 이상이면 구조체 사용 권장

● 연관된 간단한 값의 집합을 캡슐화하는 것만이 목적일 때

● 캡슐화한 값을 참조하는 것보다 복사하는 것이 합당할 때

● 구조체에 저장된 프로퍼티가 값 타입이며 참조하는 것보다 복사하는 것이 합당할 때

● 다흔 타입으로부터 상속 받거나 자신을 상속할 필요가 없을 때
