# Access Methods
## Design Decisions
`Data Organization`: 내가 원하는 key가 어딨는지를 빨리 알려줄 수 있는 `자료구조` 를 사용해야함. 
`Concurrency`: 여러 thread가 같은 데이터에 access하려고 할 때 안전하게 handling 할 수 있어야 한다.
## Hash Table
충돌 handling을 어떻게 처리하느냐에 따라서 hash의 타입이 달라진다. 
`Static hash`: join 알고리즘 처리할 때, 그 수를 알기에 inmemory에서 처리할 수 있다. 
`Dynamic hash`: 통상적으로 사용되는 hash table, 수를 모를 때 사용할 수 있다. 
### Design Decision
1. Hash Function
2. `Hashing Scheme`: hash collision을 어떻게 해결하냐에 따라서 design principle이 달라진다. 

# Static Hash
## Static Hashing Schemes
> [!NOTE]
> collision이 났을 때 어떻게 대응하는지를 각각 아는 것이 중요!!
### #1. Linear Probe Hashing
계속 순차적으로 데이터를 저장하려고 하다보니, 붙어 있는 데이터들끼리 Cluster가 형성된다. 
따라서, Free Slot을 찾기 위해 처음부터 마지막 기록 데이터까지를 linear하게 Scan해야한다. 
![[Img/SNU GSDS/BKMS1(데이터베이스)/Lecture/Lec 8 (9.25)/IMG-20241022194159.png]]
`Spatial Locality`의 가정에 의해서 요즘에는 CPU에서 원하는 데이터의 근처 데이터들까지 L2-Cache에 저장해둔다. 그래서 요즘 나오는 CPU에 적당한 데이터를 저장하는 경우에는 `Linear Probe Hashing`이 나쁘지 않은 성능을 보여준다.
- Randomly Search보다 Linear Search가 오히려 효율적일 수 있다. 그러나, 너무 데이터가 많아지게 되면 느려지게 된다.
- Cluster가 생기지 않게끔 중간중간에 random하게 delete를 해주면 Linear Probe Hashing의 Cluster가 생기는 문제를 해결해줄 수 있다(Sigmod’23)
### #2. Robin Hood Hashing
너무 가까이 몰려있는 애들을 뒤로 밀고, 
너무 멀리 있는 애들은 가까이 가져온다. 

Cluster를 없애자는 목적이 아니라, 거리의 inbalance가 있으니깐, 원래 찾고자 하는 위치 근처에 있어야지 Search를 할 때 효과가 더 클 것이다. 
따라서, 내부에서 재배치를 해서 왠만하면 근처에서 찾을 수 있게 해주는 알고리즘이다. 
### #3. Cuckoo Hashing
> libcuckoo 사이트에 가서 코드를 인용해서 사용해도 된다. 
> → time complexity도 좋고, industry에서 많이 사용한다.

여러개의 Table을 search하고, 둘 다 차있다면 그냥 그 자리의 데이터를 빼고 새 데이터가 들어간다. 
빠져나온 데이터는 다른 빈 자리로 알아서 들어간다. 
→ 자동으로 Shuffling이 된다. 

Hash Table이 두 개라고 가정하면, 새로운 데이터들마다 Chance가 2개씩 존재하므로 데이터들끼리 계속 똑같은 자리에 들어갈려고 하는 Cycle이 발생활 확률이 매우 낮다. 
### #4. Perfect Hashing
Collision이 날 확률: nC2 x 1/m(m: table size)
분자가 n^2 이므로, m을 `n의 square(제곱)`만큼의 값으로 만들어야지만Linear Time Complexity를 만들 수 있다. 

if m=n, Collision이 n개 정도 나온다. 
→ Collision이 발생한 곳이 Spreading되기에, 어떤 곳은 생기고 어떤 곳은 안생긴다. 

Collsion이 생긴 entry의 크기만큼만 제곱 배를 해서, 전체 크기를 줄이자. 그래서, 결과적으로 m(Collision이 생긴 entry)의 Square만큼의 크기가 n보다 작다는 것을 보이면, n에 대해 Linear Time Complexity를 가진다는 것을 증명할 수 있다. 

E([simga(n_i C 2)]) + E(sigma(n_i))
==O(n)되는 이유 다시 공부해보기==

이론상으론 완벽하지만, 안쓰이는 이유
: 모든 data를 다 알고 있어야만 Collision Table을 만들 수 있기 때문에 실제에서는 비효율적이다. 

> [!NOTE]
> 1. Reshuffling이 중요하구나.
> 2. Second Chance를 주는 철학이 중요하구나. 













