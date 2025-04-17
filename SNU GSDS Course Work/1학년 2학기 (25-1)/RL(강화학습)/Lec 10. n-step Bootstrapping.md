# n-step TD Prediction

> bias: 모델의 예측값의 평균이 정답에서 얼마나 먼가
> variance: 데이터가 바뀔 때 예측값이 얼마나 요동치는가 
> **bootstrapping**: 추정값을 이용해서 다시 추정하는 방식
### Monte Carlo (MC)
- 전체 episode가 끝난 다음에 그동안 받은 reward를 기반으로 update
- 업데이트 타이밍이 늦기에, **bias는 없음. 다만 variance가 크다.** 
### 1-step TD (TD(0))
- 현재 reward 하나와 바로 다음 상태의 value만 보고 즉시 update
- 빠르지만 **bias가 있음, 대신 variance는 작음.** 
### n-step TD 
- 위 두 극단 사이의 trade-off
- **reward를 n개 받은 후 + bootstrapping 한 번**
![[IMG-20250417175825.png]]
## Error-Reduction Property (오차 감소 속성)
![[IMG-20250417181240.png]]
- 어떤 상태 $s$ 에 대해서 n-step return의 기댓값이 
  현재의 추정치보다 더 나은 예측을 제공한다는 것을 보장한다. 
- $\gamma^n$가 작아지므로 $n$이 커질수록 오차는 줄어든다. 

> [!important] TD 확장의 이론적 근거
> 위 속성을 통해 적절한 조건만 만족하면 
> **n-step TD는 $v_\pi$에 수렴한다는 걸 보장**할 수 있다. 
> 
> **step size $n$ 이 너무 크면**, 수렴이 느리기에, 적절한 $n$ 을 골라야 한다. 

# n-step Sarsa
- n-step으로 일반화하여, 더 많은 reward를 고려해서 더 정교한 업데이트가 가능하다. 
- short-term과 long-term reward 간의 균형을 조절할 수 있다. (bias-variance trade-off)


# n-step Expected Sarsa
핵심은 **마지막 step에서 하나의 $Q$ 값만 쓰는 대신, 가능한 모든 액션에 대한 기댓값을 쓰는 것**이다. 
![[IMG-20250417182413.png]]
→ 랜덤한 액션에 따라 흔들리는 업데이트를 줄여줌으로써, 좀 더 안정적인 학습이 가능하다. 

## n-step off-policy learning by Imortance Sampling
![[IMG-20250417183253.png]]
1. Off-policy 문제는 behavior policy로 데이터를 수집하고, 다른 target policy에 대해 학습한다. 
2. 그러나, $\pi$의 입장에서 보면, 우리가 관찰한 transition은 **“원래 정책이랑 안 맞는 경험”** 이다. 
	→ ==따라서, 이를 importance sampling 비율 $ρ$ 로 보정한다!==
![[IMG-20250417183823.png]]

> [!important] 한 스텝 차이나는 이유
> behavior policy $b$ 로 행동을 골랐기에, importance sampling을 통해 “보정”을 해주어야 한다!
> 
> 그럼 어떤걸 보정해야 할까?
> 우리는 지금 
> $$Q(S_t, A_t)$$
> 를 업데이트 하고 있다. 
> 그런데! $A_t$는 **이미 행동을 했던 거니깐? 보정 하지 않고 그냥 받아들이고 쓰면 된다!**
> 
> ![[IMG-20250417184345.png]]
> $A_t$ 이후의 행동들로부터 따라온 보상에 대해서만 보정하면 된다~!

# Off-policy without Importance Sampling
#### 📌 문제: importance sampling이 불편하다 ㅠ
importance sampling은 계산이 귀찮고, variance도 크다…그래서 나온게 **tree backup** 방법!

기존 n-step TD는
우리가 실제로 겪은 trajectory만 보고 값을 업데이트 했다. 
→ $R_{t+1}, R_{t+2},$ … 처럼

Tree backup은 이걸 바꿔서, 
“지금 정책($\pi$) 대로 행동이 갈 수 있는 모든 경우의 수를 기대값으로 미리 다 계산하자!”
는 아이디어이다. 

- 이전엔 실제로 **선택한 행동만 사용해서 업데이트**했지만? (예: SARSA, Q-learning)
- Tree Backup은 **선택하지 않은 행동들까지도** 고려해서, 전체 정책에 걸친 **기댓값**으로 업데이트한다!

결과적으로, ==1) 선택한 행동에 대해서는 값을 업데이트==, ==2) 선택하지 않은 행동에 대해서는 기댓값으로 값을 업데이트==! 

### One-step Tree Backup
![[IMG-20250417185851.png]]
가장 간단한 형태. 
한 스텝만 보되, 다음 상태에서 모든 행동의 Q값을 기댓값으로 반영

### Two-step Tree Backup
![[IMG-20250417190018.png]]
![[IMG-20250417190147.png]]

### n-step Tree Backup
![[IMG-20250417190258.png]]
결국에는 최종 업데이트 수식을 보면, 평소 쓰는 TD나 SARSA랑 똑같이 생겼다!
다만 여기에 들어가는 $G_{t:t+n}$만 트리 구조로 복잡한 계산이 들어가는 것이다!














