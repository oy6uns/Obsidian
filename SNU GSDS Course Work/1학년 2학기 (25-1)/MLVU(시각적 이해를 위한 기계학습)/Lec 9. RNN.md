# RNN의 3가지 문제를 해결하기 위한 여러 모델
#### #1. Vanishing Gradient
RNN은 순차적인 데이터를 처리하는데, 가중치가 반복적으로 곱해져서 기울기가 너무 커져버리면 학습이 제대로 되지 않을 수 있다. 
→ long sequence를 다룰 때 특히 문제가 된다. long sequence에서는 기울기 값이 여러 번 곱해지기 때문에, 급격하게 커져서 학습이 불안정해질 수 있다. 
##### → LSTM/GRU도 long range dependency를 완전하게 해결할 수는 없다.
#### #2. input/output sequences of different length
Rnn은 input/output의 길이가 달라지는 것에 잘 대응하지 못한다. 
##### → Seq2Seq model 등장

## Exploding / Vanishing Gradient
![[IMG-20250416230836.png]]
`빨간색부분`: tanh’의 값은 거의 1보다 작다. 이를 t-1번 제곱하면, 매우 작아진다. 
`파란색부분`: W_hh 값을 t-1번 제곱할 때, 만약 값이 1보다 크면 explode하고, 
1보다 작으면 vanishing하게 된다. 
→ 레이어 층 수가 늘어날수록 control 하기 어렵다. 

<span style="color:rgb(255, 0, 0)">CNN에서는 층 수를 그래도 줄일 수 있었지만, RNN에서는 문장의 길이에 따라 레이어 층 수가 고정된다(h의 개수). 따라서, RNN은 지수의 곱 때문에 발생하는 수학적 문제를 해결할 수 없다. </span>
## Solving Exploding Gradients
### Gradient Clipping
threshold를 설정하고,
Gradient 값이 threshold를 넘어서면
Gradient 벡터의 방향은 그대로 가지고, 크기는 threshold가 되게끔 만들어준다. 
→ exploding gradient는 해결할 수도 있지만, 

**그러나, 아직 vanishing gradient는 해결하지 못했다.** 

# #1. Towards Modeling Longer Dependence
> Solving Vanishing Gradients
### Long Short Term Memory(LSTM)
![[IMG-20250416231100.png]]
기존에 FC layer를 통해 backward 계산을 했을 때 발생한 vanishing gradient 문제를 해결하기 위해, 
1. h(==short-term==) 말고, `c(cell-state)`라는 ==long-term memory==를 하나 더 만든다. ![[IMG-20250416231042.png]]
2. 그런데 ==c==를 계속 더하기만 하면 안되니깐, ==forget gate==라는 시스템을 하나 추가하여 이를 조절하게끔 만들어준다. forget gate는 시그모이드 동일하게 h_(t-1)과 x_t 간의 FC layer를 통과한 뒤, ==sigmoid function을 거쳐서 0~1 사이의 값==을 가지게끔 한다. 그래서, ==이전의 기억을 어느정도로 forget==할지를 정한다. ![[IMG-20250416231138.png]]
3. ==forget gate를 추가==하면서, h_(t-1)와 x_t 간의 FC 값이 조금 추가되었으니깐, input gate에 넣을 때에도, ==FC 값을 sigmoid fuction을 통해 조금 조절==해주자!![[IMG-20250416231200.png]]
> [!sigmoid] 2, 3에서의 FC 값
> 2와 3에서 각각 FC layer를 통과한 값을 sigmoid function을 거치게 한다. 
> 그러면 FC layer를 통과한 값이 **0~1 사이의 값**으로 표현될 것이다. 
> - 만약 **long-term memory에 더 집중해야 하는 상황**이면, **step 2의 sigmoid가 1에 가깝게 나와 cell_state값이 거의 그대로 반영**되게끔 한다. 
> - 만약 **short-term memory에 더 가중치를 높게 학습해야하는 상황**이면, **step 3의 sigmoid가 1에 가깝게 나와, 현재의 short-term memory가 input gate로 더 많이 통과**되게끔 한다!!

