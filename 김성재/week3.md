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

### docker image와 docker container
#### docker image란?
application을 포장 및 전송하기 위해 도커는 docker image를 사용한다. docker image는 파일로 어플리케이션 실행에 필요한 독립적인 환경을 포함하며, 런타임 환경을 위한 일종의 템플릿이다.

도커 이미지는 소스 코드, 라이브러리, 종속성, 도구 및 응용 프로그램을 실행하는데 필요한 기타 파일을 포함하는 불변 파일이다.
이미지는 읽기 전용으로 스냅샷이라고도 하며, 특정 시점의 애플리케이션과 가상 환경을 나타낸다.
이러한 일관성은 도커의 큰 특징 중 하나로 개발자가 안정적이고 균일한 조건에서 소프트웨어를 테스트하고 실험할 수 있도록 한다.
 
이미지는 템플릿일 뿐이므로 시작하거나 실행할 수 없다. 컨테이너는 실행 중인 이미지일 뿐이기 때문이다.
컨테이너를 생성하면 쓰기 가능한 레이어가 immutable image(불변 이미지) 위에 추가된다. 즉, 컨테이너는 수정이 가능하다.
 
컨테이너를 생성하는 이미지 베이스는 별도로 존재하며 변경할 수 없다. 
컨테이너 환경을 실행할 때는 기본적으로 컨테이너 내부에 해당 파일 시스템(도커 이미지)의 읽기-쓰기 복사본을 만든다.
이렇게 하면 이미지 전체 복사본을 수정할 수 있는 컨테이너 레이어가 추가된다.
![](https://velog.velcdn.com/images/seongjae6751/post/1c45d9ec-3f5c-440c-80bb-1f57834e93aa/image.png)

하나의 베이스 이미지에서 도커 이미지를 무제한으로 생성할 수 있다.
이미지의 초기 상태를 변경하고 기존 상태를 저장할 때마다 추가 레이어가 있는 새 템플릿을 만든다.
 
따라서 도커 이미지는 여러 개의 레이어로 구성될 수 있으며, 각각은 다르지만 이전 레이어에서 비롯된다.
이미지 계층은 컨테이너 계층을 사용하여 가상 환경을 시작할 때 추가된 읽기 전용 파일을 나타낸다.

![](https://velog.velcdn.com/images/seongjae6751/post/250e9153-eafb-4568-b66b-190be6167423/image.png)
#### docker container란?
사용자가 기본 시스템에서 애플리케이션을 분리할 수 있는 가상화된 런타임 환경이다.
이러한 컨테이너는 응용프로그램을 빠르고 쉽게 시작할 수 있는 portable units 이다.
 
중요 기능은 컨테이너 내부에서 실행되는 컴퓨팅 환경의 표준화다. (standardization of the computing environment running inside the container.)
응용 프로그램이 동일한 환경에서 작동하도록 할 뿐 아니라 다른 사람과의 공유도 단순화한다.
 
컨테이너는 자율적(autonomous)이기 때문에 strong isolation을 제공하며 서로 방해하지 않는다. -> 격리
 
하드웨어 수준에서 가상화가 이루어지는 VM과 달리 컨테이너는 애플리케이션 계층에서 가상화된다.
하나의 머신을 활용하고 커널을 공유하며 분리된 프로세스를 실행하기 위한 운영 체제를 가상화할 수 있다.
따라서 컨테이너가 매우 가벼워져 리소스를 많이 사용하지 않을 수 있다.
![](https://velog.velcdn.com/images/seongjae6751/post/67745b87-a710-4de7-9d8a-058f44e8d9e2/image.png)


## Docker NetWork
도커 네트워크(Docker Network) 란 Docker 컨테이너 간의 통신을 관리하고 격리하기 위한 기능을 제공하는 것이다.
컨테이너화된 애플리케이션은 여러개의 컨테이너로 구성될 수 있는데, 이들 컨테이너가 서로 통신하고 데이터를 주고 받아야 할 경우가 있다.
도커 네트워크를 통해 이러한 컨테이너간 통신을 쉽게 설정하고 관리할 수 있도록 도와줍니다.
정리하자면, 같은 호스트 내에서 실행중인 컨테이너 간 연결할 수 있도록 돕는 논리적 네트워크 개념이다.

```yml
 version: '3'

services:
  front: # front container
    image: frontend
    networks:
      - mynetwork
  backend: # backend container
    image: backend
    networks:
      - mynetwork
  db: # db container
    image: mysql
    networks:
      - mynetwork
    environment:
      MYSQL_ROOT_PASSWORD: mypassword

networks:
  mynetwork:
```
위의 yml 은 Docker Compose 로 구성한 다중 컨테이너이다.
fronted,backend,db 컨테이는 모두 networks 라는 항목을 통해 mynetwork 라는 네트워크로 묶여 있습니다.
이렇게 여러 컨테이너로 구성된 애플리케이션은 같은 네트워크 내에서 통신하여 데이터를 주고받을 수 있다

## 출처
* https://www.youtube.com/watch?v=chnCcGCTyBg
* https://ebbnflow.tistory.com/200
* https://sunrise-min.tistory.com/entry/Docker-Container%EC%99%80-Image%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80
* https://velog.io/@choidongkuen/%EC%84%9C%EB%B2%84-Docker-Network-%EC%97%90-%EB%8C%80%ED%95%B4
