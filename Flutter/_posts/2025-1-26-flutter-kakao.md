---
layout: post
title: "플러터에서 카카오 로그인 구현"
tags:  flutter kakaologin
excerpt_image: "https://github.com/user-attachments/assets/14025bcc-6446-4f54-aad1-19b1b425bc28"
---

프로젝트에서 카카오 로그인 기능이 필요하기 때문에 카카오 로그인을 플러터에서 구현한다 카카오 로그인후 토큰까지 확인해본다

### 1. 플러그인 설치와 앱키 설정

사용할 플러그인은 `kakao_flutter_sdk`와 `flutter_dotenv`이다 fluter pub add 명령어를 통해서 설치한다

카카오앱은 등록은 생략하고 **네이티브 앱키를 이용해야한다** 앱키는 노출하면 안되므로 .env파일을 root에 생성한다 생성한 파일에 키를 작성한다

``` env
KAKAO_NATIVE_APP_KEY=키입력
```

이제 앱키를 .env에서 가져와 사용하면 된다

``` dart
// main.dart
import 'package:kakao_flutter_sdk/kakao_flutter_sdk_common.dart';

void main() async {
  await dotenv.load(fileName: '.env');

  KakaoSdk.init(
    nativeAppKey: dotenv.get('KAKAO_NATIVE_APP_KEY'),
  );

  runApp(MyApp());
}
```

또한 파일을 추가한것을 pubspec.yaml에 명시한다

``` yaml
flutter:
  uses-material-design: true
  assets:   # 아래를 추가한다
    - .env 
```

위설정을 하고 나면 프로젝트가 정상적으로 실행되어야한다 *카카오 로그인은 Flutter의 minSdkVersion이 21보다 낮을 경우 문제가 발생할 수 있다*

&nbsp;

### 2.  xml 파일 수정

Project/android/app/src/AndroidManifest.xml에 위치한 파일을 수정한다

``` xml
<uses-permission android:name="android.permission.INTERNET" />
```

인터넷 사용권한 허용

#### 커스텀 URL

커스텀 URL 스킴(Custom URL Scheme)은 사용자가 Android와 iOS 환경에서 카카오톡으로 로그인 후 서비스 앱으로 돌아오거나, 카카오톡 메시지 버튼 또는 링크로 서비스의 앱을 실행하는 데 사용한다

위 xml과 같은 파일을 수정한다

``` xml
        <!-- 카카오 로그인 커스텀 URL 스킴 설정 -->
        <activity 
            android:name="com.kakao.sdk.flutter.AuthCodeCustomTabsActivity"
            android:exported="true">
            <intent-filter android:label="flutter_web_auth">
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />

                <!-- "kakao${YOUR_NATIVE_APP_KEY}://oauth" 형식의 앱 실행 스킴 설정 -->
                <!-- 카카오 로그인 Redirect URI -->
                <data android:scheme="kakao${KAKAO_NATIVE_APP_KEY}" android:host="oauth"/>
            </intent-filter>
        </activity>
```

이제 xml에 사용한 변수를 정의해줘야한다

android 내부 **local.properties**에 KAKAO_NATIVE_APP_KEY를 정의한다

정의된 키를 사용하기 위해서 android/app/src/build.gradle을 수정

``` gradle
def localProperties = new Properties() //추가
localProperties.load(new FileInputStream(rootProject.file("local.properties"))) //추가

android {
    namespace = "com.example.mapping_flutter"
    compileSdk = flutter.compileSdkVersion
    ndkVersion = flutter.ndkVersion

    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_1_8
        targetCompatibility = JavaVersion.VERSION_1_8
    }

    kotlinOptions {
        jvmTarget = JavaVersion.VERSION_1_8
    }

    defaultConfig {
        applicationId = "com.example.mapping_flutter"
        manifestPlaceholders["KAKAO_NATIVE_APP_KEY"] = localProperties.getProperty("KAKAO_NATIVE_APP_KEY") //추가 부분
        minSdk = 21
        targetSdk = flutter.targetSdkVersion
        versionCode = flutter.versionCode
        versionName = flutter.versionName
    }
```

&nbsp;

### 3. 앱 등록

<a href="https://developers.kakao.com" target="_blank">카카오 디벨롭</a>에 들어가서 내 애플리케이션 > 앱설정 > 플랫폼으로 들어간다

<center>
<img src="https://github.com/user-attachments/assets/800a242d-f0da-4e4e-9e40-2bd249a2f9c0" style="zoom:30%;">
</center>

패키지명 -> 앱id와 키 해시를 입력한다 

