## Redis

`Redis의 스레드 구조`
`Redis AOF와 RDB의 차이 및 장단점`
`Redis Del를 이용한 동시성 처리`

DEL hash-key 
-  O(1)이다. N은 삭제할 key의 개수이다.

HDEL key field [field ...]
- O(N)이다. N은 삭제할 field의 개수이

## 생각해보기

### 캐시화 전략

> Look Aside Cache
> Read Through

> Write Around
> Write Through
> Write Back

### 로컬 캐시 vs 글로벌 캐시

#### 로컬 캐시와 글로벌 캐시의 특징

> 로컬 캐시

`애플리케이션의 한 노드 또는 프로세스 내에서 사용되는 캐시`
`서버마다 캐시를 따로 저장`
`서버 내에서 작동`
`로컬 서버 장비의 Resource를 이용`
`동일 서버이기에 속도가 빠름`

> 글로벌 캐시

`분산 캐시라고도 부름`
`여러 서버에서 캐시 서버에 접근`
`별도의 캐시 서버를 이용`
`다양한 서버 간에 데이터를 공유`
`서버 간 데이터 공유가 쉬움`
`로컬 캐시보다는 느림`

#### 로컬 캐시와 글로벌 캐시의 데이터 동기화

> 로컬 캐시

`모든 peer 노드에서 변경 사항 전달 필요`

> 글로벌 캐시

`단일 지점의 데이터이기에 모든 peer에게 변경 사항을 전달이 불필요`

#### Redis 이외의 다른 캐시 기술

`Redis는 가장 유명`

> Amazon ElastiCache

> Memcached

`단순성 및 사용 편의성이 높음`
`Redis에 비에 데이터 타입이 제한`

```
// Redis vs Memcached
// TODO : 아래의 링크를 읽고 차이점 더 정리해보기
https://www.javatpoint.com/redis-vs-memcached
https://aws.amazon.com/ko/elasticache/redis-vs-memcached/
https://www.imaginarycloud.com/blog/redis-vs-memcached/
```

> Hazelcast

> Apache Ignite

> Ehcache

`Java 기반 캐시`
`간단하게 사용 가능`
`로컬 캐시만 지원`
`속도가 빠르고 경량`
`단일 서버에 적합하고 Redis에 비해 복잡성이 낮음`

```
// 분산 캐시 관련 내용
https://www.ehcache.org/documentation/3.4/clustered-cache.html
```

> Caffeine

`로컬 캐시로만 사용이 가능`

> Simple

`Spring Boot에서 다른 캐시 provider를 못찾을 경우 사용`
`만약 캐시 라이브러리가 어플리케이션에 없을 경우 기본 값`
`ConcurrentHashMap를 사용한 간단한 구현`