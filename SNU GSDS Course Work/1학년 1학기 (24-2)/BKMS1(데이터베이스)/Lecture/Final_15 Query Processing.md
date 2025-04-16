**query processing**이란, 데이터베이스로부터 데이터를 꺼내오는 일련의 작업이다. 
==고수준의 query문==을 ==파일 시스템의 물리적 단계에서 사용할 수 있는 표현식==으로 **변환**하는 것. 
## 15. 1 개요
**기본적인 단계**
1. 파싱과 변환
2. 최적화
3. 평가

질의 처리를 위한 일련의 기본 연산을 **query execution plan**이라 부른다. 
**query execution engine**은 plan을 받을이고, 계획을 수행한 후, 질의에 대한 결과를 넘겨준다. 

주어진 하나의 query에 대한 서로 다른 query execution plan은 서로 다른 비용을 가지게 된다. 
사용자가 작성하는 **query의 query execution cost를 최소화**하는 ==execution plan을 짜는 것이 시스템이 해야할 일==이다. 

- query plan이 선택되면
- query는 해당 plan을 통해 평가가 되고
- plan의 결과가 query의 결과가 된다. 

query optimization을 위해 **query optimizer**는 각 연산의 비용(대강 비용을 추정)을 알고 있어야 한다. 
15장(query execution)에서는 **각 연산을 평가하는 방법**과 **연산의 비용을 추정하는 방법**을, 
16장(query optimization)에서는 그 **연산을 최적화하는 방법**을 배운다. 

## 15.2 query execution cost
> 디스크 접근 횟수, query 수행 중 CPU 사용 시간 등,,,

- 이 장의 모든 알고리즘의 비용은 메인 메모리 내의 버퍼 크기(M)에 의존한다. 

## 15.3 Plan processing model
DBMS가 쿼리를 어떻게 실행하는지를 정의
→ 각각의 workload에 따라 다양한 절충안을 제공
#### #1. Iterator Model
- 튜플 단위 처리**방식을 정의한다. 
- query plan의 각 연산자는 **Next()** 라는 함수를 구현한다. 
- 연산자는 부모 연산자로부터 **Next()** 호출을 받고, 자신의 하위 연산자들에게 **Next()** 를 호출하여 데이터를 가져온다. 
- 처리가 완료되면 상위 연산자로 전달된다. 
- 각 실행계획의 연산자는 **Cursor**(현재 작업 위치를 기억)를 가지고 있다. 
- **==파이프라인 형태**==로 데이터를 처리(=**Volcano 모델**로도 불림)
→ 메모리 효율성이 높고 간단하지만, <span style="color:rgb(229, 93, 98)">반복적인 함수 호출로 인해 성능에 제약</span>이 있다. 
→ <span style="color:rgb(229, 93, 98)">특정 연산자</span>들은 <span style="color:rgb(229, 93, 98)">하위 연산자가 모든 튜플을 반환</span>할 때까지 <span style="color:rgb(229, 93, 98)">기다려야(block) 한다</span>. 
- ==PostgreSQL 사용==
#### #2. Materialization Model
- All-at-once; 각 연산자는 입력 데이터를 모두 받아서 처리한 뒤, 결과를 **완전한 데이터 집합**으로 상위 연산자에게 전달한다. 
- 전체 결과를 한 번에 반환
  결과는 NSM(N-ary, **전체 튜플**) 또는 DSM(Decomposed, **열의 부분집합**)
- 중간 결과를 완전한 데이터 집합으로 저장해야함, 따라서 한 번에 처리해야 할 데이터의 양이 적은 ==OLTP에서 적합==
- OLAP는 대량의 데이터를 처리하며 중간 결과가 매우 크기 때문에 비효율적
#### #3. Vectorized Model
- 각 연산자는 **Next()** 함수 호출 시 하나의 튜플이 아닌 **batch 단위의 튜플 집합**을 반환
  → CPU 효율성을 극대화, 반복적인 함수 호출 비용 줄임
