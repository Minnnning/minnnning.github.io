---
title: 같은 AMI인데 왜 staging만 CodeDeploy 배포가 실패할까
date: 2026-07-05 21:00:00 +0900
categories: [AWS, 트러블슈팅]
tags: [codedeploy, ec2, ami]
---

운영 중인 서비스에서 CodeDeploy 배포가 이상하게 실패하는 케이스를 만났다. prod 배포는 잘 되는데 staging 배포만 파일 충돌로 실패했다. 문제는 두 환경이 **완전히 같은 AMI**를 쓰고 있었다는 거다. 같은 이미지에서 뜬 인스턴스인데 한쪽은 되고 한쪽은 안 되니까 처음에는 뭘 의심해야 할지도 몰랐다

에러는 이런 식이었다

```
The deployment failed because a specified file already exists at this location: /var/www/.../xxx
```

CodeDeploy의 file_exists_behavior가 기본값(DISALLOW)이면 배포하려는 위치에 파일이 이미 있을 때 실패하는 건 알고 있었다. 근데 그러면 prod도 같이 실패해야 하는 거 아닌가? 같은 AMI니까 파일도 똑같이 들어있을 텐데

## 처음 의심한 것들

일단 AMI를 의심했다. 이 AMI는 base 이미지가 아니라 운영 중이던 prod 인스턴스의 스냅샷으로 만든 거였다. 그러니까 애플리케이션 파일이 이미지에 통째로 구워져(baked-in) 있는 상태다. 그래서 "스냅샷 AMI라서 파일이 있고, 그래서 충돌하나?" 했는데 이걸로는 prod가 성공하는 게 설명이 안 됐다

다음으로 블루/그린 배포 방식 차이인가 싶어서 배포 설정도 비교했는데 여기도 답이 없었다

## 진짜 원인은 deployment group이었다

CodeDeploy 에이전트의 동작을 파고들다가 알게 된 게 있는데, 에이전트는 배포가 끝나면 인스턴스 안에 **cleanup 파일**이라는 걸 남긴다. 경로는 대략 이렇다

```
/opt/codedeploy-agent/deployment-root/deployment-instructions/{deployment-group-id}-cleanup
```

이 파일에는 "직전 배포에서 내가 설치한 파일 목록"이 들어있고, 다음 배포의 Install 단계 전에 에이전트가 이 목록에 있는 파일들을 먼저 지운다. 그래서 같은 위치에 파일이 있어도 "내가 지난번에 깔았던 파일"이면 충돌이 안 나는 거다

여기서 중요한 게 파일명에 박혀있는 **deployment group ID**다. cleanup 파일은 배포 그룹 단위로 관리된다

이제 퍼즐이 맞춰진다

- 이 AMI는 prod 인스턴스 스냅샷이라서, **prod 배포 그룹의 cleanup 파일까지 같이 구워져 있었다**
- prod에 배포하면: 에이전트가 자기 그룹 ID의 cleanup 파일을 찾음 → 있음 → baked-in 파일들을 "지난 배포 산출물"로 인식하고 지운 뒤 설치 → 성공
- staging에 배포하면: staging 그룹 ID의 cleanup 파일을 찾음 → **없음** (AMI에 든 건 prod 그룹 것) → baked-in 파일들이 "출처를 모르는 이미 존재하는 파일"이 됨 → DISALLOW 충돌 → 실패

즉 변수는 AMI도 블루/그린도 아니고 배포 그룹이었다. 같은 AMI라도 어느 배포 그룹으로 배포하느냐에 따라 결과가 갈린 거다

## 해결

당장은 file_exists_behavior를 OVERWRITE로 바꾸거나 충돌 파일을 지우고 재배포하면 넘어갈 수 있다. 근데 정석은 **배포용 Launch Template에 운영 스냅샷 AMI를 쓰지 않는 것**이다. 애플리케이션 파일이 안 들어있는 배포 전용 base AMI를 만들어서 쓰면 cleanup 파일이고 뭐고 애초에 충돌할 파일 자체가 이미지에 없다

운영 인스턴스 스냅샷으로 AMI를 만들어 쓰는 건 급할 때 편해서 유혹적인데, 이번 건으로 확실히 배웠다. 스냅샷에는 애플리케이션 파일만 담기는 게 아니라 CodeDeploy 에이전트의 상태(deployment-root 전체)까지 같이 담긴다. 이 상태가 특정 배포 그룹에 종속되어 있어서 다른 그룹에서 쓰는 순간 어긋난다

## 정리

- CodeDeploy는 Install 전에 `{배포그룹ID}-cleanup` 파일 목록의 파일을 지우고 시작한다. 충돌 판정은 이 파일 기준이다
- 운영 스냅샷 AMI에는 이 cleanup 파일까지 들어가고, 배포 그룹이 다르면 무용지물이라 파일 충돌이 난다
- 배포용 AMI는 애플리케이션이 안 담긴 base 이미지로 분리하자

비슷하게 "같은 이미지인데 환경마다 배포 결과가 다른" 상황이라면 인스턴스 들어가서 deployment-instructions 폴더에 어느 그룹의 cleanup 파일이 있는지부터 확인해보는 걸 추천한다
