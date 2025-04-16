## p. 10 Executing with locks
hash table 에 접근하는 것도 concurrency control이 필요하다. 
따라서, DB는 hash table 전체를 protect해버린다. 

?? hash table은 natural parellelism이 있기 때문에 slot latch 써도 되는거 아니에요?
하나의 hash entry만 바라보는게 아니라, dead lock에 엮이는지도 체크해야되기 때문에
여러 hash entry를 방문해야한다. 

## p.11 
==unlock을 일찍 해제하면 안된다. ==
→ 잘 모르겠어요
+ 풀었다가 다시 잡는 것도 하면 안된다. 
  → 중간에 누군가가 들어오기 때문에 

하나의 transaction에서 begin 하고 commit할 때까지 시행되는 두 번의 `read` 값이 달라지기 때문에 문제가 발생한다.

## p.14 Two-Phase Locking
대부분의 DB는 TPL로 작동한다. 

## p.20 2PL - Cascadking Aborts
![[IMG-20241118132733.png]]
T1 transaction에서 A에 대해서는 변경이 끝난 것 같아서, **Unlock**을 시켰다. 
그러나, B에 대한 작업을 이후에 진행하다가 잘못한 것 같아서 **Abort**를 시켰다.

그러나, T1에서 **Abort**를 시켯기에, 기존에 변경된 A의 값은 다시 원상복구가 되어야 하는데, T2에서는 변경된 A 값을 전달받아 **Write**를 실행하려고 한다. 

따라서, T2의 작업은 쓸모가 없는 작업이기에 이를 **Cascading Aborts**라 한다. 

## p. 21 2PL Observation
### ERL
위의 Cascading Aborts를 해결하기 위해, T1 중간에 들어온 다른 트랜잭션들의 작업들(T2, T3, ,,,,)을 **Coloring** 해둔다. 
추후에 Coloring 된 Transaction들은 **Parking**해두고, T1이 Commit 되는 순간에 왕창 Commit을 시킨다. 
![[IMG-20241118133515.png]]
문제는 해결이 됐지만, 오랫동안 Lock을 계속 잡고 있어야 하기에 성능은 느려진다. 
## p.25, 26, 27 Non-2PL Example vs. 2PL Example
==다시 공부해보기==

## p.37 DeadLocks
1. 주어진 Resource가 있을 때, Resource를 access하는 순서를 한쪽 방향으로 고정하면 
   ex) A B C D 가 있을 때, A, B에 접근하면 항상 A→B의 고정된 순서로 할당
   → `ordered locking`
## p. 40 DeadLock Detection
**waits-for graph**를 생성

## p.44 DeadLock Handling
위에 내용 다시 공부해야함!!
victim selection
### RollBack Length
무조건 patial rollback이 좋은 것은 아니다. 

## p. 47 DeadLock Prevention
wait된 상황에서 
priority를 가지는 기준은 transaction id를 기준으로 한다. 
### Wait-Die
나이든 transaction이 기다리는 경우
### Wound-Wait
젊은 transaction이 기다리는 경우

## p. 52 Lock Granularities
- Lock Table에는 Granularity에 따라 lock을 다 나눠놓는다. 
  → Granular Locking
이 작업을 위해 도입된 Intention Locks

## p. 55 Intention Locks
큰 단위의 database content에 대해서 작업을 할 때, 
작은 단위의 database content에 대한 작업들을 알 수 있게 해야한다. 

T1 | A | IX
T1 | R | X

그러기 위해 작은 단위의 database content에서 작업을 할 때, 
큰 단위의 node에게 알려주어야 한다. 

**그러면, Table Drop과 같은 일을 할 때, Drop을 해야하는지에 대해 모든 page와 record에 대해 검사하는 일을 생략 할 수 있다!!**

![[Intention Locks]]
==Intention Locks 3가지 종류 다시 공부해야함!!==

## Compatibility Matrix
위에서 말한 