---
layout: post
title: "생일 출력하기"
date:   2023-01-03 20:35:47 +0900
categories:
tags: 
---

저장된 json파일을 이용해서 이름이나 id번호를 입력하면 해당된 사람의 생일을 출력하는것이다 

<center>
<img src="https://user-images.githubusercontent.com/80758613/210351717-ad9f3053-40ee-4980-9af7-74554a9a66c1.png" style="zoom:30%;">
</center>

### 기능

1. 인덱스 번호를 입력하여 서버로 부터 생일 데이터를 요청하여 그에 맞는 생일을 출력한다
2. 번호가 아닌 이름을 입력해도 생이리 나오도록 한다
3. 번호가 잘못된 경우 '존재하지 않음'을 출력한다

&nbsp;

&nbsp;

### 프로그래밍 요구 사항

1. rest api를 이용해서 생일 정보를 얻어 와야한다
2. 자바스크립트 문법을 이용
3. rest api를 구현하기 위해 fetch나 axios라이브러리 활용

&nbsp;

&nbsp;

### 구현

#### html

먼저 기본적이 틀인 html파일을 만든다

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1> 생일출력하기</h1>
    <h2>0.a 1.b 2.c 3.d 4.e 5.f 6.g</h2>
    <form>
        이름이나 id를 입력하세요: <input type = "text" id = "index" />
        <input type="button" onclick="input()" value="출력">
    </form>
    <div id="userInfo"></div>
    <div id="error1"></div>

    <script src="js.js"></script>
    
</body>
</html>
```

form에서 input을 button 타입으로 선언하고 onclick을 통해서 자바스크립트 내부에 있는 함수를 실행시키게 작성했다

바로 아래 div태그는 자바스크립트 파일에서 함수를 실행하고 그 결과가 들어갈 공간이다

&nbsp;

&nbsp;

 #### js

js파일에서 html에 입력 받은 데이터를 받고 json파일에 요청을 보내야한다

요청 보내는데 fetch를 사용했고 기본값인 get방식을 사용한다

``` javascript
//cors 오류로 http 임시 서버를 열어서 확인함
function input() {	
	let inputname = document.getElementById("index").value;
	console.log(inputname);
    // 이름음 입력 받았을 경우 fetch에 이름을 넣고 찾는 방식을 해볼려고 했는데 fetch get방식에서
    // id말고 다른 걸 넘결 줄 수 있는지 모르겠다
    if (isNaN(inputname)){//숫자가 아닌 경우
        switch(inputname) { 
            case "a": num = 0;
            break;
            case "b": num = 1;
            break;
            case "c": num = 2;
            break;
            case "d": num = 3;
            break;
            case "e": num = 4;
            break;
            case "f": num = 5;
            break;
            case "g": num = 6;
            break;

            default: num=100; // 만약 이상한 숫자가 아닌값이 넘어가면 오류가 나지만 catch에 잡히지 않는다 
            //따라서 위에 속하지 않으면 숫자로된 아무 값을 넣어 catch로 오류를 찾을 수 있게함 
        }
    } else { //숫자인 경우
        num = inputname
    }
    //url + id를 fetch에 넣어 값을 가져온다
    var address = "json파일의 url"+num;
    fetch(address)
        .then((response) => response.json())
        .then((data) => {
            //받은 값은 promise<data> 로 반환 된다고 하는데 이부분은 잘 이해하지 못하고 사용
            console.log(data);
            const birthday = document.createElement("div");
            birthday.textContent = "생일은 "+data.birth.substr(0,2)+"년" +data.birth.substr(2,2) +"월"+data.birth.substr(4,2)+"일" + "입니다";
            const userInfo = document.getElementById("userInfo");
            userInfo.appendChild(birthday);
            
        })
        //num에 숫자가 와야 오류를 잡는다
        .catch(error => {
            console.log("fetch 에러!");
            alert("존재하지 않음")
        }) 
}

```



**json 파일**

``` json
[
  {
    "id": 0,
    "name": "a",
    "birth": "000704"
  },
  {
    "id": 1,
    "name": "b",
    "birth": "990520"
  },
  {
    "id": 2,
    "name": "c",
    "birth": "001112"
  },
  {
    "id": 3,
    "name": "d",
    "birth": "990210"
  },
  {
    "id": 4,
    "name": "e",
    "birth": "010813"
  },
  {
    "id": 5,
    "name": "f",
    "birth": "001231"
  },
  {
    "id": 6,
    "name": "g",
    "birth": "020313"
  }
]
```

&nbsp;

&nbsp;

**fetch 사용법** 

``` javascript
fetch("url + id")
	.then((res) => res.json())
	.then((data) => console.log(data));

```

만약 위 코드를 실행하면 자바스크림트 객체를 받을 수 있다 console 로그에는 id에 해당하는 값이 들어온다 만약 id가 1이었다면 아래를 콘솔에 보여준다

``` js
{
    "id": 1,
    "name": "b",
    "birth": "990520"
  }
```

이 값들은 Promis로 감싸진 객체이기 때문에 그냥 출력할 수는 없다

data의 값을 따로 추출해서 변수에 저장해 html에 보여줄 수 있다

위 js코드에서 .then((data)=> 이후 부분이 그 과정이다

[fetch](https://minnnning.github.io/자바스크립트/2023/01/03/fetch.html){:target="*_blank"}*



&nbsp;

### html에서 js로 변수 주고 받기

이번 생일 출력하기에서 html에 입력한 값을 가지고 js파일에서 처리를 한뒤에 다시 html에 그 정보를 띄워줘야 한다

html의 태그에서 id를 이용해 js파일의 변수와 대응시켜줄 수 있었다

`let 변수 = document.getElementById("id").value;`

getElementById외에도 class등을 이용하여 대치할 수 있다 html에서 id가 가진 값을 변수에 저장해서 js에서 이용할 수 있다

js에서 값을 적절히 사용한뒤 html에 보여주기위해 ` let show = document.createElement("div");` div라는 태그를 만들고 `show.text = "내용" `으로 변수의 텍스트 값을 저장하고 `const 변수a = document.getElementById("userInfo");`

`변수a.appendChild(show);` 변수a의 아이디를 가진 태그의 자식으로 show를 추가한다 -> html에 id ='userInfo'를 가진 태그의 하위에 `<div> 내용 </div>`가 들어가서 html에 보여진다

&nbsp;

*dom은 따로 작성할 예정*

cors 에러

axios를 통해 이름검색 기능 구현

rest api

