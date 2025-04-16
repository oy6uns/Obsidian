## 16.1 Query Optimization
- Heuristics / Rules → 저번 포스팅
- ==Cost-based Search== → 이번 포스팅!!!
	- model을 통해 plan의 **cost를 estimate** 하는 법
	- **multiple equivalent plans를 evaluate**한 뒤 최적의 query plan을 결정하는 법
	→ ==optimal 한 쿼리 플랜==을 어떻게 빨리 찾을 것인가!!

## 16.2 Cost Model Components
Query의 Cost를 측정할 때, 어떤 것들이 Cost가 될 수 있을까?
- Physical Costs
	- **I/O**
- Logical Costs
- Algorithmic Costs
	- 모든 데이터가 **In-Memory Access가 가능**하다면 **Algorithmic Cost가 가장 중요**해진다!! (Physical I/O는 중요하지 않아진다.)

## 16.3 Statistics (a.k.a Stat)
Estimation을 하기 위해서는 분포를 알아야 하고, 
**분포(Stat)** 는 ==internal catalog==의 ==information schema==에 저장되어 있다. 

각 Relation $R$ 에 대해 
1. $N_R$: **몇 개의 Tuple**이 들어 있는지
2. $V(A, R)$: **특정한 attribute $A$에 distinct value**가 몇개인지 

#### Selection Cardinality
: $SC(A, R)$
- 속성 $A$에 대해 특정 값을 가진 레코드(튜플)의 평균 개수를 나타낸다. 
- 이는 **테이블 $R$에 대해 속성 $A$의 값이 ==균일하게 분포되어 있다고 가정**==할 때 계산된다. 
![[스크린샷 2024-12-10 오후 7.10.55.png]]
- `WHERE id = 123`과 같이 특정 id 값에 대한 Cost를 측정하는 것은 비교적 수비다. 
- 그러나, `WHERE val > 1000`과 같이 ==predicate이 범위로 주어질 때는 고민이 필요하다. ==Cost를 측정하기 위해서는 해당 Table의 `MIN`값과 `MAX`값이 필요하다. 
  → 현재 상황에서는 Uniform이라 가정하고 `MIN`, `MAX`를 찾는다. 
#### Selections - Complex Predicate
1. 값이 모두 동일하게 주어지고, `WHERE age = 2` 를 실행하는 경우
![[스크린샷 2024-12-10 오후 7.13.47.png]]
2. `WHERE age >= 2`를 실행하는 경우![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 2/IMG-20250304110005-1.png]]![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 2/IMG-20250304110005-2.png]]
3. `WHERE age != 2`를 실행하는 경우![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 2/IMG-20250304110007.png]]
4. 합집합, 교집합을 계산할 경우, 확률을 구한다.  ![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 2/IMG-20250313142327-1.png]]
#### 다시 돌아보는 Selection Cardinality의 가정
- #1. Uniform Data
  → **데이터가 균일하게 분포**한다고 가정
  <span style="color:rgb(229, 93, 98)">실제로는 균일하게 분포하지 않음</span>. 
- #2. Independent Predicates
  → 속성에 대한 **조건(predicate)들이 서로 독립적**이라 가정
  <span style="color:rgb(229, 93, 98)">실제로는 Dependent한 경우가 많기 때문</span>에, Estimation이 잘 안 맞는다. 
- #3. Inclusion Principle
  → join 시, **inner relation의 key**는 반드시 **outer relation에도 존재**해야한다고 가정

#### 그럼 실제로는 다른 분포를 어떻게 맞춰줄 수 있을까?
: ==**히스토그램**==을 사용해서 ==Approximate== 해보자!!
##### 1. Equi-Width Histogram
: **value의 range를 균등**하게 잘라줘!!
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 2/IMG-20250313142327-2.png]]
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 2/IMG-20250313142328.png]]
##### #2. EQUI-DEPTH Histogram
: **각 bucket에 동일한 수의 value**가 들어가게끔 잘라줘!!
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 2/IMG-20250416150918-2.png]]
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 2/IMG-20250416150918-3.png]]
##### #3. SAMPLING
: 튜플의 수가 너무 많아지면서, histogram으로 변환하는 overhead가 너무 커졌다. 
→ 그때그때마다 **필요한 데이터만 Sampling**해서 쓰자!
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 2/IMG-20250416150919.png]]
## 16.4 Query Optimization
#### Single-Relation Query Planning
single relation의 경우, ==**access method를 잘 뽑는 것**==이 지대한 영향을 미친다. 
ex) 약 70%의 데이터가 필요한 query라면 **index scan보다 sequential scan을 사용하는 것이 더 효율적**일 것이다!

#### Multi-Relation Query Planning
- 탐색해야하는 범위가 exponentially 하게 넓어진다!
- ==**Left-deep join tree를 설계하는 것이 가장 효율적이더라**==
  → 비교할 때, 하나에 대해 여러개를 고려할 경우가 안생기기 때문에! ![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 2/IMG-20250416150919-1.png]]
##### 고려할 수 있는 방법들
- **Join의 순서를 바꾸기**
- 각 operator에 대해 **어떤 Join을 사용**할 것인지![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 2/IMG-20250416150919-2.png]]
  → Hash, Sort-Merge, Nested Loop, …![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 2/IMG-20250416150920.png]]
- 각 operator이 **table에 어떤 access를 통해 접근**할 것인지
→ ==“Dynamic Programming”==을 통해 number of cost estimation을 줄여보자! 
아래 그림은 전체 진행 과정의 도식도이다. ![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 2/IMG-20250416150920-1.png]]
#### Postgres의 Genetic Optimizer
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_16 Query Planning 2/IMG-20250416150920-2.png]]
1. 유전 알고리즘과 비슷하게 가장 안 좋은 Cost를 하나씩 떨구고
2. 나머지 경우의 수에 대해서도 약간의 변형을 가해주며
	→ 최적의 Cost를 갖는 Query Plan을 만든다!!

