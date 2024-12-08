---
layout: post
title: "URLSession과 Alamofire 비교하기"
tags:  urlsession alamofire
excerpt_image: "https://github.com/user-attachments/assets/73dada4f-d9ac-498c-87fa-b9d49ee19edd"
---

 데이터를 주고 받기 위해  api를 이용한다 이때 SwiftUI에서는 기본으로 제공하는 URLSession과 패키지를 깔아서 사용하는 Alamofire가 있다 프로젝트 도중 2개를 혼용해서 사용했는데 각각의 장점과 단점을 알아볼려고 한다

&nbsp;

### URLSession

URLSession는 애플에서 기본적으로 제공하는 네트워크 라이브러리이다 별도의 패키지 없이 바로 사용할 수 있고 애플에서 직접 제공하는것이기 때문에 최신 네트워크 기술을 완전히 지원(네이티브의 장점)한다

하지만 Alamofire에 비해서 코드가 길어지거나 기본 기능(JSON 처리)등이 부족하다

``` swift
import SwiftUI

struct ContentView: View {
    @State private var data: String = "Loading..."
    @State private var isLoading: Bool = true
    @State private var num: Int = 1
    
    var body: some View {
        NavigationStack {
            VStack {
                if isLoading {
                    ProgressView("Fetching Data...")
                        .progressViewStyle(CircularProgressViewStyle())
                } else {
                    Text(data)
                        .font(.headline)
                        .padding()
                }
                
                Button(action: {
                    Task {
                        await fetchData()
                    }
                }) {
                    Text("Load Data")
                        .padding()
                        .background(Color.blue)
                        .foregroundColor(.white)
                        .cornerRadius(8)
                }
                NavigationLink(destination: AlamoView()){
                    Text("Alamofire")
                        .padding()
                        .background(Color.blue)
                        .foregroundColor(.white)
                        .cornerRadius(8)
                }
            }
            .padding()
        }
    }
    
    func fetchData() async {
        isLoading = true
        defer {
            isLoading = false
            num += 1
        } // 현재 함수 또는 스코프가 종료될때 실행된다
        
        guard let url = URL(string: "https://jsonplaceholder.typicode.com/posts/\(num)") else {
            data = "Invalid URL"
            return
        }
        
        do {
            let (responseData, _) = try await URLSession.shared.data(from: url)
            if let json = try? JSONSerialization.jsonObject(with: responseData, options: []) as? [String: Any],
               let title = json["title"] as? String {
                data = title
                print(json)
            } else {
                data = "Failed to parse data"
            }
        } catch {
            data = "Error: \(error.localizedDescription)"
        }
    }
}

#Preview {
    ContentView()
}
```

<center>
<img src="https://github.com/user-attachments/assets/73dada4f-d9ac-498c-87fa-b9d49ee19edd" style="zoom:50%;">
</center>

load data를 눌러서 URLSession을 통해서 데이터를 가져오는 코드이다 async/ await를 사용해서 비동기 기능을 구현했다

&nbsp;

### Alamofire

Alamofire는 URLSession기반으로 만들어진 오픈소스 네트워킹 라이브러리이다 기존 URLSession에 편의성을 극대화하기 위해서 만들어졌다 간단한 문법과 추가적인 기능을 제공한다

하지만 외부 라이브러리에 종속하기 때문에 프로젝트 크기가 증가할 수 있다

File -> Add Package Dependencies를 누르고 Alamofire를 검색해 패키지를 추가한다

``` swift
import SwiftUI
import Alamofire

struct AlamoView: View {
    @State private var data: String = "Loading..."
    @State private var isLoading: Bool = true
    @State private var num: Int = 1
    
    var body: some View {
        VStack {
            if isLoading {
                ProgressView("Fetching Data...")
                    .progressViewStyle(CircularProgressViewStyle())
            } else {
                Text(data)
                    .font(.headline)
                    .padding()
            }
            
            Button(action: {
                    fetchData()
            }) {
                Text("Load Data")
                    .padding()
                    .background(Color.blue)
                    .foregroundColor(.white)
                    .cornerRadius(8)
            }
        }
        .padding()
    }
    
    func fetchData() {
        isLoading = true
        defer {
            isLoading = false
            num += 1
        } // 현재 함수 또는 스코프가 종료될때 실행된다
        
        AF.request("https://jsonplaceholder.typicode.com/posts/\(num)").responseJSON { response in
            switch response.result {
            case .success(let data):
                let json = String(describing: data)
                self.data = json
            case .failure(let error):
                print("Error: \(error.localizedDescription)")
            }
        }
    }
}

#Preview {
    AlamoView()
}
```

<center>
<img src="https://github.com/user-attachments/assets/68fcbd1d-f8f5-4cb2-bd02-cda4541523ab" style="zoom:50%;">
</center>

출력할때 형식 차이가 존재 하지만 fetchData 코드가 확실히 줄어들고 보기 편해졌다

&nbsp;

URLSession을 사용할때는 async/await를 사용했지만 Alamofire는 그렇지 않다

그 이유는 Alamofire가 **클로저 기반의 비동기 작업 처리**를  제공하기 때문이다 클로저 기반 비동기 작업 처리란 비동기 작업이 완료되었을 때 실행될 코드 블록(클로저)을 미리 정의한 후 작업이 끝난 후 호출하는 방식을 의미한다

 [클로저 설명](https://minnnning.github.io/swift/2024/07/02/스위프트-함수-메서드-클로저.html)

