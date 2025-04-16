MVCC: 들어있는 record를 physical 하게 어떻게 관리할거냐에 관한 얘기
각 record마다 데이터 변경 이력이 담긴 chain 이 존재한다. 
MVCC를 위해서 결국에 `Search Structure`가 필요하다. 
→ `Version Search Index`를 통해 원하는 시점의 데이터에 접근할 수 있게 해주어야 한다. 

![[IMG-20241127131146.png]]
결국에, 현재 실행되고 있는 transaction이 필요한 record는 A_48과, A_99이다. 
중간에 A_50 ~ A_97의 record는 모두 쓰레기 record이기에 제거해주어야 한다. 
## p. 10
- T1 기준에서는 1보다 작은 버전인 A0를 읽으면 되기 때문에 123값을 가져온다. 
## p. 11
- p. 10 ~ 20 까지의 예제는 Serializable하지 않다. 그저 동작 하는 과정을 보여주기 위한 예이다. 

## p. 26 Version Stroage
### Append-Only 
in-row versioning
### Time-Travel
off-row versioning
### Delta
off-row versioning인데, 변화량만을 새로운 storage에 저장한다. 
변화량만을 저장하기 때문에 **공간효율성은 높아**지지만, 
현재 record를 찾기 위해 **모든 변화 과정을 거슬러올라가며 recovery하는 overhead**가 생긴다. 

Delta는 


