# 도커 스웜

## 사용해야하는 이유

>하나의 호스트 머신에서 도커 엔진을 구동하다가 CPU나 메모리, 디스크 용량과 같은 자원이 부족하면 이를 어떻게 해결할까? 가장 간단한 답은 "매우 성능이 좋고 디스크 용량이 큰 좋은 서버를 새로 산다"이다. 하지만, 자원의 확장성 측면이나 비용 측면에서도 이것은 좋은 해답은 아니다. 자원이 부족할 때마다 성능이 좋은 서버를 살 수 없을 뿐더러 높은 가격의 서버를 사고 유지하는 비용 또한 무시할 수 없기 때문에 이를 해결하기 위해 ㄴ여러 가지 방법이 제안됐다. 이 중 가장 많이 사용하는 방법은 여러 대의 서버를 클러스터로 만들어 자원을 병렬로 확장시키는 것이다. 그러나! 여러 대의 서버를 하나의 자원 풀로 만드는 것은 쉬운 작업이 아니다. 새로운 서버나 컨테이너가 추가됐을 때, 이를 발견(Service Discovery)하는 작업부터 어떤 서버에 컨테이너를 할당할 것인가에 대한 스케줄러와 로드밸런서 문제, 클러스터 내의 서버가 다운됐을 때 고가용성(Hig Availability) 을 어떻게 보장할지를 해결하기 위해 도커 스웜을 사용한다.

## 특징
- 도커 스웜은 오케스트레이션도구이다. → 로드밸런싱을 할 수 있다.
- `Docker Engine 27.1`버전에서는 기본적으로 `Swarm`모드 포함되어있다.
- `Docker Engine`을 클러스터링 하기 위해 새로운 소프트웨어를 깔지 않아도 된다.

