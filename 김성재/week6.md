##  도커 스웜 실습
![](https://velog.velcdn.com/images/seongjae6751/post/aea6e0e8-e7e1-40cd-a1b6-ba34428d2012/image.png)

### 매니저 노드와 워커 노드 지정
aws의 t2.micro 인스턴스들을 생성해서 도커 스웜으로 클러스터를 구축 해볼 것이다.

먼저 사전에 도커 엔진 1.12 버전을 각 인스턴스들에 설치한 후 아래 포트들을 보안그룹에서 사용 가능한 상태로 만들면 된다.

* 2377/tcp : 클러스터 관리에 사용되는 포트
* 7946/tcp, 7946/udp : 노드 간 통신에 사용
* 4789/udp : 클러스터에서 사용되는 Ingress 오버레이 네트워크 트래픽에 사용

도커엔진에 도커 스웜이 통합되어 있어서 도커가 설치되어 있다면 아래 명령으로 스웜 모드를 바로 시작할 수 있다. 아래의 명령어는 매니저 노드로 설정할 인스턴스에 해야 한다.

``` 
docker swarm init
```
위 명령어를 치면
To add a worker to this swarm, run the following command:

```
docker swarm join --token 토큰 ip주소:2377
```
아래와 같은 안내문이 뜬다 저 안내문을 그대로 복사해서 다른 인스턴스에 복사하면 그 인스턴스가 워커 노드가 된다

이렇게만 하면 너무 간단하니까 몇가지를 더 알아보자

### 서비스 생성
```
git clone https://github.com/username/repository.git
cd repository
```
위와 같이 깃 레포지토리를 클론해서 프로젝트를 build한 다음 해당 레포지토리로 이동한 후 루트에다가 도커 파일을 작성하자

```
./gradlew build -x test
cd repository
```

``` Docker File 
# 베이스 이미지로 OpenJDK 사용
FROM openjdk:17-jdk-alpine

# JAR 파일을 /app 디렉토리로 복사
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar

# 포트 노출
EXPOSE 8080

# 애플리케이션 실행
ENTRYPOINT ["java","-jar","/app.jar"]
```

그런 다음 도커 이미지를 빌드한다.
```
docker build -t repository:latest .
```

빌드한 도커 이미지를 도커허브에서 사용할 수 잇게 도커 허브에 푸시해서 다른 노드에 사용할 수 있게 하자
```
docker tag repository:latest my-dockerhub-username/repository:latest
docker push my-dockerhub-username/srepository:latest

```
그런 다음 도커 스웜 서비스를 생성하면 된다.
```
docker service create --name roomescape-service --replicas 3 -p 8080:8080 my-dockerhub-username/spring-roomescape-playground:latest
```

### 서비스 업데이트
먼저 코드를 수정 후 다시 빌드하고
```
./gradlew build
```

도커 이미지를 재빌드 후 푸시한다
```
docker build -t spring-roomescape-playground:latest .
docker push my-dockerhub-username/spring-roomescape-playground:latest
```

스웜 서비스 롤링 업데이트를 하면 된다
```
docker service update --image my-dockerhub-username/spring-roomescape-playground:latest roomescape-service
```

### 서비스 롤백
```
docker service rollback roomescape-service
```

## 참고 자료
https://seongjin.me/docker-swarm-introduction-nodes/
