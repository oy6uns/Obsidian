Duck: Table scan
- 효율적인 스캔을 위해 Vectorized Execution을 사용한다. 
- 행단위 처리보다 훨씬 빠름
- 멀티 스레드 환경에서 테이블 스캔을 병렬로 시행한다. 
`Buffer pool`
- 캐싱, LRU, Concurrency Control
- Read-optimized(자주 읽는 데이터를 메모리에 유지)
- sequential read

데이터 포맷
- 컬럼날 storage → OLAP 워크로드에 최적화됨
- 특정열만 필요한 경우 관련 열만 읽게끔 함
- 데이터 압축을 사용
- 모든 테이블과 메타 데이터를 단일 파일에 저장
##### Postgresql
- 데이터를 행단위로 처리함
- 스캔 중에 WHERE절을 통해 Predicate filtering을 진행한다. 
- row-oriented storage이다. 
- oltp workload에 최적화되어 있다. 
- 데이터를 페이지(블록) 단위로 관리한다. 
- MVCC를 자원하여 데이터 변경 시 기존 데이터를 삭제하지 않고 새로운 버전을 생성한다. 