---
layout: post
title: "플러터에서 Provider와 Service 비교"
tags:  flutter Provider Service
---

Flutter로 앱을 개발할때 provider와 service의 역할이 헷갈렸기 때문에 각각에 대해서 정리하고 비교한다

- **Provider**는 주로 **상태 관리**와 **UI 업데이트**에 초점 
- **Service**는 **비즈니스 로직**, **데이터 처리**, **네트워크/API 통신** 등 UI와 직접 관계가 없는 작업

&nbsp;

### Provider의 정의 및 역할

**정의**: Provider는 Flutter의 상태 관리 솔루션으로, `ChangeNotifier`를 확장하여 앱의 상태를 중앙에서 관리하고, 해당 상태 변화에 따라 UI를 자동으로 업데이트한다.

**주요 역할**:

- 애플리케이션의 상태(데이터)를 저장 및 관리
- UI 위젯에 상태를 공급하여, 상태 변화 시 자동으로 리렌더링
- 의존성 주입(DI)을 통해 앱의 여러 부분에서 동일한 상태에 접근 가능

**사용:**

- 사용자 정보, 테마 설정, 네비게이션 상태 등 전역 상태 관리
- 특정 페이지나 위젯 트리 내에서 로컬 상태 관리

&nbsp;

&nbsp;

### Service의 정의 및 역할

**정의**: Service는 애플리케이션의 비즈니스 로직, 데이터 처리, 네트워크/API 호출 등 UI와 직접 관련 없는 작업을 수행하는 모듈 또는 클래스

**주요 역할**:

- 외부 API 통신 및 데이터베이스 접근
- 데이터 전처리, 캐싱, 비즈니스 로직 실행
- 서비스 로직을 캡슐화하여 UI와 분리

**사용:**

- 로그인, 토큰 갱신, API 데이터 요청 등 외부 시스템과의 통신
- 데이터 가공 및 비즈니스 로직 처리
- 로컬 데이터 저장 및 캐싱

&nbsp;

&nbsp;

### 역할 분리의 필요성

- **책임 분리(Single Responsibility Principle)**:  
  - **Provider**: 앱 상태 및 UI 관련 데이터를 관리  
  - **Service**: 데이터 처리 및 외부 API 통신 로직을 담당
- **유지보수성**:  
  - 서비스 로직 변경 시 UI 코드를 수정할 필요 없이, Service 파일만 업데이트하면 됨.
  - Provider는 UI 상태 관리에 집중하여, 각 컴포넌트의 역할이 명확해짐

&nbsp;

### 상호 작용 방식

1. **Service → Provider**:  
   - Service에서 API 호출을 수행하고, 그 결과 데이터를 Provider에 전달하여 상태를 업데이트
   - 예를 들어, `AuthService`는 로그인 API를 호출하고, 로그인 성공 시 받은 유저 정보를 `UserProvider`에 저장 (내가 만든 flutter앱의 예시)

2. **Provider → Service**:  
   - Provider는 필요 시 Service의 메서드를 호출하여 데이터를 가져오거나, 토큰 갱신 등 추가 작업을 수행
   - 예를 들어, `UserProvider`의 `fetchUser()` 메서드는 API 호출 전에 Service를 통해 토큰을 갱신(내가 만든 flutter앱의 예시)



``` dart
//service
import 'package:kakao_flutter_sdk/kakao_flutter_sdk_user.dart';
import 'package:flutter_secure_storage/flutter_secure_storage.dart';
import 'package:dio/dio.dart';
import '../models/user_model.dart';
import '../providers/user_provider.dart';

class AuthService {
  final Dio _dio = Dio();
  final FlutterSecureStorage _secureStorage = const FlutterSecureStorage();

  Future<void> login(UserProvider userProvider) async {
    try {
      OAuthToken token;
      if (await isKakaoTalkInstalled()) {
        token = await UserApi.instance.loginWithKakaoTalk();
      } else {
        token = await UserApi.instance.loginWithKakaoAccount();
      }
      print('카카오 로그인 성공: ${token.accessToken}');

      final response = await _dio.post(
        'https://api.mapping.kro.kr/api/v2/member/login',
        options: Options(
          headers: {'accept': '*/*', 'Content-Type': 'application/json'},
        ),
        data: {'accessToken': token.accessToken},
      );

      if (response.statusCode == 200 && response.data['success']) {
        final data = response.data['data'];
        String newAccessToken = data['tokens']['accessToken'];
        String refreshToken = data['tokens']['refreshToken'];

        await _secureStorage.write(key: 'accessToken', value: newAccessToken);
        await _secureStorage.write(key: 'refreshToken', value: refreshToken);

        print('서버 로그인 성공, 토큰 저장 완료');
        await userProvider.fetchUser();
      } else {
        print('서버 로그인 실패: ${response.data}');
      }
    } catch (error) {
      print('카카오 로그인 실패: $error');
    }
  }

  Future<void> logout(UserProvider userProvider) async {
    try {
      await _secureStorage.delete(key: 'accessToken');
      await _secureStorage.delete(key: 'refreshToken');
      userProvider.clearUser();
      print('로그아웃 완료');
    } catch (error) {
      print('로그아웃 실패: $error');
    }
  }
}
```

&nbsp;

``` dart
//provider
import 'package:flutter/material.dart';
import 'package:flutter_secure_storage/flutter_secure_storage.dart';
import 'package:dio/dio.dart';
import '../models/user_model.dart';

class UserProvider with ChangeNotifier {
  UserModel? _user;
  final Dio _dio = Dio();
  final FlutterSecureStorage _secureStorage = const FlutterSecureStorage();

  UserModel? get user => _user;

  void setUser(UserModel user) {
    _user = user;
    notifyListeners();
  }

  void clearUser() {
    _user = null;
    notifyListeners();
  }
}

```

