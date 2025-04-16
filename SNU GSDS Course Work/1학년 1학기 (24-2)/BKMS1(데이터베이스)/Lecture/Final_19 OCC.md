PCC 때는 무슨 작업을 하더라도 lock table에 적어놓고 조심해서 동작을 해야한다. 
나는 read만 하고 싶은데, lock을 다 걸어놓고 하는 것은 불필요해 보인다. 

OCC: Timestamp를 통해 Schedule을 serialization하겠다!!

→ Tims Stamp를 언제 발급 받는지가 중요하다! order를 언제 결정할지
주로 Logical Counter를 통해 Assign을 한다. 
**time stamp를 언제 발급하느냐**가 중요한 이슈이다!!

## Basic Timpstamp Ordering (T/O)
![[IMG-20241211063826.png]]![[IMG-20241211063833.png]]
#### Violation
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_19 OCC/IMG-20250304110005-1.png]]
#### Basic T/O - Performance Issues
long running transactions는 **time stamp를 너무 일찍 받았기 때문에**, 
나중에 실행되는 transaction에 접근하지 못하게 된다. 
→ ==time stamp를 받는 시점을 미루자!!==

## Optimistic Concurrency Control
#### OCC phase
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_19 OCC/IMG-20250304110005-2.png]]
1. 해당 transaction이 할일을 다한 순간에 timestamp를 받는다. 
2. 문제가 발생하지 않는지 timestamp를 토대로 Validation을 진행한다. ![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_19 OCC/IMG-20250304110007.png]]
3. Workspace에 적어놓은 것을 실제 DB에 반영한다. ![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_19 OCC/IMG-20250313142327-1.png]]
### OCC - Read Phase
OLAP workload에서 사용 시에는 모든 것을 private space에 복제해야 하기 때문에 금지된다. 
→ OLTP Workload에 적합하다. 

### OCC - Validation Phase
time stamp를 받고, serialization order를 할당 받는다.
- Backward Validation
  already committed된 txns와 timestamp를 비교한다. 
  → 이미 완료된 txns를 갖고 있어야 하므로 overhead 때문에 사용X
- Forward Validation
	1. $T_i$의 Read, valid, write이 $T_j$ 시작 전에 다 끝났는지를 먼저 확인
	2. 현재 Validation 진행 중인 WriteSet($T_i$)와 ReadSet($T_j$)와 겹치는 부분이 있는지 확인
	   겹친다면, 임의의 Transaction 하나를 abort 시킨다. 
	3. Read 하는 시점에서의 Version을 확인![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_19 OCC/IMG-20250313142327-2.png]]
	   위의 그림의 경우 $T_2$ 가 Read하기 전에, $T_1$ 이 이미 commit 된 상태이기에 문제가 안 생긴다. 
## OCC - performance issue
OLTP Workload에서 매우 빠르게 동작한다!!

## The Phantom Problem
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_19 OCC/IMG-20250313142328.png]]
→ Inconsistent Read 발생!
#### Solution: Index Locking
DB의 index를 Lock으로 보호하여, **조건에 부합하는 데이터뿐만 아니라 조건에 따라 삽입될 가능성이 있는 위치까지 보호**하는 메커니즘이다. 

인덱스가 존재하지 않는 경우?
→ 테이블 전체를 보호해야 한다. 
1. Gap Lock
   레코드가 삽입될 수 있는 인덱스 간격을 잠금 
   → 새로운 레코드가 특정 인덱스 위치에 추가되는 것을 방지
2. Next-Key Lock
   레코드와 해당 레코드의 다음 간격을 함께 잠금
   → 현재 조건에 부합하는 레코드 뿐만 아니라, 조건에 부합하는 새 레코드가 추가되지 않도록 보호
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_19 OCC/IMG-20250416150918-2.png]]
