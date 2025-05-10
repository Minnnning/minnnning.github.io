---
layout: post
title: "이미지 뷰어"
date:   2022-01-21 15:35:47 +0900
categories:
tags: 
image: "https://user-images.githubusercontent.com/80758613/160329768-a5ec994c-4832-433a-adb4-8d6dedc1599c.png"
---

아울렛 변수외 그냥 변수를 사용 초깃값 지정 버튼 이름 바꾸기와 스위치 상태에 따를 값 변경

&nbsp;

```swift
import UIKit

class ViewController: UIViewController {
    var isZoom = false //전구의 확대 여부를 나타냄
    var imgOn: UIImage? // 이미지 타입은 UIImage
    var imgOff: UIImage? //사진이 없을 수 있으므로 옵셔널로 선언한다

    @IBOutlet var imgView: UIImageView! // 보여주는 이미지
    @IBOutlet var btnResize: UIButton! //버튼 이름 바꾸기위한 아울렛 변수

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        imgOn = UIImage(named: "lamp_on.png") // 위 변수에 값(이미지)를 넣는다 초깃값 지정
        imgOff = UIImage(named: "lamp_off.png")
        
        imgView.image = imgOn //아울렛 변수에 초기값을 넣음
    }
    @IBAction func btnResizeImage(_ sender: UIButton) { //버튼의 액션함수
        let scale: CGFloat = 2.0 //확대 축소 배율
        var newWidth: CGFloat, newHeight:CGFloat //가로 세로 길이를 저장할 변수
        
        if (isZoom) { //상태 true 확대된 상태일 때
            newWidth = imgView.frame.width/scale //가로 2로 나눔
            newHeight = imgView.frame.height/scale //세로 2로 나눔
            
            btnResize.setTitle("확대", for: .normal) //버튼 이름 바꿈
        }
        else { //상태 false인 상태 축소인 상태
            newWidth = imgView.frame.width*scale
            newHeight = imgView.frame.height*scale
            
            btnResize.setTitle("축소", for: .normal)
        }
        imgView.frame.size = CGSize(width: newWidth, height: newHeight)//변경한 값을 이미지에
        isZoom = !isZoom // 값을 반전 시킨다
    }

    @IBAction func switchlmageOnOff(_ sender: UISwitch) {
        if sender.isOn { //스위치가 켜져 있을때 
            imgView.image = imgOn //보여지는 이미지
        }
        else {
            imgView.image = imgOff
        }
    }
    
}
```

&nbsp;

위에서 imgOn = UIImage(named: "lamp_on.png") 

​            imgOff = UIImage(named: "lamp_off.png")

​            imgView.image = imgOn  코드가 들어 있는 **viewDidLoad 함수**는 내가 만든 뷰룰 실행 했을때 호출되는 함수로 뷰가 불러지고 실행하고자 하는 기능이 있을 때 viewDidLoad함수 안에 입력하면 된다

<center>
<img src="https://user-images.githubusercontent.com/80758613/160329768-a5ec994c-4832-433a-adb4-8d6dedc1599c.png" style="zoom:30%;">
</center>

----

&nbsp;

&nbsp;

<center>
<img src="https://user-images.githubusercontent.com/80758613/160329838-1d9549a8-8602-412f-8c75-8e10f039e548.png" style="zoom:50%;" align="left">
</center>

이미지 뷰어에는 콘탠트 모드가 옆 사진과 같이 종류가 매우 많다

1.scale to fill

뷰어의 크기에 맞게 사진의 크기가 변경된다 비율이 안맞을 수도 있어서 찌그러져 보일 수 있다

2.aspect fit

가로 세로 비율은 유지하면서 이미지 뷰어에 맞게 조정 된다

3.aspect fll

이미지 비율을 유지하면서 이미지 뷰를 채운다 이미지하고 뷰어하고 비율이 맞지 않으면 이미지가 잘릴 수도 있다

4.center

이미지이 원본 크기를 유지한 채 이미지 중앙부분을 출력

top, top left ···· 나머지 모두 center와 비슷함