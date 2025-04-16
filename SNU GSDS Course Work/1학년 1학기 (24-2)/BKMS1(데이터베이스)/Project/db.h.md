- 데이터베이스 관리 시스템(DBMS)에서 데이터를 삽입하고 삭제하며, 키 값으로 레코드를 검색하고 관리하는 기능들을 구현한 헤더 파일
- 데이터베이스 파일을 열고, 데이터베이스 트리를 조작하여 데이터를 관리할 수 있게 함

```cpp
#define MIN_VALUE_SIZE 50 
#define MAX_VALUE_SIZE 112 
#define THRESHOLD 2500
```
## 데이터베이스 관리 API(Disk Management)
1. `open_table`
2. `db_insert`
3. `db_find`
4. `db_delete`
5. `db_scan`
6. `init_db`
7. `shutdown_db`
## 삽입 관련 함수
1. `insert_into_leaf`
       - **설명**: 특정 테이블에서 리프 노드에 새로운 키와 값을 삽입하는 함수입니다. 트리의 리프 노드에 공간이 충분한 경우 데이터를 바로 삽입합니다.
    - **매개변수**:
        - `table_id`: 데이터를 삽입할 테이블의 식별자.
        - `leaf_buf`: 리프 노드 버퍼.
        - `key`: 삽입할 레코드의 키.
        - `value`: 삽입할 값.
        - `val_size`: 값의 크기.
```cpp
int insert_into_leaf(int64_t table_id, buf_descriptor_t *leaf_buf,
db_key_t key, const char* value, uint16_t val_size);
```

2. **`insert_into_leaf_after_splitting`**
       - **설명**: 리프 노드에 공간이 부족할 때, 노드를 분할한 후 키와 값을 삽입하는 함수입니다. 새로운 노드를 생성하고 데이터를 분산시킨 후 삽입합니다.
    - **매개변수**:
        - `table_id`: 데이터를 삽입할 테이블의 식별자.
        - `leaf_buf`: 리프 노드 버퍼.
        - `key`: 삽입할 레코드의 키.
        - `value`: 삽입할 값.
        - `val_size`: 값의 크기.
```cpp
int insert_into_leaf_after_splitting(int64_t table_id, buf_descriptor_t* leaf_buf, db_key_t key, const char* value, uint16_t val_size);
```
3. **`insert_into_node`**
       - **설명**: 부모 노드에 새로운 키와 포인터(페이지 번호)를 삽입하는 함수입니다. 이는 리프 노드가 아닌 내부 노드에 삽입이 필요할 때 호출됩니다.
    - **매개변수**:
        - `table_id`: 작업할 테이블의 식별자.
        - `parent_buf`: 부모 노드의 버퍼.
        - `right_index`: 삽입할 위치의 인덱스.
        - `key`: 삽입할 키 값.
        - `right_num`: 오른쪽 자식의 페이지 번호.
```cpp icon
int insert_into_node(int64_t table_id, buf_descriptor_t* parent_buf, int right_index, uint64_t key, pagenum_t right_num);
```
4. `insert_into_node_after_splitting`
       - **설명**: 부모 노드에 공간이 부족할 때, 노드를 분할한 후 키와 포인터를 삽입하는 함수입니다.
    - **매개변수**:
        - `table_id`: 작업할 테이블의 식별자.
        - `parent_buf`: 부모 노드의 버퍼.
        - `right_index`: 삽입할 위치의 인덱스.
        - `key`: 삽입할 키 값.
        - `right_num`: 오른쪽 자식의 페이지 번호.
```cpp
int insert_into_node_after_splitting(int64_t table_id, buf_descriptor_t *parent_buf, int right_index, uint64_t key, pagenum_t right_num);
```
5. `insert_into_parent`
       - **설명**: 두 자식 노드를 부모 노드에 연결하고, 해당 위치에 키를 삽입하는 함수입니다.
    - **매개변수**:
        - `table_id`: 작업할 테이블의 식별자.
        - `left_buf`: 왼쪽 자식 노드 버퍼.
        - `key`: 부모 노드에 삽입할 키 값.
        - `right_buf`: 오른쪽 자식 노드 버퍼.
