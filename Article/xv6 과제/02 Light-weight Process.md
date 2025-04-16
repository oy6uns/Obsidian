## LWP
기존의 process가 컴퓨터의 scheduling 단위를 추상화한 것이라면, thread는 실행 흐름을 추상화한 것이다.
process가 state를 관리한다면, thread는 
- process로부터 pid, page directory와 VM, file descriptor 등을 공유하고
- register, context, stack, thread state, tid 등을 독립적으로 관리한다. 
각각의 thread는 독립된 stack을 기반으로 instruction sequence를 실행해 나간다. 
VM을 공유하는 다른 쓰레드들과 race condition, deadlock등의 문제가 발생할 수 있다. 

### LWP 동작 과정
#### 1. **LWP 생성**
- `thread_create` 호출 시:
    1. `allocproc`를 호출하여 새로운 `proc` 구조체를 할당.
    2. `master` 프로세스와 동일한 PID를 설정하고 고유한 TID를 부여.
    3. 새로운 스택 메모리를 할당.
    4. `start_routine`을 실행하기 위한 초기화 (`eip`, `esp` 등) 설정.
    5. 상태를 `RUNNABLE`로 설정하여 스케줄러에서 실행되도록 준비.

#### 2. **LWP 실행**
- `mlfq_scheduler`에 의해 선택된 프로세스가 `RUNNING` 상태로 실행.
- 동일 그룹의 LWP는 Round Robin 방식으로 CPU를 공유.

#### 3. **LWP 종료**
- LWP 실행 중 `thread_exit` 호출 시:
    1. `retval`에 반환값 저장.
    2. 스택 메모리를 해제하거나 재사용 가능 메모리 풀에 추가.
    3. 상태를 `ZOMBIE`로 전환.

#### 4. **LWP 자원 정리**
- `thread_join` 호출 시:
    1. 특정 TID를 가진 쓰레드가 `ZOMBIE` 상태인지 확인.
    2. 자원을 정리하고 반환값(`retval`)을 호출자에게 전달.

## 디자인
1. proc.h의 proc structure를 수정한다. 실행 흐름에 관련된 부분들을 thread structure로 분리해낸다. 
2. curproc → master: 쓰레드일 경우
3. curproc → master == 0: 현재 프로세스가 가리키는 마스터 쓰레드가 0이기에 master 쓰레드를 의미한다. 

## 함수
- **`thread_exit`**: 단일 스레드 종료.
  → 현재 실행 중인 스레드를 종료 상태로 전환
- **`thread_clean`**: 좀비 상태(ZOMBIE) 스레드의 자원 해제 및 정리.
  → 종료된 스레드의 자원을 정리하고, 다시 사용할 수 있도록 초기화
  → `thread_join` 호출 후 또는 좀비 스레드 정리 시
- **`kill_threads`**: 모든 하위 스레드를 종료하거나 비활성화
  → `exec()` 호출 시 또는 전체 종료가 필요할 때

## exec.c
### Master 쓰레드일 경우 (`{cpp}curproc->master == 0`):

- Master 쓰레드를 종료할 때는 **`kill` 호출 후 `kill_threads(curproc, 1)`을 호출**
- **왜냐하면 Master 쓰레드와 모든 하위 스레드를 완전히 종료**해야 하기 때문

- `{cpp}kill(curproc->pid);        // Master 쓰레드 종료 
- `{cpp}kill_threads(curproc, 1);  // 모든 하위 스레드 정리`

### 하위 쓰레드일 경우 (`{cpp}curproc->master != 0`):

- 하위 스레드는 **Master 쓰레드가 여전히 살아 있어야 하므로, `kill`을 호출하지 않는다. 
- 대신, 현재 스레드를 **비활성화(`{cpp}kill_threads(curproc, 0)`)** 하여 실행을 중단시킨다. 

- `{cpp}kill_threads(curproc, 0);  // 현재 스레드 비활성화`


thread_exit() → ZOMBIE → thread_join() → thread_clean()



