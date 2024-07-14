__[🔑키워드]__ `Nginx` `tomcat` `3tier-Architecture`

# 웹 서비스의 흐름
## 종류
### 클라이언트
- 서비스를 이용하기 위해 네트워크를 통해 요청을 보내는 주체
	ex) 크롬, 사파리, 익스플로러 등의 웹 브라우저
    
### 웹 서버
- 클라이언트의 요청에 따라 HTML, CSS, JS, 이미지 등의 정적 파일을 응답으로 제공하는 소프트웨어
- HTTP 프로토콜을 사용해 클라이언트와 통신
	ex) Nginx, Apache 등 
    
### WAS(Web Applicaion Server)
- 클라이언트의 요청에 대해 동적인 처리를 담당
- 웹 서버와 달리 어플리케이션 로직을 실행할 수 있음
- 회원가입, 로그인 등의 로직을 처리하고, DB 연동, 트랜잭션 관리, 보안, 로깅 등의 기능을 제공함
	ex) Tomcat, JBoss, WebLogic, WebSphere 등
    
### DB
- 정보를 체계적으로 저장, 관리하고 검색할 수 있는 시스템
- 대규모 데이터의 저장과 검색을 처리할 수 있음
<br>

## 요청과 응답
**웹 서비스**는 `클라이언트` -> `웹 서버` -> `WAS` -> `DB` 순으로 요청이 되고
`DB` -> `WAS` -> `웹 서버` -> `클라이언트` 순으로 응답이 된다.
![](https://velog.velcdn.com/images/choon0414/post/01b1c6ad-5945-42cb-8719-4094dd0aaa34/image.png)


---

## 웹 서버는 왜 사용하는가?
**WAS만으로도 웹 서버의 기능까지 모두 수행이 가능**한데 왜 웹 서버를 붙여 함께 사용할까? 
이유는 다음과 같다.
- __WAS의 부담을 줄이기 위해__
    - WAS는 이미 동적인 작업을 처리하는 것만도 작업량이 많기 때문에 정적인 파일처리를 웹 서버에 넘겨 부하를 줄일 수 있다.
    - 정적 컨텐츠 처리와 동적 작업을 분리해 처리함으로써 각각의 성능을 향상시킬 수 있다.
- __로드밸런싱__
    - 백엔드에 여러 WAS가 배포되어 있는 경우 웹 서버는 들어오는 요청들을 여러 서버에 분산시켜 서버 부하를 줄일 수 있다. -> 코인의 도커스웜??과 비슷한 느낌인가
- __보안__ 
    - 웹 서버의 리버스 프록시 역할을 통해 외부와 내부를 격리할 수 있다.
    - WAS가 직접적으로 공격받는 것을 방지하여 보안 수준을 높일 수 있다.
    
<br>

#### [참고자료]
[왜 WAS와 Web Server를 같이 사용하는걸까?](https://hstory0208.tistory.com/entry/%EC%99%9C-WAS%EC%99%80-Web-Server%EB%A5%BC-%EA%B0%99%EC%9D%B4-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94%EA%B1%B8%EA%B9%8C)

---

# 1. 3tier-Architecture
## 3tier-Architecture(3계층-구조)란?
- 어떠한 플랫폼을 __`클라이언트` - `웹/어플리케이션` - `DB`__ 3계층으로 나누어 별도의 논리적/물리적 장치에 구축 및 운영을 하는 형태
![](https://velog.velcdn.com/images/choon0414/post/fc300c3b-64fa-4253-98ae-ed278f600f3c/image.png)

### 1) 프레젠테이션 계층
- 사용자가 직접 마주하게 되는 계층
- 주로 사용자 인터페이스(인터넷 브라우저 등)를 지원
- `프론트엔드` 라고도 부름 (HTML, Javascript, CSS 등)

### 2) 어플리케이션 계층
- 요청되는 정보를 특정 규칙을 바탕으로 처리 및 가공하거나 데이터 계층에 요청을 보내는 계층
- 동적인 데이터를 제공하는 비즈니스 로직 계층 또는 트랜잭션 계층
- `백엔드` 라고도 부름 (PHP, Java, Ruby 등)

### 3) 데이터 계층
- DB에 접근하여 데이터를 읽기와 쓰기 작업을 관리
- 주로 DBMS가 이 계층에 해당함
- `백엔드` 라고도 부름 (MySQL, MongoDB 등)

## 3계층-구조의 장단점
### 장점
- 각 계층이 논리적/물리적으로 분리되어 있어 업무 분담이 가능함
- 여러 대의 서버로 나누어 각 계층을 동작시킬 수 있음 -> 서버의 부하 감소

### 단점
- 단일 계층으로 사용하는 것에 비해 관리가 더 필요함
- 장애가 발생 가능한 구간이 늘어남

<br><br>__[참고자료]__
[3계층-구조(3tier-Architecture) 이해하기](https://jaws-coding.tistory.com/9)
[3계층 아키텍처란?](https://www.ibm.com/kr-ko/topics/three-tier-architecture)

---

# 2. Nginx

## Nginx란?
- Nginx는 경량 웹서버로 정적 파일을 serving하는 web server나 요청을 따른 서버로 전달하는 reverse proxy server로 활용된다.

## Nginx 구조
- master process: 설정 파일을 읽고 유효성 검사를 함
- worker process: 요청을 처리함
- event-driven 모델 사용
- 요청을 효율적으로 분배하기 위해 OS에 의존적인 메커니즘을 사용함
- worker process의 개수는 설정 파일에서 정의되며, 정의된 프로세스 개수와 사용가능하는 CPU 코어 수에 맞게 자동으로 조정된다.

## Nginx의 장점
### 1) 높은 성능과 적은 메모리 사용
- Nginx는 비동기 I/O 처리 방식을 사용해 높은 성능을 제공함 -> 대규모 웹 사이트에서도 빠른 응답시간 보장
- 적은 메모리 사용량 -> 서버 운용 비용 절감 가능
### 2) 리버스 프록시 사용 가능
#### 리버스 프록시란?
- 프록시 : 인터넷 접속 시 보안상의 문제로 직접 통신을 주고받지 않는 경우, 중계기를 통한 통신을 수행하는 기능
- 포워드 프록시 : 클라이언트 - 인터넷 사이의 영역 중계
  ![](https://velog.velcdn.com/images/choon0414/post/23f01025-6000-49d9-944e-b0a6fa94602d/image.png)
- 리버스 프록시 : 인터넷 - 백엔드 사이의 영역 중계
  ![](https://velog.velcdn.com/images/choon0414/post/3488d85c-dfdc-4028-87d3-3999dd889c87/image.png)
### 3) SSL 지원
- SSL : 웹 사이트와 사용자 간의 통신을 암호화하고 보안을 유지하는 데 사용되는 프로토콜
- HTTPS로 알려진 보안 HTTP 프로토콜 기반 기술 -> Nginx는 HTTPS 인증서를 제공해줄 수 있다.
### 4) 데이터 압축
### 5) 비동기 처리
- Nginx는 이벤트 루프 방식을 사용해 높은 성능을 제공함


## Nginx 설정
- Nginx는 nginx.conf 라는 config 파일에 설정된 값을 통해 동작함
	- nginx.conf 위치 : 대부분 `/etc/nginx/nginx.conf`, `/user/local/nginx/conf/nginx.conf`, `/usr/local/etc/nginx/nginx/conf` 와 같은 경로에 있다.

<br>

#### [참고자료]
[Nginx란 무엇인가?](https://blog.naver.com/gi_balja/223028077537)
[[NGINX] NGINX 란?](https://jammdev.tistory.com/217)

---

# 3. tomcat
## tomcat이란?
- 자바 웹 어플리케이션 서버, 웹 어플리케이션을 실행하기 위한 자바 서블릿 및 JSP를 지원함

## tomcat의 장점
### 1) 경량화된 서버
### 2) 개발과 배포의 용이성
- 많은 개발자가 참여하여 다양한 플러그인과 라이브러리를 제공받을 수 있음
### 3) 높은 호환성
- Java Servlet, JavaServer Pages 등 JEE 기술을 기반으루 구현되어 있어서 다양한 플랫폼 및 운영체제에서의 호환성을 보장함
### 4) 보안성
- SSL/TLS 암호화를 지원함
- 웹 어플리케이션의 인증 및 권한 부여를 위한 보안 기능 제공
### 5) 오픈소스 및 무료
- 오픈 소스로 개발되고 있음

<br><br>__[참고자료]__
[What is an Apache Tomcat?](https://cordcat.tistory.com/104)
[아파치? 톰캣?](https://cheershennah.tistory.com/54)

---

# 4. AWS로 3tier-architecture 구현 방법
실패해서 이번주중으로 다시 시도해보고 정리하겠습니다..!
