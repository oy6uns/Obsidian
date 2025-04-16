## Selections - Complex Predicates
- Selectivity가 낮은 것을 최하단으로 보내야 한다. 
- 위쪽에 둬봤자, 데이터를 많이 거를 수 없기 때문에 효율이 안나온다. 
## Cost Estimations
- Equi-Width: bucket에 들어있는 총 개수와 관계 없이 동일한 길이로 자른다. 
- Equi-Depth: bucket에 들어있는 총 개수가 거의 비슷해지게끔 가변 길이로 자른다. 
## Query Optimization
- scan할 데이터의 비율이 많다면, index scan하는 것은 매우 비효율적이다. (I/O 너무 많이 발생)
- 따라서, sequential scan이 훨씬 더 효율적이다. 
## MULTI-RELATION QUERY PLANNING
**결국에, Buffer Size와 Data의 상황을 보고
Query planning을 잘 짜야하는 것이 중요할 것 같다.** 