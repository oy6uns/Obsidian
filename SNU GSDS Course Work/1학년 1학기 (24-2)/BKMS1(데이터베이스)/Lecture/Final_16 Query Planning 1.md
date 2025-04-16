## 16.1 Query Optimization
- ==Heuristics / Rules== → 이번 포스팅!
	- query를 효율적으로 재작성하는 방법
	- catalog를 참조한 technique
- Cost-based Search → 다음 포스팅!!!
#### Architecture Overview
![[IMG-20241210182247.png]]
1. SQL Rewriter가 사용자로부터 전달받은 SQL Query문을 쉽게 변환한다. 
2. 들어온 Plan에 대해 해당 데이터가 실제로 있는지를 **System Catalog**를 통해 확인한다. 
   → Logical Plan으로 만든다. 
3. Tree Rewriter가 실행되는 상황에서는 System Catalog를 반복해서 들여다본다. 
   → 데이터가 실제로 있는지, 분포가 어떻게 되는지
4. Optimizer가 여러개의 Logical Plan을 토대로 최적의 Physical Plan을 찾는다. 
   → Cost-base Search를 진행한다. 
5. 4번까지의 과정이 모두 완료되면, 지난시간에 배웠던 Query 과정대로 실행이 된다. 
> 약 15개의 테이블 정도에 대한 Plan이라면, optimizer가 Dynamic Programming을 통해 1초 이내에 동작을 처리한다. 넘어가면 적당한 선에서 끊는다. 
> → **optimizing plan**은 **np hard plan**이기 때문이다.

## 16.2 Relational Algebra Equivalences
![[IMG-20241210182248.png]]
- Physical Query Plan을 Query Optimizer에게 던져주면, 
  지난 시간에 배웠던 Query Optimizer가 데이터를 직접 가져와서 실행을 한다ㅏ. 
#### SQL
- 사람이 무엇을 원하는지에 대한 정보만 있고, 
  어떻게 데이터를 가져와야하는지에 대한 얘기는 없다. 
- 기계가 알아듣기 좋은 쪽으로 바꿔주어야 한다!!
#### Relational Algebra Preliminaries
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 1/IMG-20250304110005-1.png]]
- **Closed**: 결과도 Relational Instance이다. 
- **Typed**: input Schema가 output의 type을 결정한다. 
- - -
#### Relational Algebra

![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 1/IMG-20250304110005-2.png]]
- $\pi$ (projection): SQL에서의 Select문
  → **Column 선택 연산**으로, 스키마의 구성 요소인 **Column 집합을 변경**한다. 
- $\sigma$ (Selection): SQL에서의 Where문
  → 튜플 수준의 연산으로, **행 필터링만 수행**, 열이나 속성에는 영향 X
	Selection을 Tree의 아래로 내려서, 데이터를 미리 선별하는 작업을 진행하는 것을 **“Selection Pushdown”** 또는 **“Predicate Pushdown”** 이라고 한다. 
- $\Join$ (join): 두 테이블의 속성을 결합한 새로운 스키마를 생성한다. 
  → 두 관계의 속성을 병합하여 새로운 관계를 형성하기에, **결과 스키마는 원본 스키마와 다를 가능성이 높다**. ![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 1/IMG-20250304110007.png]]
### Composing SELECT and PROJECT
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 1/IMG-20250313142327-1.png]]
순서를 바꾸면 type이 안맞는다. 
→ 누구를 먼저 실행하는게 **reduction effect**가 큰 지를 보고 optimizer가 결정하게 된다. 

