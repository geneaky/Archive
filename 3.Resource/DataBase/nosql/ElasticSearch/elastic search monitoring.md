#### [CAT API](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat.html)
Compact and Aligned Text(CAT) APIs

클러스터의 정보를 사람이 읽기 편한 형태로 출력하기 위한 용도로 만들어진 API

ui기반 모니터링 시스템이 원인 파악에 더 편하지만 cat API가 상황을 빠르게 판단하는데 더 도움이 되기 때문에 사용한다.

``` curl
GET /_cat/healt?v // verbose
```
- cat health
	- elasticsearch 클러스터의 전반적인 상태를 파악할 수 있다.

| header name           | meaning                                   |
| --------------------- | ----------------------------------------- |
| cluster               | 클러스터 이름                             |
| status                | 클러스터 상태                             |
| node.total            | 총 노드 수                                |
| node.data             | 데이터 노드 수                            |
| shards                | 샤드 수                                   |
| pri                   | 프라이머리 샤드 수                        |
| relo                  | 재배치가 일어나고있는 샤드 개수           |
| init                  | 초기화중인 샤드 수                        |
| unassign              | 샤드 중 아직 노드에 배치되지 않은 샤드 수 |
| pending_tasks         | pending걸린 작업 수                       |
| max_task_wait_time    | 가장 오래 대기한 태스크의 대기시간        |
| active_shrads_percent | 전체 샤드중 active상태 샤드의 비율        |
|                       |                                           |

status
- green: 프라이머리 샤드, 레플리카 샤드 모두 정상적으로 각 노드에 배치되어 동작하고 있는 상태
- yellow: 프라이머리 샤드는 정상적으로 동작하지만 일부 레플리카 샤드가 정상적으로 배치되지 않은 상태
- red: 일부 프라이머리 샤드와 레플리카 샤드가 정상적으로 배치되지 않은 상태 색인 성능, 검색 성능에 모두 영향을 주며 문서 유실이 발생할 수 있다.

``` curl
GET /_cat/nodes?v
```
- cat nodes
	- 노드들의 전반적인 상태를 확인
	- 노드들의 디스크 사용량 확인
	- 노드들이 명확한 역할을 수행하고 있는지 확인
	- 어떤 노드가 마스터 노드인지 확인
	- 노드들의 메모리 사용량 확인
	- etc...

| header name  | meaning                 |
| ------------ | ----------------------- |
| heap.percent | jvm heap mem 사용량     |
| ram.percent  | 노드 메모리 사용량      |
| cpu          | 노드 cpu 사용량         |
| load_xm      | 로드 average            |
| node.role    | 노드 역할을 약자로 표기 |
| master       | `*` 가 붙은 노드가 현재 마스터 노드                        |

기본적인 정보로 확인 가능한 정보는 제한적이기 때문에 옵션들을 추가하여 확인해야함


``` curl
GET /_cat/indices?v
```
-  cat indices
	- 인덱스의 상태를 확인
	- 인덱스들의 프라이머리 샤드 개수와 레플리카 개수 확인
	- 이상 상태인 인덱스 확인

| header name    | meaning                                                          |
| -------------- | ---------------------------------------------------------------- |
| health         | 클러스터의 상태는 인덱스의 상태로 생각할 수 있다.                |
| status         | open, close(색인, 검색 불가능 상태 인덱스)                       |
| index          | 인덱스 이름                                                      |
| pri            | 프라이머리 샤드 수                                               |
| rep            | replica 샤드 수                                                  |
| doc.count      | 문서의 수                                                        |
| store.size     | 인덱스 전체 데이터의 크기(프라이머리, 레플리카 샤드 둘 다 포 함) |
| pri.store.size | 프라이머리 샤드 기준 데이터 크기                                 |
|                |                                                                  |

``` curl
GET /_cat/shards?v
```

- cat shards
	- 샤드의 상태를 확인

| header name | meaning                     |
| ----------- | --------------------------- |
| index       | 인덱스 이름                 |
| shard       | 몇 번째 샤드인지            |
| prirep      | 프라이머리인지 레플리카인지 |
| state       | 상태(STARTED, UNASSIGNED)   |
|             |                             |


ELS System 종류
- Elastic Cloud(Kibana Stack Monitoring)
- Selft-Hosted ELS(Kibana Stack Monitoring)
- AWS OpenSearch Service(AWS CloudWatch)

#### 중접적으로 모니터링해야하는 기준 지표

지표는 임계치를 걸어 알람을 받을 목적의 지표와
문제 원인을 찾기 위해 분석용도로 사용하는 지표가 있다.

임계치를 걸어 알람 받을 목적의 지표
- CPU Usage
	- 노드가 cpu를 얼마나 많이 사용하고 있는지
	- 임계치 설정 50% 이상
- Disk Usage
	- 노드가 얼마나 많은 문서를 저장하고 있는지
	- 임계치 설정 70% 이상
- Load(부하)
	- 노드가 얼마나 많은 CPU와 디스크 `연산`을 처리하고 있는지
	- 임계치 설정 CPU개수에 따라 상이
- JVM Heap
	- 노드의 JVM이 얼마나 많은 메모리를 사용하는지
	- 임계치 설정 85% 이상
- Threads
	- 처리량을 넘어서는 색인/검색 요청이 있는지
	- Rejected Threads가 1 이상
문제 원인 분석을 위한 지표
- Memory Usage
	- 노드에 설치되어 있는 물리 메모리의 사용량으로 JVM Heap과는 다르다.
- GC Rate
	- 노드에서 발생하는 GC의 발생 주기
- GC Duration
	- 노드에서 발생하는 GC의 소요시간
- Rate
	- 노드에서 색인 및 검색 요청이 인입되는 양
- Latency
	- 노드에서 색인 및 검색에 소요되는 시간
- Disk I/O
	- 노드에서 발생하는 디스크 연산 지연 시간