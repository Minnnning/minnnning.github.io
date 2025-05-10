---
layout: post
title: "Hello World! 출력"
date:   2022-01-21 14:35:47 +0900
categories:
tags: 아울렛변수 액션함수
image: "https://user-images.githubusercontent.com/80758613/160329169-7d79b712-7191-493b-8183-cfe4f148c548.png"
excerpt: "변수와 비슷한 개념으로 특정 동작을 수행하기 위한 정보를 저장하는 곳 여기서는 입력한 텍스트를 저장하고 출력할 아울렛 변수에 넣는다"
---

## **아울렛 변수**

변수와 비슷한 개념으로 특정 동작을 수행하기 위한 정보를 저장하는 곳 여기서는 입력한 텍스트를 저장하고 출력할 아울렛 변수에 넣는다

주로 클래스 선언부 바로 아래에 추가한다

&nbsp;

&nbsp;

## **액션 함수**

특정 동작을 수행하는 함수 

여기서는 버튼을 액션함수로 지정하고 그안에 버튼을 누르면 실행할 동작을 작성한다

일반적으로 클래스 제일 마지막에 추가한다

&nbsp;



```swift
import UIKit

class ViewController: UIViewController {
    
    @IBOutlet var txtName: UITextField!  // 아울렛 변수
    @IBOutlet var lblHello: UILabel!     // UILabel 타입이 따로 있나보다 
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
    }

    @IBAction func btnSend(_ sender: UIButton) { //액션 함수
        lblHello.text = "Hello, " + txtName.text! //버튼 누를시 실행되는 함수 
    }
    
}
```

<center>
<img src="https://user-images.githubusercontent.com/80758613/160329169-7d79b712-7191-493b-8183-cfe4f148c548.png" style="zoom:30%;">
</center>

위 사진에서 원래 hello 밖에 없지만 텍스트 박스에 minnnning 추가하고 버튼을 누르면 위 사진 처럼 나온다