## 1. 개요
1. **MLFQ(멀티 레벨 피드백 큐) + Stride** 스케줄러
    - `mlfq.c` 내부에서 “MLFQ”를 가상의 한 ‘Stride 프로세스’로 간주한다.
    - MLFQ로 들어온 프로세스가 `set_cpu_share()`를 통해 “Stride 스케줄러”로 이동할 수도 있다.
2. **프로세스/스레드(`struct proc`) 관리**
    - `allocproc()`에서 새 프로세스(혹은 마스터 프로세스)가 할당
    - `thread_create()`에서 “Light Weight Process(LWP, 즉 커널 스레드)”를 추가로 생성
    - 스레드도 `allocproc()`을 통해 생성하지만, `tid`만 다르고 `pid`는 마스터 프로세스와 공유하게끔 구현
3. **스레드 스택 및 자원 정리**:
    - 스레드는 마스터 프로세스와 페이지 디렉토리(`pgdir`)를 공유하여 동일한 주소 공간에서 동작
    - 스레드별 별도의 커널 스택(`kstack[tid]`)을 갖는다.
    - 스레드가 종료(`thread_exit()`)하면, 최종 정리는 부모(마스터)가 `thread_join()`으로 수행하고, `thread_clean()`에서 스케줄러 큐에서 제거한다.
4. **`exit()`에서** 프로세스(또는 스레드)는 **즉시 스택 해제를 하지 않고** `ZOMBIE` 상태로 바꾼 뒤,
    - 마스터라면 자식 스레드를 모두 죽여 `ZOMBIE`로 만들고, 자식 스레드라면 마스터를 깨워 알린다.
    - 최종 해제(스택, `freevm`, etc.)는 `wait()`나 `thread_join()` 쪽에서 이뤄진다.

## 2. `mlfq.c` 안에서의 MLFQ + Stride 결합 방식
1. `mlfq_init()`
    - 내부에서 `stride_init(&this->assistive_sched)`를 호출해, _Stride 스케줄러 상태_(`stride` 구조체)를 초기화
    - **Stride 스케줄러**의 첫 번째 엔트리에 `MLFQ_PROC`(가짜 프로세스 포인터)를 넣고, 많은 티켓(`MAXTICKET`)을 배정한다. → 처음에 프로세스가 스케줄러로 들어갈 때 MLFQ로 처음 스케줄링 되게끔 
    - 즉, 기본적으로 “MLFQ”가 하나의 거대한 프로세스처럼 동작하며, Stride 관점에서는 “MLFQ_PROC vs. 다른 Stride 프로세스들”이 CPU 점유를 공유한다.
2. `mlfq_append()` / `mlfq_delete()`
    - **MLFQ 큐**(`this->queue[level]`) 배열에 `struct proc* p`를 넣거나 제거한다. 
    - 만약 `p->mlfq.level == -1`이면 “Stride로 이동한 프로세스”라 판단, `stride_delete()`로 처리한다. 
3. **`stride_append()`** (MLFQ 밖으로 빠진 프로세스가 Stride에 들어갈 때)
    - 프로세스가 `set_cpu_share()`를 통해 MLFQ를 빠지고, Stride에 들어갈 때
    - `mlfq_cpu_share()` → 내부적으로 `stride_append(...)`로 **티켓**을 부여하여 Stride 스케줄러의 큐(`this->queue[]`)에 등록한다.
4. **스케줄링 순서** (`mlfq_scheduler()` 함수)
    1. **Stride → MLFQ**: `stride_next()`로 우선 “pass 값이 가장 작은 프로세스”를 뽑음.
        - 만약 그 프로세스가 `MLFQ_PROC`라면, 진짜 MLFQ 내부에서 실제 프로세스를 `mlfq_next()`로 선택.
    2. **MLFQ 내부**: 레벨 0 ~ 2 큐 순서대로 라운드 로빈 식으로 탐색해서 RUNNABLE한 프로세스를 찾는다.
    3. 해당 프로세스를 한 번 실행(`swtch()`) 후, `mlfq_update()`에서 레벨 이동이나 `stride_update()` 등을 수행.

