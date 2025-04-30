# Dot-Product Attention
![[IMG-20250422111746.png|675]]
hidden state를 query에 각각 어느정도로 반영시켜 줄지를 계산하기 위해, 
- - -
![[IMG-20250422111846.png]]

각 hidden state를 key로 하여 query와 곱한 뒤에, softmax를 계산한다. 
- - - 
![[IMG-20250422111925.png]]
각 hidden state에 가중치를 어느정도를 가져야 하는지를 
key와 query로 부터 계산해냈으니깐(Attention Coefficient),

1. 각 hidden state를 다시 value로 설정하여 
2. 위에서 계산한 Attention Coefficient와 Weighted Sum을 해주면, 
3. 각각의 hidden state를 어느정도로 다음 step에 사용할지가 반영된 Attention Value값을 구할 수 있다. 
- - - 
![[IMG-20250422112009.png]]
1. 구한 Attention value를 Decoder hidden state에 concatenate 시키고, 
2. 다시 기존 hidden state size를 맞춰주기 위해 fully-connected layer를 붙인다. 
3. 그러면 다음 Decoder hidden state $\tilde{s_{0}}$ 를 구해낼 수 있다. 


> [!Important] Visual Attention
> p. 12~13 복습할 때 자세히 다시 봐볼 것!

# Transformer
Key: 너 얼마나 이 값에 집중할거야?
Query: Key에서 구한 가중치를 곱해줄 reference frame!
![[IMG-20250422115818.png]]
결국에 모든 이미지를 Key로 하여 
Attention Coefficient를 각 이미지에 계속 곱해주는 과정을 거치기에
결국에는 모든 이미지의 embedding이 균등하게 섞여간다. 

# Positional Encoding
### 1. 다양한 주기 (frequency)
- $2i/d_{\text{model}}​$ → 인덱스가 커질수록 변화 속도는 느려짐    
- 앞쪽 채널은 빠르게 변화 (세밀한 위치 구분)
- 뒤쪽 채널은 천천히 변화 (전역적 위치 구분)
### 2. 인접한 위치(pos)는 비슷한 인코딩
→ 모델이 자연스럽게 단어 순서를 반영할 수 있게 함
### 3. 모든 위치의 조합이 유일함
→ 서로 다른 위치는 서로 다른 벡터를 가짐


> [!important] Positional Encoding
> Positional Encoding에 대해 좀 더 자세히 공부해보자!


