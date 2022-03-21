---
layout: post
title: "Swift 데이터 타입 (1)"
date:   2021-11-22 15:35:47 +0900
categories: Swift
tags: Int Bool Float Double Character String
---

### **Int**

Int형은 정수를 표현하며 +,-를 포함한다 UInt는 0을 포함하지 않는 양의 정수를 표현한다

``` swift
import UIKit

let minInt : Int = Int.min
let maxInt : Int = Int.max

let minUint : UInt = UInt.min
let maxUint : UInt = UInt.max

print("Int의 최솟값은 \(minInt) 최대값은 \(maxInt)")
//Int의 최솟값은 -9223372036854775808 최대값은 9223372036854775807
print("UInt의 최솟값은 \(minUint) 최대값은 \(maxUint)")
//UInt의 최솟값은 0 최대값은 18446744073709551615

```

Int형의 음수를 부분 대신 그 부분을 양수로 더 많이 표현 가능한 것이 UInt이다

Int와 UInt는 각각 8비트 16비트 32비트 64비트가 있는데 시스템의 아키텍처에 따라 달라진다 64비트 형은 Int64가 기본이 된다

2^64 =  18446744073709551616이므로 UInt 값과 비교하면 표현할 수 있는 숫자가 같으므로  이 컴퓨터는 64비트 운영체제이다

&nbsp;

**swift에서는 데이터 타입이 다르면 연산이 되지 않는다**

```swift
import UIKit
var a : Int = 10
var b : Int32 = 12

print(a+b)
//Binary operator '+' cannot be applied to operands of type 'Int' and 'Int32' 오류 출력
print(a+Int(b)) //타입 변경후 22 정상 출력

```

10진수 외의 표현

\- 2진수 : 접두어 0b

\- 8진수 : 접두어 0o

\- 16진수 : 접두어 0x

&nbsp;

&nbsp;

### **Bool**

true 와 false 2개의 값을 가진다

```swift
import UIKit

var boolean : Bool = true
boolean.toggle() // 값 반전
print(boolean) // false 출력
```

&nbsp;

&nbsp;

### **Float,** **Double**

부동 소수 타입 소수점을 표현할 수 있다

**표현 범위 Float  < Double**

 사용되는 수의 크기를 잘 모를 경우 Double를 사용해서 효율은 떨어지지만 오류를 줄일 수 있을 것이다

&nbsp;

&nbsp;

### **Chatacter**

단 하나의 문자 

```swift
import UIKit

var a : Character = "q"
var b : Character = "뷀"
var c : Character = "ㅂ"
var d : Character = "@"

print(a,b,c,d) // q 뷀 ㅂ @
```

 한글의 경우 하나의 자음 또는 모음뿐만 아니라 한 글자까지는 Character에 저장 가능하다

&nbsp;

&nbsp;

### **String**

문자열이다 Character가 하나의 문자열로 합쳐진것으로 생각하면 쉽다



String의 다양한 기능

```swift
var name : String = String() // 빈문자열 생성

name.append("minnnng 입니다") //문자열 이어서 넣기

name = "안녕하세요 " + name //+문자로 문자열 추가및 연결 가능
print(name) // 안녕하세요 minnnning입니다

print(name.count) //문자열에서 문자 개수

print(name.isEmpty) //빈문자열인지 확인 맞으면 true 아니면 false
//연산자로 문자열 비교하기
var isSame : Bool = false

isSame = "hello" == "Hello"
print(isSame) //false
isSame = "hello" == "hello"
print(isSame) //true

// 메서드로 접두어 접미어 확인하기
var name : String = "안녕하세요 밍 입니다"

print(name.hasPrefix("안녕")) //접두어 확인 true

print(name.hasSuffix("다")) // 접미어 확인 true

//메서드로 대소문자 변경하기
var name: String = "minnnning"

print(name.uppercased()) //MINNNNING

print(name.lowercased()) //minnnning

```

&nbsp;

&nbsp;

### **특수문자** 

특수문자는 백슬래와 문자 조합으로 활용한다

```
\n   //줄바꿈 문자
\\   //문자열내에서 백슬래쉬 표현
\"   //문자열내에서 큰따음표 표현
\t   //탭키를 눌렀을때의 효과 띄어쓰기 4개
\O   //문자열이 끝남을 알리는 null 문자
```

문자열 내에서 백슬래쉬로 특수문자를 표현 가능한다 하지만 이기능을 사용하지 않을려면 앞뒤에 # 사용

```swift
print(#"\\백슬래쉬 \t탭키"#) // \\백슬래쉬 \t탭키

//그안에서 문자열 보간법만 사용하고 싶으면 \#(변수 or 상수)로 사용한다 
```

&nbsp;

&nbsp;

### **Any, AnyObject, nil**

Any는 스위프트의 모든 데이터 타입을 사용 가능하다 

AnyObject는 클래스의 이스턴스만 할당가능하다 Any보다 제한적

nil은 데이터 타입이 아니라 없음을 나타내는 키워드이다 변수 또는 상수의 값이 nil이면 오류가 발생한다