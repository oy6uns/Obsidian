# Logistic Regression
## Linear Regression for Classification
![[IMG-20241021211455.png]]
Y 값 간의 상관관계를 고려하지 않고, 1, 2, 3으로 Label을 지정하는 것을 오류가 많을 수 있다. 

## Logistic Regression
> Regression 모델인 것 같지만, Classification 모델이다. 
### Log odds
![[IMG-20241021211455-1.png]]
default가 날 것 같은지, 안날 거 같은지를 보여주는 값. 
log odds가 linear한 모델이라고 가정한 모델이 `Logistic Regression` 이다. 

input X에 대해서 linear한 관계를 갖는다. 
![[스크린샷 2024-09-19 오전 11.34.47.png]]
→ Logistic Regression은 MLE의 Closed form solution이 존재하지 않는다! 따라서, 이걸 해결하기 위해 `gradient descent` 방법이 나오게 되었다. 

## Optimization: Gradient Descent
### 문제점
`1. 분포는 항상 아래로 볼록한 포물선 모양이 아니다.` 
`2. 뾰족하게 생긴 부분에 대해서는 differentiable 하지 않다.` 
`3. local minimum에 수렴하는 것은 느리다.` 

> [!MLE의 Closed Form Solution이 안 나올때]
> Gradient Descent를 통해 
> 계속 좋아지는 방향으로 업데이트해야한다.
> 
> Linear Regression 이후에 나오는 모델들은 항상 Closed Form Solution이 존재하지 않기에, Gradient Descent를 이용해야 한다. 

## Stochastic Gradient Descent
- 전체 example에 대해 gradient descent를 하는 것이 아니라, random하게 샘플링된 subset으로 gradient descent를 진행한다. 
- 32, 64, 128 등의 크기를 갖는 미니배치를 사용한다. 

![[IMG-20241021211455-2.png]]
→ 특정 mini batch에 대해서만 `Gradient Descent`를 적용하면 위처럼 삐죽하게 튀어나온 부분들이 계속 나온다. 

## Logistic Regression with 3+ Classes
### Log-loss
![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 5. Classification 1/IMG-20250304110005-1.png]]
#### **Optimization과 Stochastic Gradient Descent (최적화와 경사하강법)**

- **최적화 문제**를 해결하기 위해 경사하강법(Stochastic Gradient Descent, SGD)을 사용합니다. 로그 손실(Log-loss)을 최소화하는 방향으로 파라미터를 조정하면서 반복적인 업데이트가 이루어집니다.
- 경사하강법을 통해 손실 함수를 최소화하는 파라미터를 찾으며, 이는 닫힌 해(Closed-form solution)가 존재하지 않을 때 주로 사용됩니다.