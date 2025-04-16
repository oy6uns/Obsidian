## #1. Triple Indirect innode
각 level의 block이 각 level에서 몇번째 block인지 정확히 계산해야 한다. 
`bn`은 데이터를 저장하는 특정 블록이다. 
`bn=0`은 파일의 첫 번째 데이터 블록, `bn=1`은 두 번째 데이터 블록을 의미한다. 

### 직접 블록과 간접 블록:
1. **직접 블록 (Direct Block)**:
    - Inode가 직접 데이터를 가리키는 블록이다. 
    - 예를 들어, 첫 12개의 블록은 직접 블록으로 관리된다. 
2. **1-level indirect block**:
    - Inode가 가리키는 블록이 다시 데이터 블록 번호를 저장한다. 
    - 1-level 블록은 **NINDIRECT (128)** 개의 블록을 가리킬 수 있다. 
3. **2-level indirect block**:
    - Inode가 가리키는 블록이 다시 여러 **1-level 블록**을 가리키고, 각 1-level 블록이 데이터를 가리킨다. 
    - 2-level 블록은 **NINDIRECT × NINDIRECT (128 × 128 = 16,384)** 개의 데이터를 관리할 수 있다. 
4. **3-level indirect block**:
    - Inode가 가리키는 블록이 여러 **2-level 블록**을 가리키고, 각 2-level 블록이 여러 **1-level 블록**을 가리키고, 최종적으로 데이터를 가리킨다. 
    - 3-level 블록은 **NINDIRECT × NINDIRECT × NINDIRECT (128 × 128 × 128 = 2,097,152)** 개의 데이터를 관리할 수 있다. 

### 각 lebel의 block이 각 lebel에서 몇번째 block인지 정확히 계산
- 2-level indirection block 번호 = `(first level block number)` * `NINDIRECT(한 블럭당 포인터 수` + `(second level block number)`
	-  (first level block number) = (2 - level indirection block 번호) / (한 block 당 포인터 수 = NINDIRECT)
	- (second level block number) = (2 - level indirection block 번호) % (한 block 당 포인터 수 = NINDIRECT)
- 3 - level indirection block 번호 = (1-lev-bn) * (NINDIRECT * NINDIRECT)+ (2-lev-bn) * (NINDIRECT) + (3-lev-bn)
        - (1-lev-bn) = (bn) / (NINDIRECT * NINDIRECT)
        - (2-lev-bn) = ((bn) % (NINDIRECT * NINDIRECT) ) / (NINDIRECT)
        - (3-lev-bn) = (bn) % (NINDIRECT)

#### 실제 계산 예시
- 특정 데이터 블록 번호 (`bn`)가 **2-level indirect block**에 속하는 경우, 해당 블록의 **1-level 블록 번호**와 **2-level 블록 번호**를 구하는 방법.
![[IMG-20250116170102.png]]
![[IMG-20250116170130.png]]
### 데이터 블록 - bmap
`bmap`은 특정 블럭 번호(bn)가 파일의 데이터 블록 중 어디에 위치하는 지 찾아주는 함수이다. 
##### 코드 예제: `bn = 200`
1. **첫 번째 계산**:
    - `bn -= NINDIRECT = 200 - 128 = 72`.
    - 2-level indirect 블록에 속함.
2. **첫 번째 레벨**:
    - `bn / NINDIRECT = 72 / 128 = 0`.
    - 첫 번째 레벨의 0번 인덱스 포인터가 두 번째 레벨 블록을 가리킴.
3. **두 번째 레벨**:
    - `bn % NINDIRECT = 72 % 128 = 72`.
    - 두 번째 레벨의 72번 인덱스 포인터가 데이터 블록을 가리킴.

### 버퍼 - bread, bwrite, brelse
-  `bread`: 디스크의 데이터 블록을 읽어와 버퍼에 복사
- **`log_write`**: 버퍼에 수정된 데이터를 기록하고, 나중에 디스크로 기록할 준비
- **`bwrite`**: 버퍼의 내용을 디스크로 바로 기록
- **`brelse`**: 버퍼 사용을 끝낸 뒤, 버퍼를 해제

