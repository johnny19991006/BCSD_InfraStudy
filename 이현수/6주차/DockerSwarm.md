# 도커 스웜 실습

## 노드 설정
### 매니저노드 초기화
![](https://i.imgur.com/zCNO9iX.png)

### 인바운드 규칙 편집
- 도커 스웜의 매니저노드는 2377포트를 사용한다.
![](https://i.imgur.com/ONNyufg.png)

### 워커노드 추가
![](https://i.imgur.com/PmyzvYZ.png)

### 결과 확인
![](https://i.imgur.com/kgyCfO0.png)

### 도커 컴포즈 설정 파일 생성
- `--bind-address=0.0.0.0`를 통해서 `Mysql`을 외부에서 접근할 수 있도록 했다.
- 워커노드에서 레플리카를 실행하도록 설정했다.
- DB가 먼저 생성되어야 was를 실행할 수 있기 때문에 `depends_on`으로 `database`가 실행이 끝나면 was컨테이너를 생성할 수 있도록 함.
- `volume`을 지정해 줬다.
```yml
    command: >
      --bind-address=0.0.0.0  # bind-address를 0.0.0.0으로 설정
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
          - node.role == worker
  was:
    image: leehyeonsu/infra_back-was:latest
    ports:
      - 8080:8080
    volumes:
      - /usr/local/INFRA_BACK/logs
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
          - node.role == worker
    depends_on:
      - database

networks:
  starbucks_network_02:
    driver: overlay

volumes:
  mysql_data:
```

## Label 설정
- 매니저 노드 라벨 지정(특정 레플리카를 매니저에 띄우기 위해서)
![](https://i.imgur.com/TAomgU1.png)
- 워커 노드 라벨 지정(특정 레플리카를 워커에 띄우기 위해서)
![](https://i.imgur.com/ukt3OkL.png)
## 도커 서비스 생성
![](https://i.imgur.com/FojUFK7.png)

## 워커 노드에 생성된 컨테이너 확인
- 서비스를 생성하면 워커노드에 컨테이너들이 생성된것을 볼 수 있다.
- 2개의 레플리카를 생성했기 때문에 `name.1`, `name.2`와 같이 생성된것을 볼 수 있다.
![](https://i.imgur.com/vnqh2Mx.png)

## 외부에서 Mysql 접속해보기
- 두 개의 레플리카중 한개는 외부의 접속을 허용했고, 나머지 한개는 외부 접속을 허용하지 않았다.![image.png|300](https://i.imgur.com/OMGhqun.png)
- 어떤 요청에 대해서는 차단되고, 어떤 요청은 성공하는것을 볼 수 있다. → 로드밸런싱이 되고있다.
  ![](https://i.imgur.com/jvfSxdO.png)![](https://i.imgur.com/Yahwu5k.png)
## 네트워크 확인
- `서비스 이름` + `네트워크 이름`으로 네트워크가 생성된것을 볼 수 있다.
![](https://i.imgur.com/EuS9WHe.png)

## volume 설정
- `volume`을 호스트 경로로 지정하면 실행되는 노드마다 같은 경로가 있어야 하기 때문에 도커를 이용한 `volume`경로를 생성해준다.  ![](https://i.imgur.com/IrBlaCl.png)
- 생성 결과 확인
  ![image.png|500](https://i.imgur.com/3e33UYW.png)
- 워커노드의 `Mysql`컨테이너에서 `volume` `mount`된것을 확인
  ![](https://i.imgur.com/2wHPWob.png)

## 삽질..
- Mysql을 외부에서 접근해보고 싶었다.
- 외부에서 접근하기 위해서는 `my.cnf`파일과 `Mysql`유저의 접근권한을 열어줘야 한다.
- 컨테이너에 접속해서 설정해도 되지만, 서비스를 업데이트하게 되면 설정이 초기화 되기 때문에 `compose yml`을 이용해서 `volume`으로 설정을 마운트 하고 싶었다.
- 처음에는 `/usr/local/INFRA_BACK/my.cnf:/etc/my.cnf`와 같이 절대경로로 지정해줬다.
- 하지만 위와 같은 방법을 사용하게되면 해당 컨테이너가 실행되는 노드에도 경로가 있어야 한다는 사실을 삽질하다가 알게되었다.(당연히 서비스를 생성한 노드의 경로인줄)
- `Docker Volume`이라는 것은 노드간의 경로가 다른 문제를 해결할 수 있다는 것을 알게 됐다.