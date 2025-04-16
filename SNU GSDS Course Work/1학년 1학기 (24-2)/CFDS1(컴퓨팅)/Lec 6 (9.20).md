`{cpp icon} auto 키워드: 자동으로 적절한 자료형으로 변수를 선언 가능하게 해준다. `

각 Container의 iteration도 잘 봐야할듯

## vector
![[IMG-20240920141839.png]]
 - accumulate: vector 원소들의 sum
	4번째 인자에 원하는 함수를 넣어주면, 그 방법으로 값을 더해준다. 
## list
![[IMG-20240920165248.png]]
- Insertion, Deletion ![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/Lec 6 (9.20)/IMG-20250304110005.png]]![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 6 (9.20)/IMG-20250313142327-1.png]]
- 앞의 5개의 원소들은 Heap에 dynamic하게 할당이 된거고, 마지막에 나온 값(`List`의 `Sentinel Node`)은 Stack에 할당되어 있는 값이다. 
  → 따라서, List는 doubly linked list로 계속 순환되는 형태로 존재한다는 것을 알 수 있다.  
## map
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 6 (9.20)/IMG-20250313142327-2.png]]
map의 각 node는 key와 value를 가지는 pair라는 object로 구성되어 있다. 
## set
key가 곧 value인 형태
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 6 (9.20)/IMG-20250313142327-3.png]]
→ 함수는 깊게 공부할필요는 없고, 간단히 쓰임만 알면 될듯!


![[Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 6 (9.20)/IMG-20250416150918-2.png]]