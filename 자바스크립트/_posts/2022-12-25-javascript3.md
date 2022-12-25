---
layout: post
title: "자바스크립트 객체 -내장 객체"
date:   2022-12-25 14:35:47 +0900
categories:
tags: 
---

자바 스크립트는 객체(object) 기반 언어이다 객체에는 기능(method)과 속성(property)이 있다

&nbsp;

객체.메서드();

객체.속성; 또는 객체.속성=값; 을 통해서 객체의 속성값을 바꿀 수 있다

 &nbsp;

### 객체 종류

- 내장 객체

  자바스크립트 엔진에 내장 되어있다 문자, 날짜, 배열, 수학등 객체가 있다

- 브라우저 객체 모델

  BOM은 브라우저에 계층 구조로 내장되어 있는 객체 window, screen, location, history, navigator등 객체가 있다

- 문서 객체 모델

  DOM은 HTML문서 구조를 말한다 

&nbsp;

&nbsp;

### 내장 객체 생성하기

내장 객체는 `var 변수이름 = new Object();`로 생성 되고 객체를 생성할 때 new라는 키워드를 사용한다 여기서 Object는 기본 객체 생성 함수로 다른 객체 생성함수를 이용할 수 있다

``` javascript
<script>
  var tv = new Object();
	tv.color = "white"
	tv.price = 30000 //속성
</script>
```

&nbsp;

&nbsp;

#### 날짜 정보 객체

날짜 관련 객체는 Date 함수를 이용한다

`var 변수 = new Date();`날짜 객체 생성

###### 날짜 관련 메소드 및 속성

| 날짜 정보 가져옴 (get) |                                                   | 날짜 정보 수정(set) |                                                      |
| :--------------------: | :-----------------------------------------------: | :-----------------: | :--------------------------------------------------: |
|     getFullYear()      |                 연도 정보 가져옴                  |    setFullYear()    |                   연도 정보만 수정                   |
|       getMonth()       |                 월 정보를 가져옴                  |     setMonth()      |                     월 정보 수정                     |
|       getDate()        |                  일 정보 가져옴                   |      setDate()      |                     일 정보 수정                     |
|        getDay()        |                요일 정보(0일~6토)                 |                     |                                                      |
|       getHours()       |                 시 정보를 가져옴                  |     setHours()      |                     시 정보 수정                     |
|      getMinutes()      |                 분 정보를 가져옴                  |    setMinutes()     |                     분 정보 수정                     |
|      getSeconds()      |                 초 정보를 가져옴                  |    setSeconds()     |                     초 정보 수정                     |
|   getMilliseconds()    |                    밀리초 정보                    |  setMilliseconds()  |                   밀리초 정보 수정                   |
|       getTime()        |       1970년 부터 경과된 <br />시간 밀리초        |      setTime()      |      1970년 부터 경과된 <br />시간 밀리초 수정       |
|     toGMTString()      | GMT 표준 표기 방식으로<br /> 문자형 데이터로 반환 |  toLocaleString()   | 운영 시스템 표기 방식으로 <br />문자형 데이터로 변환 |

``` javascript
<script>
	var t = new Date("2022/12/25");
	month = t.getMonth() + 1;
	date = t.getDate();
	document.write(month + "월" + date + "일" +"<br>");
	t.setDate(1);
	date = t.getDate();
	document.write(month + "월" + date + "일" +"<br>");
</script>// 출력
//12월25일
//12월1일
```

&nbsp;

&nbsp;

### 수학 객체

변수의 자료형이 숫자 일때 사용가능하고 최댓값,최솟값, 반올림값등을 연산시켜준다

###### 수학 관련 메소드 및 속성

|          종류          |             설명             |
| :--------------------: | :--------------------------: |
|     Math.abs(숫자)     |      숫자의 절대값 반환      |
| Math.max(숫자1,숫자2~) |   숫자중 가장 큰값을 반환    |
| Math.min(숫자1,숫자2~) |     가장 작은 값을 반환      |
| Math.pow(숫자,제곱값)  |      거듭 제곱값을 반환      |
|     Math.random()      |     0~1사이 난수를 반환      |
|    Math.round(숫자)    | 소수점 첫째 자리에서 반올림  |
|    Math.ceil(숫자)     | 소수의 첫째 자리 무조건 올림 |
|    Math.floor(숫자)    | 소수의 첫째 자리 무조건 내림 |
|    Math.sqrt(숫자)     |      숫자의 제곱근 반환      |
|        Math.PI         |      원주율 상수를 반환      |

&nbsp;

&nbsp;

### 배열 객체

