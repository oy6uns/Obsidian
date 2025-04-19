## 수식 (1)
$$ \mathcal{L}=\mathcal{L}_0+λ\mathcal{L}_{reg},\\ \; \\where\;\mathcal{L}_{reg}=∑_{i,j}A_{ij}∥f(X_i)−f(X_j)∥^ 2=f(X)^⊤Δf(X). $$
Node Classification task를 해결하기 위한 graph-based semi-supervised learning에서
**graph Laplacian regularization term을 loss함수에 추가**함으로써 정보가 그래프를 통해 smoothing될 수 있게 해준다.
기존의 $\mathcal{L}_0$는 labeled part에 대한 supervised loss를 나타낸다.

### 그럼 $\mathcal{L}_{reg}$는 어떻게 유도될까?
그래프 정규화 항은 **인접한 노드들의 특징 벡터 차이를 최소화하는 역할**을 한다!
$$ \begin{align}
\mathcal{L}_{reg}& = ∑_{i,j} A_{ij}(f(X_i)^⊤f(X_i) − 2f(X_i)^⊤f(X_j) + f(X_j)^⊤f(X_j)) \\
&= 2\Big(f(X)^\top Df(X) - f(X)^\top Af(X)\Big) \\ &= 2\Big(f(X)^\top(D-A)f(X)\Big)\\&= 2\Big(f(X)^\top\Delta f(X)\Big)
\end{align} $$
여기서,
- $A$ : 그래프의 **인접 행렬 (Adjacency Matrix)**
- $D$ : 그래프의 **차수 행렬 (Degree Matrix)**, 즉 $D_{ii} = ∑_j A_{ij}$
- $Δ = D−A$ : **그래프 라플라시안 행렬 (Graph Laplacian)**

❓기존의 방식은 수식 (1)과 같이 **$\mathcal{L}$(loss)에 추가적인 regularization 항을 추가하는 방식**을 통해 노드 간의 정보가 학습 시에 전달 될 수 있도록 하였다. 그러나, 학습하는 함수 $f(X)$ 자체에 노드 간의 연결성은 고려되지 않는다.

💡 이 논문은 학습하는 함수 $f(X, A)$ 를 제시하여
연결된 노드들의 **표현을 유사하게 만들도록 강제하는 정규화 항을 손실함수에 추가하는 대신, 신경망 자체가 그래프 구조(인접행렬 $A$)를 직접 학습하도록 설계한다. 
→** 인접행렬을 학습시에 사용하면, ⭐️ **레이블이 있는 노드뿐만 아니라 없는 노드의 정보도 학습할 수 있게 된다.** ⭐️


> [!Important] 논문의 2가지 Contribution
> **📌#1**. graph에서 **직접적으로 작동하는 neural networks model**을 설계하기 위한 simple & well behaved **layer-wise propagation rule 을 제시**한다. 그리고 위 rule이 어떻게 **spectral graph convolution의 1차 근사치로부터 유도될 수 있는지**를 보인다.
> **📌#2**. 위의 모델이 **fast & scalable semi-supervised classification of nodes in a graph**를 잘 달성한다는 것을 보인다.

## 수식 (2)
는 최종식이라, 나중에 모든 정리가 끝나고 다시 다루기로 하자.

# 2.1 Spectral Graph Convolutions
## Contribution #1.
