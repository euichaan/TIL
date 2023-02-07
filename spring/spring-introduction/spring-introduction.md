# 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
스프링을 배우는 이유 - 실무에서 제대로 동작하는 웹 어플리케이션을 개발하기 위해서  
- 스프링 프로젝트 생성  
- 스프링 부트로 웹 서버 실행  
- 회원 도메인 개발  
- 웹 MVC 개발  
- DB연동 - JDBC, JPA, 스프링 데이터 JPA  
- 테스트 케이스 작성  
  
https://start.spring.io/ - 스프링 부트 기반으로 프로젝트를 만들어주는 사이트  
  
## 스프링 부트 라이브러리
spring-boot-starter-web  
- spring-boot-starter-tomcat : 톰캣(웹서버)  
- spring-webmvc : 스프링 웹 MVC  
  
spring-boot-starter-thymeleaf : 타임리프 템플릿 엔진(View)  
  
spring-boot-starter(공통) : 스프링 부트 + 스프링 코어 + 로깅  
spring-boot
  - spring-core  
spring-boot-starter-logging
  - logback(구현체), slf4j(인터페이스)  
  
## 스프링 부트가 제공하는 Welcome Page 기능
`static/index.html`을 올려두면 Welcom page 기능을 제공한다.  
  
웹 애플리케이션에서 첫 번째 진입점 - Controller.  
  
컨트롤러에서 리턴 값으로 문자를 반환하면 뷰 리졸버(viewResolver)가 화면을 찾아서 처리한다.  
- 스프링 부트 템플릿엔진 기본 viewName 매핑  
- resources:temlates + {viewName} + .html  
  
## 스프링 웹 개발 기초  
웹을 개발한다는 것? -> 세 가지가 있다.  
1. 정적 컨텐츠  
2. MVC와 템플릿 엔진  
3. API  
  
### 정적 컨텐츠
정적 컨텐츠 : 파일을 그대로 웹 브라우저에 내려주는 것  
MVC와 템플릿 엔진 : 서버에서 변형을 해서 내려주는 방식  
API : JSON 이라는 데이터 포맷으로 클라이언트에게 데이터를 전달.  
  
localhost:8080/hello-static.html  
내장 톰캣 서버가 요청을 받음 -> 스프링에게 넘김 -> hello-static 관련 컨트롤러 찾아봄  
-> resources: static/hello-static.html을 찾음 -> 웹 브라우저에 반환.  
  
### MVC와 템플릿 엔진
MVC :  Model, View, Controller  
관심사를 분리해야 한다. 역할과 책임.  
뷰는 화면을 그리는 데 집중해야 함. 컨트롤러와 모델은 비지니스 로직, 내부 처리 중심.  

localhost:8080/hello-mvc  
내장 톰캣 서버를 먼저 거침 -> helloController에 매핑 되어있는 것을 보고 호출.  
-> viewResolver가 동작. templates/hello-template.html -> 변환 한 HTML을 반환.  
  
### API
@ResponseBody : HTTP BODY 부분에 데이터를 직접 넣어주겠다. viewResolver를 사용하지 않음.  
객체 반환하고 ResponseBody로 반환하면 기본은 JSON으로 반환한다.  
  
localhostL8080/hello-api -> 내장 톰캣 서버 -> 스프링에게 전달 -> @ResponseBody가 붙어있음 ->  
HttpMessageConverter가 동작(기존에는 viewResolver가 동작)  
  
hello 객체를 JSON 포맷으로 바꿔서 웹 브라우저에 응답.  
클라이언트의 `HTTP Accept 헤더`와 `서버의 컨트롤러 반환 타입 정보` 둘을 조합해서 HttpMessageConverter` 선택.  
  
## 회원 관리 예제 - 백엔드 개발  
데이터 : 회원ID, 이름  
기능 : 회원 등록, 조회  
아직 데이터 저장소가 선정되지 않음(가상의 시나리오)  
  
컨트롤러 : 웹 MVC의 컨트롤러 역할  
서비스 : 핵심 비즈니스 로직 구현  
리포지토리 : 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리  
도메인 : 비즈니스 도메인 객체. 예) 회원,주문,쿠폰 등등 주로 데이터베이스에 저장하고 관리됨  
  
아직 데이터 저장소가 선정되지 않아서, 우선 인터페이스로 구현 클래스를 변경할 수 있도록 설계  
초기 개발 단계에서는 구현체로 가벼운 메모리 기반의 데이터 저장소 사용(MemoryMemberRepository)  
  
`@AfterEach` : 한번에 여러 테스트를 실행하면 메모리 DB에 직전 테스트의 결과가 남을 수 있다.  
이전 테스트 때문에 다음 테스트가 실패할 가능성이 있다. @AfterEach를 사용하면 각 테스트가 종료될 때 마다  
이 기능을 실행한다.  
  
서비스는 비즈니스에 의존적으로 설계.  
빌드 될 때 테스트 코드는 실제 코드에 포함되지 않는다.  












