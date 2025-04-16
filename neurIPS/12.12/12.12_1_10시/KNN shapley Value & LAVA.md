# Data Valuation

#### How to quantify data contribution?
![](스크린샷%202023-12-12%20오전%201.16.51.png)
## Machine Learning 측정 방법

## LOO(Leave-One-Out)
## Shapley value
> [!  Unique properties]
> - Efficiency
> - Fairness
> - Linearity

There are computational challenges in shapley value. 
#### Monte Carlo estimation
*Two Heuristics to Accelerate MCE*
- Truncated Monte Carlo
- Gradient Shapley

### Shapley vs LOO
**Why is Shapley better at identifying data quality than LOO?**

## Data Banzhaf: a Robust Data Value Notion

## Connections between LOO, Shapley, Banzhaf
- LOO, Shap Value, and Banzhaf value all belong to a class value notions called semi-values 
	- What is a semi value?!

## 발전 과정
- Monte Carlo 를 사용한 Shapley
- Gradient Shapely
- LOO
- KNN-Shapely
- Threshold-KNN Shapley

> [!NOTE]
> efficiency와 computing power를 모두 만족시킬 만한 값을 찾기 위해
> Data-OOB (Out of Bag) 이라는 아이디어를 접목시켰다. 

#### 그래서 나온것이 **LAVA**




