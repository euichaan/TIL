# 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술

### 웹 서버(Web Server)

- HTTP 기반으로 동작
- 정적 리소스 제공, 기타 부가기능
- 정적(파일) HTML, CSS, JS, 이미지, 영상
- 예) NGINX, APACHE

### 웹 어플리케이션 서버(WAS)

- HTTP 기반으로 동작
- 웹 서버 기능 포함+ (정적 리소스 제공 가능)
- 프로그램 코드를 실행해서 애플리케이션 로직 수행
    - 동적 HTML, HTTP API(JSON)
    - 서블릿, JSP, 스프링 MVC
- 톰캣, Jetty, Undertow

웹 서버는 `정적 리소스(파일)`, WAS는 `애플리케이션 로직`

WAS는 애플리케이션 코드를 실행하는데 더 특화

자바는 서블릿 컨테이너 기능을 제공하면 WAS

WAS, DB 만으로 시스템 구성 가능

WAS는 정적 리소스, 애플리케이션 로직 모두 제공 가능

WAS가 너무 많은 역할을 담당, 서버 과부하 우려

가장 비싼 애플리케이션 로직이 정적 리소스 때문에 수행이 어려울 수 있음

WAS 장애시 오류 화면도 노출 불가능

### 웹 시스템 구성 -Web Server, WAS, DB

- 정적 리소스는 웹 서버가 처리
- 웹 서버는 애플리케이션 로직같은 동적인 처리가 필요하면 WAS에 요청을 위임
- WAS는 중요한 애플리케이션 로직 처리 전담
- 효율적인 리소스 처리
    - 정적 리소스 많이 사용되면 Web 서버 증설
    - 애플리케이션 리소스가 많이 사용되면 WAS 증설
- WAS, DB 장애시 WEB 서버가 오류 화면 제공 가능

Api 서버만 구축할 때는 WAS 서버만 구축해도 괜찮다.

---

## 서블릿

> 서블릿은 웹 애플리케이션 개발을 위한 자바 기반 서버 사이드 프로그래밍 기술입니다.

서블릿은 클라이언트의 요청을 처리하고, 응답을 생성하는데 사용됩니다. 서블릿은 웹 어플리케이션 서버에 배치되어 웹 클라이언트에서 요청이 들어오면 해당 요청을 처리하고 그에 대한 응답을 반환합니다.
> 

웹 브라우저가 생성한 요청 HTTP 메시지

```html
POST /save HTTP/1.1
Host: localhost:8080
Content-type: application/x-www/form-urlencoded

username=kim&age=20
```

application/x-www-form-urlencoded: form의 내용을 메시지 바디를 통해 전송(key, value 쿼리 파라미터 형식)

서블릿 - 애플리케이션 로직만 구현하면 된다!

### HTTP 요청시

- WAS는 `Request, Response` 객체를 새로 만들어서 서블릿 객체 호출(요청이 다 다르기 때문에)
- 개발자는 Request 객체에서 HTTP 요청 정보를 편리하게 꺼내서 사용
- 개발자는 Response 객체에 HTTP 응답 정보를 편리하게 입력
- WAS는 Response 객체에 담겨있는 내용으로 HTTP 응답 정보를 생성

### 서블릿 컨테이너

- 톰캣처럼 서블릿을 지원하는 WAS를 서블릿 컨테이너라고 함
- 서블릿 컨테이너는 서블릿 객체를 생성, 초기화, 호출, 종료하는 생명주기 관리
- 서블릿 객체는 **싱글톤**으로 관리
    - 공유 변수 사용 주의
    - 서블릿 컨테이너 종료시 함께 종료
- JSP도 서블릿으로 변환 되어서 사용
- 동시 요청을 위한 멀티 쓰레드 처리 지원

---

## 멀티 쓰레드

### 쓰레드

- 애플리케이션 코드를 하나하나 순차적으로 실행하는 것은 쓰레드
- 자바 메인 메서드를 처음 실행하면 main이라는 이름의 쓰레드가 실행
- 쓰레드가 없다면 자바 애플리케이션 실행이 불가능
- 쓰레드는 한번에 하나의 코드 라인만 수행
- 동시 처리가 필요하면 쓰레드를 추가로 생성

