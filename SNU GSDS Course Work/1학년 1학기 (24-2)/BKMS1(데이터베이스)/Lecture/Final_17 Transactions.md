과정을 **완전히  완료 하던지**, 아니면 **완전히 없던 일로 하던지**

## 17. 1 Motivation
- `Concurrency Control`
  같은 데이터를 두 사용자가 동시에 접근하려고 할 때 **Race Condition** 발생
- `Durability`
  송금 중에 **power failure** 발생 시, 올바른 Database State를 찾을 수 있어야 함
  failure 시에 **Recovery**를 잘 수행할 수 있어야 함
##### 각 transaction은 ACID Properties의 concepts를 잘 따라야 한다. 

## 17. 2 Transaction?
: **데이터베이스의 change를 유발하는 기본 단위이다!**
: the **basic unit of change in a DBMS**( *Partial transaction이 허용되지 않는다!! )*
###### 어? 그럼 지금까지 실행되었던 SQL query문 같은 사용자 동작들은 다 transaction인건가요?
사용자가 transaction의 시작과 끝을 정의하지 않으면
DB는 들어오는 single statement 자체를 **implicit transaction**으로 처리해버린다. 

- 지금까지는 SQL query 문을 실행할 때, `BEGIN`, `COMMIT`과 같은 명시를 해주지 않고 **single SQL query에 대해서 개별적으로 처리**했다. 
- 이제는 사용자가 `BEGIN` 이라고 선언을 하게 되면, DBMS는 이를 “아~ 사용자가 여러개의 operation을 실행할 예정이구나” 로 알아차린다. 

모든 **transaction을 줄지어서(serial) 실행**시키면, 문제 생길 일이 없다!
그러나, 이렇게 실행하면 **DBMS는 너무 느리게 동작**한다. 
→ ==동시에 수행할 수 있도록 허용==하면서도, ==하나씩 따로 시행한 것과 같은 결과==가 나오게끔 하는 것이 ==**“concurrency control”의 main goal**== 이다!!

> [!transaction?] Concurrency Control’s Main Goal
> **Equivalent** to **Serializable Execution!!**
#### 앞으로 나올 용어 정리
- **Database**: 고정된 데이터 객체(a fixed set of named data object)
  e.g., `A`, `B`, `C`, …
- **Transaction**: atomic한 read 및 write 동작 (sequence of read & write operations)
  e.g., `R(A)`, `W(B)`, …
#### Transactions in SQL
- 새로운 Transaction은 `BEGIN` command를 통해 시작된다. 
- Transaction은 `COMMIT` 또는 `ABORT` command를 통해 종료된다. 
	- `COMMIT`: DBMS는 모든 **Transaction의 change를 save** 시켜야 한다. 
	- `ABORT`: **Transaction이 아예 안 일어났던 것**처럼 DBMS 상태를 돌려놔야 한다. 
- 사용자가 일정 시간이 지나고도 `COMMIT` 또는 `ABORT` command를 입력하지 않는 경우에만 DBMS가 스스로 Transaction을 종료시킬 수도 있다. 

## 17. 3 Correctness Criteria: ACID
#### <span style="color:rgb(229, 93, 98)">A</span>tomicity
: “all or nothing”
Transaction은 Database에 **Commit되어 모두 남아 있거나**, 
**Abort되어 아무 결과물도 남아 있지 않게**끔 해야 한다. 
#### <span style="color:rgb(229, 93, 98)">C</span>onsistency
: “it looks correct to me”
**사용자가 정의한 consistent한(일관된) 조건들을 만족**하고 있는지.
ex) foreign key constraint, referential integrity
#### <span style="color:rgb(229, 93, 98)">I</span>solation
: “as if alone”
**많은 수의 client의 transaction이 돌고 있음에도 불구**하고, 
**한명이 사용하고 있는 것과 같은 착각**을 주어야 한다. 
#### <span style="color:rgb(229, 93, 98)">D</span>urability
: “survive failures”
**어떤 실패**가 있었더라도, **항상 데이터는 남아 있어야 한다**!!

**Atomic Durability**: ==Recovery Module==이 Cover
**Consistency**: ==Concurrency Control Module==이 Cover
- - -
#### ✅ Atomicity of Transactions
- Scenario #1: 
  → 사용자가 일을 하다가 **사용자가 중간에 Abort** 시키는 경우
- Scenario #2:
  → 사용자가 일을 하다가 **시스템의 power failure**가 나버리는 경우
**transaction이 Abort된 두 상황**에서 **<u>사용자의 Correct State</u>는 어떤 것이 되어야 할까?**
##### Approach #1. Logging
남겨놨던 흔적을 토대로 예전 상태로 되돌려야 한다. 
→ Undo할 수 있게끔 충분한 정보가 필요하다. 

- Transaction을 하면서 했던 **모든 Log를 memory와 disk**에 남겨놓는 것이다!
- **old & new value**가 필요하다. 
##### Approach #2. Shadow Paging
DB 상태를 바꿀 때, 
Original Database는 그대로 냅두고 **Shadow Page**에 쓴다.
- Restore 할 일이 있으면, Shadow Page를 **그냥 버려버리면 된다.** 
  → 장점: Recovery가 굉장히 빠르다
  → 단점: Shodow page를 계속 들고 있어야 하기 때문에 매우 무겁다
