## Review
![[IMG-20241020205858.png]]
- 최적의 β를 찾는 과정을 진행
  β는 간단하게 하기 위해 상수항인 β₀도 함께 행렬에 포함시킨다. 
- (실제 값 y와 Xβ의 차이의 제곱)을 최소화하는 β값을 찾는 것이 목표이다. 
  이를 위해 loss 식을 미분하고, 0이 되는 β값을 계산한다. 
## R-squared 
지표 다시 공부해보기


# Maximum Likelihood
## Why Least Squares?
→ i.i.d Assumption! (- Independent and Identically Distributed)
주사위를 던질 때 앞의 결과나 뒤의 결과에 영향을 받지 않고 값이 나온다는 것은 `Independence`에 해당한다. 
주사위를 던질때 일정한 분포의 값으로 결과가 나온다는 것이 `Identical Distribution` 이다. 

![[IMG-20241020205858-1.png]]
각 시행이 독립적이기 때문에, 각 시행에 대한 확률을 모두 곱한 값이 전체 확률이 된다. → `Likelihood`
![[IMG-20241020205858-2.png]]
log-likelihood는 모든 확률의 곱을 모든 확률의 시그마 합으로 바꿔줄 수 있기 때문에, 계산을 쉽게 만들어준다. 
또한, log 함수가 기존 f(x) 값의 성질을 바꾸지 않는 함수이기 때문에 붙여주어도 문제가 생기지 않는다. 
## Maximum Likelihood Estimator (MLE)
`Likelihood Estimator`는 주어진 데이터가 특정한 파라미터 값일 때 관찰될 확률을 나타낸다. 
이 함수의 값을 최대화하는 것은, 그 데이터를 생성했을 때 가능성이 가장 높은 파라미터를 찾는 것이다. 

→ Maximum Likelihood Estimator를 통해 데이터를 설명할 가능성이 가장 높은 파라미터를 추정할 수 있다. 
> [!최적의 파라미터?]
> 예를 들어, 우리가 정규분포를 가정하고 데이터를 설명하려고 한다면, 그 데이터를 설명할 수 있는 **평균**과 **표준편차**를 추정해야 합니다. 우도 함수를 최대화하는 파라미터가 바로 **평균과 표준편차**가 되며, 이는 주어진 데이터를 가장 잘 설명할 수 있는 분포를 찾는 데 사용됩니다.

![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 3. Linear Regression 1/IMG-20250304110005-1.png]]
두번 미분한 값이 음수면 위로 볼록, 양수면 아래로 볼록이다. 
따라서, **한번 미분한 값이 0이 되게 하는 Parameter 값을 찾고**! **두번 미분한 식이 절대적으로 음수**를 가지면, 해당 parameter는 Maximum Likelihood Estimator가 된다!

Bernoulli distribution, Gaussian distribution 
→ MLE를 적용했을 때 우리가 잘 아는 정리로 유도되게 된다. 

## MLE for Linear Regression
![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 3. Linear Regression 1/IMG-20250304110005-2.png]]
평균은 우리의 데이터의 값(Bx) 으로 가정하고, 표준편차는 임의의 σ로 가정한다. 
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 3. Linear Regression 1/IMG-20250313142327.png]]
처음에 강의 때 왜 하필 `Linear Regression의 MLE가 제곱으로 표현`되는지에 대해 의문을 가졌는데, 
Maximum Likelihood Estimator를 사용해보니깐, 식이 Least Square로 자연스럽게 유도되는 것을 알 수 있다!

# Linear Regression with Matrix Notation
## Matrix Notation
Σ를 사용하는 것 보다, matrix를 사용하는 것이 계산적 효율성이 높다. 
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 3. Linear Regression 1/IMG-20250313142327-1.png]]
→ B를 계산하는 식이 훨씬 더 간단해진다. 
## Statistical Properties of β̂
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 3. Linear Regression 1/IMG-20250313142328.png]]
`Gauss-Markov Theorem`
### 앞으로의 Notation 축약
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 3. Linear Regression 1/IMG-20250416150918-1.png]]
모든 coeffiecient를 하나의 matrix로 묶어서 표현하겠다!
# Bias-Variance Decomposition
## Evaluating an Estimator
어떤게 좋은 Estimator일까?
unbiased & low-variance estimator이다.
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 3. Linear Regression 1/IMG-20250416150918-2.png]]
Bias는 어떤 x에 대한 기댓값과 모델로 예측한 값의 차이를 얘기하고, 
Variance는 학습된 모델이 새로운 데이터에 대해서 민감하게 반응하냐는 것이다. 
## Linear Regression
Bias가 0인 모델들 중에 Variance가 가장 작은 모델이다.
→ 기댓값(어떤 x에서 가지는 y값들의 평균)이 예측값과 동일할 것이기 때문에’

따라서, Linear Regression에서 MSE는 추정값의 분산에만 의존한다. 
→ 물론 bias가 0이 아닌 모델들 중에 Variance를 linear regression보다 작게 할 순 있다. 