### 요청 마다 쓰레드 생성

장점

- 동시 요청을 처리할 수 있다.
- 리소스(CPU, 메모리)가 허용할 때 까지 처리가능
- 하나의 쓰레드가 지연 되어도, 나머지 쓰레드는 정상 동작한다.

단점

- 쓰레드 생성 비용은 매우 비싸다.
    - 고객의 요청이 올 때 마다 쓰레드를 생성하면, 응답 속도가 늦어진다.
- 쓰레드는 컨텍스트 스위칭 비용이 발생한다.
- 쓰레드 생성에 제한이 없다.
    - 고객 요청이 너무 많이 오면, CPU, 메모리 임계점을 넘어서 서버가 죽을 수 있다.

### 쓰레드 풀

풀 안에 미리 쓰레드들을 만들어둔다. 사용이 끝나면 쓰레드 풀에 반납한다.

쓰레드가 이미 다 사용중이라면? `대기 or 거절` 

특징

- 필요한 쓰레드를 쓰레드 풀에 보관하고 관리한다.
- 쓰레드 풀에 생성 가능한 쓰레드의 최대치를 관리한다. 톰캣은 최대 200개 기본 설정(tomcat max connection)

장점

- 쓰레드가 미리 생성되어 있으므로, 쓰레드를 생성하고 종료하는 비용(CPU)이 절약되고, 응답 시간이 빠르다.
- 생성 가능한 쓰레드의 최대치가 있으므로 너무 많은 요청이 들어와도 기존 요청은 안전하게 처리할 수 있다.

WAS의 주요 튜닝 포인트 = 최대 쓰레드(max thread) 수.

클라우드면 일단 서버부터 늘리고, 이후에 튜닝 

쓰레드 풀의 적정 숫자?

성능 테스트

- 최대한 실제 서비스와 유사하게 성능 테스트 시도
- 툴: 아파치 ab, 제이미터, nGrinder

핵심

- **멀티 쓰레드에 대한 부분은 WAS가 처리**
- 개발자가 멀티 쓰레드 관련 코드를 신경쓰지 않아도 됨
- 개발자는 마치 싱글 쓰레드 프로그래밍 하듯이 편리하게 소스 코드를 개발
- 멀티 쓰레드 환경이므로 싱글톤 객체(서블릿, 스프링 빈)는 주의해서 사용

---

정적 리소스

- 고정된 HTML 파일, CSS, JS, 이미지, 영상 등을 제공
- 주로 웹 브라우저

HTML 페이지

- 동적으로 필요한 HTML 파일을 생성해서 전달(JSP, 타임리프와 같은 뷰 템플릿)
- 웹 브라우저: HTML 해석

HTTP API

- HTML이 아니라 데이터를 전달
- 주로 JSON 형식 사용
- 다양한 시스템에서 호출
- UI 화면이 필요하면, 클라이언트가 별도 처리

웹 클라이언트(자바스크립트) to 서버

앱 클라이언트(아이폰, 안드로이드 등) to 서버

서버 to 서버

서버 사이드 렌더링(SSR)

- HTML 최종 결과를 서버에서 만들어서 웹 브라우저에 전달
- 주로 정적인 화면에 사용
- 관련기술: JSP, 타임리프

요청 /orders.html → 서버 주문 정보 조회, 동적으로 HTML 생성→ 웹 브라우저에 전달

웹 브라우저는 보여주는 역할만.

클라이언트 사이드 렌더링(CSR)

- HTML 결과를 자바스크립트를 사용해 웹 브라우저에서 동적으로 생성해서 적용
- 주로 동적인 화면에 사용, 웹 환경을 마치 앱처럼 필요한 부분부분 변경할 수 있음
- 관련기술: React, Vue.js

### 스프링 부트

- 스프링 부트는 서버를 내장
- 과거에는 서버에 WAS를 직접 설치하고, 소스는 War 파일을 만들어서 설치한 WAS에 배포
- 스프링 부트는 빌드 결과(Jar)에 WAS 서버 포함 → 빌드 배포 단순화

