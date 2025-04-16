## DBMS Architecture
![[IMG-20240930123238.png]]
![[IMG-20240930123240.png]]
- Backend Engine은 어느 CS 분야에서나 사용된다. 
- 따라서, 굳이 Database를 개발하지 않는다고 해도, 아래의 Backend Engine의 principle은 잘 이해하고 있어야 한다. 
## Flat FIles
1. Data Integrity
	데이터의 무결성을 보장해야한다. 
2. Implementation
	- 원하는 특정 레코드를 바로 찾기
	- 같은 데이터 베이스를 사용하는 다른 어플리케이션을 개발
	- 동시에 두 쓰레드가 하나의 file에 접근시에 처리
3. Durability
	record를 업데이트 시에 어플리케이션 충돌이 일어나면 어떨까
	높은 효율성을 바탕으로 데이터베이스를 여러 머신에 복제할 수 있을까
### 단어 정리
`Entity = Table = Relation(for relational Database)`
`Attribute = Column`
`Row = Instance = Record`

구체적으로 어떤 데이터 타입으로 어떤 데이터가 포함되어있는지가 기술된 Entity를
`Schema` 라고 한다. 

## Modeling
따로 이 수업에서는 다루지 않음

## Schema
`Physical schema`: 실제로 데이터베이스가 저장되는 방법을 정의 
`Logical schema`: 물리적 구조가 아닌 논리적 구조, 개체관 관계, 제약 조건, 접근 권한, 보안, 무결성 규칙 등을 정의
`Eternal Schema(View):` 실제로 외부에 보여질 구조를 정의 
![[Img/SNU GSDS/BKMS1(데이터베이스)/Lecture/Lec 2 (9.4)/IMG-20241022194159.png]]
`Logical Data Independence`: External Schema에서 SQL을 통해 원하는 데이터를 얻고자 할 때, Logical Schema에서 데이터를 External Schema로 전송하는 과정에서 문제가 없어야 한다. 
`Pysical Data Independence`: Logical Schema에서 데이터를 사용하고자 할 때, Pyhsical Schema측에서 문제가 없이 원할히 데이터를 공급할 수 있게끔 해줘야 한다. 

상위에서 사용하는 Address는 `Logical Address`와 하위층에서 사용하는 `Physical Address`를 분리해서 사용한다. 중간에 두개의 `Address를 치환하는 index table`을 놓고 사용한다. 


