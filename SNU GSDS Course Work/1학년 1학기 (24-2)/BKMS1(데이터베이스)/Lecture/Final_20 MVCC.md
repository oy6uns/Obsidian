blocking을 서로 하지 말자~
각자 시점마다, frozen snapshot을 바라보는 느낌을 준다. 
읽기 트랜잭션은 데이터의 **스냅샷(과거 버전)** 을 읽고, 쓰기 트랜잭션은 새로운 버전을 생성.
![[IMG-20241211073143.png]]
Version Search할 Index만 좀 추가해주면 좋다!
- 데이터의 여러 버전을 유지하므로 Lock 기반의 DBMS가 겪었던 트랜잭션이 다수의 읽기 및 쓰기 작업을 처리하려 할 때의 병목현상을 해결할 수 있다. 
## MVCC Example 
![[IMG-20241211074917.png]]
$A_0$는 항상 똑같은 버전을 읽기 때문에, 
repeatable read(inconsistant read) 문제가 절대 발생하지 않는다. 
## MVCC Tuple Format
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_20 MVCC/IMG-20250304110005-1.png]]
## Version Storage
##### #1. Append-only Storage
  - 새 데이터 버전을 **같은 테이블 공간에 추가**(Append)
  - 기존 데이터는 변경 X, 데이터가 변경될 때마다 새로운 버전을 같은 테이블에 추가
- 테이블 크기가 커질수록 읽기 성능이 저하될 수 있다. 
- 가비지 컬렉션 필수!
##### #2. Time-Travel Storage
- 오래된 데이터 버전은 별도의 테이블 공간에 복사
- **현재 데이터 접근 성능이 향상됨**
- 관리가 복잡하다. 
##### #3. Delta Storage
- 변경된 속성만 별도의 델타 공간에 저장
- 전체 데이터를 복사하지 않고 변경된 부분만 저장하기에 효율적이다. 
  → **델타 데이터**를 활용해 **최종 데이터를 재구성**해야하므로 **읽기 복잡성이 증가**한다. 
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_20 MVCC/IMG-20250304110005-2.png]]
## Garbage Collection
- 어떤 애들이 ==불필요하는가에 대한 기준==
- ==어떻게== 지울 것인가?
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Final_20 MVCC/IMG-20250304110007.png]]
동일한 작업에 대해 
==예전 트랜잭션의 시작==부터 ==새로운 트랜잭션의 시작==까지가 dead zone이 되어 
Vacuum 작업을 진행해 주어야 하는 대상 범위가 된다. 

![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lecture/Final_20 MVCC/IMG-20250313142327-1.png]]