변수에는 하나의 데이터만 저장되지만 여러개의 데이터를 한 변수에 저장할려면 배열을 사용해야한다 배열에는 자료형이 다르더라도 저장할 수 있다

배열 선언 3가지 방법

```
var 변수 = new Array();
변수[0] = 값1; 변수[1]= 값2;
var 변수 = new Array(값1,값2,값3...)
var 변수 = [값1,값2,값3...]
```

&nbsp;

###### 배열 관련 메소드 및 속성

|         종류         |                            설명                            |
| :------------------: | :--------------------------------------------------------: |
|   join(연결 문자)    |  배열 객체를 연결문자를 이용해 1개의 문자형 데이터로 반환  |
|      reverse()       |                 데이터 순서를 거꾸로 반환                  |
|        sort()        |                     오름차순으로 정렬                      |
| slice(index1,index2) |     원하는 인덱스 구간만큼 잘라서 배열 객체로 가져온다     |
|       splice()       | 객체의 지정 데이터를 삭제하고 그 구간에 새로운 데이터 삽입 |
|       concat()       |               2개의 배열 객체를 하나로 결합                |
|        pop()         |                 마지막 인덱스 데이터 삭제                  |
|    push(new data)    |                 마지막 인덱스 데이터 삽입                  |
|       shift()        |                첫번째 인덱스에 데이터 삭제                 |
|  unshift(new data)   |             가장 앞의 인덱스에 새 데이터 삽입              |
|       length()       |                   총 데이터의 개수 반환                    |

&nbsp;

``` javascript
<script>
	var a = ['a','b','c','d','e','f','g']
	b = a.slice(2,4)
	a.splice(2,2,'new1','new2','new3') //c = a.splice(2,2,'new1','new2','new3') 는 제대로 실행이 되지 않음
	document.write(b+'<br>')
	document.write(a+'<br>')
</script> // 출력
//c,d
//a,b,new1,new2,new3,e,f,g
```



 어떤 것은 메소드르 실행할때 변수에 저장할 수 있는게 있고 저장없이 원래 기존의 값을 바꾸는 메소드 splice가 있다 why????

&nbsp;

&nbsp;

### 문자열 객체

문자열 객체 생성하기

`var 변수이름 = new String("string") ` 또는 `var 변수 = "adsfa"`로 선언 가능하다

###### 문자열 객체의 메서드 및 속성

|              종류              |                             설명                             |
| :----------------------------: | :----------------------------------------------------------: |
|          char(index)           |               문자열에서 해당 인덱스 부분 반환               |
|     indexOf(''찾을 문자'')     | 왼쪽부터 찾을 문자와 일치하는 문자를 찾아 제일 먼저 <br />일치한 인덱스 반환없으면 -1반환 |
|    lastIndexOf("찾을 문자")    | 오른쪽부터 찾을 문자와 일치하는 문자를 찾아 제일 먼저 <br />일치한 인덱스 반환없으면 -1 반환 |
|       match("찾을 문자")       | 왼쪽부터 처음 찾은 문자를 찾아 있으면 찾은 문자 반환 없으면 null |
| replace("바꿀 문자","새 문자") |         왼쪽부터 처음 나온 바꿀 문자를 새문자로 바꿈         |
|      search("찾을 문자")       |  왼쪽부터 일치하는 문자를 찾아 제일 먼저 일치한 인덱스 반환  |
|           slice(a,b)           |                    a부터 b까지 값을 반환                     |
|         substring(a,b)         |                    a부터 b까지 값을 반환                     |
|      substr(a,문자 개수)       |              a 인덱스부터 지정한 개수 만큼 반환              |
|         split("문자")          |         지정한 문자를 기준으로 나눠 배열에 저장 반환         |
|         toLowerCase()          |                 모든  대문자를 소문자로 변환                 |
|         toUpperCase()          |                 모든 소문자를 대문자로 반환                  |
|            length()            |                       문자열 길이 반환                       |
|     concat("새로운 문자")      |                 문자열에 새로운 문자를 결합                  |
|       charCodeAt(index)        |                인덱스의 아스키 코드 값을 반환                |
|    fromCharCode(아스키코드)    |                 아스키 코드 해당 문자를 반환                 |
|             trim()             |                   앞 또는 뒤의 공백을 삭제                   |

&nbsp;

``` js
<script>
	var a = "abcdefg"
	b = a.slice(2,-1) // a,b 가 a<b일때 결과는 같지만 음수면 결과가 달라진다
	c = a.substring(2,-1)
	document.write(b + "<br>")
	document.write(c)
</script> // 결과
//cdef    slice는 2->c, -1->g가 대응되었다
//ab      substring은 -1이 0으로 대응되고 0부터 2까지로 대응된다
```

