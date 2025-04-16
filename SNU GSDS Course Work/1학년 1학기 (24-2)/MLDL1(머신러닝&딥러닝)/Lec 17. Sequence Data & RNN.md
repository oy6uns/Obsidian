## Word2vec
- 어떤 단어를 중심 단어로 정하고 그 단어에 대한 주변 단어들에 대해 확률 값을 계산
## GloVe
- word를 vector로 표현할 수 있구나!
- 앞선 Word2vec 보다 더 성능이 좋구나!
→ 2가지 정도만 알면 된다
# RNN
## p. 20 Sentiment Analysis Problem
- 1D-Convolution으로 common pattern을 학습할 수 있다. 
## p.22
- sequence의 length가 모두 다르기 때문에 1D-Convolution에는 한계가 있다. 
  → Encoder를 통해 모든 length에 대응하자!
  → 임의의 길이를 처리할 수 있게끔 해보자 
## Many-to-one
- backpropagation할 때 그냥 Wxh와 Whh에 대해 나온 편미분값들을 주르르 빼주기만 하면 된다. 
- ==backpropagation 계산해보기==
## RNN 장점, 단점
#### 장점
- input이 가변 길이여도 처리가 가능하다. 
- **매 step마다 W의 shape은 동일하다.** 
#### 단점
- 하나씩 문장을 읽어야 하기 때문에 느리다. 
- parallelization이 어렵다. → 앞에서 만들어둔 hidden state가 있어야지 다음 스텝으로 넘어갈 수 있기 때문에
- `Vanishing gradient 문제 `→ 다음 시간
- 앞에서 했던 것을 잊어버리기 쉽다. 
  `fails to model long-range dependence`
  