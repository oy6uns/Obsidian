# Llumnix: Dynamic Scheduling for LLM Serving(OSDI’24)
### LLM
regressive
LLM inference는 output length를 예측하기 어려운 특성으로 인해 LLM serving을 잘 해줘야 한다. 

현재 LLM의 3가지 문제
1. Severe queuing delay
2. Poor tail latencies
3. SLO violations
`heterogeneity`

`unpredictable`
dynamic memory allocation
그때그때마다 memory를 allocate하기 때문에, 계속 queue를 왔다갔다 하는 문제가 발생한다. 

### Goal
- Load balancing
- De-frag
- Prioritization
- Auto-Scaling
> [!NOTE]
> 최초의 위의 4가지 Goal을 달성한 LLM Serving System을 개발한 논문이다. 


핵심: fragmentation을 고려하는 것이 LLM Serving의 핵심이라고 할 수 있다.

# SLITS: Sparsity-Lightened Intelligent Thread Scheduling
쓰레드 간의 상관관계를 행렬로 표현해주어, Thread Scheduling을 할 수 있게끔 하였다. 

