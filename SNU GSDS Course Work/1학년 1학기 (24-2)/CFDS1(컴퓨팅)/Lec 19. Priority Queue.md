> max_heap 자료구조를 배우고, Heap Sort와 Priority에 어떻게 활용되는지를 배운다. 
## Binary Tree
- **Full binary tree**: 각 node가 leaf이거나, degree = 2를 가지는 tree
- **Perfect binary tree**: Full binary tree임과 동시에 모든 leaves가 동일한 depth를 가지고, internal node가 모두 degree=2를 가지는 tree
- <span style="font-weight:bold; color:rgb(229, 93, 98)">Complete binary tree</span>: 마지막 레벨을 제외한 모든 레벨에서 노드들이 왼쪽부터 차례대로 채워지는 binary tree![[IMG-20241207021853.png]]
## Max Heaps
- <span style="color:rgb(229, 93, 98)">Complete binary tree</span> data structure
- <span style="color:rgb(229, 93, 98)">greater than or equal to</span> the values of its child nodes
![[IMG-20241207021854.png]]
#### Max-Heapify
`max_heapify(A, i)`
: A heap list에서 i번째 인덱스 값에 대해 Max-Heapify를 수행
1. 해당 노드와 leaf node를 비교해서 더 큰 값을 부모 노드로 올린다. 
2. 1번의 과정을 재귀 수행한다. 

#### Build-Max-Heap
![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/Lec 19. Priority Queue/IMG-20250304110005.png]]
$⌊n/2⌋$ 값보다 작은 index의 값들에 대해 `max_heapify`를 시행한다. 
→ 위의 예에서는 $n=10$이니깐, $⌊n/2⌋ = 5$보다 작은 index 들에 대해 시행
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 19. Priority Queue/IMG-20250313142327-1.png]]
##### worst time complexity
- 각 `max_heapify`는 $O(lg n)$ 시간복잡도를 가진다. 
- $⌊n/2⌋$ 수의 index에 대해 수행하므로, $O(n)$의 시간복잡도를 가진다. 
- 총 시간 복잡도 = $O(nlgn)$ 인것 처럼 보이지만, 
##### Tighter Bound
- tree의 낮은 height에서 시작할수록 max_heapify를 하는 height가 줄어드므로, ![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 19. Priority Queue/IMG-20250313142327-2.png]]
  <span style="color:rgb(229, 93, 98)"> linear time</span>에 수행이 가능하다. 
## Heap Sort
1. `max_heapify` 수행; $O(lgn)$의 시간복잡도
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 19. Priority Queue/IMG-20250313142327-3.png]]
2. $index=0$ node와 $index = n - 1$ node를 교환한다. 
   이후, $index = n - 1$ node의 값을 지우고, 새로운 배열의 마지막 원소로 대입해준다. ![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 19. Priority Queue/IMG-20250416150918-2.png]]
3. 이후,  $index=0$ node의 원소에 대해 `max_heapify(A, 0)`를 수행한다. 
4. 1~3까지의 과정을 반복시행해준다. 
	→ max heap을 만드는데, $O(n)$ 
	→ 각 heap의 원소들을 max_heapify하는데에 $O(lgn)$의 시간복잡도가 걸린다. 
	→ 따라서 heap sort의 시간복잡도는 $O(nlgn)$ 이 된다. 
## Priority Queues
Max_heap의 각 node에 priority(key)와 더불어 element가 존재하는 것이다. 
각 element는 priority에 따라 정렬된다. 
#### Functions
- **maximum(A)**: $O(1)$
  → return $A[0]$
- **extract_max(A)**: $O(lgn)$
  1. 가장 priority가 낮은 값을 $A[0]$ 과 교환해준다. 
  `{python} A[0] = A[A.heapsize − 1]
  2. max_heapify를 수행한다. 
- **increase_key(A, x, k)**: $O(lgn)$
  → 해당 node부터 root node까지 거슬러 올라가며 값을 교환해준다. 
- **insert(A, x, n)**: $O(lgn)$
  → 새로운 node를 추가한 뒤에, increase_key를 시행해서, 올라갈 수 있는 만큼 node를 상향조정한다.
