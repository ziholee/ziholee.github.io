---
title: "[운영체제] CPU Scheduling"
author: ziho
date: 2025-04-12
categories: [운영체제, 스케줄링]
tags: [os, process, cpu, scheduling, burst, preemptive, non-preemptive, study]
pin: true
---
# Cycle of CPU/IO Burst (CPU-I/O 버스트 주기)

#### 프로세스 실행 흐름은 CPU Burst와 I/O 대기 시간의 반복으로 구성된다.

### CPU 버스트: CPU에서 실제로 실행되는 시간 

- e.g. 엑셀 정렬, 게임을 할 때

### I/O 버스트: 디스크나 네트워크 등의 입출력을 기다리는 시간 

- e.g. 키보드나 마우스 입력, 네트워크로 데이터를 주고받을 때

---

## CPU Scheduler (CPU 스케줄러)

Short-Term Scheduler가 Ready Queue에 있는 프로세스 중 하나를 선택해 CPU에 할당한다.
Ready Queue는 단순 FIFO가 아니며 다양한 방식으로 정렬 가능하다.
스케줄링은 프로세스가 다음 중 하나의 상태가 될 때 발생한다.

---

- running → waiting             <span style="color: red;">Non-Preemptive</span>
- running → ready (인터럽트 등)    <span style="color: blue;">Preemptive</span>
- waiting → ready (I/O 완료 등)   <span style="color: blue;">Preemptive</span>
- running → terminated          <span style="color: red;">Non-Preemptive</span>

## Preemptive vs. Non-preemptive Scheduling

사전적 의미:
- Preemptive: 강제성, 뺏는, 선점하는
- non-Preemptive: 강제성이 없는, 비선점하는

<span style="color: red;">Preemptive</span>: <span style="color: yellow;">스케쥴러가 강제로 프로세스를 CPU로부터 추방시킨다.</span>

- 실행 중인 프로세스를 강제로 중단시키고, 다른 프로세스로 CPU를 넘겨줄 수 있는 방식이다. (e.g. 타이머 인터럽트)
- 운영체제가 타이머 인터럽트(timer interrupt) 등을 통해 CPU 점유권을 회수한다.
- 우선순위가 더 높은 작업이 도착하면 즉시 전환 가능하다.
- 실시간 반응성, 멀티태스킹에 적합하다.

<span style="color: red;">Non-Preemptive</span>: <span style="color: yellow;">프로세스가 자발적으로 CPU를 포기할 때까지 계속 실행된다.</span> (I/O 대기 중이거나 종료 중)

- 현재 실행 중인 프로세스가 I/O 요청하거나 종료하기 전까지는 CPU 점유 유지한다.
- 운영체제가 개입하지 않는다. -> 단순하지만 유연성이 떨어진다.
- Context Switch(문맥 전환) 비용이 거의 없다.

예시: 비행 도중에 비행기 안에 탑승한 승무원을 갑자기 교체할 수 없다. (중간에 멈출 수 없고, 반드시 자신이 끝내야 한다.)

대부분의 최신 운영 체제는 타이머 인터럽트를 통해 구현된 선점형 CPU 스케줄링(Preemptive CPU Scheduling)을 사용한다.

# Scheduling Criteria (스케줄링 기준)

## CPU Utilization (CPU 이용률)

- CPU가 실제로 일을 하고 있는 시간의 비율을 말한다.
- CPU가 가능한 한 쉬지 않고 일하게 하는 것이 목표다. 
- (CPU가 놀지 않고 얼마나 바쁘게 일했는지)

## Throughput (처리량)

