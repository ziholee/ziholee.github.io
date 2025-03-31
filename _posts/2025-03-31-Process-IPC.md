---
title: "[운영체제] Processec-IPC"
author: ziho
date: 2025-03-31
categories: [운영체제]
tags: [os, process, ipc, interprocess, study]
pin: true
---
Process-IPC
=============

## Interprocess Communication
### 프로세스는 시스템 내에서 <span style="color:red">독립적</span>이거나 <span style="color:red">협조적</span>일 수 있다.
### 프로세스가 협동을 하는 이유
- 정보 공유
- 계산 속도 향상
- 분업(각각의 모듈이 독립적으로 실행해서 일을 분담)
- 편리
### 협조적인 프로세스는 프로세스 간 통신(IPC, interprocess communication)이 필요하다.
### IPC의 두 가지 모델
- <span style="color:yellow">공유 메모리(Shared memory)</span>
- <span style="color:yellow">메시지 패싱(Message passing)</span>
## Communications Models
![메시지패싱,셰어드메모리사진](/assets/img/SmMp.png)
## Shared Memory & Message Passing

||Message Passing|Shared Memory|
|------|---|---|
|Implementation(구현)|쉽다, Easy|어렵다, Difficult|
|Speed(속도)|느리다, Slow|빠르다, Fast|
|Kernel intervention(커널 간섭 혹은 관여)|많이, alot, via system calls|조금, 적다(<span style="color:red">없다 = X</span>), No system calls <span style="color:blue">except setup</span>|
|Data size(데이터 크기)|작은용량, Good for small amount|대용량, Good for large amount|

## Shared Memory Systems
추후 수정 예정

