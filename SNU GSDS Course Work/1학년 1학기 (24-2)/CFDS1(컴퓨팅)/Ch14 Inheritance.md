### 1. Access Control for Inheritance
- **`public` 상속**:
    - 상속 시 `public`을 사용하면 기본 클래스(Base Class)의 멤버 접근 수준을 유지합니다. 예를 들어, 기본 클래스의 `public` 멤버는 파생 클래스에서도 `public`으로 유지됩니다.
- **`protected` 상속**:
    - `protected` 상속을 사용하면 기본 클래스의 `public` 멤버가 파생 클래스에서 `protected`로 바뀌며, 외부에서는 접근할 수 없게 됩니다.
- **`private` 상속**:
    - `private` 상속을 사용하면 기본 클래스의 모든 멤버가 파생 클래스에서 `private`로 바뀌며, 외부와 파생 클래스에서도 접근할 수 없게 됩니다.
### 2. 추가 멤버 선언 (Additional Attributes)
- **기본 클래스 외에 추가 속성 선언**:
    - 파생 클래스는 기본 클래스에 없는 추가 속성을 선언할 수 있습니다. 예제에서는 `electricLevel`이 `Pikachu` 클래스에 추가되었습니다.
- **기본 클래스의 속성을 다시 선언하지 않음**:
    - 기본 클래스에서 이미 선언된 속성은 파생 클래스에서 다시 선언할 수 없습니다. 만약 동일한 이름으로 변수를 선언하면 기본 클래스의 변수와 파생 클래스의 변수가 별도로 존재하게 됩니다.
### 3. 생성자 (Constructors)
- **기본 클래스의 생성자 호출**:
    - ==파생 클래스의 생성자는 기본 클래스의 생성자를 명시적으로 호출==할 수 있습니다. 이 호출은 초기화 리스트에서 이루어지며, 기본 클래스가 먼저 초기화되어야 하기 때문에 생성자 본문에서는 호출할 수 없습니다.
- **명시적 호출이 없을 경우 기본 생성자 호출**:
    - 기본 클래스 생성자가 명시적으로 호출되지 않으면 컴파일러는 자동으로 기본 생성자를 호출하려고 시도합니다.
- **생성자는 상속되지 않음**:
    - 기본 클래스의 생성자는 파생 클래스에 상속되지 않으며, 파생 클래스에서 별도로 생성자를 정의해야 합니다.
### 4. 소멸자 (Destructors)
- **==기본 클래스의 소멸자는 상속되지 않음==**:
    - 기본 클래스의 소멸자는 파생 클래스에 상속되지 않습니다.
- **파생 클래스 소멸 후 기본 클래스 소멸**:
    - 파생 클래스 객체가 파괴될 때, 파생 클래스의 소멸자가 먼저 호출되고 그다음에 기본 클래스의 소멸자가 호출됩니다.

## Dynamic Binding
파생 클래스의 독자적인 자원 해제를 위해 기본 클래스의 소멸자를 virtual로 선언해야 한다. 
예제 코드에서 `Pokemon* pokemon = new Pikachu();`로 객체를 생성하고, `delete pokemon;`으로 삭제하는데, `Pokemon`의 소멸자가 가상 함수로 선언되어 있지 않다면 `Pikachu`의 소멸자는 호출되지 않습니다.

### 예시

