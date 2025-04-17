#TD-learning 
# Temporal-Difference Learning
## Every-visit Monte Carlo 방식
- 에피소드가 끝날 때까지 기다렸다가 ==에피소드 종료 후에 해당 State ($S_t$) 에 대한 보상 $V(S_t)$을 한꺼번에 업데이트== 한다. 
![[IMG-20250417154153.png]]

## TD(0) one-step TD update rule
![[IMG-20250417154153-1.png]]
- $R_{t+1}$: 바로 다음에 받은 보상
- $V(S_{t+1})$: 다음 상태의 현재 추정 가치 
✅ **한 타임 스텝만 지나면 바로 업데이트** 가능 
![[IMG-20250417154153-2.png]]

> [!important] MC target vs. TD target 
> ![[IMG-20250417154153-3.png]]

### TD(0) Pseudcode for prediction
![[IMG-20250417154153-4.png]]
1. given $\pi$ 에 따라 action 을 고른다. 
2. action을 시행함에 따라 $R, S’$ 값이 나온다. 
3. 이를 통해 one-step의 $V(S)$ 를 계산한다. 
4. $S→S’$ 을 통해 step을 업데이트 해준다. 
	→ $S$가 terminal state가 될 때까지 반복 update를 해준다. 

## TD Error
TD update rule에서 
매 step의 $V(S_t)$ 를 얼만큼 업데이트 해줄지를 계산한 값이다. 

중요한 것은 Error를 계산할 수 있는 건 시간 $t+1$이 되어야 가능하다. 

> [!important] TD error vs. MC error
> ==**에피소드 중 가치 함수 $V$ 가 업데이트 되지 않고 고정되어 있다면,**== 
> Monte Carlo 방식의 전체 return과 TD 방식의 오차 누적이 같아진다. 
> 즉, MC target = $\sum$ TD error

## TD를 언제 사용하면 좋을까?
1. 에피소드가 매우 길 때 
2. 에피소드 구분이 없는 continuing task일 때

그러나, **TD method가 정말 true value function으로의 Convergence를 보장**하는가?

## Chain MDP
![[IMG-20250417154153-5.png]]
- 시작점으로부터 왼쪽 또는 오른쪽으로 이동할 수 있고, $V(E→right \;terminal \;state)$ 가 1이다. 
- 반복 횟수가 늘어날수록, True value function으로 수렴하는 모습을 확인할 수 있다. 
- TD가 MC보다 빠르게 수렴하는 모습을 보인다. 
