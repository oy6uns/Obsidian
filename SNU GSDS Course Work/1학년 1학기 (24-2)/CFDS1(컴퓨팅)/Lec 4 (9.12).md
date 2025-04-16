## Namespaces
C++ 은 naming conflicts의 risk를 줄이기 위해 따로 namespace라는 공간을 만들어서 function과 class를 관리한다. 

같은 함수명을 가지는 function이라도, namespace가 다르면 다른 함수로 취급된다. 
- namespace를 다른 이름으로 선언할 수도 있다. `alias`

`Namespace` vs. `Header`
- namespace는 코드 이름 충돌을 방지하기 위해 특정 이름 영역을 정의하는데 사용되지만, 헤더 파일을 코드 선언(declaration)을 포함하는 파일이다. 
- namespace와 header파일은 `many-to-many relationship`을 가진다. 하나의 네임스페이스가 여러 헤더 파일에 걸쳐 있을 수 있으며, 반대로 하나의 헤더 파일이 여러 네임스페이스를 정의할 수 있다. 
## Standard I/O Streams
cin & cout 
- C++ Standard Library이다. 
- predefined, globally accessible variables 이다. 
- 사용하기 위해 `namespace std` 를 명시해 주어야 한다. 
- framework for input and output operations를 제공한다. 

### cin
> istream이라는 class의 instance

standard input device(키보드) 로부터 input을 읽어옴
variable type 또는 whitespace가 나타날 때까지의 characters를 읽는다. 
- char: 하나의 character를 읽는다
- int: non-numeric character가 나타날 때까지 읽는다. 
- string: white space가 나타날 때까지 읽는다. 

### cout
> ostream 의 class → *write output to the standard output device(console)* 

`endl`
- ‘ n ’ 문자를 삽입시켜준다. 
- output buffer에 있는 데이터를 flush 시켜준다. 
`flushing`
- Python: `{python icon} print("...", flush=True)`
- C++: `{cpp icon} cout << " " << flush`
 