---

### 로깅

로그 라이브러리는 Logback, Log4J, Log4J2 등등 수 많은 라이브러리가 있는데, 그것을 통합해서 인터페이스로 제공하는 것이 바로 SLF4J 라이브러리다.

`SLF4J`는 인터페이스고, 그 구현체로 라이브러리를 선택하면 된다.

실무에서는 스프링 부트가 기본으로 제공하는 Logback을 대부분 사용한다.

`@RestController`

- `@Controller`는 반환 값이 `String`이면 뷰 이름으로 인식된다. 그래서 **뷰를 찾고 뷰가 랜더링** 된다.
- `@RestController`는 반환 값으로 뷰를 찾는 것이 아니라, **HTTP 메시지 바디에 바로 입력한다.**

로그 레벨 : **TRACE > DEBUG > INFO > WARN > ERROR**

- 개발 서버는 **debug** 출력
- 운영 서버는 **info** 출력

`log.debug(”data=” + data)`

- 로그 출력 레벨을 info로 설정해도 해당 코드에 있는 “data=”+data가 실제 실행이 되어 버린다. 결과적으로 문자 더하기 연산이 발생한다.

`log.debug(”data={}”, data)`

- 로그 출력 레벨을 info로 설정하면 아무 일도 일어나지 않는다. 따라서 앞과 같은 의미없는 연산이 발생하지 않는다.

**로그 사용시 장점**

- 쓰레드 정보, 클래스 이름 같은 부가 정보를 함께 볼 수 있고, 출력 모양을 조정할 수 있다.
- 로그 레벨에 따라 개발 서버에서는 모든 로그를 출력하고, 운영서버에서는 출력하지 않는 등 로그를 상황에 맞게 조절할 수 있다.
- 시스템 아웃 콘솔에만 출력하는 것이 아니라, 파일이나 네트워크 등, 로그를 별도의 위치에 남길 수 있다. 특히 파일로 남길 때는 일별, 특정 용량에 따라 로그를 분할하는 것도 가능하다.
- 성능도 일반 System.out 보다 좋다.(내부 버퍼링, 멀티 쓰레드 등등) 그래서 실무에서는 꼭 로그를 사용해야 한다.

---

서블릿

1. 톰캣 같은 WAS 직접 설치
2. 서블릿 코드 클래스 파일로 빌드해서 올림
3. 톰캣 서버 실행

→ 스프링 부트는 톰캣 서버 내장. 편리하게 서블릿 코드 실행 가능

웹 애플리케이션 서버의 요청 응답 구조

HTTP 요청 메시지를 기반으로 request, response 객체 생성

request, response 가지고 서블릿 실행, 종료

response 객체 정보로 HTTP 응답 생성 

---

## HttpServletRequest

HTTP 요청 메시지 직접 파싱 ? → 매우 불편.

서블릿은 개발자가 HTTP 요청 메시지를 편리하게 사용할 수 있도록 개발자 대신에 HTTP 요청 메시지를 파싱한다.

그리고 그 결과를 `HttpServletRequest` 객체에 담아서 제공한다.

HTTP 요청 메시지 파싱 + 부가기능

**임시 저장소 기능**

해당 HTTP 요청이 시작부터 끝날 때 까지 유지되는 임시 저장소 기능

저장: request.setAttribute(name, value)

조회: request.getAttribute(name)

**세션 관리 기능**

request.getSession(create: true)

HTTP 요청 메세지를 통해 클라이언트에서 서버로 데이터를 전달하는 방법

1. `GET - 쿼리 파라미터`
- /url?username=hello&age=20
- 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달
- 검색, 필터, 페이징등에서 많이 사용하는 방식
1. `POST- HTML Form`
- Content-Type: application/x-www-form-urlencoded(form의 내용을 메시지 바디를 통해서 전달)
- 메시지 바디에 쿼리 파라미터 형식으로 전달 username=hello&age=20
- 예) 회원 가입, 상품 주문
1. `HTTP message body에 데이터를 직접 담아서 요청`
- HTTP API에서 주로 사용, JSON, XML, TEXT
- 데이터 형식은 주로 JSON 사용
    - POST, PUT, PATCH 전부 사용 가능
    - 

