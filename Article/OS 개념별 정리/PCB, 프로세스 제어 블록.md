### PCB의 위치 및 구성

1. **위치**:
    - **주기억장치(RAM)** 에 저장됩니다. 주기억장치는 프로세스가 실행되는 동안 동적으로 변경될 수 있는 데이터를 저장하기에 적합한 장소입니다.
    - 각 프로세스마다 하나의 PCB가 할당되며, 이는 보통 **프로세스 제어 블록 리스트** 또는 **운영체제의 데이터 구조**로 관리됩니다. 이 리스트는 운영체제의 커널 공간에서 관리될 수 있습니다.
2. **구성 요소**: PCB는 프로세스의 **상태**와 관련된 중요한 정보를 포함합니다. 주요 항목들은 다음과 같습니다:
    - **프로세스 상태**: 실행 중, 준비 중, 대기 중 등 프로세스의 현재 상태.
    - **프로세스 식별자(PID)**: 각 프로세스를 고유하게 식별하는 번호.
    - **프로그램 카운터(PC)**: 다음에 실행할 명령어의 주소.
    - **CPU 레지스터 값**: 프로세스 실행 중의 레지스터 값들.
    - **메모리 관리 정보**: 베이스 레지스터, 바운드 레지스터 등 메모리 할당에 관련된 정보.
    - **I/O 상태 정보**: 프로세스가 사용하는 입출력 장치나 파일에 대한 정보.
    - **프로세스 우선순위**: 프로세스의 우선순위(스케줄링에 사용).
    - **프로세스 자원 정보**: 파일 디스크립터, 열린 파일, 자원 사용량 등.
3. **운영체제와의 상호작용**:
    - 운영체제는 **스케줄링**을 위해 PCB를 사용하여 프로세스를 관리합니다. 문맥 교환 시, 현재 프로세스의 PCB에 저장된 상태 정보를 이용해 프로세스의 실행을 중단하고, 다른 프로세스의 실행을 시작합니다.
    - 또한, 프로세스가 종료되거나 대기 상태에 있을 때, PCB는 메모리 해제와 같은 자원 관리를 위한 정보도 저장하고 있습니다.