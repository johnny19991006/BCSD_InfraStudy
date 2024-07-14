
**[🔑키워드]** AWS : `Vpc` `cidr` `ec2` `s3` `cdn` `route53` `nacl` `security group`

# **AWS**
## AWS의 구조
- AWS는 AWS Cloud 서비스 내에 지역별로 분포된 리전이 존재하고, 리전 안에 가용 영역들이 존재한다.
![](https://velog.velcdn.com/images/choon0414/post/c2a0a974-6787-49e9-a16d-e14bb5d765d9/image.png)
- __리전__
  - AWS 서비스가 제공되는 데이터 서버의 물리적 위치
  - 각 리전은 고유 번호가 부여됨
  - 하나의 리전은 반드시 2개 이상의 가용 영역으로 구성됨
- __가용 영역(Availability Zone, AZ)__
  - 하나 이상의 데이터 센터로 구성되어 있음
  - 여러 AZ 간에는 매우 빠른 전용 네트워크로 긴밀히 구성되어 있으며, 물리적인 위치는 떨어져 있어 하나의 AZ에 문제가 생길 경우 다른 AZ가 보완 가능함
  

<br> 

#### [참고자료]
[AWS 기초개념과 구조 파악](https://brunch.co.kr/@danni/12)
[그림으로 이해하는 AWS 구조](https://slog2.tistory.com/33)

---

# **1. VPC (Virtual Private Cloud)**

## VPC란?

- AWS 클라우드 내에서 가상 네트워크를 제공하는 서비스
- VPC를 사용하면 가상 네트워크 환경을 설정하고 AWS Resource(EC2, ELB, RDS, Security Group, Network ACL 등)을 탑재할 수 있다.

![](https://velog.velcdn.com/images/choon0414/post/712f652a-25cc-4e9f-a264-ee25868d2fde/image.png)


## 서브넷이란?
- VPC 영역 내에서 망을 더 쪼개서 사용하는 것
- 쪼개진 서브넷들은 하나의 AZ 내에 위치함
![](https://velog.velcdn.com/images/choon0414/post/8ef25874-09e9-4deb-b5a0-115ad511030b/image.png)

### 1) 퍼블릭 서브넷
- 외부에서 접근이 가능한 네트워크 영역
- 주로 웹 서버, 로드 밸런싱, CDN 등이 해당 서브넷에 위치함
- 공인 IP 주소가 할당됨
- 서브넷이 인터넷 게이트웨이로 향하는 라우팅이 있는 라우팅 테이블과 연결됨

### 2) 프라이빗 서브넷
- 외부에서 직접적으로 접근이 불가능한 네트워크 영역
- 주로 DB 서버, 어플리케이션 서버 등이 해당 서브넷에 위치함
- 사설 IP 주소가 할당됨
- 보안이 중요한 리소스들을 관리할 때 사용
- 외부와의 통신은 NAT 게이트웨이를 통해 이루어짐

## VPC의 특징

- 서브넷을 통해 여러 개의 VPC로 쪼개서 사용이 가능하다.
    ** 서브넷: VPC를 쪼갠 조각들*
- 용도에 따라 서브넷을 퍼블릭과 프라이빗으로 사용 가능
- 가상 라우트와 라우트 테이블을 제공함

<br> 

#### [참고자료]
[Virtual Private Cloud(VPC) 쉽게 이해하기 #1](https://aws-hyoh.tistory.com/49)
[[AWS] VPC의 개념](https://velog.io/@yenicall/AWS-VPC의-개념)
[퍼블릭 서브넷 vs 프라이빗 서브넷](https://velog.io/@rockwellvinca/AWS-%ED%8D%BC%EB%B8%94%EB%A6%AD-%EC%84%9C%EB%B8%8C%EB%84%B7%EA%B3%BC-%ED%94%84%EB%9D%BC%EC%9D%B4%EB%B9%97-%EC%84%9C%EB%B8%8C%EB%84%B7)

---

# **2. CIDR (Classless Inter-Domain Routing)**

## 클래스 기반 주소

- **1990년대 초까지 IP 주소는 클래스 기반 주소 지정 시스템을 사용해 할당됨**
- 고정된 길이의 주소
- IPv4 주소는 32비트로 구성됨
- **클래스 종류**
  - 클래스 A: 네트워크 접두사 비트가 8개, 1,600만 7,214개의 호스트 지원
  - 클래스 B: 네트워크 접두사 비트가 16개, 6만 5,534개의 호스트 지원
  - 클래스 C: 네트워크 접두사 비트가 24개, 254개의 호스트 지원
- **단점**
    - 각 클래스는 고정된 수의 디바이스를 지원함
    - 비효율적이고 공간 낭비가 심함
        - ex) 300개의 디바이스를 가진 조직에서 클래스 C는 46개가 부족하므로 클래스 B를 사용함 → 6만 5,234개는 사용하지 않고 낭비됨
    - 네트워크 결합 기능이 제한됨

- IP 주소 체계의 하나로, 네트워크나 서브넷을 식별하기 위해 사용됩니다. 예를 들어, 192.168.0.0/24는 192.168.0.0에서 192.168.0.255까지의 IP 주소 범위를 나타냅니다.

## CIDR이란?

- 인터넷상의 데이터 라우팅 효율성을 향상시키는 IP 주소 할당 방법

## CIDR의 장점

- **IP 주소 낭비 감소**
    - 특정 네트워크에 필요한 수의 IP 주소를 프로비저닝을 통해 낭비를 줄일 수 있음
        
        ** 프로비저닝: IT 인프라를 생성, 설정하는 프로세스*
        
    - 라우팅 테이블 항목이 줄고 데이터 패킷 라우팅이 단순화됨
- **데이터를 빠르게 전송할 수 있음**
    - 라우터에서 보다 효율적으로 IP 주소를 여러 서브넷으로 구성할 수 있다.
        - ex) 라우터에 연결된 모든 디바이스는 동일한 서브넷에 있고 동일한 IP 주소 접두사 사용
            
            ** 서브넷: 네트워크 내에 있는 소규모 네트워크*
            
    - 여러 서브넷을 만들고 통합 가능 → 불필요한 경로 없이 데이터 전달 가능
- **Virtual Private Cloud 생성**
    - 클라우드 내에서 호스팅되는 프라이빗 디지털 공간
    - VPC를 이용해 격리되고 안전한 환경에 워크로드를 프로비저닝할 수 있다.

- **슈퍼넷을 유연하게 생성 가능**
    - CIDR을 사용하면 필요에 맞게 IP 주소를 할당할 수 있어 주소 공간을 보다 효율적으로 관리할 수 있어 슈퍼넷 생성을 유연하게 할 수 있게 만든다.

<br> 

#### [참고자료]
[CIDR이란?- CIDR 블록 및 표기법 설명 - AWS](https://aws.amazon.com/ko/what-is/cidr/)

---

# **3. EC2 (Elastic Compute Cloud)**

## EC2란?

- AWS에서 제공하는 클라우드 컴퓨팅 서비스
- 인터넷(클라우드)를 통해 서버, 스토리지, DB 등의 컴퓨팅 서비스를 제공함
    
    → 원격 제어가 가능한 가상의 컴퓨터를 한 대 대여하는 개념
    
- 사용한 만큼 비용을 지불하는 탄력적 서비스

## 장점

- 비용 뿐 아니라 성능, 용량 등을 자유롭게 조절할 수 있음
- 간단한 설정으로 서버를 생성할 수 있어 효율적임


![](https://velog.velcdn.com/images/choon0414/post/7a75f11b-6289-47a6-8554-2577220087f1/image.png)




## EC2 인스턴스

- 한정된 요금으로 인스턴스의 유형과 사이즈를 골라 사용 목젝에 따라 최적화 시켜 사용
- 사용 목적(서버용, 머신러닝용, 게임용 등)에 따라 인스턴스의 타입이 다양하게 나뉨

![](https://velog.velcdn.com/images/choon0414/post/dd038ae9-bfd3-4fc4-840d-37746f7704b5/image.png)


## 인스턴스 구매 옵션

- **온디맨드 인스턴스**:
    - 필요할 때마다 인스턴스를 생성하고, 사용한 만큼만 비용을 지불한다.
    - 초기 비용이 없으며, 사용한 시간에 따라 비용이 청구됨
- **예약 인스턴스**:
    - 1년 또는 3년 기간으로 예약하여 사용
    - 온디맨드에 비해 최대 75%까지 비용 절감이 가능
    - 일정한 용량이 필요할 때 적합하다.
- **스팟 인스턴스**:
    - AWS의 여유 컴퓨팅 자원을 경매 방식으로 저렴하게 제공
    - 비용이 매우 저렴하지만, AWS가 필요할 경우 사전 통지 후 인스턴스를 중지시킬 수 있음
    - 유연한 작업에 적합


<br> 

#### [참고자료]
[Amazon EC2란 무엇인가요? - Amazon Elastic Compute Cloud](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/concepts.html)
[AWS EC2 개념 정리](https://velog.io/@server30sopt/AWS-EC2-개념-정리)

---

# **4. S3 (Simple Storage Service)**

## S3란?

- AWS에서 제공하는 클라우드 스토리지 서비스 → 데이터를 인터넷을 통해 저장
- 객체 업로드/다운로드 시 HTTP/HTTPS를 통한 API가 사용됨
- **장점**
    - 파일, 데이터 및 다양한 유형의 미디어 저장 가능
    - 저장 가능한 데이터 양에 제한이 없음
    - 파일에 인증을 붙여서 무단으로 엑세스 하지 못하도록 할 수 있음
    - 데이터의 손실이 발생할 경우 자동으로 복원

## S3 버킷

- 객체들을 그룹핑하는 디렉토리
- **특징**
    - 버킷의 이름은 유일해야 함
    - 버킷을 만들기 위해서는 지역을 선택해야 함 → 지역 간 객체 공유는 불가능
    - 버킷 주소는 https:// __bucketname__.s3.**Region**.amazon.com 형태

## S3 객체

- S3에 저장되는 데이터 하나 하나를 객체라 부름(파일과 같은 개념)
- **특징**
    - 객체 의 크기는 1Byte ~ 5TB
    - 저장 가능한 객체 수는 무제한
    - 객체마다 각각의 접근 권한 설정 가능

<br> 

#### [참고자료]
[[ S3 ] S3 란 무엇인가? S3 개념](https://bigco-growth-diary.tistory.com/43)
[초보자도 이해할 수 있는 S3(Simple Storage Service) | DevelopersIO](https://dev.classmethod.jp/articles/for-beginner-s3-explanation/)

---

# **5. CDN (Content Delivery Network)**

## CDN이란?

- 콘텐츠 전송 네트워크
- 데이터 사용량이 많은 어플리케이션의 웹 페이지 로드 속도를 높이는 상호 연결된 서버 네트워크
- 폭발적으로 증가하는 많은 양의 데이터들을 지연 없이 처리하기 위해 데이터를 분산해 전달하는 기술이 필요함 → CDN이 분산시켜줌
- 각 지역에 캐시 서버를 분산 배치해, 근접한 사용자의 요청에 원본 서버가 아닌 캐시 서버가 콘텐츠를 전달
    
    ex) 미국에 있는 사용자가 한국에 호스팅 된 웹 사이트에 접근할 경우, 미국에 위치한 pop 서버에서 웹 사이트의 콘텐츠를 전송함
    
- **장점**
    - 서버와 사용자 사이의 물리적인 거리를 줄여 콘텐츠 로딩에 소요되는 시간을 최소화 함
    - 여러 중간 서버 간에 로드를 분산시켜 본 서버의 부담을 줄임
        
        → DDoS 공격이나 트래픽 급증에 비교적 영향을 덜 받음
        

## CDN의 활용

- 동영상 스트리밍이나 온라인 게임, 대용량 파일 전송, 용량이 큰 이미지 등을 다루는 쇼핑몰이나 포털 사이트 등에서 안정적인 서비스 제공을 위해 활용됨
- 지역별로 서버를 분산 배치하는 원리이므로 특정 국가(지역)만을 타깃으로 하는 웹 서비스라면 CDN을 활용할 필요가 없음
    
    → 괜히 불필요한 연결지점만 늘어나 성능 저하가 생길 수 있기 때문
    

<br> 

#### [참고자료]
[CDN이란 무엇인가요? - 콘텐츠 전송 네트워크 설명 - AWS](https://aws.amazon.com/ko/what-is/cdn/)
[가비아 라이브러리](https://library.gabia.com/contents/infrahosting/8985/)

---

# **6. Route 53**

## Route 53이란?

- AWS에서 제공하는  DNS(Domain Name System)로, 도메인 이름을 IP 주소로 변환하거나, 트래픽을 애플리케이션으로 라우팅하는 데 사용됨
- **장점**
  - GSLB를 이용한 로드 밸런싱 기능을 제공해 여러 대의 서버에 트래픽을 분산시킬 수 있음
        *** 로드 밸런싱**: 도메인을 이용해 여러 개의 서비스로 부하를 분산시키는 것*
    ![](https://velog.velcdn.com/images/choon0414/post/252d524a-b374-4dc9-b0ff-1a6b63d27142/image.png)
  - 사용자의 요청을 EC2, ELB, S3버킷 등 인프라로 직접 연결 가능
  - 외부의 인프라로 라우팅 하는데 사용 가능
  - 지연시간 기반 라우팅 가능
  - 모니터링 기능 제공

## Domain Name 등록 방법

- 등록 및 접속 예시
    1. 사용자가 주소(`www.example.com`) 입력
    2. ISP가 관리하는 DNS 해석기로 라우팅
        **** ISP**: 케이블 인터넷 공급업체, 광대역 공급업체, 기업 네트워크 등의 인터넷 서비스 제공업체*
        
    3. DNS 해석기는 해당 요청을 DNS 루트 이름 서버에 전달
    4. DNS 해석기는 해당 요청을 `.com` 도메인의 TLD 이름 서버 중 하나에 다시 전달
    5. DNS 해석기는 해당 요청을 Route53 이름 서버에 전달
    6. 도메인(`example.com`)의 호스팅 영역에서 해당 레코드(`www.example.com`)를 찾아 IP주소를 DNS 해석기에 전달
    7. DNS 해석기는 IP 주소를 해석하여 그 값을 웹 브라우저로 반환
    8. 웹 브라우저는 얻은 IP 주소로 해당 요청을 전송
    9. 해당 IP에 있는 웹 페이지를 웹 브라우저에게 반환하여 사용자가 볼 수 있게 됨

![](https://velog.velcdn.com/images/choon0414/post/4226a4db-2dbe-4061-81b0-46a690238a03/image.png)


<br> 

#### [참고자료]
[Amazon Route 53은 무엇인가요? - Amazon Route 53](https://docs.aws.amazon.com/ko_kr/Route53/latest/DeveloperGuide/Welcome.html)
[AWS Route 53 이란 ?](https://velog.io/@tootb/AWS-Route-53-이란)
[[AWS] Route53란?](https://velog.io/@hahaha/AWS-Route53란)

---

# **7. NACL (Network Access Control List)**

## Network ACL이란?

- 네트워크 액세스 제어 목록, 서브넷 내/외부의 트래픽 제어를 위한 방화벽 역할을 하는 VPC의 선택적 보안 계층이다.
- 서브넷 단위로 제어하며 인스턴스 단위의 제어는 불가능
- 다양한 서브넷 연동 가능
- NACL을 사용해도 추가 요금이 부과되지는 않음

![](https://velog.velcdn.com/images/choon0414/post/63b4a48d-d411-477d-bd69-ff90342b97f9/image.png)


![](https://velog.velcdn.com/images/choon0414/post/aba2ce65-b1db-44ac-8384-ef5bf30c7c39/image.png)



## 특징

- 인바운드 규칙과 아웃바운드 규칙으로 나뉨
- NACL은 여러 서브넷에 적용 가능
- 허용 규칙 뿐 아니라 거부 규칙도 생성 가능
- 규칙에 우선순위를 정할 수 있음
- Stateless함
    - 상태를 저장하지 않아 인바운드/아웃바운드를 한 번 통과한 트래픽이어도 여전히 아웃바운드/인바운드의 규칙을 적용 받음

<br> 

#### [참고자료]
[네트워크 ACL을 사용하여 서브넷에 대한 트래픽 제어 - Amazon Virtual Private Cloud](https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/vpc-network-acls.html)
[AWS NACL이란 ?](https://velog.io/@ragnarok_code/AWS-NACL이란)

---

# **8. Security Group**

## Security Group이란?

- EC2 인스턴스 또는 다른 리소스에 대한 인바운드(외부 → 인스턴스) 및 아웃바운드(인스턴스 → 외부) 트래픽을 제어하는 가상 방화벽
- 프로토콜 유형과 포트, IP 등을 지정할 수 있음

![](https://velog.velcdn.com/images/choon0414/post/ca2f1cea-d5cb-4cfa-a1b2-267594be6ca9/image.png)


## 특징

- 보안 그룹은 인바운드 규칙과 아웃바운드 규칙으로 나뉨
- 허용 규칙만 생성 가능 → Deny 불가능
    - 블랙리스트처럼 특정 IP만 골라 차단 불가능
- 하나의 인스턴스에는 최대 5개까지의 Security Group 동시 적용 가능
- Stateful함
    - 상태를 저장하여 한 번 인바운드를 통과하는 트래픽은 아웃바운드의 규칙 적용을 받지 않음
    - 상태를 저장하여 한 번 아웃바운드를 통과하는 트래픽은 인바운드의 규칙 적용을 받지 않음
        
        ex) 인바운드에 80번 포트를 허용하고 아웃바운드를 none으로 둘 경우, 80포트 요청이 오면 인바운드에서 톨과가 되고 상태가 기억되기 때문에 아웃바운드 적용 없이 나갈 수 있음
        

## Network ACL vs Security Group

- Security Group이 인스턴스의 접근 제어 목록이라면 Network ACL은 서브넷을 오가는 모든 트래픽을 제어하는 역할을 한다.
- **Network ACL**
    - 서브넷 상자 위에 위치함
    - 서브넷에 오가는 트래픽 제어
- **Security Group**
    - 서브넷 상자 내에 위치함
    - 인스턴스의 트래픽을 제어함
    - 여러 인스턴스가 하나의 Security Group을 함께 쓰거나 각자 따로 사용할 수도 있음
    
![](https://velog.velcdn.com/images/choon0414/post/e4bebc8e-b91d-434b-a5ea-f45b22aa27fb/image.png)

    
<br> 

#### [참고자료]
[보안 그룹을 사용하여 AWS 리소스에 대한 트래픽 제어 - Amazon Virtual Private Cloud](https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/vpc-security-groups.html)
[[AWS] 📚 VPC 개념 & 사용 - 보안 설정 [Security Group / NACL]](https://inpa.tistory.com/entry/AWS-📚-VPC-개념-사용-보안-설정-Security-Group-NACL)
[[VPC] NACL과 Security Group의 차이점은 무엇일까??](https://potato-yong.tistory.com/84)