- ==SIMD(Single Instruction, Multiple Data)==: 하나의 명령어로 여러 데이터 요소를 동시에 처리할 수 있도록 설계된 병렬 처리 기술(벡터 단위로)
- 배치 크기가 **크면 효**율적이지만, **메모리 부담이 증가**할 수 있음
- 배치 크기가 **작으면 메모리 부담은 적지만**, **성능이 저하**될 수 있음
- ==OLAP, HTAP 쿼리==에 이상적으로 동작
- ==DuckDB 사용== 

### Plan Processing의 두 가지 접근 방식
#### #1. Top-to-Bottom
- 루트 연산자(root)에서 시작하여 하위 연산자(child)로 데이터를 요청
- 루트 연산자는 하위 연산자의 `Next()`함수를 호출하여 한 번에 튜플 단위로 데이터를 가져온다. 
###### 단점
1. 각 `Next()` 호출이 반복되므로 함수 호출 비용이 누적된다. 
2. CPU 캐시에서 함수 호출마다 새로운 데이터를 가져오려 하면서 캐시 미스(Cache Miss)가 자주 발생한다. 
   → 상위 연산자가 필요할 때마다 데이터를 요청 하기 때문에, **데이터 접근 패턴이 비연속적**
   → CPU prefetching을 어렵게 만든다. 
#### #2. Bottom-to-Top
- 리프 연산자(leaf)에서 시작하여 상위 연산자로 밀어올리는 방식
- 하위 연산자는 데이터를 준비한 후 상위 연산자로 전달
- CPU 캐시 효율성이 높아지고, 반복적인 함수 호출 오버헤드가 감소한다. 
###### 단점
1. 메모리 사용량 증가(하위 연산자가 모든 결과를 준비해야 하므로)
2. 구현이 복잡하다. 

## 15.4 Access Method
DBMS에서 테이블에 저장된 데이터를 접근하고 검색하는 방식을 정의
relational algebra에서 정의된 개념 ❌ 
물리적으로 데이터를 효율적으로 읽고 검색하기 위해 DBMS가 사용하는 방법✅
#### #1. Sequential Scan
테이블의 모든 페이지와 튜플을 순차적으로 읽는 접근 방식
👍🏻 index가 sort되어 있는 상황에서 범위를 가져올 때
👍🏻 where 절에 대응되는 데이터가 거의 전체 데이터일 때
👍🏻 CPU 캐시 및 디스크 캐시를 효과적으로 사용 가능
##### 최적화 기법
1. **Zone Map**
   테이블의 **각 데이터 페이지에 대해 요약 정보**를 유지(MIN, MAX, AVG, SUM, COUNT)
   쿼리 실행 시 ==해당 페이지가 쿼리 결과에 포함되어 있지 않으면 스킵할 수 있어 효율적이다==. 
   → OLAP query에서 효과적으로 동작
   - Zone Size를 너무 크게 잡으면 skipping 될 확률이 낮아지기에 ==적당한 크기의 Zone Size==를 잡아서 sequential scan을 가속화할 수 있게 해야한다!
2. **Late Materialization**
   데이터를 최소한으로 필요할 때까지 **실제로 결합하지 않고 지연**시키는 방식
   실제 데이터(열 값)를 가져오지 않고, ==조건을 만족하는 행의 인덱스/오프셋 정보==만 유지
   불필요한 데이터를 일찍 읽지 않음으로써 **메모리와 처리 효율을 극대화**한다. 
   → DSM(Decomposed Storage Model)에서 효과적
#### #2. Index Scan
테이블에 정의된 인덱스를 사용하여 특정 조건을 만족하는 데이터만 빠르게 검색하는 방법
👍🏻 전체 테이블을 읽는 것보다 빠름
###### 단점
- 인덱스를 생성하고 관리하는 데 추가적인 비용 발생
- 작은 테이블에서는 Sequential Scan보다 오히려 느릴 수 있음
  index scan: **두번의 I/O 작업** (1) 인덱스 읽기, (2) 테이블 읽기 
  sequential scan: **한번의 I/O 작업**으로 완료 (1) 테이블 전체 읽기