- 성공적으로 Commit되면, Shadow Page를 반영시킨다. 
#### ✅ Consistency
DBMS 자체에서 관리할 것은 따로 없다. 
- 단지 사용자가 정의한 **Integrity Constraints**들만 잘 만족하고 있으면 된다. 
#### ✅ Isolation
격리성을 유지하면, 사용자는 다른 transaction의 중간 상태를 볼 수 없으며, 실행 결과만 볼 수 있다. 
- 데이터베이스는 **동시성을 향상**시키기 위해 **여러 트랜잭션의 작업을 교차하여 실행**한다. 
- 그러나, 다른 트랜잭션의 간섭으로 인해 Data Integrity가 깨질 가능성이 존재한다
#### Mechanisms for Ensuring Isolation
동시성 제어 프로토콜**은 DB가 여러 transaction의 작업을 적절히 교차(interleaving)시키는 방식을 결정한다. 

<u><b>동시성 제어 프로토콜</b>의 <b>2가지 접근법</b></u>
##### 1. Pessimistic Concurrency Control (**PCC**)
   - **문제가 발생하지 않도록** ==미리 방지하는 방식==이다. 
   - ==Lock==
     - **Shared Lock(S-lock)**: 데이터를 **읽을 때** 다른 transaction의 **쓰기를 막는다**
     - **Exclusive Lock(X-lock)**: 데이터를 읽거나 쓸 때 다른 transaction의 접근을 완전히 차단한다. 
   - 장점 👍🏻:
     충돌 가능성을 사전에 차단하므로 **안정적**이다. 
   - 단점 👎🏻: 
     - Lock으로 인해 **병렬 처리 성능이 저하**된다. 
     - “**Hot Spot 문제**”: 특정 데이터에 접근하는 transaction이 많을 경우, 락 경쟁이 발생해 성능이 저하된다. 
##### 2. Optimistic Concurrency Control (**OCC**)
   - 충돌이 드물다는 가정 하에, **==충돌 발생 후 해결**하는 방식==이다.
   - 충돌이 발생하면 transaction을 강제로 중단(**abort**)
   - 장점 👍🏻:
     Lock을 사용하지 않아 병렬 처리 성능이 향상된다. 
   - 단점 👎🏻: 
     - 충돌이 빈번하게 발생하면, **transaction 중단과 재실행**으로 성능이 저하된다. 
     - **Skewness(데이터 편향)** 이 심한 경우 성능 저하가 크다. 

#### InterLeaving Transaction(트랜잭션 간섭)
##### Good Example
![[스크린샷 2024-12-11 오전 1.22.16.png]]
##### Bad Example
![[스크린샷 2024-12-11 오전 1.23.09.png]]
- Serial Schedule
  **다른 트랜잭션 간의 interleave가 발생하지 않는** Schedule
- Equivalent Schedules
  **트랜잭션 결과가 동일**한 Schedule
- Serializable Schedule
  실행한 Schedule 중 **하나의 결과가 Serial Execution과 동일**할 때, 
  이 **Schedule의 Serializable**하다고 말한다. 

#### Conflicting Operations
Read는 상태를 변경하지 않기에, Read끼리는 충돌을 발생시키지 않는다. 
**Write는 상태를 변경하기에 Conflict가 발생할 수 있다.** 
- Read-Write Conflicts (R-W)
- Write-Read Conflicts (W-R)
- Write-Write Conflicts (W-W)
그러나, 위의 Good Example에서 봤듯이 conflict가 **항상 문제를 일으키는 것은 아니다.** 
### #1. Read-Write Conflicts
: **Unrepeatable Reads** (=Inconsistant Read)
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_17 Transactions/IMG-20250304110005-1.png]]
하나의 Transaction Block 내에서 두 Read가 서로 다른 반환 값을 반환하는 경우
→ **내가 값을 변경 안했는데, 값이 달라져 있네?**
→ Isolation은 ==나 혼자만 Transaction을 수행하는 것과 같은 착각==을 느끼게 해주어야 하는데, ==값이 중간에 변경==되는건 나 말고 누가 더 있다는 뜻이다
### #2. Write-Read Conflicts
: Reading Uncommitted Data(**”Dirty Reads”**)
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_17 Transactions/IMG-20250304110005-2.png]]
T2는 T1에서 변경된 A 값을 읽어서 Write를 시행해주었다. 
이후 T2는 커밋을 했지만, 이후에 T1에서 Abort가 난다. 
T2가 읽은 **변경된 A 값**은 쓰레기 값이 된 것이고, 이를 **Dirty Reads** 라고 한다. 
→ 잘못된 값을 다른 Transaction들(T3, T4, …)도 연속적으로 읽게 되면, 모두 나중에 **Abort** 시켜주어야 한다, 이를 <b><span style="font-style:italic; color:rgb(229, 93, 98)">Cascading Abort</span></b>라 한다. 
### #3. Write-Write Conflicts
: Overwriting Uncommitted Data
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_17 Transactions/IMG-20250304110007.png]]
아까 #2 에서 봤던 문제와 더불어
**잘못된 A값**에 Write를 수행하는 것은 uncommited data에 데이터를 덮어 씌우는 꼴이다. 

## Schedule이 Serializable한지 판단하는 법
with **”Conflict Equivalent“**
Conflict 나지 않는 작업 집합의 순서를 교환할 수 있다. ![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_17 Transactions/IMG-20250313142327-1.png]]

> [!Serializable] 더 쉽게 Detect할 수 없을까?
> **“Dependency Graph”**! 
> acyclic 하지 않으면 충돌 상황을 안 만든다고 보장할 수 있다. 

==처음 Conflicting이 나온 순서를 보존하도록== Serializable Schedule을 설계할 수 밖에 없다!
