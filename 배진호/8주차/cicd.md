# AWS 추가학습이라 부르고 CI/CD를 곁들인

<img width="1699" alt="image" src="https://github.com/user-attachments/assets/581f15ad-915c-4731-8a51-2f13dc786be4">
Koin에서 CI/CD가 동작할 때 편리했는데, 소마에서 프로젝트할때마다 수동으로 배포하는 것과 빌드 실패해서 배포 실패하는 경험을 하였다.
그래서 확실히 CI/CD가 있으면 편리할 것 같아서 소마 프로젝트에 도입해보았다.

사용한 것은 Github Actions

## CI
CI는 Koin과 동일하게 작성하였다. (대신 Koin에는 Flyway 버전 체크도 추가되었다.)
<img width="906" alt="image" src="https://github.com/user-attachments/assets/3f9af331-c96a-4339-a09e-a2223cb2cc01">

```
name: Continuous Integration

on:
  push:
    paths: '/**'
  pull_request:
    branches:
      - develop
      - main

permissions:
  pull-requests: write
  checks: write

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
      - name: Set up Repository
        uses: actions/checkout@v3

      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Give permission for Gradle
        run: chmod +x gradlew

      - name: Run gradle build
        run: ./gradlew build

      - name: comments test result on PR
        uses: EnricoMi/publish-unit-test-result-action@v1
        if: always()
        with:
          files: '**/build/test-results/test/TEST-*.xml'

      - name: comments test result in failed line if test failed
        uses: mikepenz/action-junit-report@v3
        if: always()
        with:
          report_paths: '**/build/test-results/test/TEST-*.xml'
          token: ${{ github.token }}

```

CI Steps (PR이 업데이트 될 때마다 동작한다.)
1. 리포지토리 설정
2. JDK 17 설정
3. Gradle 빌드 실행
4. 테스트 결과 PR에 게시
5. 테스트 실패 시 라인 코멘트

## CD
<img width="523" alt="image" src="https://github.com/user-attachments/assets/bca75fcf-af28-4d13-b5b3-f0542eab6b60">

```
name: Deploy to Amazon ECS

on:
  push:
    branches: [ "develop" ]

env:
  AWS_REGION: ap-northeast-2
  ECR_REPOSITORY: oatnote
  ECS_SERVICE: oatnote-service
  ECS_CLUSTER: oatnote-cluster
  ECS_TASK_DEFINITION: oatnote-family-revision.json
  CONTAINER_NAME: oatnote-container

permissions:
  contents: read

jobs:
  deploy:
    name: Deploy
    runs-on: ubuntu-latest
    environment: production

    steps:
    - name: Checkout
      uses: actions/checkout@v4

    - name: Set up JDK 17
      uses: actions/setup-java@v3
      with:
        distribution: 'temurin'
        java-version: '17'

    - name: Grant execute permission for gradlew
      run: chmod +x ./gradlew

    - name: Build with Gradle
      run: ./gradlew build

    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v2
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ${{ env.AWS_REGION }}

    - name: Login to Amazon ECR
      id: login-ecr
      uses: aws-actions/amazon-ecr-login@v2

    - name: Build, tag, and push image to Amazon ECR
      id: build-image
      env:
        ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
        IMAGE_TAG: ${{ github.sha }}
      run: |
        docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .
        docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
        echo "image=$ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG" >> $GITHUB_ENV

    - name: Fill in the new image ID in the Amazon ECS task definition
      id: task-def
      uses: aws-actions/amazon-ecs-render-task-definition@v1
      with:
        task-definition: ${{ env.ECS_TASK_DEFINITION }}
        container-name: ${{ env.CONTAINER_NAME }}
        image: ${{ env.image }}

    - name: Deploy Amazon ECS task definition
      uses: aws-actions/amazon-ecs-deploy-task-definition@v1
      with:
        task-definition: ${{ steps.task-def.outputs.task-definition }}
        service: ${{ env.ECS_SERVICE }}
        cluster: ${{ env.ECS_CLUSTER }}
        wait-for-service-stability: true
```

Deploy to Amazon ECS (푸시 될 때 마다 동작한다.)
1. 리포지토리 설정
2. JDK 17 설정
3. Gradle 실행 권한 부여
4. Gradle 빌드 실행
5. AWS 자격 증명 설정
5. Amazon ECR 로그인
6. Docker 이미지 빌드, 푸시
7. ECS 태스크 정의에 새 이미지 ID 반영
8. ECS 태스크 정의 배포

이때 프로젝트의 환경변수를 `AWS Secrets Manager`를 통해 보안성을 향상 시켰다.
`AWS Secret Manager`는 한번 설정하면 다른 리포지토리에서도 사용이 가능한 반면 `Github Actions Secret`은 단일 레포지토리에서만 설정이 가능


## KOIN에서는?

### Koin에서는 빌드서버를 따로 두었다.
이유(추축)
1. build에 필요한 파일을 한곳에서 효율적으로 관리
2. build시에 서버의 자원을 많이 사용하게되면 서버에 악영향을 둘 수 있음

### Koin의 CI/CD 프로세스
1. CI가 동작하여, 빌드하고 테스트/버전확인을 진행한다.
2. 코드 리뷰를 마친 후 merge 한다.
3. Jenkins에서 webhook을 통해 변경사항을 감지한다.
4. 변경사항이 감지되면, 빌드 서버에서 프로젝트를 Clone한 후 빌드한다.
5. 빌드 산출물인 .jar실행파일을 실제로 실행할 서버(KOIN_STAGE)에 전송한다.
6. KOIN_STAGE서버 에서 전송받은 .jar파일을 실행한다.

### +) 추가적으로 Docker Swarm이 사라진 버전인 간략화된 Koin 인프라 구조를 그려보았다. (이상한부분 지적해주시면 감사드립니다.)
<img width="583" alt="image" src="https://github.com/user-attachments/assets/a9f08971-e19c-4749-97f5-7c9cd3e958d9">
