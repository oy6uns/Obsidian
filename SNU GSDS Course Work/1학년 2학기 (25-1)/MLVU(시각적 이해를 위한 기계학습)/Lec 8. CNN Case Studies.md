## AlexNet → ZFNet 
구조는 비슷하지만, 필터 설정만 바꿔도 성능이 좋아질 수 있음을 보여줌. 

## VGGNet
- 1개의 7×7 필터:
$$1\times (7 \times 7) \times C^2 = 49C^2$$
- 3개의 3×3 필터 쌓기:
$$3 \times (3 \times 3) \times C^2 = 27C^2$$
왜 3×3 필터 여러 개가 좋을까?
- **파라미터 수가 더 적다**    
- **비선형성 (non-linearity)** 가 더 많이 들어가 → 모델 표현력 증가    
- **복잡한 패턴**도 더 잘 포착 가능
- 구조가 **단순하고 일관됨** (3x3 conv + maxpool 반복)
→ 즉, 작은 필터를 깊게 쌓는 게 **효율 + 성능** 측면에서 유리하다는 걸 보여준 
대표적인 모델이 VGGNet이야. 
###### 단점
FC layer에서의 파라미터가 많고 메모리를 많이 잡아먹는다. 

## GoogLeNet
> 핵심 구조: Inception Module
> → **여러 크기의 필터를 병렬로 동시에 적용**해서 다양한 크기의 특징을 추출

![[IMG-20250416224835.png]]
각각의 Convolution을 거친 값들을 채널 방향으로 Concatenation해줌
#### ✅ 장점
다양한 사이즈 필터, 다양한 해상도의 특징을 추출 가능
#### ❌ 문제점
연산량이 너무 많아질 수 있음
![[IMG-20250416225050.png]]
#### 앞에 3x3, 5x5 앞에 1x1 conv를 붙여서 채널 수를 줄였다. 
- 연산량은 줄이고
- 성능은 유지하거나 오히려 향상시켰다. (deep + multi-scale)

### 전체 구조
Input → Stem Network(Conv-Pool-Conv-Pool-Conv) 
→ ==**Stacked Inception Module**==→ Classifier output

✚ 그라디언트 소실 문제를 해결하기 위해 중간 layer에 임시 분류기를 붙여서 추가적인 loss를 계산해주게끔 하였다. 

## ⭐️ ResNet
#### ❓ 모델은 깊을수록 항상 성능이 좋을까?
	YES!
- 얕은 모델이 할 수 있는 모든 일을, 깊은 모델도 할 수 있음 
- **일부 레이어를 통과로 만들면** 얕은 모델과 똑같이 행동할 수 있음
but, 깊은 모델일수록 최적화가 어려워져서 훈련이 안됨. (+ vanishing gradient)
### Residual Network를 제시!
![[IMG-20250416225619.png]]
→ 그럼 그냥 Idnetity 경로를 진짜로 넣어주자! 
→ gradient가 아래까지 바로 흘러갈 수 있게!
### 그럼 모델 구조는?![[IMG-20250416225859.png]]
1. 축소하여 계산량을 줄임
2. 64개 채널에 대해서만 3x3 Conv 진행
3. residual network 쓸 수 있게 다시 복원 
→ ==**identity mapping + bottleneck 구조의 결합**==이 ResNet의 핵심!

# Inception의 발전
### v2, v3
- “큰 필터는 작은 필터 여러개로 나눌 수 있다.”![[IMG-20250416230206.png]]
- Grid Size는 줄이돼, 정보량은 유지하자 
	stride=2로 grid size 줄이되, 채널 수는 확장해서 정보 보존
- Pooling은 정보를 버리는 것이기 때문에, 이를 Inception 이후에 처리하는 것이 아니라, 병렬로 처리하자!
![[IMG-20250416230313.png]]
### v4
Inception + Residual Connection 적용


