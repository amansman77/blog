# MariaDB Galera 클러스터에서 rsync가 SST로 동작하는 원리

안녕하세요. yeTi입니다.

오늘은 MariaDB Galera 클러스터에서 사용되는 SST(State Snapshot Transfer) 방식 중 하나인 `rsync`에 대해 이야기해보려 합니다. 특히 `rsync`가 SST로 선택된 이유와 그 특성을 깊이 있게 살펴보면서, 실제 운영 환경에서 고려해야 할 점들을 함께 알아보겠습니다.

## SST와 rsync의 만남

데이터베이스 클러스터링에서는 새로운 노드가 합류할 때 기존 데이터를 복사하는 과정이 필수적입니다. Galera 클러스터에서는 이 과정을 SST라고 부르며, 그 방법 중 하나로 Linux의 파일 동기화 도구인 `rsync`를 선택했습니다.

왜 `rsync`일까요? 이는 데이터베이스의 근본적인 특성과 관련이 있습니다. 데이터베이스는 결국 파일 시스템에 데이터를 저장합니다. 데이터 파일, 로그 파일, 설정 파일 등이 모두 물리적 파일로 존재하며, 이들은 대부분 특정 디렉터리(`/var/lib/mysql/` 등)에 모여 있습니다. `rsync`는 이러한 파일 기반 동기화에 최적화된 도구이기에 자연스러운 선택이었던 것입니다.

## rsync의 장단점 살펴보기

### 장점: 단순함과 효율성
`rsync`의 가장 큰 장점은 구성의 단순함입니다. 대부분의 Linux 시스템에 기본으로 설치되어 있으며, 설정도 간단합니다. 또한 델타 전송이라는 강력한 기능을 통해 변경된 부분만 전송할 수 있어 네트워크 대역폭을 효율적으로 사용할 수 있습니다.

### 단점: 서비스 영향과 확장성 한계
그러나 `rsync`는 중요한 한계도 가지고 있습니다. SST 과정에서 Donor 노드의 쓰기 작업을 중단해야 하며, 이는 서비스 가용성에 직접적인 영향을 미칩니다. 또한 데이터베이스 크기가 커지거나 클러스터 노드 수가 증가하면, 이러한 제약은 더욱 심각한 문제가 될 수 있습니다.

## 언제 rsync를 사용해야 할까?

`rsync`는 모든 상황에 적합한 것은 아닙니다. 다음과 같은 기준으로 SST 방식을 선택하는 것이 좋습니다:

- **데이터베이스 크기**: 10GB 미만의 소규모 데이터베이스
- **클러스터 규모**: 2-3개의 소규모 클러스터
- **서비스 특성**: 쓰기 작업이 빈번하지 않은 서비스
- **운영 환경**: 테스트 환경이나 개발 단계

반대로 다음과 같은 경우에는 `mariabackup`과 같은 대안을 고려해야 합니다:
- 대규모 데이터베이스 (10GB 이상)
- 3개 이상의 노드로 구성된 클러스터
- 고가용성이 중요한 프로덕션 환경

## rsync의 효율성: 오해와 진실

`rsync`의 델타 전송 기능은 매력적으로 보이지만, SST 상황에서는 그 장점이 제한적입니다. 특히 초기 노드 합류 시에는 전체 데이터를 복사해야 하므로 델타 전송의 이점을 전혀 활용할 수 없습니다.

### Re-Join 상황의 이해: SST vs IST

노드가 일시적으로 클러스터에서 이탈했다가 다시 합류하는 Re-Join 상황에서는 두 가지 동기화 방식이 가능합니다:

1. **IST (Incremental State Transfer)**:
   - 노드가 짧은 시간 동안만 이탈했고, 필요한 트랜잭션 이력이 클러스터에 보존되어 있는 경우
   - 누락된 트랜잭션만 전송하여 빠르게 동기화
   - 서비스 중단 없이 동기화 가능

2. **SST (State Snapshot Transfer)**:
   - 노드가 오랫동안 이탈했거나, 필요한 트랜잭션 이력이 클러스터에서 삭제된 경우
   - `rsync`를 통한 전체 데이터 복사 필요

Galera 클러스터는 가능한 경우 항상 IST를 우선적으로 시도합니다. IST는 최소한의 데이터만 전송하므로 더 효율적이고 빠르며, 서비스 중단도 발생하지 않기 때문입니다. `rsync`를 통한 SST는 IST가 불가능한 경우의 마지막 수단으로 사용됩니다.

## 마치며

`rsync`는 MariaDB Galera 클러스터의 SST 도구로서 단순하고 효과적인 선택입니다. 그러나 이는 모든 상황에 적합한 만능 해결책이 아니며, 클러스터의 규모와 운영 환경에 따라 신중하게 선택해야 합니다.

특히 프로덕션 환경에서는 `rsync`의 한계를 명확히 인지하고, 필요한 경우 `mariabackup`과 같은 대안을 고려하는 것이 중요합니다. 데이터베이스 클러스터링에서 SST 방식의 선택은 단순한 기술적 결정을 넘어, 서비스의 안정성과 직결되는 중요한 아키텍처 결정이기 때문입니다.

## 참고자료
- MariaDB Documentation: Galera Cluster SST Methods
  - [State Snapshot Transfer Methods](https://mariadb.com/kb/en/introduction-to-state-snapshot-transfers-ssts/)
  - [Galera Cluster System Variables](https://mariadb.com/kb/en/library/galera-cluster-system-variables/)
- rsync Documentation
  - [rsync Manual Page](https://download.samba.org/pub/rsync/rsync.html)
  - [rsync Algorithm Technical Report](https://rsync.samba.org/tech_report/)
- Galera Cluster Documentation
  - [Node Provisioning](https://galeracluster.com/library/documentation/node-provisioning.html)
  - [State Transfer](https://galeracluster.com/library/documentation/state-transfer.html)
