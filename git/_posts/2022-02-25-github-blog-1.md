---
layout: post
title: "github 블로그 만들기(1)"
date:   2022-02-25 16:06:47 +0900
categories:
tags: git
image: "https://user-images.githubusercontent.com/80758613/155680099-eeae1b73-cdfe-4c83-b2fc-7b51b9775ec7.png"
---

원래 네이버 블로그에서 공부한것을 정리했지만 네이버 블로그의 스마트에디터가 블루투스 키보드나 사파리와 맞지 않아서 다른 플랫폼을 찾아보다 github.io 블로그를 찾게 되었다

git블로그를 사용할려면 기본적인 git 사용법과 터미널의 사용법 또 블로그를 꾸미기위해 html과 css등이 필요할 수 있지만 이번에는 그냥 맘에 드는 테마를 사용하기로 했다 테마를 적용한다고 끝이 아니라 글쓰기 테마 사용법등을 기록하기 위해 글을 작성해본다

먼저 처음하는거라 다른 [블로그](https://zeddios.tistory.com/1222){:target="_blank"} 글을 참고했다 

---

&nbsp;

&nbsp;

### 1. 깃허브 레파지토리 만들기

깃허브 홈페이지에 들어가서 새로운 레파지토리를 만든다

<center><img alt="스크린샷 2022-02-25 오후 4 31 29" src="https://user-images.githubusercontent.com/80758613/155680099-eeae1b73-cdfe-4c83-b2fc-7b51b9775ec7.png" style="zoom:33%;"></center>

레파지토리 버튼을 누르면 위 화면이 나오는데 오른쪽에 초록색 new버튼을 눌러 새로운 레파지토리를 만든다

<center><img alt="스크린샷 2022-02-25 오후 4 33 41" src="https://user-images.githubusercontent.com/80758613/155680842-e956d4dc-7d89-459a-8a23-8564fbef84e7.png" style="zoom:33%;"></center>

레포 이름을 {사용자이름}.github.io 로 만들어준다

위에 사진에는 이미 같은 이름이 있어서 생성이 안된다고 뜨는것

레포이름은 나중에 접속할 주소가 된다

&nbsp;

&nbsp;

### 2. 로컬에 git 파일을 clone

터미널을 키고 클론할 위치까지 현재위치를 이동시킨다

<center><img alt="스크린샷 2022-02-25 오후 4 41 34" src="https://user-images.githubusercontent.com/80758613/155680984-d0410306-667a-471f-9bea-1ece0de7da5b.png" style="zoom:33%;"></center>

| 간단한 터미널 명령어 |                           |
| -------------------- | ------------------------- |
| pwd                  | 현재위치 표시             |
| cd 폴더명            | 해당 폴더로 진입          |
| cd ..                | 이전으로 나온다           |
| ls                   | 현재 위치의 파일을 보여줌 |

원하는 위치로 이동하면 클론을 시작한다

`git clone https://github.com*******` 이 코드를 입력한다 이 뒤에 주소는 본인의 레포로 들어가서 

초록색 code 버튼을 누르면 주소가 나온다

<center><img width="994" alt="스크린샷 2022-02-25 오후 4 48 38" src="https://user-images.githubusercontent.com/80758613/155681338-0b1940cf-012d-48d0-9f30-89cb128f80ac.png" style="zoom:60%;"></center>

그럼 해당 위치에 클론한 폴더가 만들어져 있다

&nbsp;

&nbsp;

### 3. jekyll을 시작하자

터미널에서 현재 위치를 확인한 다음 클론한 폴더로 이동한다 클론한 폴더명은 github이름.github.io로 되어있다

cd 폴더명을 통해서 해당 폴더로 접속후 

`gem install jekyll bundler ` 을 입력해서 jekyll을 설치해준다

###### 만약 설치과정에서 오류가 나온다면 [참조](https://unluckyjung.github.io/develop-setting/2021/01/20/Mac-Jekyll-Setting/){:target="_blank"}

클론한 폴더 안의 모든 파일을 지우고( readme.md파일을 지운다) 

터미널에 `jekyll ./ new` 입력한다 

###### 만약에 폴더를 비우지 않으면 

###### Conflict: /Users/minnnning/Desktop/minnnning.github.io exists and is not empty. 충돌된다



위 과정을 오류 없이 넘겼으면 `bundle install` 터미널에 입력

`bundle exec jeklly serve` 터미널에 입력시키면 로컬 주소가 나온다

<center>
<img alt="스크린샷 2022-02-25 오후 5 51 48" src="https://user-images.githubusercontent.com/80758613/155685267-bab47e74-e41d-45db-aa12-43b64fd491f9.png" style="zoom:50%;">
</center>

http://127.0.01:4000/ 을 주소창에 치면 내가 만든 블로그를 로컬환경에서 볼 수 있다 (아직 서버에 올라가지 않음)

&nbsp;

&nbsp;

### 4. URL 변경하기

내가 만든 페이지의 주소를 변경하려면 먼저 클론한 파일안에 있는 _config.yml 파일을 연다(텍스트 편집기)

파일안에서 url 부분을 찾아서 본인의 주소로 바꿔준다

형식은 url: "http://본인것.github.io" 이렇게 바꿔 주고 저장한다

<center><img alt="스크린샷 2022-02-25 오후 6 01 54" src="https://user-images.githubusercontent.com/80758613/155686451-77e9d9c4-8d8a-44c4-ba0a-94fbab56cf22.png" style="zoom:50%;"></center>

###### 예시는 테마를 적용해서 내용이 다를 수 있지만 url만 찾아서 바꾸면 된다

&nbsp;

&nbsp;

### 5. 깃허브에 올리기

클론한 폴더를 깃허브에 커밋하고 올린다

`git add *`

`git commit -m"커밋 이름"`

`git push -u origin main `

3가지를 모두 터미널에 순서대로 작성하면 본인의 레포에 올라갔을 것이다

이제 주소창에 http://본인것.github.io를 검색하면 만들어진 사이트가 뜬다

<center>
<img alt="스크린샷 2022-02-25 오후 6 14 20" src="https://user-images.githubusercontent.com/80758613/155688389-aaedbbf7-7e90-4a8f-af9a-a93d3d03a0a6.png" style="zoom:50%;">
</center>

<center>**이렇게 뜨면 성공!**</center>

&nbsp;

&nbsp;

만약 정상적으로 뜨지 않는다면 깃허브를 확인해 봐야한다

<center>
<img alt="스크린샷 2022-02-25 오후 6 16 32" src="https://user-images.githubusercontent.com/80758613/155691388-0b724ea7-fb1e-4f34-bc36-fe117dc63f9b.png" style="zoom:50%;">
</center>

자신의 레포로 들어가서 actions를 들어가면 현재 진행 상황을 알 수 있다 만약 사진 처럼 커밋후 맨위에것이 노란색이면 깃허브 서버에 만든 블로그를 올리고 있는 중이어서 기다렸다가 초록색으로 변하면 다시 확인한다

