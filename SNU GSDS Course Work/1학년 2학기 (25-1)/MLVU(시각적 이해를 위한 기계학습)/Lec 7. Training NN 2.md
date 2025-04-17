#### for Machine Learning
- Regularization
- Optimization
- Batch Norm
- Transfer Learning
# Regularization
## Overfitting
train data에 너무 적합하게 학습이 진행되면, 
evaluation data에 대해 일반화 성능이 떨어질 수 있다. 
→ 이를 위해 Regularization Term을 넣어줄 수 있다. 
## Regularization
loss function에 penalty term을 더해준다. 

기본적으로 모델은 “오차를 줄이려고” 한다. 
실제값 $y$ 와 예측값 $x^T\theta$의 차이가 크면 → 그걸 줄이기 위해 weight를 크게 조정한다. 

그러나, 학습 데이터에 너무 집착하면 → 특정한 sample 몇 개에 딱 맞추려고 weight을 막 키우게 된다. 그러면, 새로운 데이터에서 이상한 예측이 막 나올 수 있다. 

따라서, **"야, 그렇게 막 키우지 마! 너무 커지면 벌금 낼 거야!”** 식으로 
weight가 커지면 loss가 커지게 만들어서 **작은 weight를 유지하도록 유도**한다. 
→ 이것이 바로 <span style="font-weight:bold; color:rgb(51, 112, 255)">Weight Decay</span>
## Early Stopping
training set에 overfit이 되기 전에 미리 멈춰준다. 
## Dropout
> 학습할 때 무작위로 일부 뉴런을 꺼버리는(=randomly “drop”) 기법

학습 초기에 하나의 단서에만 집중해서 학습하다가, 
해당 단서의 뉴런을 확 꺼버리면 다른 정보를 학습할 수 밖에 없게 된다. 
→ 이걸 반복하면, 학습 중 **계속해서 랜덤한 단서들이 사라지니까**, 
**모델은 여러 단서들을 동시에 사용하는 법을 익히게 된다!**

아래와 같이 mini batch별로 사용하는 Neurun이 다르다. 
결국에는 평균적으로 모든 neurun이 빠짐없이 학습될 수 있다. 
![[IMG-20250416213706.png]]
단, 테스트할 땐 모든 neurun을 사용하여 inference해야 하기 때문에, 
학습 때 dropout 비율만큼 출력값을 보정해준다. ![[IMG-20250416215011.png]]
## Cutout
- 학습 데이터의 일부 영역을 검정색으로 가리는 데이터 증강 기법
- 모델이 이미지의 특정 부분에 과도하게 의존하지 않도록 유도 

## Optimizers
### SGD + Momentum
- 관성을 가지고, 예전에 했던 방향에 가중치를 좀 더 둬서
  local minimum에 빠지지 않게끔 한다. 
### AdaGrad
- 가파른 방향에서는 학습속도를 느리게 하여 진동(jittering)을 줄인다. 
- 평평한 방향에서는 학습속도를 빠르게 하여 효율적으로 최적화한다. 
- **지수 이동 평균**:
	- 과거의 기울기 정보를 점진적으로 잊어버리며, 최신 정보를 반영.
###### 단점
- 시간이 지남에 따라 기울기 제곱값이 계속 커지면서, 결과적으로 학습률이 지나치게 작아져 학습이 멈추는 문제가 발생한다. 
### Adam
**RMSProp**과 **Momentum**을 결합한 최적화 알고리즘
- **이전 기울기(gradient)의 지수 이동 평균**을 계산하여 학습 방향을 부드럽게 조정
- SGD with momentum 과 유사하게 기울기의 방향성을 기억
- 기울기의 제곱값에 대한 지수 이동 평균을 계산하여, 학습률을 적응적으로 조정

## Batch Normalization
- 딥러닝 학습이 어려운 이유 중 하나는, 각 레이어의 입력 분포가 계속 바뀌는 것 때문이다. (internal covariate shift)
- 그래서 각 mini-batch의 평균과 분산을 구해서 입력을 평균 0, 분산 1로 정규화 해주는 것이다. 

그러나, 정규화 해주면 모든 입력이 평균 0, 분산 1이 된다. 
이걸 그대로 쓰면 모든 뉴런이 너무 평범하게 학습될 우려가 있다. 
그래서 <span style="font-weight:bold; color:rgb(51, 112, 255)">모델이 원하는 대로 다시 스케일(γ), 이동(β)할 수 있도록</span> 해준다. 
![[IMG-20250416222454.png]]
Inference 시에는 **train에서 사용했던 Weight을 그대로 사용**해준다. 

# Transfer Learning
![[IMG-20250416222944.png]]
1. 큰 데이터셋으로 학습
	- 하위 계층은 일반적인 특성(edge, texture 등)
	- 상위 계층은 특정 클래스에 특화된 특성을 학습
2. C개의 클래스만 있는 소규모 데이터로 학습
	- 아래 Convolution 계층은 그대로 사용 → edge, curve 같은 일반적인 특징은 공용이니깐
	- 위쪽 FC 계층만 다시 학습 → 클래스 수가 달라졌으니깐
3. 2보다는 조금 큰 데이터로 학습
	- 아래쪽은 여전히 Freeze, 하지만 FC 계층 + 몇 개의 Conv layer도 fine tune
	- Fine tuning 시에는 learning rate는 작게 유지 
![[IMG-20250416223334.png]]







