# 3계층 구조(3 Tier Architecture)
3계층 구조란 말 그대로 계층을 3개로 나눈 구조이다. 각각의 계층은 다음과 같다.
## 프레젠테이션 계층(Web Server)
사용자에게 보이는 계층이고, FrontEnd, Android, IOS에서 사용자에게 보여지는 이 계층에 해당한다.

## 애플리케이션 계층(WAS)
프레젠테이션 계층에서 보내진 데이터, 프레젠테이션 계층으로 보낼 데이터를 주로 처리한다. BackEnd라고 말하면 보통 이 계층을 말한다.

## 데이터 계층(DB)
데이터베이스를 관리하는 계층이고, DBMS가 해당 계층이다. 데이터 계층도 보통 BackEnd가 담당한다.

## 장점
계층을 3개로 분리하였을 때 장점은 무엇일까? 다음과 같은 경우 장점이 있을 것이다.
> 프레젠테이션 계층에 FrontEnd, IOS, Android등 여러 매체가 있을 때

이 경우 애플리케이션 계층에서 개발한 API를 이용해서 3개의 매체에서 동일한 데이터를 받을 수 있다. 따라서 일관된 데이터 처리와, 각각의 매체에서 데이터를 처리하기 위한 개발 비용을 아낄 수 있다.

## 단점
관리해야할 계층이 많아진다. 따라서 서비스의 특징에 맞는 계층을 구조를 선택하는 것이 중요하다.


## 다른 계층들

### 1 Tier
1계층은 하나의 서버에 모든 계층이 다 들어가 있는 경우이다. 소규모 개발에서 주로 사용되며, 서버가 변경되면 모든 구성을 변경해야 한다.

### 2 Tier
2계층은 프레젠테이션, 애플리케이션 계층을 하나로 묶고, 데이터 계층을 별도로 구성한다. 프론트만 개발하여 모바일에서 웹뷰로 띄워주는 Full Stack개발자들이 선호하는 방식인 것 같다.

# 코인 인프라
## 코인의 구조
### 빌드 서버
>인터널, 코인 BackEnd, 코인 FrontEnd등 BCSD의 모든 프로젝트의 빌드를 담당하는 서버이다.

빌드 서버에는 CI/CD를 위해서 jenkins가 설치되어 있다.

> CI(Continuous Integration): 지속적 통합이라는 의미로 GitHub의 내용을 지속적으로 빌드서버로 통합해주는 기능

> CD(Continuous Delivery): 지속적인 제공으로 지속적 배포라고 볼 수 있다. 빌드, 테스트, 배포를 자동화한다.

#### 빌드 서버 구성하는 법
1. 빌드 서버에 jenkins를 설치한다.
2. 빌드 서버의 jenkins홈페이지에 들어가서 item을 추가한다.
3. 추가한 item의 구성을 설정한다.
  - CI할 GitHub주소를 설정한다.
  ![alt text](이미지/image.png)
  - 빌드할 때 사용할 JDK를 설정한다.
  ![alt text](이미지/image-1.png)
  - 소스코드 관리에서 어느 브랜치의 소스코드를 CI할것인지 설정한다.
  ![alt text](이미지/image-2.png)
  - **실제 빌드 설정(중요)**
  ![alt text](이미지/image-7.png)
  ![alt text](이미지/image-8.png)
  ![alt text](이미지/image-9.png)
  ![alt text](이미지/image-10.png)
  ![alt text](이미지/image-11.png)
  - 빌드 산출물을 어떻게 할것인지 설정
  ![alt text](이미지/image-12.png)
  ![alt text](이미지/image-13.png)
  - GitHub에서 Merge가 될 때 자동 배포를 하려면 빌드 유발 설정을 한다.
  ![alt text](이미지/image-3.png)
  ![alt text](이미지/image-4.png)
  ![alt text](이미지/image-5.png)
  ![alt text](이미지/image-6.png)

#### 빌드 서버가 존재하는 이유
빌드를 하는 데에는 많은 리소스(cpu, ram)가 필요하기 때문에 빌드 서버를 따로 구성한다. 만약 프로젝트 서버에서 빌드도 같이 하게 되면 자원 부족 문제로 프로젝트 서버가 터질수도 있을 것 같다.

### 프로젝트 서버
빌드 산출물 jar파일을 실행하는 서버이다. 스테이지 WAS는 이 서버의 8085포트에 떠있다.

#### Nginx
실제로 웹사이트에 접근할 때에는 koreatech.in과 같은 도메인을 통해 접근한다. 이걸 가능하게 해주는 것이 Nginx이다.

다음은 Nginx스크립트이다.

`api.stage.koreatech.in`이라는 요청이 들어오면 WAS가 떠있는 localhost:8085로 해당 요청을 연결시켜준다.
>![alt text](이미지/image-14.png)

### DB서버
DB서버에서는 Mysql, Redis등과 같은 DB가 설치되어 있다.


# 실습
![alt text](이미지/1.png)

![alt text](이미지/2.png)

처음에 만들때 캡쳐한건데 AMI잘못선택했음. Ubuntu선택했어야했는데.. 나중에 바꿨는데 캡쳐를 못해서 일단 이거로 첨부
![alt text](이미지/3.png)

![alt text](이미지/4.png)

![alt text](이미지/5.png)

![alt text](이미지/6.png)

![alt text](이미지/7.png)

![alt text](이미지/8.png)

![alt text](이미지/9.png)

 ![alt text](이미지/10.png)

 ![alt text](이미지/11.png)

 젠킨스 실행이 안되서 젠킨스 실습은 여기까지...

## 클라이언트와 백엔드의 코드 배포

> 클라이언트 소스코드: https://github.com/20HyeonsuLee/INFRA_FRONT

> 백엔드 소스코드: https://github.com/20HyeonsuLee/INFRA_BACK

인스턴스에 Mysql설치 후 쿼리문을 이용해서 DB생성

트러블슈팅: ec2의 `/usr/local`경로에 프론트, 백엔드 코드 clone하고 빌드해서 실행시켜보는 것 까지 해보려고 했는데 백엔드 코드 빌드가 안된다...