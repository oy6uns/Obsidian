![[IMG-20241209194826.png]]
## Normalization
- **각 테이블**에서 추출되는 **정보를 referencing**하게 만들어서 (with foreign key constraints) **redundant infomation을 제거**하는 데에 있다. 
==join이 왜 필요한지, join을 줄일 수 있는지, join을 줄이면 뭐가 안 좋은지==… 등을 공부해야한다!!

## 14.1 Join Algorithms!
일반적으로 **작은 테이블을** 왼쪽 테이블(outer table)로 두는 것이 성능에 유리하다
- 조인 알고리즘(ex: Nested Loop Join, Hash Join)에서 작은 테이블을 활용하여 큰 테이블을 탐색하거나 필터링하는 경우가 많기 때문이다!
### 2 Design Decision
<b><u>#1. Output</u></b>
- **Early Materialization**: 전체 ==Data==를 계속 들고 다니기
  → original data를 다시 볼 필요가 없다. 그러나, 중간 결과물이 크면 비효율적이다. 
  → 결과물의 크기를 예측 과정을 **”Cardinality Estimation”** 이라고 한다. 
- **Late Materialization**: ==Record Id==만 들고 다니기
  → 나중에 필요할 때만 table에 직접 접근해서 데이터를 들여다볼게!
  → I/O가 한 번 더 발생한다. ![[스크린샷 2024-12-09 오후 8.05.41.png]]
<b><u>#2. Cost Analysis Criteria</u></b>
**Cost Metric**: ==page I/O의 횟수!!==
를 통해 어떤 Join을 시행할지를 정한다. 
→ page I/O가 중요한 것이다!! tuple I/O 를 하나하나 보면 안된다~!!

## 14.2 여러가지 Join Algorithms
- Nested Loop Join
	- Simple / Stupid
	- Block
	- Index
- Sort-Merge Join
- Hash Join
## Nested Loop Join
#### #1. Stupid Nested Loop Join
두 테이블 R과 S를 Join할 때, R의 모든 튜플에 대해 S의 전체를 탐색한다. 

![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_14 Join Algorithm/IMG-20250304110005-1.png]]
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_14 Join Algorithm/IMG-20250304110005-2.png]]
#### #2. Block Nested Loop Join
두 테이블 R과 S를 Join할 때, R의 ==모든 튜플을 block 단위로 묶어서== 이에 대해 S의 전체를 탐색한다.
- ==Buffer의 크기가 딱 M과 동일==하다면?![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_14 Join Algorithm/IMG-20250304110007.png]]
- ==Buffer의 크기가 outer table을 모두 담을 만큼 충분==하면, Block Nested Loop Join은 아직도 **가장 효율적**일 수 있다.![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_14 Join Algorithm/IMG-20250313142327-1.png]]
- 그러나, Buffer의 크기가 한정적이라면, **M개의 outer page**를 **B-2개의 Buffer**들로 반복해서 scan을 해주어야 한다. ![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_14 Join Algorithm/IMG-20250313142327-2.png]]
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_14 Join Algorithm/IMG-20250313142328.png]]
테이블의 사이즈가 비슷하면 그냥 Join을 하면 되지만, 
한쪽 **테이블의 사이즈가 너무 작다면** **index를 사용하는 것이 효율적**일거 같은데?
→ 그래서 나온 것이 Index Nested Loop Join이다!
#### #3. Index Nested Loop Join
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_14 Join Algorithm/IMG-20250416150918-2.png]]
- $C$ : S 테이블의 인덱스를 통해 탐색하는 비용
- R 테이블에 있는 총 튜플 수는 m이기 때문에, **각 튜플마다 S 테이블의 인덱스를 탐색하는 비용**은 
  $m*C$가 된다!
###### 단점
- S 테이블에 인덱스가 없으면 사용할 수 없다!
- 인덱스를 통해 S 테이블을 랜덤하게 탐색하기 때문에 디스크 랜덤 접근이 많아질 수 있다. 

> [!random access] index random access
> Index를 통해 테이블의 특정 데이터를 찾는 것은 **“논리적이고 효율적인 정확한 검색”** 아닌가요?
> **물리적 디스크 관점**에서 생각해보면 인덱스를 통해 랜덤하게 접근하는 것처럼 보인다!!

