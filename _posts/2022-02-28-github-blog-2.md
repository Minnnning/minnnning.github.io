---
layout: post
title: "github 블로그 만들기(2)"
date:   2022-02-28 16:06:47 +0900
categories: github
tags: git blog
---

저번 글에서 jekyll을 실행해서 블로그를 github.io에 띄웠다 이번에는 자신의 블로그에 테마 적용하는법은 작성해본다 테마마다 적용하는 방법이 약간씩 다르다 또 테마의 구현된 기능을 적용해 본다

&nbsp;

### 1. 원하는 테마 찾기

jekyll을 이용하는 테마는 엄청 많은데 자신의 블로그의 취향에 따라 정하면 된다

- [http://jekyllthemes.org](http://jekyllthemes.org/)
- [https://github.com/topics/jekyll-theme](https://github.com/topics/jekyll-theme)
- [https://jekyllthemes.io/free](https://jekyllthemes.io/free)

위 사이트에서 원하는 테마를 선택하고 github페이지를 들어가서 테마를 다운 받는다

저는 여기서 1번 링크에 있는 Monophase테마를 적용했습니다

<center>
<img alt="스크린샷 2022-02-28 오후 4 51 07" src="https://user-images.githubusercontent.com/80758613/155945048-32de8b49-76c7-4f25-8093-d69cf66d8511.png" style="zoom:50%;">
</center>

자기가 정한 테마의 github로 들어가서 코드를 다운 받는다 코드 버튼 클릭후

<center>
<img alt="스크린샷 2022-02-28 오후 4 52 36" src="https://user-images.githubusercontent.com/80758613/155945235-2c0f9787-e020-46c2-a327-673d45995d2d.png" style="zoom:50%;">
</center>

다운후에 zip파일을 푼다

&nbsp;

&nbsp;

### 2. 테마 적용하기

<center>
<img alt="스크린샷 2022-02-28 오후 5 00 14" src="https://user-images.githubusercontent.com/80758613/155946276-b24289c4-6f5d-4ec6-86f7-23bb87c42c35.png" style="zoom:50%;">
</center>

다운로드 받은 폴더 안의 모든 파일을 복사한다

그 다음 자신의 github블로그 폴더(github.io폴더)에 붙여넣는다

<center>
<img alt="스크린샷 2022-02-28 오후 5 01 11" src="https://user-images.githubusercontent.com/80758613/155946538-5e7771ae-d0fa-41c0-b8fb-8a9393aa9309.png" style="zoom:50%;">
</center>

그럼 이 경고 창이 뜨는데 모두 대치를 적용시켜준다

#### 만약 yml파일의 url이 본인의 url로 안되어있으면 수정한다

&nbsp;

이제 받은 테마에 있는 readme.md 파일을 보고 테마를 설정해줘야한다(테마마다 다름 Monophase 기준)

이 테마의 readme에 따라서

Add this line to your Jekyll site's `Gemfile`:

1. `gem "monophase"`

And add this line to your Jekyll site's `_config.yml`:

2. `theme: monophase`

And then execute:(터미널에서)

3. `bundle` 입력

`bundle install` 와 같이 bundle를 설치이다

4. `bundle exec jekyll sever` 를 통해서 로컬 서버를 실행시킨다

&nbsp;

&nbsp;

### 3. 서버 실행하기(오류 나와서 오류 해결법)

위 `bundle exec jekyll sever` 후에 아래위 에러가 떴다

```
[!] There was an error parsing `Gemfile`: You cannot specify the same gem twice coming from different sources.

You specified that monophase (>= 0) should come from source at `.` and

. Bundler cannot continue.



 \# from /Users/minnnning/Desktop/minnnning.github.io/Gemfile:6

 \# -------------------------------------------

 \#

 \> gem "monophase"

 \# -------------------------------------------
```

gemfile의 gemspec를 지워주었더니 정상적으로 실행되었다

<center>
<img alt="스크린샷 2022-02-28 오후 5 35 46" src="https://user-images.githubusercontent.com/80758613/155950928-dafa4faa-c699-4e06-b2ae-492158404a7d.png" style="zoom:50%;">
</center>

로컬 주소에 접속하면 테마가 적용된것을 알 수 있다

<center>
<img alt="스크린샷 2022-02-28 오후 5 40 07" src="https://user-images.githubusercontent.com/80758613/155951461-d76ecf95-6696-4080-a76f-20665ff3b313.png" style="zoom:33%;">
</center>

사진처럼 테마가 적용될것이다 사진은 현재 사용하는 페이지라서 많지만 처음에는 글 하나와 위 카테고리도 없을것이다(나중에 적용)

테마가 적용 된것을 로컬에서 확인했고 이제 github에 올려서 확인해야한다

&nbsp;

&nbsp;

### 4. github에 올리기 (오류 해결)

사실 이 과정은 별것없고 그냥 로컬파일을 github에 올리기만 하면 된다

터미널 위치를 github.io폴더로 이동(아마 아까와 계속 같은 위치일 것이다)

`git add *` 폴더 안 모든 파일을 추가함

`git commit -m "theme add"` 커밋

`git push` 푸시 

github에 푸시하면 본인의 github.io로 접속해서 올라가 페이지를 볼 수 있다

&nbsp;

저는 github안에서 오류가 났는데 push는 정상적으로 되었다고 해도 페이지가 보이지 않았다

<center>
<img alt="스크린샷 2022-02-28 오후 6 09 48" src="https://user-images.githubusercontent.com/80758613/155955651-5b294c8a-27c4-49b4-a825-665d6d56943a.png" style="zoom:33%;">
</center>

내 레포의 action을 보니 github.io에 내 페이지를 올리는 과정에서 에러가 난것 같았다(push는 정상적으로 이루어짐)

<center>
<img alt="스크린샷 2022-02-28 오후 6 12 54" src="https://user-images.githubusercontent.com/80758613/155956273-dbab9167-1200-4998-9dd3-df175adf61e9.png" style="zoom:30%;">
</center>

yml파일에서 테마를 monophase롤 바꿨는데 정상적으로 인식은 못하는것 같다 

`gem install monophase` 도 해봤는데 같은 에러가 나온다

&nbsp;

해결하기위해 theme를 remote_theme: monophase로 바꿨다 [참고한 사이트](https://github.com/jekyll/jekyll/issues/8468)

바꾸고 다시 `bundle exec jekyll serve` 를 했는데

```
Liquid syntax error (/Users/minnnning/Desktop/minnnning.github.io/_includes/head.html line 8): Unknown tag 'seo' included (Liquid::SyntaxError)
```

에러가 떴다 seo관련된게 없다고 하니까 추가한다

터미널에서 `gem install jekyll-seo-tag` 입력

yml파일에 들어가서 plugins에   \- jekyll-seo-tag 를 추가 한다

&nbsp;

이제 `bundle exec jekyll serve`를 통해서 로컬 작동을 확인하고 github에 push해서 자신의 블로그가 정상적으로 뜨는지 확인한다!!!