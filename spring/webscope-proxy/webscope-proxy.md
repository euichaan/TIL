# 웹 스코프와 프록시 동작 원리
```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS) // HTTP 요청 당 하나 씩 생성되고, HTTP 요청이 끝나는 시점에 소멸된다.
// proxyMode = ScopedProxyMode.TARGET_CLASS: MyLogger의 가짜 프록시 클래스를 만들어두고 HTTP request와 상관없이 가짜 프록시 클래스를 다른 빈에 미리 주입해둔다.
public class MyLogger {

...

@RestController
@RequiredArgsConstructor
public class LogDemoController {

	private final LogDemoService logDemoService;
	private final MyLogger myLogger;

	@GetMapping("/log-demo")
	public String logDemo(HttpServletRequest request) {
		final String requestURL = request.getRequestURL().toString();
		System.out.println("myLogger = " + myLogger.getClass());

		myLogger.setRequestURL(requestURL);

		myLogger.log("controller test");
		logDemoService.logic("testId");
		return "OK";
	}
}
```
주입된 MyLogger를 확인해보면 출력 결과는 다음과 같다.
```
myLogger = class hello.core.common.MyLogger$$EnhancerBySpringCGLIB$$b68b726d
```
**CGLIB라는 라이브러리로 내 클래스를 상속 받은 가짜 프록시 객체를 만들어서 주입한다.**  
@Scope의 `proxyMode = ScopedProxyMode.TARGET_CLASS`를 설정하면 스프링 컨테이너는 CGLIB라는 바이트코드를 조작하는 라이브러리를 사용해서, MyLogger를 상속받은 가짜 프록시 객체를 생성한다.  
결과를 확인해보면 `MyLogger$$EnhancerBySpringCGLIB` 이라는 클래스로 만들어진 객체가 대신 등록된 것을 확인할 수 있다.  
그리고 스프링 컨테이너에 "myLogger"라는 이름으로 진짜 대신에 이 가짜 프록시 객체를 등록한다. 그래서 의존관계 주입도 이 가짜 프록시 객체가 주입된다.  
  
**가짜 프록시 객체는 요청이 오면 그때 내부에서 진짜 빈을 요청하는 위임 로직이 들어있다.**  
`myLogger.log()`을 호출하면 사실은 가짜 프록시 객체의 메서드를 호출한 것이다.  
가짜 프록시 객체는 request 스코프의 진짜 `myLogger.log()`를 호출한다.  
가짜 프록시 객체는 원본 클래스를 상속받아서 만들어졌기 때문에 이 객체를 사용하는 클라이언트 입장에서는 사실 원본인지 아닌지도 모르게, 동일하게 사용할 수 있다.(다형성)  
  
## 동작 및 특징 정리
- CGLIB이라는 라이브러리로 내 클래스를 상속받은 가짜 프록시 객체를 만들어서 주입한다.  
- 이 가짜 프록시 객체는 실제 요청이 오면 그때 내부에서 실제 빈을 요청하는 위임 로직이 들어있다.  
- **가짜 프록시 객체는 실제 request scope과는 관계가 없다. 그냥 가짜이고, 내부에 단순한 위임 로직만 있고, 싱글톤처럼 동작한다.**  
- 핵심 아이디어는 진짜 객체 조회를 꼭 필요한 시점까지 지연처리 한다는 점이다.  
