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



