---
layout: post
title: "Swift 함수"
date:   2021-12-14 15:35:47 +0900
categories: Swift
tags: 함수
---

### **함수와 메서드**

함수와 메서드는 기본적으로 같다 상황이나 용도에 따라 다른 용어로 부른다 

메서드: 구조체, 클래스, 열거형 등 특정 타입에 연관되어 사용하는 함수

함수: 모듈 전체에서 전역적으로 사용 가능한 함수

&nbsp;

함수는 조건문이나 반복 문과 달리 **소괄호()를 생략 불가능**

재정의(오버라이드), 중복 정의(오버로드)를 모두 지원

따라서 매개변수 타입이 다르면 같은 이름의 함수를 여러 개 만들 수 있고 매개변수의 개수가 달라도 같은 이름의 함수를 만들 수 있다 -> **매개변수가 다르면 다른 함수다**

&nbsp;

&nbsp;

### **함수의 정의와 호출**

함수의 이름, 매개변수, 반환 타입 등을 사용하여 함수를 정의한다

```swift
func 함수이름 (매개변수...) -> 반환 타입 {
    실행구문
    return 반환 값
}
```

&nbsp;

*return 생략 조건*

*만약 결괏값이 반환 타입과 같다면 return 없이 그 결괏값이 함수의 반환값이 된다*

&nbsp;

&nbsp;

### **매개변수**

매개변수 없음 공백으로 표현

```swift
func function () -> String {
    실행구문
}
```

매개변수 여러 개

```swift
func function (a: Int, b: Int, c: String) -> String {
    return "Hello to day is \(a)M \(b)d \(c). "
}

print(function(a:12, b:13, c:"Monday")) //Hello to day is 12M 13d Monday.

```

&nbsp;

&nbsp;

### **매개변수 이름과 전달인자 레이블**

바로 위 function 함수에서 매개변수 이름과 전달인자 레이블은 모두 a, b, c이다 

매개변수 이름만 작성하면 **매개변수 이름 == 전달인자 레이블** 이렇게 된다

구분하려면 매개변수 이름 앞에 전달인자 레이블을 작성한다

```swift
func function1 (from name: String, to reciver: String) -> String {
    name + " " + "hello" + " " + reciver
}
print(function1(from:"min", to:"jony")) //min hello jony

```

&nbsp;

function1에서 매개변수의 이름 name, reciver이고 전달인자 레이블 from, to이다

**매개변수 이름은 함수 내부에서만 사용하고 전달인자 레이블은 함수 외부에서 사용**한다

두 개를 같은 이름으로 사용해도 되지만 다른 이름으로 사용하는 것은 **함수 외부에서 매개변수의 역할을 더 명확히 하기** 위해서이

다

&nbsp;

만약 전달인자 레이블은 사용하고 싶지 않으면 와일드카드 식별자(_) 사용

```swift
func function2 (_ a: Int, _ b: Int) -> Int {
    return a + b
}

print(function2(1,2)) //3
```

&nbsp;

*전달인자의 이름만 바뀌면 함수의 이름이 바뀌므로 함수 중복 정의로 동작할 수 있다*

&nbsp;

&nbsp;

### **가변 매개변수**

가변 매개변수는 매개변수의 개수가 정해지지 않을 때 사용할 수 있다

입력될 값의 개수가 0개 이상인 경우 사용 가능하다

String...으로 표현 데이터 타입 뒤에 ... 을 붙인다

```swift
func function (friends names: String...) -> String {
    var friendsNames: String = ""

    for friend in names {
        friendsNames += "Hello \(friend)! "
    }

    return friendsNames
}

print(function(friends: "min","nick","jenny"))//Hello min! Hello nick! Hello jenny! 

```

&nbsp;

**가변 매개변수는 함수마다 하나만 가질 수 있다**

&nbsp;

&nbsp;

### **입출력 매개변수**

함수의 전달인자로 값을 전달할 때 보통 값을 복사해서 전달한다 만약 값이 아닌 참조로 전달하려면 입출력 매개변수를 사용한다 **값을 전달하기보다는 그 값의 주소를 전달하는 느낌** 포인터 비슷

``` swift
var numbers: [Int] = [1,2,3]

func noReference(_ arr: [Int]) {
    var copyArr: [Int] = arr
    copyArr[1] = 1 //복사본을 변경한것
}
func reference(_ arr:inout [Int]) {
    arr[1] = 1 //참조를 통해서 배열의 [1]인덱스 값을 1로 바꿈
}

noReference(numbers)
print(number[1]) //2 원래의 배열은 바뀌지 않고 복사본만 바뀜

reference(&numbers)
print(number[1]) //1 //배열의 값이 바귀어 출력된다

```

&nbsp;

참조를 이용하려면 매개변수 데이터 타입 앞에 **inout을 붙이고** 매개변수에 값을 넣을 때 **&**를 이용해야 참조 가능 

&nbsp;

&nbsp;

