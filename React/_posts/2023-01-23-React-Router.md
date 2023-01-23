---
layout: post
title: "React-Router"
date:   2023-01-23 15:35:47 +0900
categories:
tags:link router
---

리액트는 싱글 페이지 어플리케이션이다 싱글 어플리케이션과 멀티 어플리케이션 차이는 말 그대로 하나의 페이지 어플리케이션, 여러개 페이지의 어플리케이션이다

* 싱글 페이지: html을 한번만 받아와서  어플리케이션을 실행시키고 그 이후 필요한 데이터를 받아서 화면에 업데이트해준다 
* 멀티 페이지: 사용자가 다른 페이지로 이동할 때마다 새로운 html을 받아오고 페이지를 로딩할 때 css,js등을 서버에서 받아서 브라우저에 보여준다 사용자 인터렉션이 많은 경우 서버의 사용이 늘어난다

&nbsp;

싱글 페이지 어플리케이션은 기술적으로 하나의 페이지이지만 결과를 보면 여러 페이지로 이루어진것 같다 리액트에서는 주소에 따라 알맞은 페이지를 보여준다 이때 새로운 html을 받아오는게 아니라 브라우저의 주소값만 변경하고 기존의 웹은 유지하면서 **라우팅 설정에 따라 다른 페이지를 보여주게 된다**

 &nbsp;

&nbsp;

### 리액트 라우터 사용하기

`npm install react-router-dom`를 리액트앱에서 실행하여 리액트 라우터 돔을 설치한다

* BrowserRouter 태그로 전체를 덮어야 해서 `index.js`에 작성

* Routes는 Route 태그를 묶는데 사용 **Route 태그만 포함되어야한다** 다른 태그가 들어가면 오류
* Route pate="주소 경로" element={실행할 컴포넌트} 주소가 path와 일치하면 해당 컴포넌트 실행
* Link to="붙는 주소" Link를 사용해서 주소를 조작한다

<center>
<img src="https://user-images.githubusercontent.com/80758613/214004018-ddc799b9-8e25-4f38-b272-b08527d651d5.png" style="zoom:40%;">
</center>

`index.js`에 `import { BrowserRouter } from 'react-router-dom';`와 `<BrowserRouter>`를 추가해서 사용한다

``` js
//App.js
import React from "react";
import { Route, Routes, Link } from 'react-router-dom';
import Home from "./components/Home";
import About from "./components/About";
import Detail from "./components/Detail";

function App () {
	
	return (
		<>
		<h1>app page route 위</h1>
		<Routes>
			<Route path='/' element={<Home/>}></Route>
			<Route path='/about' element={<About/>}></Route>
			<Route path='/detail' element={<Detail/>}></Route>
		</Routes>
		<h1>app page route 아래</h1>
		</>
	)
	
}
export default App;
```

`npm run start`를 하면 기본 주소인 /가 실행되어서 Home 컴포넌트가 보여지게 된다

<center>
<img src="https://user-images.githubusercontent.com/80758613/214004456-a0ab0dea-ea20-419c-9f98-fed4ad14f6ac.png" style="zoom:40%;">
</center>

``` js
//Home.js
import React from 'react';
import { Route, Routes, Link } from 'react-router-dom';

function Home() {

    return (
        <>
        <h1>홈 입니다.</h1>
        <Link to='/about' className="button"> about 이동 버튼</Link>
        </>
    )
}
export default Home;
```

홈에서 `Link`를 이용해서 주소를 변경시킬수 있다  버튼을 누르면 주소가 변경되고 `Route`에 의해서 컴포넌트가 변경된다

<center>
<img src="https://user-images.githubusercontent.com/80758613/214006907-9afb0120-ad06-4cc5-8e0f-5d7b3a6b3922.png" style="zoom:40%;">
</center>

``` js
//About.js
import React from 'react';
import { Route, Routes, Link } from 'react-router-dom';

function About() {

    return (
        <>
        <h1>about 페이지</h1>
        <Link to='/detail' className="button"> detail 이동 버튼</Link>
        </>
    )
}
export default About;
```

이동하면 `Home`컴포넌트 자리에 `About` 컴포넌트가 들어간다 (Route 선언 위치와 관계됨) `App.js`에서 작성된 위 아래   태그는 유지된다 안에 컴포넌트만 바뀐다

만약 `Route`컴포넌트를 중첩적으로 사용하고 싶다면 `Route`위치를 바꿔야한다

&nbsp;

about 컴포넌트를 유지하면서  detail 컴포넌트를 띄우고 싶다면

``` js
//App.js
import React from "react";
import { Route, Routes, Link } from 'react-router-dom';
import Home from "./components/Home";
import About from "./components/About";


function App () {
	
	return (
		<>
		<h1>app page route 위</h1>
		<Routes>
			<Route path='/' element={<Home/>}></Route>
			<Route path='/about/*' element={<About/>}></Route>
		</Routes>
		<h1>app page route 아래</h1>
		</>
	)
}
export default App;
```

`Route path='/about/*'` *와일드 카드를 통해서 뒤에 주소가 추가로 더올 수 있다는것을 의미

``` js
//About.js
import React from 'react';
import { Route, Routes, Link } from 'react-router-dom';
import Detail from "./Detail";

function About() {

    return (
        <>
        <h1>about 페이지</h1>
        <Link to='detail' className="button"> detail 이동 버튼</Link>
        <Routes>
            <Route path='/detail' element={<Detail/>}></Route>
        </Routes>
        </>
    )
}
export default About;
```

**여기서 주소를 뒤에 이어서 붙이고 싶다면 `Link to=""`에 /를 작성하면 안된다**

버튼을 누르면 about의 태그를 유지하면서 Detail 컴포넌트를 추가할 수 있다

<center>
<img src="https://user-images.githubusercontent.com/80758613/214011972-1c85fee1-7475-401c-9c47-a0b445d6db92.png" style="zoom:40%;">
</center>

&nbsp;

&nbsp;

### URL쿼리스트링