: reduction effect가 큰지는 어떻게 확인할까? 
→ **System Catalog**의 **Schema Info를 통해 확인**한다. 
## 16.3 Compound Operator: JOIN
#### #1. Theta Join
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 1/IMG-20250313142327-2.png]]
두 테이블의 Cartesian Product 이후에 조건 $\sigma_\Theta$  를 적용하는 방식으로 수행된다. 
#### #2. Natural Join
두 테이블을 **공통 속성(attribute)을 기준**으로 **join**하는 연산이다. 
공통 속성(attribute)가 같은 경우에만 적용되며, **중복된 속성은 결과에서 하나만 유지**한다. ![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 1/IMG-20250313142328.png]]
#### Join Example![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final\_16 Query Planning 1/IMG-20250416150918-2.png](IMG-20250416150918-2%203.png)
## 16.4 Pushdown
불필요한 데이터를 빠른 시점에 처리해버리자!!
#### #1. Predicate Pushdown
Logical Plan이 들어오면, **보지 않아도 되는 데이터를 최대한 빠른 시점에 pruning하자**. 
![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final\_16 Query Planning 1/IMG-20250416150918-3.png](IMG-20250416150918-3%203.png)
- grade=’A’라는 predicate을 아래로 보내면 filtering을 더 early하게 수행하여 적은 데이터만 위로 올릴 수 있다. 
- **==Reduction Factor==**: 전체 데이터 중에 어느정도의 데이터를 털어내는지에 대한 정보
  → 이를 plan 순서를 정하는데에 사용한다. 
#### #2. Projection Pushdown
![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final\_16 Query Planning 1/IMG-20250416150919.png](IMG-20250416150919%203.png)
1. Projection 연산은 s.name, e.cid 2개의 column 만을 요구한다. 
2. Student, Enrolled Table 전체에 대해 Selection, Projection을 수행하는 것은 비효율적이다. 
3. 따라서, **Projection 연산을 Selection 이전에 수행**하여 **최소한의 필요한 값 만을 위로 넘겨준다.**
#### #3. else
- <b><u>Split Conjunctive Predicates</u></b>
  Horitontal 하게 존재하는 연산을 Tree(Vertical) 형태로 바꾸어 순차적으로 처리할 수 있게 한다 ![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final\_16 Query Planning 1/IMG-20250416150919-1.png](IMG-20250416150919-1%202.png)
- <b><u>Replace Cartesian Products with Joins</u></b>
  Cartesian은 너무 연산량이 많기 때문에 Join으로 바꿔줘야 한다! ![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final\_16 Query Planning 1/IMG-20250416150919-2.png](IMG-20250416150919-2%201.png)
  → 여기서 좀더 optimize를 해주게 되면
## 16.5 Local Query Optimization
#### #1. Split Conjunctive Predicates
 Horitontal 하게 존재하는 연산을 Tree(Vertical) 형태로 바꾸어 순차적으로 처리할 수 있게 한다 ![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final\_16 Query Planning 1/IMG-20250416150919-1.png](IMG-20250416150919-1%202.png)
#### #2. Replace Cartesian Products with Joins
  Cartesian은 너무 연산량이 많기 때문에 Join으로 바꿔줘야 한다! ![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final\_16 Query Planning 1/IMG-20250416150919-2.png](IMG-20250416150919-2%201.png)
   → 여기서 **좀더 optimize**를 해주게 되면 **아래와 같은 최종 Tree**가 나온다!! ![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final\_16 Query Planning 1/IMG-20250416150919-3.png](Img/IMG-20250416150919-3.png)
#### #3. Nested sub-Queries: Decompose
**변경 전**
![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final\_16 Query Planning 1/IMG-20250416150920.png](IMG-20250416150920%201.png)**변경 후** ![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final\_16 Query Planning 1/IMG-20250416150920-1.png](IMG-20250416150920-1%201.png)
nested query를 도려내서 위에서 한번만 실행되고 **==이를 Reference 하게끔 변경==**한다. 
→ SQL Rewrite 단계에서 처리한다.**
## 16.6 요약
데이터베이스의 내부의 정보를 이해하지 않고도
- static rules와 heuristics
를 통해 Query 계획을 효율적으로 최적화할 수 있다!!




