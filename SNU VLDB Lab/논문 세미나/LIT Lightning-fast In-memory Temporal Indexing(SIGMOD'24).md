---
Date: 24.09.13
---
Valid 한 record를 모두 가져오는 Indexing 

## Index
1. Multi Version B-Tree 
2. time interval Index
위 두개의 index algorithm을 사용해서 temporal indexing이 가능해진다. 

==time-evolving DB에 indexing interval을 사용해서 CPU cost를 minimize시키는 것이 Goal 이다. ==

> [!NOTE]
> Time Series Data의 DB에서는 원하는 Interval을 딱 골라서 데이터를 뽑아줄 수 있어야 한다. 따라서 Compression이 없어야 한다. predicate 만족시키는 데이터를 모두 제공할 수 있게 해야한다. 


