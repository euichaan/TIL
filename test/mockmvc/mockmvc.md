# MockMvc
- 스프링 MockMVC: 애플리케이션 서버를 구동하지 않고도 서블릿 컨테이너와 거의 비슷하게 작동하는 mock 구현체로 컨트롤러를 테스트할 수 있다.  
- 웹 통합 테스트: 톰캣, 제티 등 내장 서블릿 컨테이너에서 애플리케이션을 실행하여 실제 애플리케이션 서버에서 애플리케이션을 테스트할 수 있다.  
  
[Class MockMvc - docs](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/MockMvc.html)  
```java
 WebApplicationContext wac = ...;

 MockMvc mockMvc = webAppContextSetup(wac).build();

 mockMvc.perform(get("/form"))
     .andExpectAll(
         status().isOk(),
         content().contentType("text/html"),
         forwardedUrl("/WEB-INF/layouts/main.jsp")
     );
```
Main entry point for server-side Spring MVC test support.  
서버측 Spring MVC 테스트 지원을 위한 주요 진입점.  
  
perform 메서드: Perform a request and return a type that allows chaining further actions, such as asserting expectations, on the result.
```java
public ResultActions perform(RequestBuilder requestBuilder)throws Exception
```
  
MockMvc의 패키지는 `package org.springframework.test.web.servlet;`이다.  
## MockMvcBuilders
[Class MockMvcBuilders - docs](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/setup/MockMvcBuilders.html#standaloneSetup(java.lang.Object...))  
MockMvc를 설정하려면 MockMvcBuilders를 사용한다. 이 클래스는 정적 메서드 두 개를 제공한다.  
- standaloneSetup(Object ... controllers): **@Controller 인스턴스를 하나 이상 등록하고 스프링 MVC 인프라를 프로그래밍 방식으로 구성하여 MockMvc 인스턴스를 빌드합니다. 이를 통해 일반 단위 테스트와 유사하게 컨트롤러 및 해당 종속성의 인스턴스화 및 초기화를 완벽하게 제어할 수 있으며, 한 번에 하나의 컨트롤러를 테스트 할 수 있습니다.** 이 빌더를 사용하면 애노테이션이 달린 컨트롤러로 요청을 처리하는 데 필요한 최소 인프라가 자동으로 생성되고 사용자 지정할 수 있으므로 빌더 스타일 메서드를 사용하지 않는 한 MVC Java 구성과 동일한 구성이 제공됩니다. 애플리케이션의 Spring MVC 구성이 비교적 간단한 경우 이 빌더를 사용하는 것이 대부분의 컨트롤러를 테스트하는 데 좋은 옵션이 될 수 있습니다. 이러한 경우 훨씬 적은 수의 테스트를 사용하여 실제 Spring MVC 구성을 테스트하고 검증하는 데 집중할 수 있습니다. 파라미터는 controllers - one or more @Controller instances to set(specified Class will be turned into instance)입니다.  
- webAppContextSetup(WebApplication context): 주어진, 완전히 초기화된(즉, 새로고침된) WebApplicationContext를 사용하여 MockMvc 인스턴스를 빌드합니다. DispatcherServlet은 context를 사용하여 Spring MVC infrastructure와 그 안의 애플리케이션 컨트롤러를 검색합니다. 컨텍스트는 ServletContext로 구성되어 있어야 합니다.  
  
요약하자면, standaloneSetup은 하나 이상의 @Controller 인스턴스를 등록하고 프로그래밍 방식으로 Spring MVC 인프라를 구성하여 MockMvc 인스턴스를 빌드하고 webAppContextSetup은 완전히 초기화된(즉, 새로 고쳐진) WebApplicationContext를 사용하여 MockMvc 인스턴스를 빌드합니다.  
  
설정 예시는 다음과 같다.  
```java
	protected MockMvc mockMvc(Object controller) {
		return MockMvcBuilders.standaloneSetup(controller)
			.setControllerAdvice(new GlobalExceptionHandler())
			.setCustomArgumentResolvers(memberInfoArgumentResolver)
			.alwaysDo(MockMvcResultHandlers.print())
			.build();
	}
```
  
해당 MockMvc는 다음과 같이 사용할 수 있다.  
```java
	@Test
	void refresh_token을_이용해_access_token을_재발급한다() throws Exception {
		given(tokenService.createAccessTokenByRefreshToken(REFRESH_TOKEN))
			.willReturn(ACCESS_TOKEN_RESPONSE);

		mockMvc(new TokenController(tokenService))
			.perform(post("/api/access-token/issue")
				.contentType(MediaType.APPLICATION_JSON_VALUE)
				.header(HttpHeaders.AUTHORIZATION, AUTHORIZATION_HEADER_REFRESH)
			)
			.andExpect(status().isCreated())
			.andExpect(jsonPath("$.grantType").value("Bearer"))
			.andExpect(jsonPath("$.accessToken").value("access_token"));
	}
```
## WebApplicationContext
[Interface WebApplicationContext - docs](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/context/WebApplicationContext.html)  
```java
public interface WebApplicationContext extends ApplicationContext {
```
**웹 애플리케이션에 대한 구성(configuration)을 제공하는 인터페이스.** 이것은 애플리케이션이 실행되는 동안 읽기 전용이지만 구현이 이를 지원하는 경우(implementation supports this) 다시 로드될 수 있다.  
  
이 인터페이스는 일반 ApplicationContext 인터페이스에 getServletContext() 메서드를 추가하고 루트 컨텍스트가 부트스트랩 프로세스에서 바인딩되어야 하는 잘 알려진 애플리케이션 속성 이름을 정의한다.  
  
일반 application context와 마찬가지로, web application context는 계층적이다. 응용 프로그램당 하나의 루트 컨텍스트가 있는 반면 응용 프로그램의 각 서블릿(MVC 프레임워크의 디스패처 서블릿 포함)에는 자체 하위 컨텍스트가 있다. 표준 애플리케이션 컨텍스트 라이프사이클 기능 외에도 WebApplicationContext 구현은 ServletContextAware 빈을 감지하고 그에 따라 setServletContext 메서드를 호출해야 한다.  
  
[WebApplicationContext란?](https://unordinarydays.tistory.com/131)  
스프링 어플리케이션에 application context는 2개가 들어간다.  
- ContextLoaderListener에 의해서 만들어지는 Root WebApplicationContext  
- DispatcherServlet에 의해서 만들어지는 WebApplicationContext  
  
1. Root WebApplicationContext. 최상단에 위치한 Context  
서비스 계층이나 DAO를 포함한, 웹 환경에 독립적인 빈들을 담아둔다.  
서로 다른 서블릿컨텍스트에서 공유해야 하는 빈들을 등록해놓고 사용할 수 있다.  
Servlet context에 등록된 빈들은 이용 불가능하고 Servlet context와 공통된 빈이 있다면 Servlet context 빈이 우선된다.  
WebApplication 전체에 사용 가능한 DB연결, 로깅 기능들이 이용된다.  
  
2. WebApplicationContext. 서블릿에서만 이용되는 Context
DispatcherServlet이 직접 사용하는 컨트롤러를 포함한 웹 관련 빈들을 등록하는 데 사용된다.  
DispatcherServlet은 독자적인 WebApplicationContext를 가지고 있고, 모두 동일한 Root WebApplicationContext를 공유한다.  
  
스프링에서 말하는 "애플리케이션 컨텍스트"란 **스프링이 관리하는 빈들이 담겨 있는 컨테이너**라고 생각하면 된다. ApplicationContext라는 인터페이스를 구현한 객체들이 다 이 애플리케이션 컨텍스트이다. 웹 애플리케이션 컨텍스트는 ApplicationContext를 확장한 WebApplicationContext 인터페이스의 구현체를 말한다.   WebApplicationContext는 ApplicationContext에 getServletContext()메서드가 추가된 인터페이스이다. 이 메서드를 호출하면 서블릿 컨텍스트를 반환한다. 결국 WebApplicationContext는 스프링 애플리케이션 컨텍스트의 변종이면서 서블릿 컨텍스트와 연관 관계에 있다는 정도로 정리가 된다. 이 메서드가 추가됨과 동시에 서블릿 컨텍스트를 이용한 몇 가지 빈 생애 주기 스코프(애플리케이션, 리퀘스트, 세션 등)가 추가되기도 한다.  
  
DispatcherServlet은 여러 개 등록될 수 있다. 각각 DispatcherServlet은 독자적인 Web ApplicationContext를 가지고 있고, 모두 동일한 Root WebApplicationContext를 공유한다.  
  
스프링의 ApplicationContext는 ClassLoader와 비슷하게 자신과 상위 AC에서만 빈을 찾는다. 따라서 같은 레벨의 형제 ApplicationContext의 빈에는 접근하지 못하고 독립적이다.  
