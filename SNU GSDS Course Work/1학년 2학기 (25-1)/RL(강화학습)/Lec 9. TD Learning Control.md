지난 시간의 TD Prediction은
> 주어진 정책(Policy) 하에서 상태의 가치를 예측하는 것

이었다. 이번 시간에는 단순히 예측하는 게 아니라, TD **정책도 직접 개선해나가는 제어(Control) 문제**에 대해 다룬다. 

## Generalized Policy Iteration (GPI)
GPI는 크게 2가지 반복 절차로 구성된다. 

| 단계                 | 설명                                             |
| ------------------ | ---------------------------------------------- |
| Policy Evaluation  | 현재 정책 하에서 value function을 학습 (→ TD prediction) |
| Policy Improvement | 더 나은 정책을 선택                                    |
# Sarsa: On-policy TD Control
SARSA는 상태 가치 함수 $V(s)$ 대신 행동 가치 함수 $Q(s,a)$ 를 학습한다. 

## 왜 Q(s, a)를 사용할까?
- $V(s)$: **상태의 가치**만 알려줌 → 행동 선택에 직접적인 정보가 부족하다. 
- $Q(s, a)$: **상태에서 특정 행동을 취했을 때의 기대 누적 보상** 
즉, 모델(transition, reward function)을 모를 때는 **어떤 행동이 좋은지를 직접 알려주는 $Q(s, a)$** 가 훨씬 실용적이다. 

## Sarsa의 구성 요소
- $S_t​$: 현재 상태
- $A_t$​: 현재 행동    
- $R_{t+1}$​: 다음 보상    
- $S_{t+1}​$: 다음 상태    
- $A_{t+1}$​: 다음 행동    

그래서 이름이 SARSA 이다!
$(S → A → R → S' → A')$
![[IMG-20250417160731.png]]
- **적절한 조건이 충족되면, Sarsa도 수렴한다** → 현재 정책의 true action-value $q_\pi(s,a)$로 
- TD learning 과 동일하게 각 행동 후마다 업데이트(except Terminal State)

> [!important] 요약
> - SARSA는 TD 기반의 On-policy Control 방법이다. 
> - $Q(s, a)$ 는 ==모델 없이도 직접 행동의 가치를 학습할 수 있어서 실용적==이다. 
> - On-policy는 Lec7에서 배웠듯 학습과 평가를 동일한 정책 하에서 한다는 것!

## Sarsa Pseudocode
![[IMG-20250417161508.png]]
==행동 선택은 탐험을 보장하기 위해 $\epsilon$-greedy 로 한다. ==
-  **모든 행동을 일정 수준 이상 시도하게 만들어** 모든 $Q(s, a)$를 학습할 수 있도록 한다. 

TD learning과 거의 동일한데, 몇가지 차이만 존재한다. 
![[IMG-20250417161725.png]]

## Greedy in the Limit with Infinite Exploration (GLIE)
==**초기에는 충분히 탐험**==하고, 
![[IMG-20250417163815.png]]
==**나중에는 점점 greedy하게 행동**==하게 되면, 
![[IMG-20250417163830.png]]
정책이 최적 정책으로 수렴할 수 있다.


> [!important] $\epsilon$-greedy to GLIE
> - $\epsilon$-greedy에서 **탐험을 위해 무작위 행동을 선택할 확률 $\epsilon$**을 남겨두었다. 
> - 시간에 지남에 따라 $\epsilon$ 이 감소하게끔 설정하면, greedy하게 수렴하게 할 수 있다. → GLIE
> $$\epsilon_t=\frac{1}{t}$$

## Convergence of SARSA
SARSA는 다음 두 조건을 만족하면 최적 $Q$ 함수 $q_*(s, a)$로 수렴할 수 있다. 
1. 정책이 GLIE 조건을 만족할 것
	- 모든 행동이 무한히 탐험되고, 
	- 결국에는 greedy 한 정책으로 수렴해야 한다. 
2. 학습률 $\alpha_t$ 이 stochastic convergence 조건을 만족해야 한다. 
![[IMG-20250417164844.png]]
	조건 1) **충분히 학습해야 한다** (수렴하려면 무한히 업데이트 해야 하기에)
	조건 2) **너무 많이 흔들리면 안된다.** (수렴 안정성을 위해 )


# Q-learning: Off-policy TD Control
![[IMG-20250417165056.png]]
- 현재 정책이 어떤 것이든 상관없이, **다음 상태에서의 최적 행동**을 기준으로 업데이트 한다. 
- 최적 행동은 **현재까지의 $Q$ 값을 기준으로 평가**한다. 

![[IMG-20250417165541.png]]
→ 모든 pair가 update되면, convergence가 guranteed 될 수 있다. 

