---
layout: post
title: "Swift 데이터 타입 (2)"
date:   2021-11-23 15:30:47 +0900
categories:
tags: 타입추론 타입별칭 Tuple Array Dictionary Set
---

### **타입 추론**

스위프트에서는 변수나 상수를 선언할 때 타입을 명시하지 않아도 데이터로 타입을 자동으로 추론시켜준다

``` swift
var name = "minnnning" print(type(of: name)) //String
```



위에 와 같이 알아서 데이터 타입을 결정해 주지만 배열과 세트 이런 부분에서 표현이 같지만 데이터 타입이 다르므로 선언을 할 때 데이터 타입을 명시해 줘야 한다 또한 데이터 타입을 명시하면 일일이 확인하지 않아도 돼서 오류검사가 편해질 수 있다

&nbsp;

### **타입 별칭**

원래있던 타입이나 사용자가 만든 데이터 타입을 다른 이름(별칭)을 부여하는 것이다

``` swift
typealias MyInt = Int

var num : MyInt = 200 // Int형
```

&nbsp;

&nbsp;

### **Tuple**

프로그래머가 만드는 데이터 타입 **지정된 데이터 타입의 묶음**이다

``` swift
var person : (String, Int, Double) = ("min", 24, 177.4)

print("이름 : \(person.0)") // min
```

person.0으로 첫번째요소를 꺼낼 수 있다 편하게 사용하기 위해 번호 대신 이름 붙히기도 가능하다

``` swift
var person : (name: String, age: Int, height: Double) = ("min", 24, 177.4)

print("키 : \(person.2)") // 177.4
```

**튜플을 타입 별칭으로 지정  -> 튜플을 간편하게  사용 가능하다**

``` swift
typealias personTuple = (name: String, age: Int, height: Double)

var a : personTuple = ("a", 244, 23.5)
var b : personTuple = ("b", 31, 190.2)

print(a) //(name: "a", age: 244, height: 23.5)
print(b.age) //31
```

&nbsp;

&nbsp;

<center>**여기부터 컬랙션형 여러 데이터를 묶어서 저장 관리**
</center>

&nbsp;

&nbsp;

### **Array**

같은 타입의 데이터를 일렬로 나열하고 ******순서**대로 저장한 데이터 타입이다 중복을 허용

``` swift
//배열 표현
var names : Array<String> = ["dd", "aa", "ss"]
var names : [String] =  ["dd", "aa", "ss"] // 위에 것과 같은 표현이다

var anyArray : [Any] = [Any]()
var anyArray : [Any] = Array<Any>()
var anyArray : [Any] = [] //데이터 타입이 명시되면 []로 빈배열 생성 가능
//위 3개표현은 같은 표현이다
```



배열은 각요소에 인덱스로 접근하고 인덱스는 0부터 시작한다 처음과 끝은 first와 last 프로퍼티로 가져 올 수있다

추가적인 메서드들

``` swift
var names : [String] = ["min"]
// 배열 요소 추가
names.append("john") //하나 추가
names.append(contentsOf: ["yun","kang"]) //여러개 추가
print(names) //["min", "john", "yun", "kang"]

names.insert("king", at: 2) // 지정된 위치에 추가
print(names) //["min", "john", "king", "yun", "kang"]

//해당 요소의 인덱스 출력
print(names.firstIndex(of: "min")) //Optional(0)
print(names.lastIndex(of: "john")) //Optional(1)

//처음과 마지막 요소 출력
print(names.first) //Optional("min")
print(names.last) //Optional("kang")

//지우기 remove는 지운후 값이 반환된다
names.remove(at: 3) //인덱스 3번 제거
print(names) //["min", "john", "king", "kang"]

names.removeLast() //마지막 요소 제거
print(names) //["min", "john", "king"]
```



###### 위에 print(names.first)의 출력을 보면 Optional("min")이 출력된다 옵셔널이 붙은 이유는 추출할때는 처음에 선언한 문자열 배열이 아니라 문자열만 추출하기 때문에 자동으로 타입추론이 되고 그 결과 데이터 타입이 String?으로 되어서 출력값이 옵셔널이 된것이라 추측해 본다 

&nbsp;

&nbsp;

### **Dictionary**

키와 값이 쌍으로 구성되는 타입이다 딕셔너리에서 값은 중복이어도 상관 없지만 키값은 중복이 될 수 없다

순서 상관 x

**키 값은 딕셔너리에서 식별자이다 따라서 중복x**

``` swift
var dictionary1 : Dictionary<String, Int> = Dictionary<String, Int>()

var dictionary2 : [String: Int] = [String: Int]()

//타입 별칭이용
typealias diction = [String: Int]
var dictionary3 : diction = diction()

//위 표현 3가지는 모두 같은 빈딕셔너리를 생성한다
```



딕셔너리의 사용

``` swift
var dictionary : Dictionary<String, Int> = ["one" : 11,"two" : 23, "three" : 33]

print(dictionary["one"]) // Optional(11)

dictionary["four"] = 234 //딕셔너리 키 + 값 추가

//제거후 값 반환
print(dictionary.removeValue(forKey: "three")) //Optional(33)
print(dictionary) //["two": 23, "four": 234, "one": 11]
```

&nbsp;

&nbsp;

### **Set**

같은 타입의 데이터를 순서 없이 하나의 묶음으로 저장 **중복 x**

순서가 중요하지 않고 요소가 유일한 값이어야 하는 경우 사용한다

``` swift
// 두표현은 빈세트를 선언
var names1 : Set<String> = Set<String>()
var names2 : Set<String> = []

names1 = ["one", "two", "two", "three"] //요소 중복 불가능 따라서 중복 요소 하나 사라짐
print(names1.count) //3
```

세트는 자신의 값이 유일함을 보장하므로 집합관계를 표현 할 때 좋다

``` swift
// 두표현은 빈세트를 선언
var set1 : Set<Int> = [2,3,41,5,12,45]
var set2 : Set<Int> = [2,3,4,5,11,23,44]

//교집합
print(set1.intersection(set2)) //[2, 5, 3]

//여집합의 합(두세트의 교집합의 여집합)
print(set1.symmetricDifference(set2)) //[11, 12, 4, 45, 23, 44, 41]

//합집합
print(set1.union(set2)) //[3, 11, 5, 12, 4, 2, 45, 23, 44, 41]

//차집합
print(set1.subtracting(set2)) //[12, 45, 41]

//sorted() 메서드를 통해 정렬
print(set1.union(set2).sorted()) //[2, 3, 4, 5, 11, 12, 23, 41, 44, 45]
```

