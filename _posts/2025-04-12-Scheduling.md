---
title: "[운영체제] CPU Scheduling"
author: ziho
date: 2025-03-31
categories: [운영체제, 스케쥴링]
tags: [os, process, cpu, scheduling, study]
pin: true
---
## Cycle of CPU/IO Burst (CPU-I/O 버스트 주기)
프로세스 실행 흐름은 CPU Burst와 I/O 대기 시간의 반복으로 구성된다.
- CPU 버스트: CPU에서 실제로 실행되는 시간 ex) 엑셀 정렬, 게임을 할 때
- I/O 버스트: 디스크나 네트워크 등의 입출력을 기다리는 시간 ex) 키보드나 마우스 입력, 네트워크로 데이터를 주고받을 때

## CPU Scheduler (CPU 스케줄러)
단기 스케줄러(short-term scheduler)가 ready queue에 있는 프로세스 중 하나를 선택해 CPU에 할당한다.
ready queue는 단순 FIFO가 아니며 다양한 정책으로 정렬 가능
스케줄링은 프로세스가 다음 중 하나의 상태가 될 때 발생한다.

- running → waiting
- running → ready (인터럽트 등)
- waiting → ready (I/O 완료 등)
- running → terminated

## Preemptive vs. Non-preemptive Scheduling
사전적 의미:
- Preemptive: 강제성, 뺏는, 선점하는
- non-Preemptive: 강제성이 없는, 비선점하는

<span style="color: red;">Preemptive</span>: 스케쥴러가 강제로 프로세스를 CPU로부터 추방시킨다.
- <span style="color: yellow;">실행 중인 프로세스를 강제로 중단시키고, 다른 프로세스로 CPU를 넘겨줄 수 있는 방식이다.<span> (e.g. 타이머 인터럽트)
- 운영체제가 타이머 인터럽트(timer interrupt) 등을 통해 CPU 점유권을 회수한다.
- 우선순위가 더 높은 작업이 도착하면 즉시 전환 가능하다.
- 실시간 반응성, 멀티태스킹에 적합하다.

<span style="color: red;">Non-Preemptive</span>: 프로세스가 자발적으로 CPU를 포기할 때까지 실행된다. (I/O 대기 중이거나 종료 중)
- <span style="color: yellow;">현재 실행 중인 프로세스가 I/O 요청하거나 종료하기 전까지는 CPU 점유 유지한다.</span>
- 운영체제가 개입하지 않는다. -> 단순하지만 유연성이 떨어진다.
- Context Switch(문맥 전환) 비용이 거의 없다.

예시: 비행 도중에 비행기 안에 탑승한 승무원을 갑자기 교체할 수 없다. (중간에 멈출 수 없고, 반드시 자신이 끝내야 한다.)

대부분의 최신 운영 체제는 타이머 인터럽트를 통해 구현된 선점형 CPU 스케줄링(Preemptive CPU Scheduling)을 사용합니다.

## Scheduling Criteria (스케줄링 기준)
### 1. CPU Utilization (CPU 이용률)
- CPU가 실제로 일을 하고 있는 시간의 비율을 말한다.
- CPU가 가능한 쉬지 않고 일하게 하는 것이 목표다.

### Throughput (처리량)
- 단위 시간당 완료된 프로세스 수(# of processes)
- 1초에 몆 개의 프로세스를 얼마나 많이 끝냈는지

## Turnaround Time (반환시간)
- 프로세스가 제출되어 완전히 끝날 때까지 걸린 총 시간 = 완료시간 - 도착시간

### Waiting Time (대기 시간)
- 프로세스가 Ready Queue에서 기다린 총 시간
- 얼마나 대기실에서 대기를 했는가?
- 실제 CPU를 쓰지는 못하고, 준비 상태로 대기한 시간

### Response Time (응답 시간)
- 요청을 보낸 순간부터 처음 반응이 나타날 때까지의 시간
- 사용자가 어떤 요청을 했을 때, 언제까지 걸렸는지

|<span style = "color:yellow">Scheduling Algorithm Optimization Criteria|</span>|
|------|---|
|<span style = "color:red">*Max*</span>|<span style = "color:blue">*Min*</span>|
|CPU Utilization|Turnaround Time|
|Throughput|Waiting Time|
||Response Time|

---

|Scheduling Criteria |	설명|	좋은 방향|
|------|---|---|
|CPU Utilization|	CPU가 실제로 일하는 비율|	높을수록 좋음|
|Throughput|	단위 시간당 완료된 프로세스 수|	높을수록 좋음|
|Turnaround Time|	하나의 작업이 완료되기까지 걸린 총 시간|	낮을수록 좋음|
|Waiting Time|	ready queue에서 기다린 총 시간|	낮을수록 좋음|
|Response Time|	요청 후 첫 응답이 시작될 때까지 시간|	낮을수록 좋음|

## Scheduling Policies

### Non-Preemptive

FCFS(First Come First Served)
---
- 먼저 온 사람이 먼저 할당 받는다.

SJF(Shortest Job First) aka Shortest Process Next
---
- 짧은 작업을 먼저 할당 받는다.
- e.g. 식당에 가서 줄을 서있는데 대기 줄에 1번은 삼겹살 세트를 먹고 2번은 포장 고객이어서 1번 고객보다 2번 고객이 먼저 들어와서 포장을 받아간다.

## 스케줄링 알고리즘

| 알고리즘 | 특징 | 장점 | 단점 |
|----------|------|------|------|
| FCFS | 도착 순서대로 실행 | 구현 간단 | 긴 작업 먼저 오면 전체 지연 커짐 (Convoy Effect) |
| SJF | 버스트 시간 짧은 작업 우선 | 평균 대기시간 최소 | 버스트 시간 예측 어려움 |
| SRTF | 남은 시간 가장 짧은 작업 선점 실행 | 이론적으로 최적 | 선점 비용 큼, 예측 필요 |
| Round Robin | 시간 할당량 순환 실행 | 반응성 뛰어남 | 문맥 전환 오버헤드 있음 |
| Priority Scheduling | 우선순위 높은 순서로 실행 | 중요한 작업 빠르게 처리 | 기아(Starvation) 가능 |
| Multilevel Queue | 프로세스 그룹 별로 큐 분리 | 구조화된 처리 가능 | 유연성 낮음, 큐 이동 어려움 |

+ 멀티 레벨 큐는 아직 배우지 않음

