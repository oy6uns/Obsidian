![[IMG-20250122144123.png]]
엘리스와 밥이 주사위를 각각 던져서 두 주사위 모두 앞면이 나올 확률을 계산해보자. 
단순히 1/2 + 1/2 가 되어서 앞면이 나올 확률은 1이 된다고 단순하게 생각하면 오산이다. 
![[IMG-20250122144232.png]]
겹치는 경우의 수가 존재하기 때문에 위의 확률을 합에서 빼주어야 한다. 
따라서, 각 사람이 주사위의 앞면이 나올 확률이 **Disjoint**하지 않기 때문에 단순히 두 확률을 더하는 것을 접근하는 것은 잘못되었다. 

## Partial Information
때로는 실험과 관련된 일부 부분 정보를 사전에 알게 될 수 있다. 이 정보는 특정 사건의 확률을 변화시킬 수 있다. 
이를 ==**Conditional Probability(조건부 확률)**== 라고 한다. 
![[Img/Img/SNU GSDS/1학년 1학기/부트캠프/확률과 통계/Day 2/IMG-20250304110005.png]]
![[Img/Img/SNU GSDS/1학년 1학기/부트캠프/확률과 통계/Day 2/IMG-20250304110007.png]]
## Conditional Probability Axioms
1. Nonnegativity
	어떤 사건 $A$의 확률은 항상 음수가 될 수 없다. 
2. Normalization
	조건부 확률에서, 사건 $B$가 이미 발생했을 때 $B$의 확률은 항상 1이다. 
	![[Img/Img/SNU GSDS/1학년 1학기/부트캠프/확률과 통계/Day 2/IMG-20250304110007-1.png]]
3. Additivity
	![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/부트캠프/확률과 통계/Day 2/IMG-20250313142327-1.png]]
### Properties of Condition Probability
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/부트캠프/확률과 통계/Day 2/IMG-20250313142327-2.png]]
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/부트캠프/확률과 통계/Day 2/IMG-20250313142328.png]]
![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/부트캠프/확률과 통계/Day 2/IMG-20250416150918-2.png](Img/IMG-20250416150918-2.png)
![[IMG-20250122152202.png]]
## Example: Cointoss
![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/부트캠프/확률과 통계/Day 2/IMG-20250416150918-3.png](Img/IMG-20250416150918-3.png) ![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/부트캠프/확률과 통계/Day 2/IMG-20250416150919.png](Img/IMG-20250416150919.png)
→ B의 표본 공간중 동전이 교대로 앞면과 뒷면이 나오는 건 하나이기에 확률은 $1/4$가 된다. 

