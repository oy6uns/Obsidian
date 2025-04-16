---
Date: 24.09.13
---
## DataFusion
> 앞으로의 시대에서 개발의 트렌드가 Easily Composable 하게 하고, DB 코드를 쉽게 누구나 Customizable할 수 있게, 되는 것을 목표로 Data Fusion 연구가 진행되었다. 

query Engine을 모듈화 
개발의 편의성을 위해 시작된 프로젝트

분석 시스템을 위해 만들어진 기능
`Arrow` & `Rust`

- user가 원하는 query를 작성(Parquet, CSV, SQL, DataFrame 등,,) 
  원하는 type의 언어를 사용해서 query를 작성
	→ 이때 user는 DB안의 세부적인 코드를 모르고도 DB를 원하는 방식으로 커스텀화가 가능!


> [!Summary]
> OLAP는 단순히 분석용이기 때문에 Data Recovery, Concurrency Control 등을 신경쓰지 않아도 된다. 따라서, 이 논문의 Data Fusion도 OLTP, HTAP 타겟이 아니기 때문에 단순 OLAP에 초점을 맞춰서 간단하게만 구현했다고 한다. 

