---
title: "[운영체제] Process-Threads"
author: ziho
date: 2025-04-07
categories: [운영체제, 프로세스, 스레드]
tags: [os, process, thread, study]
pin: false
---
## Motivation(동기)
애플리케이션 내에 실행되는 스레드
애플리케이션을 사용하는 여러 작업은 별도의 스레드로 구현할 수 있다.
- Update display 디스플레이 업데이트
- Fetch data 데이터 가져오기
- Spell checking 철자 검사
- Answer a network request 네트워크 요청 응답
프로세스를 새로 만드는 것은 무겁지만 쓰레드를 만드는 것은 가볍다.
코드를 간소화 하고 효율성을 높일 수 있다.
커널은 일반적으로 멀티스레드에 포함된다.
### Examples
웹 브라우저
- 스레드1: 디스플레이 이미지
- 스레드2: 텍스트 제공
- 스레드3: 네트워크로 부터 데이터 불러오기
워드 프로세서
- 스레드1: 디스플레이 이미지
- 스레드2: 텍스트 제공
- 스레드3: 네트워크로 부터 데이터 불러오기
웹 서버
-
커널
## Single and Multithreaded Processes(단일 및 다중 스레드 프로세스)

![singleandmultithread](/assets/img/SingleAndMultiThread.png)

- code, data, files는 공유
- registers(SP,PC), stack은 분리 -> 즉, 공유되지 않는다.
### Benefits(스레드 이점)
Responsiveness(반응성) -> 반응시간과 반비례하다.
Easy Resource Sharing(자원 공유 용이성)
- 같은 주소 공간을 스레드가 사용한다.
Economy(경제성)
- Cheaper creation(만들기에 저렴하다), context switch -> 스레드는 공유하고 있는 것들이 많아서 context switching할 것이 적다.
Scalability(확장성)
- 다른 프로세스에서 스레드가 돌아가고 있다.
- e.g. 멀티 코어 환경에서 여러 스레드가 병렬적으로 실행될 수 있어 성능을 최대한 활용할 수 있다.

## Parallel Execution on a Multicore System

### single core

```
T1 - T2 - T3 - T4 - T3 - T2 - T1 - ...

--------------------------------------->
                time
```

### Multi-core

```
core 1

T1 - T3 - T1 - T3 - T1 - ...

------------------------------>
                time

core 2

T2 - T4 - T2 - T4 - T2 - ...

------------------------------>
                time

        코어 수에 따라 달라진다.
```

## Multicore Programming

### 프로그래머에게 압력을 가하는 멀티코어 시스템에는 다음과 같은 문제가 있다.

- Dividing activities (활동 나누기)
- Balance (균형)
- Data splitting (데이터 분할)
- Data dependency (데이터 종속성)
- Testing and debugging (테스트 및 디버깅)


