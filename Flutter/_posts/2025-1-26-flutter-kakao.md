---
layout: post
title: "플러터에서 카카오 로그인 구현"
tags:  flutter kakaologin
excerpt_image: ""
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

