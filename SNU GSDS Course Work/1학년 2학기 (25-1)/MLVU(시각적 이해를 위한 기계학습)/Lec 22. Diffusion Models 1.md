# Score-based Generative Modeling (NCSN)
- unknown data distribusion p(x)가 존재한다. 
- 내가 어느 방향으로 가야 데이터가 더 많이 발견되는 곳으로 갈 수 있는지 방향만 찾을 수 있으면 전체 $p(x)$식은 모르더라도 그쪽으로 가서 거기서 sampling을 하면 그럴듯한 이미지가 나올 수 있다. 
$$
\nabla_{x}\log p(x)
$$
- **gradient** of the **log probability density**를 MLP score network $s_\theta$ 를 통해 현재 서있는 곳에서의 기울기를 찾아서 내가 어느 방향으로 이동을 하면 더 density가 높아질지를 본다. 

> [!important] 핵심
> - 데이터 분포 $p(x)$는 **어디에 데이터가 많이 몰려 있는지 알려주는 지도**이다!    
> - 우리는 그 지도를 **직접 갖고 있지 않지만**,  
>   각 위치에서 **더 높은 확률로 가는 방향(gradient)** 은 배울 수 있다! 
>   → 이게 **score function**이다.

![[IMG-20250602103940.png]]
- Scores 그림을 보면 데이터가 있는 방향으로 화살표(벡터 장)가 나 있는 것을 확인할 수 있다. 
## Score estimation
![[IMG-20250602104642.png]]
- 위의 objective function을 minimize하고 싶은데, 애초에 $p_{data}(x)$를 알지 못한다. 
![[IMG-20250602144638.png]]
- 전개 과정을 거쳐서 식 안에 있던 $p_{data}(x)$를 밖의 기댓값으로 빼주면, 식 안에 $s_\theta$ 에 대한 값만 남길 수 있다. 
# Challenge 1: Limited Support
![[IMG-20250602172131.png]]
- 위의 Data samples 그림을 보면, 빨간색 동그라미 구역에는 아예 데이터가 없기 때문에 gradient 계산 자체가 불가능한 문제가 있다. 
- 확률 분포 $p(x)$를 모든 곳에서 데이터가 있는 방향으로 갈 수 있게 해주어야 하는데, 데이터가 없는 곳은 아예 정의 자체가 안되는 문제가 있다. 
 
> [!important] using denoising Auto Encoder’s Idea
> 각 이미지 샘플에 대해서 noise를 추가하면, 더 넓은 범위의 영역까지 커버가 가능해진다!

![[IMG-20250602173136.png]]
- 기존의 $p_{data}$를 가우시안 노이즈를 추가한 $q_\sigma(\tilde{x}|x)$ 로 대체한다. 
- 결과적으로 $log\;q_\sigma(\tilde{x}|x)$ 를 미분하여 $\frac{1}{\sigma^2}(x-\tilde{{x}})$ 를 구해낼 수 있다. (정규분포로 가정했기에)
![[IMG-20250602175820.png]]
- 결과적으로 다음과 같은 loss function을 구해낼 수 있다. 

## Sampling with Langevin Dynamics
결과적으로 벡터장을 생성했더라도, 
이미지를 생성하기 위해서는 Sampling을 해서 뽑아낼 수 있어야 한다. 

이를 위해 사용하는 것이 **Langevin Dynamics** 이다. 

### Langevin Dynamics
액체나 기체 분자를 모델링하는 시스템이다. 
![[IMG-20250602180415.png]]
쉽게 설명하면, 
- 🔺 첫번째 항: 확률이 높은쪽으로 이동
	- 이때, $\nabla_{x}\log p(x)$ 는 score function $s_\theta(x)$ 로 근사해두었다. 
- 🔺 두번째 항: 랜덤한 노이즈 $z_t$ 를 추가 → local minimum에 빠지지 않게
**"조금씩 확률 높은 쪽으로 이동하면서, 살짝 노이즈도 넣는"** 탐색 방법이다!

> [!note] Langevin Dynamics
> 1. 랜덤한 위치에서 시작해서
> 2. **score function을 따라 조금씩 이동**    
> 3. **약간의 노이즈를 섞으면서**    
> 4. 여러 번 반복하면 → 진짜 데이터처럼 보이는 샘플이 만들어짐!


# Challenge 2: Incorrect Relative Density
![[IMG-20250602192336.png]]
  - 중간 대각선에 위치(50% 지점)한 값들에 대해, 
	  - 데이터가 더 많이 모여있는 쪽으로는 더 많이 모이고, 
	  - 데이터가 더 적은 쪽으로는 적게 모여야 된다. 
- 그러나, 현재는 중간에 있는 값들이 동일한 비율로 모이게 된다. 
### Annealed Langevin Dynamics
![[IMG-20250602192748.png]]
1. 처음에는 σ3\sigma_3σ3​와 같이 큰 노이즈를 사용하여 더 넓은 영역을 탐색하게 하고, 이때는 데이터가 흩어져 있어 잘 모이지 않는다.
2. 이후 σ\sigmaσ 값을 점차 낮추면, 데이터가 점차 **밀도 높은 영역**으로 모이며 구조가 선명해진다.
### 전체 Training Step
![[IMG-20250602193003.png]]
마지막에 보이는 $\sigma_i$ 가 guissian noise level을 조정하는 항이다. 





