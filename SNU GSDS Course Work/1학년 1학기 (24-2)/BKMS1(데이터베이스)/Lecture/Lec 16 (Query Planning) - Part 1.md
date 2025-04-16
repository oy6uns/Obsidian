## Architecture Overview
15개 정도 테이블이라면, estimate를 해서 1초 이내에 걸리는지를 확인

## Relational Algebra
### SQL Query
우선 조건들만 나열한다. (predicates)
operational description 형태로 바꿔줘야 한다. 

→ Relational Algebra 형태로 되어 있어야지 기계가 이해할 수 있다. 
## Relation
- Unary Operator
- Binary Operator
### Closed
relational algebra 자체는 input/output 모두 table을 가지는 relational schema이다. 
### Typed
==어떤건지 다시 공부==

## Relational Algebra Preliminaries
### Selection
애초에 볼 필요도 없는 데이터면
early stage에 dropping하는 것이 훨씬 효율적이다. 

### Composing SELECT and PROJECT
순서를 바꾸면 type이 안맞는다. 
→ 누구를 먼저 실행하는게 reduction effect가 큰 지를 보고 optimizer가 결정한다. 
→ 그러면 어떻게 아는거지?? `System Catalog`를 보고

## Predicate Pushdown
- Logical Plan이 들어오면, **보지 않아도 되는 데이터를 최대한 빠른 시점에 pruning하자**. 
	→ 가장 중요한 핵심 principal
## Projection Pushdown
- pushdown을 할 때는 데이터의 손실이 없게끔, 중간단계에서 요구하는 field를 추가해서 pushdown을 해줘야 한다. 다시 말해, type을 잘 맞춰서 pushdown해줘야 한다. 

==wide scan vs. join?== trade-off 뭘지 공부해보기
## Decomposing Queries
- From의 Nested query는 그나마 낫지만, **Where에 들어있는 Nested query**는 계속 redundant하게 계산에 반복해서 사용되므로 최악이다. 
  → **Rule-based 휴리스틱**이라고 한다. 
- Rule-based optimizing이 끝난 query를 보고 다음 시간에 query planning에서 여러가지 plan들을 보고 query estimation을 한다. 