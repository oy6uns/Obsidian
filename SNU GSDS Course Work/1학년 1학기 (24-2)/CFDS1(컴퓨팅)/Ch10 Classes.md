`std::initializer_list<int> elements`
- 클래스 인자로 vector를 전달할 수 있게 해준다. 
### 선언 시에
```cpp
// Declare a pointer and assign a dynamic object
1. SimpleVector<int> *intVecPtr3 = new SimpleVector<int>(10);

// Create a new object using an initializer list

2. SimpleVector<int> *intVecPtr4 = new SimpleVector<int>({1, 2, 3});

3. SimpleVector<int> *intVecPtr5 = new SimpleVector<int>{1, 2, 3};
```
- 3가지 방법 중 어떤 것을 사용해도 괜찮다. 
## Class Templates
- 템플릿 클래스가 `int`, `float`, `double` 등 서로 다른 데이터 타입으로 사용될 때마다, 컴파일러는 각 타입에 맞는 별도의 기계어 코드를 생성한다. 
## Destructors
- delete 연산자로 메모리를 해제할 때
- 스마트 포인터가 소멸될 때
- 함수가 종료되거나, 스코프를 벗어날 때

# Operator Overloading
## ++ Postfix Increment Operator
전위증가 연산자와 다르게 (int)를 뒤에 붙여서 명시를 해준다. 
