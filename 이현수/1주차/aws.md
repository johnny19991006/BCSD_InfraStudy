# 서론
도커 & 인프라 스터디를 시작했다. 가장 기본이 되는 AWS를 먼저 공부하기로 했고, 스터디의 시작으로 다음과 같은 학습키워드에 대해 간단하게 조사해보기로 했다.
> `vpc` `cidr` `ec2` `s3` `cdn` `route53` `nacl` `security group`

# 본론
## aws(Amazon Web Services)

다음 사진은 aws 메인페이지이다.
`클라우드 컴퓨팅`이라는 단어가 눈에 띈다.
>
<img src="https://velog.velcdn.com/images/leehyeonsu4888/post/e03b9c45-536e-47f1-b468-23df4da9b2d3/image.png" width="500px">

Microsoft 홈페이지에는 다음과 같이 설명돼 있다.

> 인터넷(“클라우드”)을 통해 서버, 스토리지, 데이터베이스, 네트워킹, 소프트웨어, 분석, 인텔리전스 등의 컴퓨팅 서비스를 제공하는 것입니다.

눈에 안보이는(클라우드) 컴퓨터를 대여해서(인터넷을 통해) 사용하는것 이구나 정도로 이해했다.

### 클라우드 컴퓨팅을 사용해야하는 이유
집에있는 컴퓨터를 사용하면 안되는 것일까?
클라우드 컴퓨팅을 이용해야하는 이유가 무엇일까?
많은 장점을 찾을 수 있었지만 그 중 가장 눈에 띄었던 것은 `안정성`과 `보안`이였다.
> Microsoft 홈페이지에서 제공하는 정보
>
<img src="https://velog.velcdn.com/images/leehyeonsu4888/post/19f0bbe7-54bb-4ecd-b42a-be9c44ca372b/image.png" width="400px">
<img src="https://velog.velcdn.com/images/leehyeonsu4888/post/60e7dfc0-0cff-4ab3-aa01-8d8459b92252/image.png" width="400px">

집에있는 컴퓨터로 설정하기 번거롭고 어려운 것들을 클라우드 컴퓨팅 환경에서는 기본적으로 제공해 준다는 것이 큰 장점이다.

### 왜 aws인가
못을 박을 때 돌로 박는 것 보다 망치를 사용하는 것이 좋을 것이다. 클라우드 컴퓨팅 서비스를 선택하는 기준도 동일하다. 내가 만들고자 하는 프로젝트에 최적화된 기능을 제공하는 서비스를 선택하는것이 좋다.

Microsoft에서 제공하는 `Azure`, 아마존에서 제공하는 `aws`, 구글에서 제공하는 `GCP`등 이 외에도 여러가지 서비스가 있다.

`GCP`는 데이터 분석 및 기계 학습에 특화된 기능을 제공하고, `Azure`는 Microsoft제품을 사용하는 기업들에게는 호환성이 좋을것이다. `aws`는 웹 서비스에 특화된 기능들을 제공한다.

웹 기반 서비스를 만들기 때문에 aws를 사용하는것이 유리하다.

