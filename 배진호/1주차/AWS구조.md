# 1주차 : AWS구조


## 1. VPC (Virtual Private Cloud)
AWS 클라우드 내에 **논리적으로 격리된 가상 네트워크**

IP 주소 범위 지정, 서브넷 및 게이트웨이 추가, 보안 그룹 연결

다른 가상 네트워크와 논리적으로 분리됨

![image](https://github.com/johnny19991006/BCSD_InfraStudy/assets/72592302/5471bbf3-10cf-4ffb-b906-4c4bca667e9a)
![image](https://github.com/johnny19991006/BCSD_InfraStudy/assets/72592302/754a56fc-3af6-4436-9723-26c3fb50060a)



## 2. CIDR (Classless Inter-Domain Routing)
IP 주소 및 서브넷을 정의하는 표준

**네트워크 범위** 를 나타내는 표기법 (예: 192.168.0.0/24)

VPC 내의 인스턴스 및 리소스에 할당되는 IP 주소 결정



## 3. EC2 (Elastic Compute Cloud)
**가상 서버(인스턴스)** 를 생성하고 관리하는 서비스

필요에 따라 인스턴스 시작, 중지, 크기 조정 가능

다양한 유형의 인스턴스 제공 (예: CPU 최적화, 메모리 최적화)



## 4. S3 (Simple Storage Service)
**객체 스토리지 서비스**

대용량 데이터를 안전하게 저장 및 관리

각 버킷에 파일을 저장하며 고유한 이름 부여

높은 내구성과 가용성 제공



## 5. CDN (Content Delivery Network)
AWS의 CloudFront 서비스

**전 세계에 분산된 엣지 로케이션을 통해 콘텐츠 전달**

웹사이트, 동영상, API 등의 콘텐츠를 사용자에게 더 가깝게 배포

지리적, 물리적으로 떨어진 사용자에게 콘텐츠를 빠르게 제공

![image](https://github.com/johnny19991006/BCSD_InfraStudy/assets/72592302/c911f5cf-1ce5-4887-9b0f-79101793bef8)



## 6. Route 53
DNS(도메인 이름 시스템) 웹 서비스

**도메인 이름을 IP 주소로 변환**

도메인 등록, DNS 라우팅, 상태 확인 및 트래픽 관리 지원

고가용성 및 확장성 제공

![image](https://github.com/johnny19991006/BCSD_InfraStudy/assets/72592302/0e71fb16-a649-446d-b628-0f4bb5b948f5)



## 7. NACL (Network ACL)
**VPC의 서브넷 수준에서 트래픽 제어**

들어오는 트래픽과 나가는 트래픽에 대해 허용 및 거부 규칙 정의

스테이트리스(stateless) 특성, 각 요청 별도 허용 또는 거부 필요



## 8. Security Group
**EC2 인스턴스 수준에서 트래픽 제어**

인바운드(들어오는) 및 아웃바운드(나가는) 트래픽 규칙 설정

스테이트풀(stateful) 특성, 허용된 인바운드 트래픽에 대해 자동 응답 트래픽 허용

![image](https://github.com/johnny19991006/BCSD_InfraStudy/assets/72592302/fe690d8c-9832-437d-87ad-c2431cd3c1ee)

![image](https://github.com/johnny19991006/BCSD_InfraStudy/assets/72592302/9ce88dd2-3c39-42b0-aea4-6362543e03fd)



---
(추가로 학습한 내용)

## 1. IAM (Identity and Access Management)
AWS 리소스에 대한 액세스를 안전하게 제어하는 서비스

**사용자, 그룹, 역할을 생성 및 관리하고 권한을 부여**

세부적인 권한 설정으로 AWS 리소스 접근 제어 가능

MFA(다중 인증)를 지원하여 보안 강화

정책 기반 접근 제어를 통해 유연한 권한 관리 가능

![image](https://github.com/johnny19991006/BCSD_InfraStudy/assets/72592302/b98c8486-5747-4ee3-807f-00ed4f01f3f9)



## 2. ECS (Elastic Container Service)
컨테이너화된 애플리케이션을 쉽게 실행, 관리, 확장

**Docker 컨테이너를 AWS에서 관리** 할 수 있게 해주는 서비스

클러스터를 사용하여 다중 컨테이너 인스턴스를 관리

Fargate를 통해 서버리스 방식으로 컨테이너 실행 가능

높은 확장성과 관리 용이성 제공

**EC2와 차이** : EC2는 AWS의 가상 서버를 제공하는 서비스로, 사용자가 직접 서버를 관리.
반면, ECS는 컨테이너 오케스트레이션 서비스로, 컨테이너화된 애플리케이션을 클러스터에서 자동으로 배포하고 관리

![image](https://github.com/johnny19991006/BCSD_InfraStudy/assets/72592302/2b34323e-7a75-4af3-abee-0f8877fa3d4e)



## 3. RDS (Relational Database Service)
클라우드에서 관계형 데이터베이스를 쉽게 설정, 운영, 확장

MySQL, PostgreSQL, MariaDB, Oracle, SQL Server 등 지원

자동 백업, 소프트웨어 패치, 모니터링 등 관리 작업 자동화

고가용성과 내구성을 위해 Multi-AZ 배포 지원

데이터베이스 성능 최적화를 위한 다양한 옵션 제공



## 4. CloudWatch
**AWS 리소스 및 애플리케이션 모니터링 서비스**

로그 파일, 지표, 이벤트를 수집, 추적, 모니터링

경보(알람)를 설정하여 특정 조건 시 알림 받기 가능

대시보드를 통해 실시간 모니터링 및 시각화 지원

리소스 사용량을 최적화하고 성능 문제를 식별 가능



## 5. Lambda
서버를 프로비저닝하거나 관리할 필요 없이 코드를 실행

이벤트 기반으로 동작하며, **특정 이벤트 발생 시 코드 실행**

다양한 프로그래밍 언어 지원 (Node.js, Python, Java 등)

확장성 및 고가용성 제공, 사용한 만큼만 비용 지불

API Gateway와 연동하여 서버리스 애플리케이션 구축 가능



## 6. CloudFront
**글로벌 콘텐츠 배포 네트워크 서비스 (CDN)**

엣지 로케이션을 통해 콘텐츠를 사용자에게 빠르게 전달

웹사이트, 동영상, API 등을 전 세계에 고속으로 제공

SSL/TLS 인증서로 안전한 콘텐츠 전송 지원

실시간 분석과 모니터링을 통해 성능 최적화 가능

![image](https://github.com/johnny19991006/BCSD_InfraStudy/assets/72592302/0d289b97-f78c-4f04-88fb-7ff79558a752)



## 7. DynamoDB
빠르고 유연한 NoSQL 데이터베이스 서비스

일관된 저지연 시간과 자동 확장성 제공

키-값 및 문서 데이터 모델을 지원

내구성과 가용성을 보장하는 Multi-AZ 리플리케이션 제공

DynamoDB Streams를 통해 실시간 데이터 처리 가능



## 8. API Gateway
API를 쉽게 생성, 배포, 관리할 수 있는 서비스

RESTful 및 WebSocket API 지원

Lambda, EC2, DynamoDB 등과 연동 가능

API 트래픽 관리, 인증 및 권한 부여, 모니터링 기능 제공

확장성과 비용 효율성을 동시에 제공



## 9. CodeCommit
안전하고 확장 가능한 Git 기반 소스 코드 리포지토리 서비스

팀 협업을 위한 코드 저장소 제공

기존 Git 도구와의 호환성 보장

암호화된 데이터 전송 및 저장으로 보안 강화

변경 사항 모니터링 및 알림 기능 제공

![image](https://github.com/johnny19991006/BCSD_InfraStudy/assets/72592302/19fb714e-b183-499d-9ff5-89b90aeec496)



## 10. CodeDeploy
애플리케이션을 다양한 컴퓨팅 서비스에 **자동 배포** 하는 서비스

EC2 인스턴스, Lambda 함수, 온프레미스 서버 등에 배포 가능

배포 중 애플리케이션 가동 시간을 최대화

다양한 배포 전략 지원 (In-place, Blue/Green)

배포 상태 모니터링 및 롤백 기능 제공

![image](https://github.com/johnny19991006/BCSD_InfraStudy/assets/72592302/5c7d0e24-6944-4f0b-8635-ab98d9e88d05)



## 11. CodePipeline
**애플리케이션 빌드, 테스트, 배포를 자동화하는 CI/CD 서비스**

소스 코드 변경 시 자동으로 파이프라인 실행

다양한 AWS 서비스 및 타사 도구와 통합 가능

병렬 및 시퀀셜 작업을 통한 유연한 워크플로우 정의 가능

배포 파이프라인의 각 단계를 시각적으로 모니터링 가능

![image](https://github.com/johnny19991006/BCSD_InfraStudy/assets/72592302/08c5d431-b9c8-4112-83de-17bb0d552528)



