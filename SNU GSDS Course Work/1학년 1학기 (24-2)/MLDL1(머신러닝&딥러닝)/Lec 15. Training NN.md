## Review
1. Sigmoid Function이 왜 안좋을까
	- **==killing gradient==**
	- not zero-centered output(minor)
	- exponential function이 있어서 계산이 오래 걸린다. (minor)
2. ReLU Function이 왜 안좋을까
	- **==Dead ReLU problem==**
	- 0에서 미분이 불가능 하다(minor) → smoothing으로 해결 가능
3. Data Augmentation이 무엇일까

## Sigmoid Function의 문제
1. ==**Killed Gradients**==
   왜 Sigmoid는 안쓰는지![[스크린샷 2024-12-10 오전 4.03.30.png]]
2. Not Zero-centered Output
   gradient update가 오른쪽 위, 왼쪽 아래로밖에 안된다. ![[스크린샷 2024-12-10 오전 4.06.12.png]]
## Tanh Function의 문제
sigmoid와 동일하게 ==**Killed Gradients**== 문제 발생
## ReLU(Rectified Linear Unit)
- Zero-centered output이 아니다. 
- x=0에서 differentiable 하지 않다. 
- **==Dead ReLU problem==** : initial output이 negative면, 앞으로 계속 Update되지 않는다.
## Leaky ReLU
- hyperparameter 값(slope where x<0)을 정해야 한다. 
## ELU (Exponential Linear Unit)
- exp()는 computational expensive하다. 

## Data Preprocessing & Augmentation
Data Augmentation은 sematics를 해치지 않는 선에서 modify해야 한다. 

## Weight Initialization
#### Xavier Initialization
Xavier Initialization은 **뉴럴 네트워크를 초기화할 때, 각 층의 가중치(W)를 적절히 설정**하여, 학습 과정에서 **출력의 분산이 폭발하거나 소멸되지 않도록** 하는 방법이다. 
→ **입력 데이터의 분산**과 **출력 데이터의 분산**이 **일정하게 유지되도록** 초기 가중치를 설정한다. 
![[스크린샷 2024-12-10 오전 4.41.05.png]]