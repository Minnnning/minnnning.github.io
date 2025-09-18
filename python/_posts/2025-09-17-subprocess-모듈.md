---
layout: post
title: 파이썬 subprocess 모듈과 threading 모듈
tags: subprocess threading
---

클라우드 프로젝트 중 나는 클라이언트(블랙박스) + 영상 업로드 서버를 맡아서 개발을 진행중이었다

기능 요구사항 중 ffmpeg을 실행할 수 있어야 하고 이후 ffmpeg을 실행하면서 동시에 저장된 영상을 보내는 2가지 동작을 해야하는 경우가 있어서 **subprocess 모듈**을 사용했고 **threading 모듈**을 사용할 예정이다

이번 기회에 두개의 모듈을 처음 사용해봐서 두 모듈에 사용한 메서드에 대해서 작성했다 매서드가 매우 많아서 제대로 사용할려면 공식문서를 자주 봐야겠다

&nbsp;

## subprocess

`subprocess` 모듈은 파이썬 스크립트 내에서 완전히 새로운 프로세스를 생성하여 셸 명령어(`ls`, `copy` 등)나 다른 실행 파일(`ffmpeg`, `git` 등)을 실행하고 그 결과를 받아오기 위해 사용된다 

### 언제 사용하는가?

- 기존 cli를 이용해야 할 때: 커맨드로 실행하는 프로그램을 실행할 수 있다 이번 프로젝트에서 ffmpeg을 사용하기 위해서 subprocess를 사용했다
- 다른 언어로 작성된 스크립트를 실행할 때: 셸 스크립트나 다른 언어로 작성된 간단한 프로그램을 실행하고 결과를 통합해야 할 때 유용하다

👉 **파이썬 프로세스가 외부에게 특정 작업을 지시하고 보고 받을 수 있다**

&nbsp;

### 사용 메서드

#### 1. subprocess.run()

run에 포함된 외부 명령을 실행하고 **끝날 때까지 기다린 후** 결과를 반환한다 **동기** 

``` python
import subprocess
#중략
def stream_to_server():
    """온라인 모드: 서버로 스트리밍을 시작합니다. 연결이 끊기면 함수가 종료됩니다."""
    print("\n🚀 [온라인 모드] 서버로 스트리밍을 시작합니다.")
    REQUEST_TIME = datetime.now().strftime("%Y%m%d-%H%M%S")
    SRT_URL = f'srt://{SERVER_IP}:{SERVER_PORT}?streamid=publish:{CLIENT_UUID}/{REQUEST_TIME}'
    
    command = get_base_ffmpeg_command(platform.system())
    if not command: return

    command.extend([
        '-c:v', 'libx264', '-preset', 'veryfast', '-tune', 'zerolatency',
        '-c:a', 'aac', '-b:a', '128k',
        '-f', 'mpegts', SRT_URL
    ])
    
    print("실행될 명령어: ", ' '.join(command))
    subprocess.run(command, stderr=sys.stderr)
```

위 함수를 실행하면 ffmpeg을 계속 실행해서 서버로 영상을 보낸다 만약 ffmpeg이 문제가 발생하거나 종료되면 그제서야 다음 코드로 넘어간다 위 함수 실행 다음에 오는 코드는 당연히 ffmpeg에 연결이 끊기거나 문제가 발생한것을 의미하므로 에러발생 로그를 출력했다

&nbsp;

#### 2. subprocess.Popen()

위 메서드는 내가 이번에 사용하지는 않았지만 subprocess에서 주로 사용하는 메서드이다

popen은 명령어 실행 결과를 기다리지 않고 popen객체를 반환해서 파이썬이 다음 코드를 실행할 수 있다

프로세스를 백그라운드에서 실행할 때 사용할 수 있고 아래는 예시로 5초 동안 ping을 보내는 동안 다른 작업 수행한다

``` python
import subprocess
import time

print("🚀 ping 프로세스를 백그라운드에서 시작합니다.")

# stdout=subprocess.PIPE: 자식 프로세스의 표준 출력을 파이프로 연결하여 읽을 수 있게 함
process = subprocess.Popen(['ping', '-c', '5', 'google.com'], stdout=subprocess.PIPE, text=True)

# Popen은 기다리지 않으므로, 아래 코드가 바로 실행됨
count = 0
while process.poll() is None: # poll(): 프로세스가 종료되었으면 리턴 코드, 아니면 None 반환
    print(f"  - 메인 작업 수행 중... ({count+1}초)")
    time.sleep(1)
    count += 1

# 프로세스가 끝나면 communicate()로 남은 모든 출력을 가져옴
stdout, stderr = process.communicate()
print("\n✅ ping 프로세스 종료!")
print(f"최종 리턴 코드: {process.returncode}")
# print("--- 전체 출력 ---")
# print(stdout)
```

