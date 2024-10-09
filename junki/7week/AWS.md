# 클라우드 컴퓨팅
> IT 리소스를 인터넷을 통해 `온디맨드`로 제공, 사용한 만큼 비용을 지불 &rarr; 빌려쓰기
`온디맨드` : 수요에 반응

## 서버-클라이언트 아키텍쳐
> 중간에 서버를 두고 클라이언트들에게 정보를 전달하는 방식

`데이터센터` : 어플리케이션 서버를 호스팅하는 실제 시설
	- 컴퓨팅 시스템을 위한 하드웨어
    - 네트워킹 장비
    - 전원공급장치
    - 전기 시스템
    - 백업 발전기
    - 환경 제어장치(냉각장치, 팬 등)
    - 운영 인력
    - 기타 인프라

  - 문제점
    - 운영 비용이 많이 필요함
    - 한 번 구매하면 수요에 상관없이 계속 보유
    - 느린 구축시간

## 클라우드
### 장점
- 자본 비용을 가변 비용으로 대체
- 규모의 경제로 얻게 되는 이점
- 용량 추정 불필요
  - 최대 피크가 아닐때는 낭비가 됨 &rarr; 클라우드를 통해 `온디맨드`로 조절 가능
- 속도 및 민첩성 개선 &rarr; 몇 번의 클릭으로 리소스 확보 가능, 개발비용 절감
- 데이터 센터 운영 및 유지 관리에 비용 투자 불필요
- 빠른 확장성

# 클라우드 컴퓨팅의 모델

## 클라우드 컴퓨팅 모델

```
애플리케이션의 구성
- application
- OS(Windows/Linus)
- Computing(CPU + RAM)
- Storage(HDD/SSD)
- Network(랜카드/랜선)
```

`Iaas` : Infrastructure as a Service
- 인프라만 제공(Network, Storage, Computing)
- OS를 직접 설치하고 필요한 소프트웨어를 개발해서 사용
- 가상의 컴퓨터 하나 임대
- ex) AWS EC2

`Paas` : Platform as a Service
- 인프라 + OS + 기타 프로그램 실행에 필요한 부분 제공(Network, Storage, Computing, OS+Runtime)
- 바로 코드만 올려서 돌릴 수 있도록 구성
- ex) firebase, Google App Engine

`Saas` : Software as a Service
- 인프라 + OS + 필요한 소프트웨어 제공 (Network, Storage, Computing, OS+Runtime, APP)
- 서비스 자체 제공
- 다른 세팅 없이 서비스만 이용
- ex) gmail, dropbox, slack, google docs


## 클라우드 컴퓨팅 배포 모델

### 공개형(클라우드)
- 모든 부분이 클라우드에서 실행
- 낮은 비용
- 높은 확장성 

### 혼합형(하이브리드)
- 폐쇄형과 공개형의 혼합
- 폐쇄형에서 공개형으로 전환하는 과도기에 사용
- 혹은 폐쇄형의 백업

### 폐쇄형
- 높은 수준의 커스터마이징 가능
- 초기 비용이 비쌈
- 유지보수 비용이 비쌈
- 높은 보안

# AWS의 구조
> AWS(Amazon Web Services)
클라우드 서비스 점유율 1위 &rarr; 많은 레퍼런스

서비스 - 리전 - 가용영역

