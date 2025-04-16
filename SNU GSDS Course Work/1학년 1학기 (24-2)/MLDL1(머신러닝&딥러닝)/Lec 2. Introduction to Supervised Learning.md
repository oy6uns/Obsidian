## Artificial Intelligence
`ability to perceive`
(정보를 인지하거나 추론)하는 능력을 가진 intelligence of machine or software
## ML
field of study in AI 
seen 데이터로부터 학습하고, unseen data로 일반화할 수 있는 알고리즘
**without explicit instructions**
### ML’s import keyword
- `AI`
- `Data-driven`
- `minimizing manual instructions`
## Ideal Regression Function *f(x)*?
특정 x에서 여러개의 y값을 가질 수 있다. 
따라서, 최적의 f(4)값은 x=4에서 **가질 수 있는 모든 y값들의 기댓값(평균)** 이다. 
## Non-parametric Models
> 함수 f의 형태에 대한 명시적인 가정을 하지 않는다. 

parametric model은 분석하려는 데이터가 특정한 수학적 분포를 따를 것이라 가정하고, 그 분포의 파라미터만 추정하는 방식이다. 
그러나, Non-parametric model은 이러한 **가정이 없기 때문**에 **더 유연하고 다양한 데이터에 적용**할 수 있다는 장점이 있지만, 그만큼 `더 많은 데이터가 필요`하고 과적합(overfitting)될 수 있는 가능성이 높다. 

## Step of Supervised Learning
### Step 1. How to Design f
Most statistical learning methods for this task can be characterized as either **parametric** or **non-parametric**.
### Step 2. Objective of the Regression Function f(x)
![[IMG-20241021015130.png]]
![[IMG-20241021020738.png]]
실제 데이터에서 발생하는 노이즈 때문에 발생하는 줄일 수 없는 오차는 생각하지 않고, 
줄일 수 있는 오차를 최소화하는 것이 목표이다. 
### Step 3. How to Estimate f
여러 learning method를 사용하여 training data를 토대로 unknown function f를 추정하는 것이다.
## Bias-Variance Trade-off
![[IMG-20241021021613.png]]
- 모델이 너무 간단하면 편향(bias)이 높아질 수 있다. 
- 모델이 너무 복잡하면 분산(variance)가 높아질 수 있다. 
  → 평균 테스트 오률ㄹ 최소화 하는 방향으로 모델의 유연성을 조절해나가야 한다. 

