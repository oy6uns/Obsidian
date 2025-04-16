self-balancing BST, 모든 height = $O(lgn)$
![[IMG-20241208141036.png]]
- nil은 모두 black, root에서 nil로 가는 모든 path의 검은색 node 수는 동일
- node가 red라면, children은 모두 black이다. 
- longest path는 <span style="color:rgb(229, 93, 98)">shortest path의 2배</span>를 넘을 수 없다. 

## Operations
- Search - binary search tree
  → $O(lgn)$
 - Insert, Delete → $O(lgn)$![[IMG-20241208145519.png]]
 - left-rotate: y가 root가 되고, y.left가 x.right이 된다. 
 - right-rotate: y가 root가 되고, y.right이 x.left가 된다. 
#### Insertion
> 삽입, 삭제 위치 탐색, Rotate, Fix-up은 모두 트리의 높이에 비례하고, Red-Black Tree의 높이는 $O(logn)$으로 제한되기 때문에 시간복잡도는 $O(logn)$으로 귀결된다. 
##### insertion의 핵심은 빨간색 node가 2개가 연달아 나오는 것을 어떻게 처리할지이다. 
1. BST에 넣듯이 넣고, <span style="color:rgb(229, 93, 98)">red로 색칠</span>해준다. 
2. <span style="color:rgb(229, 93, 98)">height balancing과 color 재설정</span> ![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/Lec 21. Red-Black Tree/IMG-20250304110005.png]]
- 만약, 새 node를 삽입했을 때, 부모 노드가 검은색일때는 아무 문제가 안생긴다. 
	uncle: 삼촌(3번 거쳐서 도달하는 node)
1. uncle이 빨간색이면, uncle level의 node들을 black으로 recoloring한다. ![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 21. Red-Black Tree/IMG-20250313142327-1.png]]
2. ==uncle이 triangle side==에 있다면, 1촌 tree를 rotate 시킨다. ![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 21. Red-Black Tree/IMG-20250313142327-2.png]]
3. ==uncle이 line side==에 있다면 전체 2촌 tree를 Rotate하고, recoloring한다.![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 21. Red-Black Tree/IMG-20250313142327-3.png]]
4. root가 red라면 recoloring 한다. 

#### Deletion
##### Deletion의 핵심은 Black node가 지워질 때, Black node의 개수가 path마다 동일하다는 조건을 어떻게 다시 만족시켜줄 것인지이다. 
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 21. Red-Black Tree/IMG-20250416150918-2.png]]
- ==Delete하려고 하는 Node가 black==일 때 위 그림처럼 문제 발생
### Case 1. 
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 21. Red-Black Tree/IMG-20250416150918-3.png]]
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 21. Red-Black Tree/IMG-20250416150919.png]]
==w와 both 자식들이 black==이라면, w를 red로 변경해준다. 
x의 parent가 red라면, parent를 black으로 조정해준다. 
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 21. Red-Black Tree/IMG-20250416150919-1.png]]
아래 형제들간의 균형은 다 맞춰졌는데, 이제 부모들 간의 불균형이 생긴다. 
따라서, x와 w를 부모들로 옮기고 다시 Case 1 rebalancing을 진행해준다. 

### Case 2. 
==w는 black이고, w의 right child가 red==일 때, 
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 21. Red-Black Tree/IMG-20250416150919-2.png]]
형을 할머니로 하고, 엄마를 형의 아들로 해준다. 
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 21. Red-Black Tree/IMG-20250416150919-3.png]]
### Case 3. 
==w는 black이고, w의 left child가 red==일때, 
![[IMG-20241208163659.png]]
### Case 4 
==w가 빨간색==일 때, 
![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 21. Red-Black Tree/IMG-20250416150920.png]]