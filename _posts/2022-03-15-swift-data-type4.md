---
layout: post
title: "Swift 데이터 타입 (4) 열거형"
date:   2022-03-15 15:35:47 +0900
categories: Swift
tags: 열거형
---

### **항목순회**

열거형의 모든 항목을 알아야 할 때 항목순회를 사용한다 열거형에서 CaseIterable 프로토콜 사용 그러면 allCases 프로퍼티를 통해서 모든 케이스의 컬렉션을  생성해 준다

&nbsp;

``` swift
enum School : String, CaseIterable {
    case primary = "유치원"
    case elementary = "초등학교"
    case middle = "중학교"
    case high = "고등학교"
    case university = "대학교"
}

let allCase : [School] = School.allCases
print(allCase)
//[__lldb_expr_4.School.primary, __lldb_expr_4.School.elementary, __lldb_expr_4.School.middle, __lldb_expr_4.School.high, __lldb_expr_4.School.university]

```



열거형에서 CaseIterable 프로토콜을 채택해 주는 것만으로도 allCases 프로퍼티를 사용할 수 있지만 다른 플랫폼이거나 열거형의 케이스가 연관 값을 가지는 경우는 CaseIterable 프로토콜을 채택하더라도 바로 allCases 프로퍼티를 사용할 수 없다 

이런 경우는 직접 allCases 프로퍼티를 구현해 줘야 한다

&nbsp;

&nbsp;

### **순환 열거형**

열거형 항목의 연관값이 영거형 자신의 값이고자 할때 사용한다

순환 열거형 명시는 **indirect** 키워드를 사용한다

``` swift
indirect enum ArithmeticExpression {
    case number(Int)
    case addition(ArithmeticExpression,ArithmeticExpression)
    case multiplication(ArithmeticExpression,ArithmeticExpression)
}

let five = ArithmeticExpression.number(5)
let four = ArithmeticExpression.number(4)

let sum = ArithmeticExpression.addition(five,four)
let multiplication = ArithmeticExpression.multiplication(five,four)

func evaluate(_ expression: ArithmeticExpression) -> Int {
    switch expression {
    case .number(let value):
        return value
    case .addition(let left, let right):
        return evaluate(left) + evaluate(right)
    case .multiplication(let left, let right):
        return evaluate(left) * evaluate(right)
    }
}

let a : Int = evaluate(sum)
print(a) // 9

let b : Int = evaluate(multiplication)
print(b) // 20
```



순환 열거형은 순환 알고리즘에서 자주 사용된다는데 재귀와 비슷한 느낌 같다

&nbsp;

&nbsp;

### **비교 가능한 열거형**

Comparable 프로토콜을 사용하여 각 케이스를 비교할 수 있다 

**원시값이 있으면 안되고 연관값이 있으면 Comparable 프로토콜을 준수 해야한다**

**앞에 있는 케이스가 더 작은 값이 된다**

``` swift
enum dddda : Comparable{
    case one
    case two
    case three
    case four
}

var a = dddda.one
var b = dddda.four

if a >= b {
    print("a는 앞에 케이스")
} else {
    print("b는 뒤에 케이스")
} //b는 뒤에 케이스
```

