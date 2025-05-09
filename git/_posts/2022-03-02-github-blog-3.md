---
layout: post
title: "github 블로그 만들기(3)"
date:   2022-03-02 21:06:47 +0900
categories:
tags: git
excerpt_image: "https://user-images.githubusercontent.com/80758613/156361954-73c24192-7721-4d46-a5ec-b9759b8ac676.png"
---

github블로그에 테마를 적용시켰으니 블로그를 꾸며야한다 이번에는 블로그 이름 바꾸기와 테마의 기능을 사용한다

---

&nbsp;

&nbsp;

### 1. 블로그 기본 설정 바꾸기

블로그의 이름을 바꾸기 위해서는 _config.yml 파일을 이용한다

<center>
<img alt="스크린샷 2022-03-02 오후 9 29 39" src="https://user-images.githubusercontent.com/80758613/156361954-73c24192-7721-4d46-a5ec-b9759b8ac676.png" style="zoom:30%;">
</center>

여기서 title은 말 그대로 블로그의 제목이 된다

tagline은 이 테마에서 제목 옆에 작게 써진다

email이나 twitter등은 이 테마에서 보이지 않기 때문에 위 2가지만 설정해줘도 괜찮다

<center>
<img alt="스크린샷 2022-03-02 오후 9 53 11" src="https://user-images.githubusercontent.com/80758613/156366021-e36b445e-2c63-400a-af1f-9e681aace316.png" style="zoom:50%;">
</center>

적용된 모습

&nbsp;

&nbsp;

### 2. 네비게이션 바 만들기

다운 받은 테마의 readme 파일에는 테마의 적용된 기능을 사용하는 방법이 나온다

내가 적용한 monophase 테마의 기능중에서 네비게이션을 적용해보자

&nbsp;

#### 1- 네비게이션 파일 만들기

github.io폴더 안에서 새로운 폴더를 만들어준다 _data 라는 폴더를 만들고 그안에 navigation.yml파일을 만든다

yml파일을 만드는 방법은 터미널에서 만들고 싶은 위치에 `touch navigation.yml ` 을 통해서 파일은 만든다

<center>
<img alt="스크린샷 2022-03-02 오후 10 03 51" src="https://user-images.githubusercontent.com/80758613/156366873-9b9f7226-a1d6-4b3b-929e-1b5749507149.png" style="zoom:50%;">
</center>

파일 안에 만들고 싶은 네비게이션 이름을 정하고 title에 입력 url은 그 버튼을 누르면 이동할 url이다 메인 페이지 url 뒤에 붙어서 버튼을 누르면 minnnning.github.io/years/ 로 보여진다

네비게이션 버튼을 다 만들었으면 이동할 페이지를 만들어야한다

&nbsp;

#### 2- 보여줄 페이지 만들기

github.io폴더로 돌아와서 네비게이션 title이름으로 마크다운 파일을 만든다

`touch categories.markdown` 또는 마크다운 에디터를 이용하여 파일을 만들어도 된다

<center>
<img alt="스크린샷 2022-03-02 오후 10 18 09" src="https://user-images.githubusercontent.com/80758613/156369122-8513966a-b9bf-4667-a8b6-96b58efd8bde.png" style="zoom:50%;">
</center>

`---`를 감싸주고 안에 layout 과 title type 링크를 적어준다

레이아웃은 페이지를 보여주는데 이용할 html파일이다 _layouts 폴더를 보면 안에 archive.html로 만들어진 파일을 볼 수 있다 (이미 만들어진것은 include 하는것이다)

타이틀은 클릭시 보여줄 타이틀이된다

type은 이용할 요소이다 여기서는 years,categories,tags를 사용 할 수 있다

permalink는 해당 url로 이동하면 현재 마크다운 페이지를 보여주는것이라 페이지를 보여줄 url이 들어간다

&nbsp;

`---`안에 작성하는것은 템플릿 언어인 liquid을 위해서 yaml이라는 형태로된 정보이다 [여기보고 알았다](https://velog.io/@kasterra/Github-블로그-만들기-첫걸음-Jekyll-기초){:target="_blank"}

다른 네비게이션 버튼도 동일한 방법으로 만든다

<center>
<img alt="스크린샷 2022-03-02 오후 10 41 35" src="https://user-images.githubusercontent.com/80758613/156373036-d338e15e-aa80-426a-bcf8-a1a66373ba90.png" style="zoom:50%;">
</center>

이렇게 네비게이션 바가 생기고 버튼을 누르면 이동할 수 있게 된다 만약 글이 있다면

<center>
<img alt="스크린샷 2022-03-02 오후 10 42 05" src="https://user-images.githubusercontent.com/80758613/156373257-0e126acc-623c-4ef9-9601-2b428d47affe.png" style="zoom:50%;">
</center>

위 처럼 글이 카테고리에 따라 나눠서 보인다 카테고리나 태그 설정은 글 작성 할때 작성한다

&nbsp;

&nbsp;

###  3. 소셜 버튼 추가하기

소셜 버튼은 화면 맨 아래 2개의 버튼이 있는데 메일과 깃허브 페이지 버튼이 있다 트위터도 추가 할 수 있지만 트위터가 없어서 위 2개만 추가 했다

_data 폴더에 들어가서 `touch social.yml `을 만들고 안에 본인의 것으로 작성한다

```
- title: Email
  url: mailto:zivmsg@gmail.com
  icon: fas fa-envelope
- title: Twitter
  url: https://twitter.com/zivtwt
  icon: fab fa-twitter
- title: GitHub
  url: https://github.com/zivhub
  icon: fab fa-github
```



그러면 페이지 하단에 버튼이 3가지 버튼이 추가된다

<center>
<img alt="스크린샷 2022-03-02 오후 10 53 07" src="https://user-images.githubusercontent.com/80758613/156374816-f351ac0c-6f6d-4755-93c8-fe765e664be6.png" style="zoom:33%;">
</center>
