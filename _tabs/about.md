---
icon: fas fa-info-circle
order: 4
---

# 안녕하세요! 개발자 김민정입니다. 👋

아이디어가 떠오르면 바로 실행에 옮기고, 필요한 것이 있다면 새로운 기술을 배우는 데 주저하지 않는 **실행력과 도전정신을 가진 개발자 김민정**입니다.
조금씩이라도 꾸준한 성장과 경험을 바탕으로, 더 나은 사용자 경험과 효율적인 솔루션을 만들어가는 데에 관심이 많습니다. 🚀

---

## 기술 스택 및 역량 ⚙️

- **프로그래밍 언어:** Python 🐍, Swift 🦅, JavaScript, SQL, Dart
- **프레임워크/라이브러리:** SwiftUI, React ⚛️, FastAPI, Express, Flutter
- **데이터베이스 및 검색 엔진:** MongoDB, MySQL, MariaDB, Elasticsearch 🔍  
- **도구 및 기타:** Docker 🐳, Docker-compose, Selenium

---

## 프로젝트 정보 💻

### 1. Mapping 앱 – 지도 기반 소셜 네트워크 앱 🗺️

- **목적:**  
  새로운 지역 방문 시, 지도에 표시되지 않는 공중화장실, 쓰레기통, 흡연장 등 유용한 정보를 쉽게 확인할 수 있도록 소셜 네트워크 서비스를 구상  
- **개발환경:**  
  - iOS 네이티브 앱 (SwiftUI)  
  - iOS ➡️ Kakao SDK, Alamofire, Google sgin in 활용  
  - Flutter 안드로이드 개발
  - Android ➡️ kakao_flutter_sdk, google_maps_flutter, google_sign_in, dio등
- **역할:**
  - 앱 디자인 및 개발
  - 프런트엔드(iOS) 100%
  - 프런트엔드(Android) 100%
- **기능:**
  - 위치 기반 메모 생성 기능
  - 현재 카메라 중심 기반 메모 검색 기능
  - 카테고리별 메모 필터링 기능
  - 메모를 제목 기반으로 검색 기능
  - 메모의 댓글, 좋아요, 싫어요 기능
  - 사용자 필터링을 위한 차단, 신고 기능
  - 좋아요, 내가 작성한 메모 모아보기 기능
- **배포일:** 2025년 2월 27일  (iOS), 2025년 5월 21일 (Android)
- **GitHub 링크:** [Frontend-iOS](https://github.com/Mapping2024/Frontend-iOS), [Frontend-Android](https://github.com/Minnnning/Mapping-Frontend-Flutter)

---

### 2. 충북대 검색앱 – 교내 검색 엔진 앱 🔎

- **목적:**  
  교내 여러 사이트에 흩어진 공지사항을 한 곳에서 쉽게 검색할 수 있도록 검색 엔진 앱 개발  
- **기술 스택:**  
  - iOS 네이티브 앱 (SwiftUI), Alamofire  
  - Docker, Elasticsearch, FastAPI, MariaDB, Selenium  
  - 자체 서버 구축 ESXi, Ubuntu
- **역할:**
  - 공지사항 및 교내 정보 크롤링 코드 작성 및 정보 저장
  - Elasticsearch 검색 최적화(토큰화 가중치 부여)
  - 스케줄링과 자동화를 위한 shell script 작성
  - iOS 앱 개발 및 디자인
  - ESXi를 이용한 Ubuntu 서버 구축
  
- **기능:**
  - 검색어 토큰 분해를 통한 검색 최적화
  - 검색어 필터링 (학식, 학과 검색시 해당 정보만 가져옴)
  - 공지 사항 외 건물 위치 정보 지도로 표시
  - 실시간 검색어 표시
  - 자동 크롤링 및 데이터 저장
  - 서버 실행, 종료를 위한 shell script 생성

- **성과:**  
  교내 캡스톤 디자인 대회 우수상 수상 🏆  
- **GitHub 링크:**  
  - [cbnu-search-engine-server](https://github.com/Minnnning/cbnu-search-engine-server)  
  - [cbnu-search-app-iOS](https://github.com/Minnnning/cbnu-search-app-iOS)

---

### 3. 충북대 Judge 서버 – 코딩 테스트 및 과제 채점 시스템 📝

- **목적:**  
  교내 코딩테스트 및 코딩 과제 시 채점 시스템의 효율적 운영 및 기능 개선  
- **기술 스택:**  Express, MongoDB, Angular, Docker, Docker-compose, Kafka
- **담당 역할 및 개선 내용:**  
  - Express, MongoDB, Angular, Docker, Docker-compose 기반 서버 유지보수  
  - 대회 나가기 기능 추가 (재입장 불가능 처리)  
  - C언어 math 라이브러리 오류 수정  
  - 대회 분할 API 생성 (참가신청, 진행중 기능 포함)  
  - 대회 작성자 문제 검색 기능 및 문제 파일/PDF 다운로드 기능 추가  
  - 공지사항 CRUD 기능 생성  
- **GitHub 링크:** [sw-judger](https://github.com/cbnusw/sw-judger)

---

### 4. LG CNS 인턴십 – 스마트 팩토리 부서 생산 DX팀 대시보드 프로젝트 📊

- **프로젝트 배경:**  
  기존 ESS 제품의 에프터 서비스 정보 관리를 위해 엑셀이나 수작업으로 데이터를 정리하는 불편함 해소  
- **진행 과정:** 
  - 데이터 시각화를 위해 Elasticsearch, 태블로, D3 등을 검토 후, Elasticsearch를 활용한 대시보드 구축  
  - Oracle 데이터 Elasticsearch에 자동 저장 코드 생성
  - 담당자 및 관리자와의 협의를 통해 사용자 친화적인 데이터 표현 방법 도출  
  - 다른 사용자가 사용할 수 있도록 사용 문서 작성
- **성과:**  
  대시보드에서 날짜 조작, 국가 선택 등의 간단한 조작만으로 애프터 서비스 정보를 쉽게 모니터링할 수 있도록 개선  
- **느낀점:**  
  단독 프로젝트 진행이었으나, 부서 내 팀원 회의 참여를 통해 조직 내 협업 및 팀워크의 중요성을 체감

---

### 5. 오픈소스 스프린트 – Githru VSCode 익스텐션 🔧

- **목적:**  
  VSCode에서 Git 관리를 돕는 익스텐션 개발을 통해 오픈소스에 기여  
- **기술 스택:**  
  React 기반 개발  
- **주요 기능:**  
  PR의 Number 클릭 시 해당 링크로 이동하는 기능 구현  
- **GitHub 링크:** [Githru](https://github.com/githru)

---

## 추가 사항 및 연락처 📞

- **이메일:** [6alswjd6@gmail.com](mailto:6alswjd6@gmail.com)  
- **GitHub:** [Minnnning](https://github.com/Minnnning)

---

> **개발자로서의 목표:**
> 기술적인 성장뿐만 아니라 **팀원들과 소통하기 편하고 먼저 말을 걸기 쉬운 개발자**가 되고자 합니다.
> 개인적으로는 **현재에 안주하지 않고, 끊임없이 발전하며 새로운 것을 발굴하는 개발자**를 목표로 삼고 있습니다. 🤝✨
