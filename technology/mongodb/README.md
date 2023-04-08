#### Mongodb architecture, storage engine

`MongoDB 서버에서는 다양한 스토리지 엔진을 사용할 수 있음.`

- MMAPv1
- WiredTiger
- In-Memory
- RocksDB
- TokuDB

> MMAPv1

`MMAPv1은 MongoDB 2.6 버전까지 기본 스토리지 엔진`
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