## review
query: 나의 현재 상태를 담고 있는 vector
key: 내가 합치고자 하는 대상의 query와의 similarity를 계산하기 위한 vector
value: 계산된 similarity를 기반으로 실제 값을 계산하는 대상이 되는 vector

multi-head attention의 역할 & masked attention network 왜 사용하는지

# ViT: Vision Transformer
진짜 진짜 큰 데이터셋에서만 CNN을 이기더라
- ViT는 inductive bias를 사용하지 않는다, 
	- spatial locality: 어떤 물체인지 알기 위해 주변 픽셀만 살펴보면 된다. 
	- positional invariance: 모든 곳에서 같은 필터를 사용해주면 된다. 
- why? 각 임베딩을 학습하기 위해, 모든 이미지의 부분을 보며 학습을 시키기 때문에
그런데, 엄청나게 많은 데이터에 대해 학습을 시키기에 훨씬 더 많은 삽질 끝에 결국에는 <span style="background:rgba(205, 244, 105, 0.55)">CNN과 같이 자기 주변만 보면 된다는 걸 ViT도 학습을 하긴 한다. </span>
![[IMG-20250424114118.png]]
자기 주변만 보는게 아니라, 멀리 있는 픽셀을 봐도 도움이 되는 경우도 가끔 있기에, CNN보다 성능이 조금 더 잘 나왔다고 볼 수 있다. 

# Data-efficient image Transformers
- Teacher 모델이 있고, 선생님이 가르쳐주는 것을 흡수해가며 학습하는 구조
- Teacher 모델은 transformer보다 가벼운 CNN 모델을 사용
![[IMG-20250608192338.png]]
### Regular training
soft prediction(확률 값) → hard prediction(class label) → 정답과 비교
### Distillation
Teacher가 만들어내는 분포와 Student가 내는 분포를 흉내내게끔 추가적으로 학습시키준다.
#### Loss 식
![[IMG-20250608194444.png]]
- <font color="#d83931">빨간 부분</font>(확률값): 선생님이 만든 것에 softmax를 취해준것과 학생이 만든 것에 softmax를 취해준 것 간의 KL divergence를 계산한다. 기존 Loss에 이걸 추가해준다. 
- <font color="#245bdb">파란부분</font>(실제 정답): 선생님이 만든 답안과 학생이 만든 답안과의 CrossEntropy를 추가로 준다

> [!warning] 청출어람?
> teacher(CNN)은 Inductive bias of spatial locality를 주고, 
> student model은 far-away patches에 대한 relation을 transformer로 추가적으로 배울 수 있기 때문에 더 잘한다고 할 수 있다. 
### ViT의 문제점
1. too much computational cost
2. 



# Swin Transformer

> [!important] Swin Transformer
> 이게 중요하다!!
> 다시 논문 보면서 공부해야할듯

# CvT
Swin Transformer 보다는 주목을 못 받았지만, 
요것도 한 번 확인해봐라
