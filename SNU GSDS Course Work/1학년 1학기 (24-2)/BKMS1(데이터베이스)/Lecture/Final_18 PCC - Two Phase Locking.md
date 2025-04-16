남이 어떤 operation에 접근한지를 알아야 한다. 
→ When(Time), Who, What(operation), Which(Tuple)

## p.6
Lock을 얻기 위해 ==Lock Manager의 internal lock-table== 에 표시해놓는다. 
![[IMG-20241211060735.png]]
위의 상황에서 T1이 먼저 [(T1, A), RW] 로 Lock을 먼저 잡는다. 
이후에 T2도 [(T2, A), RW]로 다음 Lock을 잡기 위해 대기를 걸어놓는다. 
앞에 (T1, A) Lock이 끝나면, 기다리고 있던 (T2, A)가 Lock을 얻는다. 

==Internal Lock-Table==은 Hash Table로 구현되어 있다. 
각 Hash Table Entry는 **Linked List로 구현**되어 있다. 
따라서, 충돌이 발생하면, Entry의 마지막 순번으로 가서 순서를 기다린다. 

1. Transactions는 **lock을 request**한다. 
2. Lock manager는 해당 request를 **grants**하거나 **block**한다. 
3. 할 일이 끝난 Transaction은 **lock을 release**한다. 
   → 어 그런데? 자기 할 일 끝났다고 바로 해제 해도 되나??
4. Lock manager은 internal lock-table을 최신화한다. 

## Two-Phase Locking
### #1. Growing
- 트랜잭션이 필요한 모든 잠금을 요청하고 획득하는 단계
- 트랜잭션은 이 단계에서 새로운 잠금을 계속 요청할 수 있다. 
- <b><span style="color:rgb(229, 93, 98)">잠금 해제는 허용되지 않음</span></b>
### #2. Shrinking
- 이미 획득한 잠금만 해제 가능
![[IMG-20241211060735-1.png]]
T1은 A와 B에 대한 X-lock을 획득한 이후에
A에 대한 작업을 먼저 모두 완료한 뒤 A를 Unlock 한다. 
따라서, Growing Phase와 Shrinking Phase를 모두 지켰다. 

### SS2PL
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_18 PCC - Two Phase Locking/IMG-20250304110005-1.png]]
SSTPL에서는 Lock을 끝까지 들고 있으니, 위와 같은 그래프가 만들어진다. 
Shirinking Phase의 마지막에 할당받은 시간이 다되거나, 

### ELR: Early Lock Release
ELR에서는 Lock을 풀되, 
1. 해당 record가 위험한 record임을 표시하고
2. 이 record를 읽어 간 transaction들은 모두 coloring한다. 
위의 Cascading Aborts를 해결하기 위해, T1 중간에 들어온 다른 트랜잭션들의 작업들(T2, T3, ,,,,)을 **Coloring** 해둔다. 
추후에 Coloring 된 Transaction들은 **Parking**해두고, T1이 Commit 되는 순간에 왕창 Commit을 시킨다. 

### Non-2PL
하나의 Transaction에서 lock-unlock을 번갈아가면서 잡는다. 
### 2PL
하나의 Transaction에서 Lock을 먼저 다 잡고, 그 다음에 unlock을 하나씩 진행한다
→ 작업 중간에 Lock을 해제하지 않는다. 작업이 끝날때마다 lock을 하나씩 해제해준다. 
### Strong Strict 2PL 
하나의 Transaction이 Commit시점까지 Lock을 모두 들고 있는 것이다. 
→ 최대 안정성 보장

#### 그래서 2PL?
- grow-shrink 로 구성
- 트랜잭션 간의 순서를 보존
- Dependency Graph가 cycle을 가지지 않는다.
→ ==항상 Conflict Serializable하다!==

## Dead Lock
서로가 원하는 데이터를 접근하려다 보니, 병목현상이 생기는 것
resource에 access하는 순서가 서로 바뀌어 있을 때 문제가 발생한다. 
#### Deadlock Detection
waits-for graph에서 cycle이 생기면 deadlock이라 얘기한다. ![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_18 PCC - Two Phase Locking/IMG-20250304110005-2.png]]
Deadlock이 detect되면 Victim Selection을 통해 ==Transaction을 abort==시켜준 뒤 retry한다. 
그럼 다시 원할하게 동작한다. 
##### 그럼 누구를 Victim으로 할까?
DBMS마다 다르다. 
- by age, by progress, by # of items, by # of txns
Rollback을 어느정도 할지도 DBMS마다 다르다. 
- Partial Rollback vs. 새로 시작하기
- partial rollback이 무조건 좋은건 아니다. 꼬인게 안풀어질 수 있다. 
#### Deadlock Prevention
- Wait-Die (“old waits for young”)
- Wount-Wait (“young waits for old”)
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_18 PCC - Two Phase Locking/IMG-20250304110007.png]]
둘 다 결국에는 Lock을 한쪽 방향으로 흘러가게 잡기 위함이다. 
→==one “type” of direction allowed==

## Lock Granularities
lock이 보호하고자 하는 data object Granul의 크기에 따라서 
DB의 Concurrency Degree가 굉장히 달라진다. 

만약, lock의 범위를 entire database로 설정해버리면 Concurrency가 1이 된다. 
→ 자동으로 serializable해진다. 

반대로, 튜플 단위까지 쪼갤 수 있다. 작은 단위로 쪼갤수록 Concurrency가 높아진다. 

#### Intention Locks
Lock을 걸 때, **잠금의 의도를 상위 노드에 표시하여 충돌여부를 신속히 결정**할 수 있게 한다. 
→ 하위 노드가 많을 경우 하위 노드의 잠금 상태를 일일히 확인하는 작업이 비효율적이다. 
→ 만약 다른 누군가가 테이블을 drop하려고 할때, Lock이 튜플에 대해 작업하고 있고, 이 intention lock을 상위 노드에 marking해놨다면 detect할 수 있다. 
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_18 PCC - Two Phase Locking/IMG-20250313142327-1.png]]
- IS는 X빼고 다 가능
- IX는 IS, IX만 가능
## Lock Escalation
full table scan을 진행할 때, 
1. table level에 S를 marking
2. table level에 IS를 걸고, Tuple level에 S를 반복해서 걸어준다. 
2번이 IX를 허용하기에 Concurrency Control 관점에서는 좋은데, 
어? 이게 S가 Lock table에 너무 많이 쌓인다,,,Lock table이 너무 비대해지네?

→ 이때 ==Lock Escalation==이 발생해서 ==그냥 전체 table level에 S를 marking한다. ==

## Summary
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_18 PCC - Two Phase Locking/IMG-20250313142327-2.png]]
**Lock table latch**
- deadlock detection, prevention을 하기 위해 테이블들을 자주 왔다갔다해야 한다. 따라서, 전체 lock table을 잡아놓을 수 밖에 없다. 