![](https://velog.velcdn.com/images/seongjae6751/post/7d69025a-3d15-43ec-a42f-9fe9dd04d444/image.png)

## 도커가 해결해주는 문제
### environment disparity(환경 불일치)
개발 환경이 운영 환경과 다를 때, 애플리케이션이 예상대로 작동하지 않는 문제를 경험할 수 있다. 예를 들어, 윈도우 컴퓨터에서 개발한 애플리케이션을 리눅스 서버에 배포할 때 환경 차이로 인해 문제가 발생할 수 있다. Docker는 이러한 문제를 해결할 수 있다. Docker를 사용하면 다양한 머신에서도 동일한 환경을 구축할 수 있기 때문이다.

### 해결 방법
![](https://velog.velcdn.com/images/seongjae6751/post/70b55e43-ca93-4fdb-8605-6e0083ed258e/image.png)

1. 일단 docker를 윈도우에 설치하고 서버에도 설치하자.
2. 그리고 docker라는 파일을 생성한후 구현하고 싶은 환경을 설정한다.(우분투, 파이썬, 깃,...)
4. 이렇게 설정한 파일을 컴퓨터와 서버에 나누어 준다. docker는 그 파일을 읽고 필요한 모든 환경을 다운로드 받게 된다. docker는 이렇게 해당 설정한 환경과 같은 가상환경 컨테이너를 컴퓨터에 만들게 된다.

이러면 윈도우에서 개발한 코드를 docker 파일과 함께 서버로 업로드를 한다면 환경이 맞지 않아 코드가 동작하지 않는 일은 없을 것이다. 이러한 docker 파일들을 깃허브 처럼 저장하고 관리 할 수 잇는 docker hub도 존재한다.

### 도커 파일 예시
```
# 베이스 이미지 설정
FROM python:3.8-slim-buster

# 작업 디렉토리 설정
WORKDIR /app

# 필요 패키지 설치
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

# 애플리케이션 복사
COPY . .

# 애플리케이션 실행
CMD ["python", "app.py"]

```
위에가 무엇인지 하나 씩 살펴보면

> FROM python:3.8-slim-buster
* 'FROM' 새로운 docker 이미지를 만들 때 기반이 되는 이미지를 지정한다.
* python3.8버전을 포함한 slim-buster라는 경량 버전을 사용한다는 뜻이다.

> WORKDIR /app

* WORKDIR: 컨테이너 내에서 작업할 디렉토리를 설정한다. 이 명령을 통해 /app 디렉토리가 생성되고, 이후의 모든 명령은 이 디렉토리 내에서 실행된다

> COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

* 'COPY requirements.txt requirements.txt': 호스트 머신(컴퓨터)의 현재 디렉토리에 있는 'requirements.txt'파일을 docker 컨테이너안으로 복사한다.
* 'RUN pip install -r requirements.txt': 파일에 명시된 python 패키지들을 설치한다.


> COPY . .

* 'COPY . .': 현재 디렉토리의 모든 파일과 폴더를 docker 컨테이너의 작업 디렉토리('/app')으로 복사한다. 즉, 호스트 머신의 애플리케이션 코드와 파일들이 모두 컨테이너 내로 복사된다.

> CMD ["python", "app.py"]
app.py 파일을 Python 인터프리터로 실행하도록 한다.

### docker의 특징
docker의 컨테이너들은 각기 분리 및 독립 적이라고 생각 할 수 있다. 이러한 특징 덕분에 한개의 서버에 각기 다른 컨테이너를 가질 수 있다. 예를 들어 딥러닝을 돌리기 위한 컨테이너, 장고로 웹 개발을 하기 위한 컨테이너 등 각기 다른 컨테이너 들이 독립적으로 존재 할 수 있다.

만약 하나의 서버 컴퓨터에 자바 앱, 장고 웹 등을 위한 여러 개의 컨테이너를 가지고 있다고 가정 할 때 갑자기 자바 앱이 인기가 많아져 사용자가 늘어나면 그냥 컨테이너의 수를 늘리면 된다. 매번 새로운 서비스를 만들때마다 새로운 서버를 사고 설정할 필요가 없다는 것이다.

### docker와 가상환경(VM)의 차이
![](https://velog.velcdn.com/images/seongjae6751/post/c41f2ad1-c4a2-4969-b580-fae8a4929ff6/image.png)
가상 컴퓨팅은 한 물리적 컴퓨터 안에서 각각 OS를 돌리는 가상 컴퓨터들이 물리적 자원을 분할해서 쓰기 때문에 성능에 한계가 생기게 된다.

![](https://velog.velcdn.com/images/seongjae6751/post/c450b439-3462-46aa-b6fa-ab0a4dc49a37/image.png)
하지만 docker는 OS단까지 내려가는 것이 아니라 실행환경만 독립적으로 돌리는 것이라 컴퓨터에 직접 요소들을 설치한 것과 별 차이 없는 성능을 낼 수 있다. 도커는 애플리케이션을 실행하기 위해 필요한 파일과 라이브러리, 의존성 등을 패키지화하여 컨테이너라는 단위로 관리한다. 컨테이너는 호스트 운영체제와 커널을 공유하고, 다른 컨테이너와는 격리된 공간에서 동작한다. 이로 인해 컨테이너는 가볍고 빠르게 실행되며, 더욱 효율적인 자원 활용이 가능하다. 또한, 컨테이너 간의 상호작용이나 컨테이너의 삭제와 같은 작업도 간편하게 수행할 수 있다

## Docker Compose
### docker-compose를 쓰는 이유
도커 컴포즈란 컨테이너 여럿을 띄우는 도커 애플리케이션을 정의하고 실행 하는 도구이다.
도커 컴포즈를 사용하면 컨테이너 실행에 필요한 옵션을 docker-compose.yml이라는 파일에 적어둘 수 있고 컨테이너 간 의존성도 관리할 수 있어서 좋다.
프론트엔드 서버, 백엔드 서버, DB서버로 구성되어 있는 웹 서비스가 있다고 하면 각 서버를 docker container로 연결하여 동작시키고 docker compose를 사용하여 해당 컨테이너들을 관리할 수 있다.

### docker-compose 파일 작성하기

#### docker compose 기본 작성법

docker compose는 docker-compose.yml 파일을 작성하여 실행할 수 있다.
docker-compose.yml 파일은 yaml 형식으로 작성해야 한다.

> #### docker compose 속성
* version
  docker compose의 파일 포맷 버전을 지정한다.
  기본적으로 버전 3을 사용하는 것이 일반적이다.
* services
  서비스의 이름
* image
  docker container의 이름을 정의한다.
  Docker Hub에 있는 이미지를 사용하여 docker container를 작성할 경우 image를 설정할 수 있다.
* restart
  docker container가 다운되었을 경우, 항상 재시작하라는 설정이다.
* volumnes
  docker run 명령의 -v 옵션과 동일한 역할을 한다.
  여러 개의 volume을 지정할 수 있으며 리스트처럼 작성하면 된다.
* environment
  dockerfile의 ENV 옵션과 동일한 역할을 한다.
  참고로, env_file 옵션으로 환경변수 값이 들어있는 파일을 읽을 수도 있다. (패스워드 등의 보안을 위한 방법)
* ports
  docker run 명령의 -p 옵션과 동일한 역할을 한다.
* build
  docker image를 Dockerfile 기반으로 작성 시 사용한다.
* depends_on
  컨테이너가 실행되는 순서를 제어
  app 안에 " depend_on : - db " 설정이 있는 경우, 우선적으로 db 컨테이너가 먼저 실행되고 그 후에 app컨테이너가 실행되어 app 컨테이너가 db 컨테이너로 접속을 시도하도록 컨테이너 실행 순서를 제어하는 것이다.

### docker-compose 명령어
```
# 컨테이너 실행 
docker-compose up -d // 도커 백그라운드 실행
docker-compose up --force-recreate // 도커 컨테이너 새로 만들기
docker-compose up --build // 도커 이미지 빌드 후 compose up
```

db, frony, back 모두 docker file을 만들고 docker-compose로 한번에 실행하면 하나의 컨테이너 안에 3개의 컨테이너가 구동되는 것을 볼 수 있다.
![](https://velog.velcdn.com/images/seongjae6751/post/db4267a0-a47f-40fe-94d3-678d834c4ec0/image.png)

# 컨테이너 내리기
docker-compose down // 컨테이너 stop & 삭제
docker-compose stop

## 출처
* https://www.youtube.com/watch?v=chnCcGCTyBg
* https://ebbnflow.tistory.com/200
* https://velog.io/@sorzzzzy/Docker-9.-%EB%8F%84%EC%BB%A4-%EC%BB%B4%ED%8F%AC%EC%A6%88%EB%9E%80
* https://velog.io/@baeyuna97/Docker-compose-%EB%9E%80
