---
layout: post
title: "Swift에서 Alamofire를 이용한 RESTAPI "
tags:  Alamofire 
image: "https://github.com/user-attachments/assets/8bc3e5fc-d28e-4847-b29b-144e40e78a5e"
---

Alamofire는 swift에서 REST API를 쉽게 하기위해 만들어진 패키지이다 pod으로 설치가 가능하고 add/package를 통해서 프로젝트에 다운 받을 수 있다

### Alamofire 패키지 설치

<center>
<img src="https://github.com/user-attachments/assets/07b25d53-da23-442b-97aa-fb5094e6f410" style="zoom:50%;">
</center>

add package를 선택

<center>
<img src="https://github.com/user-attachments/assets/8bc3e5fc-d28e-4847-b29b-144e40e78a5e" style="zoom:50%;">
</center>

오른쪽 주소 창에 패키지 github 주소 "https://github.com/Alamofire/Alamofire"를 넣고 패키지를 설치한다

&nbsp;

### GET 요청하기

``` swift
//
//  User.swift
//  restapi
//
//  Created by 김민정 on 10/28/24.
//


import Alamofire

struct User: Codable {
    let id: Int
    let name: String
    let email: String
}

class APIService {
    // 예시 API 엔드포인트 URL
    let baseURL = "https://jsonplaceholder.typicode.com"
    
    func fetchUsers(completion: @escaping (Result<[User], Error>) -> Void) {
        let url = "\(baseURL)/users"
        
        AF.request(url, method: .get).responseDecodable(of: [User].self) { response in
            switch response.result {
            case .success(let users):
                completion(.success(users))
            case .failure(let error):
                completion(.failure(error))
            }
        }
    }
}
```

위 코드에서 fetchUsers 함수는 /users 엔드포인트에서 JSON 데이터를 받아와서 User 구조체로 디코딩하는 함수이다

여기 함수 fetchUsers의 completion: 은 완료후 호출될 클로저 이 클로저는 호출 결과에 따라 성공 또는 실패를 처리한다 *여기서 @escaping은 하나의 속성으로 비동기적으로 만즐어준다고 한다*

`Result` 타입은 성공과 실패의 두 가지 상태를 나타낼 수 있다

- `.success([User])`: 성공적으로 데이터를 가져왔을 때의 상태로, `[User]` 배열을 반환
- `.failure(Error)`: 요청이 실패한 경우의 상태로, 오류를 반환

#### 위 함수를 호출하는 페이지

``` swift
import SwiftUI

struct ContentView: View {
    let apiService = APIService()
    var body: some View {
        VStack {
            Image(systemName: "iphone.homebutton.circle").onTapGesture {
                apiService.fetchUsers { result in
                    switch result {
                    case .success(let users):
                        print("Fetched Users: \(users)")
                    case .failure(let error):
                        print("Error: \(error)")
                    }
                }
            }
        }
        .padding()
    }
}

#Preview {
    ContentView()
}
```

이미지를 클릭하면 User.swift에서 작성한 함수가 호출되면서 유저 결과가 반환된다

#### 결과

``` json
Fetched Users: [Api.User(id: 1, name: "Leanne Graham", email: "Sincere@april.biz"), Api.User(id: 2, name: "Ervin Howell", email: "Shanna@melissa.tv"), Api.User(id: 3, name: "Clementine Bauch", email: "Nathan@yesenia.net"), Api.User(id: 4, name: "Patricia Lebsack", email: "Julianne.OConner@kory.org"), Api.User(id: 5, name: "Chelsey Dietrich", email: "Lucio_Hettinger@annie.ca"), Api.User(id: 6, name: "Mrs. Dennis Schulist", email: "Karley_Dach@jasper.info"), Api.User(id: 7, name: "Kurtis Weissnat", email: "Telly.Hoeger@billy.biz"), Api.User(id: 8, name: "Nicholas Runolfsdottir V", email: "Sherwood@rosamond.me"), Api.User(id: 9, name: "Glenna Reichert", email: "Chaim_McDermott@dana.io"), Api.User(id: 10, name: "Clementina DuBuque", email: "Rey.Padberg@karina.biz")]
```

&nbsp;

### POST 요청하기

``` swift
//
//  User.swift
//  Api
//
//  Created by 김민정 on 10/28/24.
//

import Alamofire

struct NewUser: Codable {
    let name: String
    let email: String
}

func createUser(newUser: NewUser, completion: @escaping (Result<User, Error>) -> Void) {
    let baseURL = "https://jsonplaceholder.typicode.com"
    let url = "\(baseURL)/users"
    
    AF.request(url, method: .post, parameters: newUser, encoder: JSONParameterEncoder.default).responseDecodable(of: User.self) { response in
        switch response.result {
        case .success(let user):
            completion(.success(user))
        case .failure(let error):
            completion(.failure(error))
        }
    }
}


```

함수를 정의 한다 get과 다른점은 AF.request를 실행할때 파라피터가 추가되고 인코더가 추가되었다

&nbsp;

#### 함수 호출하기

``` swift
//
//  ContentView.swift
//  Api
//
//  Created by 김민정 on 10/28/24.
//

import SwiftUI

struct ContentView: View {
    let apiService = APIService()
    let newUser = NewUser(name: "John Doe", email: "john.doe@example.com")
    var body: some View {
        VStack {
            Image(systemName: "rectangle.and.pencil.and.ellipsis").onTapGesture{
                createUser(newUser: newUser) { result in
                    switch result {
                    case .success(let user):
                        print("User Created: \(user)")
                    case .failure(let error):
                        print("Error: \(error)")
                    }
                }
            }
        }
        .padding()
    }
}

#Preview {
    ContentView()
}

```

&nbsp;

#### 결과

``` json
User Created: User(id: 11, name: "John Doe", email: "john.doe@example.com")
```

&nbsp;

보내고 받는 기본적인 방법을 알았지만 프로젝트 별로 사람 별로 방식이 다양하고 데이터를 

받을 때 다른 형식으로 받을 수 있기 때문에 많은 코드를 보는 것이 중요할것 같다
