---
layout: post
title: "같은 AMI인데 왜 staging만 CodeDeploy 배포가 실패했을까?"
date: 2026-07-08 00:00:00 +0900
categories: [MSP일지]
tags: [codedeploy, ec2, ami]
description: "같은 AMI인데 prod는 되고 staging만 CodeDeploy 배포가 실패한 사건을 분석하며 배운 것."
Image: "https://image.minnnning.kr/images/2026/07/20260708-002654-5adb78.webp"
---

이번 건은 제가 직접 삽질한 건 아니고, 같이 일하는 인턴분이 겪은 CodeDeploy 배포 실패를 옆에서 같이 들여다보다가 알게 된 내용입니다. 뭐 코드 디플로이 롤링이라 삭제는 안되고 스테이지 서버라 서비스에 문제는 없어서 다행이었습니다.

상황은 이랬습니다. 운영 중인 서비스에서 prod 배포는 멀쩡히 되는데 staging 배포만 파일 충돌로 실패한다는 겁니다. 근데 두 환경이 **완전히 같은 AMI**를 쓰고 있었어요. 같은 이미지에서 뜬 인스턴스인데 한쪽은 되고 한쪽은 안 되니, 이거 뭐부터 의심해야 하나 싶더라고요.

에러는 이랬습니다.

```
The deployment failed because a specified file already exists at this location: /var/www/.../xxx
```

CodeDeploy의 `file_exists_behavior`가 기본값(DISALLOW)이면 배포 위치에 파일이 이미 있을 때 실패하는 건 알고 있었습니다. 근데 그러면 prod도 같이 실패해야 하는 거 아닌가? 같은 AMI니까 파일도 똑같이 들어있을 텐데 말이죠.

## 처음엔 AMI를 의심했다

이 AMI가 base 이미지가 아니라 운영 중이던 prod 인스턴스의 스냅샷으로 만든 거였습니다. 그러니까 애플리케이션 파일 appspec.yml 이 이미지에 통째로 구워져 있는 상태인 거죠. "아, 스냅샷 AMI라서 파일이 이미 있고, 그래서 충돌하는구나" 싶었는데... 이걸로는 prod가 성공하는 이유가 설명이 안 됩니다. prod도 똑같이 파일이 구워져 있을 텐데요.

블루/그린 배포 방식 차이인가 싶어서 배포 설정도 비교해봤는데 여기서도 답이 안 나왔습니다.

## 진짜 원인은 deployment group이었다

CodeDeploy 에이전트 동작을 파고들다가 알게 된 게 있는데, 에이전트는 배포가 끝나면 인스턴스 안에 **cleanup 파일**이라는 걸 남긴다고 합니다. 경로는 대략 이렇습니다.

```
/opt/codedeploy-agent/deployment-root/deployment-instructions/{deployment-group-id}-cleanup
```

이 파일 안에 "직전 배포에서 내가 설치한 파일 목록"이 들어있고, 다음 배포의 Install 단계 전에 에이전트가 이 목록에 있는 파일들을 먼저 지웁니다. 그래서 같은 위치에 파일이 있어도 "내가 지난번에 깔았던 파일"이면 충돌이 안 나는 거였어요.

여기서 핵심이 파일명에 박혀있는 **deployment group ID**입니다. cleanup 파일은 배포 그룹 단위로 관리되더라고요.

- 이 AMI는 prod 인스턴스 스냅샷이라, **prod 배포 그룹의 cleanup 파일까지 같이 구워져 있었던** 겁니다
- prod에 배포하면: 에이전트가 자기 그룹 ID의 cleanup 파일을 찾음 → 있음 → 파일들을 "지난 배포 산출물"로 인식하고 지운 뒤 설치 → 성공
- staging에 배포하면: staging 그룹 ID의 cleanup 파일을 찾음 → **없음** (AMI에 든 건 prod 그룹 것이니까요) → 파일들이 "출처를 모르는, 이미 존재하는 파일"이 됨 → DISALLOW 충돌 → 실패

즉 변수는 AMI도 블루/그린도 아니고 **배포 그룹**이었습니다. 같은 AMI라도 어느 배포 그룹으로 배포하느냐에 따라 결과가 갈린 거죠. 알고 나니 허무할 정도로 명확한데, 모를 땐 진짜 안 보이는 종류의 문제였습니다.

## 해결, 그리고 진짜 결론

당장 급하면 `file_exists_behavior`를 OVERWRITE로 바꾸거나 충돌 파일을 지우고 재배포하면 넘어갈 수는 있습니다. 근데 이건 임시방편이고, 정석은 **배포용 Launch Template에 운영 스냅샷 AMI를 쓰지 않는 것**입니다. 애플리케이션 파일이 안 들어있는 배포 전용 base AMI를 쓰면 cleanup 파일이고 뭐고 애초에 충돌할 파일 자체가 이미지에 없으니까요.

운영 인스턴스 스냅샷으로 AMI를 만들어 쓰는 게 급할 때는 정말 편해서 유혹적인데, 이번 건으로 확실히 배웠습니다. 스냅샷에는 애플리케이션 파일만 담기는 게 아니라 CodeDeploy 에이전트의 상태(deployment-root 전체)까지 같이 담긴다는 것, 그리고 그 상태가 특정 배포 그룹에 종속돼 있어서 다른 그룹에서 쓰는 순간 어긋난다는 것까지요.

**결론은 하나입니다. 배포용 이미지는 무조건 클린한 이미지에서 만들어야 한다는 것.** 당장 편하자고 운영 스냅샷을 재활용하면, 눈에 안 보이는 상태값(cleanup 파일, 에이전트 로그, 각종 캐시)까지 같이 딸려 와서 나중에 이런 식으로 발목을 잡습니다.

다만, MSP 업무를 하다보면 고객이 기존에 ami에 어떤 프로그램을 깔고 사용했는지 모르니까 ami를 섣불리 생성할 수도 없었습니다.
이런 부분이 업무를 진행하면서 답답 하더라고요. 정보가 없는 상황에서 근거를 찾아서 해야한다... 고객에게 물어보면 빠르지만 답장이 느리고 고객도 모르는 경우가 많습니다. 허허 

## 정리

- CodeDeploy는 Install 전에 `{배포그룹ID}-cleanup` 파일 목록의 파일을 지우고 시작합니다. 충돌 판정은 이 파일 기준입니다
- 운영 스냅샷 AMI에는 이 cleanup 파일까지 들어가고, 배포 그룹이 다르면 무용지물이라 파일 충돌이 납니다
- 배포용 AMI는 애플리케이션이 안 담긴 base 이미지로 반드시 분리하자

비슷하게 "같은 이미지인데 환경마다 배포 결과가 다르다"는 상황을 만나면, 인스턴스에 직접 들어가서 `deployment-instructions` 폴더에 어느 그룹의 cleanup 파일이 있는지부터 확인해보시는 걸 추천드립니다. 저도 옆에서 지켜보며 배운 케이스라 언젠가 제가 똑같은 실수를 할 수도 있겠다 싶어서, 잊지 않으려고 기록해둡니다.



## 참고

[Troubleshoot EC2/On-Premises deployment issues - AWS CodeDeploy](https://docs.aws.amazon.com/codedeploy/latest/userguide/troubleshooting-deployments.html)