> [!Important] Conclusion
> t가 충분히 크면 어디서 시작하든 데이터가 실제로 많이 있는 곳으로 잘 흘러가게 된다!
> 확률적으로 많이 있는 곳에 많이 가고, 적게 있는 곳에 적게 가게 된다. 

# Denoising Diffusion Probabilistic Models (DDPM)
## Gaussian Noise?
픽셀 단위로 가우시안 값을 랜덤하게 뽑아가지고 더하는 것이다. 
e.g. 픽셀 값이 143인데, 랜덤한 값이 3이 나오면, 해당 픽셀의 값이 146이 된다. 

한번 정도 바꿨을 때는, 기존의 이미지 형태가 유지되지만
이를 1000번 반복하면, 완벽한 랜덤한 노이즈 형태의 이미지가 나오게 된다. 

- Forward Process: 이미지에 랜덤한 노이즈를 순차적으로 집어 넣는 과정
- Backward Process: 노이즈 이미지를 다시 기존 이미지로 복원시키는 과정

## Forward (Diffusion) Process
- Sample a data point from a real data distribution: $x_0 \sim q(x)$
### diffusion kernel
> <font color="#548dd4">망가뜨릴 때, 어떤 것을 사용하냐?!</font>
- 매 스텝 분포 합성을 일일이 계산할 필요 없이 
	- 하나의 가우시안 분포로 $q(x_t|x_0)$를 쓸 수 있게 해준다. 
$$ 
q(x_t|x_0) = \mathcal{N}(x_{t}; \sqrt{\tilde{\alpha_{t}}x_{0} }, (1-\tilde{\alpha_{t}})I)
$$


## Backward Process
![[IMG-20250607222806.png]]
$p_\theta$는 노이즈를 제거해 원래 이미지로 되돌리기 위한 딥러닝 모델이다. 
완전히 노이즈 낀 이미지 $x_t$에서 원래 이미지 $x_0$를 찾는 과정은 모호함이 있을 수 있다. 
<font color="#548dd4">따라서 단순히 “정답 하나”가 아니라, 가능한 결과들의 분포를 예측한다!</font>
→ “원래의 이미지는 대략 이렇고(평균), 이정도로 오차가 있을 수 있어요(분산)!”
### Denoising process의 핵심
**“진짜 이미지 $x_0$가 나올 확률을 최대화하는 모델 $p_\theta$를 학습하자!”**
$$
maximize \;\;p_{θ}​(x_{0}​)⇔ minimize\; −logp_{θ}​(x_{0}​)
$$
![[IMG-20250608163046.png]]
- 파란색: “진짜 노이즈 추가 과정”과 “모델이 학습한 노이즈 제거 과정”이 얼마나 비슷한지를 비교하는 항
- 빨간색: 우리가 실제로 최소화(학습) 할 식 → “진짜 과정을 모델이 얼마나 잘 흉내내고 있나?”
### 결과적으로, 
![[IMG-20250608164109.png]]
중간에 Consistency term을 보면, $q(x_{t-1}|x_t, x_0)$가 진짜 분포이고, 우리가 학습하고자 하는 $p_\theta(x_{t-1}|x_t)$를 진짜 분포와 최대한 비슷하게 오차를 minimize 시키는 것이 학습 과정이라고 생각하면 된다. 
#### Consistency term to single Gaussian
![[IMG-20250608164806.png]]![[IMG-20250608164821.png]]
결과적으로, $p_\theta(x_{t-1}|x_t)$도 가우시안 꼴이기 때문에, $q(x_{t-1}|x_t, x_0)$도 가우시안 꼴로 나타낼 수만 있다면, 복잡한 적분 계산 없이 MSE로 차이를 계산할 수 있기 때문이다. 
![[IMG-20250608165746.png]]
결과적으로, 위와 같이 정리될 수 있다. 
- 마지막에 앞에 있는 분홍색 부분은 복잡하지만, 있으면 학습이 불안정해져서 아예 빼버리는게 낫나고 논문에서는 말한다. 
![[IMG-20250608174946.png]]
결과적으로 Loss 식은 다음과 같고, 
- **$\epsilon$**: forward 과정에서 우리가 실제로 추가해 둔 랜덤 노이즈
- $\epsilon_\theta(\cdot)$: 모델이 “이 이미지에는 어떤 노이즈가 섞여 있을까?” 하고 예측해 내는 값
이 된다고 할 수 있다. 
## DDPM: Model Architecture
![[IMG-20250608175136.png]]
- 중간에 계산량을 줄이기 위해서 줄어들었다가, 다시 이미지의 크기로 복원하는 식의 U-net 구조를 사용한다. 
- 현재 어떤 step인지를 알아야 하기 때문에, time representation을 중간에 계속 넣어준다. 
## Pros and Cons of Generative Models
![[IMG-20250608180443.png]]
#### Diffusion
- 장점: 이미지 퀄리티가 좋게 나온다, Diversity가 높다. 
- 단점: 학습 시간이 정말 오래 걸린다. 
#### GAN 
- 장점: 이미지 퀄리티가 좋게 나온다, 학습이 빠르다. 
- 단점: Mode Converage가 낮다(Diversity가 낮다) - 1, 7만 계속 나온다. 
#### VAEs
- 장점: 빠르다, Diversity가 높다. 
- 단점: 이미지 퀄리티가 낮다. 