## 글로벌 서비스
> 리전에 속해있지 않은 서비스 (IAM, CloudFront

## 리전 서비스 
> 특정 지역에 속해있는 서비스
- AWS의 서비스가 제공되는 서버의 물리적 위치
- 전세계에 흩어져 있으며 큰 구분으로 묶여 있음
- 각 리전에는 고유의 코드 부여
  - 서울(ap-northeast-2)
  - 미국 동부(us-east-1)
- 리전별로 가능한 서비스가 다름

![image](https://github.com/user-attachments/assets/f80d697a-a48e-400d-ac64-79790a54af19)

### 리전을 선택할 때 고려할 점
- 지연 속도
- 법률(데이터, 서비스 제공 관련) &rarr; ex) 특정 지역에서 하는 서비스는 그 지역에만 저장되어야 한다
- 사용 가능한 `AWS` 서비스

### 가용영역(Availability Zone(AZ))
- 리전의 하부 단위
  - 하나의 리전은 반드시 2개 이상의 `가용 영역`으로 구성
- 하나 이상의 데이터 센터로 구성
- 리전간의 연결은 매우 빠른 전용 네트워크로 연결
- 반드시 물리적으로 일정 거리 떨어져있음
  - 여러 재해에 대비/보안
- 각 계정별로 `AZ`의 코드와 실제 위치는 다름
  - 보안 및 한 `AZ`로 몰림을 방지

### 엣지 로케이션
- `AWS`의 `CloudFront(CDN)` 등의 여러 서비스들을 가장 빠른 속도로 제공(캐싱)하기 위한 거점
- 전 세계 여러 장소에 흩어져 있음
- 요청했던 데이터를 일정시간 저장 &rarr; 다른 사람들도 동일한 데이터 접근 가능 &rarr; 빨라짐!

### 글로벌 서비스, 리전 서비스
- 글로벌 서비스 : 데이터 및 서비스를 전 세계의 모든 인프라가 공유
  - CloudFront
  - IAM
  - Route53
  - WAF
  
- 지역 서비스 : 특정 리전을 기반으로 데이터 및 서비스 제공
  - 대부분 서비스
  - `S3` : 전 세계에서 동일하게 사용할 수 있으나 데이터 자체는 리전에 종속 

### ARN(Amazon Resource Name)
> AWS의 모든 리소스의 고유 아이디

- 형식 : `"arn:[parittion]:[service]:[region]:[account_id]:[resource_type]/resource_name/(qualifier)"`
- 맨 끝에 와일드카드(*)를 사용해 다수의 리소스 지정 가능


# AWS 계정

## 루트 유저
- 생성한 계정의 모든 권한을 가짐
- 생성시 만든 이메일 주소
- 복구 힘듦
- 관리용으로 이용
- AWS API 호출 불가
- `MFA(Multi-factor Authentication)` 사용 권장 &rarr; 일회용 패스워드를 이용한 로그인

## IAM 유저
- `IAM` : Identity and Access Management)을 통해 생성한 유저
- 만들 때 주어진 아이디로 로그인
- 기본 권한 x &rarr; 권한 부여 필요
- 어플리케이션 등 가상의 주체 대표 가능
- AWS API 호출 가능
  - AccessKey : 아이디
  - Secret Access Key : 패스워드
- 관리를 제외한 모든 작업은 관리용 IAM User를 만들어 사용
- 다른 계정과의 리소스 공유
- `글로벌 서비스` - 리전 서비스가 아님

### 구성
- 사용자
  - 실제 AWS를 사용하는 사람 혹은 어플리케이션
  
- 그룹
  - 사용자의 집합
  - 그룹에 속한 사용자는 그룹에 부여된 권한 행사 가능

- 정책
  - 사용자와 그룹, 역할이 무엇을 할 수 있는가
  - JSON 형식
  
- 역할
  - AWS 리소스에 부여, 무엇을 할 수 있는지 정의
  - 다른 사용자가 역할을 부여받아 사용
  - 다른 자격에 대해서 신뢰관계 구축 가능
  - 역할을 바꿔가며 서비스 사용 가능

