## Sorting Algorithm
### Selection Sort(선택 정렬)
![[IMG-20241025165143.gif]]
각 시행마다 최소값을 찾아서, 맨 앞으로 보내준다. 
- **Average**: O(N^2)
- **Worst**: O(N^2)
### Quick Sort(퀵 정렬)
![[IMG-20241025165143.png]]
배열에서 pivot을 정한 뒤에, pivot 보다 크면 왼쪽으로, pivot보다 작으면 오른쪽으로 보내준다. 
- **Average**: O(Nlog(N))
- **Worst**: O(N^2)
### Merge Sort(병합 정렬)
![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/Ch2, 3 Review of CFDS/IMG-20250304110005.png]]
배열을 절반씩 계속 잘라가면서 잘린 배열마다 정렬을 시행하는 것을 반복해서, 
다시 기존 배열까지 올라가는 정렬 방식이다. 
- **Average**: O(Nlog(N))
- **Worst**: O(Nlog(N))
## Searching Algorithm
![[IMG-20250313142327.gif]]
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Ch2, 3 Review of CFDS/IMG-20250313142327-1.png]]
- 둘 다 트리의 모든 정점을 방문하는 문제를 해결한다. 
- DFS: 경로의 특징을 저장해둬야하는 문제
  → 검색 대상 그래프가 정말 크다면 DFS가 더 효율적이다. 
- BFS: 최단거리를 구해야하는 문제
  → 검색 대상의 규모가 크지 않고, 검색 시작 지점으로부터 원하는 대상이 별로 멀지 않다면 BFS가 효율적이다. 


