---
layout: post
title: "useEffect 사용하기"
date:   2023-01-22 15:35:47 +0900
categories:
tags:
---

useEffect는 컴포넌트가 특정 작업(컴포넌트 렌더링 이후)을 실행할 수 있도록 하는 hook이다 (비동기 처리에 이용)

클래스형에서는 render()가 업데이트 될때 재렌더링이 되는데 함수형에서는 불가능하다 이부분을 해결해주는게 useEffect이다 함수형 컴포넌트에서 sideeffect(라이프사이클훅, 생명주기 메소드)를 사용

useEffect에서 마운트(처음)/ 언마운트(사라질때)/ 업데이트시 할 작업을 설정한다

&nbsp;

&nbsp;

### useEffect

useEffect는 2가지 인자가 들어가는데 첫번째 인자는 함수고 두번째 인자는 배열이다 `useEffect(function,[deps])` 배열 부분에 어떤 값이 들어가는지에 따라 언제 작업을 하는지 결정한다

1. **배열인자가 없을때**

``` js
import React, { useState, useEffect } from "react";

const UseEffectTest = () => {
  const [count, setCount] = useState(0);
  const countUp = () => setCount(count + 1);

  useEffect(() => {
    console.log("useEffect!!", count);
  });
  console.log("언제실행?")
  return (
    <div>
      <p>{count}번 클릭!</p>
      <button onClick={countUp}>Click Me</button>
    </div>
  );
};

export default UseEffectTest;
```

**렌더링이 완료될 때 마다 useEffect 실행된다**  `console.log("useEffect!!", count)`를 그냥 해당 위치에 작성한는것과 같은 결과지만 이건 렌더링 이후 실행된다

 <center>
<img src="https://user-images.githubusercontent.com/80758613/213913544-8c84f84c-e7d5-45b9-80a4-c0d21b56a2be.png" style="zoom:40%;">
</center>

&nbsp;

&nbsp;

2. **빈 배열일 때**

``` js
import React, { useState, useEffect } from "react";

const UseEffectTest = () => {
  const [count, setCount] = useState(0);
  const countUp = () => setCount(count + 1);

  useEffect(() => {
    console.log("useEffect!!", count);
  }, []);

  return (
    <div>
      <p>{count}번 클릭!</p>
      <button onClick={countUp}>Click Me</button>
    </div>
  );
};

export default UseEffectTest;
```

빈 배열이라면 **처음 실행시(마운트)에만 실행한다**

<center>
<img src="https://user-images.githubusercontent.com/80758613/213913671-64449495-f5ae-49de-bc5b-d33319357dbe.png" style="zoom:50%;">
</center>



&nbsp;

&nbsp;

3. **배열에 변수 이름**

``` js
import React, { useState, useEffect } from "react";

const UseEffectTest = () => {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("김민정");
  const countUp = () => setCount(count + 1);
  const handleChangeName = (e) => setName(e.target.value);

  useEffect(() => {
    console.log("useEffect!!", count);
  }, [count]);

  return (
    <div>
      <p>안녕하세요. {name} 입니다.</p>
      <input onChange={handleChangeName} />
      <p>{count}번 클릭!</p>
      <button onClick={countUp}>Click Me</button>
    </div>
  );
};

export default UseEffectTest;
```

**특정 값이 변경 되었을 때만 재렌더링 된다** 여기서는 name은 변경되어도 useEffect가 실행되지 않지만 count가 변경되면 `console.log("useEffect!!", count)` 가 실행된다

<table><td><center><img alt="" src="https://user-images.githubusercontent.com/80758613/213913955-9387d027-017f-4d78-9ef5-b04805153ca2.png" style="zoom:30%;" /></center></td><td><center><img alt="" src="https://user-images.githubusercontent.com/80758613/213913960-57f5722c-bbf1-4f10-980a-601d441c242d.png" style="zoom:30%;" /></center></td><td><center><img alt="" src="https://user-images.githubusercontent.com/80758613/213913961-d6b4f73e-4b3f-4185-90cf-da762757ef4c.png" style="zoom:30%;" /></center></td></table>

&nbsp;

&nbsp;

### Clean up 함수

클린업 함수는 useEffect에서 파라미터로 넣은 함수의 return 함수이다

컴포넌트에서 마운트 이전 언마운트 직전에 작업을 수행하고 싶으면 clean up 함수를 반환해줘야 한다

* 특정값 마운트 이전 ` useEffect(func,[특정값])`
* 언마운트 직전 `useEffect(func,[])` 빈배열

**재렌더링 -> 이전 effect clean up -> effect** 순서로 실행

``` js
import React, {useState, useEffect} from "react";
//특정값 마운트 직전

export default function App() {
const [count, setCount] = useState(0)


  useEffect(() => {
    console.log("useEffect !")
    return () => {
      console.log(count)
    }
  },[count])


  return (
    <div >
      <h2>{count}</h2>
      <button onClick={() => setCount(count + 1)}>+</button>
    </div>
  );
}
```

&nbsp;

<center>
<img src="https://user-images.githubusercontent.com/80758613/213979894-97a9dfbf-5179-4bd8-a5ff-db6b4fc20029.png" style="zoom:40%;">
</center>

1번고 2번은 기본출력이라 무시하고 3번째 줄부터 본다면 `useEffect !`가 먼저 실행되고 버튼을 누르기직전에 return이 실행되어서 값이 1이 콘솔에 나타나고 화면에는 2가 나타나게 된다 useEffect함수가 다시 시작되기 직전에 return이 시작되는것을 알수 있다 따라서 화면에 보여지는 값보다 1이 작게된다

