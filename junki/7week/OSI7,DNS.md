# OSI 7 Layer
> 
- 컴퓨터 네트워크 및 통신을 7개의 레이어(계층) 으로 표현한 모델
- 각 계층은 하위 계층의 기능을 활용해 역할 수행 - 상위 계층으로 처리 결과 전달

![image (22)](https://github.com/user-attachments/assets/1d1cff17-0419-4cff-bd70-aabee0ccd298)

## Physical
> 
- 장치를 연결하기 위한 매체의 물리적인 사항 정의
  - 전압, 주기, 시간, 전선의 규격, 거리 등
- 주요 단위 : Bits
- ex) 케이블/안테나/RF 등 전송 매체, 허브, 리피터

- 어떤 방식으로 데이터 전달할지?

### Hub
- `Physical Layer` 단위에서 다수의 기기들을 연결해주는 장치
- 특징
  - 에러/충돌/디바이스 별 제어 기능 없음
  - 받은 내용 그대로 전달 - 무조건 broadcast
  

## Data Link
> 
- 물리적인 통신을 제어하여 디바이스간의 통신 및 전송을 안정화하기 위한 프로토콜
- 주요 단위 : Frame
- ex) Mac Address, Switch
- 특징
  - `CSMA/CD(Carrier-Sense Multiple Access with Collision Detection)` 방식을 활용해서 각 디바이스간의 통신 원활히 연결
    - 신호를 확인해서 신호가 없을때만 보냄
    - 충돌을 확인하는건 CD - 충돌이 발생하면 랜덤한 시간을 기다림
  - 대상을 구별해서 디바이스간의 통신 지원(`Unicast`)

- 해결 못한 문제
  - 로컬 네트워크 외부로 통신 불가능

### MAC Address
- 네트워크 인터페이스에 부여된 고유 주소
  - 데이터가 지정한 대상에게 잘 전달될 수 있도록 대상 식별에 사용 
- 2개의 바이트 단위로 6개 나열
  - ex) 00:1A:2B:3C:4D:5E
- 두 파트로 구분
  - 첫 3개는 `OUI` : 제조사에 부여된 고유 식별자
  - 나머지 3개는 `NIC` : 네트워크 인터페이스 별 고유 번호
- 고유의 값이며 변하지 않음

