# Week 5-M: Hash Tables - 2
## Dynamic Hashing Schemes
Static: 중간에 변할 일이 없음
Dynamic: size를 모르고, hash table을 build하고 management해야됨. 

→ lookup O(1) - linear time이 소요되는 것이 가장 이상적인 상황이라고 했다. 

inmemory에서 cycle이 있을 때, rehash를 해야하는 시점이라고 했다. 
→ 이 경우 inmemory에서 모든 hash table, function을 바꾼다. 
database쪽은 disk에 들어가있다. 따라서, 이를 모두 들어내서 바꾸려면 너무 많은 resource가 들어간다. 따라서, 그냥 모든 hash function을 뒤집어 엎으면 안된다!

## Chained Hashing
> a linked list of buckets for each slot in the hash table

bucket 자체가 하나의 chained array로써 동작한다. 
![[Img/SNU GSDS/BKMS1(데이터베이스)/Lecture/Lec 9 (9.30)/IMG-20241022194159.png]]
처음에는 구현이 쉽고 간단하지만, 데이터가 많아질수록 length of the chain 이 input key의 개수에 비례해서 계속 증가하게 된다. 따라서 불편해지게 된다. 

==chained hashing 다시 공부해보기==

`resize를 좀 해서 정의역으로 들어오는 공간에 비해 공역이 작으니깐, 그 공역 공간을 넓히겠다!`
## Extendible Hashing
> 공역의 크기를 doubling 시켜준다. 
> 몰려있는 놈들만 흐뜨려놓는 방법을 고안해야한다. 
> → 다른 애들은 그대로 놔두고, 몰려든 데이터들만 다른 곳으로 분산시켜주자!
> → `distruction은 minimize`하면서 `load balancing` 시키는 것이 핵심!

![[Img/SNU GSDS/BKMS1(데이터베이스)/Lecture/Lec 9 (9.30)/IMG-20241022194200.png]]
1. global bit를 2로 설정하고, 데이터를 넣다가, local bucket에서 data overflow가 발생
2. global bit를 3으로 올리고, local bucket을 doubling할 준비를 한다. 
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Lec 9 (9.30)/IMG-20250304110005-1.png]]
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Lec 9 (9.30)/IMG-20250304110005-2.png]]
3. local bucket의 bit를 global bit와 맞추고, 테이블을 추가한다. 

`문제점`: local bucket이 overflow가 나면, global hash table이 2배가 된다
처음에는 큰 문제 같아 보이지 않지만, 나중에 가면 global hash table의 크기가 exponentially 하게 커진다. 

## Linear Hashing
> global table size doubling을 linear하게 하겠다. 순차적으로 크기를 1씩 증가 시킨다. 

`Amortized Analysis` : 한번에 내가 지불해야하는 cost를 분할해서 쪼개겠다. 
1. Split이 되지 않으면 기존의 old hashfunction만 검사하면 된다. 
2. local bucket에서 overflow가 나면, global hash table은 어디서 overflow가 났는지는 모르지만, 가장 처음 할당되어있는 global hash table’s pointer에 해당하는 local bucket을 하나 더 추가한다. (새로운 hash function을 추가해서)![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Lec 9 (9.30)/IMG-20250304110007.png]]
3. 그러나, 정말 가려운 위치를 긁지 못하고, 잘못하면 Split Pointer가 한바퀴를 다 돌아야지만, 원하는 위치의 local bucket을 추가할 수 있게 된다. 

> [!Extendible vs. Linear]
> Extendible은 너무 데이터에 민감해서 문제고, Linear는 너무 데이터에 둔감해서 문제다. 
> 그러나 현재 Database는 search를 잘해야하기 때문에, extendible을 대부분 채택하고 있다. 

==Delete 시켜줄 때는 따로 신경 안써줘도 될까? Search랑 똑같은 걸까?==


