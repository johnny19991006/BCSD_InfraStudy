## 배포 파이프라인 구축하기

### 흐름
- 프로젝트 커밋 push
- 깃허브 액션에서 인지
- 바뀐 소스코드를 jar 파일로 빌드해 도커허브로 이미지 push
- EC2에서 도커허브에 올라온 이미지를 pull
- docker compose에서 이미지로 서비스 생성 및 실행

#### 1. 도커파일 생성
- 프로젝트 파일의 최상단 루트에 Dockerfile을 추가한다.
- ![](https://velog.velcdn.com/images/choon0414/post/c211830f-ca9f-4654-ac92-0135c6868322/image.png)

```
FROM openjdk:17  
LABEL authors="황현식"  
ARG JAR_FILE=build/libs/*.jar  
COPY ${JAR_FILE} app.jar  
ENTRYPOINT ["java","-jar", "-Dspring.profiles.active=prod", "/app.jar"]
```

#### 2. EC2 서버에서 docker-compose.yml 파일 작성하기

#### 3. GitHub Actions로 자동 배포 구성하기
- Settings > Secrets and Variables > Actions > Repository secrets 에서 비공개 값들 추가하기
![](https://velog.velcdn.com/images/choon0414/post/da07bae5-1a12-4d55-882f-625ef911c008/image.png)

- Actions > New workflow > Java with Gradle 로 깃허브 액선의 yml 파일을 작성한다.
```
name: Java CI with Gradle

on:
  push:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: write

    steps:
    - uses: actions/checkout@v4
    - name: Set up JDK 17
      uses: actions/setup-java@v4
      with:
        java-version: '17'
        distribution: 'temurin'

    - name: Generate and submit dependency graph
      uses: gradle/actions/dependency-submission@af1da67850ed9a4cedd57bfd976089dd991e2582 # v4.0.0

	- name: Setup Gradle
      uses: gradle/actions/setup-gradle@af1da67850ed9a4cedd57bfd976089dd991e2582 # v4.0.0

    - name: Set up environment
      run: |
        mkdir -p src/main/resources/
        echo "${{ secrets.APPLICATION_YML }}" > src/main/resources/application.yml

    - name: Build with Gradle
      run: ./gradlew build

    - name: Login DockerHub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKER_USER }}
        password: ${{ secrets.DOCKER_ACCESS_TOKEN }}

    - name: Build Docker image
      run: docker build -t choon0414/demo:latest .

    - name: Push Docker image to DockerHub
      run: docker push choon0414/demo:latest

    - name: EC2 deploy
      uses: appleboy/ssh-action@master
      with:
        host: ${{ secrets.EC2_HOST }}
        username: ${{ secrets.EC2_USER }}
        key: ${{ secrets.EC2_PEM_KEY }}
        script: |
          cd /home/ubuntu
          docker stop escaperoom
          docker rm -f escaperoom
          docker-compose pull
          docker-compose up -d app
```
##### 동작
- 깃 허브에 커밋이 push되는 것으로 트리거가 된다.
```
on:
  push:
    branches: [ "main" ]
```

- secrets의 APPLICATION_YML을 통해 applcation.yml 파일을 생성하고 jar 파일로 빌드한다.
```
- name: Set up environment
      run: |
        mkdir -p src/main/resources/
        echo "${{ secrets.APPLICATION_YML }}" > src/main/resources/application.yml
        
- name: Build with Gradle
      run: ./gradlew build
```

- 도커 허브에 로그인 해 jar 파일의 이미지를 생성한다.
```
	- name: Login DockerHub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKER_USER }}
        password: ${{ secrets.DOCKER_ACCESS_TOKEN }}

    - name: Build Docker image
      run: docker build -t choon0414/demo:latest .

    - name: Push Docker image to DockerHub
      run: docker push choon0414/demo:latest
```

- EC2에 접근해 도커 허브의 이미지를 pull 받고 새로 빌드한다.
```
	- name: EC2 deploy
      uses: appleboy/ssh-action@master
      with:
        host: ${{ secrets.EC2_HOST }}
        username: ${{ secrets.EC2_USER }}
        key: ${{ secrets.EC2_PEM_KEY }}
        script: |
          cd /home/ubuntu
          docker stop escaperoom
          docker rm -f escaperoom
          docker-compose pull
          docker-compose up -d app
```


![](https://velog.velcdn.com/images/choon0414/post/0026c787-282a-49c9-9a8c-042468a452ee/image.png)


![](https://velog.velcdn.com/images/choon0414/post/b7c5bda5-5ace-48c8-9efd-02cde0dcd867/image.png)