![image](https://github.com/user-attachments/assets/2720a10e-99f3-4052-a44f-b6996c5e958e)

![image (1)](https://github.com/user-attachments/assets/800aeed5-42be-4075-b709-f0a74445d024)


## Network Layer
>
- 여러 노드의 경로를 찾고 올바르게 전달 될 수 있는 기능과 수단 정의
- 주요 단위 : 패킷
- ex) Router, IP, ARP
- 특징
  - 서로 떨어진 Local Network간의 통신 지원 &rarr; Inter Network(Internet)
  - 노드들을 거쳐서 목적지까지 도달할 수 있는 방법 지원

- 해결 못한 문제
  - 한번에 하나의 통신만 가능 &rarr; 여러 어플리케이션 동시에 통신 안됨
  - 패킷 순서 보장 X
  - 유실 방지 불가


### IP Address
> 
- Internet Protocol 상에서 통신 주체를 식별하기 위한 아이디
- 종류
  - IPv4 : 32Bits(약 43억개) - IP의 최대한의 활용을 위해 사설IP와 공인IP로 분류
  - IPv6 : 128Bits(완전 많음)
- 수시로 변동 가능

### CIDR(Classless Inter Domain Routing)
- 네트워크 주소와 호스트 주소를 표시해줌
- 호스트 주소 비트만큼 IP 주소 보유 가능
- 네트워크 비트가 같으면 안나가도 됨

### Subnet Mask
- 어느 부분이 호스트 비트인지, 어느 부분이 네트워크 비트인지 구분해주는 Mask
  - AND 연산 활용

![image (2)](https://github.com/user-attachments/assets/5feb1f54-0bc5-4ed7-a9cd-23f4f944d272)

서브넷 마스크가 `255.255.255.0` 일 때 IP 주소와 비교하여 둘 다 1인 경우(AND 연산)를 뽑아내서 네트워크 주소를 파악

### 라우터 
- 네트워크간에 패킷을 주고 받는 장치
- IP 대역별 최적 경로 수집 및 관리
  - 라우트 테이블로 관리
  - 특정 IP 주소가 대상인 패킷의 전달을 요청받을 때 해당 경로로 요청
- 로컬 네트워크는 자신의 로컬 네트워크 주소가 아니면 `Router`로 전달
- `Router`는 패킷을 Frame안에 넣어서 최적 경로에 따른 다른 `Router`로 전달

### ARP(Address Resolution Protocol)
- IP로 MAC 주소를 찾는 프로토콜
- 순서 
  - Broadcast(MAC Address FF:FF:FF:FF:FF:FF)로 IP 요청
  - 응답 받은 IP MAC Address 기반으로 MAC 확정 후 테이블 저장
![image (3)](https://github.com/user-attachments/assets/29d9577f-736b-4b7d-b1f4-b5d80d56ef70)


## Transport Layer
>
- 통신 주체끼리 데이터 전달의 신뢰성 확보 방법 정의
- 주요 단위 : 세그먼트
- ex) TCP/UDP
- 특징
  - Network Layer로 성립된 통신 위에서 실질적인 활용을 위한 다양한 기능 정의
    - 패킷 순서 보장, 에러 처리, 포트 기반 분할

### 세그먼트
- TCP/UDP의 데이터 전달 단위
- 패킷에 담김
- `TCP 세그먼트` 구성
  - Port(Source/Destination)
  - Sequence/Acknowledgement Number : 통신 주체끼리 데이터 주고 받았는지 확인
  - Flags : 세그먼트 목적 정의
  - Window Size : 세그먼트를 만든 주체가 얼마나 많은 데이터를 받을지 전달
  - Urgent Pointer : 세그먼트 중요도 설정
  - 기타
  - 실제 데이터

### Port
- IP 프로토콜에서 패킷을 올바른 프로세스로 라우팅 하기 위한 논리적 단위
- TCP Port / UDP Port 로 구분
  - 0 ~ 65535 까지 정수 범위
- Well Known Port : 주로 서버에서 사용하는 어플리케이션/프로토콜 별로 미리 지정된 포트
  - 주로 사용에 따라 공통적으로 사용 &rarr; 바꿀 수 있음
- Epgemeral Port : 클라이언트에서 사용하는 포트로 연결할 떄마다 임의 지정

### TCP(Transmission Control Protocol)
- 패킷의 전달 과정에서 순서 보장, 유실되지 않도록 보장하는 통신 규약
  - 패킷 안에 `세그먼트`를 담아주고 받아서 로직 처리
- 연결 지향
  - 지속적으로 채널 수립 &rarr; 전달 여부 확인, 무결성 확인
  - 지속적으로 무결성을 확보하는 과정에서 느리고 복잡한 과정 필요
- ex) 웹페이지(HTTP, HTTPS), 이메일, 파일 전송, SSH 등

![image (4)](https://github.com/user-attachments/assets/d4de38af-6dc8-4a0a-a877-960c4bcec950)

- 보내고 확인하는 형식 rarr; 느릴듯?

#### TCP Handshake
- TCP Protocol에서 통신을 수립하고 서로를 인식하는 첫 과정
- 보통 Three Way Handshake로 부르며 3가지 과정으로 구분
  - Syn(Synchronize) : 첫 요청으로 사용할 첫 Client Sequence Number(CS) 전달
  - Syn-Ack : Syn에 대한 응답으로 CS+1과 Server Sequence Number(SS) 전달
  - ACK : 마지막 단계로 연결이 수립되었음을 알려줌 &rarr; CS+1과 SS+1을 전달

![image (5)](https://github.com/user-attachments/assets/67be4a9e-5077-48aa-b16b-06d4db7210b2)


### UDP(User Datagram Protocol)
- 빠르고 간단하게 데이터를 주고 받을 수 있는 방법
- Connectionless
  - 연결지향과 달리 데이터의 무결성, 순서, 전달여부 체크 안함
  - 패킷이 순서대로 오지 않거나, 중복되거나, 아예 전달되지 않을 수 있음
- 빠르고 간단
- ex) 스트리밍, 보이스톡, 온라인 게임

![image (6)](https://github.com/user-attachments/assets/6c87b301-dc82-44ed-aac0-cd65e0aa71ef)

- TCP 세그먼트보다 용량이 작음
- 유실 되어도 그냥 넘김


### 주요 프로토콜(TCP/UDP)
- TCP
  - HTTP/HTTPS : TCP / 80, 443
  - FTP(File Transfer Protocol) : TCP / 20, 21
  - SSH(Secure Shell) : TCP / 22
  - DNS(Domain Name System) : TCP / 53
- UDP
  - DNS : UDP / 53
  - DHCP(Dynamic Host Configuration Protocol) : UDP / 67, 68
  - VoIP(Voice over IP) : UDP / 5060


## Session Layer
>
- 통신 주체끼리 연결이 유지될 수 있는 방법 정의
- 예전의 컴퓨팅 환경에서 Layer 1,2,3,4 이외의 차원에서 지속적인 연결(세션)이 수립될 수 있는 방법 제공
- 현재도 Layer 4 이상의 추가적인 차원에서 지속적인 연결(세션)이 수립할 수 있는 방법 포함
  - ex) HTTP Cookie
- 몇몇 프로토콜은 Session Layer 구현 안함 - ex) FTP &rarr; 그래서 파일 다운받다가 와이파이 바꾸면 이어서 다운이 안되는듯

![image (7)](https://github.com/user-attachments/assets/120787cc-2af3-4fa2-936c-790ae5c54dee)


## Presentation Layer
>
- 받은 데이터를 해석하는 방법 정의 
  - 파싱, 압축 해제, 복호화 등 Application Layer에서 사용할 수 있는 형식으로 변환 담당

![image (8)](https://github.com/user-attachments/assets/e7f451bf-536a-46ee-8859-703c1ecb9f7b)

## Application Layer
>
- 실제 받은 데이터를 처리하는 방법 정의
  - 데이터를 가지고 무엇을 어떻게 처리할지에 관한 레이어
- ex) HTTP
  - Method(Get/Post/Put/Delete/Head/Option/Patch)
  - Status Code(2xx,3xx,4xx,5xx)
  - Header

![image (9)](https://github.com/user-attachments/assets/a4e4fe40-8ff7-4844-87e1-190412785902)



# DNS(Domain Name System)
>
- 사람이 읽을 수 있는 도메인이름을 기계가 읽을 수 있는 `IP 주소`로 변환
- 사람이 읽을 수 있는 문자열과 Internet 프로토콜 기반 정보를 매칭시켜주는 시스템
- 도메인 : 대상의 IP 주소등의 정보와 매핑되는 사람이 알아볼 수 있는 문자열
- 레코드 : 도메인이 어떤 방식으로 데이터와 매칭되는지 정의
  - 다양한 레코드 종류가 있음
  - ex) A 레코드 : IPv4 주소, MX 레코드 : 메일 서버 
- Domain Zone : 도메인의 정보를 담은 레코드의 집합
- Zone File : Domain Zone 정보를 저장한 텍스트 파일
- DNS Query : 주어진 도메인에 해당하는 정보를 요청하는 쿼리
- Name Server : DNS Query 를 Zone File 기반으로 응답할 수 있는 서버
  - Authoritative : DNS 정보 원본을 가진 가장 최상위 NS 서버
  - None-Authoritative : Authoritative NS 서버를 조회하여 정보를 보관하고 있거나 응답(캐시)
- DNS Resolver : 사용자와 NS 서버 사이에 위치한 서버 - 실제 유저의 요청에 따라 IP 주소 등의 정보 확보

## 구성
- 계층 구조
  - 최상위 도메인부터 차례대로 계층구조로 구성 
  - 실제 레코드는 가장 마지막 계층에서 보관 및 처리
  
![image (10)](https://github.com/user-attachments/assets/eef70b99-5041-463e-9404-f5d9ee059429)
  
![image (11)](https://github.com/user-attachments/assets/98799c66-9b66-4379-a79b-aa4e3ecf15ae)

- DNS Root
  - DNS 계층 구조의 최상위 레벨
    - DNS Query 수행 시 최초로 조회
    - 다음 단계인 TLDs의 Zone File을 가진 NS서버의 주소 정보 가짐
  - Root Hints File
    - DNS Root의 주소를 담은 파일
    - 각 DNS Resolver에 하드코딩 

- TLDs(Top Level Domains)
  - DNS 계층 구조의 두번째 레벨
  - 실질적으로 정보를 가지고 있는 최상위 레벨
  - ex) .com, .org, .net, .info
  - 종류
    - Country Code : 각 나라에 할당된 두자리 코드 &rarr; .kr, .jp, .uk, .ai 등
    - Sponsored : 사설 조직이나 기관에 할당 &rarr; .edu, .gov, .mil 등
    - New Generic : 기타 &rarr .app, .tech, .xyz, .blog 등
  - 관리 주체 : TLDs Registry(각 회사/ 나라 등)

- NS 서버
  - 실제 도메인의 레코드를 가진 서버
    - 이 NS 서버의 주소를 TLDs에 등록하면 클라이언트의 DNS 쿼리에 따라 NS 서버에 도착
  - 해당 도메인 및 서브도메인들이 어떤 프로토콜의 어떤 주소로 매핑되는지에 관한 `Zone File` 보유 


## 도메인 등록
- 도메인 등록 대행을 통해 등록
  - 도메인 등록 대행 : `ICANN` 에게 인증받고 TLDs Registry와 협의하여 도메인 등록의 권한을 가짐
  - ex) 가비아, GoDaddy, Cafe24, AWS Route53 등
- 등록할 때 TLDs Registry에 자신이 원하는 NS서버 주소 등록


# 끝!
[AWS 강의실](https://www.youtube.com/watch?v=DufRXdDF9zI&list=PLfth0bK2MgIYuZguspeqGFdXXVckWwrlg&index=1)
