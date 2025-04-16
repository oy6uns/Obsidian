## Network Management?

수천개의 하드웨어/소프트웨어가 상호작용하는 네트워크를 관리하기 위함.

- 모니터링, 설정, 제어 등이 포함
    - 합리적인 비용으로 실시간으로 운영, 성능 확인 및 서비스 요구사항을 충족할 수 있게 해줌.

## Components of network management

: 네트워크 관리의 구성요소

1. Managing server : 관리 서버
2. Network management protocol : 프로토콜을 통한 서버 ↔ 클라이언트 통신 및 관리
3. Managed device : 소프트웨어 및 하드웨어
4. Data : device “state”, configuration data, operational data 등을 포함

## Network Operator approaches to management

: 네트워크 장치간의 데이터 교환과 관리를 표준화

- CLI
    
    - 명령어를 통해 각 device에 직접적으로 접근 가능하다.
- SNMP / MIB
    
    Simple Network Management Protocol (SNMP) UDP/IP를 사용하여 네트워크를 관리한다.
    
    MIB(Management Information Base)의 관리 정보 베이스로부터 데이터를 가져와서 사용한다.
    
- NETCONF / YANG
    
    가장 표준적으로 많이 쓰이며 전체 네트워크 환경을 관리한다.
    
    <aside> 💡 YANG 언어로 쓰여진 XML 기반의 데이터 형식을 통해 네트워크 환경을 관리한다. → 데이터 모델링 및 데이터 계층 구성 관리 측면에서 더 효과적이라고 평가 받는다.
    
    </aside>
    

## SNMP protocol

MIB는 정보를 전송하고, 명령을 전달하는 데 2가지 주요 방식을 제공한다.

1. Request/Respons mode
    
    관리자가 클라이언트(agent)에게 MIB 정보나 명령을 요청 한 뒤 response를 받는다.
    
2. Trap mode
    
    관리자가 주기적으로 MIB 정보를 요청하지 않고도 이벤트에 대한 경고 또는 알림을 받을 수 있다.
    
![[SNMP protocols의 메시지 타입.png]]
<aside> ✅ setRequest는 잘 사용되지 않는다! (굳이,,? 다른 좋은 tool들이 많음)

</aside>
![[SNMP protocols의 메시지 형식.png]]
## SNMP: Management Information Base (MIB)

- MIB는 device의 작동 및 구성 데이터를 관리한다.
    
    - 수집된 장치는 MIB 모듈로 구성되어 있다.
    - RFC(Requests for Comments) 문서에 400개 이상의 모듈이 정의되어 있다.
- SMI(Structure of Management Information
    
    데이터 모델링 및 정보 정의를 위한 규칙과 구조를 제공한다.
    
    MIB의 구조와 변수의 데이터 유형, 이름, OID, 설명, 범위 등을 정의한다.
    
    c.f.) example MIB variables for UDP protocol
    ![[example MIB variables for UDP protocol.png]]
## NETCONF

목표: 네트워크 전체의 장치를 적극적으로 관리하고 구성하는 것이다.

- 서버 ↔ network devices 사이에서 작동한다.
    - retrieve, set, modify, activate configurations
    - 여러 장치에 대한 작업을 쪼개서 커밋하기에 설정 변경이 중간에 실패해도 모든 장치에 적용되지 않는다.
    - 장치들의 운영 데이터와 통계 정보를 조회 가능하다.
    - 장치에서 발생하는 이벤트와 알림을 관리자가 구독하고 수신 가능하다.
- RPC(Remote Procedure Call) paradigm
    - NETCONF 메시지는 XML 형식으로 인코딩된다.
        - TLS와 같이 안전하고 신뢰할 수 있는 전송 프로토콜로 변환된다.
![[NETCONF 동작원리 및 operations.png]]
## NETCONF: YANG

데이터 모델링 언어: 네트워크 장치의 구성, 상태 및 운영 데이터와 관련된 정보를 표현하는데 사용된다.

- 내장 데이터 타입이다. like SMI
    
- YANG을 통해 디바이스 및 기능에 대한 XML 문서를 생성할 수 있다.

    ![[NETCON에서 YANG 동작 원리.png]]
- YANG을 사용하면 NETCONF 구성이 올바르고 일관된 제약조건을 충족하는지 확인할 수 있다.
    

## SNMP vs NETCONF

1. **목적**:
    - SNMP: SNMP는 주로 네트워크 장치 및 서버의 모니터링 및 데이터 수집에 사용된다. 주로 운영 데이터를 검색하고 알림을 수신하기 위한 목적으로 설계되었다.
    - NETCONF: NETCONF는 네트워크 장치의 구성을 관리하고 구성 변경을 수행하기 위한 목적으로 설계되었다. 주로 구성 데이터를 변경하고 구성 데이터를 읽는 데 사용된다.
2. **프로토콜 유형**:
    - SNMP: SNMP는 Point-to-Point 프로토콜로, 주로 읽기 및 감시 작업에 사용된다.
    - NETCONF: NETCONF는 클라이언트-서버 모델을 따르며, 읽기와 쓰기 모두에 사용된다. 따라서 구성 변경 및 관리가 가능하다.
3. **데이터 모델링**:
    - SNMP: SNMP는 MIB (Management Information Base)을 사용하여 데이터 모델링을 수행한다. MIB는 계층 구조를 가진 정적 데이터 모델을 사용한다.
    - NETCONF: NETCONF는 YANG을 사용하여 데이터 모델링을 수행한다. YANG은 더 동적이고 유연한 데이터 모델링을 제공하며 XML 형식을 사용한다.