Docker를 이용해 하나의 서버에서 두 버전의 MySql을 사용해보자.

# 도커
## 도커로 여러 컨테이너 띄우기
### 도커 설치
1. __도커 설치__
  ```java
  $ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
  ```

2. __설치 확인__
  ```java
  $ sudo docker run hello-world
  ```
  아래 문구가 뜨면 성공적으로 설치된 것이다.
<div style="margin: 0; padding: 0;"><img src = https://velog.velcdn.com/images/choon0414/post/ddcbcbb8-06b7-43a4-bb84-052f95c154b5/image.png></div>

### 도커 실행
1. 도커 이미지 생성
	```java
    // 두 버전의 이미지 생성
    $ docker pull mysql:5.7
    $ docker pull mysql:8.0
    ```
2. 도커 컨테이너 실행
  ```java
  // mysql5.7은 3306포트로, mysql8.0은 3307포트로 연결되도록 설정
  $ docker run --name mysql5.7 -e MYSQL_ROOT_PASSWORD=[password] -d -p 3306:3306 mysql:5.7
  $ docker run --name mysql8.0 -e MYSQL_ROOT_PASSWORD=[password] -d -p 3307:3306 mysql:8.0
  ```
3. 실행 중인 컨테이너 확인
  ```java
  // 모든 컨테이너 확인
  $ docker ps -a

  // 실행중인 컨테이너 확인
  $ docker ps
  ```
<div style="margin: 0; padding: 0;"><img src = https://velog.velcdn.com/images/choon0414/post/8fa0a0f2-e723-4a6e-8068-44a557c4f5b0/image.png></div>
mysql8.0 버전은 3307 포트, mysql5.7 버전은 3306 포트로 잘 띄워져 있다.
<br>

### yml 파일 수정
1. 웹 서버의 어플리케이션 yml에서 연결되어있는 DB 정보를 수정한다.
  ```java
  src/main/resources$ vim application.properties
  ```
  ```
  // 설정한 도커 컨테이너의 mysql 정보대로 수정
  spring.datasource.url=jdbc:mysql://[DB서버 IP주소]:3307/escaperoom
  spring.datasource.username=root
  spring.datasource.password=[password]
  spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
  ```
<div style="margin: 0; padding: 0;"><img src = https://velog.velcdn.com/images/choon0414/post/258c602e-ea3d-44f2-b1a9-4decd73639a7/image.png></div>
<div style="margin: 0; padding: 0;"><img src = https://velog.velcdn.com/images/choon0414/post/d75d0252-8d8c-412e-9d00-95108be27ebe/image.png
></div>
<div style="margin: 0; padding: 0;"><img src = https://velog.velcdn.com/images/choon0414/post/87d75e4d-7b15-4405-86c3-d8e83fecd8e8/image.png></div>
DB가 잘 연결되었다.
<br><br>

# 도커 컴포즈
> __도커 컴포즈__란? 
  - 단일 서버에서 여러 개의 컨테이너를 하나의 서비스로 정의해 컨테이너를 묶음으로 관리할 수 있는 작업 환경을 제공하는 관리 도구
  
## 도커 컴포즈로 여러 컨테이너 띄우기
### 도커 컴포즈 설치
1. 도커 컴포즈 설치
  ```java
  $ sudo curl -L "https://github.com/docker/compose/releases/download/latest/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  ```
2. 권한 부여
  ```java
  $ sudo chmod +x /usr/local/bin/docker-compose
  ```
3. 심볼릭 링크 설정
  ```java
  $ sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
  ```
4. 설치 확인
  ```java
  $ docker-compose --version
  ```
<div style="margin: 0; padding: 0;"><img src = https://velog.velcdn.com/images/choon0414/post/3e60429f-f07d-4bfd-a504-911a7880070e/image.png></div>
사진처럼 버전이 잘 나오면 성공적으로 설치가 된 것이다.

### 도커 컴포즈로 여러 컨테이너 띄우기
1. 도커 컴포즈 yml 파일 작성
  ```java
  // vim docker-compose.yml
  version: '3.8'

  services:
    mysql8:
      image: mysql:8.0
      container_name: mysql8.0c
      environment:
        MYSQL_ROOT_PASSWORD: qwer1234
        MYSQL_DATABASE: escaperoom
      ports:
        - "3308:3306"
      volumes:
        - mysql8.0c-data:/var/lib/mysql

    mysql57:
      image: mysql:5.7
      container_name: mysql5.7c
      environment:
        MYSQL_ROOT_PASSWORD: qwer1234
        MYSQL_DATABASE: escaperoom
      ports:
        - "3309:3306"
      volumes:
        - mysql5.7c-data:/var/lib/mysql

  volumes:
    mysql8.0c-data:
    mysql5.7c-data:
  ```

2. yml 파일로 컨테이너 생성
  ```java
  $ docker-compose up -d
  ```
<div style="margin: 0; padding: 0;"><img src = https://velog.velcdn.com/images/choon0414/post/d5698178-b70f-492e-acb1-735b5c49718e/image.png></div>


3. 실행 확인
  ```java
  $ docker-compose ps
  ```
<div style="margin: 0; padding: 0;"><img src = https://velog.velcdn.com/images/choon0414/post/edac2778-ded1-4c3b-bf38-b8614a1a130f/image.png></div>
<br>
  
## 도커 컴포즈를 사용하는 이유
- 도커를 사용할 경우 여러 개의 컨테이너를 띄울 때, 위에서 사용했던 방법과 같이 __각각의 컨테이너를 따로 생성__해야 한다.
```java
  // mysql8.0은 3306포트로, mysql5.7은 3307포트로 연결되도록 설정
  $ docker run --name mysql5.7 -e MYSQL_ROOT_PASSWORD=[password] -d -p 3306:3306 mysql:5.7
  $ docker run --name mysql8.0 -e MYSQL_ROOT_PASSWORD=[password] -d -p 3307:3306 mysql:8.0
  ```
- 하지만 도커 컴포즈를 사용하면 하나의 yml 파일로 여러 개의 컨테이너를 한 번에 생성할 수 있어 편리하다.
```java
  // vim docker-compose.yml
  version: '3.8'

  services:
    mysql8:
      image: mysql:8.0
      container_name: mysql8.0c
      environment:
        MYSQL_ROOT_PASSWORD: qwer1234
        MYSQL_DATABASE: escaperoom
      ports:
        - "3307:3306"
      volumes:
        - mysql8.0c-data:/var/lib/mysql

    mysql57:
      image: mysql:5.7
      container_name: mysql5.7c
      environment:
        MYSQL_ROOT_PASSWORD: qwer1234
        MYSQL_DATABASE: escaperoom
      ports:
        - "3308:3306"
      volumes:
        - mysql5.7c-data:/var/lib/mysql

  volumes:
    mysql8.0c-data:
    mysql5.7c-data:
  ```

---

__[참고자료]__
[[Ubuntu] docker 및 docker-compose 설치](https://zhfvkq.tistory.com/41)
[도커 컴포즈 - 개념 정리 및 사용법](https://seosh817.tistory.com/387)

 
