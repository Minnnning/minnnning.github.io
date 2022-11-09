---
layout: post
title: "자바스크립트 기초"
date:   2022-11-09 15:35:47 +0900
categories:
tags: 
---

### 자바스크립트 선언문

자바스크립트는 html파일에서 `<script>자바스크립트 코드</script>` sctipt테그 안에 작성 된다''

\<head>또는 \<body>에 선언된다

``` javascript
<script>
  document.write("화면에 보여줄 메세지");
</script>
```

&nbsp;

### 주석처리

한줄 설명글인 경우에는 **//** 를 이용하고 두줄 이상 넘어가는 경우 **/* 주석 */**을 이용한다

&nbsp;

### 내부 스크립트 분리하기

html 파일에서 자바스크립트까지 작성하게 된다면 코드가 많아지고 유지보수가 어려워진다 따라서 자바스크립트 코드를 html파일과 따로 분리시키고 두개의 파일을 연동시킨다

`<script src="js 파일 경로"></script>`

js파일을 따로 만든다음 html에 그 파일의 위치를 위 문법을 이용해서 연결시킨다

&nbsp;

&nbsp;

&nbsp;

### 코드 입력 시 주의 사항

1. 자바 스크립트는 대소문자를 구분하여 작성한다

``` javascript
New date(); //x
new date(); //o
```



2. 한코드 뒤에는 세미 콜론을 붙인다 (파이썬하다가 와서 빼먹는 경우가 많았다)