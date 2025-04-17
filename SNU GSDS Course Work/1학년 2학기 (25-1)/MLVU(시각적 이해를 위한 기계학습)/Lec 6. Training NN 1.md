#### for Machine Learning
- Activaton function
- Learning rate
- Weight Initialization
- Regularization
# Activation Functions
> How to choose an activation function for my neural network?

## Sigmoid Function
> 3 Problems
### Killed Gradients
![[IMG-20250416200634.png]]
### Not Zero-centured Output
![[IMG-20250416200825.png]]
항상 양수 $\times$ 양수 $\times$ 양수를 가진다. 
→ 즉, gradient가 항상 양수 혹은 전체적으로 음수로 구성된다. 
→ 경사 하강법이 “최적 방향”으로 학습되지 않고, 이상한 경로로 돌아가는 경우가 많아짐
→ ==**학습 효율이 떨어지고, 수렴 속도가 느려짐**==

## Tanh Function
$$ tanh(x)=2\sigma(2x)-1$$
tanh(x)도 결국은 scaled sigmoid neuron이다. 
따라서, <span style="font-weight:bold; color:rgb(51, 112, 255)">kill the gradients</span> 문제가 발생한다. 

## ReLU (Rectfied Linear Unit)
- <span style="color:rgb(51, 112, 255)">Not zero-centured output</span>
- <span style="color:rgb(51, 112, 255)">Not differentialble when x=0</span>
- <span style="color:rgb(51, 112, 255)">Dead ReLU problem: </span>만약 initial output이 음수를 가지면, never updated 된다. 
## Leaky ReLU
> ReLU 함수에서 음수값일때 약간의 slope를 추가한 함수
- An additional hyperparameter (slope where x<0)
## ELU (Exponential Linear Unit)
모든 benefits of ReLU를 가지지만, $exp()$는 computationally expensive 하다. 


> [!정리] Activation functions in Practice
> - Use ReLU
> - Try out Leaky ReLU or ELU to squeeze out some marginal gains
> - Do NOT use **sigmoid** or **tanh**
> 	- killing gradients가 가장 치명적이다.

# Data Preprocessing
- Zero-centering & Normalization
- <span style="font-weight:bold; color:rgb(51, 112, 255)">PCA</span> & whitening
	- whitening? 데이터가 타원형으로 퍼져 있다면, 각 축이 독립적이고, 균일하게 퍼진 데이터로 바꿔줌![[IMG-20250416202015.png]]
### Data Preprocessing in Practice
- AlexNet → image의 평균을 전체 image 픽셀값에서 빼준다. 
- VGGNet → 각 채널별 평균을 각 채널의 픽셀값에서 빼준다. 
- ResNet → 각 채널별 평균과 표준편차를 각 채널의 픽셀값에서 빼주고, 나눠준다. 
- Youtube 8M → PCA, whitening

# Data Augmentation
Horizontal Flips, Random Crops, Scaling, Color Jitter, …
# Weight Initialization
### 왜 weight 초기화가 중요할까?
너무 작거나 큰 값으로 초기화 시에 **Gradient Vanishing** 문제가 발생한다. 
1. Xavier Initialization
	입력과 출력의 분산이 거의 비슷하게 유지되도록 weight scale을 조정
	but, ReLU에서는 음수를 다 0으로 죽여버려 분산이 반으로 준다
	→ Xavier보다 크게 초기화해주어야 한다!
2. Kaiming Initialization(a.k.a. He Initialization)
	분산을 보존하게 weight 초기화 
# Learning Rate
- Learning Rate Decay
- initial Warmup - 처음에 아주 큰 learning rate까지 올렸다가 계단식으로 확 줄여 나간다. 
