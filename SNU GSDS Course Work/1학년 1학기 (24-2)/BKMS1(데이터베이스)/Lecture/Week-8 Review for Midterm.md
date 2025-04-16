`Database` - Collection of Structure of data
최대한 많은 사용자가 문제 없이 사용하게끔 하는 것이 최종 goal

==제안된 방법의 궁극적인 의미
Pros/Cons 를 잘 파악해야 함==

## Access Methods (Index Manager)
`Hash Table`
O(1) time에 Concurrency Control 을 효과적으로 해결할 수 있다. 
그러나, Range Search 가 불가능하다

`B+ Tree`
O(logn) → logaric time으로 조금 느려졌지만, 
Range Search 및 Collision Control도 보완됨

## Hash Table Latching
==구조가 변환하는 Operation이 일어났을 때, 
latch를 해야하는 범위를 잘 알아야한다. ==




