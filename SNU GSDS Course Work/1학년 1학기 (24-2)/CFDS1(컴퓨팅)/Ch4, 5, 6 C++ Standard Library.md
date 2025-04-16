## Namespaces
- 함수나 클래스 밖의 전역 스코프에 선언된 함수, 변수, 타입명과 같은 식별자들은 프로그램의 모든 부분에서 접근 가능하지만, 이로 인해 **충돌의 위험**도 증가한다. 
- **같은 범주에 속한 이름들을 하나의 범위로 그룹화하는 방법**을 제공한다. 
- **C++ Standard Library**는 입출력 연산, 자료구조, 알고리즘 등의 공통적인 기능을 포함하며, 이는 `std` namespace 내에서 캡슐화된다.
- namespace는 **다양한 코드 영역에서 관리 및 확장**될 수 있다. 즉, namespace는 한 곳에만 선언될 필요가 없으며, 다른 영역에서 계속 확장될 수 있다. 
### Namespaces - *using*
- 특정 네임스페이스에 있는 식별자를 현재 선언 영역(함수나 전역 스코프 등)에 **도입**한다. 
- 네임스페이스 이름을 명시하지 않고도 해당 식별자에 직접 접근이 가능하다. 
```cpp
int main () {
	using first::x;
	std::cout << x << std::endl;
	std::cout << second::x << std::endl;
	return 0;
}
```

- `using` 지시어는 **전체 namespace**를 도입하는 데도 사용될 수 있다. 
  → 코딩의 편리함을 제공하지만, 동시에 이름 충돌의 위험이 증가하게 된다. 
### Namespaces - Aliasing
- 기존 네임스페이스에 별칭을 지정해서 사용할 수 있다. 
- `{cpp icon} namespace third = first;`
### Namespaces != Header Files
- 네임스페이스와 헤더 파일은 다대다 관계를 가지고 있다. 
- 즉, **하나의 네임스페이스가 여러 헤더 파일**에 걸쳐 있을 수 있고, **하나의 헤더 파일이 여러 네임스페이스를 포함**할 수도 있다. 
# Standard I/O Streams
## cin
- C++의 `cin`은 타입 변환을 자동으로 처리하므로, 형 변환 지정자(`scanf("%d", &age)`에서 `%d`와 같은)를 따로 사용할 필요가 없다. `cin >> age`와 같이 간단하게 쓸 수 있다. 
- 입력된 데이터가 **stream buffer**로 들어가고, `cin`이 그 데이터를 추출하여 각 변수로 할당시킨다.
1. `cin`은 변수의 타입에 맞는 만큼의 문자를 읽거나, 공백이 나타날 때까지 문자를 읽는다. 
	- `char`: 한 문자를 읽는다.
	- `int`: 숫자가 아닌 문자가 나올 때까지 읽는다. 
	- `string`: 공복이 나올때까지 문자열을 읽는다. 
2. **버퍼에 남는 초과 문자**
	- 입력이 끝나도 남은 문자는 버퍼에 남아 있으며, 이후의 추출 연산으로 읽어들일 수 있다. 이를 통해 **예상하지 못한 입력**이 발생할 수 있다. 
### cin - Demystified
1. `cin`은 `istream`**클래스**의 객체다. 즉, 입력 스트림을 다루는 클래스의 인스턴스다. 
2. `>>`연산자
	`>>`는 `cin`객체의 메서드이다. 정확히는 **연산자 오버로딩**된 `operator>>()`함수이다. 
	`cin >> age` == `cin.operator>>(age)`와 **기능적으로 동일**하다. 
### cin - Error Handling
1. 오류 발생 시, `cin.clear()`로 오류 상태를 해제한다. 
2. `cin.ignore(numeric_limits<streamsize>::max(), '\n')`으로 입력 버퍼에 남아있는 잘못된 입력을 무시한다. 
- **`cin.clear()`: 오류 상태를 리셋한다.** 
- **`cin.ignore()`: 버퍼에 남아있는 데이터를 무시한다.** 
### cin - Reading a Line
```cpp
int main(void) {
	std::string myStr;
	std::getline(std::cin, myStr);
	std::cout << myStr;
	return 0;
}
```
- `getline`함수는 `cin`으로부터 한 줄 전체를 문자열로 읽어들인다. 
  이때, 공백을 구분하지 않고 한 줄 전체를 입력받을 수 있다. 
## cout
- `cout`은 `ostream`클래스의 인스턴스로, 데이터를 출력하는 데 사용된다. 
- `<<`연산자를 통해 데이터가 출력 버퍼로 보내진다. 
- `endl` 
	1. 줄 바꿈 문자(‘\n’)를 출력 버퍼에 삽입한다. 
	2. 출력 버퍼를 Flush하여 버퍼에 쌓인 데이터를 강제로 출력한다. 
- **Flushing**
	- `endl`을 사용하지 않을 경우 특정 조건에서만 Flush된다. 
		- 버퍼가 가득 찼을 때
		- `cin`으로 입력을 받을 때
		- 프로그램이 정상 종료될 때
### cout - Formatting Output
- **Fixed-Point Notation** - `{cpp icon} #include<iomanip>`
	-  `{cpp icon} std::setprecision(n)`: 소수점 n번째자리까지 출력
- **Leading Zeros**
	- `{cpp icon} std::setfill('0'), std::setw(8)`: 출력 길이를 8로 정하고, 나머지 자리를 0으로 채움
### Useful Manipulators for Formatting
1. **`setw(int n)`**:
    - 다음 출력 작업에 대해 출력 필드의 너비를 설정한다. 출력될 값이 이 필드에 맞춰져 출력된다.
