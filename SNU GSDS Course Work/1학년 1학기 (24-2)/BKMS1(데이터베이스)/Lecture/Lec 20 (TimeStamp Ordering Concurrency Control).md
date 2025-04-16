## Timestamp Ordering
assign된 transaction을 기준으로 serializable order를 결정

time stamp 기반의 serializable order에 위반되는 transaction이 발생하면 abort하자. 
`Pessimistic` 에서는 `lock`을 통해 ordering을 복잡하게 처리하였지만, `Optimistic`
은 `timestamp`를 통해 좀 더 쉽게 처리하기로 한다. 

timestamp를 빨리 받으면 더 빨리 처리되는 것이고, 
timestamp를 늦게 받으면 늦게 처리된다. 
위의 틀을 거스르려는 operation이 생기면 abort시킨다. 

![[IMG-20241125123944.png]] 
됨

## OCC Validation
- Backward Validation
	나를 기준으로 already committed된 Transaction들과 비교한다. 
- Forward Validation
	나를 기준으로 현재 동작하고 있는 Transaction들과 비교한다. 

### Forward Validation Step
1. Validation Step이 겹치면 안된다. 
2. WriteSet(T_i)와 ReadSet(T_j) 는 서로 겹치는 부분이 있으면 안된다. 
   발각되면 둘 중 하나는 Kill 해야한다. 


