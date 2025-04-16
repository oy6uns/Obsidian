## CH-benCHmark
HTAP 성능 측정에 최적화된 데이터셋

insert, delete, update가 실행되고 있는 상황에서 analysis가 가능하게끔 하는 DB를 HTAP이라 한다. 

- OLTP - tps(transactions per second)
  TPC-C benchmark
- OLAP - query latency
  TPC-H benchmark

order_line 테이블에 대해 hash index를 생성하면, 해당 테이블의 특정 컬럼에 대한 정확한 키 검색에 대해 성능이 향상될 수 있다. 

특히, TPC-C throughput(트랜잭션 처리량) 이 중요한 시스템에서는 성능 개선효과를 볼 수 있다. 

그러나, 범위 검색을 자주 사용하는 경우에는 해시 인덱스보다는 B-tree 인덱스를 사용하는 것이 더 나을 수 있다. 