2. **`left`, `right`, `internal`**:
    - `setw`로 설정한 필드 너비 내에서의 **정렬 방식**을 설정한다.
        - `left`: 왼쪽 정렬
        - `right`: 오른쪽 정렬
        - `internal`: 내부 정렬(음수 부호는 왼쪽에, 숫자는 오른쪽에 위치)
3. **`setfill(char c)`**:
    - `setw`로 설정한 필드를 채우기 위한 **채움 문자**를 설정한다. 기본적으로 공백으로 채워지지만, 다른 문자를 설정할 수 있다.
4. **`setprecision(int n)`**:
    - 소수점 이하 자릿수 또는 총 유효 자릿수를 설정한다. **부동 소수점 숫자**의 자릿수를 조정할 때 유용하다.
5. **`fixed`**:
    - **고정 소수점 표기법**으로 부동 소수점 숫자를 출력하도록 강제한다.
6. **`scientific`**:
    - **과학적 표기법**으로 부동 소수점 숫자를 출력하도록 강제한다.
7. `toupper(char c)`, `tolower(char c)`:
	- 입력된 문자를 **대문자 또는 소문자로 변환**한다
### Other Standard Streams
- `std::cerr`: 버퍼링 없이 오류를 즉시 출력
	`cout`은 버퍼링되기 때문에 즉시 출력이 보장되지 않는다. 
- `std::clog`: 버퍼링을 통해 효율적인 출력
- - -
## Strings
### Strings - Comparison
![[IMG-20241028171708.png]]
### Strings - Finding Substrings
```cpp 
string fullName = "Data Science";
size_t pos = fullName.find("Science");
if (pos != string::npos) {
	cout << "Found 'Science' at position: " << pos << endl;  
}
// Output: Found 'Science' at position: 5
```
- `string::npos`: 만약에 find()가 substring을 찾지 못한경우 npos를 반환하게 된다.
### Strings - Memory
만약에 array의 capacity를 넘게 되면, 더 큰 size로 array가 reallocate된다. 
# Containers
- iteration
- list
- map
- set
## Vector

### Iterators
```cpp
1.
for(vector<int>::iterator it = vec.begin(); it != vec.end(); ++it) {
	cout << *it << endl;
}

2. 
for(auto it = vec.begin(); it != vec.end(); ++it) {
	cout << *it << endl;
}
```
### Finding Elements
- `{sql} #include <algorithm>`
```cpp hl:1,5
auto it = std::find(vec.begin(), vec.end(), 3);

if(it != vec.end()) {// 중간에 3값을 찾아서 it가 중간에 멈춘다면
	std::cout << "found 3 at index:" 
	<< std::distance(vec.begin(), it)} << std::endl;
} else {
	~~~
}
```
- `#include <numeric>`
  `{cpp icon} int sum = std::accumulate(vec.begin(), vec.end(), 0);
## List
- 리스트는 배열과 달리 메모리 상에서 연속적인 공간을 할당받지 않아 각각의 요소가 개별적으로 메모리 상에서 흩어져 있다. 
 - **Doubly Linked List**로 구성되어 있어서 **효율적인 삽입 및 삭제**가 가능하다.
 - **Random Access**를 지원하지 않기 때문에, 원하는 요소를 찾기 위한 탐색은 **O(n)** 시간이 걸린다. 
### list - Insertion, Deletion
```cpp
std::list<int> lst = {1, 2, 3, 4, 5};

lst.push_back(6);
lst.push_front(7);

auto it = lst.begin();
std::advance(it, 4);
lst.insert(it, 8);
lst.erase(it)

lst.pop_back();
lst.pop_front();
```
### list - Iteration
vector와 마찬가지로 iteration 가능
![[IMG-20241028191133.png]]
각 값들의 주소값이 vector와 다르게 흩어져있음. 

## map
- map은 **binary-search tree**(usually red-black tree) 로 구현되어 있기 때문에, 키를 삽입할 때마다 자동으로 오름차순으로 정렬된다. 
- iterator가 it→first, it→second로 각 원소의 key, value에 접근할 수 있다. 
- for문으로 원소를 출력하면 자동으로 정렬된 Key의 value들이 출력된다. 
### map - Insertion, Deletion
```cpp
map<string, int> ageMap;

ageMap["Alice"] = 35; // Update the value
cout << ageMap["David"] // 키가 없을 때 Int면 0, String이면 ""을 출력한다. 

ageMap.erase("Alice");
ageMap.erase("Bob"); // 키가 없는 것을 없애려 해도 에러는 발생 X
```
### map - Search
```cpp
auto it = ageMap.find("Charlie");
if(it != ageMap.end()) {
	count << it->first << ": " << it->second << endl;
}
```
## set
- set은 중복되지 않는 고유한 요소만을 저장할 수 있는 container이다. 
- key와 value가 동일해, **요소 자체가 곧 키**로 간주된다. 
- map과 같이 **binary-search tree**(usually red-black tree) 로 구현되어 있기에 **저장된 값들이 자동으로 오름차순**으로 정렬된다. 
- 하나 특이한 점은 `iterator`를 통해 `erase`함수를 실행시킬수도 있다. 
```cpp
// Removing elements
std::set<int> mySet = {2, 4, 1, 5, 3};

mySet.erase(4); // By value
auto it = mySet.find(2);
if (it != mySet.end()) {
	mySet.erase(it); // By iterator
}
```
![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/Ch4, 5, 6 C++ Standard Library/IMG-20250304110005.png]]