[subprocess 공식문서](https://docs.python.org/ko/3.13/library/subprocess.html)

&nbsp;

## threading

하나의 파이썬 프로세스 내에서 **여러 개의 실행 흐름(스레드)을 만들어** 특정 작업을 동시에 처리하기 위해 사용된다 

주로 I/O 바운드(I/O-bound) 작업의 효율을 높이는 데 사용한다

### 언제 사용하는가?

**네트워크 작업**: 여러 URL에서 동시에 데이터를 다운로드하거나, 여러 클라이언트의 요청을 동시에 처리하는 서버를 만들 때 사용

**파일 I/O**: 대용량 파일을 읽거나 쓰는 동안 프로그램이 멈추지 않고 다른 작업을 수행하게 할 때 유용

👉 **작업이 I/O 대기인 경우 그 시간에 다른 작업을 수행할 수 있다**

&nbsp;

### 사용 메서드

#### threading.Thread(실행 함수, 인자)

스레드를 생성하고 관리하는 핵심 클래스이다 스래드를 생성, 시작, 대기

``` python
import threading
import time

def worker(number):
    """스레드가 실행할 함수"""
    print(f"스레드 {number}: 시작")
    time.sleep(2) # I/O 작업(예: 네트워크 대기)을 시뮬레이션
    print(f"스레드 {number}: 종료")

threads = []
for i in range(3):
    # target에 실행할 함수를, args에 인자를 전달하여 스레드 생성
    t = threading.Thread(target=worker, args=(i,))
    threads.append(t)
    t.start() # 스레드 실행

print("메인 스레드: 모든 작업 스레드가 끝날 때까지 기다립니다.")
for t in threads:
    t.join() # 해당 스레드가 끝날 때까지 대기

print("모든 작업이 완료되었습니다.")
```

&nbsp;

#### start()

각각의 스레드 작업은 start() 메서드로 실행한다

&nbsp;

##### 데몬 스레드 (daemon=True) 옵션

메인 프로그램이 종료될때 사용되는 백그라운드 스레드

`thread = threading.Thread(target=background_task, daemon=True)`이렇게 사용한다

메인 작업에 종속적인 보조 작업(로깅, 상태 감시 등)을 수행할 때 유용하다 그렇다고 꼭 메인프로그램이 종료될 때만 종료되는게 아니라 스레드가 끝나면 자동으로 종료된다 -> 메인 프로그램이 꺼지면 이 스레드로 강제로 꺼지게 하는 설정이기도 하다

[threading 공식문서](https://docs.python.org/ko/3.13/library/threading.html)

&nbsp;

## subprocess와 multiprocessing 차이

위 내용을 보고 별개의 프로세스를 생성하는 subprocess와 비슷하게 프로세스를 생성하는 multiprocessing의 차이가 궁금해졌다

찾아보니 두개의 사요용도가 다르다는 것을 알았다

subprocess는 **외부 명령어를 실행하기 위한 모듈이고**, multiprocessing은 **Python 코드를 병렬로 실행하기 위한 특화된 모듈**이다 

| 특징            | `subprocess`                                     | `multiprocessing`                                       |
| --------------- | ------------------------------------------------ | ------------------------------------------------------- |
| **주요 목적**   | **모든 종류의 외부 프로그램/명령어 실행**        | **Python 함수(코드)를 병렬로 실행**                     |
| **추상화 수준** | **저수준 (Low-level)** OS의 프로세스를 직접 제어 | **고수준 (High-level)** 복잡한 프로세스 관리를 자동화   |
| **데이터 교환** | 복잡함 (표준 입출력 파이프를 직접 관리)          | 쉬움 (`Queue`, `Pipe`, `Manager` 등 Pythonic 객체 제공) |
| **사용 편의성** | Python 함수 병렬 실행에는 복잡함                 | Python 함수 병렬 실행에 매우 편리함                     |
| **API**         | 범용적 프로세스 관리 API                         | `threading` 모듈과 유사한 API (사용자에게 친숙)         |
| **대표 클래스** | `subprocess.Popen`, `subprocess.run`             | `multiprocessing.Process`, `multiprocessing.Pool`       |
