## 데이터베이스
`collection of data` -> organized in a structured way
구조화된 데이터의 집합
: 정보화하는 대상을 어떻게 데이터 베이스 속에 집어 넣을지

전체 `Database Table`
안에 각 theme별로 `Schema`가 존재
Schema에 들어오는 각각의 정보들이 `Instance`

## DBMS
데이터베이스를 관리하는 `Software` 
많은 사람들이 동일한 데이터에 접근할 때, 한 사람이 그 데이터를 수정할 시에 발생하는 문제
-> 따라서 데이터베이스는 여러 사람들이 접근하더라도, 안전한 결과값을 return해줄 수 있는 `Concurrency Control` 이 가장 중요하다
## Architecture of a DBMS
SQL로 Query문이 DBMS로 들어오면
DBMS는 SQL을 본인들이 이해하기 쉬운 방식으로 `Query Parsing`을 진행한다. 
그 후, Query속도를 올리기 위해 `Query Optimization`을 후에 진행한다. 
후에 실제 데이터와 데이터베이스의 물리주소에 저장된 데이터와의 간극을 줄이는 `Relational Operators`
`Index Management`를 통해 RID(Record ID)를 설정하고, 데이터에 효율적으로 접근할 수 있다. 
다른말로, Access Management라고도 한다. 
![[IMG-20240930123253.png]]
Buffer는 자주 쓰는 데이터의 효율적인 접근을 위해 사용된다. 
`Buffer Cache`는 없어도 DBMS는 동작한다. 그러나, 속도를 위해 필수적이다. 
![[IMG-20240930123306.png]]
`Concurrency Control`과 `Data Recovery`를 묶어서 `Transactional Component`라 부른다. 

> [!NOTE]
> 데이터 베이스의 핵심은 아무리 많은 사람들이 데이터베이스를 사용한다고 해도, 
> 혼자 사용하는 것과 같은 착각을 줄 수 있게끔 해야한다. 
![[Img/SNU GSDS/BKMS1(데이터베이스)/Lecture/Lec 1 (9.2)/IMG-20241022194159.png]]
