## "우아한레디스 by 강대명님" 영상 시청 및 정리

해당 글은 아래의 "우아한레디스 by 강대명님" 영상을 시청하고 정리한 글입니다.

> https://www.youtube.com/watch?v=mPB2CZiAkKM


### 1. Redis 소개

`In-Memory 데이터 구조 저장소`
`다양한 자료 구조를 지원`
`Open Source (BSD 3 License)`
`오직 1명만 커미터`

#### 1.1. Cache란 무엇인가?

`나중에 요청올 결과를 미리 저장해두는 것(빠르게 제공하기 위함)`
`Disk에 비해 Memory가 빠름`
`용량은 Disk가 Memory 보다 빠름`
`파레토 법칙, 전체 요청의 80%는 20% 사용자에 의해 발생`
`Look aside cache 패턴을 일반적으로 가장 많이 사용`


`많은 로그를 DB에 저장하는 경우 Write Back을 사용할 수 있음`
`극단적으로 큰 데이터를 저장할 경우 Wirte Back 전략을 사용할 수 있음`



> 찾아볼 키워드
>
> `Write back cache`
> `Write through cache`

#### 1.2. 왜 Collection 이 중요한가?

> 개발의 편의성 증대
> 
> `기존에 있는 기능을 활용하여 개발을 빠르게 할 수 있음`

> 개발의 난이도 감소
> 
> `자료구조가 Atomic 하기 때문에, Race Condition을 피할 수 있음`

#### 1.3. Redis 사용 처 19:13..


#### 1.4. Redis Collections

`중간에 데이터를 삽입하는 경우에는 List가 적절하지 못함`
`하나의 컬렉션에 너무 많은 아이템을 담으면 좋지 않음(1만개 이하 몇천개 수준으로 유지를 권고)`
`Expire는 개별 Item별이 아닌 전체 Collection에 설정`

#### 1.5. redis 운영

> 메모리 관리를 잘하자!
> 
> `물리적 Memory 이상을 사용하면 문제 발생`
> `Swap 이 있다면 Swap 사용으로 해당 메모리 Page 접근으로 인해 느려짐`
> `Maxmemory를 설정하더라도 더 사용할 가능성이 큼`
> `RSS 값을 모니터링 해야함`
> `많은 업체가 Swap을 쓰고 있다는 것을 모름`
> `큰 메모리를 사용하는 하나 인스턴스 보다는 적은 메모리를 사용하는 여러 인스턴스가 안전함`
> `jemalloc 버전에 따라서 다르게 동작할 수 있음`
> `60% ~ 75% 사이에 사용 비율이 나오면 장비 스펙을 향상하는 방법에 대해 고려 권고`
> `Hash -> HashTable을 하나 더 사용,  Sorted Set -> Skip List를 사용`

> O(N) 관련 명령어는 주의하자!
> 
> `단순 get/set의 경우, 초당 10만개 TPS 이상 가능(CPU에 의해 다를 수 있음)`
> `Process Input Buffer를 통해 하나의 Command가 완성되면 Process Command를 통해 처리됨`
> `긴 시간을 소모하는 명령어는 실행을 주의`
> `KEYS, FLUSHALL, FLUSHDB, Delete Collections, Get All Collections 등 사용 주의(Collection의 크기가 큰 경우 문제 발생)`
> `Spring security oauth redis token sotre 이슈가 있었음`
> `O(N), O(1)을 잘 구분해야 함`

> Redis Replication
> 
> `Async Replication이 발생할 수 있음(Replication Lag 발생 가능)`
> `slaveof 명령으로 master/slave 설정 가능`
> `DBMS로 보면 statment replication과 유사, now time 과 같은 실행으로 인해 값이 다를 수 있음`
> `Sencondary는 P`


redis 데이터 분산
redis failover
redis cluster

다루지않는 것
Redis persistence rdb aof
redis pub sub
redis stream
확률적 자료구조
  - hyperloglog
redis moudle

1) Redis 는 어떤 것이고 어떤 식으로 사용할 수 있는가?
2) Reids 기본 기능
3) Redis 운영시 장애 포인트등 잘 쓰는법