## aws의 여러가지 기능들
aws에서 제공하는 여러가지 기능들이 `VPC` `cidr` `ec2` `s3` `cdn` `route53` `nacl` `security group`인 것이다. 이제부터 하나씩 살펴보도록 하겠다.
![](https://i.imgur.com/woKSkrQ.png)

### EC2(Elastic Compute Cloud)
> aws에서 제공하는 일종의 컴퓨터 이다.
#### 인스턴스
> 서버에 필요한 구성 요소(운영 체제와 추가 소프트웨어 포함)를 패키징하는 인스턴스용 사전 구성 템플릿.
#### Amazon Machine Images(AMIs)
> 인스턴스의 다양한 CPU, 메모리, 스토리지, 네트워킹 용량 및 그래픽 하드웨어 구성.
![](https://i.imgur.com/fNCrtS2.png)

#### 인스턴스 타입
> 인스턴스의 다양한 CPU, 메모리, 스토리지, 네트워킹 용량 및 그래픽 하드웨어 구성.

#### Amazon EBS
> Amazon Elastic Block Store(Amazon EBS)를 사용하는 데이터에 대한 영구 스토리지 볼륨.
- EC2 인스턴스가 연산에 관한 (CPU,메모리 등) 처리를 한다고 하면, 데이터를 저장하는 역할(SSD, HDD)은 바로 EBS가 한다고 보면 된다.
- 클라우드에서 사용하는 가상 하드디스크(HDD)
- EC2 인스턴스가 종료되어도 별개로 작동
- EBS는 **네트워크로 별개로 연결된** 서비스
- 하나의 인스턴스에 여러 개의 EBS를 붙일 수 있음
- 속도가 느림
- 대신 인스턴스가 삭제되더라도 EBS는 남아있음 (영구적 스토리지)
- 하나의 인스턴스에 연결한 EBS 볼륨을 따로 분리해서 다른 인스턴스에 연결 가능 (마치 USB를 뺏다 넣듯이)
- EBS는 EC2와 같은 가용영역(AZ)에 존재
  👉 가용 역역이 무엇인지 공부 필요
##### 인스턴스 저장 기반
- EC2안에 storage가 들어있음 (네트워크 연결X)
- 그래서 속도가 빠름
- 안에 들어있는 형태이니, 인스턴스가 삭제되면 storage도 같이 삭제 됨
- EBS처럼 스토어를 분리해서 다른 인스턴스에 연결 불가능
- 보통 영구적이지 않은 데이터를 저장  
    ex) 캐시 데이터
![](https://i.imgur.com/xQajjYk.png)
![](https://i.imgur.com/AkGwa4s.png)
![](https://i.imgur.com/yr8Gjwg.png)
https://inpa.tistory.com/entry/AWS-%F0%9F%93%9A-EBS-%EA%B0%9C%EB%85%90-%EC%82%AC%EC%9A%A9%EB%B2%95-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC-EBS-Volume-%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0
#### 키 페어
> 인스턴스에 대한 보안 로그인 정보. AWS는 퍼블릭 키를 저장하고 사용자는 프라이빗 키를 안전한 장소에 저장합니다.
https://potato-yong.tistory.com/46
#### 보안 그룹
> 인스턴스에 도달할 수 있는 프로토콜, 포트 및 소스 IP 범위와 인스턴스가 연결할 수 있는 대상 IP 범위를 지정할 수 있는 가상 방화벽.
![](https://i.imgur.com/aWMRdAt.png)
인바운드 규칙
![](https://i.imgur.com/Fxh9K6j.png)
아웃바운드 규칙
![](https://i.imgur.com/gRwLpgP.png)

### S3(Simple Storage Service)
> 객체 스토리지 서비스이다.

S3는 버킷을 이용해서 파일을 객체로 저장한다. 각 객체는 데이터, 메타데이터, 고유 식별자로 구성된다. 즉 버킷에 객체를 저장한다.

즉, HTTP/HTTPS API를 통해 파일을 객체로 변환하여 버킷에 저장한다

### 버킷
버킷의 이름은 유일하고, 버전관리 기능을 통해서 사용자의 실수 시 이전 버전으로 되돌릴 수 있다.

버킷 주소 형식: `https://bucketname.s3.Region.amazon.com`

### 객체
객체 하나의 크기는 1Byte ~ 5TB이고, 저장 가능한 객체의 갯수는 무한이다. 또한 각각의 객체마다 접근권한을 설정할 수 있다.

![](https://i.imgur.com/gCGiiEi.png)
![](https://i.imgur.com/cXmwArf.png)

![](https://i.imgur.com/HnqCjb3.png)
![](https://i.imgur.com/EouHUUR.png)
![](https://i.imgur.com/kO0xX3J.png)
	
### CDN
> 지리적으로 분산된 서버들을 연결한 네트워크로서 웹 컨텐츠의 복사본을 사용자에 가까운 곳에 두거나 동적 컨텐츠(예: 라이브 비디오 피드)의 전달을 활성화하여 웹 성능 및 속도를 향상할 수 있게 한다.
![](https://i.imgur.com/GfNeq9v.png)
- CloudFront가 콘텐츠를 Edge Location에 캐싱하기 때문에 S3 버킷 부하감소 및 콘텐츠 응답속도 향상
![](https://i.imgur.com/YHhutig.png)
![](https://i.imgur.com/sdPXQvn.png)
![](https://i.imgur.com/QEV2mz5.png)

### Route53
> AWS에서 지원하는 DNS서비스이다.

DNS를 이용하여 로드밸런싱을 할 수 있다.
BCSD에서 사용중인 도메인
![](https://i.imgur.com/puSGQ2M.png)


### VPC(Virtual Private Cloud)
> AWS에서 제공하는 가상 사설 네트워크 서비스로, 논리적으로 격리된 네트워크 환경을 제공하여 AWS 리소스를 운영할 수 있게 한다.

>
VPC를 사용하지 않은 경우 모든 인스턴스가 서로 연결되어 인터넷에 연결된다.
>
<img src="https://velog.velcdn.com/images/leehyeonsu4888/post/d8908387-67a8-48b9-8bdc-01d1eefc3577/image.png" width="400px">

>
VPC를 사용한 경우 VPC로 인스턴스를 묶을 수 있고 VPC별로 설정을 다르게 줄 수 있다.
>
<img src="https://velog.velcdn.com/images/leehyeonsu4888/post/aa7e33a2-324f-4caa-8093-c67c9f81572a/image.png" width="400px">
>



### CIDR(Classless Inter-Domain Routing)
> 데이터 라우팅 효율성을 향상시키는 IP 주소 할당 방법입니다. 


### NACL(Network Access Control List)
> 서브넷(subnet)에 연결되어 있는 가상 방화벽 역할을 한다.

NACL은 각각의 서브넷에 대해 설정된다. 서브넷 내에서 들어오는(인바운드) 및 나가는(아웃바운드) 트래픽을 제어하여 보안 정책을 적용할 수 있다.

### security group
> ec2및 다른 리소스에 대한 네트워크 트래픽을 제어하는 가상 방화벽이다.

인바운드, 아웃바운드 트래픽에 대해 각각 규칙을 설정할 수 있다.

인바운드 규칙은 특정 포트, 프로토콜 및 소스 IP 범위에서 들어오는 연결을 제어하며, 아웃바운드 규칙은 특정 목적지 IP, 포트 및 프로토콜에 대한 나가는 연결을 제어한다.

인바운드 트래픽에 대한 응답이 아웃바운드 규칙에 따라 자동으로 허용된다. 예를 들어 SSH로 접속한 후에는 해당 연결에 대한 응답 트래픽도 자동으로 허용된다.

### 서브넷


동적 IP대응을 지원한다.


 온-프레미스: 기업이 자체적으로 IT 인프라를 소유, 관리 및 운영하는 경우

# 결론
키워드에 대해 조사하다보니 알아야 할것이 너무 많다. 앞으로 오늘 조사한 키워드에 대해서 하나씩 깊게 공부해보고 실습해볼 예정이다.

### 출처
https://ibm.com/kr-ko/topics/content-delivery-networks
https://brunch.co.kr/@topasvga/85
https://medium.com/harrythegreat/aws-%EA%B0%80%EC%9E%A5%EC%89%BD%EA%B2%8C-vpc-%EA%B0%9C%EB%85%90%EC%9E%A1%EA%B8%B0-71eef95a7098