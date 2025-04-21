# TGNN Taxanomy
## 1. Snapshot-based Methods (**스냅샷 기반**, STG 기반)
- 그래프가 일정 시간 간격(스냅샷)으로 분리되어 있고, 각 시점별로 정적인 그래프처럼 처리함.
- 시간적 연속성은 여러<span style="background:rgba(205, 244, 105, 0.55)"> 스냅샷 간의 관계를 모델링함으로써 학습</span>됨.
- **하위 분류:**
    - **Model Evolution:**
        - GNN의 파라미터 자체가 시간에 따라 변화(진화)하도록 학습하는 방식. 예: EvolveGCN.
    - **Embedding Evolution:**
        - 각 노드 임베딩을 시간 축을 따라 순차적으로 업데이트(진화)하는 방식. 예: DySAT, VGRNN.

## 2. Event-based Methods (**이벤트 기반**, ETG 기반)
- 그래프 변화가 스냅샷이 아니라 **개별 이벤트(노드/에지의 추가, 삭제 등)** 수준에서 기록되고 모델링됨.
- 연속적이고 비정기적인 시점에서의 변화, 혹은 개별 이벤트가 시간적으로 중요한 경우에 강점.
- **하위 분류:**
    - **Temporal Embedding:**
        - 시간 임베딩(ex: Random Fourier Features 등)과 노드/에지 임베딩을 결합해, 순서 혹은 시간 차를 명시적으로 표현.
        - Attention 또는 RNN 계열로 시간 정보를 처리. 예: TGAT, NAT.
    - **Temporal Neighborhood:**
        - 각 노드의 이웃과 과거 이벤트를 모아두는 Mailbox/Memory 등 특수 구조를 사용해 동적으로 임베딩 업데이트.
        - 예: TGN, APAN, DGNN.

# 5. Temporal Graph에서의 Learning Task
다양한 학습 과제(task)와 학습 설정(setting)을 정리
어떤 종류의 예측이나 분류 문제를 풀 수 있는지, 그 문제들을 어떤 맥락에서 접근할 수 있는지를 체계적으로 설명

## 5.1 Learning Settings (학습 설정)
#### (1) Topological Setting 구조적 설정
1. Transductive Setting 
2. Inductive Setting
#### (2) Temporal Setting; 시간적 설정
1. Past Prediction
	- 누락된 정보 추정이나 과거 데이터 보완을 위해 활용되며, 
	  주변 시계열 패턴을 활용하여 예측한다. 
2. Future Prediction
	- 미래 패턴을 예측하는 것이 목적이다. 
→ 결과적으로 네 가지 조합(Transductive/Inductive × Past/Future)이 나올 수 있다. 

## 5.2 ==Supervised== Learning Tasks
1. Node Classificaiton
2. Edge Classification
3. (sub)Graph Classification
4. Regression
	위 분류 문제를 회귀로 변환
5. Link Prediction
	특정 시점에 **엣지가 생길 확률** 예측
6. Event Time Prediction
	**엣지(이벤트)가 언제 처음 발생**할지 그 시점 예측

## 5.3 Unsupervised Learning Tasks 
1. Clustering
	노드/그래프를 비슷한 특성을 바탕으로 군집화
2. Low-dimensional Embedding (LDE)
	노드/그래프를 **저차원 공간에 임베딩**하여 **시계열 변화 및 패턴을 시각화/분석**


# 6. 다양한 TGNN 방법론들
## 6.1 Snapshot-based Models
“Snapshot”: 일정한 시간 간격으로 전체 그래프(노드/엣지/속성 등)의 스냅샷을 연속적으로 나열한다. 
- 대표적 예시: 하루/1시간마다 찍힌 전체 네트워크 상태
### 6.1.1 Model Evolution
- GNN 모델 파라미터 자체를 시간에 따라 업데이트
- 대표 모델:
	- [EvolveGCN]: GCN의 파라미터를 RNN(LSTM, GRU 등)으로 시간에 따라 진화시킨다. ![[스크린샷 2025-04-21 오후 4.44.17.png]]
