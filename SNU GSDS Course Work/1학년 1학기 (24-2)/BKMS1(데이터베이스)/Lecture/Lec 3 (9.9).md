## DDL
아직 schema가 존재하지 않는 상황에서 어떻게 schema를 핸들링할지에 대한 내용
a.k.a) system table, system catalog에 저장되게 된다

## DML
schema가 존재하는 상황에서 schema를 핸들링하는 언어

![[IMG-20240930123242.png]]
## Key Constraints
minimal subset of atributes

## NULL and NOT NULL
NULL은 값을 모른다는 뜻이다. 
SQL에서는 특정 컬럼에 대해서는 NOT NULL constraint를 걸어주어야 한다. (ex. name, age 등)

## SQL DDL
![[IMG-20240930123246.png]]
system 입장에서는 row를 구분할 수 있는 최소 key가 어떤건지 모르는 입장이다. 따라서, `primary key`를 통해 그 정보를 알려준다. 
또한, 혼란을 없애기 위해 `foreign key`  를 테이블을 처음 설계할 때 다른 primary key를 reference하게끔 한다. 

integrity constraints 가 중요하다. 
제약이 애매하게 걸려있으면, performance degradation이 일어날 수도 있다. 

## Relational Terminology
![[Img/SNU GSDS/BKMS1(데이터베이스)/Lecture/Lec 3 (9.9)/IMG-20241022194159.png]]

## SQL Query
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Lec 3 (9.9)/IMG-20250304110005-1.png]]
SFW Query를 통해 나온 결과가 새로운 relation을 나타낼 수 있기 때문에, SFW Query를 S, F, W 어디든지 subquery로 또 집어넣을 수 있다. 
→ `nested expression`을 허용한다. 

### Selection
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Lec 3 (9.9)/IMG-20250304110005-2.png]]
### Projection
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Lec 3 (9.9)/IMG-20250304110007.png]]
## Notation
Temporarily 하게 원하는 query 결과를 저장하고 싶을 때, View라는 기능을 사용할 수 있다. 
## Order By, Group By
아주 큰 데이터에 대해 위 기능을 사용하는 것은 성능 저하가 많이 일어난다. 

## Cascading
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Lec 3 (9.9)/IMG-20250313142327-1.png]]

## Joins
Join processing은 시간이 많이 소요된다. 테이블 전체를 Cartesian Product 한 뒤에 processing을 진행하기 때문
따라서, Join Algorithm을 최적화하는 것이 중요하다. 
→ 10월 말에 다시 공부할 예정

## 