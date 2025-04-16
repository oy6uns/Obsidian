# What is the Internet?

## The Internet: a “nuts and bolts” view

: 수백만개의 연결된 컴퓨팅 장치들이 존재하고 각각의 컴퓨팅 장치에서는 host 또는 end system에 네트워크 어플리케이션이 실행되고 있다. 
> `running network apps at Internet's "edge"`

- **national or global ISP**
	국가 인터넷 서비스 제공업체 : 
	글로벌 인터넷 백본 네트워크를 관리하고 국가 간 데이터 흐름을 조절한다. 
	c.f. 미국의 Comcast, 한국의 KT, SKT, LG U+ 등
- **local or regional ISP**
	지역 인터넷 서비스 제공업체 : 작은 규모의 네트워크 인프라를 관리하고 고객에게 로컬 또는 지역적인 인터넷 서비스를제공한다
- **content provider network(CPN)**
    컨텐트 제공자(c.f. 스트리밍 서비스, 웹사이트, 어플리케이션)와 사용자 간의 데이터 전송 및 배포를 관리하는데 사용된다. 
    -> 대용량 데이터의 효율적인 전달, 빠른 성능, 안정성, 및 사용자 경험 향상을 목표로 한다. 
- **enterprise network**
	기업 네트워크
	1. 서버와 데이터 센터
	2. 클라이언트 장치(컴퓨터, 랩톱 등)
	3. 인트라넷 및 외부 인터넷 연결
	4. 보안 및 엑세스 제어
	5. 전화 및 음성 통신
#### "nuts and bolts" view의 구성 요소
- Devices
	hosts = end systems
	Billions of connected computing devices
- Packet Switches: forward packets
	패킷 분할: 패킷 스위치는 전송할 데이터를 작은 패킷으로 분할하고 독립적으로 다른 노드로 전달한다. 
- Communication links
	fiber, copper, radio, satelite
	transmission rate: *bandwidth(대역폭)*
- Networks
	collection of devices, routers, links
- 스위치: 데이터의 목적지를 파악하여 적합현 경로로 스위칭해주는 역할을 하는 장치
- 라우터: 들어온 데이터의 정보를 확인하고 보유한 정보를 토대로 목적지까지의 적합한 경로를 찾아주는 라우팅 기능을 한다. 
- *Internet: "network of networks"*

