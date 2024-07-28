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
