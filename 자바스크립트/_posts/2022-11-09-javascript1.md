---
layout: post
title: "자바스크립트 문법(1)"
date:   2022-11-09 16:35:47 +0900
categories:
tags: 
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

