## 스프링에서 Argument Resolver 사용하기
컨트롤러에서 쿼리 스트링을 변수에 바인딩하려면 @RequestParam을, 가변적인 경로를 변수에 바인딩하려면 @PathVariable을, HTTP Body를 변수에 바인딩하려면 @RequestBody를 사용해야 한다.  
  
하지만 HTTP Header, Session, Cookie 등 직접적이지 않은 방식 혹은 외부 데이터 저장소로부터 데이터를 바인딩해야할 때가 있다. ArgumentResolver는 방금과 같은 경우 사용한다. ArgumentResolver를 사용하면 **컨트롤러 메서드의 파라미터 중 특정 조건에 맞는 파라미터가 있다면, 요청에 들어온 값을 이용해 원하는 객체를 만들어 바인딩**해줄 수 있다.  
  
### ArgumnetResolver를 사용하지 않았을 때
우리가 만들 애플리케이션은 요청을 처리할 때 클라이언트 IP와 요청한 유저의 ID가 필요하다고 가정하자. 참고로 유저의 ID는 HTTP Authorization 헤더에 실려오는 JWT의 Payload에서 가져와야 한다. 이를 컨트롤러에서 직접 구현하려면 많은 중복이 발생할 것이다.  
```java
@PostMapping("/items)
public ResponseEntity<void> createItem(HttpServletRequest request) {
    String token = JwtUtil.extract(request);
    JwtUtil.validateToken(token);

    String userId = JwtUtil.getPayload(token);
    String ipAddress = request.getRemoteAddr();

    // userId와 ipAddress를 사용한 요청의 처리

    return ResponseEntity.ok().build();
}
```
## 직접 구현하기
접속 IP와 유저 ID를 가져와 UserDto에 담아줄 것이다.  
```java
@Data
public class UserDto {
    private final String id;
    private final String ipAddress;
}
```
  
### HandlerMethodArgumentResolver 인터페이스
Argument Resolver를 만들기 위해서는 클래스가 HandlerMethodArgumentResolver 를 구현해야한다. HandlerMethodArgumentResolver 는 두개의 메소드를 가지고 있다.  
- `supportsParameter()`: 주어진 메서드의 파라미터가 이 Argument Resolver에서 지원하는 타입인지 검사한다. 지원한다면 true를, 그렇지 않다면 false를 반환한다.  
- `resolveArgument()`: 이 메서드의 반환값이 대상이 되는 메서드의 파라미터에 바인딩된다.  
  
```java
public class UserArgumentResolver implements HandlerMethodArgumentResolver {
    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        return parameter.getParameterType().equals(UserDto.class);
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
                                  NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
        HttpServletRequest httpServletRequest = (HttpServletRequest) webRequest.getNativeRequest();

        String token = JwtUtil.extract(httpServletRequest);
        JwtUtil.validateToken(token);

        String userId = JwtUtil.getPayload(token);
        String ipAddress = httpServletRequest.getRemoteAddr();

        return new UserDto(userId, ipAddress);
    }
}
```
supportsParameter() 에서는 parameter 객체의 getParameterType 메서드를 통해 컨트롤러 메서드의 파라미터가 UserDto 타입인지 확인한다. 그리고 일치 여부를 boolean 타입으로 반환한다.  
  
resolveArgument() 에서는 컨트롤러에서 반복된 HTTP 헤더로부터 JWT 가져오는 로직, JWT 검증 로직, JWT payload 추출 로직, 클라이언트 IP 가져오기 로직을 넣어준다. 그리고 최종적으로 UserDto를 생성해서 반환한다.  
  
### WebMvcConfigurer 에서 Argument Resolver 등록
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(new UserArgumentResolver());
    }
}
```
WebMvcConfigurer 를 구현한 클래스에서 위와 같이 우리가 만든 UserArgumentResolver 를 Argument Resolver로 등록한다.  
  
### Controller에 Argument Resolver 구현
```java
@PostMapping("/items")
public ResponseEntity<Void> createItem(UserDto userDto) {
    // UserDto를 사용한 요청의 처리
    return ResponseEntity.ok().build();
}
```
컨트롤러에 존재했던 복잡한 여러 로직들이 사라지고, 자동으로 컨트롤러 메서드 파라미터인 UserDto에 바인딩되는 것을 확인할 수 있다.  
  
### 애노테이션으로 제한하기
위와 같이 구현하면 많은 중복 로직이 사라질 수 있다. 하지만 특정한 부분에서만 자동으로 객체가 Argument Resolver를 통해 바인딩되도록 만들고 싶은 경우가 있을 것이다. 이를 커스텀 애노테이션을 만들어서 해결할 수 있다. 아래와 같은 User 라는 애노테이션을 만든다.  
```java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface User {
}
```
그리고 아래와 같이 Argument Resolver에서 hasParameterAnnotation() 이라는 메서드로 파라미터의 애노테이션으로 ArgumentResolver 적용 대상을 제한할 수 있다.  
```java
public class UserArgumentResolver implements HandlerMethodArgumentResolver {
    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        return parameter.getParameterType().equals(UserDto.class) &&
                parameter.hasParameterAnnotation(User.class); // 추가됨
    }

		// ...
}
```
그렇다면 아래와 같이 @User 가 붙은 파라미터에만 UserDto가 바인딩될 것이다.  
```java
@PostMapping("/items")
public ResponseEntity<Void> createItem(@User UserDto userDto) {
    // UserDto(id=devHudi, ipAddress=0:0:0:0:0:0:0:1)
    return ResponseEntity.ok().build();
}
```