키 해시는 아래의 명령어로 찾을 수 있다 하지만 아래로 얻은 해시값으로 로그인이 안되었다 *키 오류 발생*

``` cmd
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

main.dart에서 키값을 추출할 수 있었다

&nbsp;

### 4. main.dart

이제 카카오톡 로그인을 실행하기 위해서 위젯을 생성한다

``` dart
import 'package:flutter/material.dart';
import 'package:kakao_flutter_sdk_user/kakao_flutter_sdk_user.dart';
import 'package:kakao_flutter_sdk/kakao_flutter_sdk_common.dart';
import 'package:flutter_dotenv/flutter_dotenv.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await dotenv.load(fileName: '.env');

  KakaoSdk.init(nativeAppKey: dotenv.get('KAKAO_NATIVE_APP_KEY'));

  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Kakao Login Demo',
      home: const MyHomePage(title: 'Kakao Login'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  const MyHomePage({super.key, required this.title});

  final String title;

  @override
  State<MyHomePage> createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  String? _userNickname;
  String? _userProfileImage;

  Future<void> _loginWithKakao() async {
    try {
      if (await isKakaoTalkInstalled()) { //앱 존재하는 경우
        OAuthToken token = await UserApi.instance.loginWithKakaoTalk();
        print('카카오톡으로 로그인 성공 ${token.accessToken}');
      } else { //앱이 존재하지 않는 경우
        OAuthToken token = await UserApi.instance.loginWithKakaoAccount();
        print('카카오톡으로 로그인 성공 ${token.accessToken}');
      }
      _fetchUserInfo(); //유저정보를 가져옴
    } catch (error) {
      print(await KakaoSdk.origin);
      print('Login failed: $error');
    }
  }

  Future<void> _fetchUserInfo() async {
    try {
      User user = await UserApi.instance.me();
      setState(() {
        print(user); //유저정보를 모두 보기 위함
        _userNickname = user.kakaoAccount?.profile?.nickname;//닉네임, 이미지만 가져옴
        _userProfileImage = user.kakaoAccount?.profile?.thumbnailImageUrl;
      });
    } catch (error) {
      print('Failed to get user info: $error');
    }
  }

  Future<void> _logout() async {
    try {
      await UserApi.instance.logout();
      setState(() {
        _userNickname = null;
        _userProfileImage = null;
      });
    } catch (error) {
      print('Logout failed: $error');
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        backgroundColor: Theme.of(context).colorScheme.inversePrimary,
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            _userProfileImage != null
                ? CircleAvatar(
                    backgroundImage: NetworkImage(_userProfileImage!),
                    radius: 40,
                  )
                : const Icon(Icons.account_circle, size: 80),
            const SizedBox(height: 10),
            Text(
              _userNickname ?? '로그인 해주세요',
              style: Theme.of(context).textTheme.headlineMedium,
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: _userNickname == null ? _loginWithKakao : _logout,
              child: Text(_userNickname == null ? '카카오 로그인' : '로그아웃'),
            ),
          ],
        ),
      ),
    );
  }
}
```

위 코드는 로그인을 하면 유저 데이터중 닉네임, 프로필 사진을 가져와서 저 이후 닉네임의 유무로 로그인을 확인한다 로그아웃은 저장한 데이터를 null로 다시 넣어 데이터를 초기화한다

``` bash
I/flutter ( 8616): 카카오톡으로 로그인 성공 jnK-84QiddghSqciI91qZELqq0X0fbDAAAAAAQorDKgAAAGUzAOlsdafsadfsdf
I/flutter ( 7540): {id: *******, properties: {nickname: 김민정}, kakao_account: {profile_nickname_needs_agreement: false, profile: {nickname: 김민정}}, connected_at: 2024-11-02T10:59:32.000Z}
```

위 데이터는 가져온 **유저 정보와 accesstoken**이다 초기 앱설정시 닉네임만 가져오도록 설정해서 profile에 nickname만 존재한다 만약 사진까지 허용하면 사진도 같이 받을 수 있다

<table><td><center><img alt="" src="https://github.com/user-attachments/assets/79a706d9-5254-4c17-ac55-bea927a76273" style="zoom:40%;" /></center></td><td><center><img alt="" src="https://github.com/user-attachments/assets/14025bcc-6446-4f54-aad1-19b1b425bc28" style="zoom:40%;" /></center></td></table> 

위 코드에서 `print(await KakaoSdk.origin);`를 통해서 직접 키 해시를 확인하고 카카오 앱 설정에서 확인한 키 값으로 교체했더니 카카오 로그인이 정상적으로 실행되었다
