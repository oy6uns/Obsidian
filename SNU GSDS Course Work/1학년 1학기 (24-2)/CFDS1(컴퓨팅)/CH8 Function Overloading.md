## Function Overloading
- **반환형만으로 함수를 구분할 수 없다.**  반환형이 다르더라도 매개변수의 조합이 같으면 오버로딩이 불가능하다. 
- **함수 이름이 같더**라도 **매개변수의 조합이 다르면** 서로 다른 함수로 인식됩니다.
### 단점
- **코드 관리의 어려움:** 같은 기능을 수행하는 함수가 여러 개 존재하기 때문에 코드를 관리하기 어려울 수 있다.
- **오버로딩의 남용:** 함수 오버로딩을 남용하면 코드가 복잡해지고 오류 발생 가능성이 높아진다. 
## Function Templates
```cpp
template <typename T>
void mySwap(T &x, T &y) {
	T temp = x;
	x = y;
	y = temp;
}
```
### 함수 호출 시
```cpp
// 앞에 변수형을 써준다. 
mySwap<int>(intA, intB);
mySwap<double>(doubleX, doubleY);

// 컴파일러가 추론하는 것도 가능
mySwap(a, b);
```
### 변수형 명시해줘야하는 경우
![[IMG-20241029174322.png]]
1. 함수가 어떤 값을 반환해야되는지에 대한 정보가 없는 경우
2. null 포인터를 전달하는 경우
등 어떤 자료형인지를 정확히 판단하기 어려운 경우
### Non-Type Template Arguments
컴파일타임에 빠르게 함수에 상수 값을 매개변수로 초기화시켜주고 싶을 때, 사용한다. 
```cpp
#include <iostream>
// Compute base^N with loop unrolling
template <int N>
int power(int base) {
	int result = 1;
	for (int i = 0; i < N; ++i)
		result *= base;
	return result;
}

int main() {
	std::cout << "2^3 = " << power<3>(2) << '\n';
}
```