![image (1)](https://github.com/user-attachments/assets/3d655091-a1d2-48da-9693-07dcaa3a400d)

### 모범 사용 사례
- 루트 사용자 사용 X
- 불필요한 사용자 생성 X
- 그룹과 정책 사용
- 최소한의 권한만 허용
- MFA 활성화
- 역할 활용
- 자격 증명 보고서 활용


# 가상화
> 단일 컴퓨터의 하드웨어 요소를 `가상 머신`이라고 하는 다수의 가상 컴퓨터로 분할할 수 있도록 해주는 기술

## 역사

### 1세대 : 완전 가상화(Fully Emulated)
- 모든 시스템 요소가 에뮬레이터 안에서 돌아감
- `에뮬레이터` : 프로그램이 있는것처럼 구현
- 엄청 느림
![image (2)](https://github.com/user-attachments/assets/ae89cc77-b3f1-4c1a-ac81-d9a6be7d4c66)

### 2세대 : Paravirtualization
- Guest OS 는 하이퍼 바이저와 통신
- `하이퍼바이저` : OS와 하드웨어 사이에 존재하는 가상화 매니저
- 속도 향상
- 여전히 에뮬레이터 필요

![image (3)](https://github.com/user-attachments/assets/a098fa6d-e136-4eaa-a424-5cc0492b74fa)

### 3세대 : Hardware Virtual Machine(HVM)
- 하드웨어에서 직접 가상화 지원
- Guest OS가 하드웨어와 직접 통신 &rarr; 속도 빠름

![image (4)](https://github.com/user-attachments/assets/b1296d97-a17c-475b-b310-2cc3efb8600f)

# EC2(Elastic Compute Cloud)
> 안전하고 크기 조정이 가능한 컴퓨팅 파워를 클라우드에서 제공
간편하게 필요한 용량을 얻고 구성 가능
컴퓨팅 자원에 대한 포괄적인 `제어권` 제공

## 사용
- 서버 구축
- 어플리케이션을 사용하거나 호스팅

## 특성
- 초 단위 온디맨드 가격 모델
- 빠른 구축 속도, 확장성
- 다양한 구성방법
	- 다양한 용도에 최적화된 서버 구성
    - 다양한 과금 모델
- 여러 AWS 서비스와 연동
	- 오토스케일링, Elastic Load Balancer, CloudWatch

## 구성
- 인스턴스 : 클라우드에서 사용하는 가상 서버 &rarr; CPU, 메모리, 그래픽카드 등 연산을 위한 하드웨어 담당
- EBS : Elastic Block Storage - 클라우드에서 사용하는 가상 하드디스크
- AMI : EC2 인스턴스를 실행하기 위한 정보를 담고 있는 이미지
- 보안 그룹 : 가상의 방화벽 

## 인스턴스 유형
- 각 인스턴스 별로 사용 목적에 따라 최적화
  - 메모리 위주, CPU 위주, 그래픽카드 위주
- 타입별로 이름 부여
  - ex) t타입, m타입, inf타입 등
- 세대별로 숫자 부여
- 아키텍처 및 사용 기술에 따라 접두사
![image (5)](https://github.com/user-attachments/assets/7761f793-1827-4bcc-affe-391218babd5f)

## 인스턴스 크기
- 인스턴스의 cpu 개수, 메모리 크기, 성능 등으로 결정
![image (6)](https://github.com/user-attachments/assets/c4dde20b-3e99-4f78-bff4-98162cf2c713)

## EBS
> Elastic Block Store - 영구 블록 스토리지
가상 하드 드라이브

- EC2 인스턴스가 종료 되어도 유지 가능
![image (7)](https://github.com/user-attachments/assets/a3e1c261-d4ff-4dbf-b9db-db7df22a0f98)
  - 네트워크로 묶여있기 때문에 업그레이드/다운그레이드가 쉬움
  - 하나의 인스턴스가 여러개의 `EBS`를 가질 수 있음  
- 인스턴스 정지 후 재 기동 가능 &rarr; 인스턴스를 정지할 경우 `EBS`의 요금만 내게 됨
- 하나의 `EBS`를 여러 `EC2`에 장착 가능
- 루트 볼륨으로 사용시 `EC2` 종료되면 같이 삭제
  - 설정을 통해 따로 존속 가능
- `EC2`와 같은 가용영역에 존재
- 5가지 타입 제공
![image (8)](https://github.com/user-attachments/assets/3bcaf778-c083-4531-9aba-1b870b8d5aea)


## Snapshot
> 
- 특정 시간 `EBS` 상태의 저장본
- 필요시 스냅샷을 통해 특정 시간의 `EBS` 복구 가능
- `S3`에 저장
  - 증분식 저장 &rarr; 변화한 부분만 저장


## AMI
> Amozon Machine Image
- `EC2` 인스턴스를 실행하기 위해 필요한 정보를 모은 단위
  - OS, 아키텍쳐 타입, 저장공간 용량 등
- `AMI`를 사용하여 `EC2`를 복제하거나 다른 리전/계정으로 전달 가능
- 스냅샷 기반

### 구성
- 1개 이상 `EBS 스냅샷`
- 사용 권한
- 블록 디바이스 매핑 &rarr; `EC2` 인스턴스를 위한 볼륨 정보

### EBS 기반
> 네트워크로 연결
- 속도가 느림
- 인스턴스가 사라져도 남아있음
- 스냅샷 기반 루트 디바이스 생성

### 인스턴스 저장 기반
> 인스턴스 안에 `AMI`가 들어가있음
- 속도가 빠름
- 인스턴스가 사라지면 같이 사라짐
- `S3`에 저장된 템플릿 기반 생성

![image (9)](https://github.com/user-attachments/assets/399a7ab7-ca99-45f9-b57b-71ef5f15bb3b)

- 스냅샷을 찍어서 `S3`에 저장하고 그 스냅샷을 기반으로 `AMI`를 만듦

## 생명주기
![image (10)](https://github.com/user-attachments/assets/ea1118f5-a761-49ab-8bf4-329ae4b0b3b5)
- pending : 준비 상태, `EBS` 등 준비
- running : 실제 `EC2`를 사용할 수 있는 단계
- rebooting : 다시 시작 &rarr; 퍼블릭 IP 변동 X
- shutting-down : 종료
- terminated : 삭제
- stopping : 중지 &rarr; 중지 중에는 인스턴스 요금 미청구 - `EBS`나 다른 구성요소(Elastic IP등)는 청구됨, 재시작시 퍼블릭 IP 변경, `EBS` 사용 인스턴스만 중지 가능
- stopped : 중지된 상태
- 최대 절전모드 : 종료할 때 메모리에 있던 정보들을 하드디스크로 옮겨서 다시 켜도 메모리에 있던 작업을 그대로 할 수 있음 

![image (11)](https://github.com/user-attachments/assets/03dfee5b-d4ea-4607-9744-8d960f9e76d3)


# 오토스케일링
> 애플리케이션을 모니터링하고 용량을 자동으로 조정, 최대한 저렴한 비용

## 스케일링
> 인스턴스 혹은 컴퓨팅 파워를 늘리는 것

### Vertical Scale(Scale Up)
> `EC2`의 크기를 늘리는 것 &rarr; 성능과 비용이 비례하지 않음  
탄력성이 부족

### Horizontal Scale(Scale Out)
> `EC2`의 규모를 늘리는 것 &rarr; `Vertical`에 비해 저렴, 성능과 비용이 비례
- 소프트웨어적인 고민을 많이 해야함
- 수요에 따라 조절 가능 - 유연하게 대처 가능

## 목표
- 정확한 수의 `EC2` 인스턴스를 보유하도록 보장
  - 최소 인스턴스 및 최대 인스턴스 숫자 설정 가능
  - 다양한 스케일링 정책 적용 가능 - ex) CPU 부하에 따라 인스턴스 크기 늘리기
- 가용 영역에 인스턴스를 골고루 분산 - 하나의 가용영역이 터졌을 때를 대비 

## 구성
- 시작 구성
  - `EC2`의 타입, 사이즈
  - `AMI`
  - 보안그룹, Key, IAM
  - 유저 데이터
- 모니터링 : 언제 실행 + 상태 확인
  - 어떤 상황에서 `오토 스케일링`을 실행할지 설정
  - CloudWatch ELB와 연계
- 설정 : 얼마나 어떻게 실행
  - 원하는 인스턴스 숫자
  - ELB와 연동

- `EC2` 하나가 죽었을때 오토스케일링이 감지하고 시작 구성을 통해서 다시 만들어줌

# ELB(Elastic Load Balancer)

## 로드 밸런싱
> 모든 인스턴스를 활용하기 위해서는 나뉘어진 모든 IP 주소들을 알아야 하는 번거로움이 있는데, 이를 해결하기 위해 고안된 방법
- 다수의 인스턴스를 트래픽을 하나의 경로로 받아서 분산해주는 서비스
- 들어오는 애플리케이션 트래픽을 여러 대상에 자동으로 분산
- 단일 가용 영역 또는 여러 가용 영역에서 다양한 애플리케이션 부하 처리
- `Health Check` : 직접 트래픽을 발생시켜 인스턴스 상태 체크 가능
- 지속적으로 IP 주소가 바뀜, IP 고정 불가능 - 도메인 기반

- 종류
  - Application LB
    - 트래픽을 모니터링해서 라우팅 가능
  - Network LB
    - 빠름
    - TCP 기반 빠른 트래픽 분산
    - Elastic IP 할당 가능
  - Classic LB
    - 옛날 타입
  - Gateway LB
    - 트래픽 먼저 체크 
    - 가상 어플라이언스 배포/확장 관리
    - 트래픽을 먼저 검사/분석/인증함
    - 일반적인 LB랑 역할이 다름

- 대상 그룹 : ALB가 __라우팅 할 대상의 집합__
  - 구성
    - 3 + 1가지 종류
      - Instance
      - Private IP
      - Lambda
      - ALB : 다른 ALB와 연결 가능 - 특수한 경우
  - 프로토콜(HTTP, HTTPS, gRPC 등)
    - ALB 상에서 인증서를 가지고 HTTPS 금방 처리 가능
  - 기타 설정 : 트래픽 분산 알고리즘, 고정 세션 등

![image (12)](https://github.com/user-attachments/assets/69c8ddc2-6795-48d2-8909-563b8e1f5672)
>AWS Cloud 안에 `VPC`가 있고 다양한 가용 영역 안에 `ELB`를 통해 분산해주는 서비스가 일반적

# EFS(Elastic File System)
> 간단하고 확장 가능하며 탄력적인 완전관리형 파일 시스템 제공
온디맨드 방식, 파일을 추가하고 제거할 때 자동으로 확장/축소

![image (13)](https://github.com/user-attachments/assets/3accbbbe-6d39-4c37-837d-1f96a3344ccf)

- 공유 스토리지 서비스
  - 따로 용량을 지정할 필요 없이 사용한 만큼 용량 증가 - `EBS`는 미리 크기 지정
- 몇 천개의 동시 접속 유지 가능
- 데이터는 여러 AZ에 나누어 분산 저장
- Private Service : AWS 외부에서 접근 불가능
  - Direct Connect나 VPN을 사용해야 접근할 수 있음!
- 리눅스만 됨
- Mount Target : `EFS`를 대변하는 출장소 느낌

## 모드

### 퍼포먼스 모드
- General Purpose : 가장 보편적인 모드, 거의 대부분의 경우 사용
- Max IO : 매우 높은 `IOPS`가 필요한 경우 &rarr; 빅데이터, 미디어 처리 등
  - `IOPS` : 1초간 처리할 수 있는 입출력 횟수

### Throughput 모드
- Bursting Throughput : 낮은 Throughput일 때 크레딧을 모아서 높을때 사용 - 일반적
- Provisioned Throughput : 미리 지정한 만큼을 확보하고 사용



# 사설 IP(Private IP)
>한정된 IP주소를 최대한 활용하기 위해 IP주소를 분할하고자 만든 개념 - IPv4 최대개수가 43억개, IPv6는 훠얼씬 많음
- 사설망
  - 사설망 내부에는 외부 인터넷 망으로 통신이 불가능한 사설 IP로 구성
  - 외부로 통신할 떄 통신 가능한 공인 IP로 나누어 사용
  - 보통 하나의 망에는 사설 IP를 부여받은 기기들과 `NAT` 기능을 갖춘 `Gateway`로 구성


# NAT(Network Address Translation)
> 사설 IP가 공용 IP로 통신할 수 있도록 주소 변환
- 종류
  - Dynamic NAT : 1개의 사설 IP를 가용 가능한 공인 IP로 연결 &rarr; 공인 IP 그룹에서 현재 사용 가능한 IP 연결
![image (14)](https://github.com/user-attachments/assets/0c91766f-7b9a-4c8d-a0eb-8b44918de5b3)
  - Static NAT : 하나의 사설 IP를 고정된 하나의 공인 IP로 연결
![image (15)](https://github.com/user-attachments/assets/425c6e44-80a6-4df0-88ae-780ae653c407)
  - PAT(Port) : 많은 사설 IP를 하나의 공인 IP로 연결 - 포트 기반으로 어떤 private IP로 갈지 결정
![image (16)](https://github.com/user-attachments/assets/97b36bec-8eea-45d5-a377-9ff69d5d9004)


# CIDR(Classless Inter Domain Routing)

> 
- IP 주소의 영역을 여러 네트워크 영역으로 나누기 위해 IP를 묶는 방식
- 여러개의 사설망을 구축하기 위해 망을 나누는 방법

- CIDR Block : IP 주소의 집합
- CIDR Notation : CIDR Block을 표시하는 방법
  - 네트워크 주소와 호스트 주소로 구성
  - 각 호스트 주소 숫자만큼의 IP 주소를 가진 네트워크 망 형성 가능

# 서브넷 
>
- 네트워크 안의 네트워크
- 큰 네트워크를 잘게 쪼갠 단위 - 쪼개서 관리
- 일정 IP주소의 범위 보유 - 작은 단위로 나눈 후 각 서브넷 할당
![image (17)](https://github.com/user-attachments/assets/7b7596b4-f91c-4545-9628-edca111444d0)

# VPC(Virtual Private Cloud)
> 사용자의 AWS 계정 전용 가상 네트워크 - 다른 가상 네트워크와 논리적 분리(외부와 격리)
- 원칙적으로 Public Internet에서 접근 불가능
- `Internet Gateway`를 통해서 접근해야함 - AWS Service에서도 불가능 &rarr; 접근하려면 게이트웨이를 통해 퍼블릭에 갔다가 AWS Service로 가야함
- 가상의 데이터 센터
- 외부에 격리된 네트워크 컨테이너 구성 가능
  - 원하는대로 사설망 구축 가능
- 리전 단위

## 구성요소
- 서브넷 : VPC에 할당된 IP를 더 작은 단위로 분할 - 하나의 서브네은 하나의 가용영역 안에 위치
  - AWS의 사용 가능 IP 숫자는 5개를 제외하고 계산 &rarr; `네트워크 주소`, `VPC Router`, `DNS Server`, `미래를 위해 하나 남김`, `네트워크 브로드캐스트 어드레스`
  - 퍼블릭 서브넷 : 외부에서 인터넷을 통해 연결할 수 있는 서브넷 - 웹서버, WAS 등
  - 프라이빗 서브넷 : 외부에서 인터넷을 통해 연결할 수 없는 서브넷 - DB, 로직서버 등
- 인터넷 게이트웨이 : VPC가 외부의 인터넷과 통신할 수 있도록 경로를 만들어줌 - 확장성, 고가용성 확보 - 라우트 테이블에서 경로 설정 후 접근 가능
- 라우트 테이블 : 트래픽이 어디로 가야할지 알려주는 이정표 - 가장 구체적인쪽으로 감 - 서브넷 마스크가 가장 큰쪽
- NACL/보안그룹 
  - 보안그룹 : 인스턴스에 대한 인바운드/아웃바운드 트래픽 제어
    - 방화벽 역할
    - 기본적으로 모든 포트 비활성화 - 설정을 해줘야 함 - 허용만 가능(막을 수 없음!)
    - 인스턴스 단위
    - Stateful : 한번 들어왔으면 나갈때는 검사를 안함
  - NACL(Network access control list) : 
    - 방화벽 역할
    - 서브넷 단위 
    - Port, IP를 직접 Deny 가능 - 막을 수 있음
    - 허용, 거부 가능
    - Stateless : 들어올 때, 나갈 때 둘 다 검사
![image (18)](https://github.com/user-attachments/assets/970b8239-9c63-44ab-98a8-5532cac02285)

    - 규칙 우선순위가 있기 때문에 이렇게 되어 있으면 그냥 전부 허용됨 &rarr; 막고싶은 것들을 규칙 우선순위를 둬야함
![image (19)](https://github.com/user-attachments/assets/2c32bb9a-8461-427f-9a41-06bfd1e94671)

- NAT Instance/ NAT Gateway : VPC의 프라이빗 서브넷에 있는 인스턴스에서 인터넷에 쉽게 연결할 수 있도록 지원
  - 서브넷 단위 - public subnet에 있어야함
![image (20)](https://github.com/user-attachments/assets/6d15a2a8-4adb-4e84-bebb-02c2f5df1fa8)
 
- Bastion Host : 외부에서 사설 네트워크에 접속할 수 있도록 경로 확보
  - private 인스턴스에 접근하기 위한 `EC2` 인스턴스
![image (21)](https://github.com/user-attachments/assets/a7ec9f39-e10a-4d17-bd88-0a8d9d5e8acf)
  - 관리가 번거로움

- VPC Endpoint


# S3(Simple Storage Service)
> 최고의 확장성과 데이터 가용성 및 보안과 성능을 제공하는 __객체 스토리지 서비스__

- 객체 스토리지 서비스 : 파일 보관만 가능 &rarr; 어플리케이션 설치해서 쓰는건 안됨
- 글로벌 서비스 &rarr; 데이터는 리전에 저장
- 무제한 용량 - 하나의 객체는 5TB까지 가능

## 버킷
>
- `S3`의 저장공간 - 디렉토리/폴더 느낌
- 버킷이름은 고유값

## 구성
- Owner : 소유자
- Key : 파일 이름
- Value : 파일 데이터 - 없을 수 있음
- Version Id : 파일 버전
- Metadate : 파일 정보 메타데이터
- ACL : 파일 권한 

## 내구성
- 최소 3개의 가용영역에 데이터 분산 저장
- 99.99999999999% 내구성

## 보안 설정
- 기본적으로 Private
- 보안 설정은 객체 단위, 버킷 단위로 구성 가능 &rarr; 거의 버킷 단위
- MFA를 활용해 객체 삭제 방지 가능
- 버저닝을 통해 파일 관리 가능 
- 액세스 로그 생성 및 전송 가능 - 생성, 삭제 로그 가능

## 권한 관리
- Identity-based policies(자격 증명 기반 정책)
    - 자격증명(IAM유저, 그룹, 역할)에 부여하는 정책
    - 자격증명이 무엇을 하는지 허용
- Resource-based policies(리소스 기반 정책)
  - 리소스(S3, SQS,VPC Endpoint 등) 등에 부여하는 정책
  - 리소스에 누가 무엇을 할 수 있는지 허용
  
- 버킷 정책 : 버킷 단위로 부여되는 __리소스 기반 정책__
  - `언제 어디서 누가 어떻게 무엇을` 할 수 있는지 정의 가능

## 버전관리
- 객체의 생성, 업데이트, 삭제의 모든 단계 저장 &rarr; 삭제의 경우 진짜 삭제가 아니고 삭제 마커 추가
- 버킷 단위 활성화(기본적 비활성화) - 중지 가능, 한번 활성화 하면 비활성화 안됨
- 수명주기 관리와 연동 가능
- MFA 인증 후 삭제 기능 - 실수 방지
- 모든 버전에 대한 비용 발생


## 객체 잠금
- Write Once, Read Many(WORM) 모델 활용 객체 저장 - 변경은 불가능하지만 읽는건 여러번 가능
- 고정된 시간, 무기한으로 객체의 삭제/덮어쓰기 방지
- 규정 준수 및 객체 보호 위해 사용
- 버전 활성화 필요
- 종류
  - 보관모드 : 일정 기간동안 수정 방지
    - 규정 준수 모드 : 누구도 잠금 설정 변경, 객체 삭제 불가능
    - 커버넌스 모드 : 특별한 권한 없이 삭제 혹은 잠금 설정 변경 불가능
  - 법적 보존
    - Hold에 객체 부여, Hold가 존재하는 한 객체 삭제, 수정 불가능 &rarr; 해제 전까지 뭐 못함
    - 제한 기간 X

## 객체 암호화
- On Transit : SSL/TLS(HTTS)
  
- At Rest(Server Side)
  - SSE S3 : S3에서 암호화
  - SSE KMS : KMS 서비스로 암호화 - S3 키가 아닌 KMS키로 암호화 - KMS 통해 직접 관리 가능 - 역할 분담 가능
  - SSE C : 클라이언트에서 제공한 암호로 암호화 - 키를 S3에서 저장하지는 않음
  
- Client Side : 클라이언트가 직접 암호화 &rarr; 강력하지만 귀찮음
  

### 정적 컨텐츠
- 서버에 저장된 파일이 모든 사용자에게 동일하게 전달
- 캐싱 가능
- HTML/Javascript 등으로 구성
- ex) 이미지, 글, 뉴스 등

### 동적 컨텐츠
- 시간, 사용자, 입력 등에 따라 내용이 변경되는 컨텐츠
- 매번 서버에 요청하여 내용을 구성
- PHP, JSP, ASP.net 등으로 서버 처리
- ex) 로그인, 게시판, 댓글 등

## 정적 호스팅
> 
- `S3`를 사용해서 정적 웹 컨텐츠 호스팅
- 별도의 서버 없이 웹사이트 호스팅 기능
- 고가용성/장애 내구성 확보
- 사례 : 대규모 접속이 예상되는 페이지, 홍보, 회사 웹사이트 등

- Route 53에서 보유한 도메인으로 연결 가능
- CloudFront와 연동 필요


# 끝!
[AWS 강의실](https://www.youtube.com/watch?v=JjiYqBl2328&list=PLfth0bK2MgIan-SzGpHIbfnCnjj583K2m)

# 블로깅
[AWS 블로깅](https://velog.io/@dradnats1012/%EC%89%BD%EA%B2%8C-%EC%84%A4%EB%AA%85%ED%95%98%EB%8A%94-AWS-%EA%B8%B0%EC%B4%88-%EA%B0%95%EC%A2%8C-%EC%A0%95%EB%A6%AC)