쿼리 파라미터 ?로 시작, &로 구분 가능

이름이 같은 복수 파라미터 조회 - `request.getParameterValues()`

중복일 때 request.getParameter()를 사용하면 request.getParameterValues()의 첫 번째 값을 반환한다.

요청 URL: http://localhost:8080/request-param

content-type: application/x-www-form-urlencoded

message body: username=hello&age=20

`**application/x-www-form-urlencoded**` 형식은 GET에서 살펴본 쿼리 파라미터 형식과 같다.

따라서 쿼리 파라미터 조회 메서드 그대로 사용하면 된다.

request.getParameter() 는 GET URL 쿼리 파라미터 형식도 지원하고, POST HTML Form 형식도 둘 다 지원한다.

> Content-Type은 **HTTP 메시지 바디의 데이터 형식을 지정한다.**
GET URL 쿼리 파라미터 형식으로 클라이언트에서 서버로 데이터를 전달할 때는 HTTP 메시지 바디를 사용하지 않기 때문에 content-type이 없다.

POST HTML Form 형식으로 데이터를 전달하면 HTTP 메시지 바디에 해당 데이터를 포함해서 보내기 때문에 바디에 포함된 데이터가 어떤 형식인지 Content-type을 꼭 지정해야 한다.
> 

HTTP message body에 데이터 직접 담아서 요청 

POST, PUT, PATCH 주로 사용

byte 코드를 문자(String)으로 보려면 문자표(Charset)을 지정해주어야 한다.

