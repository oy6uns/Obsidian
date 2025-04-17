# Spatial Locality & Positional Invariance
#### Spatial Locality
: 각 필터는 인접한 픽셀만 관찰한다. 
→ 지역적 특징을 학습하도록 설계되었다. 작은 영역에서 학습한 패턴을 큰 영역으로 확장하면서 전역적인 특징을 학습한다. 
- pattern이 match되면 높은 점수를 주는 filter를 설계
- patter이 **어디에 있고**, **얼마나 큰지**를 모르기 때문에 우리는 **multiple-sized** filter를 설계해야 한다. 
#### Positional Invariance
: 같은 필터를 이미지의 모든 위치에 동일하게 적용한다. 
→ 객체가 이미지 내에서 위치가 다르더라도 동일한 패턴을 인식한다. 
→ Translation Invariance: CNN은 객체가 이동하거나, 위치가 변경되더라도 이를 감지하는데 효과적이다. 

# CNN
==output filter 개수 만큼 bias term 존재==
![[IMG-20250416181819.png]]
→ fully-connected 시보다 parameter 수가 훨씬 더 줄일 수 있다. 
$$ W'=\frac{(W-F+2P)}{S} +1$$
## Nested Convolutional Layer
여러 계층의 conv-layer가 각각 어떤 역할을 수행하는지![[IMG-20250416182524.png]]
![[IMG-20250416182627.png]]
처음부터 
- low-level: 클래스 분류에 가장 유용한 추상적 특징
- mid-level: High-level feature를 구분짓는 데 필요한 특징들
- high-level: Mid-level feature를 만들기 위해 필요한 아주 기본적인 시각적 패턴들
![[IMG-20250416182732.png]]

## Fully-connected vs. Convolutional Layer
크기를 마음대로 정할 수 있기 때문에 서로가 서로의 special case가 될 수 있다. 

## Pooling Layer
Pooling Layer 시에는 Parameter가 따로 존재하지 않는다. 

