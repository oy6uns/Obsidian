## Review
1. overfitting?
	- specific한 training set에서 너무 과적합되서, 다른 set에서 성능이 안나옴
2. overfitting을 해결하기 위한 방법
	- drop out: neural network를 학습할 때, 일부로 node들을 누락시키는 방법
	  → 코드 짜는 방법 복습해보면 좋을듯
3. momentum?
	- 이전 업데이트 방향을 고려해 현재 업데이트에 가속도를 더함
	- 마치 물체가 관성을 가지고 움직이는 것처럼, 이전 움직임의 방향성을 유지하여 최적점을 더 빠르고 부드럽게 탐색
	- saddle point에 빠지지 않게끔 함
4. batch normalization?
	- 각 층이 출력하는 데이터의 분포가 계속 변화하면서 학습이 불안정해질 수 있다. 
	  → 활성화 함수의 입력 값이 극단적으로 커지거나 작아지는 문제
	  (Exploding/Vanishing Gradients)를 완화
	- 각 층의 입력 데이터를 정규화함으로써 문제를 해결 가능
## Optimizer
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
test 시에는 train에 썼던 값을 사용할 수 없기 때문에, 
기억해둔 값들을 쓴다. 

Linear → Batch Norm → Activation Function 순으로 배열된다. 




