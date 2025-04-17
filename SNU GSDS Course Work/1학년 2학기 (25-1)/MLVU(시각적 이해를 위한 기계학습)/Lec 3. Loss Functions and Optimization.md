# Limitations of Linear Classifier
- Score 이 unbounded 하다. 
- 437.9, -96.8, 61.95 같은 값들은 **제한이 없음**.
	→ 이 값들을 Bounded Score(0~1)를 갖게끔 제한
	→ **마치 확률인 것 처럼**
# Sigmoid, Softmax
### Sigmoid
binary classification 시에 bounded score를 제공해준다. 
- 큰 s는 1로, 작은 s는 0으로 가게끔 
### Softmax
multi-class classification 시에 출력 값을 0~1로 변환

위의 두 함수는 마치 확률처럼 보이게 하지만, 엄밀한 확률의 정의를 따르지 않는 경우가 있기 때문에 맹목적으로 사용해선 안된다. 
# How to find the Weights
- Loss Functions: 현재 값이 좋은지/나쁜지를 판단
- Optimization: loss에 기반하여 parameter($\mathcal{W}$)를 update
## Loss Functions
For Classification, Discriminative(구분) Setting을 사용한다. 
### Margin-based Loss:
- $y\hat{y}$ 에 기반하여 loss가 결정됨. 
	- 같은 부호: classificaton이 올바르다는 것이고, loss는 작게 0과 가까운 값을 가진다. 
	- 다른 부호: classification이 틀리다는 것이고, loss는 커진다. 
![[IMG-20250416170642.png|775]]
#### <u>여러가지 Margin-based Loss</u>:
1. **0/1 Loss**: 가장 이상적인 Loss Function이지만, 0에서 미분이 안된다. 
2. **Log Loss**: differentialble at any point → **확률 기반 해석**도 가능
3. **Exponential Loss**: Log loss보다 경사를 가파르게 하여, correct cases에 대해 less penalty를 줄 수 있게 설계되었다. → **확률 기반 해석**도 가능
4. **Hinge Loss**: 애매하게 맞춘 것에 대해서도 penalty를 부과 
![[IMG-20250416171206.png]]
### Probability-based Loss:
목적: “정답 분포”와 “모델이 예측한 분포”를 비교하는 것이다. \
#### Cross Entropy
![[IMG-20250416171744.png]]
- 1에 가까워질수록 loss가 0으로 수렴하게 된다. 
- 0에 가까워질수록 loss가 계속 발산한다. 

#### Kullback-Leibler (KL) Divergence
두 확률 분포를 비교한다. 
![[IMG-20250416172101.png]]
- 항상 0보다 크거나 같은 값을 가진다. 
## Optimization
### Gradient Descent
> slope를 따라 내려가면서, loss function을 minimize하는 최적 점을 탐색한다. 

![[IMG-20250416172648.png]]
### Gradient Descent: Potential problems
- surface가 항상 포물선 형태인 것은 아니다. 
- loss function이 differentiable 할 때만 사용 가능하다. 
- Converging to a local minimum이 느리다. 
### Stochastic Gradient Descent
일반적인 GD는 모든 데이터를 이용해 정확한 gradient 계산
→ 하지만 느리고, 메모리도 많이 쓴다. 
따라서 보통 **32, 64, 128, 256, ..., 8192** 같은 mini-batch 사이즈로 뽑아 학습한다. 

작은 배치는 성능 향상 효과가 크다. 
미니 배치 사이즈를 키우면 안정성은 좋아지지만, 효과는 점점 줄고 비용만 늘어난다. 
(**disminishing returns**)
![[IMG-20250416173659.png]]
- 일반적으로 Mini-Batch GD를 사용하면, 각 배치(batch)의 샘플이 랜덤하게 선택되기 때문에 손실값이 불규칙하게 변동한다. 
- 매 스텝(step)마다 전체 데이터셋이 아니라 일부 데이터(batch)만 이용하여 손실을 계산하므로, **매 step에서 구해지는 손실값이 전체 데이터에 대한 평균이 아니다.** 
# Cross Validation
model이 general patterns in the data를 잘 찾아내는 것을 여러번 검증하기 위해 사용