다음은 가상 함수 없이 구현한 경우와 가상 함수를 사용한 경우의 차이입니다.
#### 가상 함수 없이 구현한 경우 (잘못된 접근)
```cpp
class Pokemon { 
public:     
	void attack(Pokemon& target) {         
	std::cout << "[Base] Pokemon attacks " << target.getName() << std::endl;    
	} 
};  

class Pikachu : public Pokemon { 
public:     
	void attack(Pokemon& target) {         
	std::cout << "Pikachu uses Thunderbolt on " << target.getName() << std::endl;     
	} 
};`
```

위와 같이 하면 `Pokemon* pokemon = new Pikachu();`로 선언해 `pokemon->attack(target);`을 호출해도 **기본 클래스의 `attack()`만 호출**됩니다. 이를 해결하기 위해 가상 함수를 사용해야 합니다.
#### 가상 함수를 사용한 경우 (올바른 접근)
```cpp
class Pokemon { 
public:     
	virtual void attack(Pokemon& target) {         
		std::cout << "[Base] Pokemon attacks " << 
		target.getName() << std::endl;     
	} 
};
```

이제 `attack()` 메서드를 가상 함수로 선언하면, `Pokemon* pokemon = new Pikachu();`로 선언한 후 `pokemon->attack(target);`을 호출해도 **`Pikachu`의 `attack()`이 호출**됩니다.

### Dynamic Binding (동적 바인딩)
- **정의**:
    - 동적 바인딩은 객체의 실제 타입에 따라 올바른 함수 구현을 런타임에 호출하는 메커니즘입니다. 기본 클래스 포인터나 참조로 가상 함수를 호출할 때, 해당 포인터가 가리키는 객체의 타입에 맞는 메서드를 실행하게 됩니다.
    - 예를 들어, 기본 클래스의 포인터가 `Pikachu` 객체를 가리키고 있으면, `Pokemon`의 `attack()`이 아닌 `Pikachu`의 `attack()` 메서드를 호출할 수 있습니다.
### Virtual Table (vtable)
- **역할**:
    - `vtable`은 가상 함수 호출을 지원하기 위해 사용되는 메커니즘입니다. 각 클래스는 가상 함수들의 포인터를 저장하는 `vtable`을 가지고 있으며, 이 테이블의 각 항목은 해당 클래스에서 구현된 가상 함수로 연결됩니다.
    - 기본 클래스와 파생 클래스 모두 `vtable`을 가질 수 있으며, 파생 클래스가 가상 함수를 오버라이드하면 해당 클래스의 `vtable`에 새로 정의된 함수의 포인터가 포함됩니다.
### Virtual Pointer (vptr)
- **역할**:
    - `vptr`은 각 객체가 자신의 클래스의 `vtable`을 가리키도록 하는 포인터입니다. 클래스에 가상 함수가 있다면, 컴파일러는 각 객체 인스턴스에 `vptr`을 추가합니다.
    - 객체가 생성될 때 `vptr`이 자동으로 초기화되어 해당 객체의 클래스 `vtable`을 가리키도록 설정됩니다. 이를 통해 객체가 실제로 가리키는 `vtable`을 통해 올바른 함수가 호출될 수 있습니다.
### Compile Time (컴파일 타임) 동작
- **vtable 생성**:
    - 컴파일러는 가상 함수가 있는 각 클래스에 대해 `vtable`을 생성합니다. 이 테이블은 해당 클래스의 가상 함수들의 포인터 배열을 포함합니다.
- **vptr 초기화**:
    - 컴파일러는 객체 생성 시 각 객체의 `vptr`을 초기화하여 클래스의 `vtable`을 가리키도록 설정합니다. 따라서, 객체가 생성될 때마다 객체의 `vptr`이 해당 클래스의 `vtable`로 설정됩니다.
### Runtime (런타임) 동작
- **가상 함수 호출 과정**:
    1. 가상 함수 호출 시, 먼저 객체의 `vptr`을 통해 해당 객체의 `vtable`에 접근합니다.
    2. `vtable`에서 호출하고자 하는 함수 포인터를 가져옵니다. 이때 함수의 오프셋에 따라 올바른 함수 포인터를 가져오게 됩니다.
    3. 가져온 함수 포인터를 사용하여 실제 함수를 호출하게 됩니다.


### Dynamic (Runtime) Polymorphism (동적 다형성):
- 동적 다형성은 **런타임에 호출할 정확한 함수를 결정**하는 방식으로, **가상 함수(virtual function)** 와 **동적 바인딩(dynamic binding)** 을 통해 구현됩니다.
- `dynamic_cast`: 런타임에 타입 변환(**포인터와 참조 간 변환**에 주로 사용)
  → 가상함수가 있어야만 사용 가능(vtable을 통해 타입 정보 제공)
```cpp
ElectricPokemon* elecPokemon = new Pikachu();
Pikamander* pikamander = dynamic_cast<Pikamander*>(elecPokemon); // nullptr 반환 
if (pikamander) { 
	// 타입 일치 
} else { 
// 타입 불일치 
}
```
### Static (Compile-Time) Polymorphism (정적 다형성):
- 정적 다형성은 **컴파일 타임에 호출할 함수를 결정**하는 방식으로, 함수 오버로딩(overloading)과 템플릿(templates)을 통해 구현됩니다.
- `static_cast`: 컴파일 타임에 타입 변환(잘못된 타입 캐스팅 시 안전성 보장 X)
```cpp
- upcasting
	Pikachu pikachu; ElectricPokemon* elecPokemon =
	static_cast<ElectricPokemon*>(&pikachu);
- downcasting
	`ElectricPokemon* elecPokemon = new Pikachu(); 
	Pikachu* pikachu = static_cast<Pikachu*>(elecPokemon); 
```

### 추가적인 type casting
- `const_cast`: `const`를 제거하여, 값을 수정할 수 있도록 해주는 캐스팅
```cpp
void func(const std::string& str) {
    std::string& nonConstStr 
    = const_cast<std::string&>(str);
    nonConstStr += "_suffix"; // OK
}
```
- `reinterpret_cast`: 포인터 타입 간의 변환을 수행, 메모리 주소를 다른 포인터 유형으로 해석 시에 유용
> [!trade-off]
> 가상 함수는 런타임에 어떤 메서드를 호출할지 결정하기 때문에 **런타임 비용**이 증가할 수 있습니다. 이 비용은 가상 함수 테이블(vtable)과 같은 추가적인 메모리 사용과 함수 호출 시 오버헤드로 나타납니다. 따라서 성능이 매우 중요한 상황에서는 가상 함수 사용을 지양할 수도 있습니다. 하지만 **객체지향 프로그래밍에서는 유연성과 유지 보수를 위해 다형성을 허용하는 경우가 많으며, 이에 따른 런타임 비용을 감수**
## Error 처리
![[IMG-20241031041840.png]]
일어나서 할 것
- [x] 15 Inheritance - Advance ppt 보기 ✅ 2024-10-31
- [x] excercise 2 마저하고, 3까지 끝내기 ✅ 2024-10-31
- [ ] quiz 1,2,3 훑으면서 정리하기
- [ ] 다시 초반부터 훑으면서 ==cheetsheet== 만들기
- [ ] homework1 다시 풀어보기
- [ ] 시간 남으면) homework2 gpt 돌린 뒤에 풀어보기