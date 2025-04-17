
# Model-based vs. Model free
우리는 이미 위에서 나온 범주에 대한 내용을 배웠따. 
- **Model-based** (e.g., DP) 은 **planning**에 초점이 맞춰져 있다. 
- **Model-free** (e.g., MC, TD) 은 **learning**에 초점이 맞춰져 있다. 

둘 다 value function을 사용해서 학습하고, 
둘 다 미래를 예측해서 그 결과를 기반으로 현재를 업데이트 한다! (a.k.a **backup**)

### 정리
1. **Model-based**는 모델(환경 시뮬레이터)을 사용해서, **머릿속으로 미리 계산**하는 방식.  
	→ **환경이 어떻게 작동하는지 배움 또는 안다는 것을 전제로 한다.** 
    → 빠르지만, 모델이 정확해야 함. 
2. **Model-free**는 경험 기반으로 직접 배우는 방식.  
	→ **환경에 직접 부딪히며 학습**
    → 느릴 수 있지만, 환경을 몰라도 가능.
3. **Backups**는 미래의 보상을 사용해서 **현재의 가치 값을 수정하는 과정**.  
    → TD, MC, n-step, expected 모두 **다양한 backup 방식**일 뿐이다.

# Planning
> 진짜 환경과 상호작용 없이, 시뮬레이션으로부터 **정책을 더 좋게 만드는 과정**

다시 말해, 
1. 모델의 **환경에서 미래 상태들을 시뮬레이션** 하고, 
2. 시뮬레이션으로부터 **value function을 계산**하고
3. 이를 토대로 **policy (정책)을 선택**한다. 

# Learning
> 환경에서 직접 행동하고 결과를 받으며 학습하는 것


> [!comparison] Planning vs. Learning
> Planning = “Learning in your imagination”


# Dyna-Q: Integrated Planning, Acting, and Learning
> “계획, 행동, 학습을 하나로 통합한 시스템이 필요하다”

우리가 환경과 **실시간으로 상호작용(acting)** 하면서 동시에 **계획(planning)** 을 세운다면, 
몇 가지 어려움이 생긴다. 
- 환경이 바뀌면 → 기존 모델이 틀릴 수 있다 → 계획도 바뀌어야 함
- 모델을 계속 학습하면서 → **==계산 자원을 얼마나 계획/학습에 나눠 써야 할지 고민 필요==**

### 그래서 나온 해결책 📌 Dyna-Q: Real Experience vs Simulated Experience
Dyna-Q는 아래 **세 가지 기능**을 동시에 하는 구조다:
    1. 실제 환경에서 행동하고 경험 얻기 (acting)        
    2. 모델을 학습하기 (model-learning)
    3. 모델 기반으로 시뮬레이션을 돌려 계획 세우기 (planning)

![[IMG-20250417195537.png]]

#### 🟩 1. Direct RL Update
> 기존 Q-learning처럼, **실제 환경(environment)** 으로부터 얻은 경험(experience, trajectory)을 바탕으로 value function을 업데이트한다.

	$$QQ(s, a) \leftarrow Q(s, a) + \alpha \left( r + \gamma \max_{a'} Q(s', a') - Q(s, a) \right)$$
---
#### 🟦 2. Model Learning & Simulated Experience (Planning)
> 이제 Dyna-Q의 핵심은, 같은 경험을 단순히 Q 업데이트에만 쓰는 게 아니라,  
> **환경 모델(model)** 을 학습하여 **planning에도 활용**하는 것이다.
- **Model Learning**
    - 실제 환경에서 얻은 경험을 기반으로 모델을 학습한다.
    - 예: $model[(s, a)] = (s', r)$
- **Search Control**
    - 학습된 모델로부터 **가상의 experience** 를 생성할 때 어떤 $(s, a)$를 뽑을지 결정한다.
- **Planning Update**
    - 이렇게 생성된 가상의 경험을 이용해 Q-learning 업데이트를 한 번 더 수행한다.
    - 실제 환경에서 나왔는지와 관계없이 **value function을 개선하는 목적**으로는 충분하다. 
      $Q(s, a) \leftarrow Q(s, a) + \alpha \left( r + \gamma \max_{a'} Q(s', a') - Q(s, a) \right)$
> (즉, Q-learning과 Q-planning 모두 **value function을 업데이트하는 방식은 동일**하다.)

## Dyna-Q Computational Challenge
![[IMG-20250417200943.png]]
planning (f) 단계가 Computational 문제를 일으킨다. 
시뮬레이션을 ==**n 번 반복 수행하면서 Q-value를 계속 업데이트 하니 계산량이 급증**==하게 된다. 

## Example: Dyna Maze
![[IMG-20250417201328.png]]
![[IMG-20250417201339.png]]
➡️ 결과:  
**planning 횟수가 많을수록** 훨씬 빠르게 효율적인 경로를 학습하고, 매 에피소드 당 움직이는 횟수도 빠르게 줄어듦.

#### 만약, Model이 틀린 값을 내보낸다면?
모델은 
1. 환경에 데이터가 부족해서
2. 모델이 일반화에 실패한 함수 근사를 기반으로 학습됐을 때
3. 환경이 변했는데, 그 변화가 아직 관측되지 않았을 때
등 다양한 이유로 인해 틀릴 수 있다. 

→ 하지만 모델이 틀리는 것이 꼭 나쁜 것만은 아니다!
✅ 비최적 정책을 따라가다 보면, **모델이 틀렸다는 걸 금방 알아차릴 수 있고**, 오히려 모델 오류를 수정하는 계기가 될 수 있다!
❌ 모델이 너무 낙관적이라면, **계획된 정책이 허상을 탐험**하게 될 수도 있다.

# Dyna-Q+
## ✅ 비정상 환경 (Non-stationary Environment)
![[IMG-20250417202243.png]]
#### 🔄 환경 변화 사례 1: **장애물 위치 변화**
- 왼쪽에서 오른쪽으로 장애물 위치가 약간 이동.
- 기존 Dyna-Q는 이전에 배운 경로만 계속 따라가서 적응이 느림.
- 반면, **Dyna-Q+는 탐험 보너스 덕분에 더 빨리 새 경로를 발견**해서 더 많은 누적 보상(cumulative reward)을 얻음.
- - -
![[IMG-20250417202323.png]]
#### 🔄 환경 변화 사례 2: **더 나은 길이 생김**
- 이전 경로도 여전히 쓸 수 있지만, 새로운 더 빠른 길이 생김.
- Dyna-Q는 기존 경로를 고수.
- Dyna-Q+는 "새로운 길을 시도해볼 동기"가 있어서 더 빨리 적응하고 더 많은 보상 획득.
- - -

![[IMG-20250417202113.png]]

# Prioritized Sweeping
💡 **“과거 경험 중 어떤 걸 먼저 써서 업데이트하면 가장 효율적일까?”**

보통 Dyna-Q 같은 모델에서는 과거 경험을 **무작위로 뽑아서**(uniform sampling) planning update를 해.  
그런데 그게 **효율적이지 않을 수도 있어.**

#### 따라서, Prioritized Sweeping에서는 
- **업데이트 우선순위가 높은 state-action pair**를 먼저 선택해서 planning을 더 빠르게, 효율적으로 함.
- 각 경험의 **"급함 정도(=값이 얼마나 바뀔 것 같냐)"** 를 기반으로 처리 순서 결정.