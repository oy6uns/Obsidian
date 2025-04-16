## OverView: Representation
1. Record를 어떻게 표현할까?
2. 파일의 페이지에 Record를 어떻게 배치할까?
![[IMG-20240930123247.png]]
> [!NOTE]
> Storage의 핵심
> Physical Independence는 지켜야할 중요한 성질
> `Indirection` → 가장 중요!!
> 
> → Pagination, Slotted Page 등등이 다 그런 이유에서 나온 것들

## Disk Based Architecture
주된 저장장소가 Non-volatile storage라고 가정하고 저장된다. 

`저장 시에 고려할 것`
1. 어떻게 잘 찾지에 대한 문제도 같이 따라온다. 
2. 누구를 먼저 불러와야 되지?

`Volatile`
	CPU Register: 비싼만큼 빠르다
	- Random Access
	- Byte-Addressable
`Non-Volatile`
	SSD, HDD, Network Storage: 싼만큼 느리다. 
	- Sequential Access
	- Block-Addressable

Non-Volatile DRAM도 고려는 되고 있지만, 가격과 성능의 절충안을 아직 잘 찾지 못했다. 

## Access Times
![[Img/SNU GSDS/BKMS1(데이터베이스)/Lecture/Lec 4 (9.11)/IMG-20241022194159.png]]
## SEQUENTIAL VS. RANDOM ACCESS
똑같은 양을 조금씩 하나하나 읽어오는 것 보다, 여러개를 한꺼번에 불러오는 것이 효과적이다. 
==둘의 차이 다시 공부해보기==
## System Design Goals
- Allow the DBMS to manage databases that exceed the amount of memory available.

- Reading/writing to disk is expensive, so it must be managed carefully to avoid large stalls and performance degradation.

- Random access on disk is usually much slower than sequential access, so the DBMS will want to maximize sequential access.

## Disk-Oriented DBMS
어떤 데이터에 접근은 많이 하는지를 파악하고, 다음에 동일한 데이터에 다시 접근하려고 할 때, 어떻게 하면 빠르게 처리할 수 있을지에 대해서 고민한다. (Buffer Manager)

## 왜 그런데 이런 일을 OS가 아니라 DB가 해야돼요?
OS는 데이터의 sementic한 정보를 모른다. 
General Purpose로 설계되어 있기 때문에, OS에게 일을 맡기는 것이 비효율적이고, 몇몇 문제가 발생한다. 
1. 오래된 데이터를 Dirty pages로 인식하고 버려버릴 수 있다. 
2. ==나머지 경우의 수 추가적으로 공부하기 ✅ ==

> [!File Storage]
> ==Cloud로 변하면서 raw device를 컨트롤 하는 입장으로 바뀌고 있다.(나중에 다시 공부하기)==

# Database Pages
> Physical Independence를 만족시키는 첫번째 개념
> `page-oriented storage scheme`

데이터를 저장한 뒤에, 그 위치를 알아야지 나중에 retrieval할 수 있다. 그러나 그 위치 정보가 바뀌면 원래 알고 있던 위치 정보가 쓸모 없게 된다. 그대로 그 상황을 놔두면 Physical Independence가 violate된다. 

Storgae의 정보가 옮겨다니더라도 Storage manager가 알고 있는 정보가 바뀌어서는 안된다 → Physical Independence
그니깐 추상화를 하자!!
단위를? `page` 로 

책임은 항상 Disk Space Manager가 진다. 
→ 윗단에서 page를 찾을 때 우선적으로 `page ID`부터 찾는다. 
   그러면 `Disk Space Manager`가 해당 `page ID`의 정보를 윗단으로 보내준다. 
위쪽에서는 physical Disk의 데이터가 어디에 어떻게 위치해 있는지 아무런 관심이 없다. 그저 해당 page ID에 대한 정보를 똑 보내주기만 하면 되는거다. 해당 page ID에 대한 정보를 찾고, `indirection mapping table을 참고해서 resolving`하는 것은 전적으로 `Disk Space Manager`가 책임져야 한다. 
###### 데이터의 유효성 검증을 위해 나중에 data 를 복제하여 저장하는 방식도 배운다. 

## Page Storage Architecture
disk의 paging된 파일들을 관리하기 위한 방법들은 다음과 같이 있다. 
![[Img/SNU GSDS/BKMS1(데이터베이스)/Lecture/Lec 4 (9.11)/IMG-20241022194159-1.png]]
`tuple`들로 구성된 `page들의 unordered collection`을 `heap file`이라 부른다. 

![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Lec 4 (9.11)/IMG-20250304110005-1.png]]
`Disk Space Manager`가 `page Id`를 입력 받았을 때, offset을 계산해서 몇 바이트의 정보를 위로 보내줄지를 위와 같이 결정하게 된다. 
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Lec 4 (9.11)/IMG-20250304110005-2.png]]
페이지를 찾아가는 과정에서 
Linked List는 페이지 간의 이동에 I/O가 발생하기 때문에
잘 쓰이지 않는다.

`따라서, heap file은 보통 directory 구조로 쓰인다.` 
> [!NOTE]
> 다시 한번 말하지만, Disk Space Manager에서 이루어지는 모든 일들은 Page ID 하나로만 이루어진다. 

그러면, `page ID` 로 찾아온 데이터는 어떤걸까? `Record` 의`Column Data` 가 데이터의 format일 것이다. 

그러면, `Column Data가 저장되어 있는 Page의 각 Tuple` 들을 어떤식으로 `Page에 저장`시켜야될까?
## Slotted Pages

![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lecture/Lec 4 (9.11)/IMG-20250304110007.png]]
Slot Array에는 어떤 식으로 저장될까?
첫번째로 들어온 Tuple의 `시작 위치`와, `Length` 2가지 정보가 저장된다. 
튜플은 `위에서부터 저장`되게끔하고, Slot Array는 `뒤에서부터 채워`지게끔해서 최대한 `Page의 공간을 효율적으로 사용`할려고 한다. 

### 자 그럼 결론적으로, 
In page에서 움직이는 경우, Slot Array의 # 이 처리한다. 
page 단위에서 움직이는 경우, Page ID가 처리한다. 

 `Page ID`, `Slot Array의 #` 의 2가지 정보를 통해 Memory와 Storage 간의 `Physical Independece` 를 유지할 수 있다!!!

==NULL BIT Compression 다시 공부해보기==

