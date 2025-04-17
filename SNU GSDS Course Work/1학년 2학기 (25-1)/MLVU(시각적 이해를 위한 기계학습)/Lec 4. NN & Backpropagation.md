현재까지 배운걸 정리하면, 
1. Linear Model 으로 Image Classification 을 진행하는데, 
2. Softmax Loss를 사용해서 오차를 계산하고
3. Stochastic Gradient Descent를 사용해서 최적점을 찾아간다. 
# Issues with Linear Classifiers
1. 현실 속 이미지는 단순하지 않다. 
	이전까지 다뤘던 단순한 **픽셀 벡터 → 클래스 매핑** 방식은 실제 이미지에선 잘 안먹힌다. 
	왜냐하면 이미지엔 **복잡한 구조, 위치, 방향성** 등이 있기 때문이다. 
	→ Raw pixel 말고 진짜 “특징(feature)”를 뽑자 → Feature Extraction
2. Linear decision boundary의 한계가 극명
# activation function의 등장
Linear Classifier는 여러층을 쌓아도 의미가 없지만, 
중간에 비선형 층을 추가함으로써 여러 층을 쌓을 수 있게 되었다. 
1. 복잡한 비선형 함수를 근사할 수 있게 되었다. 
2. 이를 통해 복잡한 패턴과 구조를 학습할 수 있게 되었다. 

# Computing Gradients
![[IMG-20250416180818.png]]
Loss가 감소해야하는 방향을 알려주기 위해. 
기존의 Weight 값에 $LR\times Weight 미분값 \times Loss$ 값을 빼준다.
# Backpropagation
![[IMG-20250416181316.png]]
## Backpropagation with Vectors 
![[IMG-20250416181421.png]]![[IMG-20250416181446.png]]