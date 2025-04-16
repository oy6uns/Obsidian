![[IMG-20241017150447.png]]
Index를 통해 빠르게 Heap으로 full scan 을 하는 것보다 데이터에 더 빠르게 접근할 수 있다. 
![[IMG-20241017150545.png]]
- Clustered Index
	MySQL (InnoDB) → clusterd Index를 사용
- Non-clustered Index
	`PostgreSQL`, MySQL (MyISAM), SQLite
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lab/Lab 5/IMG-20250304110005-1.png]]
Heap에 저장된 각페이지에서 Tuple 데이터(record data)는 뒤에서부터 쌓이고, 각 데이터에 대응되는 line pointer들은 위에서부터 차곡차곡 쌓인다. 
![[Img/Img/SNU GSDS/1학년 1학기/BKMS1(데이터베이스)/Lab/Lab 5/IMG-20250304110005-2.png]]
Postgres는 segment 단위로 나누어지며, 기본 값으로 1GiB의 크기를 가진다. 
여러 파일로 나뉘어져서 효율적으로 데이터를 관리할 수 있도록 해준다. segment file의 크기는 조정할 수 있다. 
## PostgreSQL Index
B+ tree
Hash
Gin
Brin

각각의 index 타입의 장단점 알아보기


## CREATE TABLE
Create 할 때, Primary Key를 설정해주면, Clustered Index로 선언이 되기 때문에 b+ tree로 index가 생성된다. 
하나의 테이블에 대해 여러개의 index를 만들어줄 수도 있다. 
## CREATE INDEX, DROP INDEX
- USING hash(key_name); 을 마지막에 추가하면, 원하는 자료구조 형태로 Index가 생성된다. 
- Primary Key 에 대한 Index는 원래 지울 수 없지만, `{SQL} DROP CONSTRAINT` option을 추가해주면, primary key의 지정이 해제 되고 index도 지울 수 있게 된다. 