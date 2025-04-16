#buffer_manager #dirty_page

Memory의 BufferPool에서 Storage로부터 page를 읽어온다. page 내용을 변경했으면, 해당 페이지를 `dirty page`라고 부르고, `dirty page`를 다시 Storage로 보내주는 과정을 `Dirty Page Flush`라고 한다. 

가진 Storage의 성능이 좋을 경우, `Buffer Manager`의 `dirty page`를 더 자주 Storage로 `Flush` 시킬 수 있다. 

그러나, Memory의 성능이 Storage의 성능보다 안좋기 때문에 `Write`가 자주 발생할수록 `DBMS`의 성능에 영향을 줄 수 밖에 없다. 
`dirty page`들을 미리 `Flush`시켜준다면 `page`교체 과정에서 DBMS의 쓰기를 방지할 수 있어서 성능을 향상 시킬 수 있다. 
### Config
![[IMG-20240930123358.png]]
Postgres에서는 bgwriter(backgroundwriter)란 놈이 주기적으로 buffer pool을 검사하면서, dirty page들을 flush시켜준다. 

- 검사할 시간(주기): `{shell} bgwriter_delay`
- 한 번에 검사할 최대 페이지 수: `lru_maxpages`
- 한번에 flush할 dirty page들의 목표 개수: `lru_multiplier`
- 운영체제에서 storage로 page를 flush할 기준: `bgwriter_flush_after`
	백그라운드 라이터가 얼마나 많은 데이터를 쓴 후에 운영 체제에 디스크로 플러시(flush)하도록 요청할지를 제어합니다.
	***성능 영향***:
	- 이 값을 낮게 설정하면 더 자주 플러시가 발생하여 I/O 부하가 증가할 수 있지만, 시스템 크래시 시 데이터 손실 위험을 줄일 수 있습니다.
	- 값을 높게 설정하면 플러시 빈도가 줄어들어 I/O 성능이 향상될 수 있지만, 갑작스러운 시스템 중단 시 더 많은 데이터가 손실될 위험이 있습니다.

`{shell} SHOW ~~;`: 설정된 변수 값 확인
![[IMG-20240930123403.png]]
## PostgreSQL Shared Buffer
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lab/Lab 3/IMG-20250304110005-1.png]]

1. 사용자가 SQL query를 Postgres에 전달하게 되면, Postgres는 해당 query가 필요로 하는 데이터를 Shared Buffer에 올려주어야 한다. 
2. 따라서, 해당 데이터가 어떤 테이블의 어떤 페이지인지에 대한 정보를 담고 있는 Tag를 생성해준 뒤에 Buffer Table에 Tag를 넣어준다. 
3. 현재 사용되지 않고 있는 Buffer Descriptor를 Free List에서 할당 받은 뒤에 위의 해당 Tag와 이어줌으로써 나중에 Tag를 사용해서 다시 Page를 찾아갈 수 있게 해준다. 
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lab/Lab 3/IMG-20250304110005-2.png]]
1. Buffer Descriptor가 할당된 뒤에는 해당 Descriptor와 대응되는 Buffer Page에 실제 Data Page를 Storage로 부터 읽어와준다. 
2. Buffer Page: 실제 데이터의 읽기 및 쓰기
    Buffer Descriptor: 해당 buffer page가 참조가 되고 있는지, 최근에 몇 번 사용되었는지, dirty 한지 
    → Postgres에서는 해당 Buffer Page에서 데이터를 읽고 쓰기 위해서 Buffer Descriptor를 참조하게 될 경우 `Reference Count`와 `Usage Count`를 `1`씩 증가시키며, 해당 Page의 사용이 완료된 뒤에는 `Reference Count`만 `1` 감소시킨다. 
    Buffer Page에 데이터가 변경이 되었을 경우 `dirty`가 표시되며, Flush될 때 `dirty` 마크가 같이 사라진다. 
→ 위와 같은 과정을 통해 Postgres는 새로운 페이지를 Buffer Pool로 불러오게 된다. 
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lab/Lab 3/IMG-20250304110007.png]]
그러나, 계속해서 새로운 데이터 페이지를 불러오게 된다면 한정된 개수의 Buffer Descriptor는 언젠가 모두 사용되게 된다. 
이때는 새로운 Data Page에 대해서 Buffer Descriptor를 할당 받을 수 없기 때문에 기존에 Buffer Pool에 있는 Data Page들 중에서 하나를 골라 evict 시켜주어야 한다. 
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/BKMS1(데이터베이스)/Lab/Lab 3/IMG-20250313142327-1.png]]
Postgres는 Buffer Pool에 있는 모든 page들을 차례대로 확인한다.
1. 만약, 해당 페이지가 참조중(pinning)일 경우(`{cpp} Buffer Descriptor’s reference count != 0`) Buffer pool에서 evict할 수 없다. 따라서, 다음 페이지를 순차적으로 검사한다.
2. 만약, pinning되어 있지 않고, 현재 사용중이지 않더라도 최근에 사용된 경우(`{cpp} Buffer Descriptor usage count != 0`) 해당 Buffer Descriptor의 usage count를 `1` 낮추고 건너뛴다. 
   → 이 과정을 reference_count & usage_count가 0인 buffer page를 발견할 때까지 반복하며 발견 시에 해당 buffer page를 eviction의 대상이 되어 교체된다. 


## pg_buffercache
extension 적용
`{shell} CREATE EXTENSION pg_buffercache`
extension 확인
`\dx`

/scripts/sysbench.sh의 파일 수정 후에 
`{shell} ./scripts/sysbench.sh --prepare`
실행 시에 데이터 베이스에 데이터 새롭게 추가
![[IMG-20240930123428.png]]
→ 13521개의 buffer page를 가지는 데이터가 생성됨을 확인 가능

### Total number of pages in use 
> 현재 shared_buffer내에 할당된 buffer_page들의 총 개수를 알 수 있다. 





