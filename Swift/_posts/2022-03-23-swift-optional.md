---
layout: post
title: "Swift 옵셔널"
date:   2021-12-16 15:35:47 +0900
categories:
tags: 옵셔널
---

## **옵셔널** Optional

옵셔널은 nil을 사용할 수 있는 데이터 타입이다 nil을 사용 가능하다는 것은 그 **변수나 상수에 값이 있거나 없을 수도 있다는 것**이다 

**nil == NULL이고 스위프트에서는 nil로 표현한다**

nil은 일반적인 상수나 변수에 사용할 수 없다

``` swift
var name: String = "min"
name = nil //'nil' cannot be assigned to type 'String' 경고 출력
```

&nbsp;

옵셔널 변수 사용 변수의 데이터 타입 뒤에 물음표(?)를 붙인다

```swift
var name: String? = "min"
name = nil //정상 실행
```

옵셔널의 다른 표현으로 `var name: Optional<String>`로도 사용 가능하다

&nbsp;

&nbsp;

### **옵셔널을 사용하는 이유**

우리가 만든 함수에 전달되는 인자의 값이 잘못된 값인 경우 제대로 처리하지 못했을 경우 nil을 반환하여 표현한다 **반환 값이 nil인 경우 오류를 알릴 수 있다**

또 매개변수 타입을 옵셔널로 사용하면 매개변수 값이 없어도 된다는 것을 의미한다

&nbsp;

&nbsp;

## **옵셔널 추출**

### **강제 추출**

옵셔널 값을 간단히 추출 할 수 있지만 위험한 방법이다 옵셔널 값 뒤에 !를 붙이면 값을 강제로 추출하여 반환

만약 nil이라면 오류가 발생한다 강제추출을 사용하면 옵셔널의 사용 의미가 없어진다

```swift
var num: Int?

print(num) //nil 출력

var n = num! //nil을 강제 추출해서 n에 넣음 오류 발생
//Fatal error: Unexpectedly found nil while unwrapping an Optional value
print(n)
```

강제 추출 사용시 오류를 피하기 위한 if문 사용

```swift
var num: Int?
if num != nil { //nil이 아니라면 강제추출 사용
    print(num!)
} else { //nil인 경우
    print("num == nil")
} //num == nil

num = 23

if num != nil {
    print(num!)
} else {
    print("num == nil")
}//23 출력

```

&nbsp;

&nbsp;

### **옵셔널 바인딩**

위 if문으로 옵셔널 값을 강제추출 하는 방법은 다른 언어에서 null체크하는 방법과 비슷하고 !를 사용하는 방식이 좋은 방법이 아니다 그래서 스위프트에서는 옵셔널 바인딩을 제공한다

만약 옵셔널에 값이 있다면 옵셔널에서 추출한 값을 일정한 블록 안에서 사용할 수 있는 상수나 변수에 할당해서 옵셔널이 아닌 형태로 사용할 수 있게 한다

```swift
var num: Int? = 23 //옵셔널 값 설정

if let number = num { // number상수에 할당 가능한가?를 의미
    print("number == \(number)") //할당 가능하면 실행
} else {
    print("number == nil")
} //number == 23

if var number = num {
    number = nil //number은 변수라 값 변경 가능 하지만 number에는 Int타입이 들어가야함 오류
    print("number == \(number)") 
} else {
    print("number == nil")
}//오류
```

&nbsp;

위 코드에서 number를 둘다 사용 했지만 충돌이 나지 않았다 그 이유는 각각의 number는 임시 상수,변수라 한 블록에서만 작동한다

 number = nil 오류를 통해 옵셔널 바인딩을 통해서 만들어진 임시 상수나 변수의 데이터 타입은 원래 데이터 타입에서 옵셔널을 제거한 것으로 알 수 있다

&nbsp;

```swift
var myAge: Int? = 24
var myName: String? = nil

if let age = myAge,let name = myName {
    print("my name is \(name) \nI'm \(age) years old")
} else {
    print("nono")
}//nono

myName = "min"

if let age = myAge,let name = myName {
    print("my name is \(name) \nI'm \(age) years old")
} else {
    print("nono")
}//my name is min 
//I'm 24 years old
```

&nbsp;

옵셔널 바인딩을 여러개 사용하여 여러개의 옵셔널 값 추출

&nbsp;

&nbsp;

### **암시적 추출 옵셔널**

nil을 할당하고 싶지만 옵셔널 바인딩으로 매번 값을 추출하기 귀찮거나 nil 반환되어 런타임 오류가 발생하지 않을 확신이 있을때 nil을 할당 가능한 옵셔널이 아닌 변수나 상수가 필요하다

이때 사용가능한 것은 **암시적 추출 옵셔널**이다 옵셔널 표시는 데이터 타입 뒤에 ?를 표시했지만 암시적 추출 옵셔널은 데이터 타입 뒤에 !를 사용한다

```swift
var a: String! = "@@#"
print(a) //Optional("@@#")
//암시적 추출 옵셔널은 일반값처럼 사용 할 수 있지만 여전히 옵셔널이다
a = nil

if let name = a {
    print("my name is \(name)")
} else {
    print("nil")
} //nil

a.isEmpty //오류 출력 
```

&nbsp;

**암시적 추출 옵셔널에서 nil값을 할당 하고 있을때 접근을 시도하면 런타임 오류 발생**

암시적 추출 옵셔널을 사용하면 일반값들과 같이 사용 할 수 있다

&nbsp;

```swift
var a: Int! = 12 //암시적 추출 옵셔널 사용
print(a) //Optional(12) 여전히 옵셔널 값이 반환된다

var b: Int = 23
print(a+b) //35 옵셔널로 표시가 되었지만 암시적 추출 옵셔널이라 일반 Int와 연산 가능
```

&nbsp;

옵셔널 사용

```swift
var a: Int? = 12 //옵셔널 사용
print(a) //Optional(12)

var b: Int = 23
print(a+b) //Value of optional type 'Int?' must be unwrapped to a value of type 'Int'
```

옵셔널 값이라 Int와 연산이 안되고 오류 출력
