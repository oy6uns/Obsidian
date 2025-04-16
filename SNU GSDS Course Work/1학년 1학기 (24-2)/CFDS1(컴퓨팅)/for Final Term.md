## Max heap
- max heap을 만드는데, $O(n)$
- 각 heap의 원소들을 max_heapify하는데에 $O(lgn)$의 시간복잡도가 걸린다. 
- 따라서 heap sort의 시간복잡도는 $O(nlgn)$ 이 된다. 
## Priority Queue
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
## 정리
- 배열로부터 ==Max heap, Priority Queue를 구성==하는데에 **$O(n)$의 시간 복잡도**가 걸린다. 
## Tree search
- BFS
- DFS
## Minimum Spanning Tree
- Prim’s Algorithm
- Kruskal’s Algorithm
