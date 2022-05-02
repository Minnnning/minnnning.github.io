---
layout: post
title: "데이트 피커"
date:   2022-01-22 15:35:47 +0900
categories:
tags: 시간데이터포멧

---

DateFormatter 클래스 사용

```swift
import UIKit

class ViewController: UIViewController {
    //let timeSelector: Selector = #selector(ViewController.updateTime)
    //let interval = 1.0
    //var count = 0

    @IBOutlet var lblNowTime: UILabel! //현재시간 출력을 위한 아울렛 변수
    @IBOutlet var lblSelectTime: UILabel! //선택시간을 보여주는 아울렛 변수
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        //Timer.scheduledTimer(timeInterval: interval, target: self, selector: timeSelector, userInfo: nil, repeats: true)
    }

    @IBAction func changDatePicker(_ sender: UIDatePicker) {
        let datePickerView = sender //데이트 피커에서 선택된 시간 저장
        let formatter = DateFormatter() // DateFormatter클래스의 상수 선언
        formatter.dateFormat = "yyyy-MM-dd HH:mm EEE"
        lblSelectTime.text = "select time: " + formatter.string(from: datePickerView.date)
    }

    @objc func updateTime() { //현재 시간을 보여주는 함수
        let date = NSDate() //현재시간을 상수에 넣음
        let formatter = DateFormatter() //DateFormatter클래스의 상수 선언
        formatter.dateFormat = "yyyy-MM-dd HH:mm EEE" //상수에 문자열을 넣음
        lblNowTime.text = "now time: " + formatter.string(from: date as Date)
    }

}
```

&nbsp;

DateFormatter 클래스를 사용 가능한가가 중요하다 이 클래스를 통해서 시간관련 데이터를 저장한다

<center>
<img src="https://user-images.githubusercontent.com/80758613/160331724-45c0f6d2-ba84-482c-963f-08341555aea1.png" style="zoom:40%;">
</center>

----

&nbsp;

### 시간 관련 데이터 포맷 형식

| 년(year)         | yy       | 16       | 뒤에 두자리만          |
| --------------- | -------- | -------- | ---------------- |
|                 | yyyy     | 2016     | 네자리로 표시          |
| **월(month)**    | M        | 5        | 한자리로 월 표시        |
|                 | MM       | 05       | 두자리로 월 표시        |
|                 | MMM      | Mar      | Jan~Dec          |
|                 | MMMM     | March    | January~December |
| **주(week)**     | w        | 6        | 1~52 연간 주순서      |
|                 | ww       | 09       | 01~52 연간 주순서     |
|                 | W        | 5        | 1~6 월간 주순서       |
| **일(day)**      | d        | 8        | 1~31             |
|                 | dd       | 08       | 01~31            |
|                 | D        | 10       | 1~366            |
|                 | DD       | 35       | 01~366           |
|                 | DDD      | 035      | 001~366          |
| **요일(weekday)** | E,EE,EEE | Mon      | Sun~Sat 3글자로 표시  |
|                 | EEEE     | Monday   | 요일 전체 표시         |
|                 | EEEEE    | M        | 한글자 약어표시         |
|                 | e        | 4        | 1~7주간 날짜 순서      |
|                 | ee       | 04       | 01~07주간 날짜 순서    |
| **시기(period)**  | a        | PM       | AM/PM            |
| **시간(hour)**    | h        | 3        | 1~12까지 시각표시      |
|                 | hh       | 03       | 01~12 시각을 표시     |
|                 | H        | 15       | 1~24 시각을 표시      |
|                 | HH       | 15       | 01~24 시각을 표시     |
| **분(minute)**   | m        | 3        | 1~59까지 분을 표시     |
|                 | mm       | 03       | 01~59까지 분을 표시    |
| **초(second)**   | s        | 33       | 1~59까지 초를 표시     |
|                 | ss       | 33       | 01~59까지 초를 표시    |
| **지역(zone)**    | z        | GMT+9:00 | 타임존 표시           |
|                 | Z        | +0900    | GMT 시간차 표시       |