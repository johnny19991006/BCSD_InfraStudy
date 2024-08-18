이번에는 `도커 스웜` 실습이다. 
실습을 진행해봤지만 아직 잘 이해가 되지 않는다.. 
좀 더 공부를 해봐야 할 것 같다

# 포트 개방
우선 실습을 진행하기 전에 필요한 포트들을 개방해주어야 한다. 
> 
- 2377/tcp - 클러스터 관리
- 7946/tcp, 7946/udp - 노드 간 통신
- 4789/udp - Ingress 오버레이 네트워크 트래픽

`EC2` 의 보안그룹에 들어가서 저 포트들을 열어줬다

# init 
우선 도커 스웜을 활성화 시켜준다
> docker swarm init

위의 명령어를 통해서 활성화 시켜줄 수 있다

잘 적용이 됐는지 확인하기 위해 
>docker info

명령어를 통해서  
<img width="131" alt="image (15)" src="https://github.com/user-attachments/assets/41747b0f-34ce-4622-9819-30779c4eff94">

`Swarm`이 active 된 것을 확인해줄 수 있다

>docker node ls 

명령어를 통해서 현재 노드를 확인할 수 있다
<img width="781" alt="image" src="https://github.com/user-attachments/assets/19961f0a-686c-488a-ad3b-392fb40c6d67">

`Manager Status`가 Leader인 것을 보아 매니저 노드인 것을 알 수 있다
이 `Manager Status`는 매니저 노드만 상태를 가질 수 있다고 한다

# 워커 노드
이제 새로운 `EC2`를 하나 만들어서 그 `EC2`를 워커 노드로 등록해보자
그러기 위해서는 우선 매니저 노드의 토큰을 알아야 한다

> docker swarm join-token worker

<img width="288" alt="image (1)" src="https://github.com/user-attachments/assets/eb72e98d-332d-41bd-a0e6-a921e981b799">  

그러면 이런식으로 워커 노드 등록에 필요한 토큰이 나온다 
매니저 노드로 등록하고 싶으면 뒤의 worker를 manager로 바꾸면 된다

그리고 워커노드로 등록하고 싶은 `EC2`에 가서 
> docker swarm join --token <발급 받은 토큰> <매니저 노드 ip>:2377
을 입력하여 워커 노드로 등록할 수 있다

<img width="239" alt="image (2)" src="https://github.com/user-attachments/assets/ea0d53cf-7090-4ef9-bb08-78d24ed3220c">

![image (3)](https://github.com/user-attachments/assets/a2fe4093-c520-45b4-be6b-eee4866cc7e9)

그러면 이렇게 워커 노드가 된 것을 볼 수 있다

그 후에 매니저 노드로 가서 `docker node ls`를 해보면

<img width="845" alt="image (4)" src="https://github.com/user-attachments/assets/88348cdb-a19f-4fab-948f-59767f30496f">

이렇게 늘어난 것을 볼 수 있다

>docker info

# 스웜 정보

명령어를 통해서 스웜에 관한 다양한 정보들을 볼 수도 있다

<img width="161" alt="image (5)" src="https://github.com/user-attachments/assets/ad0ee3d2-f308-42db-9638-c41da8cfedfa">


간단하게 보자

- `NodeID` : 말 그대로 노드의 ID
- `Is Manager` : 매니저 노드인지 나타내는 항목 &rarr; 지금은 `true`이므로 매니저노드다
- `ClusterID` : 모든 노드가 공유하는 클러스터 ID
- `Managers` : 클러스터에 몇개의 매니저 노드가 있는지 알려줌
- `Nodes` : 클러스터에 몇개의 노드가 있는지 알려줌 &rarr; 현재 매니저 노드 1개와 워커 노드 1개로 총 2개
- `Data Path Port` : 도커 스웜이 통신에 사용하는 포트 &rarr; 위에서 실습전에 보안그룹 수정을 통해서 열어준 포트다!
- `Orchestration` : 도커 스웜에서 실행이력을 몇개까지 저장할지 설정, 잘려서 안보이는데 5개가 기본인 것 같다
- `Raft` : 스냅샷 관련 항목인 것 같다
- `Heartbeat` : 매니저 노드와 워커 노드 간의 통신을 유지하고, 노드가 활성 상태인지 확인하는 방식 &rarr; 노드 간의 일관성 유지, 장애를 빠르게 감지
- `CA Configuration` : 클러스터에서 사용되는 인증서




# 후기 

이번 실습에서는 실제 운영되는 `EC2`가 아닌 아예 새로 만든 `EC2`에서 단순히 `매니저 노드`와 `워커 노드`를 연결해보는 실습만 한 것 같다. 그러다보니 아직 도커 스웜이 어떤식으로 작동하는지, 설정하는지 에 관한 방법을 잘 모르겠다.

게다가 처음에 단일 `EC2`에서 `DInD` 방식으로 해보려다가 실패해서 급하게 바꾸느라 많이 하지 못해서 아쉽다. 