4. 자 마지막으로 하나를 더 추가하자!! 위에서 Forget gate(얼마만큼 long-term memory를 잊어버릴거야?)와 Input gate(얼마만큼 short-term memory를 잊어버릴거야?)의 합을 통해 cell state의 최종 weight를 구하고, weight 값을 정도로 표현하기 위해 tanh 함수에 넣어준다!
   그 이후, fc layer를 통과한 input data에 sigmoid function을 통과시키고 위의 cell state weight 값과 곱해주면 최종 h_t 값이 나오게 된다!!![[IMG-20250416231302.png]]

> [!LSTM] 궁금한 내용 정리 in LSTM
> 1. 왜 tanh/sigmoid를 같이 써서 input gate에 넣어줄까?
> 	**tanh(·)**, $\tilde{c}_t$
> 		- "무엇을" 기억할지를 제안 (정보 내용, -1 ~ 1 범위)
> 	**sigmoid(·)**: $i_t$
> 		- "얼마나" 기억할지를 조절 (정보의 강도, 0 ~ 1)
> ![[IMG-20250416231759.png]]
> 2. input gate에 넣어줬는데, 왜 output gate에 또 동일한 값들을 넣어줄까?
> **input gate는 기억에 쓰기**, **output gate는 출력에 쓰기**. 용도가 다르다!

### Gated Recurrent Units(GRU)
![[IMG-20250416232543.png|475]]
$r_t$, $z_t$ 를 통해 fc를 통과한 값을 구한다.
$r_t$에 기존의 $h_{t-1}$를 곱한 @한 값과, input $x_t$에 대한 값을 더해주고, $tanh$ 함수로 감싸준다. 
마지막으로, 아까 구한 $g_t$와 $h_{t-1}$의 ==convex combination== 을 구해주면 새로운 **hidden state 값 $h_t$를 구할 수 있다.** 
→ 따로 output gate를 설계하지 않아도 된다. 

> [!key] 핵심 포인트
> 둘다 결국에, vanishing gradient를 막기 위해 exponential의 거듭제곱 꼴만으로 gradient가 업데이트 되지 않게 한다. 
> **FC layer를 거치지 않는 경로를 하나를 추가**함으로써 direct로 gradient가 업데이트 될 수 있게 하는 것이 포인트이다!!

## #2. Solutions for variable lengths
우리가 배웠던 **RNN은 1대1 대응을 가정**한다.

그러나, 번역에서는
- 언어에 따라 번역 시에 문장 길이가 변할 수 있다
- 단어 배치의 순서가 달라질 수 있다. 

실제로 우리가 문장을 번역할 때, 한 단어마다 번역을 하지 않는다. 한 문장을 모두 읽은 이후에 번역을 한 번에 하곤 한다. 
**그러면! 문장이 끝날때까지 기다렸다가, 그 때 번역을 시행하면 되겠다!!** 가 새로운 아이디어다. 
### Seq2Seq Model
![[IMG-20250416232646.png]]
#### Encoder
15개 단어로 이루어진 문장이라고 하면, 마지막 hidden state인 $h_{15}$가 **entire sequence를 encode**하고 있다고 하자. 
$h_{15}$까지를 ==**encoder**==라고 부른다. 
#### Decoder
==**Encoder**==의 last hidden state로 ==**Decoder**==의 시작 hidden state를 initialize시켜준다. 
이후, <SOS, Start of Sentence> token을 첫번째 input으로 하여금 **Decoding을 진행한다.** **다음 hidden state**는 **context와 더불어 position 정보가 같이 encoding된 값**일 것이다. 

그러나, 첫번째 단어부터 틀리게 되면 뒤의 단어들을 그냥 모조리 틀리게 된다. 
그래서, **==Teacher Forcing==** 이라는 방법을 사용하게 된다. 
- training 시에는 ground truth 값인 $y_{t-1}$를 $\hat{y}_{t-1}$ 대신 넣어준다. 
- inference(test) 단계에서는  $y_{t-1}$값은 모르니깐, 어쩔 수 없이 $\hat{y}_{t-1}$ 값을 통해 추론을 진행한다. 
→ 이게 될까?! 싶지만 잘되더라~~

<span style="color:rgb(255, 0, 0)">단점: 번역된 문장이 실제로 존재하지 않을 경우에는 학습을 할 수 없다 ㅠㅠ</span>

# RNN의 장단점
![[IMG-20250416233008.png]]




