![[IMG-20241029161123.png]]![[IMG-20241029161208.png]]
![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/Ch7 Pointers and References/IMG-20250304110005.png]]
`{cpp} &(*arr)`은 `{cpp}&(arr[0])`과 동일하기 때문에 출력시에, `{cpp} &(arr[0])`의 주소값이 출력된다. 

## 2차원 배열의 할당(Dynamic)
![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/Ch7 Pointers and References/IMG-20250304110005-1.png]]
## Static vs. Dynamic
Static은 배열이 선언시에 배열의 모든 값들이 Stack에 저장된다. 
Dynamic은 배열의 시작 주소만 Stack에 저장되고, 나머지는 heap에 생기게 된다. 

`Dynamic Array`에서 `{cpp}&arr`과 `Static Array`에서 `{cpp}&arr`은 값이 서로 다르다. 
## Smart Pointers
- 사용되지 않는 `pointer`는 **memory leaks**과 **dangling pointers**의 문제가 있다. 
  **dangling pointers**: 해제된 메모리 위치를 가리키는 포인터
- 스마트 포인터는 원시 포인터를 캡슐화하여 더 이상 필요하지 않을 때 할당된 메모리를 자동으로 삭제한다. 
![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/Ch7 Pointers and References/IMG-20250304110007.png]]
### std::unique_ptr
- 다른 변수와 메모리를 공유할 수 없다. **복사나 이동이 불가능**하다. 
- 포인터로 가리킬 타입을 지정해야한다. 
- 소유하고 있는 포인터가 더 이상 필요 없어지면, **메모리를 자동으로 해제**하여 메모리 누수를 방지한다. 
### std::shared_ptr
- 여러개의 포인터 변수가 같은 메모리를 가리킬 수 있다. 
  → **다른 포인터 변수가 복사**하여 소유권을 공유 가능하다. 
- **마지막 소유자**가 파괴되거나 리셋되면 **메모리를 자동으로 해제**한다. 
![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/For Midterm Cheet Sheet/IMG-20250304110005.png]]
## Call by Reference
### Const References
- `const`키워드를 붙여, 함수가 해당 데이터에 대해 수정할 수 없는 보장된 참조를 제공하여 데이터의 **무결성**을 보장한다. 
- 만약, 함수 내에서 해당 매개변수를 수정하려는 시도는 **컴파일 오류**를 발생시킨다. 
## Reference Return
![[Img/Img/SNU GSDS/1학년 1학기/CFDS1(컴퓨팅)/Ch7 Pointers and References/IMG-20250304110007-1.png]]
참조형을 return 하기 때문에,
`{cpp}middleElement(m) = 10` 을 실행하면, 
map의 두번째 튜플의 value를 10으로 직접 변경시켜줄 수 있다. 
→ 불필요한 복사를 방지할 수 있기에 대용량 데이터 처리에 유용하다. 
