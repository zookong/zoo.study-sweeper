#### Mongodb architecture, storage engine

`MongoDB 서버에서는 다양한 스토리지 엔진을 사용할 수 있음.`

MMAPv1 

- MMAPv1
- WiredTiger
- In-Memory
- RocksDB
- TokuDB

> MMAPv1

`MongoDB 4.0 버전부터는 Deprecated 되었음`
`MongoDB 4.2 버전부터는 옵션에서 사라짐`

> WiredTiger

`WiredTiger는 MMAPv1보다 더 빠르고 안정적`
`MongoDB 3.2 버전부터 기본 스토리지 엔진`

> In-Memory

`Enterprise에서 사용할 수 있음`
`WiredTiger 스토리지 엔진의 변형, 데이터를 디스크에 기록하지 않고 메모리에만 보관`

> TokuDB

`Percona에서 개발 중인 스토리지 엔진`

> storage engine 혼합 사용

`하나의 인스턴스에서 동시에 여러 스토리지 엔진 사용이 불가`
`인스턴스만 다르면 스토리지 엔진을 선택에 있어 제약은 없음`
`primary와 secondary는 In-Memory로 구성, 백업 용도는 데이터를 기록하도록 WiredTiger로 구성하여 사용 가능`
`각 레플리카 셋을 다른 스토리지 엔진으로 구성해서 특성에 맞게 엔진을 선택 가능`
`효율성이 떨어지거나 관리가 번거로워질 수 있으므로 많은 고려가 필요`

#### Storage engine, MMAPv1

`v3.0까지 주로 사용되던 스토리지 엔진`
`v2.6까지 데이터 베이스의 단위의 잠금을 사용, DML 문장의 동시 처리 성능이 좋지 않음`
`v3.0부터는 컬렉션의 단위의 잠금을 사용, DML 문장의 동시 처리 성능이 좋아짐, 그러나 컬렉션 수준의 잠금 또한 동시성 처리에 많음 걸림돌이`
`자체적으로 내장된 캐시 기능이 없어서 운영체제의 캐시를 활용`
`리눅스, 윈도우의 캐시를 사용하기 때문에 커널이 제공하는 시스템 콜을 거치며 오버헤드가 상대적으로 큼`
`운영체제의 캐시 기능은 자주 사용되는 페이지의 관리, 더티 페이지의  관리가 데이터베이스보다는 안정적이지 못함`

> MMAPv1 엔진 설정

`내장된 공유 캐시가 없고 운영체제(리눅스)의 페이지 캐시를 사용하므로 성능 튜닝 옵션이 거의 없음`

대한민국

```yaml
...
storage:
  dbPath: /data/db
  indexBuildRetry: true
  repairPath: /data/db/diagnostic.data
  directoryPerDB: true
  syncPeriodSecs: 60
  jor
  journal:
    enabled: true
    commitIntervalMs: 100
  
  engine: mmapv1 # 엔진을 MMAPv1 설정, MongoDB 서버의 기본 공통 설정 포맷

  mma
  mmapv1:
    prellocDataFiles: true
    nsSize: 16
    quota:
      enabled: false
      maxFilesPerDB: 8
    smallFiles: true
...
```

