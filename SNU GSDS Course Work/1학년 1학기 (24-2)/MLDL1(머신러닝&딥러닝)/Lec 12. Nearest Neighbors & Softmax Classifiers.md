## Nearest Neighbors
- For training?
	$O(1)$
- For prediction?
	$O(N)$
→ prediction에 time complexity가 크기 때문에 효율적이지 않은 알고리즘이다. 
→ test time에 매우 느리다
- 다 다른 사진임에도, $L_2$ 거리를 계산해보면, 다 동일하게 나오기도 한다. 
- 따라서, **픽셀 간의 거리를 단순히 측정하는 것은 유용하지 않다**!
- **높은 차원에서는 필요한 데이터 포인트의 수**가 **기하급수적으로 증가**한다!
##### 해결 방안
- normalizing
- dimension reduction
## Linear Classifier
- unbounded score가 나온다. 
  → 어느정도로 커질지 모름
- hard to interpret
  → 나온 점수가 무엇을 말하는지 판단하기 어렵다
### Sigmoid Function
sigmoid function을 통해 나온 값을 0~1 사이로 재조정 해줄 수 있다. 
![[IMG-20241210004810.png]]
### Softmax Classifier
만약, Label이 3개 이상이라면, softmax function을 고려해볼 수 있다. 
![[IMG-20241210004908.png]]
## Cross-Entrophy Loss
모델이 정답에 가까운 확률을 보이는지를 계산
![[IMG-20241210005751.png]]
## Kullback-Leibler(KL) Divergence
두 확률 분포 사이의 거리를 측정![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 12. Nearest Neighbors & Softmax Classifiers/IMG-20250304110005-1.png]]
