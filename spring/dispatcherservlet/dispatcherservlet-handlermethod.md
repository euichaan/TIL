# 디스패처 서블릿이 HTTP 요청을 처리하는 방법과 핸들러 메서드(HandlerMethod)
[출처](https://mangkyu.tistory.com/180)  
## 핸들러 메서드(HandlerMethod)란?
HandlerMethod는 @RequestMapping과 그 하위 애노테이션(@GetMapping, @PostMapping 등)이 붙은 메서드의 정보를 추상화한 객체이다.  
HandlerMethod는 그 자체가 실행가능한 객체가 아니라 메서드를 실행하기 위해 필요한 참조정보를 담고 있는 객체로써 다음과 같은 정보들을 가지고 있다.  
- 빈 객체  
- 메서드 메타정보  
- 메서드 파라미터 메타정보  
- 메서드 애노테이션 메타정보  
- 메서드 리턴 값 메터정보  
디스패처 서블릿은 애플리케이션이 실행될 때 모든 컨트롤러 빈의 메서드를 살펴서 매핑후보가 되는 메서드들을 추출한 뒤, 이를 HandlerMethod 형태로 저장해둔다. 그리고 실제 요청이 들어오면 저장해 둔 목록에서 요청 조건에 맞는 HandlerMethod를 참조해서 매핑되는 메서드를 실행한다.  

## RequestMappingHandlerMapping
여기서 HTTP 요청을 HandlerMethod 객체로 변환하는 작업하거나 해당 요청에 매핑되는 HandlerMethod를 반환하는 작업은 RequestMappingHandlerMapping이 담당한다. 그래서 애플리케이션 컨텍스트가 초기화되면 RequestMappingHandlerMapping에 접근하여 저장된 매핑 정보와 핸들러 메서드 목록을 확인할 수 있다.  
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbmrtYl%2FbtroWm0Axm7%2FKIaAaLVvW3dpPvZ6FawhX0%2Fimg.png)  
  
404 에러가 발생하는 경우에 RequestMappingHandlerMapping을 사용하면 도움을 받을 수 있다.  
  
## 디스패처 서블릿이 HTTP 요청을 처리하는 방법
앞서 설명한대로 디스패처 서블릿은 적합한 컨트롤러와 메서드를 찾아 요청을 위임해야 한다. Dispatcher Servlet의 처리 과정을 보면 다음과 같다.  
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FHl576%2FbtrBxbFBnPz%2FEP1pVsvaKH92oTf9vUtA81%2Fimg.png)  
1. 클라이언트의 요청을 디스패처 서블릿이 받음    
2. 요청 정보를 통해 요청을 위임할 컨트롤러를 찾음(핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러) 를 조회)  
3. 요청을 컨트롤러로 위임할 핸들러 어댑터를 찾아서 전달함  
4. 핸들러 어댑터가 컨트롤러로 요청을 위임함  
5. 비즈니스 로직을 처리함  
6. 컨트롤러가 반환값을 반환함  
7. HandlerAdapter가 반환값을 처리함  
8. 서버의 응답을 클라이언트로 반환함  
  
### 1. 클라이언트의 요청을 디스패처 서블릿이 받음
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F1YW9L%2FbtrBu5MG2C4%2FQJAcTCI6v6Wv0kMo3ZO1xk%2Fimg.png)  
디스패처 서블릿은 가장 먼저 요청을 받는 프론트 컨트롤러이다. 서블릿 컨텍스트(웹 컨텍스트)에서 필터들을 지나 스프링 컨텍스트에서 디스패처 서블릿이 가장 먼저 요청을 받게 된다. 실제로는 Interceptor가 Controller로 요청을 위임하지는 않으므로 그림은 처리 순서를 도식화한 것으로만 이해하면 된다.  
  
