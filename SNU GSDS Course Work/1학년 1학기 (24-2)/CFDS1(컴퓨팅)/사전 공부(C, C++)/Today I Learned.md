## 9/28
- [ ] BKMS1 Lab3 과제 ⏳2024-09-28 ⏫ 
- [ ] MLDL1 Assignment1 #3, #4
- [ ] **CS162: OS&SP** Ch1, 2
- [ ] BKMS1 Week3 Buffer Manager-M 강의🔽 

## 10/10
&a = b 
→ 참조자이다! a는 b의 값을 참조(가리키게)하게 된다. 

``` cpp
int main() {
	a = 1
	b = 2
	c = sum_of_two(a, b)
}

void sum_of_two(&x, &y) {
	return x+y;
}
```

위와 같이 작성하면, 함수가 실행될 때`{cpp} &x = a, &y = b` 와 같이 참조자가 변수를 참조하게 되고, x와 y가 a, b의 주소값에 대응되는 값을 직접적으로 사용할 수 있다!!!

문자열을 stream으로 만들면
1. 구분자가 섞여 있는 문자열을 parsing하기가 쉬워진다
```cpp
#include <iostream>
#include <sstream>
#include <string>

int main() {
    std::string line = "Name, Age, Country";
    std::stringstream ss(line);  // 문자열을 스트림으로 초기화
    std::string field;
    
    // 구분자 ','를 기준으로 문자열을 분리하여 각 필드를 읽어옴
    while (std::getline(ss, field, ',')) {
        std::cout << "Field: " << field << std::endl;
    }
    
    return 0;
}

```
2. 원하는 변수형으로 문자를 변경해서 사용할 수 있다. 
```cpp
std::string line = "123 456.78";
std::stringstream ss(line);
int integer_part;
double float_part;

ss >> integer_part >> float_part;  // 정수 123과 실수 456.78을 각각 추출
std::cout << "Integer: " << integer_part << ", Float: " << float_part << std::endl;

```
3. 데이터를 특정 형식으로 결합해서 사용할 수 있다.
```cpp
#include <iostream>
#include <sstream>
#include <string>

int main() {
    std::stringstream ss;
    std::string name = "John";
    int age = 30;
    std::string country = "USA";

    ss << "Name: " << name << ", Age: " << age << ", Country: " << country;
    
    std::string result = ss.str();  // 스트림의 내용을 문자열로 변환
    std::cout << result << std::endl;  // "Name: John, Age: 30, Country: USA" 출력

    return 0;
}

```

`{cpp icon} a = 3`

asdf
***시라이시 모모***