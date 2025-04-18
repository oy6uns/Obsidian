# Lec 10. n-step TD prediction
## [[Lec 10. n-step Bootstrapping#n-step TD]]
![[IMG-20250417180406.png]]

## [[Lec 10. n-step Bootstrapping#n-step Sarsa]]
![[IMG-20250417181846.png]]

## [[Lec 10. n-step Bootstrapping#n-step off-policy learning by Imortance Sampling]]
![[IMG-20250417184000.png]]


# Lec 11. Plan & n Learn with Tabular Methods

## [[Lec 11. Plan & Learn with Tabular Methods#그래서 나온 해결책 📌 Dyna-Q Real Experience vs Simulated Experience]]
![[IMG-20250417200642.png]]
- 초기화:  
    `Q(s, a)`와 `Model(s, a)` 초기화 (모든 상태-행동 쌍에 대해)
- 반복 루프:
    - (a) 현재 상태 `S`를 선택
    - (b) `ε`-greedy 정책으로 행동 `A` 선택
    - (c) 환경에서 `R, S'` 관측 (보상, 다음 상태)
    - (d) **Q-learning 업데이트**
        `Q(S, A) ← Q(S, A) + α [R + γ maxₐ' Q(S', a') - Q(S, A)]`
    - (e) **모델 업데이트**  
        `Model(S, A) ← R, S'`
    - (f) **n번 반복해서 모델 기반 Planning 수행**
        - 이전에 본 상태 `S`와 그에 대한 행동 `A`를 무작위로 선택
        - 모델에서 예측된 `R, S'`를 가져옴
        - Q-learning 업데이트 반복