> [!Sequential vs. Index] Sequential vs. Index
> - 테이블 크기
> 	- 작은 테이블 → **Sequential Scan 유리**
> 	- 큰 테이블 → 조건에 따라 달라짐
> 		- 조건을 만족하는 데이터 적음 → **Index Scan 유리**
> 		- 조건을 만족하는 데이터 많음 → **Sequential Scan 유리**
> - 인덱스
> 	- 인덱스가 없는 경우 → **Sequential Scan**
> 	- 인덱스가 있지만 조건이 비효율적 → **Sequential Scan**
> 	- 인덱스가 있고 조건이 효율적 → **Index Scan**
#### #3. Multi-Index Scan
a.k.a. **Bitmap Scan**(PostgreSQL)
- 여러 조건을 가진 쿼리를 최적화 하는데 유용
- 조건 A, 조건 B의 쿼리 결과에 대해 **Bitmap 계산** 수행 (hash table, bloom filter 사용 가능)
👎🏻 **조건이 단순**하거나 **작은 데이터 셋**에서는 Sequential Scan 보다 느릴 수 있다. 

## 15.5 Modification Queries
쿼리 조건을 확인하는 도중에  
`INSERT`, `UPDATE`, `DELETE`와 같은 operator가 발생할 수 있다. 

갱신된 데이터가 다시 조건에 포함되지 않도록 해야 한다. 
#### Halloween Problem
일부 튜플의 값이 변하고, 튜플의 위치가 재 정렬된다면 
스캔이 이미 처리한 튜플을 다시 만나게 되는 문제가 발생할 수 있다. 
튜플의 값이 변경될 때, 
1. 변경된 값의 ==포인터를 따로 표시==
2. ==따로 저장==

## 15.6 Expression Evaluation
#### Expression Tree
`WHERE`절은 내부적으로 **Expression Tree**라는 구조로 표현된다. 
```sql
ON R.id = S.id
WHERE S.value > 100
```
`ON`과 `WHERE`절을 기반으로 아래와 같은 트리가 생성된다. 
![[스크린샷 2024-12-09 오후 6.01.09.png]]
- Current Tuple: 쿼리가 현재 처리 중인 record → : `(123, 1000)` (튜플 ID와 값)
- Query Parameters: 실행 시 외부에서 전달된 값 → `100`
#### 전통적인 DBMS 평가 방식
- 일반화된 평가 함수를 사용하여 모든 함수를 처리한다. 
- 다양한 쿼리를 처리할 수 있도록 설계되었지만, 쿼리마다 조건을 해석하고 실행하는데 추가적인 오버헤드가 발생한다. 
  ex) `WHERE 1=1`: 따로 Tree 구조로 설계할 필요 없이 TRUE라는 사실은 누구나 안다. 
#### JIT Compilation
1. **JIT 컴파일**은 쿼리가 제출되면, 쿼리의 구조와 조건에 맞는 **특화된 평가 코드**를 생성한다. 
   분기(branch)와 같은 불필요한 로직을 제거한다. 
2. **LLVM**과 같은 기술을 사용하여 **런타임에 효율적인 코드를 생성**한다. 
ex) 쿼리 예제
```sql
SELECT employee_id, name, salary
FROM employees
WHERE department = 'Engineering'
  AND experience > 5
  AND calculate_bonus(salary, experience) > 5000
  AND EXTRACT(YEAR FROM hire_date) > 2015;
```
##### JIT 적용 전
- 모든 조건(AND, OR 등)에 대해 **일반화된 평가 함수**를 호출.
- 내부적으로 트리를 탐색하며 각 조건을 평가 → 많은 분기(branch)와 불필요한 연산 발생
##### JIT 적용 후
- 위 조건을 평가하기 위한 **특화된 함수**를 생성. → 최적화된 실행 경로를 제공
  **LLVM API**를 통해 **각 step에 적합한 LLVM 코드를 런타임에 생성**
- 불필요한 트리 탐색이나 분기를 제거하고, 필요한 연산만 수행.
	❌ 그러나, JIT를 사용하는 것이 항상 빠른 것만은 아니다.
	**코드를 추가적으로 생성하는데에 오버헤드가 발생**한다. 