## 공식문서에 설명하는 기능
1. [Cluster management integrated with Docker Engine](https://docs.docker.com/engine/swarm/#cluster-management-integrated-with-docker-engine) -  통합된 클러스터 관리
2. [Decentralized design](https://docs.docker.com/engine/swarm/#decentralized-design) - 분산형 디자인
3. [Declarative service model](https://docs.docker.com/engine/swarm/#declarative-service-model) - 선언적 서비스 모델
4. [Scaling](https://docs.docker.com/engine/swarm/#scaling) - 스케일링
5. [Desired state reconciliation](https://docs.docker.com/engine/swarm/#desired-state-reconciliation) - 원하는 상태 조정
6. [Multi-host networking](https://docs.docker.com/engine/swarm/#multi-host-networking) - 다중 호스팅 네트워킹
7. [Service discovery](https://docs.docker.com/engine/swarm/#service-discovery) - 서비스 발견
8. [Load balancing](https://docs.docker.com/engine/swarm/#load-balancing) - 부하 분산
9. [Secure by default](https://docs.docker.com/engine/swarm/#secure-by-default) - 기본적인 보안
10. [Rolling updates](https://docs.docker.com/engine/swarm/#rolling-updates) - 롤링 업데이트

## 노드
- 하나의 서버에 하나의 노드만 존재할 수 있다.
- 매니저 노드와 워커 노드가 있다.

## 매니저 노드
- 매니저노드는 워커노드를 관리하기 위한 서버이다.
- 매니저노드에도 컨테이너를 생성할 수 있다.
- 워커노드는 0개여도 되지만 매니저노드는 최소한 1개 있어야 한다.
- 일반적으로 매니저, 워커노드를 구분해서 사용하는것을 권장한다.
- 서비스의 생성, 삭제, 수정은 매니저 노드에서만 가능하다.

## 워커노드
- 워커노드는 매니저노드의 작업을 할당받아서 실행한다.
- 직접 서비스 생성 등의 관리 작업을 수행할 수 없다.
- 기본적으로 자동으로 작업을 할당받는다.
- 제약조건, 레이블, 배치모든등을 사용해서 특정 노드에 작업을 할당할 수 있다.

## 서비스
- `Docker`의 기본 배포 단위는 `Container`이다.
- 반면 `Docker Swarm`의 기본 배포 단위는 `Service`다.
- 단일 이미지 기반으로 클러스터 안에서 구동시킬 컨테이너 묶음을 정의한 객체이다.
- 서비스(Service)는 선언적(Declarative)으로 구성된다.
	- 컨테이너로 배포할 이미지의 정보
	- 전체 서비스를 구성할 컨테이너의 수
	- 컨테이너의 배치 형태
	- 컨테이너에 붙일 볼륨 정보
	- 컨테이너를 배치시킬 노드 조건
	- 컨테이너의 업데이트 전략 및 정책 지정

### Task란?
- `Service`의 명세에 따라 생성되어 클러스터 노드에 배치되는 개별 컨테이너의 단위이다.
- `서비스명.일련번호` 형태의 이름이 붙게 된다.
- 미리 정의된 컨테이너의 수(`replicas`)만큼 생성되어 가용한 노드에 제각각 배치된다.
- 각 태스크는 `nginx:latest` 이미지 기반의 컨테이너를 1개씩 포함하여 동작한다.
>![image.png|300](https://i.imgur.com/sTGJICu.png)

## 레플리카
- 레플리카의 개수는 동일한 컨테이너의 개수이다.
- 레플리카를 여러개 만들면 도커에서 부하분산을 해준다.
- 서비스의 레플리카 수를 설정하면 도커 스웜은 해당 레플리카들을 여러 노드에 분산하여 실행한다.

## 오버레이 네트워크
- 도커 데몬 호스트들 간의 분산 네트워크를 만들어준다.
- 호스트 네트워크의 상단에 있으므로 컨테이너 간의 통신을 할 수 있다.
- 처음으로 Swarm 을 초기화 하고 호스트들을 연결하였을 때, ingress, docker_gwbridge의 네트워크가 자동으로 생성된다.

### ingress
- overlay 네트워크 드라이버 이다.
- 스웜 서비스와 관련된 데이터 트래픽을 통제한다.
- swarm 서비스의 기본 네트워크 드라이버 정보를 지정하지 않는다면 자동으로 ingress 네트워크에 연결 된다.

### docker_gwbridge
- bridge 네트워크 드라이버 이다.
- 스웜에 참여한 도커 데몬들을 연결해 주는 역할을 한다. (즉, 분산 환경에 참여한 호스트들을 연결해 준다.)

## 로드밸런싱
- 도커 스웜에서는 로드 밸런싱을 자동으로 처리한다.

### 내장 로드 밸런서
클러스터 내의 모든 노드는 서비스 디스커버리와 로드 밸런싱을 위해 내장 DNS 서버를 사용한다.

### 서비스 디스커버리
- 각 서비스가 생성될 때 도커는 해당 서비스에 대해 가상 IP(오버레이 네트워크)가 할당된다.
- 클러스터 내의 모든 노드는 이 가상 IP를 통해 서비스를 접근할 수 있다. - DNS 라운드 로빈을 통해 각 서비스의 컨테이너로 트래픽을 분산시킨다.

### 라우팅 메시지
- 라우팅 메시지를 통해 클러스터 내의 어느 노드로 들어온 요청이라도 적절한 컨테이너로 라우팅할 수 있다. 
- 클라이언트가 특정 노드를 지정할 필요 없이, 클러스터 내의 어떤 노드로도 요청을 보낼 수 있게 한다.

## 롤링배포
- 서비스를 업데이트하면 도커 스웜은 기본적으로 롤링배포를 해준다.

## 간단한 실습
### 매니저노드 초기화
>![](https://i.imgur.com/zCNO9iX.png)

### 인바운드 규칙 편집
- 도커 스웜의 매니저노드는 2377포트를 사용한다.
>![](https://i.imgur.com/ONNyufg.png)

### 워커노드 추가
>![](https://i.imgur.com/PmyzvYZ.png)

### 결과 확인
>![](https://i.imgur.com/kgyCfO0.png)

### 서비스 실행을 위한 yml작성
```yml
vversion: '3.8'

services:
  database:
    image: leehyeonsu/mysql:latest
    environment:
      - MYSQL_DATABASE=test
      - MYSQL_ROOT_HOST=root
      - MYSQL_ROOT_PASSWORD=1234
    ports:
      - 13306:3306
    volumes:
      - ./db/data:/var/lib/mysql
    networks:
      - starbucks_network_02
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
      placement:
        preferences:
          - spread: node.role
        constraints:
          - node.role == manager
          - node.role == worker

  was:
    image: leehyeonsu/infra_back-was:latest
    ports:
      - 8080:8080
    volumes:
      - ./logs:/app/logs
    networks:
      - starbucks_network_02
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
      placement:
        preferences:
          - spread: node.role
        constraints
```

## 도커 서비스 생성
>![](https://i.imgur.com/5WMoylr.png)

- 레플리카를 2개로 지정하고 서비스를 생성했는데 2개중 한개만 실행되는 오류 발생 → node가 인식 안됨 → 다음주 실습때 해결해볼 예정

