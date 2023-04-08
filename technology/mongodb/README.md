#### Mongodb architecture, storage engine

`MongoDB 서버에서는 다양한 스토리지 엔진을 사용할 수 있음.`

- MMAPv1
- WiredTiger
- In-Memory
- RocksDB
- TokuDB

> MMAPv1

`MMAPv1은 MongoDB 2.6 버전까지 기본 스토리지 엔진`
`MongoDB 3.0 버전부터는 MMAPv1을 사용할 수 없음`

> WiredTiger

`WiredTiger는 MMAPv1보다 더 빠르고 안정적`
`MongoDB 3.2 버전부터 기본 스토리지 엔진`

> In-Memory

`Enterprise에서 사용할 수 있음`
`WiredTiger 스토리지 엔진의 변형, 데이터를 디스크에 기록하지 않고 메모리에만 보관`

> TokuDB

`Percona에서 개발 중인 스토리지 엔진`