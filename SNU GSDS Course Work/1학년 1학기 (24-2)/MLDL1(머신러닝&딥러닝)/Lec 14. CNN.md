## CNN의 2가지 특성
#### Spatial Locality
: 각 필터는 인접한 픽셀만 관찰한다. 
→ 지역적 특징을 학습하도록 설계되었다. 작은 영역에서 학습한 패턴을 큰 영역으로 확장하면서 전역적인 특징을 학습한다. 
- pattern이 match되면 높은 점수를 주는 filter를 설계
- patter이 **어디에 있고**, **얼마나 큰지**를 모르기 때문에 우리는 **multiple-sized** filter를 설계해야 한다. 
#### Positional Invariance
: 같은 필터를 이미지의 모든 위치에 동일하게 적용한다. 
→ 객체가 이미지 내에서 위치가 다르더라도 동일한 패턴을 인식한다. 
→ Translation Invariance: CNN은 객체가 이동하거나, 위치가 변경되더라도 이를 감지하는데 효과적이다. 

## Nested Conv-layers


![[IMG-20241210010530.png]]