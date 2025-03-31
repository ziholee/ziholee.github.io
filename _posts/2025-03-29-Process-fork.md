---
title: "[운영체제] Process-fork"
author: ziho
date: 2025-03-29
categories: [운영체제]
tags: [os, process, fork, study]
pin: true
---
Process-fork
=============
Process Creation
-------------
## 1. 프로세스 생성
### 운영체제는 하나의 프로세스에서 다른 프로세스를 생성할 수 있도록 한다.
- <span style="color: yellow">부모 프로세스(Parent Process)</span>: 새로운 프로세스를 <span style="background-color: 0000FF">생성하는</span> 기존 프로세스
- <span style="color: yellow">자식 프로세스(Child Process)</span>: 부모 프로세스에 의해 생성된 <span style="background-color: 0000FF">새로운 프로세스</span>

### 프로세스들이 트리 구조를 형성하는 경우가 많다.
![tree-image](/assets/img/osTree.png)
### 보통 <span style="color: yellow">프로세스 식별자</span>에 의해 프로세스를 구별한다. => pid(process identifier)
### 자원(Resource) 공유
- 부모와 자식은 모든 자원을 공유한다.
- 부모가 자원의 일부만 자식과 공유한다. -> 자식들은 부모 자원의 부분집합이다.
- 부모와 자식이 어떠한 공유 없이 자원을 사용한다. -> 개개인이 독립적으로 자원을 사용한다.
### 실행 방식(Execution)
- 부모와 자식이 동시에 실행한다. -> 병렬로 실행.
- 부모가 자식이 종료될 때까지 기다린다.
### 주소 공간(Adress Space)
- 자식은 부모의 복사본이다. -> 자식이 부모 프로세스의 주소 공간을 그대로 상속.
- 자식은 새로운 프로그램을 메모리에 로드한다. -> 자식이 자신에게 적재될 새로운 프로그램을 가지고 있다.
### Unix Examples
- fork()를 호출하여 새로운 프로세스를 생성한다. -> 부모의 복사본을 생성.
- fork -> adress space가 완전히 복제된다.
- exec 시스템 콜을 사용하여 fork가 된 이후에 프로세스 메모리 공간에 새로운 프로그램으로 재배치한다.

```c
#include <stdio.h>
#include <unistd.h>

int main()
{
    pid_t pid;
    /*fork another process */
    pid = fork(); //복제, 자기 자식의 프로세스 아이디가 된다.
    if (pid < 0) { /*error occurred */
        fprinf(stderr, "Fork Failed"); //pid < 0, 에러 복제 실패한 것이다.
        retrun 1;
    }
    else if (pid == 0) { //0이면 사본이다.
        execlp("/bin/ls", "ls", NULL);
    }
    else { /* parent process */
        /*parent will wait for the child */
        wait(NULL); //Zombie를 막기 위해서 wait을 부르는게 좋다.
        printf("Child Complete");
    }
    return 0;
}
-------------------------------------------------------------------------------------------------
int main(){
    ...
    pid = fork();

//parent process
//pid is 1234
    if (pid < 0) { 
        fprinf(stderr, "Fork Failed"); 
        retrun 1;
    }
    else if (pid == 0) {
        execlp("/bin/ls", "ls", NULL);
    }
    else {
        wait(NULL); 
        printf("Child Complete");
    }
    return 0;
}

/* new child process
process id = 1234
pid is 0 */
    if (pid < 0) { 
        fprinf(stderr, "Fork Failed"); 
        retrun 1;
    }
    else if (pid == 0) {
        execlp("/bin/ls", "ls", NULL);
    }
    else {
        wait(NULL); 
        printf("Child Complete");
    }
    return 0;
```
![Process Creation](/assets/img/os6.png)
## 2. wait() 함수 wait() function
### pid_t wait(int *status) -> status는 잘 수행이 되었는지 안되었는지 알려준다.
- 자식 프로세스가 끝날 때까지 막는다. -> Child가 끝날 때까지 대기 = <span style="color: blue">block</span>
- (부모가 wait()을 호출하면, 자식이 종료될 때까지 부모는 블록(block) 상태가 된다.)
- 자식 프로세스가 어떻게 종료되었는지 알 수 있다.
- 리소스 클린업(메로리 클린) ex) 죽은 자식 뒷정리 (이는 좀비 프로세스(Zombie Process) 를 방지하는 중요한 역할을 한다.)
### pid_t waitpid(pid, int *status, int options)
- 특정 자식 프로세스를 대기(wait에 pid가 뒤에 붙으면 특정 누군가를 기다리는 것이다.)
- 블록킹 없이 즉시 반환된다. -> can be non-blocking
### 만약에 부모 프로세스가 wait()을 부르지 않는다면?
#### 부모가 죽으면 init이 자식을 입양한다. -> 운영체제가 뒷처리를 한다.
- 만약 자식 프로세스가 계속 실행 중이라면 -> 프로세스는 고아가 된다. <span style="color: red">(orphaned)</span>
- 만약 자식 프로세스가 실행 중이지 않다면 -> init이 처리한다. (cleaned by init)
### 부모가 안 죽고 wait을 부르지 않는다면?
- 자식은 <span style="color: red">좀비</span>가 된다. -> wait()을 호출하지 않으면 좀비 프로세스가 발생할 수 있다.
## 3. 프로세스 종료 Process Termination
### 프로세스가 마지막 명령을 실행하면 운영체제한테 죽여달라고 요청한다. (이때 <span style="color: blue">exit</span>이라는 시스템 콜이 불린다.)
- 부모는 자식으로부터 Output data(exit status, 프로그램이나 명령의 결과를 나타냄.)를 받을 수 있다. (via wait)
- 프로세스의 자원을 운영체제에 의해 반납된다. ex) 열린 파일을 닫고, 종료시키고 등 ...
### 부모는 자식 프로세스 실행을 끝낸다. (abort)
- 자식이 자신에게 할당된 자원이 초과
- 자식에게 할당된 task가 더 이상 필요 없을 때
- 만약 부모가 끝났을 때
#### 어떤 os는 다같이 죽은 os가 있다.
- 모든 자식은 죽는다. -> <span style="color: blue">casecading termination(계단형분수 종료)</span> 
- like 진시황
