# 01 System Data, Configuration
## Postgres GUC Parameter
### #1. Using Alter System
![[IMG-20240930123356.png]]
### #2. Edit parameter directly 
![[IMG-20240930123401.png]]
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lab/Lab 2/IMG-20250304110005-1.png]]
# 02 Sysbench
## Workload
데이터 베이스 별로 원하는 작업을 수행하는 일련의 과정을 workload라고 한다. ![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lab/Lab 2/IMG-20250304110005-2.png]]
## Process
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lab/Lab 2/IMG-20250304110007.png]]
### 유저 생성 및 권한 부여
1. `{sql} CREATE USER sbtest WITH PASSWORD 'sbtest';`
2. `{sql} GRANT ALL PRIVILEGES ON DATABASE postgres TO sbtest;`
3. `{sql} ALTER DATABASE postgres OWNER TO sbtest;`

### sysbench를 통해 평가 진행
1. `{shell} ./sysbench.sh --prepare`
2. `{shell} ./sysbench.sh --run`
 생성한 table들을 모두 지워주는 작업
 3. `{shell} ./sysbench.sh --cleanup`

