## Attention?
`집중한다` 라는 뜻이다. 
과거의 여러 snapshot 들 중에 어디에 집중할 것인지. 

**Query, Key, Value 3가지**를 묶어서 **Attention Value**라고 한다. ![[IMG-20241206141344.png]]
**Key와 Value의 값**에 따라 **Query에 가중치**가 다르게 붙는다. ![[IMG-20241206141444.png]]
- Query: Decoder에서 어떤 단어를 만들어내야 하는 step $t$에 있다. 
  그 현재 상태가 Query(중심)가 된다. 
- Query에 가중치를 주기 위해, **Encoder에서 어떤 단어들을 유심히 봐야하는지를 판단**해야 한다. 그것이 **Key**가 된다. 
  ![[IMG-20241206141823.png]]
- 아래 그림에서는 참조 대상이 3개니깐, 3개의 ==attention score==가 나올 것이다. 
  dot product는 그냥 곱해서 더한 것이기에, 아무 실수 값이나 나올 수 있다. 
  따라서, 이를 ==Softmax를 통해서 normalize 시킨 뒤 나온 값을 **Attention coefficients**라 부른다. ==
  ![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 19. Attention Mechanism & Transformers/IMG-20250304110005-1.png]]
- 최종적인 output은 value들의 ==weighted sum==이다.  
  Query와 Value간의 유사성이 높은 애들에게는 **Value에게 높은 가중치**를 주고, 낮은 애들에게는 **Value에게 낮은 가중치**를 준다. ![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 19. Attention Mechanism & Transformers/IMG-20250304110005-2.png]]
### 정리하자면, 
1. Query는 Decoder에 위치하고, 앞선 Value들과의 관계를 계산하기 위해 Encoder의 Value 값들을 들여다 본다.  
   Query-Value 간의 dot-product를 통해 Attention score를 계산하고, 이를 Softmax를 통과 시켜 Normalize한다. 
   → **어느 Value를 더 집중해서 볼 지에 대한 가중치**를 Query를 통해 계산
2. 위에서 계산한 가중치(Attention coefficients)를 Value와 Weighted Sum을 통해 최종 Attention Value를 뽑아낸다. 
![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 19. Attention Mechanism & Transformers/IMG-20250304110007.png]]
그러나 이렇게 Query에 계산된 Attention Value와 Query를 단순히 합치면, 크기가 2배가 되버린다. 따라서, 
3. Fully-Connected, Activation Layer를 거쳐서 다시 원래 벡터의 크기로 되돌려주어야 한다. 
4. 그 이후 기존의 RNN의 진행대로 <SOS, Start of Sentence> 벡터부터 시작하여 Decoding을 해준다. 
## Transformer
#### Review:
기존의 MLP, CNN, RNN 모두 본질적으로는 Fully Connected Layer를 여러개 이어 붙여서 학습시키는 것이다. 

==output $\hat{y}$ 는 input $x$의 weighted sum이다. ==
그렇기에, loss function을 통해 ==**Weight을 가장 잘 최적화하는 방식으로 학습**==이 진행된다. 

#### Transformer: Main Idea
##### Basic Assumption
input $x$ 가 여러 element(객체)라고 가정하고, 이는 **organically related** to each other이라 하자. 
##### Self-Attention
나를 representation할 때, 나만 보는 것이 아니라, 주변의 것들도 함께 포함해보자!
예를 들어, 문장에서 ==**한 단어의 사전적 의미**==만 보는 것이 아니라 **문장의 다른 단어들과 함께** **==한 단어를 더 잘 표현**==해보자~

#### Query, Key, Value
어떻게 사용할 것인가?

아까는 Encoder의 hidden state를 Key, Value로, 
Decoder의 hidden state를 Query로 사용하였다. 

이번에는 **직접 만들어서 사용**할 것이다!!
각 **token $x_i$에 linear weights $W_Q, W_K, W_V$를 곱해서 linear transformation시켜줌으로써** $Q_i, K_i, V_i$를 직접 만든다!!
![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 19. Attention Mechanism & Transformers/IMG-20250304110008.png]]
Query, Key, Value($Q_i, K_i, V_i$) 각각은 모두 동일한 크기를 가질 것이다. 
그러나, 다시 input token $x_i$와 크기를 맞춰주기 위해 $W_o$라는 matrix 하나를 더 곱해준다. 
![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 19. Attention Mechanism & Transformers/IMG-20250304110009.png]]
## Transformer: Step
1. 첫번째 단어가 주인공이라고 해보자! 그러면 첫번째 단어에 대한 벡터가 ==Query==가 된다. 
2. **모든 단어들로부터** ==Key==를 뽑아낸다. 그 후, 첫번째 Query와 Weighted Sum을 통해 ==각 단어에 대해 Query-Key Weight==을 계산한다!![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 19. Attention Mechanism & Transformers/IMG-20250304110009-1.png]]
3. 모든 단어들로부터 ==Value==를 뽑아낸 뒤 전 단계에서 계산한 Query-Key Weight에 각각 곱해준다. 
![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 19. Attention Mechanism & Transformers/IMG-20250304110017.png]]
4. 마지막으로, 계산된 ==Value==값들을 더해준 뒤, 다시 input 차원을 맞춰주기 위해 $W_0$를 곱해주면 한 step이 마무리 된다. 
5. 위 4 step을 모든 단어들에 대해 시행해주면된다. 
> [!result] transform! to new vector
> 최종 도출된 단어 벡터 $z_i$는 $x_1$과 차원은 같으면서 문맥 의미를 어느정도 담고 있는 벡터가 된다!!
> → 문맥을 어느정도 포함하고 있는 단어 벡터를 만드는 contextualize하는 과정이다!

![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 19. Attention Mechanism & Transformers/IMG-20250304110017-1.png]]
→ 위의 예시에서는 각각의 장면에 대한 벡터들이, 영화 전체에 대한 정보를 조금은 내포하게끔하여 약간은 내용이 섞인 벡터가 만들어진다. 
![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/Lec 21. Red-Black Tree/IMG-20250304110017-2.png]]
→ 트랜스포머층을 더 쌓을수록 각 장면 벡터들이 점점 더 전체 영화 내용을 반영하게 된다. 