StreamUtils.copyToString(inputStream, `StandardCharsets.*UTF_8*);`

### JSON 형식

JSON 형식으로 파싱할 수 있게 객체 생성

JSON 결과 → 자바 객체

Jackson, Gson 같은 JSON 변환 라이브러리를 추가해서 사용해야 한다. 스프링 부트로 Spring MVC를 선택하면 기본으로 Jackson 라이브러리(ObjectMapper)를 함께 제공한다.

---

### HttpServletResponse

HTTP 응답 메시지 생성

- HTTP 응답코드 지정
- 헤더 생성
- 바디 생성

**편의 기능 제공**

- Content-Type
- 쿠키
- redirect

`HTTP 응답 메시지`

단순 텍스트 응답

- writer.println(”ok”);

HTML 응답

- HTTP 응답으로 HTML을 반환할 때는 content-type을 `**text/html**`로 지정해야 한다.

HTTP API -Message Body JSON 응답

- content-type: application/json

JSON 결과 → 자바 객체 : `**objectMapper.readValue(messageBody, HelloData.class);**`

자바 객체 → JSON 결과 : `**objectMapper.writeValueAsString(helloData);**`

---

서블릿 덕분에 동적으로 원하는 HTML을 만들 수 있다.

→ 매우 복잡하고 비효율적.

HTML 문서에 `**동적으로 변경해야 하는 부분만 자바 코드를 넣을 수 있다면**` 더 편리.

이것이 바로 템플릿 엔진이 나온 이유이다. 템플릿 엔진을 사용하면 HTML 문서에서 필요한 곳만 코드를 적용해서 동적으로 변경할 수 있다.

JSP도 서블릿으로 변환되어 사용. request, response는 사용 가능하다.

<%@ page import = …. %>

- 자바의 import문과 같다.

<% ~~~ %>

- 이 부분에는 자바 코드를 입력할 수 있다.

<%= ~~ %>

- 이 부분에는 자바 코드를 출력할 수 있다.

코드의 상위 - 비즈니스 로직

하위 - HTML 뷰 영역

JAVA 코드, 데이터 조회하는 리포지토리 등등 다양한 코드가 모두 JSP에 노출되어 있다.

**`JSP가 너무 많은 역할을 한다.`**

**MVC 패턴의 등장**

비즈니스 로직은 서블릿처럼 다른곳에서 처리하고, JSP는 목적에 맞게 HTML로 화면(View)을 그리는 일에 집중하도록 하자.

---

## MVC

### 개요

`**변경의 라이프 사이클**`

변경주기가 다르면 분리한다. → 포인트

변경의 라이프 사이클이 다른 부분을 하나의 코드로 관리하는 것은 유지보수하기 좋지 않다.

`기능 특화`

JSP 같은 뷰 템플릿은 화면 렌더링 하는데 최적화 되어 있기 때문에 이 부분의 업무만 담당하는 것이 가장 효과적이다.

`**Model View Controller**`

MVC 패턴은 지금까지 학습한 것 처럼 하나의 서블릿이나, JSP로 처리하던 것을 컨트롤러(Controller)와 뷰(View)라는 영역으로 서로 역할을 나눈 것을 말한다.

컨트롤러: HTTP 요청을 받아서 파라미터를 검증하고, 비즈니스 로직을 실행한다. 그리고 뷰에 전달할 결과 데이터를 조회해서 모델에 담는다. **서블릿**

모델: 뷰에 출력할 데이터를 담아둔다. 뷰가 필요한 데이터를 모두 모델에 담아서 전달해주는 덕분에 뷰는 비즈니스 로직이나 데이터 접근을 몰라도 되고, 화면을 렌더링 하는 일에 집중할 수 있다. **request 내부 저장소**

뷰: 모델에 담겨있는 데이터를 사용해서 화면을 그리는 일에 집중한다. 여기서는 HTML을 생성하는 부분을 말한다. **JSP**

`dispatcher.forward();`: 다른 서블릿이나 JSP로 이동할 수 있는 기능. 서버 내부에서 다시 호출이 발생한다.

`/WEB-INF`

이 경로안에 JSP가 있으면 외부에서 직접 JSP를 호출할 수 없다. 우리가 기대하는 것은 항상 컨트롤러를 통해서 JSP를 호출하는 것이다.

> **redirect vs forward**
리다이렉트는 실제 클라이언트(웹 브라우저)에 응답이 나갔다가, 클라이언트가 redirect 경로로 다시 요청한다. 따라서 클라이언트가 인지할 수 없고, URL 경로도 실제로 변경된다. 반면에 포워드는 서버 내부에서 일어나는 호출이기 때문에 클라이언트가 전혀 인지하지 못한다.
> 

절대경로(/로 시작)

상대경로(/로 시작하지 않음)

상대경로를 사용하면 현재 URL이 속한 계층 경로 + save가 호출된다.

MVC 덕분에 컨트롤러 로직과 뷰 로직을 확실하게 분리한 것을 확인할 수 있다.

향후 화면에 수정이 발생하면 뷰 로직만 변경하면 된다.

### 한계

컨트롤러 딱 봐도 중복이 많다.  → 포워드 중복, ViewPath 중복

`HttpServletRequest`, `HttpServletResponse`를 사용하는 코드는 테스트 케이스를 작성하기 어렵다.

공통 처리가 어렵다는 문제.

**컨트롤러 호출 전에 공통 기능을 처리해야 한다.** 

[공통 기능 → 컨트롤러 호출]. 소위 수문장 역할

프론트 컨트롤러(Front Controller 패턴)을 도입하면 이런 문제를 깔끔하게 해결할 수 있다.

---

### 프론트 컨트롤러 패턴

- 프론트 컨트롤러 서블릿 하나로 클라이언트의 요청을 받음
- 프론트 컨트롤러가 요청에 맞는 컨트롤러를 찾아서 호출
- 입구를 하나로!
- 프론트 컨트롤러를 제외한 나머지 컨트롤러는 서블릿을 사용하지 않아도 됨

스프링 웹 MVC의 DispatcherServlet이 FrontController 패턴으로 구현되어 있음

컨트롤러 입장에서 HttpServletRequest, HttpServletResonse이 꼭 필요할까?

요청 파라미터 정보는 자바의 Map으로 대신 넘기도록 하면 컨트롤러가 서블릿 기술을 몰라도 동작할 수 있다.

request 객체 Model로 사용하는 대신 별도의 Model 객체를 만들어서 반환하면 된다.

컨트롤러가 서블릿 기술을 전혀 사용하지 않도록 변경

별도의 Model 객체 만들어 반환

뷰 이름 - 프론트 컨트롤러에서 처리하도록

컨트롤러가 ModelView를 반환하지 않고, viewName만 반환

`ControllerV3`, `ControllerV4`는 완전히 다른 인터페이스이다.

호환이 불가능하다. 이럴 때 사용하는 것이 바로 어댑터이다. [어댑터 패턴]

어댑터 패턴을 사용해서 프론트 컨트롤러가 다양한 방식의 컨트롤러를 처리할 수 있도록 변경해보자

**어댑터 변환**

```java
String viewName = controller.process(paramMap, model);

ModelView mv = new ModelView(viewName);
mv.setModel(mv);

return mv;
```

`ControllerV4`는  뷰의 이름을 반환했지만, 어댑터는 이것을 `ModelView`로 만들어서 형식을 맞추어 반환해야 한다. 

---

## 스프링 MVC 구조

- FrontController → DispatcherServlet
- handlerMappingMap → HandlerMapping
- MyHandlerAdapter → HandlerAdapter

스프링 부트는 `DispatcherServlet`을 서블릿으로 자동 등록하면서 모든 경로(urlPatterns=”/”)에 대해서 매핑한다.

참고: 더 자세한 경로가 우선순위가 높다. 그래서 기존에 등록한 서블릿도 함께 동작한다.

`**요청 흐름**`

- 서블릿이 호출되면 `HttpServlet`이 제공하는 `service()`가 호출된다.
- `DispatcherServlet`의 부모인 `FrameworkServlet`에서 `service()`를 오버라이드 해두었다.
- FramworkServlce.service() 시작으로 여러 메서드가 호출되면서 `DispatcherServlet.doDispatch()`가 호출된다.
    
`**동작 순서**`

1. 핸들러 조회: 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회한다.
2. 핸들러 어댑터 조회: 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다.
3. 핸들러 어댑터 실행: 핸들러 어댑터를 실행한다.
4. 핸들러 실행: 핸들러 어댑터가 실제 핸들러를 실행한다.
5. ModelAndView 반환: 핸들러 어댑터는 핸들러(컨트롤러)가 반환하는 정보를 ModelAndView로 변환해서 반환한다.
6. viewResolver 호출: viewResolver를 찾고 실행한다.
7. Viwe 반환: 뷰 리졸버는 뷰의 논리 이름을 물리 이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를 반환한다.
8. 뷰 렌더링: 뷰를 통해서 뷰를 렌더링한다.

### 핸들러 매핑과 핸들러 어댑터

빈의 이름으로 URL 매핑

**HandlerMapping(핸들러 매핑)**

- 핸들러 매핑에서 이 컨트롤러를 찾을 수 있어야 한다.
- 예) 스프링 빈의 이름으로 핸들러를 찾을 수 있는 핸들러 매핑이 필요하다.

