## Copy Semantics
```cpp
SimpleVector<int> vec{1, 2, 3};
SimpleVector<int> copiedVec = vec;
```
### Copy Constructor
- 새로운 객체가 생성될 때 호출됨
- 객체 생성과 동시에 초기화를 위해 사용된다. 
- 주로 `const`참조를 인자로 받는 `생성자`로 정의되며, 객체의 **깊은 복사**(deep copy)를 수행한다. 
![[IMG-20241030232559.png]]
### Copy Assignment Operator
- 이미 생성된 객체에 다른 객체의 값을 대입(업데이트)할 때 호출된다. 
- `operator=`를 오버로드하여 구현하며, **깊은 복사**를 통해 기존 리소스를 관리하고 업데이트한다. 
  → 기본 대입 연산자는 얕은 복사를 수행하므로, 리소스를 관리하려면 깊은 복사를 직접 정의해야 한다. 
![[IMG-20241030232611.png]]
## Move Semantics
- 객체의 소유권을 바꿔 리소스를 효율적으로 관리하는 데 초점을 맞춘다. 
- **깊은 복사**(Deep Copy)의 비용 문제: 복사 연산은 새로운 메모리를 할당하고 데이터를 복사하는 작업이 필요하므로 비용이 많이 든다. 
  → 복사할 원본 객체가 더 이상 필요하지 않을 경우(ex. 임시 객체), 굳이 복사를 할 필요 없이 **리소스의 소유권을 이동**시키는 것이 더 효율적이다.
- 원본 객체의 리소스를 **깊은 복사**하지 않고, 이동시킨다. 이동된 원본 객체는 이후에 더 이상 사용되지 않으며, 불필요한 리소스를 해제하거나 기본 상태로 초기화될 수 있다. 
> [!&&가 뭔가요]
> C++11부터 도입된 **rvalue reference**를 의미한다. 
> `&&`을 사용하면 임시 객체나 더 이상 필요 없는 객체를 참조할 수 있다. 

### - Move Constructors![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/Ch12 Special Members/IMG-20250304110005.png]]
기존의 `{cpp}SimpleVector<T>&& src`의 값들로, array, size, capacity를 초기화시킨 뒤에, 기존의 `{cpp}src`는 `nullptr`및 0으로 초기화시켜준다(valid하긴 하지만, unspecified 상태). 메모리 문제를 해결할 수 있다. 
### Move Assignment Operator
![[Img/Img/SNU GSDS/1학년 1학기 (24-2)/CFDS1(컴퓨팅)/Ch12 Special Members/IMG-20250313142327-1.png]]

```cpp
1. copy constructors
	SimpleVector<int> vec{1, 2, 3};
	SimpleVector<int> copiedVec = vec;
```

```cpp
2. copy assignment operator
	SimpleVector<int> vec1{1, 2};	
	SimpleVector<int> vec2{3, 4};
	vec1 = vec2;
```

```cpp
3. move constructors
	SimpleVector<int> vec = SimpleVector<int>{1, 2};
	ㅡㅡㅡ
	SimpleVector<int> vec1{1, 2};
	SimpleVector<int> vec2 = std::move(vec1);
```
- 객체가 새롭게 생성되면서 리소스가 이동된다. 
```cpp
4. move assignment operator
	SimpleVector<int> existingVec{1, 2};
	existingVec = SimpleVector<int>{3, 4};
	ㅡㅡㅡ
	SimpleVector<int> vec1{1, 2};
	SimpleVector<int> vec2{3, 4};
	vec2 = std::move(vec1);
```

- 객체가 이미 존재하며 뒤의 리소스를 덮어쓴다. 
  → 뒤의 객체`(SimpleVector<int>{3, 4})`는 임시 객체로 다시 초기화 된다. 
## Static Members
모든 클래스 인스턴스는 Static 멤버를 공유한다. 