### 2. 요청 정보를 통해 요청을 위임할 컨트롤러를 찾음(Handler Mapping을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회)
디스패처 서블릿은 요청을 처리할 컨트롤러를 찾고 해당 메서드를 호출해야 한다.  
HandlerMapping의 구현체 중 하나인 RequestMappingHandlerMapping은 @Controller로 작성된 모든 컨트롤러 빈을 파싱하여 HashMap으로 (요청 정보, 처리할 대상)을 관리한다. 엄밀히는 컨트롤러가 아닌, 요청에 매핑되는 컨트롤러와 해당 메서드 등을 갖는 HandlerMethod 객체를 찾는다.  
그래서 HandlerMapping은 요청이 오면 Http Method, URI 등을 사용해 Key 객체인 요청 정보를 만들고, Value인 요청을 처리할 HandlerMethod를 찾아
HandlerExecutionChain으로 감싸서 반환한다. HandlerExecutionChain으로 감싸는 이유는 컨트롤러로 요청을 넘겨주기 전에 처리해야 하는 인터셉터 등을 포함하기 위해서이다.  
  
핸들러를 못 찾을 경우 HTTP 404 응답을 클라이언트에게 전달하며 요청을 끝낸다.  
  
### 3. 요청을 컨트롤러로 위임할 핸들러 어댑터를 찾아서 전달함
디스패처 서블릿은 컨트롤러로 요청을 직접 위임하는 것이 아니라 HandlerAdapter를 통해 컨트롤러로 요청을 위임한다. 이때 어댑터 인터페이스를 통해 컨트롤러를 호출하는 이유는 **컨트롤러의 구현 방식이 다양하기 때문**이다. 최근에는 @Controller에 @RequestMapping 관련 애노테이션을 사용해 컨트롤러 클래스를 주로 작성하지만, Controller 인터페이스를 구현하여 컨트롤러 클래스를 작성할 수도 있다.  
**스프링은 HandlerAdapter라는 인터페이스를 통해 어댑터 패턴을 적용함으로써 컨트롤러의 구현 방식에 상관없이 요청을 위임할 수 있다.**   

### 4. 핸들러 어댑터가 컨트롤러로 요청을 위임함
핸들러 어댑터가 컨트롤러로 요청을 넘기기 전에 **공통적인 전/후처리 과정**이 필요하다.
전처리: 대표적으로 인터셉터들을 포함해 요청 시에 @RequestParam, @RequestBody 등을 처리하기 위한 ArgumentResolver들  
후처리: 응답 시에 ResponseEntity의 Body를 Json으로 직렬화하는 등의 처리를 하는 ReturnValueHandler 등  
컨트롤러의 메서드를 호출하도록 요청. 실제로 요청이 위임되는 과정에서는 리플렉션이 사용된다.  
  
HandlerMethod에 컨트롤러 빈 이름과 메서드, 빈 팩토리가 있어서 빈 팩토리에서 컨트롤러 빈을 찾는다.  
그리고 해당 컨트롤러 빈 객체로부터 리플렉션을 사용한다.  

### 5. 비즈니스 로직을 처리함
이후에 컨트롤러는 서비스를 호출하고 우리가 작성한 비즈니스 로직들이 진행됩니다.  

### 6. 컨트롤러가 반환값을 반환함
비즈니스 로직이 처리된 후에는 컨트롤러가 반환값을 반환한다.  

### 7. HandlerAdapter가 반환값을 처리함
HandlerAdapter는 컨트롤러로부터 받은 응답을 응답 처리기인 ReturnValueHandler가 후처리한 후에 디스패처 서블릿으로 돌려준다.  
만약 컨트롤러가 ResponseEntity를 반환하면 HttpEntityMethodProcessor가 MessageConverter를 사용해 응답 객체를 직렬화하고 응답 상태(HttpStatus)를 설정한다. 만약 컨트롤러가 View 이름을 반환하면 ViewResolver를 통해 View를 반환한다.  

### 8. 서버의 응답을 클라이언트로 반환
디스패처 서블릿을 통해 반환되는 응답들은 다시 필터들을 거쳐 클라이언트에게 반환된다.  
