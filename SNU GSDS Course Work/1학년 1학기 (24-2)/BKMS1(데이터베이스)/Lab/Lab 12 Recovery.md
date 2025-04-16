만약, WAL을 사용하지 않는다고 가정할 경우
- Atomicity를 해칠 수 있다. 
  만약, transaction이 commit되지 않은 상황에서 flush되면
  DBMS 재시작 시에 commit되지 않은 데이터가 남아 있을 수 있다. 
- Durability를 해칠 수 있다. 
  일부 변경사항이 디스크에 저장되지 않은 채로 DBMS가 종료될 수도 있다. 
  
DBMS의 Transaction은 모든 수정을 적용하기 전 먼저 WAL에 기록된다. 
이후 
1. WAL 버퍼가 가득 차거나
2. Transaction을 Commit할 때
디스크로 Flush되서 해당 내용을 디스크에 저장한다. 
- - - 
![[스크린샷 2024-12-05 오후 2.37.39.png]]
따라서, 현재 슬라이드와 같이 트랜잭션이 실행한 쿼리들이 WAL로써 디스크에 저장이 되있을 경우 Commit되기 전 Crash가 발생하여도, DBMS가 재시작할 때 해당 Transaction이 begin한 이후 어디까지 수행되었는지를 확인할 수 있다. 
![[스크린샷 2024-12-05 오후 2.39.06.png]]
Commit되기 전에 Crash가 난다면, 이전에 WAL 버퍼에 남아있던 Begin과 Update는 Undo 시킨다. 
- - - 
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lab/Lab 12 Recovery/IMG-20250304110005-1.png]]
Commit이후에 Crash가 난다면, 이전에 WAL 버퍼에 남아있던 작업들을 Redo 한다. 

따라서, WAL을 통해 Atomicity와 Durability를 보장할 수 있다. 
## Check pointing
그러나, 모든 변경사항을 WAL로만 저장할 경우
WAL의 크기는 점점 커지게 되고 Recovery 시에도 그때까지 쌓인 모든 log를 replay해야하므로 recovery 시간이 증가하게 된다. 

따라서, DBMS는 주기적으로 버퍼풀에 있는 데이터를 디스크에 적용하고 해당 시점까지 쌓인 log를 제거하는 **Checkpoint**라는 것을 사용한다. 

Postgres에서는 Archiving을 통해 Log를 따로 저장해둘 수 있다. 
해당 시점까지의 log를 Disk에 저장해두고, 복구할 수 있게 한다. 
→ 이를 **Point-In-Time-Recovery(PITR)** 이라고 한다. 

# GUCs
log-related config들을 조정함으로써 
- 로그를 유지하면서 성능 저하를 최소화하려는 균형을 맞출 수 있는 설정을 제공
- 시스템의 상태를 일정 시점에서 저장하는 **checkpoint**에 대해서도, 체크포인트를 자주 만들면 복구 시 유리하지만 