## 15.7 Query Execution with Multiple workers
위의 내용까지는 **query execute**를 **single worker**로 처리하였다. 
이제는 **multiple worker**를 사용하여 query execute를 어떻게 할지 다룰 것이다. 

#### Parallel DBMS vs. Distributed DBMS
**Parallel**
- 데이터를 나누어 여러 프로세서/코어에서 병렬로 처리
- 리소스가 물리적으로 가깝게 배치되어 있다. (서버, 스토리지가 같은 데이터센터에 배치)
- ex) 데이터 웨어하우스, OLAP(Online Analytical Processing)
→ 동일한 데이터센터 내에서 ==대용량 데이터를 병렬로 처리하여 성능을 극대화== 
**Distributed**
- 물리적으로 서버가 다른 지역에 위치한다. 
- 데이터를 여러 위치에 분산 저장, 장애 대비 복제본을 유지
- ex) 클라우드 기반 데이터베이스(Google Spanner, Amazon Aurora)
→ 글로벌 환경의 데이터에서 ==가용성, 확장성, 장애 복구를 중점==적으로 고려
#### Process Models
1. Process per DBMS Worker
   - **각각의 Worker가 독립된 OS 프로세스**이다. 
   - 요청마다 새로운 프로세스가 생성된다
   - **OS 스케줄러를** 통해 **Worker들이 스케줄링**된다. 
   - 프로세스가 Crash 하더라도, ==**프로세스 독립성**으로 인해 **다른 Worker들은 계속 작업을 수행**할 수 있다. ==
   ![[스크린샷 2024-12-09 오후 6.29.24.png]]
2. Process Pool
   - 요청마다 새로운 프로세스를 생성하는 대신, 일정 수의 프로세스를 미리 생성하여 **pool**에 유지한다. 
   - 프로세스를 매번 생성/종료할 필요가 없어, **프로세스 생성 및 컨텍스트 스위칭 비용이 줄어든다.**    ![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_15 Query Processing/IMG-20250304110005-1.png]]
3. Thread per DBMS Worker
   - DBMS 자체에서 스케줄링을 관리하며, OS 스케줄러 의존성이 낮아진다. 
     → 자동적으로 context switch overhead가 적어진다. 
   - ==쓰레드 충돌이나 크래시가 발생하면 전체 시스템에 영향을 준다. ==
   - shared memory에 대한 관리가 필요 없다. ![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_15 Query Processing/IMG-20250304110005-2.png]]
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_15 Query Processing/IMG-20250304110007.png]]
즉, 오른쪽으로 갈수록 **성능(컨텍스트 스위칭 감소)이 좋아지고, 구현이 단순해지지만**
**안정성이 떨어진다**(스레드 하나만 crash되면 프로세스 전체가 멈춤)
##### 그러나, 요즘 나오는 대부분의 DBMS는 쓰레드 기반으로 동작한다. 
쓰레드 기반 시스템은 **컨텍스트 스위칭 오버헤드 감소** 및 **공유 메모리 관리 간소화**와 같은 이점을 제공하기 때문에, 현대 DBMS에서는 표준적인 설계 방식으로 자리 잡았다. 

> [!thread per worker?] thread per worker!!
> 후에 나오겠지만, **Thread Per Worker 모델은 여러 독립적인 요청을 처리하는 데 초점**이 맞춰져 있기때문에 **intra-query의 병렬처리를 자동으로 보장하지 않는다**!!
> 쉽게 생각하면, 
> - **Thread Per Worker**: 한 쓰레드가 하나의 택배 상자를 배달한다. 각 쓰레드는 독립적으로 일하며 서로 다른 상자를 처리
> -  **intra-query 병렬처리**: 하나의 커다란 택배 상자를 여러 사람이 나눠 들어서 배달한다. 

위에서도 보면, DBMS가 동작함에 있어서 OS의 규제가 제약사항이 되는 경우가 많다. 
따라서, 최근에 DB와 OS를 합쳐서 Design 하자는 아이디어가 나오고 있다. 
- DBMS의 코드에서 **yield** system call을 통해, DBMS가 OS 자원을 너무 독점하고 있지 않게끔 한다. ex) 4ms 경과가 경과하면 yield 시켜 다른 작업이 시행될 수 있게 해주자!!
## 15.7 Inter- vs. Intra-query Parallellism
query processing을 통해 처리하는 query는 2가지이다. 
1. **Inter-Query**: **different 쿼리**들을 **concurrent**하게 동작
   → ==througput을 증가==(동시에 최대한 많은 쿼리가 돌아가게끔)
   → 추후 transaction 때 배울 것이다!!
