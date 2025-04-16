![[5 C++ Standard Library (2).pdf]]
## File I/O Streams
어떻게 file에서 input/output을 하는지를 아는 것이 중요하다.
`ifstream`, `ofstream` → C++ Standard Library’s `<fstream>` 을 include 해서 사용한다. 

### File I/O Streams - Reading
- Open a file for reading
	`{cpp} ifstream file("PATH");`
	`{cpp} file.open("PATH")`;
- Check if the file is successfully open 
	`{cpp} file.is_open()`

> [!중요하게 봐야하는 부분]
> 각 변수형에 따라 C++이 어떻게 변수를 읽고, 출력하는지를 잘 봐야할 것 같다. 

### File I/O Streams - Writing
- Open a file for writing
	`{cpp} ofstream file("PATH");`
	`{cpp} file.open("PATH")`;
- Check if the file is successfully open 
	`{cpp} file.is_open()`
- Writing
	`{cpp} file << "쓸 것" << std:endl;`

==코드 부분 실행 다시 보기==

## Strings
==여러 내장 함수 및 기능 따로 공부하기 ==
### Memory
small string을 추가했을 때는 capacity와 Memory Address의 변화가 없지만, 
large string을 추가했을 때는 capacity 및 Memory Address가 더 많은 공간을 할당 할 수 있는`새로운 메모리 공간으로 reallocate`된다. 

## Containers
### Vector
==특징==
> [!중요하게 봐야하는 부분]
> 변수 및 함수들이 Stack, Heap 등 어느 부분에 allocate되는지를 확인

- ==Vector 선언 방식==
- Insertion, Deletion

→ Vector Iteration 부터 다음 시간에,,



