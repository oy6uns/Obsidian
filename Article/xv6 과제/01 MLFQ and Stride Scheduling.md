### #1. MLFQ
- 상위 priority queue에 any process가 있다면, 하위 queue는 상위 queue가 empty가 되기 전까지는 선택되어서는 안된다. 
- default ==tick== in xv6 is about ==10ms==.
- 각 Level의 queue는 아래의 time quantum을 가진다. 
	각 프로세스가 ==CPU를 차지할 수 있는 단일 실행 단위의 시간==
	- highest: 1 tick
	- Middle: 2 ticks
	- Lowest: 4 ticks
- 각 queue는 다른 time allotment를 가진다. 
	프로세스가 해당 우선순위 큐 내에서 사용할 수 있는 시간의 상한선. ==이 시간이 초과==되면 프로세스는 ==더 낮은 우선순위 큐로 이동==한다. 
	- highest: 5 ticks
	- Middle: 10 ticks
- 낮은 우선순위 프로세스가 CPU 자원을 못얻을 때, 우선순위는 상향시키는 유일한 방식으로 Priority Boosting 사용
	- 100 ticks 마다 한 번 우선순위를 상승시킴
- 최소 20% CPU 사용량을 보장함으로써 시스템의 공정성을 유지하고 응답성을 개선
### #2. Stride Scheduling
프로세스가 새로 생성되면 처음에는 MLFQ 스케줄러로 들어간다. 
대기열에 있는 프로세스에서 요청된 CPU 점유율의 총합은 CPU 시간의 80%를 초과할 수 없다. 
만약 `{cpp}set_cpu_share()`호출 시 해당 프로세스는는 Stride Scheduling로 관리됨. 
### 구현해야할 syscalls
- yield: 다음 프로세스에게 cpu를 양도
	- `{cpp}int sys_yield(void)`
- getlev: 현재 프로세스의 ready queue의 level을 반환
	- `{cpp}int sys_getlev(void)` → (0/1/2)
- set_cpu_share: CPU 점유율을 요청
	- `{cpp}int sys_set_cpu_share(void)`
	  커널 공간에서 CPU 점유율을 요청하는 함수
	- `{cpp}int set_cpu_share(int)`
	  유저 공간에서 CPU 점유율을 요청하는 함수

## 전반적인 코드(동작) 이해
### 프로세스 생성
```cpp
int pid = fork(); 
if (pid == 0) { // 자식 프로세스 
	exit(); 
} else { // 부모 프로세스 
	wait(); 
}
```
1. 부모가 자식을 생성
2. 자식 프로세스가 `exit()`을 호출
	- 자식의 상태가 `ZOMBIE`로 변경된다. 
	- 부모를 깨우기 위해 `wakeup1()`이 호출된다. 
3. 부모 프로세스가 `wait()`에서 깨어남
	- 부모는 `ptable`에서 종료된 자식을 확인하고
		- 자식의 리소스를 정리, 자식의 PID를 반환
### 스케줄러의 동작
1. `main` - `mpmain` - `scheduler` 호출
   `scheduler`는 runnable한 process를 찾을 때까지 무한루프를 돈다. 
2. runnable한 process를 찾으면 현재 c의 proc을 해당 프로세스의 포인터로 context switching한다. 
#### 기존의 RoundRobin Scheduler
매 10ms마다 `vector.S` → `{cpp}jmp alltraps` → `{cpp}call trap` → `trap.c`
`trap.c`의 `{cpp}trap`함수에서 여러 인터럽트를 처리한다. 
타이머 인터럽트 시
- `{cpp}yield()`를 호출해 CPU를 양보한다. 
- 스케줄러가 실행되며 다른 프로세스를 실행한다. 
### 프로세스와 스케줄러의 동작
1. **프로세스 생성**:
    - 부모가 `fork()`를 호출 → 자식 프로세스 생성 → `RUNNABLE` 상태로 준비.
2. **스케줄러 실행**:
    - 스케줄러가 `RUNNABLE` 상태인 프로세스를 실행.
3. **프로세스 실행**:
    - 프로세스가 자신의 작업을 수행.
    - 필요하면 시스템 호출(`fork`, `exit`, `wait`)을 통해 운영체제와 상호작용.
4. **프로세스 종료**:
    - 작업이 끝난 프로세스는 `exit()` 호출.
    - 상태를 `ZOMBIE`로 변경하고 부모에게 알림.
5. **부모가 자식 종료 회수**:
    - 부모가 `wait()` 호출 → 자식 프로세스 리소스를 정리 → PID 반환.
![IMG-20250116051856.png](IMG-20250116051856.png)
## 함수 구현
### syscall 하나 추가 시 고려해야할 파일 → 총 5개 

#### 1. 커널 코드 수정
`mysyscall`을 추가 시
1. **`syscall.h`에 시스템 콜 번호 추가**:
    `{h}#define SYS_mysyscall 22`
    (기존 시스템 콜 번호의 마지막 번호에 +1 추가)
2. **`syscall.c`에 시스템 콜 함수 테이블 추가**:
    `{cpp}extern int sys_mysyscall(void);`
    시스템 콜 테이블에 추가:
    `[SYS_mysyscall] sys_mysyscall,`
3. **`sysproc.c`에 시스템 콜 함수 구현**:
```cpp
int sys_mysyscall(void) {     
	cprintf("Hello from mysyscall!\n");     
	return 0; // 반환값은 사용자 모드로 전달 
}
```
4. **`usys.S`에 시스템 콜 정의 추가**:
    `SYSCALL(mysyscall)`
---
#### 2. user 모드 wrapper 함수 추가
사용자 프로그램이 시스템 콜을 간단히 호출할 수 있도록 wrapper 함수를 추가
1. **`user.h`에 함수 선언**:
    `{cpp}int mysyscall(void);`
---

## 고려 사항들
1. trap.c 에서 timer interrupt에 따라 프로세스의 반환을 보장하고자 할 때, 
	- mlfq는 시간 quantum단위로 보장을 해주어야 하기 때문에 중간에 가로채가서는 안된다. 
	- 그러나, stride scheduler는 pass라는 단위를 통해 중간에 interrupt가 생겨도 그 값이 보존되므로 상관이 없다. 
2. stridescheduler, mlfq scheduler 설계시에 cpu time을 어떻게 분배할지에 대한 아이디어 
##### 사소한 이슈
1. 인터럽트 허용하는 sti() 함수를 기존의 proc.c에서 옮겨오면서 실수로 지워서 계속 안되는 문제가 발생
2. user-level function에서 kernel-level function으로 옮겨갈 때 switchuvm, switchkvm, trap-> 이제는 이해됨