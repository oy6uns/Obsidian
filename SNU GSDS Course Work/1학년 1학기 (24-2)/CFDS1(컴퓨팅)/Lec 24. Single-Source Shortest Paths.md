## SSSP

- **목적**: 그래프에서 특정 시작 노드(소스)에서 다른 모든 노드까지의 **최단 경로**를 찾는 것.
- vs. Minimum Spanning Tree(MST)
  : 그래프의 모든 노드를 연결하는 간선의 **최소 비용 트리**를 찾는 것.
## SSSP on Unweighted Graphs

- 출발 노드 s에서 시작하며, 거리를 0으로 설정
- 다른 모든 노드의 거리는 무한대(♾️)로 초기화
#### BFS 탐색
- s에서 시작해 인접 노드를 하나씩 탐색
- 탐색할 때마다, 해당 노드의 거리를 현재 노드의 거리 +1로 업데이트

## SSSP on Weighted Graphs
### Dijkstra Algorithm
음수일 때는 성립을 안함

→ 벨만포드부터 계속 공부

![[IMG-20241212124841.png]]