2. **Intra-Query**: **single 쿼리**를 **parallel**하게 동작
   → long-running query에 대한 ==latency를 감소==

#### Intra-Query Parallelism
Intra query 내에서 Parallelism을 만족시키키 위해서 아래 3가지를 수행할 수 있다. 
- **#1. Inter operator parallelism**(Vertical): 어떤 operator에 더 가중치를 많이 둘 것인가?!
- **#2. Intra operator parallelism**(Horizontal): 각각의 operation을 더 빨리 수행하기 위해, 하나의 operation을 더 쪼개서 더 많은 worker를 투입하겠다!!
- **#3. Bushy**: 너무 복잡하다(개념적으로만 파악하자)
  → 위의 #1 과 #2 를 합쳐놓은 개념

<u><b>Parallel Grace Hash Join</u></b>에서 예를 보면, 
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_15 Query Processing/IMG-20250313142327-1.png]]
Hash Table의 각 entry는 독립적으로 동작하기 때문에
operation을 쪼개서 여러 thread가 동작하게끔 할 수 있다. → **Intra operation parallelism**!!

### #1. Intra-Operator Parallelism
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_15 Query Processing/IMG-20250313142327-2.png]]
- 위에서, `value > 99`라는 동일한 함수를 페이지 범위(ex. page1~2, page3~4, ….) 마다 적용시킨다. 
- Parallel하게 모든 일이 끝나고 나서, 최상단의 Exchanger가 데이터들을 모은다. 
- Exchanger의 타입에 따라 하는 역할이 아래와 같이 달라진다. ![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_15 Query Processing/IMG-20250313142328.png]]
  1. **Gather**: multiple worker로부터 나온 결과물을 single output stream으로 보내줌
  2. **Distribute**: single output input stream을 multiple output으로 split
  3. **Repartition**: input stream의 부하에 따라 재분배 시행
##### 전체 과정은 아래와 같다. 
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_15 Query Processing/IMG-20250416150918-2.png]]
1. A table의 각 partition으로부터 `A.value < 99`인 값들을 뽑아서 **hashtable을 build한다**. 
2. B table의 각 partition으로부터 `B.value > 100`인 값들을 뽑아서 A에서 build한 **hashtable에 hit하는 데이터들을 가지고 join을 실행**한다. 
### #2. Inter-Operator Parallelism
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_15 Query Processing/IMG-20250416150918-3.png]]
- join operation에서 시행된 결과값을 주기적으로 위로 emit 해준다. 
- select operation에서 시행된 결과값도 주기적으로 위로 emit 시켜준다. 
→ **다음으로 넘겨도 되는 결과 값들을 계속 넘겨준다!!**

## 15.8 Multi-Disk Parallelism
- **RAID 0**(Stripping): 페이지를 여러 디스크에 나누어 저장 → 데이터 읽기/쓰기 속도 매우 빠름(고장 시 손실)
  **Sequential Access 시에 ==대역폭을 매우 높일== 수 있다!!**
- **RAID 1**(Mirroring): 페이지를 복제하여 디스크에 나누어 저장 → 데이터의 안정성 보장(복구 가능)
  Redundancy를 높여서 안정성을 높임
  **여러 디스크에서 병렬로 Read를 처리할 때 ==Load Balancing==이 쉽다!!** (Write ❌)
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_15 Query Processing/IMG-20250416150919.png]]
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_15 Query Processing/IMG-20250416150919-1.png]]
#### Disk 내의 Partitioning
예전의 **NSM(n-ary Storage Model), DSM(Decomposed Storage Model)**
에서 배웠듯이,==디스크 내의 Tuple들을 가로로 저장할지 세로로 저장할지==도 query execution 속도를 높이는데 중요하다!!