```cpp
int insert_into_parent(int64_t table_id, buf_descriptor_t *left_buf, uint64_t key, buf_descriptor_t *right_buf);
```
6. `insert_into_new_root`
       - **설명**: 새로운 루트 노드를 생성하고, 두 자식 노드를 루트 아래에 배치하는 함수입니다.
    - **매개변수**:
        - `table_id`: 작업할 테이블의 식별자.
        - `left_buf`: 왼쪽 자식 노드 버퍼.
        - `key`: 루트 노드에 삽입할 키 값.
        - `right_buf`: 오른쪽 자식 노드 버퍼.
```cpp
int insert_into_new_root(int64_t table_id, buf_descriptor_t *left_buf, uint64_t key, buf_descriptor_t *right_buf);
```
7. `start_new_tree`
       - **설명**: 새로운 트리를 생성하는 함수로, 처음으로 데이터를 삽입할 때 호출됩니다. 이 함수는 새로운 루트와 리프 노드를 생성합니다.
    - **매개변수**:
        - `table_id`: 작업할 테이블의 식별자.
        - `key`: 첫 번째 삽입할 레코드의 키.
        - `value`: 삽입할 값.
        - `val_size`: 값의 크기.
```cpp
int start_new_tree(int64_t table_id, db_key_t key, const char *value, uint16_t val_size);
```

## 삭제 관련 함수
1. `remove_entry_from_page`
       - **설명**: 특정 테이블의 페이지에서 주어진 키에 해당하는 데이터를 제거하는 함수입니다.
    - **매개변수**:
        - `table_id`: 작업할 테이블의 식별자.
        - `buf`: 삭제할 페이지의 버퍼.
        - `key`: 제거할 레코드의 키.
```cpp
int remove_entry_from_page(int64_t table_id, buf_descriptor_t* buf, uint64_t key);
```
2. `adjust_root`
       - **설명**: 루트 노드를 조정하는 함수로, 트리에서 삭제가 발생한 후 루트 노드가 필요 없는 경우 루트를 조정합니다.
    - **매개변수**:
        - `table_id`: 작업할 테이블의 식별자.
        - `root_buf`: 루트 노드의 버퍼.
```cpp
`int adjust_root(int64_t table_id, buf_descriptor_t* root_buf);`
```
3. `coalesce_nodes`
       - **설명**: 노드 병합을 통해 두 노드를 하나로 합치는 함수입니다. 노드 삭제 후 병합이 필요한 경우에 호출됩니다.
    - **매개변수**:
        - `table_id`: 작업할 테이블의 식별자.
        - `buf`: 현재 노드의 버퍼.
        - `neighbor_buf`: 병합할 이웃 노드의 버퍼.
        - `parent_buf`: 부모 노드의 버퍼.
        - `neighbor_index`: 이웃 노드의 인덱스.
        - `k_prime`: 부모 노드에 있는 키 값.
```cpp
int coalesce_nodes(int64_t table_id, buf_descriptor_t *buf, buf_descriptor_t *neighbor_buf, buf_descriptor_t *parent_buf, int neighbor_index, int k_prime);
```
4. `redistribute_nodes`
       - **설명**: 병합 대신 노드 간 데이터를 재분배하는 함수입니다. 이웃 노드에서 데이터를 빌려오는 방식으로 작업이 이루어집니다.
    - **매개변수**:
        - `table_id`: 작업할 테이블의 식별자.
        - `buf`: 현재 노드의 버퍼.
        - `neighbor_buf`: 재분배할 이웃 노드의 버퍼.
        - `parent_buf`: 부모 노드의 버퍼.
        - `neighbor_index`: 이웃 노드의 인덱스.
        - `k_prime_index`: 부모 노드에 있는 키의 인덱스.
        - `k_prime`: 부모 노드에 있는 키 값.
```cpp
int redistribute_nodes(int64_t table_id, buf_descriptor_t *buf, buf_descriptor_t *neighbor_buf, buf_descriptor_t *parent_buf, int neighbor_index, int k_prime_index, int k_prime);
```
5. `delete_entry`
    - **설명**: 특정 테이블에서 주어진 키에 해당하는 레코드를 삭제하는 함수입니다.
    - **매개변수**:
        - `table_id`: 작업할 테이블의 식별자.
        - `buf`: 삭제할 페이지의 버퍼.
        - `key`: 삭제할 레코드의 키.
```cpp
int delete_entry(int64_t table_id, buf_descriptor_t* buf, uint64_t key);
```




