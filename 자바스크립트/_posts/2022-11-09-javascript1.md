---
layout: post
title: "자바스크립트 문법(1)"
date:   2022-11-09 16:35:47 +0900
categories:
tags: 
excerpt: "자바스크립트에서 변수의 종류는 4가지의 형태가 있다 문자형(string), 숫자형(number), 논리형(boolean), 빈(Null) 데이터가 있다"
---

### 변수

자바스크립트에서 변수의 종류는 4가지의 형태가 있다 문자형(string), 숫자형(number), 논리형(boolean), 빈(Null) 데이터가 있다

#### 변수 선언

`var 변수명; 또는 var 변수명 = 값 `으로 작성한다 변수명에는 한글 작성이 안되고 영어와 숫자 그리고 일부 특수문자(_,$)만 포함가능하다

만약 변수명을 단어+단어 형태로 지었으면 두번째 단어의 시작은 대문자이다 ex) goalScore Camel 표기법

``` javascript
<script>
	var num;
  num = 100;
	document.write(num);  //100
</script>
```

&nbsp;

&nbsp;

#### 변수에 저장 가능한 자료형

``` javascript
var s = '문자형 자료형';
var n = 231312; //숫자 자료형
var num = Number("2313"); //숫자 자료형
var b1 = Boolean("ww"); // true값
var b2 = true;
var b3 = 10 >= 100;
var nu; //undfined
var nu1 = null; //비어 있는 값
```

논리형에서는 변수의 값이 0,null,undfined,('')빈 문자를 제외하고는 true를 반환한다

`var m = Boolean(0); //false`    `var k = Boolean('asda'); //true`

&nbsp;

&nbsp;

#### 변수 선언시 주의 사항

1. 변수명은 첫글자로 $,_,영문만 올 수 있다
2. 변수명 첫 글자 다음도 영문자,$,_만 올 수 있다
3. 변수명으로 예약어는 사용할 수 없다(document,location,window)
4. 변수명은 대소문자를 구분해야 된다

&nbsp;

--------

### 연산자

#### 산술연산자

| 종류 | 기본형 |  설명  |
| :--: | :----: | :----: |
|  +   |  A+B   | 더하기 |
|  -   |  A-B   |  빼기  |
|  *   |  A*B   | 곱하기 |
|  /   |  A/B   | 나누기 |
|  %   |  A%B   | 나머지 |

&nbsp;

&nbsp;

#### 문자 결합 연산자

문자열을  + 를 통해서 문자열을 더하거나 문자를 더할 수 있다

&nbsp;

&nbsp;

#### 대입 연산자

=를 연산된 데이터를 변수에 저장할 때사용한다

|  종류  |   풀이    |
| :----: | :-------: |
|  A=B   |    A=B    |
| A += B | A = A + B |
| A *= B | A = A * B |
| A /= B | A = A / B |
| A %= B | A = A % B |

&nbsp;

&nbsp;

#### 증감 연산자

--A, ++B 1을 증가시키는데 먼저 연산전에 증가시킨다 피연산자가 1개만 필요한 연산이다

A--, B++은 연산후에 1을 증가시킨다

&nbsp;

&nbsp;

#### 비교 연산자

| 종류  |         설명          |                         비고                         |
| :---: | :-------------------: | :--------------------------------------------------: |
|  A>B  |    A가 B보다 크다     |                                                      |
|  A<B  |    A가 B보다 작다     |                                                      |
| A>=B  | A가 B보다 같거나 크다 |                                                      |
| A<=B  | A가 B보다 같거나 작다 |                                                      |
| A==B  |     A와 B는 같다      | 자료형은 상관없이 표기된 문자나 숫자가 일치하면 True |
| A!=B  |    A와 B는 다르다     |  자료형은 상관없이 표기된 문자나 숫자가 다르면 True  |
| A===B |     A와 B는 같다      |     자료형과 표기된 문자나 숫자가 일치하면 True      |
| A!==B |    A와 B는 다르다     |      자료형과 표기된 문자나 숫자가 다르면 True       |

&nbsp;

&nbsp;

#### 논리 연산자

| 종류 |                             설명                             |
| :--: | :----------------------------------------------------------: |
| \|\| |       or 연산으로 피연산자중 하나라도 True면 True반환        |
|  &&  |        and 연산으로 피연산자 모두 True이어야 True반환        |
|  !   | not연산자로 부르면 단항연산자이다 피연산자 값을 반대로 출력한다 |

&nbsp;

&nbsp;

#### 연산자 우선 순위

1. ()
2. 단항 연산자(--,++,!)
3. 산술 연산자(*,/,%,+,-)
4. 비교 연산자
5. 논리 연산자
6. 대입 연산자



&nbsp;

&nbsp;

#### 삼항 조건 연산자

조건 ? ans1: ans2 

조건이 true이라면 ans1 반환 false라면 ans2를 반환한다





