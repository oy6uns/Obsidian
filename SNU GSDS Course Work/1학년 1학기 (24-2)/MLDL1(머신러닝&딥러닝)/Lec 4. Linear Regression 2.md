# Linear Regression with Categorical Features
## Categorical (Qualitative) Predictors
![[IMG-20241020210414.png]]
3가지 region이 생기면, variable을 3개로 설정하면, classification이 가능해진다. 

## Modeling Interactions
앞으로는 non-linear한 세상에서 수업을 진행!
`Interactions` & `nonlinearity`
![[IMG-20241020210414-1.png]]
![[IMG-20241020210414-2.png]]
0.0011 이란 수치가 작게 느껴질 수도 있지만, 실제 정확도는 89.7%에서 96.8까지 상승했다. 

## Hierarchy for Interactions
> [!NOTE]
> interaction 변수만 집어 넣어도 정확도가 올라가니깐??
> 기존의 변수는 안넣고 interaction 변수만 넣는 것은 문제가 발생한다. 

![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 4. Linear Regression 2/IMG-20250304110005-1.png]]
polynomial form 으로 수식을 표현하면 직선이었던 Linear regression이 점점 곡선형으로 바뀐다. 

## Does the least square solution always exist?
![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 4. Linear Regression 2/IMG-20250304110005-2.png]]
β가 역행렬을 가질 수 없는 경우는 보통 X의 열들이 선형적으로 종속일 때 발생한다. 
즉, 두 열이 동일하거나 중복된 경우에는 해를 구할 수 없다. 

Q. **질문**: 피처들이 완벽하게 상관되지는 않지만 **강한 상관관계**를 가질 때는 어떻게 될까요?

- 두 피처가 완전히 동일하지 않지만 매우 유사한 경우, 여전히 행렬 (X^T)X가 가역일 수 있지만 **수치적으로 불안정**할 수 있다. 즉, 행렬이 거의 역행렬을 가지지 못할 수 있고, 결과적으로 최소제곱 해를 구하는 데에 문제가 발생할 수 있다. 

> [!Then]
> feature 행렬의 두 열이 동일하거나 매우 유사할 경우, 최소제곱 해를 구하기 어렵다. 따라서 이 문제를 해결하기 위해 feature를 줄이고나 PCA등의 차원 축소 기법을 사용할 수 있다. 
## Best Subset Selection

### Greedy Algorithm
variable을 ![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 4. Linear Regression 2/IMG-20250313142327-1.png]]
다음과 같이 모든 조합을 다 해보는 무식한 방법을 시행해본다. 
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 4. Linear Regression 2/IMG-20250313142327-2.png]]
각 시행에서 나온 계수들은 모두 독립적이다. 

`why 안쓰일까?`
변수의 개수가 정말 많아지면, 
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 4. Linear Regression 2/IMG-20250313142327-3.png]]
경우의 수가 너무 많아지고, computational reason으로 인해 못한다. 


### Stepwise Selection
하나 고르고, 2개 고르고,,,,모든 경우의 수가 등차수열의 합으로 표현된다. 
![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 4. Linear Regression 2/IMG-20250416150918-1.png](IMG-20250416150918-1%201.png)![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 4. Linear Regression 2/IMG-20250416150918-2.png](IMG-20250416150918-2%2013.png)
실제 Best Subset은 `cards, income, student, limit` 의 4개 variable이지만, Forward stepwise는 `기존의 변수를 빼지 못하기 때문에` 다른 subset이 나오게 된다. 

`반대로 하나씩 없애가는 방법도 있다!` → (Backward) Stepwise Selection

**Backward Selection**은 feature를 하나씩 제거해나가는 방식이므로, n(샘플 수)가 p(피쳐 수)보다 커야 완전한 모델을 시작으로 feature를 제거할 수 있다. 즉, 충분한 데이터 샘플이 있어야 한다. 

### Stagewise Selection
변수 1개, 2개, 3개,,, 씩 늘려나가면서 적당한 계수를 찾는 방식
Stepwise Selection보다 더 greedy 하게 앞에서 선택한 변수를 다시 선택할 수 있게 해준다. 

단계마다 조금씩 변수의 기여도를 조정하며, 최적의 모델을 형성하게끔 한다. 

## Choosing the Optimal Model
`test set` 은 절대로 봐서는 안된다. 

