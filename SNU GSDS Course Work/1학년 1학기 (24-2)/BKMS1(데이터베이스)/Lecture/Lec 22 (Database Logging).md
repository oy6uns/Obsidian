나중에 failure 시에 Recovery를 하기 위해 평상시에 logging을 해놓는 작업

##### Recover From Failure
1. Transaction이 끝나고 난 후 Failure
   Commit된 Result가 잘 저장이 되있는지 `Durability`가 중요
2. Transaction이 끝나기 전에 Failure
   아예 일어나지 않은 것처럼 만들어주는 `Atomicity`가 중요

따라서, Logging은 `Atomic Durability`가 중요한 Properties이다. 

## p. 9
**Buffer Pool** 에 commit된 값이 **Storage**에 반영이 안되어 있다. 
그러면, ==Durability를 위한 Redo== 를 해주어야 한다. 

**Buffer Pool** 에 commit되지 않은 값이 **Storage**에 반영이 되어 있다. 
==Atomicity를 위한 Undo==를 한다. 

## p. 13
Failures Classification이 필요하다. 

## p. 19
buffer management가 잘 설계되어 있으면
recovery 시에 추가적인 오버헤드가 작게 든다. 
### #1. transaction 중간에 Failure 발생
만약, failure가 중간에 발생하더라도, Storage로 변경된 데이터가 흘려 들어가지 않으면
==Undo==를 따로 시행하지 않아도 된다. 

→ **no steal** policy를 **buffer manager**에게 잘 부여하면, Recovery를 추가적으로 시행하지 않아도 된다. 
### #2. transaction 이후에 Failure 발생
buffer manager에 변경된 데이터를 commit 하는 시점에 Storage에도 해당 commit 내용을 같이 추가해주면 된다. 

→ **Commit** 시에 **Force** policy 를 **buffer manager**에게 잘 부여하면, Recovery를 추가적으로 시행하지 않아도 된다. 
## 그러면 무조건 **no steal, Force**를 적용하면 좋은거 아니야?
- **long running transactions** 에서는 **no steal이 금지**된다. 
	왜냐하면 Storage로 뺏기지 않기 위해 Buffer manager에서 많은 데이터가 병목되게 된다. 
- **Force를 쓰면 너무 느려진다**. 

따라서, Force를 하지 않고 Buffer Pool 자체에서 Log를 작성하고 이를 나중에 한꺼번에 Flush 시켜주는 아이디어를 생각해냈다. 

어? 그런데 결국엔 동일한거 아니야? 
Force를 하면 너무 느려지고, 
Logging을 하면 나중에 Data Recovery 시에 Overhead가 커지는거 아닌가??

맞다. 
결국에 force, no steal을 하면 recovery가 할 게 없지만 느려지고, 
no force, steal을 하면 recovery가 할 게 많아진다. 

|          | no steal | steal      |
| -------- | -------- | ---------- |
| force    | X        | Undo       |
| no force | Redo     | Redo, Undo |

==WAL (Write Ahead Logging)== 다시 공부해보기
## p. 41 Shadow Paging
- commit 되기 이전에 faliure가 나면, Shadow page를 Buffer Pool에서 지워주면 된다. 
- commit 된 후에 failure가 나면, Disk의 Shadow page를 지워주기만 하면 된다. 
#### 단점
Cost가 너무 크다. 
- Entire Page Table을 Copying하는 것이기에
- Commit overhead 또한 크다. 
## log의 시점
1. transaction을 시행하고, force를 해야하는데 안했다.
   이때, redo log를 flush 해야 한다. 
2. uncommited transaction을 시행했는데, 누가 flush 했다. 
   이때, undo log를 flush 해야 한다. 
   
데이터를 필히 write를 해줘야하는 시점, 
Atomicity와 Durability가 깨지기 직전 시점에 log를 flush해야 한다.

→ Write 전에 Logging을 해주어야 한다고 해서 ==‘Write Ahead Logging, WAL==’이 나오게 되었다. 
## Check Point
most up to date 된 log를 truncate 시키는 시점을 말한다. 
이 이후에 시행하는 Recovery의 시간을 단축시키는 데에 중요한 포인트가 있다. 

check point의 주기를 어느정도로 설정할 것이냐
→ database의 failure가 나는 빈도에 따라 다르게 설정하면 된다. 

==Check Point 개념 다시 공부해보기==
## p. 57 Write-Ahead Log
log를 언제 해야되는지가 
write-ahead logging rule

A와 D가 깨지기 전 순간에 flush를 잘 해줘야 된다.

## p. 69 WAL - Group Commit
계속 변경사항이 생길 때마다 flush를 시키는게 비효율적이다. 
따라서, 어느정도의 transaction이 쌓인 순간에 flush를 시행하게끔

- 그러나, Schedule에서 Commit을 했더라도, WAL Buffers에서는 Group으로 묶어서 Commit을 할려고 하기 때문에 Delay는 문제가 있기는 하다. 
- WAL Buffers에서 새로운 transaction에 대한 대기를 오래 하고 있는 상황이라면, 
  특정 time이 지난다음에 그냥 flush시키는 경우도 있다. 
## p. 80 Buffer Pool Policies
결국에, 해당 DBMS가 중요하게 생각하는 Key마다
No-Steal, Steal, No-Force, Force의 조건을 달리하면된다. 

## p. 82 Physical vs. Logical Logging
→ p. 85에 한번에 정리되어 있음
![[IMG-20250113140016.png]]

## p. 86. Checkpoints














