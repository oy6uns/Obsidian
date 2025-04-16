복잡한 문제를 **더 간단한 하위 문제들로 나누고, 각 하위 문제를 한 번만 계산한 후 결과를 저장**하여 같은 계산을 반복하지 않도록 최적화를 수행하는 기법
- 결과 저장(Memoization or Tabulation)
	- ==피보나치, Knapsack, LCS 문제==
#### Iterative vs. Recursively
- bottom-up: iterative building up
- top-down: recursively solving with memorization
## vs. Divide and Conquer?
문제를 독립적인 하위 문제들로 나누고, 각각을 해결한 뒤 이를 합쳐 최종 해답을 만드는 방식
1. <span style="color:rgb(229, 93, 98)">Independent</span> Subproblems
	- 하위 문제들은 서로 독립적이며, 결과를 공유하지 않음
	- Merge Sort, Quick Sort
2. <span style="color:rgb(229, 93, 98)">no shared Information</span>

> [!compare] DP vs. Divide & Conque
> - **결과 저장**:
> 	- Divide and Conquer: ==**저장하지 않음**==.
> 	- DP: **==하위 문제의 결과를 저장==**하여 반복 계산을 방지.
> - **합병 단계**:
> 	- Divide and Conquer: 각 하위 문제를 해결한 뒤 결과를 합쳐 최종 해를 생성.
> 	- DP: 저장된 하위 문제의 결과를 참조해 최적 해를 계산.

![[IMG-20241208165453.png]]
## Rod Cutting Problem
![[IMG-20241208171347.png]]
- ==Time Complexity:==
  n + (n-1) + … + 1 = $O(n^2)$
## Bottom-Up Approach
```cpp
#include <iostream>
int main() {
	int p[11] = {0, 1, 5, 8, 9, 10, 17, 17, 20, 24, 30};
	// for reward
	int r[11] = {};
	int s[11] = {};

	for (int i = 1; i<=10; ++i) {
		int q = INT_MIN;
		for (int n=0; n<=i; ++n) {
			q = std::max(q, r[i-n]+p[n]);
			s[i] = n; // best cut을 저장
		}
		r[i] = q;
	}
}
```
- - - 
top-down, bottom-up 둘 다 same asymptotic running time을 가진다. 
그러나, bottom-up approach가 상수 시간정도의 이점을 가진다. 
- call-stack을 manage하는 데 필요한 overhead를 줄일 수 있다. (recursion 안하기에)
- Sequential access to memory

 