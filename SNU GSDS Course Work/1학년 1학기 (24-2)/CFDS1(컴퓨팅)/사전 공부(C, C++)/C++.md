# 참조자의 등장

C에서는 &(엔퍼센트) 로만 쓰이던 기호가 C++ 으로 넘어오면서 한가지 쓰임새가 추가되었다. 

바로, 레퍼런스 기능인데, ![[Img/SNU GSDS/CFDS1(컴퓨팅)/사전 공부(C, C++)/C++/IMG-20241112150211.png]]
another_a는 a의 또다른 이름이라고 컴파일러에게 알려주는 것이다. 
### 레퍼런스 특징
1. 레퍼런스는 반드시 처음에 누구의 별명이 될 것인지를 지정해야 한다. ![[Img/SNU GSDS/CFDS1(컴퓨팅)/사전 공부(C, C++)/C++/IMG-20241112150211-1.png]]
2. 레퍼런스가 한 번 별명이 되면 절대로 다른 이의 별명이 될 수 없다. 
	→ 한 번 어떤 변수의 참조자가 되버리면, 더이상 다른 변수를 참조할 수 없게 된다. 
3. 레퍼런스는 메모리 상에 존재하지 않을 수도 있다. 
	→ 그러나 항상 존재하지 않는 것은 아니다!
