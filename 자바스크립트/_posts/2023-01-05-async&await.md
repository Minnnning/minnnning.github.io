---
layout: post
title: "async & await"
date:   2023-01-05 03:35:47 +0900
categories:
tags: async await
---

비동기적으로 사용하기 위해서 콜백함수를 이용했다 하지만 콜백 함수가 계속 반복되면(콜백지옥) 가독성도 떨어지고 작성하기도 힘들어진다 여기서 나온것이 Promise를 이용한 async & await이다

``` js
function timer(time){ //Promise를 반환
        return new Promise(function(resolve){
            setTimeout(function(){
                resolve(time);
            },time);
        });
    }//time 이후 실행
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

``` js
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

