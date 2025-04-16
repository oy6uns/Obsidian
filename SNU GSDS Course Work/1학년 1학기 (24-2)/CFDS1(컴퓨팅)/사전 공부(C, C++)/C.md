# NULL

![[Img/SNU GSDS/CFDS1(컴퓨팅)/사전 공부(C, C++)/C/IMG-20241112150211.png]]


---

![[Img/SNU GSDS/CFDS1(컴퓨팅)/사전 공부(C, C++)/C/IMG-20241112150211-1.png]]
1. 문자열에 대한 포인터(char str):
	str은 문자열 리터럴에 대한 포인터입니다. C의 문자열 리터럴은 메모리의 읽기 전용 섹션에 저장됩니다. str 포인터는 이 읽기 전용 메모리에 저장된 문자열 "abcdefghi"의 첫 번째 문자를 가리킵니다. 메모리는 읽기 전용이므로 이 포인터를 통해 문자열을 수정하려고 하면 정의되지 않은 동작(일반적으로 런타임 오류)이 발생합니다.
2. 문자 배열(char c_str[]):
	c_str은 문자 배열입니다. 이렇게 정의하면 "abcdefghi"라는 문자열이 배열에 복사되고, c_str은 쓰기 가능한 메모리에 있는 이 배열을 참조합니다. 즉, 배열은 쓰기 가능한 메모리 섹션에 저장된 별도의 복사본이므로 문자열의 개별 문자를 수정할 수 있습니다.


```cpp title="String 생각해보기2" hl:1,4-6
char str_a[] = "abc";
char str_b[] = "abc";

if (str_a == str_b) {
    // 이 부분이 실행되지 않는다.
}
```
if (str_a == str_b) 문은 포인터 str_a와 str_b를 비교합니다.
C에서 항등 연산자 == 는 두 포인터 str_a와 str_b가 동일한 메모리 위치를 가리키는지 확인합니다.
→ 

# 구조체
→ : 화살표는 구조체 포인터 안의 변수에 쉽게 접근하기 위해서 사용된다. 
```cpp
struct Company {  
	int age; 
	char name[10];
};
```

라는 구조체에 대해서 
`{cpp icon} struct Company *abc` 라는 구조체 포인터 객체를 선언한다고 하면 `{cpp} abc -> age` 로 abc 구조체의 age 변수에 접근할 수 있다. 
`{cpp icon}  *(abc).age` 로도 동일하게 접근할 수 있지만, 괄호와 * 을 사용하는 것이 번거롭기 때문에 → 기호를 추가적으로 사용하게 되었다. 
> [!NOTE]
> . 기호가 * 기호보다 우선순위가 높기에, 괄호를 안쓰면 오류가 발생하게 된다. 

# Static

`{cpp} static int a = 2;`
- static 변수는 자신이 선언된 범위를 벗어나더라도 파괴되지 않는 정적변수이다. 
- 정적변수의 경우 전역 변수처럼 데이터 영역에 저장되고 프로그램이 종료될 때 파괴된다. 또한, 정적 변수도 전역 변수처럼 정의 시 특별한 값을 지정해 주지 않는 한 0으로 자동 초기화 된다. 

# Include

.h 파일에 함수의 원형을 정의하고,
.c 파일에 함수의 내용을 정의함으로써

다른 파일들이 `#include "~.h"` 를 선언함으로써 통해 해당 .h파일에 정의된 함수를 사용할 수 있게 된다. 

# define
![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/사전 공부(C, C++)/C/IMG-20250304110005.png]]
#![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/사전 공부(C, C++)/C/IMG-20250313142327-1.png]]
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/사전 공부(C, C++)/C/IMG-20250313142327-2.png]]
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/사전 공부(C, C++)/C/IMG-20250313142328.png]]
# void

64비트 시스템에서 포인터의 크기는 8byte이다. 따라서
```cpp hl:3-4
/* void 형을 가리키는 포인터 */ #include <stdio.h>  
int main() {
	void* a; // 에러가 발생하지 않음
	void a; // 에러가 발생 
	return 0;
}
```
`포인터는 void 형으로 선언해도 아무 문제가 발생하지 않지만`, 변수는 void 형으로 선언할 수 없다. 

void 형 변수라는 것은 존재할 수 없기 때문에 void 형 포인터의 존재는 쓸모가 없어 보인다. 
하지만 사실 void 라는 타입이 없기 때문에 거꾸로 생각해 보면 어떠한 형태의 포인터 값이라도 담을 수 있게 된다. 

그대신 `형 변환` 이라는 절차를 거쳐주어야 한다. 

```cpp hl:8
#include <stdio.h> 
int main() {

	void *a;  
	double b = 123.3;

	a = &b;
	printf("%lf", *(double *)a);

	return 0; 
}
```
8번째 줄을 보면 ( double * ) 와 같이 double형인 변수를 가리킨다고 명시를 해주어야 한다. 
# main

```cpp
/* 인자를가지는메인함수*/  
#include <stdio.h>  
int main(int argc, char **argv) {
	int i;  
	printf("받은 인자의 개수 : %d \n", argc);

	for (i = 0; i < argc; i++) {  
		printf("이 프로그램이 받은 인자 : %s \n", argv[i]);
	}

	return 0; 
}
```

`int argc` : 프로그램 실행 시에 받은 인자의 개수
`char **argv` : 프로그램 실행 시에 받은 실제 인자의 리스트

# malloc
> memeory allocation의 약자

`{cpp} arr = (int *)malloc(sizeof(int) * SizeOfArray);`

사용할 배열의 크기만큼 메모리를 할당 한 후에 

`free(arr)` 을 통해 메모리를 해제시켜준다. 
`free` 를 제대로 하지 않으면 메모리 누수(memory leak)가 발생한다. 

C에서는 배열 선언시에 `score[student]` 와 같이 배열 크기에 변수를 집어넣을 수 없다. 
따라서 malloc을 사용해서 이 문제를 해결하는 것이 가능해진다. 
# typedef
![Img/Img/SNU GSDS Course Work/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/사전 공부(C, C++)/C/IMG-20250416150918-2.png](IMG-20250416150918-2%208.png)
