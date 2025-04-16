[WAL](../WAL.md)
- - - 
## Key word
- WAL principle(중요)
![[스크린샷 2024-12-04 오후 1.26.46.png]]
page를 변경할 때, 가장 최근에 page가 발급받은 LSN을 pageLSN에 저장한다. 
flushed LSN과 pageLSN을 비교해서, Atomicity가 깨지지 않게끔 한다. 

## Normal Execution
Check point 빈도를 높이면 성능은 낮아지지만, 
recovery overhead는 줄어든다. 

## p. 29 Transaction abort 
왜 recovery의 길이가 점점 짧아지는지 공부

# Recovery Algorithm
uncommited result가 redo로 들어갔을 수도 있다. 
## p. 38
맨 오른쪽의 log file: append only
commit 시에 log를 flush해야한다. 
→ log file에 1~9가 다 밀려나게 된다. 

ARIES: NextUndoSeqNo를 사용해서, Compensate를 통해 undo를 잘 할 수 있게 한다. 

`flush`: 지금까지의 LogSeqNo들을 모두 Disk로 넘겨준다. 
compenste 이전껄로
redo, consider-redo: log seq num 확인 안해도 됨