- 단위 시간당 완료된 프로세스 수(# of processes)
- 1초에 몇 개의 프로세스를 얼마나 많이 끝냈는지

## Turnaround Time (반환 시간)

- 프로세스 시작~종료까지 걸린 총 시간
- 프로세스가 제출되어 완전히 끝날 때까지 걸린 총 시간 = 완료시간 - 도착시간

## Waiting Time (대기 시간)

- 프로세스가 Ready Queue에서 기다린 시간
- 얼마나 대기실에서 대기를 했는가?
- 실제 CPU를 쓰지는 못하고, 준비 상태로 대기한 시간

## Response Time (응답 시간)

- 요청을 보낸 순간부터 처음 반응이 나타날 때까지의 시간
- 사용자가 어떤 요청을 했을 때, 언제까지 걸렸는지

---

### 표로 정리하면

|Scheduling Criteria |	설명|	좋은 방향|
|------|---|---|
|CPU Utilization|	CPU가 실제로 일하는 비율|	높을수록 좋음|
|Throughput|	단위 시간당 완료된 프로세스 수|	높을수록 좋음|
|Turnaround Time|	하나의 작업이 완료되기까지 걸린 총 시간|	낮을수록 좋음|
|Waiting Time|	ready queue에서 기다린 총 시간|	낮을수록 좋음|
|Response Time|	요청 후 첫 응답이 시작될 때까지 시간|	낮을수록 좋음|

---

### 간단하게 정리하자면

|<span style = "color:yellow">Scheduling Algorithm Optimization Criteria|</span>|
|------|---|
|<span style = "color:red">*Max*</span>|<span style = "color:blue">*Min*</span>|
|CPU Utilization|Turnaround Time|
|Throughput|Waiting Time|
||Response Time|

---
# Scheduling Policies

## Non-Preemptive

- 프로세스가 <span style = "color:yellow">"자발적"</span>으로 CPU를 떠날 때까지, CPU를 사용하게 둔다.

### FCFS(First Come First Served)

- 비선점형(Non-Preemptive) 방식이다.
- 먼저 온 사람이 먼저 할당 받는다.

### SJF(Shortest Job First) 

aka Shortest Process Next -> optimal: AWT(Average Wait Time)

- FCFS와 같이 비선점형 방식이다.
- 짧은 작업을 먼저 할당 받는다. (가장 짧은 CPU 버스트 시간의 프로세스를 먼저 실행)
- e.g. 식당에 가서 줄을 서있는데 대기 줄에 1번은 삼겹살 세트를 먹으러 온 팀과 2번은 포장 고객이다. 삼겹살은 먹고 가는 것보다 포장을 준비하는 것이 더 빠르기 때문에 1번 고객보다 2번 고객이 먼저 들어와서 포장을 받아간다.

## Preemptive
- 스케줄러가 <span style = "color:yellow">"강제"</span>로 프로세스를 CPU로부터 추방시킨다.

### SRTF (Shortest-Remaining-Time-First)
- SJF의 선점형(Preemptive) 방식이다.
- Burst Time 중에 쓰고 남은 시간이 가장 짧은 사람부터 할당한다. (항상 남은 시간이 가장 짧은 프로세스가 실행됨)
- 현재 실행 중인 프로세스보다 더 짧은 남은 실행 시간을 가진 프로세스가 준비 상태가 되면, 현재 실행을 중단하고 새 프로세스로 교체한다.

### Priority (우선순위)
- 프로세스마다 우선순위 번호가 정해져 있다. (보통 운영체제가 알아서 결정한다.)
- Preemptive / non-Preemptive 모두 가능하다.
- 우선순위 번호가 작을수록 우선순위가 높다.
- 우선순위가 높은 프로세스가 도착하면, 실행 중인 프로세스를 중단할 수 있다.

#### Priority는 몇가지 문제점이 있다.
#### Problem: <span style = "color:yellow">Starvation</span>(기아)
- 우선순위가 낮은 프로세스는 영원히 실행하지 못하고 굶어 죽을 수 있다.
- (낮은 우선순위의 프로세스는 계속 밀릴 수 있음)

#### Solution: <span style = "color:yellow">Aging</span>(에이징)
- 프로세스가 시간이 지날수록 우선순위가 올라간다.
- (시간이 지나면 우선순위를 점진적으로 높여서 실행될 기회를 보장)
- e.g. 짬순, 호봉제

SJF는 일종의 Priority Scheduling으로 볼 수 있다.
+ 부가설명 필요

## Multilevel
### Multilevel Queue
#### 프로세스의 성격에 따라 고정된 큐에 배치하고, 각 큐는 서로 다른 스케줄링 정책을 사용하며, 큐 간에도 우선순위가 존재한다.

큐(Queue) 간 스케줄링 방식에 2가지가 존재한다.

1. Fixed Priority Scheduling
- 높은 우선순위 큐가 먼저 실행된다.
    - e.g. forground(gui 맨 앞에 나오는 것) 큐가 background 큐보다 항상 먼저 실행.
- Foreground 큐가 비어야만 Background 큐 실행된다.
- 단, <span style = "color:red">기아(Starvation)</span>상태가 가능하다.
    - 모든 Priority의 단점

2. Time Slice: 한 번 프로세스가 CPU를 잡았을 때 최대로 사용할 수 있는 시간
- 전체 CPU 시간을 큐별로 비율을 할당한다.
    - e.g. 80%는 foreground, 20%는 background
- 각 큐는 할당된 시간 안에서 자체 스케줄링을 수행한다.
    - foreground는 80% 시간 안에서 *RR 실행
        - RR: Round Robin (q마다 실행)
    - background는 20% 시간 안에서 *FCFS 실행
        - FCFS: First Come First Surved (위에 설명 참조)

* 장점: 
    * 이 방식을 통해 배경 작업에도 최소한의 CPU 시간 보장이 가능하여 기아(starvation) 방지에 효과적이다.
    * response가 올라간다.    

### Mutileve Queue Scheduling

~~~
+----------------------+
| System Processes     | <- 높은 우선순위
+----------------------+
| Interactive Processes|
+----------------------+
| Interactive editing  |
| Processes            |
+----------------------+
| Batch Processes      |
+----------------------+
| Student Processes    | <- 낮은 우선순위
+----------------------+
~~~

### Multilevel Feedback Queue
#### 다양한 큐 사이를 프로세스가 이동할 수 있다; 이 방법으로 에이징(Aging)이 구현될 수 있다.
#### Muliteleve-feedback-queue는 다음과 같은 파라메터로 정의된다:

- number of queues
- 큐의 갯수
    - 몇개의 큐를 만들 것인지
- scheduling algorithms for each queue
- 각 큐의 스케줄링 알고리즘
    - 각각의 큐는 어떤 스케줄링 알고리즘을 사용할 것인지
- when to upgrade a process
- 프로세스를 언제 업그레이드할지
    - 언제 상위 프로세스 큐로 올라갈 것인지
- when to demote a process
- 프로세스를 언제 다운그레이드할지
    - 언제 하위 프로세스로 내려갈 것인지
- which queue a new process will enter
- 새로운 프로세스를 어디서 시작할지
    - 새 프로세스를 어디 큐에 할당할지

### Example of Multilevel Feedback Queue

- Q0 - RR (time Quantum = 8)
- Q1 - RR (time Quantum = 16)
- Q2 - FCFS

* Quntum이 작을수록 프로세스 전환이 많아진다.

~~~
       +-----------------+
------>|*Q0 Quantum = 8  |------>
       +-----------------+ ---
                             |
   ---------------------------
   |   
   |--> +-----------------+
------> |*Q1 Quantum = 16 |------>
        +-----------------+---
                             |
   ---------------------------
   |   
   |--> +-----------------+
------> |*Q2     FCFS     |------>
        +-----------------+

~~~

- Q0가 비어야 Q1 실행, Q1이 비어야 Q2 실행
- Q0 > Q1 > Q2 우선순위
- Q0에서 8ms 이상 소요되면 → Q1로 이동
- Q1에서 16ms 이상 소요되면 → Q2로 이동

* 짧게 실행되는 프로세스는 높은 우선순위를 유지,
긴 작업은 점점 낮은 우선순위로 이동

### Windows / Linux Scheduling

#### Windows Scheduling

- 우선순위 기반 선점형(preemptive priority-based) 스케줄링
- 큐 마다 우선순위가 있다.
- Dispatcher = Scheduler
- Dispatcher가 실행 중인 쓰레드를 다음 쓰레드로 교체
- 총 32단계 우선순위
- 0: Memory 관리 스레드
- 1~15: 일반 프로세스 (<span style = "color:magenta">Variable class</span>)
- 16~31: 실시간 프로세스 (<span style = "color:magenta">real-time class</span>)
- 만약 실행 가능한 스레드가 없는 경우, *<span style = "color:pink">idle thread</span>를 실행한다.
    - <span style = "color:pink">idle thread</span>: 아무것도 안하고 있다.


#### Windows Priority Classes

- Quantum을 다 쓰면, 우선순위가 감소한다.
    - 단, 기본 우선순위 이하로는 안내려간다.
- wait이 발생한다면, 우선도가 올라간다.(기다린 것에 따라)
    - 입출력 대기 이후 복귀 -> 우선순위 보상
- foreground window 빠른 반응을 위해서 3배로 시간을 할당한다.

~~~
Burst Time <UP>, Priority <DOWN> 
                                \
                                |----> 이유는 response를 높이기 위해서
                                /
Burst Time <Down>, Priority <Up>
~~~

#### Linux Scheduling

#### Linux 

## 스케줄링 알고리즘

| 알고리즘 | 특징 | 장점 | 단점 |
|------|---|---|---|
| FCFS | 도착 순서대로 실행 | 구현 간단 | 긴 작업 먼저 오면 전체 지연 커짐 (Convoy Effect) |
| SJF | 버스트 시간 짧은 작업 우선 | 평균 대기시간 최소 | 버스트 시간 예측 어려움 |
| SRTF | 남은 시간 가장 짧은 작업 선점 실행 | 이론적으로 최적 | 선점 비용 큼, 예측 필요 |
| Round Robin | 시간 할당량 순환 실행 | 반응성 뛰어남 | 문맥 전환 오버헤드 있음 |
| Priority Scheduling | 우선순위 높은 순서로 실행 | 중요한 작업 빠르게 처리 | 기아(Starvation) 가능 |
| Multilevel Queue | 프로세스 그룹 별로 큐 분리 | 구조화된 처리 가능 | 유연성 낮음, 큐 이동 어려움 |