**HandlerAdapter(핸들러 어댑터)**

- 핸들러 매핑을 통해서 찾은 핸들러를 실행할 수 있는 핸들러 어댑터가 필요하다.
- 예) `Controller` 인터페이스를 실행할 수 있는 핸들러 어댑터를 찾고 실행해야 한다.

1. **핸들러 매핑으로 핸들러 조회**
- `HandlerMapping`을 순서대로 실행해서, 핸들러를 찾는다.
- 이 경우 빈 이름으로 핸들러를 찾아야 하기 때문에, `BeanNameUrlHandlerMapping`이 실행에 성공하고 핸들러인 `OldController`를 반환한다.
1. **핸들러 어댑터 조회**
- `HandlerAdapter`의 `supports()`를 순서대로 호출한다.
- `SimpleControllerHandlerAdapter`가 Controller 인터페이스를 지원하므로 대상이 된다.
1. **핸들러 어댑터 실행** 
- 디스패처 서블릿이 조회한 `SimpleControllerHandlerAdapter`를 실행하면서 핸들러 정보도 함께 넘겨준다.
- `SimpleControllerHandlerAdapter`는 핸들러인 `OldController`를 내부에서 실행하고, 그 결과를 반환한다.

### 뷰 리졸버

스프링 부트는 `InternalResourceViewResolver` 라는 뷰 리졸버를 자동으로 등록한다.