### **반환이 없는 함수**

없음을 표시 Void로 표기하거나 아예 반환 타입 표현을 생략한다

```swift
func function1() {
    print("Hello")
}

func function2(_ name: String) -> Void {
    print("my name is \(name)!")
}

function1
function2("kkka") //my name is kkka!
```

&nbsp;

&nbsp;

### **데이터 타입으로서의 함수**

함수는 하나의 데이터 타입으로 사용 가능하다 

매개변수 타입과 반환 타입으로 구성된 하나의 타입으로 정의할 수 있다

```swift
(매개변수 타입 나열) -> 반환 타입

func say(name: String) -> String {
    ...
}
```

위에 say 함수의 타입은 **(String) -> String**이 된다

만약 매개변수와 반환값이 모두 없으면

\- (Void) -> Void

\- () -> Void

\- () -> ()

&nbsp;

&nbsp;

### **함수 타입 사용**

``` swift
typealias CalculateTwoInts = (Int,Int) -> Int

func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}

func multiplyTwoInts(_ a: Int, _ b: Int) -> Int {
    return a * b
}

var mathFunction: CalculateTwoInts = addTwoInts //데이터 타입이 같다
print(mathFunction(2,5)) //7

mathFunction = multiplyTwoInts //데이터 타입이 같다
print(mathFunction(2,5)) //10
```

&nbsp;

위에서 CalculateTwoInts라는 데이터 타입을 만들고 이 데이터 타입은 함수 addTwoInts과 multiplyTwoInts 데이터 타입과 같아서 mathFunction = multiplyTwoInts 이 표현이 가능하다

mathFunction = multiplyTwoInts을 사용하면 multiplyTwoInts의 함수를 mathFunction 이름으로 사용 가능하다고 생각하면 된다

&nbsp;

&nbsp;

### **전달인자로 함수를 전달받는 함수**

```swift
typealias CalculateTwoInts = (Int,Int) -> Int

func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}

func result(_ mathFunction: CalculateTwoInts, _ a: Int, _ b: Int) {
    print("Result: \(mathFunction(a,b)) ")
} // 매개변수 데이터 타입 CalculateTwoInts로 함수를 받는다

result(addTwoInts,3,5) //Result: 8 
```



함수 addTwoInts를 전달인자로 받아 이용한 코드

&nbsp;

&nbsp;

### **중첩 함수**

함수를 중첩해서 함수안에 함수를 구현하는 방법이다 중첩 함수는 함수의 사용 범위를 제한할 수 있다

함수안에 함수

```swift
typealias MoveFunc = (Int) -> Int

func goRight(_ currentPosition: Int) -> Int {
    print("go right")
    return currentPosition + 1
}

func goLeft(_ currentPosition: Int) -> Int {
    print("go left")
    return currentPosition - 1
}

func functionForMove(_ shouldGoLeft: Bool) -> MoveFunc {
    return shouldGoLeft ? goLeft : goRight
}

var position: Int = 3

let moveToZero: MoveFunc = functionForMove(position > 0)
print("go to zero")

while position != 0 {
    print("\(position)")
    position = moveToZero(position)
}
print("0")

//go to zero
//3
//go left
//2
//go left
//1
//go left
//0
```

&nbsp;

중복 함수 사용

```swift
typealias MoveFunc = (Int) -> Int

func functionForMove(_ shouldGoLeft: Bool) -> MoveFunc {
    
    func goRight(_ currentPosition: Int) -> Int {
        print("go right")
        return currentPosition + 1
    }
    
    func goLeft(_ currentPosition: Int) -> Int {
        print("go left")
        return currentPosition - 1
    }
    
    return shouldGoLeft ? goLeft : goRight
}

var position: Int = -4

let moveToZero: MoveFunc = functionForMove(position > 0)
print("go to zero")

while position != 0 {
    print("\(position)")
    position = moveToZero(position)
}
print("0")

//go to zero
//-4
//go right
//-3
//go right
//-2
//go right
//-1
//go right
//0

```

위 두개의 방법은 별차이 없지만 코드가 복잡해지면 전역함수가 많은것보다는 두번째 방법처럼 **중복함수를 사용하여 함수의 사용 범위를 조금 더 명확히 표현 가능하다**

&nbsp;

&nbsp;

### **반환값을 무시할 수 있는 함수**

함수를 작성하다 보면 함수 내부에서 필요한 작업이 끝나 반환값이 필요 없는 경우가 있다 이경우 그냥 실행하면 경고를 내보내지만 **@discardableResult** 선언 속성을 사용하면 된다

```swift
func addFunction(_ a: Int, _ b: Int) -> Int { //리턴값 미사용 함수
    print("\(a*b)")
    return a+b
}//경고가 나올 수도 있다 하지만 실행해보니 안나옴
addFunction(2,3) //6

@discardableResult func say(_ name: String) -> String {
    print(name)
    return name
}
say("mine") //mine
```

