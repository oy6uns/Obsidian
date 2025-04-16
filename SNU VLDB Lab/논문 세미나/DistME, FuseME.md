# DistME: A Fast and Elastic Distributed Matrix Computation Engine using GPUs(SIGMOD’19)
## Distributed Matrix Computation
repartition & aggregation 시에 발생하는 Cost 문제

### Matrix Multiplication
3가지 방식이 존재
메모리의 크기(3차원)에 최적화시키기 위해 Matrix Multiplication에서 Matrix를 적당한 크기로 쪼개는 방식들이 존재한다. 

`그 방식을 일반화 시킨 식이 이 논문의 contribution이다. `
### Key Ideas
multiple pieces로 쪼갠다. 

## GPU Streaming
데이터 학습 중에 GPU간의 최적화가 일어날 수 있는 기술이 존재
CPU 메모리의 개입 없이 GPU로만 모든 일을 할 수 있게끔.


# FuseME
sparse matrix (희소 행렬)
> 0이 많은 행렬, 약 행렬의 99.9%가 0일때 sparse matrix라고 정의를 내린다. 그게 아닌 행렬은 dense matrix




