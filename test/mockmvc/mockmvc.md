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
  
perform 메서드: Perform a request and return a type that allows chaining further actions, such as asserting expectations, on the result.
```java
public ResultActions perform(RequestBuilder requestBuilder)throws Exception
```
  
MockMvc의 패키지는 `package org.springframework.test.web.servlet;`이다.  
## MockMvcBuilders
[Class MockMvcBuilders - docs](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/setup/MockMvcBuilders.html#standaloneSetup(java.lang.Object...))  
MockMvc를 설정하려면 MockMvcBuilders를 사용한다. 이 클래스는 정적 메서드 두 개를 제공한다.  
- standaloneSetup(Object ... controllers): @Controller 인스턴스를 하나 이상 등록하고 스프링 MVC 인프라를 프로그래밍 방식으로 구성하여 MockMvc 인스턴스를 빌드합니다. 이를 통해 일반 단위 테스트와 유사하게 컨트롤러 및 해당 종속성의 인스턴스화 및 초기화를 완벽하게 제어할 수 있으며, 한 번에 하나의 컨트롤러를 테스트 할 수 있습니다. 이 빌더를 사용하면 애노테이션이 달린 컨트롤러로 요청을 처리하는 데 필요한 최소 인프라가 자동으로 생성되고 사용자 지정할 수 있으므로 빌더 스타일 메서드를 사용하지 않는 한 MVC Java 구성과 동일한 구성이 제공됩니다. 애플리케이션의 Spring MVC 구성이 비교적 간단한 경우 이 빌더를 사용하는 것이 대부분의 컨트롤러를 테스트하는 데 좋은 옵션이 될 수 있습니다. 이러한 경우 훨씬 적은 수의 테스트를 사용하여 실제 Spring MVC 구성을 테스트하고 검증하는 데 집중할 수 있습니다. 파라미터는 controllers - one or more @Controller instances to set(specified Class will be turned into instance)입니다.  
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
  
