---
layout: post
title: "Swift에서 url 이미지 출력"
tags:  swiftui AsyncImage
---

프로젝트 도중 이미지를 서버에서 받는데 aws를 사용하고 있어서 버킷을 이용하다 보니 url된 이미지를 표시해야 했다 애플에서는 이럴 때 사용할 수 있는 AsyncImage를 제공한다

### AsyncImage

사용법은 매우 간단하다

``` swift
AsyncImage(url: URL(string: "https://example.com/icon.png")) { phase in
    if let image = phase.image {
        image // Displays the loaded image.
    } else if phase.error != nil {
        Color.red // Indicates an error.
    } else {
        Color.blue // Acts as a placeholder.
    }
}
```

위 코드를 보면 Url을 이용해서 함수를 실행하고 결과를 phase에 받는다

정상적으로 이미지를 받아 오면 이미지를 표시하고 에러라면 빨강 그 이외의 문제라면 파란색을 표시한다

``` swift
AsyncImage(url: URL(string: "https://example.com/icon.png")) { image in
    image.resizable()
} placeholder: {
    ProgressView()
}
.frame(width: 50, height: 50)
```

이렇게 작성하다면 로딩중 프로그래스 뷰를 출력한다

``` swift
AsyncImage(url: url) { phase in
    switch phase {
    case .empty:
        ProgressView() // 로딩 중 표시
    case .success(let image):
        image
            .resizable()
            .scaledToFill()
            .frame(width: 50, height: 50)
            .clipShape(Circle())
    case .failure:
        Image(systemName: "person.circle.fill") // 실패 시 기본 아이콘 표시
            .resizable()
            .scaledToFit()
            .frame(width: 50)
            .foregroundStyle(Color.skyBlue)
            .background(Circle().fill(Color.black))
    @unknown default:
        EmptyView()
    }
}
```

프로젝트에서 사용한 코드는 switch를 사용할뿐 로직은 똑같다 아직 정보를 못받았다면 프로그래스 뷰, 성공시 이미지, 실패시 시스템 이미지를 표시한다
