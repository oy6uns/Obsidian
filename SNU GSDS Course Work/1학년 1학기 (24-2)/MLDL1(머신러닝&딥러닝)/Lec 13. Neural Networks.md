## 복습
1. nonlinear decision boundary
   - linear한 feature들을 고차원으로 transform
   - perceptron
2. backpropagation할 때 무슨 일이 일어났나?
   - 오차를 역방향으로 전파하면서 가중치를 업데이트
     → upstream, downstream gradient 다시 공부해보기
3. input data dimension: 20 / output data dimension: 10
   - 10차원: output gradient와 동일해야 한다. 
   - 20 x 10차원: local gradient
   - 20차원: input gradient와 동일해야 한다. 