**MLFQ 전체가 하나의 ‘Stride 엔트리’**로 관리되면서, “MLFQ vs. 여러 Stride 프로세스”를 공정하게 CPU 점유율로 나누고, **MLFQ 내부**는 “우리가 흔히 아는 단계별(레벨별) 시간 할당과 우선순위를 내리는 등으로 라운드 로빈한다.


## 3. `proc.c`에서의 LWP(쓰레드) 처리

xv6 기본 프로세스 구조에 LWP를 추가하는 방향으로 구현

1. **`thread_create()`**
    - 내부에서 `allocproc()`으로 새 `proc` 구조체 할당 → `np->tid = nexttid++`와 같이 **새로운 tid**를 할당.
    - `np->pid = master->pid;`를 통해 **마스터와 동일한 pid** 공유.
    - 페이지 테이블(`pgdir`)도 마스터와 공유(`np->pgdir = master->pgdir;`).
    - **스택**: 사용자 영역에서 2페이지(스택 + 가드 페이지)만큼 할당(`allocuvm(...)`)하고, `np->kstack[np->tid]`에 커널 스택을 할당.
2. **`thread_exit()`**
    - “현재 쓰레드”가 종료될 때, 파일 디스크립터 등을 닫고,
    - 스레드를 `ZOMBIE`로 만들고 스케줄러에게 넘긴다.
    - 마스터 쓰레드를 깨워서(`wakeup1(curproc->master)`) “나 종료했으니 `thread_join()` 해달라”고 알림.
3. **`thread_join()`**
    - 마스터 프로세스만 호출할 수 있다. (만약 현재가 쓰레드면 -1 리턴)
    - 특정 `thread_t thread`가 `ZOMBIE` 상태가 될 때까지 기다렸다가,
    - `thread_clean()`을 호출해 스케줄러 큐에서 제거(`mlfq_delete(...)` or `stride_delete(...)`), 스레드 자원을 최종 해제한다.
4. **`thread_clean()`**
    - “MLFQ/Stride 큐에서 제거” + “사용한 스택 해제” + “`proc` 구조체를 `UNUSED`로 전환” 등을 하는 최종 정리 함수.

> [!NOTE] Title
> **마스터**는 `{cpp}“pid=N, tid=0, master=0”`
> **서브 스레드**는 `{cpp}pid=N, tid=k(!=0), master=마스터_proc`
> 
> **동일 프로세스 내 스레드라고 해서 늘 같은 MLFQ 레벨**에 있지 않음. 쓰레드가 프로세스와 동일하게 완벽히 개별적으로 동작
> 반납 시에도 프로세스와 동일하게 원하는 시점에 타이머 인터럽트나 `{cpp} yield()`로 반납함

## 4. `exit()`와 `wait()` (프로세스 종료/수거)

- **마스터 프로세스**가 `exit()`를 호출하면, 그 **하위 쓰레드**들(`master == curproc`)도 모두 죽이려고 시도하고, 자식 프로세스(이 프로세스를 부모로 둔 프로세스들)도 `initproc`으로 부모를 바꾼다.
    - 최종적으로 `curproc->state = ZOMBIE`로 만들고, **스택은 여기서 바로 해제하지 않음**.
    - 실제로는 `wait()`가 불려야 마스터의 커널 스택 등을 해제한다.
- **쓰레드**(마스터가 아닌 경우)가 `exit()`하면, “마스터를 깨워서 나를 thread_join() 해주길 요청”하고 `ZOMBIE`가 된다.

- **`wait()`** 함수:
    - 부모(마스터) 프로세스가 자식(또는 쓰레드이자 자식인 상태)을 기다린다.
    - `ZOMBIE` 상태인 자식이 발견되면, 해당 `proc`의 커널 스택을 해제, `freevm()` 호출(프로세스인 경우), 그리고 `mlfq_delete(...)`로 스케줄러 큐에서 제거.
    - 이후 `p->state = UNUSED`로 돌려놓는다.

> [!NOTE] 정리
> **“exit() → ZOMBIE → wait() → 실제 자원 해제”** 구조를 쓰레드에도 그대로 적용

## 5. 실제 스케줄링 동작 흐름

