## Docker Compose
![](https://velog.velcdn.com/images/seongjae6751/post/4c8df40c-594d-4be9-b7c2-70ee20b3788f/image.png)
### docker-compose를 쓰는 이유
도커 컴포즈란 컨테이너 여럿을 띄우는 도커 애플리케이션을 정의하고 실행 하는 도구이다.
도커 컴포즈를 사용하면 컨테이너 실행에 필요한 옵션을 docker-compose.yml이라는 파일에 적어둘 수 있고 컨테이너 간 의존성도 관리할 수 있어서 좋다.
프론트엔드 서버, 백엔드 서버, DB서버로 구성되어 있는 웹 서비스가 있다고 하면 각 서버를 docker container로 연결하여 동작시키고 docker compose를 사용하여 해당 컨테이너들을 관리할 수 있다.

### docker-compose 파일 작성하기

#### docker compose 기본 작성법

docker compose는 docker-compose.yml 파일을 작성하여 실행할 수 있다.
docker-compose.yml 파일은 yaml 형식으로 작성해야 한다. 먼저 각각이 의미 하는 것이 뭔지 보고 가자
### docker compose 속성
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

### 컨테이너 내리기
docker-compose down // 컨테이너 stop & 삭제
docker-compose stop


## 실습
이제 free tier로 가장 싼 t2.micro 인스턴스를 aws에 먼저 생성하자
그 후 접속을 하자(여기까지는 간단한거니 따로 설명을 적지 않겠다)

> docker 설치

``` bash
sudo apt-get update
sudo apt-get install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
```

> docker compose 설치

``` bash
sudo curl -L "https://github.com/docker/compose/releases/download/$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

> yml 파일 작성

이제 이번주 과제 docker로 한 인스턴스에서 mysql 두개 띄우기를 yml 파일을 작성하며 해보자
먼저 작업 디렉토리를 생성하자
```bash
mkdir ~/mysql-multi-version
cd ~/mysql-multi-version
```

그리고 compose 파일을 생성해서 작성하자

```yml
version: '3.8'  # Docker Compose 파일의 버전을 지정

services:  # 여러 서비스를 정의하는 섹션
  mysql5.7:  # 첫 번째 MySQL 서비스 이름
    image: mysql:5.7  # 사용할 Docker 이미지로 MySQL 5.7을 지정
    container_name: mysql5.7  # 컨테이너의 이름을 지정
    environment:  # 컨테이너 내에서 설정할 환경 변수를 정의
      MYSQL_ROOT_PASSWORD: password  # MySQL 루트 사용자 비밀번호
      MYSQL_DATABASE: db57  # 생성할 기본 데이터베이스 이름
    ports:  # 호스트와 컨테이너 간의 포트 매핑을 정의
      - "3307:3306"  # 호스트의 3307 포트를 컨테이너의 3306 포트로 매핑
    volumes:  # 호스트와 컨테이너 간의 데이터 볼륨을 정의
      - mysql57-data:/var/lib/mysql  # 호스트의 mysql57-data 볼륨을 컨테이너의 /var/lib/mysql 디렉토리에 매핑

  mysql8.0:  # 두 번째 MySQL 서비스의 이름
    image: mysql:8.0  # 사용할 Docker 이미지로 MySQL 8.0을 지정
    container_name: mysql8.0  # 컨테이너의 이름을 지정
    environment:  # 컨테이너 내에서 설정할 환경 변수를 정의
      MYSQL_ROOT_PASSWORD: password  # MySQL 루트 사용자 비밀번호.
      MYSQL_DATABASE: db80  # 생성할 기본 데이터베이스 이름
    ports:  # 호스트와 컨테이너 간의 포트 매핑을 정의
      - "3308:3306"  # 호스트의 3308 포트를 컨테이너의 3306 포트로 매핑
    volumes:  # 호스트와 컨테이너 간의 데이터 볼륨을 정의
      - mysql80-data:/var/lib/mysql  # 호스트의 mysql80-data 볼륨을 컨테이너의 /var/lib/mysql 디렉토리에 매핑

volumes:  # Docker 볼륨을 정의하여 컨테이너가 종료되더라도 데이터를 영구적으로 저장할 수 있게 함
  mysql57-data:  # 첫 번째 MySQL 서비스에서 사용할 볼륨
  mysql80-data:  # 두 번째 MySQL 서비스에서 사용할 볼륨

```
> docker 서비스 실행

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

> docker compose 실행

``` bash
docker-compose up -d
```

> mysql 5.7 & 8.0 접속

현재 사용자를 docker 그룹에 먼저 추가 해서 권한을 줘야 한다

```bash
sudo usermod -aG docker $USER
```
그런 다음 아래의 명령어로 접속하면 된다
```bash
docker exec -it mysql5.7 mysql -u root -p
```

```bash
docker exec -it mysql8.0 mysql -u root -p
```

이제 docker ps 명령어를 실행하면 잘 돌아감을 알 수 있다
![](https://velog.velcdn.com/images/seongjae6751/post/0f63329c-6a76-4d26-8c66-ed7d621598e3/image.png)
http://34.229.23.195:8080/time
전에 was/web 서버를 내려서 처음부터 다시 만들었기 때문에 nginx와 https 적용등은 안했다..(스터디 끝나고 인스턴스 바로 내릴 예정)
## 이미지 레이어
도커 이미지는 컨테이너를 만들기 위한 "압축파일" 개념이다.
이미지는 한번 만들어지면 이미지 내 정보는 절대 변하지 않으며 이미지를 통해 컨테이너를 만들 수 있다.
이 이미지는 컨테이너를 만들었다고 사라지지 않고, 하나의 이미지로 여러개의 컨테이너를 만들 수도 있다.

이 이미지를 만들기 위해서는 dockerfile을 작성해야 한다.(전 주차 참고)
도커 이미지는 컨테이너를 생성하기 위한 모든 정보를 갖고 있어서 수백MB~수GB가 넘는다.(VM에 비해는 상당히 작은 편)
그런데 기존 이미지에서 작은 변경사항이 생겨 도커 파일에 코드 한줄을 추가해 다시 이미지를 만들고 그 이미지를 다운로드 받는다고 가정해보자.
겨우 코드 한줄 추가했는데 이미지의 불변성 때문에 수백MB ~ 수GB가 되는 이미지를 다시 다운로드 받는 것은 매우 비효율적인 방법이다.
 
Docker는 이러한 문제를 해결하기 위해 Layer(레이어)라는 개념을 도입했다.

### Layer
레이어란 기존 이미지에 추가적인 파일이 필요할 때 다시 다운로드 받는 방법이 아닌 해당 파일을 추가하기 위한 개념이다.
![](https://velog.velcdn.com/images/seongjae6751/post/a9c08dc6-7b9e-4111-8ecd-5b8901613ea9/image.png)

위 그림은 ubuntu와 nginx는 A B C 레이어가 동일하고 nginx 레이어만 다르다.만약 ubuntu 이미지가 기존에 존재하는데 nginx 이미지를 다운 받을 경우 nginx 레이어만 다운받게 된다.
Docker 이미지는 위 그림 처럼 여러 레이어로 구성되며, 각 레이어는 이전 레이어의 변경 사항을 가지고 있다.
이 여러 개의 레이어에는 읽기 전용인 read only 레이어와, 새로 변경되거나 추가된 내용을 담은 새로운 레이어로 구성된다.
도커 이미지에 작업이 추가되면 새로운 레이어가 생성되는 이러한 개념은 Git 레포지토리에 commit 로그를 쌓는 것과 같다고 볼 수 있다.

기존에는 이미지에 변경사항이 생겨 새 이미지를 받아와야 되었다면,
이제는 레이어 개념을 통해 기존 레이어는 그대로 둔 채 새로 업데이트된 내용만 담고 있는 레이어만 쌓는 개념으로 관리를 하기 때문에 효율적이다.
업데이트된 부분만을 이미지로 생성하고 실행 시점에 기존 이미지를 바뀐 부분과 조합하기 때문에 바뀐 이미지의 크기는 큰 변화가 없다.  (Git 레포지토리에서 업데이트된 코드를 pull 받는 것과 같다.)

이미지를 실행하여 도커 컨테이너를 생성할 때도 레이어 방식을 사용한다.
컨테이너 생성 시, 기존의 읽기 전용 이미지 레이어 위에 읽기/쓰기 전용 레이어(R/W layer) 를 추가한다.
이미지 레이어를 그대로 불변의 레이어로 사용하면서, 컨테이너가 애플리케이션 실행 중에 생성하는 모든 파일이나 변경사항은 읽기/쓰기 전용 레이어에 저장되므로
여러 개의 컨테이너를 실행하면서 이미지는 불변성을 유지할 수 있다.

#### Docker 레이어의 특징

* 레이어 격리: 각 레이어는 독립적인 파일 시스템으로, 각 레이어에 애플리케이션과 종속성에 대한 변경 사항만 포함된다.
* 레이어 재사용: 동일한 레이어를 여러 이미지에 공유할 수 있다. 이를 통해 이미지 빌드 시간과 저장소 사용량을 줄일 수 있다.
* 레이어 버전 관리: 레이어를 생성할 때마다 새로운 버전이 만들어진다. 이렇게 되면, 이미지 업데이트 시에도 이전 버전의 레이어를 유지할 수 있어, 애플리케이션의 변경 이력을 관리할 수 있다.
* Dockerfile 기반 레이어 생성: Dockerfile에서 작성된 명령어들이 각각 실행될 때마다 새로운 레이어가 만들어진다. 이렇게 하여 애플리케이션과 그에 상응하는 설정, 라이브러리, 종속성 등의 변경 사항을 추적할 수 있다.

#### Docker 레이어의 작동 원리

* Docker 이미지를 생성할 때, Dockerfile을 사용하여 이미지 레시피를 정의
* Dockerfile에 기술된 명령어들이 순서대로 실행.
* 각 명령어가 실행될 때마다 새로운 레이어가 생성되고, 변경 사항이 레이어에 저장
* Docker 이미지를 실행할 때, 해당 이미지의 모든 레이어가 연결되어 전체 파일 시스템을 구성. 이렇게 하여 실행하는 컨테이너는 모든 레이어의 데이터에 액세스할 수 있다.
* 컨테이너 내에서 변경해 저장한 것은 컨테이너의 최상단 레이어에 저장되며 이미지의 레이어에는 영향을 주지 않는다.


## 출처
* https://velog.io/@sorzzzzy/Docker-9.-%EB%8F%84%EC%BB%A4-%EC%BB%B4%ED%8F%AC%EC%A6%88%EB%9E%80
* https://velog.io/@baeyuna97/Docker-compose-%EB%9E%80