application.properties에 등록한 spring.mvc.prefix, spring.mvc.suffix 설정 정보를 사용해서 등록한다.

### 스프링 MVC - 시작하기

@RequestMapping

- RequestMappingHandlerMapping
- RequestMappingHandlerAdapter

HandlerMapping

```java
0 = RequestMappingHandlerMapping : 애노테이션 기반의 컨트롤러인 @RequestMapping에서 사용
1 = BeanNameUrlHandlerMapping : 스프링 빈의 이름으로 핸들러를 찾는다.
```

HandlerAdapter

```java
0 = RequestMappingHandlerAdapter : 애노테이션 기반의 컨트롤러인 @RequestMapping에서 사용 
1 = ...
```

`**@Controller**`

스프링이 자동으로 스프링 빈으로 등록한다. (내부에 @Component 애노테이션이 있어서 컴포넌트 스캔의 대상)

스프링 MVC에서 애노테이션 기반 컨트롤러로 인식된다.

`**@RequestMapping**`

요청 정보를 매핑한다

`**ModelAndView**`

모델과 뷰 정보를 담아서 반환하면 된다.

`**RequestMappingHandlerMapping**`은 스프링 빈 중에서 `**@RequestMapping**` 또는 `**@Controller**`가 

클래스 레벨에 붙어 있는 경우에 매핑 정보로 인식한다.

컴포넌트 스캔 없이 스프링 빈으로 직접 등록해도 동작한다.

@RequestParam(”username”) 은 reqeuset.getParameter(”username”)과 거의 같은 코드

---

## 스프링 MVC - 웹 페이지 만들기

@Data - DTO에서만 사용 권장

중복 vs 명확성 → 명확성!!

정적 리소스가 공개되는 `/resources/static` 폴더에 HTML을 넣어두면, **실제 서비스에서도 공개된다.**

**서비스를 운영한다면 공개할 필요없는 HTML을 두는 것은 주의하자.**

`@PostConstruct`:  해당 빈의 의존관계가 모두 주입되고 나면 초기화 용도로 호출된다.

HTML을 그대로 볼 때는 href 속성이 사용되고 뷰 템플릿을 거치면 th:href 의 값이 href로 대체되면서 동적으로 변경할 수 있다.

리터럴 대체 - |…|

상품 등록 폼: GET `/basic/items/add`

상품 등록 처리: POST `/basic/items/add`

action에 값이 없으면 현재 URL에 데이터를 전송한다.

```java
@PostMapping("/add")
public String addItemV2(@ModelAttribute("item") Item item, Model model) {
	itemRepository.save(item);
	return "basic/item";
}
```

**@ModelAttribute - 요청 파라미터 처리**

ModelAttribute는 `Item` 객체를 생성하고 요청 파라미터의 값을 프로퍼티 접근법으로 입력해준다.

**@ModelAttribute - Model 추가**

model.addAttribute(”item”, item) 해준다.

@ModelAttribute 자체도 생략가능하다. 대상 객체는 모델에 자동 등록된다.

스프링은 `redirect:/...`으로 리다이렉트를 편리하게 지원한다.

HTML Form 전송은 GET, POST만 사용할 수 있다.

## PRG

Post Redirect Get

새로고침: 마지막에 서버에 전송한 데이터를 다시 전송한다

1. GET/add
2. POST/add
3. Redirect /items/{id}
4. GET /items{id}

URL → 인코딩 해서 넘겨야함.

RedirectAttributes 를 사용해서 해결할 수 있다.

### RedirectAttributes

URL 인코딩도 해주고, PathVariable, 쿼리 파라미터까지 처리해준다.