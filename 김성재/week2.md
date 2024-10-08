## 이론
### 3tier란?
![](https://velog.velcdn.com/images/seongjae6751/post/39359acf-2c47-4961-a82f-cbba71376a1f/image.png)
어떤 플랫폼을 3계층으로 나누어 별도의 논리적/물리적 장치에 구축 및 운영하는 형태를 3tier architecture라고 한다.

웹 서버 운영을 예로 들면 서버 한대에 한꺼번에 모든 기능을 구축 하는 것이 아닌 데이터를 저장하고 읽는 데이터 계층, 데이터를 처리하는 어플리케이션 계층, 데이터를 표현해주는 클라이언트(프레젠테이션) 계층과 같이 각각 3계층으로 나누어 각각의 기능으로 별도의 논리적/ 물리적인 장치에서 운영하는 방식이다.

이 밖에도 2계층, 4계층 처럼 다양하게 운영할 수도 있고 이 경우 다층 구조라고도 표현 가능하다.

* 프레젠테이션 계층(presentation tier)
  사용자가 직접 마주하는 계층으로 주로 사용자 인터페이스(브라우저 등)을 지원하며 이 계층은 gui나 프론트엔드라고 부른다. 따라서 이 계층에서는 사용자 인터페이스와 관계없는 데이터를 처리하는 로직은 포함하지 않는다. 주로 웹 서버를 예시로 들 수 있고, html, js, css 등이 이 계층에 해당 된다.
* 어플리케이션 계층(application tier)
  이 계층에서는 프레젠테이션 계층에서 요청되는 정보를 어떠한 규칙을 바탕으로 처리하고 가공하는 것을 담당한다. 동적인 데이터를 제공한다. 비즈니스 로직 계층 또는 트랜잭션 계층이라고 하며 첫번째 계층에서 이 계층을 바라볼 때에는 서버처럼 동작하고 세번째 계층의 프로그램에 대해서는 마치 클라이언트처럼 행동한다.
* 데이터 계층(data tier)
  데이터 계층은 데이터 베이스와 데이터 베이스에 접근하여 데이터를 읽거나 쓰는 것을 관리하는 것을 포함한다. 주로 dbms가 이 계층에 해당된다. 데이터 계층 또한 백엔드라고 부른다.

### nginx, 아파치 , 톰캣
#### 웹서버란?
* 서버란 어떤 컴퓨터가 서버 역할을 하게 도와주는 소프트웨어로 대표적으로 아파치와 nginx, IIS(윈도우)가 있다. 웹서버란 이 컴퓨터들 서버들 중에서도 웹사이트를 제공하는 서버로 만들어주는 그런 서비스라고 할 수 있다.
* 원래 서버 컴퓨터에 저장되어 있는 파일들을 서버의 특정 폴더, 디렉토리 등에 넣어두면 이 폴더를 외부에서 접근 가능하도록 개방해서 서버에 지정된 웹사이트 주소로 접속시 이것을 받아갈 수 있게 하는 것,
  한 마디로 브라우저들이 읽을 수 있는 기본적인 파일들(html, css, 자바스크립트 파일, 각종 이미지)을 서버에서 사용자의 컴퓨터로 보내주는 것이 기본적인 웹서버의 역할이다.

#### 정적웹과 동적웹
* 정적 웹: 블로그 페이지나 회사 소개 페이지처럼 그 안의 내용들이 바뀔 일이 없는 웹페이지를 고정된 HTML, CSS, 자바스크립트로 제공하는 말하자면 완제품들을 갖다놓은 편의점 진열대라고 할 수 있다.
* 동적 웹: 게시판이나 쇼핑몰처럼 사용자와의 상호작용에 따라 내용이 변경되는 웹페이지를 말한다. 이러한 웹페이지는 데이터베이스의 데이터를 사용하여 사용자 요청에 따라 실시간으로 생성된다. 예를 들어, 식당에서 요리사가 주문에 맞춰 음식을 즉석에서 만드는 것과 같다.
* 이런 동적 웹을 제공하는 것도 아파치와 Nginx의 모듈로 할 수는 있다.(Apache와 PHP, MySQL을 연동시켜서 동적인 PHP를 제공하는 방법 등이 있음 -> Apache, PHP, MySQL 조합이라 해서 APM이라 함(물론 NginX로도 가능))
* Apache와 Nginx는 정적 웹일 때는 편의점 알바도 하고 PHP로 만든 동적 웹일때는 요리사도 한다고 할 수 있다. 또는 식당 매니저로도 일하기도 하는데 톰캣, Node.js, Django 내장 서버 등의 전문 요리사들과 손님들 사이에서 서빙이나 매니지먼트를 담당하기도 한다.

#### WAS(Web Application Servver), 톰캣
* 이름에 application이 추가된 것을 보면 알 수 있듯이 단순히 뭘 가져다 주는 것이 아니라 뭔가 프로그래밍 된 걸 더 한다. 동적 사이트를 전문적으로 처리해주는 것이라고 보면 된다.
  아파치나 NginX같은 웹서버도 PHP 같은 분식 종류는 요리할 수 있지만 스프링 같은 고급한정식이나 일식으로 넘어가면 톰캣 같은 전문 요리사 WAS의 손을 빌려야 한다. 자바 바이트 코드로 컴파일 되는 언어들에 쓰이는 걸로 가장 흔히 사용되는 톰캣이나 Jetty 등이 있다.
* tomcat 사용법: 스프링으로 개발된 웹 애플리케이션을 WAR 파일로 빌드하여 Tomcat 서버의 특정 폴더에 배치하고 실행하면 된다. 최근에는 스프링을 Tomcat 내장형 JAR 파일로 배포하기도 한다.
* 정리하면 아파치와 Nginx가 웹서버, 톰캣은 was라고 할 수 있다. 웹서버를 앞에 둬서 방문자들을 응접실에서 접대하게 하고 그 뒤에서 was와 같은 전문 요리사가 요리를 하고 있는 것이다. 사실 톰캣과 같은 일들도 서빙(정적 리소스 제공)을 할 수 있지만 이 외에도 아파치와 NginX는 다양한 기능들을 제공해서 사용한다

#### reverse proxy와 로드밸런싱
* 먼저 reverse proxy를 제공하는데 forward proxy와 반대로 손님들에게서 서버의 정보를 감춰준다.
  서버는 보안상 내부 구조를 감출 필요가 있는데(정적 리소스들이 있는 방은 어디인지 톰캣 같은 요리사들이 동적 요소를 요리하는 주방은 어디인지, 서비스가 몇번 포트로 돌고 있는지 등) Nginx와 같은 애들이 응접실에서 대신 손님을 맞이하고 요리를 내오는 것들을 할 수 있다.
* 그 다음으로는 로드 밸런싱을 제공한다. 여러 서버에 트래픽을 분산시켜 서버의 부하를 조절하는 기능이다. Nginx는 로드 밸런싱 기능을 제공하여 다수의 Tomcat 서버에 요청을 고르게 분배할 수 있다. 손님들이 몰릴 때 여러 요리사(톰캣)에게 분산해서 주문을 넣어주는 역할을 한다고 할 수 있다.

#### 캐싱 & 헬스 체크
* 그 다음으로  Nginx는 캐시 서버로도 사용할 수 있다.자주 요청되는 정적 리소스를 캐싱하여 서버의 응답 속도를 향상시킨다.
* 헬스 체크도 제공하는데 서버의 상태를 주기적으로 확인하여 문제가 발생한 서버를 자동으로 감지하고 대처한다. 이를 통해 서버의 안정성을 유지할 수 있다.

