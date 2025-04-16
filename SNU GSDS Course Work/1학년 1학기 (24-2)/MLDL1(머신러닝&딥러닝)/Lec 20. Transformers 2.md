#### Attention?
앞에서 했던 것들을 잊어버릴 수 있으니깐, 
어느 부분을 더 중요하게 참조할건지를 판단하게 해주는 장치
## Remaining Questions
1. output이 sequence가 나올텐데, downstream task에 어떻게 적용시켜서 풀어나갈 것인지에 대한 논의
2. transformer를 학습을 어떻게 시킬까?
   → 학습되는 Weight? $W_q, W_k, W_v, W_o$
3. RNN은 Sequence의 순서를 자연스럽게 지켰다. 
   → 얘는 자기 자신이 주인공이 되는 경우가 계속 있다. **순서에 상관없이** 동일한 값으로 계속 처리된다. 
## #1. 무엇을 할 것인가?(applying to downstream task)
- Classifier를 어디에 달아야 될까?
  → 가장 무식한 방법은 나온 $z_i$ 값들을 평균 내서 Classifier나 Regressor를 다는 것이다. 
![[IMG-20241206171817.png]]
- 그런데, 토큰들을 단순히 평균을 내는 것은 오류가 많다. 
  예를들어, **I ate pizza** 와 **pizza ate I**는 완전히 다른 의미이지만, 평균을 내면 그 값이 동일하게 나오게 된다. 
- 그래서 나온 Idea가 평균이 아니라, Weighted Average를 내보자!
  맨 앞에 **==범위 토큰(아무 의미가 없는 빈 토큰)==** 인 **CLS**를 하나 붙인다. ![[IMG-20241206172139.png]]
  → 특정 토큰에 의존하지 않고, 가짜 토큰 하나가 전체 문장의 Attention을 알아서 배우게끔 한다. 그 위에 Classifier나 Regressor를 붙인다. 
## #2. 어떻게 학습되나? Inside the Transformer
## for Encoder
#### Step 1. Input Embedding
Input: sequence of tokens
→ 항상 input과 output의 길이와 차원은 동일해야 한다. 
#### Step 2. Contextualizing the Embeddings
![[IMG-20241206172948.png]]
![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 20. Transformers 2/IMG-20250304110005-1.png]]
→ ==지난 시간에 했던 Self-Attention Contextualizing을 진행한다. ==
- - - 
##### 새롭게 등장하는 **Multi-head** 개념
**Self-Attention**은 문장 내에서 각 단어가 다른 단어들과 어떻게 연관되는지를 파악하는 방법이었다.  예를 들어, 다음과 같은 문장이 있다고 하자. 
> "The cat sat on the mat."

→ **"cat"** 이라는 단어가 있을 때, **"sat"** 와의 관계를 파악하려면, **"cat"** 의 Query 벡터와 **"sat"** 의 Key 벡터를 비교하여 얼마나 밀접하게 연결되어 있는지 계산한다. 이렇게 관계가 잘 반영되면,  **"cat"** 이 **"sat"** 과 연관이 있음을 잘 이해하게 된다. 

==**Multi-Head Self-Attention**은 Self-Attention을 여러 번, "다양한 방식으로" 수행하는 방법이다.==
→ **"cat"** 은 **”sat”** 과도 관련이 있지만, **”mat”** 와 **“on”** 과의 관계도 중요할 수 있다. 한 번의 Self-Attention으로 이 모든 관계를 잘 이해하기는 어렵다. 
따라서, 각 단어들이 문장에서 다른 단어들과 **어떻게 연결되는지에 대한 다양한 시각이 필요하기 때문**에!! **==Multi-Head Self-Attention**을 사용해야 한다==. 
- - -
$W, Q, V$ 벡터를 하나씩 만드는게 아니라, 여러 쌍을 만들어두고, 학습을 진행한다. 
![[Img/Img/SNU GSDS/1학년 1학기/MLDL1(머신러닝&딥러닝)/Lec 20. Transformers 2/IMG-20250304110005-2.png]]
그렇게 아까 구한, Attention Vector를 8개정도 만들어서 이어 붙인다. 
8개는 실험적으로 구한 값으로, 한 단어가 대략 8개 정도의 의미를 가지면 충분할거라 생각한 거 같다. 
⬇️ **전체 과정**
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 20. Transformers 2/IMG-20250313142327-1.png]]
#### Step 3. Feed-forward Layer
각각의 토큰들을 Fully connected layer에 통과시켜서 **토큰 자기 자신의 의미를 한 번 더 바꿔준다**. 
- Multi-Head Attention Layer를 통과하든
- Feed Forward Layer를 통과하든
두 Layer의 결과값의 차원은 동일하다. 

**Add & Norm layer**로 바로 향하는 경로**(=Residual Connection)**를 추가해주는것은
- Vanishing Gradient Problem을 완화시켜준다. 
- 이를 통해 기울기가 더 잘 흐를 수 있게 해준다. 
#### Step 4. Positional Encoding
Transformer는 순차적인 구조가 아니기 때문에 **단어의 순서**를 모델이 인식할 수 없으므로, [Positional Encoding](../../../Img/Positional%20Encoding.md)을 사용하여 각 단어가 문장에서 차지하는 **위치 정보**를 임베딩에 추가한다. 
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 20. Transformers 2/IMG-20250313142327-2.png]]
Input Embedding에 Positional Encoding을 단순히 더해준다. 
Input Token이 64차원이면 Positional Encoding Matrix도 64차원이 되어야 할 것이다. 
- Positinal Encoding Matrix에서 멀어지면 달라지고, 주변의 값들은 비슷해야 한다. 

