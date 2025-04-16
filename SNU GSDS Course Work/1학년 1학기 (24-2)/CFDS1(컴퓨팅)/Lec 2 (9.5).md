![[2 Review of CFDS.pdf]]
## Python vs C
python: memory operation 같은 경우에 따로 stack을 사용하지 않고, heap을 사용한다. 
C: stack을 통해 memory를 관리한다. 

`메모리 저장 부분 나중에 다시 공부하기`

## Class
Class vs Class Object

Attributes & Methods가 정의된 blueprint가 Class이고, 
그것을 기반으로 만들어진 Class 객체를 Class Object라고 한다. 

![[IMG-20240905151200.png]]
포인터 변수를 사용한 뒤에 더이상 사용하지 않을거면 NULL로 주소값을 초기화 시켜주는 것이 중요하다. 

## Stack vs. Heap
### Stack
higher addresses → lower addresses
컴파일러가 자동적으로 코드에서 사용될 공간을 계산해서 할당하기 때문에
따로 사용자가 할당 X
### Heap
malloc, free를 통해서 원하는 메모리를 dynamically 하게 할당하고, 제거할 수 있다. 



