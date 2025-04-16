p.14 atomicity 개념 다시 공부해보기
→ recovery 개념과 이어지게 된다. 

## p.26
초고성능 transaction을 달성하기 위해서는 OCC가 유리하다. 
OCC vs. PCC 차이 생각해보기. 

Index Concurrency Control : in-Memory operation
Concurrency Control: 

둘 다 principle은 동일하지만 granul이 다르다. 

## p.31
두 serial schedule의 실행 결과는 같다. 
interleaving이 잘 된 케이스

## p.46
conflict가 마냥 나쁜건 아니다. 
**conflict를 잘 제어만 한다면은 concurrency가 높으면서 serializable한 transaction을 만들 수 있겠다!**

## p. 47
==conflict serializable 다시 공부해보기==

conflict serializable intuition 결과물 

→ 첫번째 conflict를 보존하는 방식으로 밖에 compact를 시킬 수 없다. 두 개의 transaction에 conflict operation이 있다고 할 때, 두 schedule에서의 순서는 conflict operation의 순서를 preserve해야 한다. 

→ 처음 conflict났을때의 순서가 두 transaction의 순서를 결정해버리는거다. 



## p. 49
하나의 operation에 대해 위 아래에 conflict가 나는 operation이 존재한다면, 
해당 transaction은 serializable한 결과를 만들 수 없다. 


## Concurrency Control의 핵심 순서
1. conflict를 어떻게 detect할 지
2. 어떻게 해결할지

passimistic: 막는것
optimistic: conflict를 그냥 보내준다. 