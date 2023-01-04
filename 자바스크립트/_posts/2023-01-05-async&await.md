---
layout: post
title: "async & await"
date:   2023-01-05 03:35:47 +0900
categories:
tags: async await
---

비동기적으로 사용하기 위해서 콜백함수를 이용했다 하지만 콜백 함수가 계속 반복되면(콜백지옥) 가독성도 떨어지고 작성하기도 힘들어진다 여기서 나온것이 Promise를 이용한 async & await이다

&nbsp;

### async & await 사용 이유

``` js
timer(1000,function(){
  console.log("1");
  timer(1000,function(){
    console.log("2");
    timer(1000,function(){
      console.log("3");
    });
  }); 
});
```

위 코드에서 timer가 3번만 나왔지만 더 반복되면 코드가 보기 어려워진다(콜백 지옥)

promise를 이용 할 수도 있다

&nbsp;

``` js
function timer(time){ //Promise를 반환
        return new Promise(function(resolve){
            setTimeout(function(){
                resolve(time);
            },time);
        });
    }//time 이후 실행
timer(1000)
  	.then(function(){
  	console.log("1");
  	return timer(1000);
})
  	.then(function(){
  	console.log("2");
  	return timer(1000);
})
  	.then(function(){
  	console.log("3");
  	return timer(1000);
})
```

promise를 이용해서 then으로 체이닝을 통해 처리했지만 then을 연쇄적으로 사용하고 있어서 오류가 나온다면 몇번째 then에서 나온건지 파악이 어렵다 또한 catch를 통해서 예외 처리를 해야하는데 예외처리가 알기 어렵게 되거나 누락하는 경우가 생긴다

&nbsp;

``` js
//async & await 사용
async function run(){
  await timer(1000);
  console.log("1");
  await timer(1000);
  console.log("2");
  await timer(1000);
  console.log("3");
}
run();
```

async와 await를 이용하면 같은 결과를 더 보기 쉽게 나타낼수 있다 또한 async & await 사용하면 비동기 코드를 동기 코드처럼 보기게 할 수 있다

&nbsp;

&nbsp;

&nbsp;

### async와 await 사용

#### async 함수

async는 항상 함수 앞에 위치한다 함수 앞에 위치하면서 그 함수는 **항상 promise를 반환**한다

``` js
async function f() {
  return 1;
}

f().then(alert); // 1 promise 값이 아니더라도 promise로 반환하는걸 확인할 수 있다 
```

또한 **await는 항상 async함수 안에서만 동작한다**

&nbsp;

#### await

await를 만나면 promise가 처리 될때까지 기다린다 기다린 이후에 값이 반환된다

``` js
async function f() {

  let promise = new Promise((resolve, reject) => {
    setTimeout(() => resolve("완료!"), 1000)
  });

  let result = await promise; // 프라미스가 이행될 때까지 기다림 (*)

  alert(result); // "완료!"
}

f();
```

함수를 호출하면 await에서 promise가 처리될 때까지 기다린다음 처리된다(1초) result에는 "완료"가 들어간다

await시간 동안 엔진이 다른일을 처리할 수 있어 리소스가 낭비되지 않는다

&nbsp;

&nbsp;

### 에러 처리

promise가 정상적으로 처리되면 await promise의 result에 저장된 값을 반환한다 만약 거부 된다면 throw처럼 에러가 던져진다

에러를 try/catch를 이용해서 잡을 수 있다

``` js
async function f() {

  try {
    let response = await fetch('http://유효하지-않은-주소');
    let user = await response.json();
  } catch(err) {
    // fetch와 response.json에서 발행한 에러 모두를 여기서 잡습니다.
    alert(err);
  }
}

f();
```

try/catch가 없다면 promise에러가 발생한다

<center>
<img src="https://user-images.githubusercontent.com/80758613/210627234-fdd958bf-5f94-4a12-baa9-c326a7705581.png" style="zoom:50%;">
</center>
