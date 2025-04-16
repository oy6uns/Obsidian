## Point Operations
pointer형의 크기 만큼 address를 더준다. 
![[IMG-20240926140738.png]]
### Dereferencing:
포인터의 타입이 중요한 이유는 메모리에서 **몇 바이트를 읽을지**와 **그 비트 패턴을 어떻게 해석할지**를 결정하기 때문입니다.

- `*intPtr`: 정수 포인터로, 일반적으로 4바이트를 읽고 그 내용을 정수로 해석합니다.
- `*stringPtr`: 문자열 포인터로, 해당 위치를 문자열 객체의 시작으로 간주하고 객체와 상호작용(예: 속성에 접근하거나 메소드를 호출)할 수 있습니다.
## Static Array
한번 선언한 Static Array는 포인터 변수가 아니기 때문에 다른 변수를 가리키게끔 할 수 없다. 
## Smart Pointers
`발생하는 문제들`
1. free를 안시켜줌으로써 발생하는 memory leaks
	쓰레기 데이터로 가득 차서 더 이상 할당이 안되는 상황이 발생
2. free가 되지 않은 쓰레기 포인터들을 `Dangling pointers`라고 한다. 
### Unique Ptr
memory leak을 어느정도 해결해줄 수 있다. 
==따로 공부해보기==
### Shared Ptr
`Unique Ptr`과의 큰 차이
- ptr끼리 메모리를 공유할 수 있다. 
- 마지막 shared ptr이 scope에서 사라지는 순간에 memory가 free가 된다. 
- 같은 메모리 공간을 가리키고 있는 변수들이 여러개 있을 수 있는데, 하나라도 delete되는 순간에 같은 곳을 가리키고 있는 나머지 변수들도 같이 사라진다. 

## Call by Value vs. Call by Reference
주소를 넘겨줌으로써 실제 메모리에 할당된 값 자체를 변경할 수 있다!

# References
> ![[IMG-20240926143630.png]]
> 같은 메모리 공간에 이름을 하나만 붙이는게 아니라, 다른 alias도 사용할 수 있게끔 하겠다. 메모리 주소를 2개의 변수와 associate 시켜준다. ![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/Lec 8 (9.26)/IMG-20250304110005.png]]

![[스크린샷 2024-09-26 오후 2.44.33.png]]
Const Reference를 통해 참조자를 사용할 때 함수내부에서 변수 값 원형이 변경되는 것을 막아준다. 

==언제언제 Reference및 pointer를 쓰는지 그 사용처를 잘 공부해두면 좋을 듯==
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Lec 8 (9.26)/IMG-20250313142327-1.png]]
==위의 내용 다시 봐보기==