## Sort-Merge Join
1. both table을 key에 따라 sorting한다. 
   → External Merge-sort를 사용할 수 있다. 
2. sorted된 key들에서 순서대로 하나씩 비교하며, 동일한 key를 가지면 Merge한다. ![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_14 Join Algorithm/IMG-20250416150918-3.png]]![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_14 Join Algorithm/IMG-20250416150919.png]]
만약, 양쪽의 튜플들이 모두 동일한 값을 가지는 최악의 경우에 **Merge Cost가** $MN$이 된다. ![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_14 Join Algorithm/IMG-20250416150919-1.png]]
→ 만약 한쪽, 또는 **양쪽 테이블**이 join key에 대해 **already sorted** 되있다면, sort cost가 없기 때문에 아주 효율적이다!

## Hash Join
1. **Build**: outer relation의 원소들을 **hash function** $h_1$을 통과시켜 hash table을 build한다. 
2. **Probe**: inner relation을 scan하면서, 동일한 **hash function** $h_1$을 통과시킨 값이 hash table에 있다면, 두 tuple을 matching 시킨다. ![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_14 Join Algorithm/IMG-20250416150919-2.png]]
#### Hash Table의 Value
- **#1. Full Tuple을 모두 저장**
	외부 관계(outer relation)의 **전체 튜플을 해시 테이블에 저장.**
	메모리의 많은 공간을 필요로 함
- **#2. Tuple Identifier**
	Tuple의 identifier만 저장한다. 
	→ 해당 identifier는 기본 테이블(base table) 또는 쿼리 계획의 하위 연산자에서 생성된 중간 출력값을 가리킨다. 
#### Probe Phase Optimization - Bloom Filter
- 쓰레드는 probe 전에 bloom filter를 통과시켜 필요 없는 tuple들을 join 전에 먼저 제거한다. 
- **Bloom Flter**는 **CPU 캐시에 적합한 크기로 설계**되어 조회 속도가 빠름.
#### Hash Join Cost
버퍼 하나는 DIsk I/O로 사용해야 하기 때문에 
버퍼 크기가 $B$라고 하면, 파티션 하나의 최대 크기는 $B-1$이다. 

Hash function의 Entry가 $B$개 이고, 파티션 하나의 최대 크기가 $B-1$이기에, 
총 Cost는 $B*(B-1)$이다. 

→ table이 $N$개의 페이지를 가진다면, 일반적으로 하나의 파티션이 $\sqrt{N}$개의 버퍼를 필요로 한다. 
→ hash의 분포가 고르지 않다는 것을 고려해서 “fudge factor” $f>1$ 를 곱해주면 
→ 총 Cost는 $B*\sqrt{f*N}$이 나온다. 

그러나, 만약 전체 <span style="color:rgb(229, 93, 98)">hash table이 memory에 다 들어가지 않는다면</span>, 
<span style="color:rgb(229, 93, 98)">buffer pool manager가 hash table page를 random하게 swap</span>해버릴 수 있다. 

## Grace hash join (+Recursive partitioning)
#### Grace Hash Join
1. 양쪽 테이블에 동일한 hash function을 적용하고, 이를 partition으로 저장한다. 
2. 각 테이블의 일치하는 partition간에 join을 수행한다. ![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_14 Join Algorithm/IMG-20250416150920.png]]
해시 테이블이 너무 커서 메모리에 올라가지 않는 경우, table을 작은 partition으로 나누기에 이를 방지할 수 있디. 

**Recursive Partitioning**: 만약, **partition도 memory에 fit하지 않는 경우** 재귀적으로 ==새로운 hash함수를 사용==하여 partition을 더 작게 나누는 방법으로 해결할 수도 있다. 
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_14 Join Algorithm/IMG-20250416150920-1.png]]
만약 Recursive Paritioning이 필요하지 않은 경우, Cost는 **3(M+N)**이 된다. 
- Partition phase에서 **양쪽 테이블을 read, write**하는데 **2(M+N) I/O**가 요구되고, 
- Probe phase에서 **양쪽 테이블의 모든 partition**에 대해 read작업이 발생하기 때문에, **M+N** I/O가 요구되기 때문이다.
## 정리
![[스크린샷 2024-12-10 오후 4.21.03.png]]