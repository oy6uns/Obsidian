## Graphs
- $G = (V, E)$
	- $V$: <span style="color:rgb(229, 93, 98)">vertices</span>, node라고 불리는 꼭짓점
	- $E$: <span style="color:rgb(229, 93, 98)">Edges</span>, 두 vertices를 잇는 connection
- Undirected graph / Directied graph
- **Degree**: 연결된 총 edges 수 
  **In-degree**: 안으로 들어오는 화살표(directed edges) 수
  **Out-degree**: 밖으로 나가는 화살표(directed edges) 수
- **Walk**: 경로(sequence of edges)
- **Path**: 중복되는 $V$와 $E$가 없는 Walk
- <span style="color:rgb(229, 93, 98)">Complete graph</span>: 모든 vertex(꼭짓점)이 다른 vertex(꼭짓점)으로의 direct edge가 존재하는 graph
- Cycle: 동일한 vertex에서 출발하여 다시 vertex로 돌아오는 path
- Acyclic graph: cycle이 없는 graph
- Loop: vertex 자기 자신만을 순회하는 edge
- Parallel edge: 동일한 vertices pair를 가지는 edges
- <span style="color:rgb(229, 93, 98)">Simple graph</span>: self-loops와 parallel edges가 없는 graph
#### Weighted Grapths
각 edge에 가중치가 있는 그래프
#### Trees
모든 tree는 그래프이다. 
- Connected / Acyclic / Undirected
#### Forests
tree들의 disjoint union

## Graph Representations
#### Adjacency Matrix
각 꼭짓점들이 연결되어 있는지에 대한 2차원 map
space: $V^2$
#### Adjacency List
각 꼭짓점들마다 인접한 Vertex를 딕셔너리에 담아준다. 
space: $O(V+E)$
#### Edge List
모든 Edge들의 pair of vertices를 list에 담아준다. 
space: $O(E)$

## Searching
- BFS(너비 우선 탐색)
	- 최단거리 구해야하는 문제
	- 큐로 구현
- DFS(깊이 우선 탐색)
	- 경로의 특징을 저장해둬야 하는 문제
	- 재귀 함수(일반적), 스택으로 구현

# Minimum Spanning Trees
#### Spanning Tree
→ 최소 연결 부분 그래프이다. 최대한 적은 수의 간선으로 tree를 만든 것이다. 
#### Minimum?
- 간선에 가중치를 고려하여 최소 비용의 Spanning Tree를 선택한다. 
- n개의 정점을 가지는 그래프에 대해 반드시 (n-1)개의 간선만을 사용해야 한다. 
- 사이클 X
## Prim’s Algorithm
시작 노드부터, 그 노드와 연결된 간선 중 <span style="color:rgb(229, 93, 98)">최소 가중치의 간선을 선택</span>하면서 노드를 하나씩 추가해간다. 
따라서, 총 7개의 vertex가 있다면 6개의 edge를 선택하면 알고리즘이 끝난다. 
1. 각 vertex priority 초기화: $O(V)$ → 첫번째 vertex만 0으로, 나머진 무한대
2. `pq.top()`을 통해 priority queue에서 최솟값을 꺼내는 데 $O(lgV)$ 
3. 이를 전체 정점에 대해 처리해야 하므로, $O(VlgV)$
4. 각 정점에 연결된 간선마다 `pq.push()`를 호출: $O(ElgV)$
→ 그러나, Edge의 수가 Vertex의 제곱에 비례하는 밀집 그래프에서는 $O(ElgV)$와 동일하게 처리된다. 
``` cpp 
#include <iostream>
#include <vector>
#include <queue>
#include <climits>

using namespace std;

void Prim_Using_Heap() {
    int n = 4;  // 정점의 개수 (예시로 4개)
    vector<vector<pair<int, int>>> graph(n);  // 각 정점에서 나가는 간선들 (인접 리스트 방식)

    // 예시 그래프의 간선 (정점, 가중치)
    graph[0].push_back({1, 2});
    graph[0].push_back({2, 3});
    graph[1].push_back({0, 2});
    graph[1].push_back({3, 4});
    graph[2].push_back({0, 3});
    graph[2].push_back({3, 5});
    graph[3].push_back({1, 4});
    graph[3].push_back({2, 5});
    
    vector<bool> visited(n, false);  // 각 정점이 MST에 포함되었는지 여부
    vector<int> minDistance(n, INT_MAX);  // 각 정점에 대해 MST와 연결된 최소 거리
    int totalCost = 0;  // MST의 총 가중치

    // 우선순위 큐: (가중치, 정점 번호)
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;

    // 시작 정점 0에서 시작
    minDistance[0] = 0;
    pq.push({0, 0});  // (가중치, 정점)

    while (!pq.empty()) {
        // 가장 작은 가중치를 가진 정점 추출
        int currentCost = pq.top().first;
        int currentVertex = pq.top().second;
        pq.pop();

        // 이미 방문한 정점이면 무시
        if (visited[currentVertex]) continue;

        // MST에 정점 추가
        visited[currentVertex] = true;
        totalCost += currentCost;

        // 현재 정점과 연결된 모든 간선에 대해
        for (auto& edge : graph[currentVertex]) {
            int nextVertex = edge.first;
            int edgeCost = edge.second;

            // MST에 포함되지 않은 정점에 대해 최소 거리 갱신
            if (!visited[nextVertex] && edgeCost < minDistance[nextVertex]) {
                minDistance[nextVertex] = edgeCost;
                pq.push({edgeCost, nextVertex});
            }
        }
    }

    cout << "MST의 총 가중치: " << totalCost << endl;
}

int main() {
    Prim_Using_Heap();
    return 0;
}

```
## Kruskal’s Algorithm
다음과 같은 7개의 노드와 간선들이 있다고 하자. 
```cpp
간선 목록:
(1, 7, 12), (1, 4, 23), (1, 2, 26), (2, 3, 36), (2, 4, 21), (2, 5, 45),
(3, 5, 29), (3, 6, 37), (3, 7, 55), (4, 7, 20), (5, 6, 30)
```
getParent → 각 노드는 자기 자신을 부모로 초기화 한다. 
```cpp
check = [0, 1, 2, 3, 4, 5, 6, 7]
```
1. 간선들을 먼저 가중치 순으로 오름차순 정렬한다. 
2. 부모를 확인해서 서로 같으면 cycle이 있다고 판단하고, 간선을 선택하지 않는다. 
   ⬇️ getParent함수를 통해 check 배열을 계속해서 최신화해준다. ![[IMG-20241208014648.png]]
3. 마지막 간선까지 마무리 하면 minimum spanning tree를 구할 수 있다. 
### rank 배열을 왜 사용하나?
![[IMG-20241208021820.png]]
(0, 3, 5)를 보면, 
0의 parent를 2로 바꾸는게 2의 parent를 0으로 바꾸는거 보다 overhead가 적다. 
2의 parent를 0으로 바꾸면 3의 parent도 2였기 때문에 0으로 바꿔줘야 한다. 
따라서 더 적은쪽의 값을 바꿔준다. 
→ 간선을 정렬하는데에 $O(ElgE)$가 소요된다. cycle 확인하는 과정은 거의 $O(1)$에 완료될 수 있다. 

==Prim(Vertex 중심의 알고리즘)==: $O(ElgV)$
- 간선이 많은 밀집 그래프에서 효율적이야.
- 우선순위 큐를 사용하면 빠르게 동작해.
==Kruskal(Edge 중심의 알고리즘)==: $O(ElgE)$
- 간선의 개수가 적은 희소 그래프에서 잘 동작해.
- 구현이 간단하고 직관적이야.

