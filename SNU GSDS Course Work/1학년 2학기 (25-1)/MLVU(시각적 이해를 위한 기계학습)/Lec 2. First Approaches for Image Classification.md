# Nearest Neighbor Classifier
- train에서는 그냥 모든 데이터와 라벨을 불러오고, 
	$O(1)$
- predict 시에 모든 label과 비교하여 가장 근접한 label 출력
	$O(N)$
### Q. k=1일때 무엇이 안좋은가
- overfitting 발생 → train 데이터 중 가장 가까운 하나의 샘플만보고 분류(노이즈, 이상치에 민감)
- 일반화 성능 부족
### NN in practice
1. Normalize 해야한다. 
2. Dimension Reduction (차원 축소) 고려해야 한다. 
3. k값은 성능에 민감하기에 훈련셋 / 검증셋으로 나눈 뒤에 성능이 가장 좋은 k를 선택하는 방식이 효과적일 수 있다. 
4. 고차원 데이터에서는 time complexity가 너무 높다 → ANN 사용
# Linear Classifier
> Weighted Sum of input pixels

- KNN과 다른 점이라면, 
	- KNN은 $N$개의 training examples와 비교하지만, 
	- Linear Classifier는 $K$개의 classes와 비교한다. 

### Bias-Variance Trade-off
**Bias**는 어떤 x에 대한 기댓값과 모델로 예측한 값의 차이를 얘기하고, 
**Variance**는 학습된 모델이 새로운 데이터에 대해서 민감하게 반응하냐는 것이다. 
- 모델이 너무 간단하면 **편향(bias)** 이 높아질 수 있다. 
- 모델이 너무 복잡하면 **분산(variance)** 이 높아질 수 있다. 

### i.i.d Assumption! (- Independent and Identically Distributed)
주사위를 던질 때 앞의 결과나 뒤의 결과에 영향을 받지 않고 값이 나온다는 것은 `Independence`에 해당한다. 
주사위를 던질때 일정한 분포의 값으로 결과가 나온다는 것이 `Identical Distribution` 이다. 