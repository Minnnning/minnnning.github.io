---
layout: post
title: "Fast API get, post, delete 사용하기"
date:   2023-02-01 15:35:47 +0900
categories:
tags: 
excerpt_image: "https://user-images.githubusercontent.com/80758613/215980066-654d91b4-0385-4efa-a243-e414cbb5741d.png"
---

fast api를 사용하기위해 기본적인 get,post,delete를 사용해보자 http의 대표적인 메소드로는 get, post, put, delete가 있다

* get: 데이터를 가져오기
* post: 데이터를 생성및 저장
* put: 데이터 업데이트
* delete: 데이터 삭제

&nbsp;

### Fast API

``` python
from fastapi import FastAPI
app = FastAPI()
@app.get("/")
def root():
    return {"Hello":"World!"}
```

위 코드는 fast api를 임포트하고 fast api의 인스턴스를 생성하는 과정이다

`uvicorn main:my_awesome_api --reload`로 main.py를 실행하면 Hello : World가 출력된다

&nbsp;

### post

post를 하는 경로와 동작을 생성해야된다

``` python
//main.py
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

db = []

class City(BaseModel):
    name: str
    capital: str
    
@app.get("/")
def root():
    return {"Hello":"World!"}

@app.post('/cities') #도시 정보 저장
def create_city(city: City):
    db.append(city.dict())
    
    return db[-1]
```

`@app.post(경로)`에서 경로를 설정하고 아래 함수를 통해서 동작을 설정한다 City클래스를 이용해서 입력된 값을 딕셔너리로 바꾸고 db리스트에 추가하는 동작이다

`return`을 통해서 방금 저장한 값을 확인 할 수 있다 db에 마지막으로 저장된 값을 리턴

<center>
<img src="https://user-images.githubusercontent.com/80758613/215980066-654d91b4-0385-4efa-a243-e414cbb5741d.png" style="zoom:50%;">
</center>

&nbsp;

&nbsp;

### get

get을 통해서 원하는 값 받아오기오기와 전체의 값을 받아 올 수 있다

``` python
//main.py
@app.get('/cities') # 저장된 전체 도시정보 가져오기 
def get_cities():
    results =[]
    for city in db:
        results.append({'name':city['name'],'capital':city['capital']})
        
    return results

@app.get('/cities/{city_id}') #선택한 아이디 도시정보 가져오기
def get_city(city_id:int):
    city = db[city_id-1]
    
    return { 'name':city['name'],'capital':city['capital']}
```

`app.get(경로)`경로를 보면 두개의 get이 다른 경로를 나타내는걸 알 수 있다

**get_cities함수**는 저장된 전체 정보를 가져오는 함수이다 results란 변수를 새로 생성해서 db리스트를 반복문으로 돌려 값을 가져와 새로운 리스트에 추가한다 db에는 데이터가 딕셔너리로 되어있어 딕셔너리 형태로 값을 가져와야한다

<table><td><center><img alt="" src="https://user-images.githubusercontent.com/80758613/215982893-bb6f34d2-b2a3-410a-a582-6e44013acd15.png" style="zoom:50%;" /></center></td><td><center><img alt="" src="https://user-images.githubusercontent.com/80758613/215982936-51d038c0-16be-47b1-8fe3-b1f44b888b69.png" style="zoom:40%;" /></center></td></table>

&nbsp;

**get_city함수**는 뒤에 추가적으로 city_id가 붙는다 이건 경로 매개변수로 여기에 들어가는 값을 이용한다

db리스트에서 입력값의 -1의 인덱스(0부터 시작해서 -1함)의 데이터를 city라는 변수에 넣고  해당 데이터만 출력한다

<table><td><center><img alt="" src="https://user-images.githubusercontent.com/80758613/215983560-7aff42a7-ce37-44db-bf7b-72640ce5b7cb.png" style="zoom:50%;" /></center></td><td><center><img alt="" src="https://user-images.githubusercontent.com/80758613/215983271-71198fb2-d593-4c52-b7a9-5bfaa78cde22.png" style="zoom:50%;" /></center></td></table>

&nbsp;

&nbsp;

### delete

``` python
//main.py
@app.delete('/cities/{city_id}') # 도시 정보 삭제
def delete_city(city_id:int): 
    db.pop(city_id-1)
    
    return {}
```

경로 매개변수를 받아서 db pop에 값을 이용한다 해당 인덱스의 값을 삭제하는 함수를 실행해서 delete를 구현한다

<table><td><center><img alt="" src="https://user-images.githubusercontent.com/80758613/215986067-ba1f6a06-4a3c-4088-a3c3-390ef5309b09.png" style="zoom:50%;" /></center></td><td><center><img alt="" src="https://user-images.githubusercontent.com/80758613/215986050-c22192c5-90cc-4bca-92d3-fea3e2805f71.png" style="zoom:50%;" /></center></td></table>

인덱스 0번이었던(입력은 1이지만 인덱스는 0) `name:string,captial:string23` 이 삭제된것을 확인 할 수 있다

&nbsp;

&nbsp;

### 경로 같을 때

만약 get 방식으로 경로가 같은 2개가 있다고 한다면 

``` python
@app.get("cite/a")
def get_city1():
  return {"Hello":"a!"}

@app.get("cite/{city_name}")
def get_city2(city_name: str):
  return {"Hello":city_name}
```

a를 입력 했을때 `get_city2`대신 `get_city1`을 실행시키고 싶다면 위 예시처럼 get_city1을 먼저 선언해주면 된다

만약 `get_city2`가 먼저 선언되었으면 `get_city1`은 실행되지 못한다

&nbsp;

위 코드에서 경로가 같은 2개가 있다 get_city와 delete_city는 경로가 같지만 주소창에 입력하면 delete는 실행되지 않고 get_city가 실행된다

왜냐하면 브라우저는 기본적으로 get방식을 이용하기 때문에 선언 순서를 바꾸더라도 get 방식인 `get_city`를 실행시킨다

&nbsp;

&nbsp;



