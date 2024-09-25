---
layout: post
title: "github 블로그 글쓰기"
date:   2022-03-03 14:35:47 +0900
categories:
tags: git
excerpt_image: "https://user-images.githubusercontent.com/80758613/156505200-bc96c3ca-a114-4c6f-8782-d539820f3370.png"
---

이제 블로그를 만들었으니 글을 써야 한다 + 글 올리기 github 블로그에서 글을 쓰기 위해서는 markdown이라는 파일을 알아야 한다

마크다운은 마크업 언어의 일종으로 쉽게 읽고 쓰기가 가능하고 html로 변환이 가능하다 마크다운은 텍스트 편집기에서도 작성이 가능하고 다양한 형태로 바꿀 수 있기 때문에 많이 이용한다

---

&nbsp;

&nbsp;

### 1. 마크다운 에디터

마크다운 에디터를 사용하면 마크다운 파일을 작성할 때 더 편리하게 작성이 가능하다 에디터를 이용하면 문법을 몰라도 작성 가능하다

- [typora](https://typora.io/){:target="_blank"}
- [stackedit](https://stackedit.io/){:target="_blank"}
- [하루패드](http://pad.haroopress.com/){:target="_blank"}

&nbsp;

&nbsp;

### 2. 마크다운 문법

마크다운 안에는 많은 문법들이 있지만 블로그 글을 쓸 때 필요한 몇 가지만 적어본다

&nbsp;

#### Header

- 제목

  제목은 제목을 작성한뒤에 아래줄에 =를 작성

  &nbsp;

- 부제목

  부제목은 부제목 작성후 밑에 - 작성한다

  <center>
  <img alt="스크린샷 2022-03-03 오후 2 55 42" src="https://user-images.githubusercontent.com/80758613/156505200-bc96c3ca-a114-4c6f-8782-d539820f3370.png" style="zoom:33%;">
  </center>

  &nbsp;

- 글머리

  #글머리 사용 #뒤에 띄어쓰기 한번을 한뒤에 작성 1~6개까지 사용 가능하며 점점 글씨 크기가 작아진다

  #한개는 제목 효과 ##2개는 부제목

<center>
<img alt="스크린샷 2022-03-03 오후 2 51 52" src="https://user-images.githubusercontent.com/80758613/156504664-1ccbdda4-d3cb-4d84-a066-3ea024a5aba8.png" style="zoom:33%;">
</center>

&nbsp;

#### 인용문

`>`문자를 사용해서 작성한다

<center>
<img alt="스크린샷 2022-03-03 오후 3 00 54" src="https://user-images.githubusercontent.com/80758613/156505643-fdebfa1f-4c73-4dcb-889c-eeb1693b0319.png" style="zoom:33%;">
</center>

&nbsp;

#### 목록

순서가 있는 목록과 순서가 없는 목록이 구분된다

순서가 있는 목록은 숫자와 마침표을 이용한다(숫자. 목록) 다음 목록은 자동으로 다음 숫자가 들어간다

순서가 없는 목록은 *,-,+ 3개중 1가지를 이용하여 표시한다

<center>
<img alt="스크린샷 2022-03-03 오후 3 07 01" src="https://user-images.githubusercontent.com/80758613/156506707-860b1e71-b825-4f97-bfb9-6a2c202b0597.png" style="zoom:33%;">
</center>

&nbsp;

#### 코드 박스

`'''`로 감싸주면 된다 만약 특정 언어로 하고 싶으면 앞쪽의 `'''`뒤에 언어를 써주면 된다

<center>
<img alt="스크린샷 2022-03-03 오후 3 15 40" src="https://user-images.githubusercontent.com/80758613/156507443-ecaf4b1b-4b14-432f-a1df-a039b0391193.png" style="zoom:33%;">
</center>

`'''`뒤에 언어를 작성하면 해당언어에 맞는 효과를 추가 할 수 있다

&nbsp;

#### 한줄 띄우기 

마크다운에서는 엔터로 한줄 띄어도 실제로 적용되지는 않는다 `&nbsp;`를 이용하면 된다

<center>
<img alt="스크린샷 2022-03-03 오후 3 25 12" src="https://user-images.githubusercontent.com/80758613/156508593-ad8c4060-f2bd-423c-b0b0-cc27321554c1.png" style="zoom:33%;">
</center>

&nbsp;

#### 하이퍼링크

하이퍼링크를 작성하는 방법은 `[표시할 이름](주소)` 가 된다 만약 클릭후 다른 창에 띄우고 싶으면 하이퍼링크 작성후 뒤에 `{:target="_blank"}`를 작성해주면 된다

<center>
<img alt="스크린샷 2022-03-03 오후 3 46 31" src="https://user-images.githubusercontent.com/80758613/156511660-35f9f979-8ecc-4ad5-bdfb-f5ae1d607b0a.png" style="zoom:33%;">
</center>

&nbsp;

&nbsp;

### 3. git blog에 글 작성

마크다운 문법을 통해서 글을 작성했다 만약 그냥 마크다운 파일만 보여주는 것이라면 상관이 없는데 테마를 적용한 git blog에서는 추가적으로 작성해야 될 것이 있다 블로그 테마와 적용이 잘 되기 위해서 제목, 글쓴이, 작성 시간, 수정시간, 카테고리, 태그 등을 작성해야 한다

<center>
<img alt="스크린샷 2022-03-03 오후 3 54 23" src="https://user-images.githubusercontent.com/80758613/156512596-efd4ca9c-b206-4485-b061-483b38533ba3.png" style="zoom:33%;">
</center>

사진을 보면 위에 본문 말고 따로 작성된 것이 보인다 에디터에서 작성하면 `---`만 적고 엔터를 치면 되지만 텍스트 편집기에서는 `---`로 묶어서 작성해야 한다

레이아웃은 내 테마의 레이아웃을 가져오고 타이틀은 제목이 된다 날짜는 작성된 날짜가 되고 카테고리와 태그를 추가했다

last_modified_at, author등을 따로 추가 가능

&nbsp;

이제 저장을 하고 파일을 _post 폴더에 넣으면 된다 하지만 파일 이름을 한글로 작성하면 페이지를 못 찾는다고 나올 수 있다 영어로 작성하자 ex) 2022-03-03-hi.md -> 파일 이름 형식은 **날짜-영어로파일이름. md** 이렇게 한다

&nbsp;

&nbsp;

### 4. 글 올리는 방법

위 과정을 다 끝내고 github에 push만 해주면 자신의 블로그에 글이 올라간걸 확인 할 수 있다

다른 컴퓨터에서 작업하고 글을 올리는것이면 다시 colne해서 _post파일에 넣을 수도 있지만 github를 이용해서 바로 올릴수 있다

<center>
<img alt="스크린샷 2022-03-03 오후 4 11 18" src="https://user-images.githubusercontent.com/80758613/156514639-015d0fb8-1424-4209-9cd2-9ae72cedda47.png" style="zoom:33%;">
</center>

자신의 레포에서 _post파일로 들어간뒤 upload files를 누른다

<center>
<img alt="스크린샷 2022-03-03 오후 4 09 14" src="https://user-images.githubusercontent.com/80758613/156514722-6370c5c3-19cd-4f85-b66f-364bc6e35d81.png" style="zoom:33%;">
</center>

여기에 작성한 마크다운 파일을 드래그 앤 드롭하거나 choose your files를 통해서 파일을 선택후 올려도 된다

