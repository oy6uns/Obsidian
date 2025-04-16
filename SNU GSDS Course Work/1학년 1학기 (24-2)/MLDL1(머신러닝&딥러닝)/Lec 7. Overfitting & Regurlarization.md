## Recall: Model Selection & Cross-validation
일반적으로 valid error가 가장 잘 나온 것을 토대로 Eval Set에 test해서 좋은 모델을 측정

### 그러면 언제 valid error가 낮은 지점을 찾아낼 것이냐!!
가 이번 강의의 핵심이다. 

## Overfitting
1. Sample이 몇 개 없을 때
   데이터가 너무 적기 때문에 중요한 패턴을 학습하기가 어려움
2. feature가 noisy 할 때
3. 데이터셋의 dimension이 너무 클 때
   모델을 학습하기 위해서는 dimension의 exponential한 양만큼 데이터가 필요하기 때문에
### overfitting은 model capacity와 연관이 크다
모델이 불필요하게 complex하면, **overfitting에 취약**하게 된다. 
train data의 noise까지 기억할 수 있는 **sufficient capacity**를 가지게 되기때문이다. 

반대로, 모델이 **insufficient capacity**를 가진다면(너무 단순하면), 모델이 **underfitting되기 쉬워진다.** 

따라서, **적당하게 모델의 크기**를 정해야되고, **적당한 시점에 모델의 학습을 멈춰**야한다. 
## Early Stopping: 언제 멈출까?
validation loss가 나빠지는 순간에 멈추자! → 이게 일반적인 방법

**i.i.d(independent & identically distributed)** 의 가정이 있기에 데이터는 **similar probability distribution**을 갖는다고 하자. 

각각의 dataset이 충분한 크기를 가진다고 하면, overfitting은 비슷한 시점에서 일어날 것이다. 

overfitting을 방지하기 위해서는 내가 `관심 있어 하는 metric의 그래프`(most important metric)에서 `stop 지점`을 정해야한다. 
- **Validation Loss**에 의존하는 방법은 모델이 **일반화 성능**을 최대화하려고 할 때 유리하며, 보편적으로 많이 사용된다. 
- 반면, 관심있는 **specific metric**에 초점을 맞추는 방법은 **특정 작업**에 대한 성능을 최대화할 때 적합하다.

### # 방법
Cross Validation 방법으 모든 경우에 대해 Model을 만들어서 학습시키고, Model Selection 하는 방법 → 너무 많은 경우의 수를 고려해야되기에 불가능!
![[IMG-20241021211433.png]]
**필요하지 않은 coefficient(β)는 0**으로 만들고, **필요한 β 값들만 남아있게끔 학습**을 진행!
→ 불필요한 coefficient(β) 축소하면 분산을 크게 줄일 수 있다.

## Ridge Regression
![[IMG-20241021211433-1.png]]
**loss 항에 β 값을 추가**함으로써, 왼쪽 회귀 식과 오른쪽 β 항 간의 힘겨루기를 시킨다. 
→ λ를 통해 규제의 가중치를 부여한다. 학습 시의 parameter 값들이 최대한 작아지게 만드는 것

따라서, 전체 loss의 값이 작아지는 조건에서만 coefficient(β)를 키우게끔 하고, 아닌 경우에는 penalty를 준다. 

Regularization이 강해질수록, **모델은 단순**해지고 **Variance 또한 낮아진다**. 
`variance`라는 것은 새로운 데이터가 들어올 때, 해당 데이터에 대한 Label이 변경되는 경향을 얘기한다. variance가 낮으면 변경이 잘 안되고 variance가 높으면 변경이 잘 된다. 
데이터로부터 배우고 싶은 것을 flexible하게 배우지 못하고, 패턴을 잘 반영하지 못하는 것을 `bias`라고 한다. 따라서, 모델의 학습이 단순해지기에 **bias는 높아진다**. 



![[IMG-20241021211433-2.png]]
λ값이 커질수록 더 강한 규제가 가해져 원의 크기가 작아진다. (과적합을 방지)
λ값이 작아질수록 규제가 약해지며, coefficient(β)가 더 자유롭게 큰 값을 가질 수 있다. 

λ = 0 일때, coefficient(β)는 아무 곳에나 다 위치할 수 있다. 

![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 7. Overfitting & Regurlarization/IMG-20250304110005-1.png]]
L_p norm이 패널티 항이고, Lasso, Ridge가 패널티 항의 특수한 이름이다. 

## Lasso vs. Ridge
![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 7. Overfitting & Regurlarization/IMG-20250304110005-2.png]]
Lasso는 β가 필요가 없다 판단하면 바로 0으로 감소하지만, 
Ridge는 천천히 곡선을 그리며 0으로 수렴한다. 

**왜 이 차이가 일어날까?**

![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 7. Overfitting & Regurlarization/IMG-20250313142327-1.png]]
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 7. Overfitting & Regurlarization/IMG-20250313142327-2.png]]
Lasso Regularization 같은 경우엔 β그래프가 정사각형 꼴로 그려지기 때문에, 
위의 그림처럼 β1, β2가 있더라도, 둘 중에 β 하나만 사용하는 꼭짓점으로 수렴하게 된다. ![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 7. Overfitting & Regurlarization/IMG-20250313142327-3.png]]
차원이 높아지면 높아질수록 sparse한 모델로 수렴할 가능성이 더 높아진다. 
![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 7. Overfitting & Regurlarization/IMG-20250416150918-2.png](IMG-20250416150918-2%2014.png)
- Lasso의 경우에는 β의 값이 급격하게 0으로 변경되기에, MSE(Mean Squared Error) 그래프도 급격하게 튀는 형태를 그린다. 그에 반해 Ridge의 경우에는 곡선을 그리면서 MSE 값이 변경된다. 
- Lasso 그래프의 빨간색 부분에서 극단적인 경우에는 β_0 빼고 나머지 β값들이 다 0으로 수렴하게 된다. 

## So, which one is better?
다양한 λ값들로 모델을 학습시켜보면서 가장 효과적인 모델을 찾는 것이 중요하다. 
어떤 것이 객관적으로 더 좋다고는 할 수 없다. 

## Summary
![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 7. Overfitting & Regurlarization/IMG-20250416150918-3.png](IMG-20250416150918-3%208.png)