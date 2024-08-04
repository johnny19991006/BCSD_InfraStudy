# 도커 컴포즈

> 단일 서버에서 여러개의 컨테이너를 하나의 서비스로 정의해 컨테이너의 묶음으로 관리할 수 있는 작업환경 제공
시스템 구축에 필요한 설정을 `yaml` 포맷으로 기재한 정의 파일을 이용해 전체 시스템을 일괄 실행 또는 종료 및 삭제 가능

> 정의파일 : 컨테이너나 볼륨을 '어떤 설정으로 만들지'에 대한 항목 기재

## 명령어
- `up` : `docker run` 과 유사, `정의 파일`에 기재된 내용대로 이미지를 내려받고 컨테이너를 생성 및 실행 - 환경을 한꺼번에 생성

- `down` : 컨테이너와 네트워크를 정지 및 삭제 - 볼륨과 이미지는 삭제 X 

- `stop` : 컨테이너와 네트워크를 삭제 없이 종료

## Dockerfile과의 차이
> 만드는 대상이 다름

- Docker Compose : `docker run` 명령어를 여러개 모아놓은것과 같음 - 컨테이너와 주변 환경을 생성, 네트워크와 볼륨까지 만듦
- Dockerfile : 이미지를 만들기 위한 것 - 네트워크나 볼륨은 만들 수 없음

## 구조
```
version: "3"

services:
  example:
    depends_on:
      - mysql000ex11
    image: wordpress
    networks:
      - example000net1
    ports:
      - 8085:80
    restart: always
    environment:
      example_DB_HOST=mysql000ex11
      example_DB_NAME=example000db
      example_DB_USER=example000kun
      example_DB_PASSWORD=example
```

- `version` : 컴포즈 파일 버전
- `services` : 컨테이너 정보 - 있는만큼
- `depends_on` : 다른 서비스와의 의존관계
- `networks` : 네트워크 관련 정보
- `ports` : 볼륨 관련 정보
- `restart` : 컨테이너 종료 시 재시작 여부 설정
- `environment` : 환경변수 설정


# 이미지 레이어
> 이미지는 한번 만들어지면 정보는 절대 변하지 않으며, 이미지를 통해 컨테이너를 만들 수 있음 
이렇게 만들어진 이미지는 컨테이너 생성을 위한 모든 정보를 갖고 있기 때문에 수백 메가바이트에서 수 기가바이트가 넘음 &rarr; 이때 이미지에서 작은 변경사항이 생겨서 이미지를 변경해야 할때 한줄로 인하여 이미지를 다시 다운로드 받으면 비효율적임 &rarr; 이러한 문제를 해결하기 위해 `레이어` 개념 도입

## 레이어
```
기존 이미지에 추가적인 파일이 필요할 때 다시 다운로드 받는 방법이 안니 해당 파일을 추가하기 위한 개념
```

