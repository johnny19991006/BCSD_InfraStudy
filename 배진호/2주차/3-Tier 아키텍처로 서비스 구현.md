# 3주차 : 3-Tier 아키텍처로 서비스 구현 (실습)

서비스를 구현하기 전에 3 tier 아키텍처에 대해 알아보고자 한다.

## 3-Tier 아키텍처 
응용 프로그램을 3개의 주요 계층으로 나누어 관리하는 방식의 구조적인 패턴이다.
<img width="771" alt="image" src="https://github.com/user-attachments/assets/e4e6d456-a193-414f-96e4-2f833728c747">

### 1. 프레젠테이션 계층 (WEB)
- 사용자와 상호작용
- 클라이언트
- Nginx

### 2. 애플리케이션 계층 (WAS)
- 비즈니스 로직 처리
- 프레젠테이션 계층과 데이터 계층 간의 데이터 흐름 조정
- Apach Tomcat, Spring Boot

### 3. 데이터 계층 (DB)
- 데이터베이스와 연결 관리
- CRUD 작업
- MySQL, MongoDB

그러면 왜 쓰는 걸까?
** 각 계층을 독립적으로 관리하여 모듈화와 유지보수성을 높이고 개발과 확장을 용이하게 하기 위해. **


### 실습과정

- EC2 t2.micro(프리티어)를 사용하여 배포하였습니다.

- 8080포트로 접근할 수 있도록 인바운드 규칙을 수정했습니다.

- 인스턴스의 물리적 메모리가 부족하기 때문에 디스크 공간을 임시 메모리로 사용하는 스왑메모리 기능을 사용하였습니다.

- 사용자가 8080포트로 들어오는 것은 번거로우니 nginx로 80포트로 들어오는 것을 8080포트로 리다이렉트해준다.
(리버스 프록시, 정적파일 제공, SSL 인증서로 클라이언트와 암호화된 연결 처리)

- 원래 도메인 kro.kr로 연결해주었으나 Let's Encrypt 에서 kro.kr 인증서 발급을 제한해버려서 도메인을 수정하였습니다.
![image](https://github.com/user-attachments/assets/1b302b4e-4b9f-46cd-a070-826bb338727a)


배포한 서비스 (이전 초록스터디 파일을 재활용하였습니다.)
### https://infrastudy.o-r.kr/

<img width="641" alt="image" src="https://github.com/user-attachments/assets/d901292a-5fe5-4e3a-a687-fa57ed72e4a1">