1. **CPU가 `scheduler()` 루프**로 들어간다. (`scheduler()`는 `mlfq_scheduler(...)` 호출)
2. **Stride 스케줄러**에서 `stride_next()`로 “pass 값이 최소이고 RUNNABLE인 프로세스”를 고른다.
    - “MLFQ_PROC”일 경우에는 **다시** `mlfq_next()`로 실제 MLFQ 큐에서 RUNNABLE인 프로세스를 하나 뽑는다.
3. 선택된 프로세스(혹은 스레드) `p`를 `c->proc`로 세팅하고, `p->state = RUNNING` 후 `swtch()`로 **유저 공간**에 들어간다.
4. **프로세스가 일정 시간**(타이머 인터럽트 발생 전까지) 실행 후,
    - 인터럽트가 발생하면 `yield()` → `p->state = RUNNABLE` → 다시 스케줄러로 돌아와
    - `mlfq_update()`가 레벨 이동(혹은 Stride 업데이트)을 하거나, `mlfq_next()`에서 다른 프로세스를 뽑는다.
5. **스레드**도 “개별적인 `struct proc`”이므로, **RUNNABLE** 상태라면 다른 프로세스와 동일하게 MLFQ/Stride 안에서 돌아가며 실행된다.

---

# 6. 요점 정리 (한눈에 보는 동작 시나리오)

1. **프로세스 생성**: `allocproc()`으로 `proc` 할당 → MLFQ에 `mlfq_append(&mlfq, p, 0)` → RUNNABLE → 스케줄러가 MLFQ에서 해당 프로세스를 꺼내 실행.
2. **스레드 생성**: `thread_create()`
    - `allocproc()`을 호출해 새 `proc` 구조체 준비, `tid`만 다르게.
    - 주소 공간(`pgdir`)은 마스터와 공유, user‐level stack은 추가 할당.
    - MLFQ에도 동일하게 삽입되므로, 스레드 역시 독립적으로 CPU 스케줄링을 받음.
3. **스레드 종료**: `thread_exit(retval)`
    - 파일 닫기, `retval` 저장, `ZOMBIE` 상태 전환 → 스케줄러로 감.
    - 마스터가 `thread_join(thread, &retval_ptr)`를 부르면 그 스레드를 찾아서 `thread_clean(...)`으로 스택/스케줄러 큐 제거.
4. **프로세스 종료**: `exit()`
    - 마스터면 “하위 스레드 다 죽임” + 자기 자신도 ZOMBIE 전환 → 부모(또는 init)가 `wait()`해서 최종 해제.
    - 스레드면 “마스터를 깨움” 정도로 끝.
5. **Stride로 이동**: `set_cpu_share(percent)`
    - MLFQ에서 제거(`mlfq_delete()`) 후, `stride_append()`로 Stride 배열에 등록.
    - 이후에는 “pass‐value” 기반의 Stride 스케줄링으로 CPU를 할당받는다.



## 버그 및 수정
1. **스레드가 `exit()` 시점에 스택을 `kfree()`하고**, 이후 `thread_join()` → `thread_clean()`에서 다시 `kfree()`를 하므로 **이중 해제(double free) 버그** 발생
   → `exit()`시점에는 수거 안하게끔 변경
2. `wait()`시에 기존에는 프로세스와 스레드의 수거를 동시에 진행했는데, 이제는 p→tid !=0 일 경우에는 바로 skip하게끔 구현 → **중복 수거를 방지**
3. 기존에는 동일한 프로세스 내의 쓰레드는 동일한 레벨에서 관리되게끔 구현했었는데, 
   이제는 동일한 프로세스 내의 쓰레드라도 다른 레벨에서 관리될 수 있다. 완전히 개별적으로 동작


time quantum 공유 → master 든 child 든 다 동일한 time quantum

list에 제거된 쓰레드를 담고
master 쓰레드가 List를 순회하면서 처리

비추) array에 제거된 위치 Marking 후에 구현


1. 할당한 메모리가 PGSIZE에 위반되거나 
2. free 시키려고 한 주소가 scope 내부에 들어오지 않거나 
3. `v < end`


