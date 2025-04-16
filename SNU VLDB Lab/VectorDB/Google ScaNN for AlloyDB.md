벡터 검색을 단순화하고 성능을 대폭 향상시키는 동시에 데이터베이스와의 통합을 통해 기존 워크플로를 개선하는 데 중점을 둔다.  
- **HNSW 인덱스**는 대규모 데이터셋, 빠른 인덱스 생성, 빠른 검색 속도 등에서 한계가 있었다. 
- Google은 이를 보완하기 위해 **ScaNN(Scalable Nearest Neighbors)** 인덱스를 도입
- ScaNN과 pgvector HNSW를 함께 제공하여 PostgreSQL 사용자에게 다양한 선택지를 제공한다. 
#### 이 글의 목표
- ScaNN의 트리 양자화 기반 인덱스 기본 개념 설명.
- ScaNN이 AlloyDB의 벡터 인덱싱 및 검색을 HNSW와 비교하여 어떻게 개선하는지 시연.

## Part 1. The basics of nearest neighbor search
#### KNN?
데이터베이스에 저장된 벡터와 쿼리 벡터 간의 거리를 계산하여 가장 짧은 거리의 상위 k개의 데이터베이스 벡터를 찾는 문제이다. 
→ 유클리드 거리(Euclidean Distance), 코사인 거리(Cosine Distance), 내적(Inner Product) 등
- **문제점**: 브루트 포스(brute force) 방식으로 모든 데이터 벡터와 쿼리 벡터의 거리를 계산하면 시간이 오래 걸리고 비용이 비쌉니다. 특히 데이터가 수백만~수십억 개에 이를 경우, 비효율적이다. 

#### Approximate Nearest Neighbor (ANN) vector indices
→ 대략적으로 가까운 이웃들을 찾는 방식을 사용: 정확도를 약간 희생
- **Partitioning**: 데이터 공간을 여러 부분으로 분할하여 탐색 범위를 줄인다. 
  예. KD-트리, Ball-트리.
- **Graph-based Approaches**: 벡터 간의 유사성을 기준으로 그래프를 생성하고 탐색. 
  예: HNSW (Hierarchical Navigable Small Worlds).
- **Quantization**: 데이터 포인트를 여러 클러스터로 양자화하여 계산량을 줄임. 
  예: ScaNN에서 사용하는 트리 양자화(Tree Quantization).