## Why is Q-learning Off-policy?
- Q-learning은 데이터를 $\epsilon$-greedy policy로 수집해도, 
- 항상 greedy policy를 기준으로 업데이트 하기 때문에 **==Off-policy==** 이다. 

# Sarsa vs. Q-learning
## 1️⃣ Cliff Walking Example
![[IMG-20250417170156.png]]
- Q-learning은 **Cliff 바로 옆 Optimal Path**를 탐색하려다 자주 추락함 (큰 -100 벌점)    
- SARSA는 ε-greedy에 의해 실수한 경험을 반영하여 **더 안전한 경로**를 선호하게 됨

그래프 해석:
- **Q-learning**: ==평균 보상이 낮고 진동이 큼== **(risk 감수)**    
- **SARSA**: ==평균 보상이 높고 더 안정적임== **(risk 회피)**


> [!important] 각각 어떤 상황에서 사용하면 좋을까?
> **SARSA**
> → 리스크가 큰 환경
> → 보상 구조가 불안정하거나, 작은 실수가 큰 손해를 초래할 때 (**실시간 안정성** 중요)
> 
> **Q-learning**
> → 에이전트가 **충분히 많은 시도를 할 수 있는 환경**
> → 안전보다 **빠른 최적화와 수렴**이 중요한 경우
> 
> 초반엔 SARSA로 안전하게 시작하고 점점 Q-learning 스타일로 전환하는 방법을 사용할 수 도 있다!

## 2️⃣ Convergence Guarantees
### State-action pair 방문 관련
Sarsa는 **GLIE 조건을 충족**해야 한다. 
→ ==$\epsilon$ 을 줄여가며 $\pi(a|s)$ 가 점점 greedy policy로 수렴해야 한다. ==
→ 그래야 학습이 끝날 무렵에는 탐험보다 greedy한 활용 중심으로 바뀐다. 

Q-learning은 greedy policy만을 택하므로, 모든 state action pair에 대해 끝까지 탐험이 유지되게끔 해야 한다. ==따라서, $\epsilon$을 고정된 값으로 유지하여야 한다. (**$\epsilon$-soft 조건 충족**)==
![[IMG-20250417171718.png]]

# Expected SARSA
> 다음 행동 $A_{t+1}$의 기댓값을 사용하여 업데이트하는 방법

- $\pi(a|S_{t+1})$: 다음 상태에서의 행동 선택 확률 (e.g., $\epsilon$-greedy)
- ==무작위로 한 행동만 선택하는 SARSA와 달리, **모든 가능한 행동을 평균**함!==

분산은 기댓값을 사용하기에, **가장 낮지만** (**가장 정확**) 
**모든 행동에 대해 $Q$ 계산이 필요**해서 매우 높다. 

Expected SARSA에서 다음 상태의 $Q$ 값을 
$$ \sum_a\pi(a|s')Q(s',a)$$
로 계산한다. 

Q-learning은 여기서 $\pi$ 를 greedy로 고정한 형태이다. 
$$ \max_aQ(s', a) $$

> [!important] Expected SARSA
> **Expected SARSA는 On/Off-policy 모두 가능한 General한 구조**이고, 
> **Q-leraning은 그 중 가장 greedy한 special case**인 셈이다. 


# 전체 총 정리!
## TD (Sample-based) 방법

> 샘플 $(s, a, r, s')$ 하나만 보고 그때그때 업데이트 하는 방식

1. **TD learning (state value)**
$$
    R + \gamma V(S')
$$
- 현재 보상 + 다음 상태의 값
- 기본적인 TD(0), **value prediction** 용도

2. **SARSA (action value)**
$$R + \gamma Q(S', A')$$
- 실제로 선택된 다음 행동 $A'$을 기반으로 업데이트
- **On-policy**, 실제 행동 흐름 반영

2. **Q-learning**    
$$R + \gamma \max_{a'} Q(S', a')$$
- 다음 상태에서의 가장 좋은 행동을 가정
- **Off-policy**, 행동은 달라도 타겟은 항상 최적화

---
## DP (Model-based, MDP backup)

환경의 transition probability $P(s', r | s, a)$ 를 **전부 알고 있다**는 가정 아래의 계산

1. **Policy Evaluation**
$$\mathbb{E}[R + \gamma V(S') \mid s] $$
- 모델을 바탕으로 기대값을 계산

2. **Q-policy iteration**
$$\mathbb{E}[R + \gamma Q(S', A') \mid s, a]$$
- 특정 policy에 따라 행동 선택한 후, 기대값 계산
- SARSA의 모델 기반 버전

3. **Q-value iteration**
$$\mathbb{E}[R + \gamma \max_{a'} Q(S', a') \mid s, a]$$
- Q-learning과 동일한 업데이트
- 모델이 있으면 기대값을 직접 계산

![[IMG-20250417174856.png]]