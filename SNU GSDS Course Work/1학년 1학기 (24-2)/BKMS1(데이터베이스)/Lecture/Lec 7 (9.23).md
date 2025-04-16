# Buffer Management
아래에 있는 데이터를 위로 올리는 데에 너무 오랜 시간이 걸린다. 
따라서, buffer manager에 자주 쓰는 데이터를 올려놓은 뒤에 빠르게 사용할 수 있게끔 만들어주는 노력이 필요하다. 
![[Img/SNU GSDS/BKMS1(데이터베이스)/Lecture/Lec 7 (9.23)/IMG-20241022194159.png]]

## Buffer Pool Organization
buffer pool 의 frame도 paginated 되어 있다. 
1. 각 frame에 On-Disk File의 포인터를 넘겨준다. 
2. Buffer Pool에 어떤 page가 담겨있는지를 알려주는 Page Table이 필요하다. 그리고, 변경을 관리할 수 있게 Page Table에서 자체적으로 Lock을 관리한다. ![[Img/SNU GSDS/BKMS1(데이터베이스)/Lecture/Lec 7 (9.23)/IMG-20241022194159-1.png]]
### Locks vs. Latches
Latch는 주로 Mutex로 구현된다. 

## Buffer Pool Optimizations
### Multiple Buffer Pools
보통 mutax 형태의 latch로 concurrency Control을 하기 때문에, 여러개의 Buffer Pool을 설정함으로써 `Load Balancing`이 가능하게 해준다. 
`fragmentation`: Load Balancing이 안되게 되면, 용량이 큰데 실제로는 resource를 아주 조금만 사용하는 문제가 발생하는 것을 얘기한다. 
### Pre-Fetching
`Spatial한 특성이 있음` → 미리 Fetching해와. 

### Scan Sharing
동일한 Table에 Scan을 하려고 하는 여러 쿼리가 들어오면, Buffer Pool에 걸리는 I/O Activity가 급증하게 된다. 
	ex) 방금 전에 읽은 Table을 또 읽을려고 했는데, 바로 다른 Process가 채가는 경우
==Piggy back.== → 다시 공부해보기 

### Buffer Pool Bypass
가끔은 없는걸로 치는게 더 나을 때도 존재한다. 

> [!NOTE]
> `Load Balancing`
> load를 줄이기 위한 randomzation
> → 강조하시는 개념인듯!!!!

## Pre-fetching

## LRU Replacement Policy
`1. Dirty?`
`2. Pin Count?`
`3. Last Used?`
→ 셋 중에선 `Last Used`를 가장 중요한 Attribute로 보자
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Lec 7 (9.23)/IMG-20250304110005-1.png]]
==왜 안되는지 다시 공부해보기==
Approximate가 필요, 너무 많은 용량 차지하기 때문에 
## LRU-C
> LRU를 Approximate했더니 잘 돌아가네?
> triumph of Approximation

한 주기 동안 봤을 때, 한번도 Access안된 애들은 빼는 방법 사용
Clock Algorithm 사용!
계속 row를 내려가면서 `Dirty 여부를 체크`한다. 

# Recap on Buffer Pool Manager
database buffer를 설계할 때, 더 자주 사용하는 데이터를 올리기 위해 buffer pool을 사용
- Recency(LRU)
- Capathy()
- Frequency(LFU)
- 