## for Decoder
#### Step 5. Masked Multi-head Self-attention
Decoder에서 문장을 생성할 때, 
- 첫 번째 step에서는, <SOS, Start of Sentence> 토큰만 주어주고, 나머지 토큰들은 쓰레기 값들을 넣어준다. 나머지 값들을 숨긴다고 하여 Masked라는 이름을 붙이는 것이다. 
- 두 번째 step에서는, <SOS, Start of Sentence> 토큰과 예측한 두번째 토큰을 제외한 나머지 토큰들을 Masking 처리해준다. 
**masked token이 필요한 이유**는 transformer에서는 **같은 길이의 token이 input**으로 들어가면, **같은 길이의 token이 output**으로 나와야하기 때문이다. 
#### Step 6.  Encoder-Decoder Attention
Multi-Head Attention을 보면, 
- Encoder 때는, **input token들에서 뽑은 Query, Key, Value**를 Multi-Head Attention에 넣어주었다. 
- Decoder 때는, 
  - ==**Query는 Decoder의 Input==에서 올라오고**
  - ==**Key, Value는 Encoder의 최종 Output**==으로부터 올라온다. 
##### Why?
- **Query** (Decoder의 입력)는 **현재 생성하고자 하는 단어**와 관련된 정보를 담고 있다. 즉, Decoder는 이전까지의 생성된 단어들(또는 상태)을 기반으로 다음 단어를 예측해야 하기 때문에, **Query는 Decoder의 입력**으로부터 유도된다.
- **Encoder**는 입력 문장을 처리하여 **입력의 의미**와 **문맥**을 이해한다. 이 과정에서 생성된 **Encoder의 출력**은 각 단어의 고차원 표현을 포함하고 있으며, 이는 Decoder가 생성할 문장의 **정확한 맥락**을 제공하는 데 사용된다. 
  → Key는 “어디서” 중요한 정보를 가져올지에 대한 기준을 제시하고
  → Value는 실제 내용을 제공한다!
결과적으로, Encoder의 Key와 Value로부터 “입력 문장의 의미적 정보를 전달 받은” Decoder의 Query는 다음에 나올 단어를 해당 의미를 바탕으로 예측해낸다!

*좀 더 쉽게 설명하자면,* 
“I love you” 라는 문장을 한글로 번역한다고 하자. 
첫번째 Decoder의 output이 “나는” 이 나왔다고 하자. 
이때, Decoder는 **문장의 의미**를 Decoder의 첫번째 output “나는”으로부터 배우는게 아니라, **“I love you”라는 Encoder의 문장으로부터 배워야하기 때문에** Encoder로부터 Key와 Value를 전달받는 것이다!!

당연히 Mask는 씌우면 안될 것이다!
원문을 잘 해석해서 그 의미를 바탕으로 다음에 올 단어를 잘 추론해야하기 때문에
#### Step 6. Fc layer
위의 Encoder-Decoder 구조를 반복해서 실행함으로써, <EOS, End of Sentence>가 나올때까지 단어를 하나씩 도출해내서 결과적으로 하나의 문장을 생성한다. 
#### Step 7. Linear layer, Softmax layer
Linear layer, Softmax layer를 거친 문장 [실제 벡터와의 차이를 Cross-Entrophy loss를 통해 계산](../../../Img/실제%20벡터와의%20차이를%20Cross-Entrophy%20loss를%20통해%20계산.md)해준다. 
beam search: 단어 하나만 보고 top-k를 선택하는 것이 아니라, 단어들의 조합을 보고 더 많은 조합들 중에서 top-k를 선택해서 greedy함을 줄인다. 

## Encoder Based Models

## BERT
> Large-scale pre-training of word embeddings using Transformer Encoder
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/MLDL1(머신러닝&딥러닝)/Lec 20. Transformers 2/IMG-20250313142327-3.png]]
#### Training task 1: Masked Language Modeling(MLM)
빈칸 뚫어놓고, 빈칸에 들어갈 단어 맞추기!
BERT는 입력 문장에서 **임의의 단어 15%** 를 무작위로 마스킹하고, 그 마스킹된 단어를 **모델이 예측**하게 학습한다. 
→ 단어 레벨의 정확도를 높이기 위함
#### Training task 2: Next Senetence Prediction(NSP)
두 문장을 가져와서 연결 시킨다음에, 
두 문장이 원래 연결성이 있는지 없는지를 T/F로 예측
→ 문장 레벨의 정확도를 높이기 위함
##### 두 task 모두 인간이 직접 Labeling할 필요가 없기 때문에, 굉장히 많은 데이터를 학습시킬 수 있다. 

## ViT
CNN은 가까운 정보끼리 관계가 많다는 것을 짧은 시간 내에 학습하지만, 
Trasformer는 정말 많이 학습을 해야지 겨우 가까운 정보끼리 관계가 많다는 것을 학습할 수 있다. 


