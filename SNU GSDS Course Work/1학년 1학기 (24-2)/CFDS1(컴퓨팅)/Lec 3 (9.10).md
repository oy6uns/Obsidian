![[IMG-20240912142545.png]]
## Queues
First in First out
## Stacks
Last in First out
- Undo function 
- Parentheses matching: ((){}[])
- Memory stack for function calls

> [!그러면 queue와 stack을 구현할 때 linked list와 array 중에 뭐가 더 효과적일까?]
>
> 다 조금씩 장단점이 있지만, 
> queue를 구현하는데 있어서는 linked list가 효과적일 것이다. 
> 왜냐하면 element들의 위치가 계속 바뀌게 되는 shifting issue로 인해 Array는 구현이 불편할 것 같다. 그렇기에 Linked List가 효과적이다. 
> 
> Stack은 Array와 Linked List 둘 다 효과적일 것 같다. 
## Hash Table
## Trees
### Rooted Binary Trees
### Binary Search Trees
> Rooted Search Tree 이지만, 같은 값을 가지는 Node들이 존재하면 안된다. 
> 노드의 왼쪽에 있는 subtree의 값들은 더 작은 값을 가져야 하고, 
> 노드의 오른쪽에 있는 subtree의 값들은 더 큰 값을 가져야 한다. 

## Graphs
### Simple Graphs
1. no Loops (하나의 노드에서 시작해서 같은 노드로 돌아오는 edge가 없다)
2. no Parallel edges(서로의 노드를 가리키고 있는 2개의 edge)
	→ 보통 알고리즘에서는 simple Graph를 가정하고 얘기를 한다. 

### Terminology
- Vertex: a node(v)
- Edge:a connection between two vertices(v, w)
- path: (v, w, y, z)
	- simple path: A path with unique vertices(중복 vertex X)
	- Cycle: v로 시작해서 v로 끝나는 path
- 하나의 edge로 연결되어 있는 두 vertex를 인접해 있다고 한다. 
- (v, w, y, z)에서 v와 z vertex는 서로 연결(connected) 되어 있다. 
![[IMG-20240912142545-1.png]]

![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/Lec 3 (9.10)/IMG-20250304110005.png]]
## Sorting Algorithm 