![](https://velog.velcdn.com/images/dradnats1012/post/8bd172f7-9200-40ae-a74e-65c89162c389/image.png)

`Docker` 이미지는 여러 레이어로 구성되며, 각 레이어는 이전 레이어의 변경 사항을 가지고 있음
이미지에 작업이 추가되면 새로운 레이어가 생성되는 개념은 `Git commit` 로그와 비슷하다고 보면됨

## 특징
- 레이어 격리 : 각 레이어는 독립적 파일 시스템, 애플리케이션과 종속성에 대한 변경 사항만 포함
- 레이어 재사용 : 동일한 레이어를 여러 이미지에 공유 &rarr; 이미지 빌드 시간과 저장소 사용량 줄임
- 레이어 버전 관리 : 레이어를 생성할 때마다 새로운 버전이 만들어짐, 이미지 업데이트시에도 이전 버전의 레이어 유지 &rarr; 변경 이력 관리 가능
- `Dockerfile` 기반 레이어 생성 : `Dockerfile` 에서 작성된 명령어들이 실행될때마다 새로운 레이어가 만들어짐 - 변경 사항 추적 가능

## 작동 원리 
1. 이미지 생성시, `Dockerfile`을 사용하여 이미지 레시피 정의
2. `Dockerfile`에 기술된 명령어들이 순서대로 실행
3. 명령어가 실행될 때마다 새로운 레이어 생성, 변경 사항 레이어 저장
4. 이미지 실행 시, 해당 이미지의 모든 레이어 연결 - 전체 파일 시스템 구성 - 실행 컨테이너는  모든 레이어의 데이터에 접근 가능
5. 컨테이너 내에서 변경해 저장한 것은 컨테이너의 최상단 레이어에 저장되며 이미지의 레이어에는 영향을 주지 않음



# 서론
이번주 인프라 스터디 실습은 도커를 이용하여 두가지 버전의 `MySQL`을 띄워보는 실습이다!
```
mysql -V
``` 
명령어를 통해서 기존에 올라가 있는 `MySQL`의 버전을 확인해보니 
![image](https://github.com/user-attachments/assets/bae7419c-2db7-4bd4-a426-0c2344781e02)
8.0.37이어서 도커를 이용하여 5.7 버전을 띄워봤다

# 실습
## 패키지
```
sudo apt-get update
```
우선 패키지 목록을 업데이트 해준다

그 후 필요한 패키지를 깔아주어야 한다

```
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
    
or    
    
sudo apt-get install ca-certificates
sudo apt-get install curl
sudo apt-get install gnupg
sudo apt-get install lsb-release
```

- `ca-certificates` : ssl 인증서 관리 패키지 &rarr; 도커 저장소에서 데이터 다운로드할 때 씀
- `curl` : url을 통해 데이터 전송 &rarr; 도커의 `GPG` 키 다운로드
- `gnupg` : 데이터 암호화 & 서명 &rarr; 도커의 `GPG` 키 관리 & 검증
- `lsb-release` : `LSB` 배포 관련 정보 제공 &rarr; 사용 중인 리눅스 정보 제공

## GPG 키
> `GPG 키`는 도커에만 존재하는건 아니고 리눅스 패키지 관리 도구가 프로그램 패키지가 유효한지 확인하기 위해 설치 전에 `GPG 키`를 통해서 검증하는 과정을 거치는데 사용된다고 한다

`GPG 키`를 이용해서 도커 패키지의 무결성을 확인해줘야 해서 키를 추가해줘야 한다

```
sudo mkdir -m 0755 -p /etc/apt/keyrings
```
우선 키를 저장할 디렉토리를 만들어 준다

그 후에 도커의 `GPG 키`를 다운로드하고 변환해서 저장해준다

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
`curl =fsSl https://download.docker.com/linux/ubuntu/gpg` 에서 나온 출력값을 `|` 뒤의 명령어에 전달해준다

`sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg` 
명령어를 통해서 다운로드 받은 파일을 바이너리 형식으로 바꿔서 저장해준다

![image (1)](https://github.com/user-attachments/assets/b7e21a0b-813e-45a8-942c-6c7bc6039718)
그러면 이렇게 저장이 된걸 볼 수 있다!

![image (2)](https://github.com/user-attachments/assets/076284cb-bc19-4f85-aa87-2ae826b2925f)
라 그런가 깨진다

```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
다음은 이 명령어를 이용해서 docker.list 파일에 도커 저장소 URL을 추가해준다

이제 패키지를 다 깔고 설정을 해줬으면 도커를 설치해준다

```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```
![image (3)](https://github.com/user-attachments/assets/bc14c21f-9e8f-415e-a99f-b7492632ae55)
볼 수 있다

```
docker --version
sudo systemctl status docker
```
명령어를 통해 확인해보면 
![image (4)](https://github.com/user-attachments/assets/e71c139d-9326-41f7-b1de-5e33e48ebdb6)

![image (5)](https://github.com/user-attachments/assets/8fddb9ac-8236-4a85-9390-a42b76ef6dda)

제대로 깔린 것을 볼 수 있다


## 도커 컨테이너

### MySQL
```
docker run --name mysql57 -e MYSQL_ROOT_PASSWORD=비밀번호 -e MYSQL_DATABASE=dockerDB -e MYSQL_USER=이름 -e MYSQL_PASSWORD=이름 -p 3307:3306 -d mysql:5.7
```
기존 `MySQL`에서 3306번 포트를 사용중이어서 3307번 포트로 연결을 해줬다

위의 명령어를 실행시키려고 보니 권한이 없어서 권한을 추가해줬다
```
sudo usermod -aG docker $USER - $USER는 현재 로그인한 사용자
newgrp docker
```

그 후 docker run을 다시 해줬다

![image (6)](https://github.com/user-attachments/assets/ee979d27-a23a-4d80-add8-1e803d929f50)

```
docker ps
```
를 통해서 확인을 해주니

![image (7)](https://github.com/user-attachments/assets/7d319504-90c5-4ffb-8b08-67a0391bcd7e)
잘 뜬것을 확인할 수 있었다

### SpringBoot
그리고 이제 `SpringBoot` 프로젝트를 도커로 띄워줘야 했다. 그러기 위해서는 소스 코드에 Dockerfile을 생성해줘야했다 &rarr; 이 `Dockerfile`을 통해 도커 이미지가 생성이 된다고 한다

프로젝트에 Dockerfile을 생성후 `cat` 명령어를 통해 확인해줬다

```
FROM openjdk:17-jre-slim
VOLUME /tmp
ARG JAR_FILE=target/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

![image (8)](https://github.com/user-attachments/assets/88fa167c-b170-4e13-b168-9caab73ee08d)

```
docker build -t senier-image .
```
그리고 빌드를 해줬다. 여기서 senier-image는 내 졸작 프로젝트를 이미지로 만드는거라서 이름을 저렇게 해줬고 원하는 이름으로 빌드를 해주면 된다

![image (9)](https://github.com/user-attachments/assets/09a305b9-f47e-475b-96b8-cf4d1aea2b00)


```
docker images
```
를 통해서 이미지들을 확인해주면 

![image (10)](https://github.com/user-attachments/assets/0fd107bb-aa48-44f0-9cb6-f687b759f114)

이렇게 이미지가 잘 생성된 것을 볼 수가 있다

그리고 이렇게 만든 `SpringBoot` 이미지를 run 해줬다

```
docker run --name springboot --network springboot-mysql-network -e SPRING_DATASOURCE_URL=jdbc:mysql://mysql57:3306/dockerDB -e SPRING_DATASOURCE_USERNAME=이름 -e SPRING_DATASOURCE_PASSWORD=비밀번호 -p 8081:8080 -d senier-image
```

이것도 역시 8080포트가 사용중이기 때문에 8081포트를 이용했다

`docker ps`를 통해서 확인해보면 

![image (11)](https://github.com/user-attachments/assets/a1364c0a-7ba7-408f-8d59-059b1c540f96)

잘 떠있는것을 볼 수 있다
그리고 `MySQL 5.7` 버전에 접속해서 보니까 제대로 테이블들이 생성이 되어 있었다 

![image (12)](https://github.com/user-attachments/assets/8ec9928c-ecc6-48d0-b32a-62f52e0540aa)

이렇게 잘 뜬것을 확인했고 스웨거를 통해서 잘 동작을 하는지 확인하려고 했다
8080 포트인 기존 스웨거는 
`http://주소:8080/swagger-ui/index.html` 잘 접속이 됐는데 
`http://주소:8081/swagger-ui/index.html` 는 접속이 되지 않았다

왜 그런지 고민을 하다가 내가 보안그룹에서 인바운드 규칙을 8081 포트를 허용해주지 않았다는게 기억이 나서 8081 포트를 허용해줬다
![image (13)](https://github.com/user-attachments/assets/64215308-b30e-4ea1-b5d2-4a69d3fd66e3)

그 후에 8081포트를 통해서 스웨거에 접속을 해주니
![image (14)](https://github.com/user-attachments/assets/53b94463-391e-4844-bdf4-4b9cf8bcf6f1)

잘 되는 것을 볼 수 있다!

# 결론
이렇게 도커를 이용해서 다른 버전의 `MySQL`을 띄워봤다
하면서 든 생각은 이런식으로 여러개를 띄웠을 때 `DB`의 데이터가 일치하지 않을 것 같아서 주기적으로 데이터를 맞춰주는 작업이 필요할 것 같다는 생각이 들었다.
이에 관한 실습을 또 진행해봐야겠다
