# 11월 17일
## thymeleaf th:id
th:each 문과 같은 경우, onclick으로 값을 빼내고 싶어도 쉽지 않다.
id를 다르게 구별하는 전략을 주로 쓰는데, th:id를 통해 객체의 값을 id로 쓰면 구분 가능하다.
```html
th:id="'g' + ${question.id}"
```
이런식으로 문자를 추가하고 싶을 때는 '문자열' + 를 붙이면 된다.

## jQuery
```html
.children()
```
.children() 은 어떤 요소의 자식 요소를 선택한다.
```html
$('div').children().css('color','blue'); //div 자식 요소의 색을 파란색으로 만든다.
$('ul').children('ip').css('color','red); //ul의 자식 요소 중 ip를 클래스 값으로 가지는 요소의 색을 빨간색으로 만든다.
```
td:eq(index)로 테이블 선택 데이터 정보 가져오기
```html
td:eq(0) -> 선택(클릭)한 row의 첫 번째 데이터. 즉 컬럼 0
td:eq(1) -> 선택(클릭)한 row의 두 번째 데이터. 즉 컬럼 1
```
사용 예시는 다음과 같다.
```html
const clickedQuestionAuthor = $("#question-" + questionId).children('td:eq(3)').text()
```

# 11월 18일
## LocalDate를 LocalDateTime으로 변환하기
.atStartOfDay() 날짜 + 00:00:00.00000000을 의미한다.
.atTime(LocalTime.MAX) 날짜 + 23:59.99999999를 의미한다.
```java
LocalDate date = LocalDate.parse("2021-10-25);
LocalDateTIme localDateTime1 = date.atStartOfDay();
LocalDateTIme localDateTIme2 = date.atTime(LocalTime.MAX);
```
실제 사용 예시는 다음과 같다.
```java
String matchingStartTime = player.getMatching().getMatchingStartTime(); // 21:30
LocalDate date = player.getMatching().getMatchingDate(); // 11-18
LocalDateTime localDateTime = date.atTime(LocalTime.parse(matchingStartTime)); // 11-18T21:30
```

# 11월 19일 
## 서블릿 예외 처리
- `Exception` 예외
- `response.sendError` (HTTP 상태 코드, 오류 메시지)

웹 애플리케이션은 사용자 요청별로 별도의 쓰레드가 할당. 서블릿 컨테이너 안에서 실행   
애플리케이션에서 예외가 발생했는데, try ~ catch로 예외를 잡아서 처리하면 문제가 없다.  
만약에 애플리케이션에서 예외를 잡지 못하고, 서블릿 밖으로 까지 예외가 전달되면?  
```
WAS <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
```
톰캣 같은 WAS까지 예외 전달. WAS는 예외가 올라오면 어떻게 처리해야 할까?  

```java
 @GetMapping("/error-ex")
  public void errorEx() {
    throw new RuntimeException("예외 발생!");
  }
```
### `Exception`의 경우 
서버 내부에서 처리할 수 없는 오류가 발생한 것으로 생각. HTTP 상태 코드 500을 반환한다.  
### `response.sendError(HTTp 상태 코드, 오류 메시지)`
호출한다고 당장 예외가 발생하는 것은 아니지만, 서블릿 컨테이너에게 오류가 발생했다는 점을 전달할 수 있다.  
이 메서드를 사용하면 HTTP 상태 코드와 오류 메시지도 추가할 수 있다.  
```java
WAS <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(response.sendError())
```
`response` 내부에 오류가 발생했다는 상태 저장. 서블릿 컨테이너는 고객에게 응답 전에 response에 sendError()가  
호출되었는지 확인한다. 그리고 호출되었다면 설정한 오류 코드에 맞추어 기본 오류 페이지를 보여준다.  

# 11월 20일
## jQuery
- `$(셀렉터).html()`
셀렉터 하위에 있는 자식 태그들을 **태그나 문자열 따질 것 없이 전부** 가져온다.  
```html
<script type="text/javascript">
$( function() {
  var word = $("#name").html();
  console.log(word);
});
</script>
</head>
<body>
  <div id="name">
    <span>안녕하세요.</span>
  </div>
</body>
```
결과 : <span>안녕하세요.</span>  

- `$(셀렉터).text()`
셀렉터 하위에 있는 자식 태그들의 **문자열**만 출력
```html
<script type="text/javascript">
$( function() {
  var word = $("#name").text();
  console.log(word);
});
</script>
</head>
<body>
  <div id="name">
    <span>안녕하세요.</span>
  </div>
</body>
```
결과 : 안녕하세요.  

- `$(셀렉터).val()`
input 태그에 정의된 **value 속성**의 값을 확인하고자 할 때 사용  
```html
<script type="text/javascript">
$( function() {
  var word = $("#name").val();
  console.log(word);
});
</script>
</head>
<body>
 <div>
  <input id="name" type="text" value="텍스트">
 </div>
</body>
```
결과 : 텍스트  

- `on Function`  
jQuery 이벤트를 바인드 하는 방법. `on()` 함수를 이용해서 이벤트를 바인드 할 수 있다.  
기본적으로 다음처럼 작성한다.  
```javascript
$(selector).on(eventType, function() {
  // ...something
});
```

- `focusout과 함께 사용하기`   
focusout 포커스 아웃 이벤트는 요소(또는 하위요소)가 포커스를 잃을 때 발생한다.  
```javascript
$("#foucisId").focusout(fuction() {
// ...something
});
```
focusin 포커스 인 이벤트는 요소(또는 하위요소)가 포커스를 얻을 때 발생한다.
```javascript
$("#foucisId").focusin(fuction() {
// ...something
});
```
on 함수와 함께 사용하면 다음과 같다.   
```javascript
$("#selfIntroduction").on("focusout", function () {

    let member = {
        introduction : $("#selfIntroduction").val()
    }
    console.log(member.introduction.length)

    if (member.introduction.length >= 100) {
        //자기소개 100자 이상일 시 에러
        $("#introduction-length-check").removeAttr("hidden");
        validationLength.length = "error"
        validationLengthCheck();
    } else {
        //정상 경우
        $("#introduction-length-check").attr("hidden", true);
        validationLength.length = "ok";
        validationLengthCheck();
    }
})
```  
## Spring 예외 처리 복습
서블릿은 다음 2가지 방식으로 예외 처리를 지원한다.  
- `Exception`
- `response.sendError(HTTP 상태 코드, 오류 메시지)`

### Exception
웹 애플리케이션은 사용자 요청별로 별도의 쓰레드 할당, 서블릿 컨테이너 안에서 실행된다.  
만약 `서블릿 밖으로 까지` 예외가 전달된다면?  
일반적인 호출 구조
```
WAS -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러
```

예외 전달 과정  
```
WAS <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
```  
결국 톰캣 같은 WAS 까지 예외가 전달. `WAS는 예외가 올라면 어떻게 처리할까?`  

```java
 @GetMapping("/error-ex")
  public void errorEx() {
  throw new RuntimeException("예외 발생!"); 
}
```
`Exception`의 경우 서버 내부에서 처리할 수 없는 오류가 발생한 것으로 생각. HTTP 상태 코드 **`500`** 반환.  
### response.sendError(HTTP 상태 코드, 오류 메시지)
`HttpServletRespone`의 `sendError` 메서드를 사용해도 된다. 이것을 호출한다고 당장 예외 발생하는 것은 아님.    
서블릿 컨테이너에게 오류가 발생했다는 점을 전달할 수 있다.    
```java
@GetMapping("/error-404")
  public void error404(HttpServletResponse response) throws IOException {
    response.sendError(404, "404 오류!");
  }
```
sendError의 흐름은 다음과 같다.
```
WAS <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(response.sendError())
```
`response.sendError()`를 호출하면 `response` 내부에 오류가 발생했다는 상태를 저장해둔다.  
그리고 서블릿 컨테이너는 고객에게 응답 전에 `response`에 `sendError()`가 호출되었는지 확인한다.  
그리고 호출되었다면 설정한 오류 코드에 맞추어 기본 오류 페이지를 보여준다.  

## 서블릿 예외 처리 - 오류 화면 제공  
서블릿은 `Exception(예외)`가 발생해서 서블릿 밖으로 전달되거나 또는 `response.snedError()`가 호출되었을 때  
각각의 상황에 맞춘 오류 처리 기능을 제공한다.  
```java
@Component
public class WebServerCustomizer implements WebServerFactoryCustomizer<ConfigurableWebServerFactory> {

  //==기본 오류 페이지 커스텀==//
  @Override
  public void customize(ConfigurableWebServerFactory factory) {
    ErrorPage errorPage404 = new ErrorPage(HttpStatus.NOT_FOUND, "/error-page/400");
    ErrorPage errorPage500 = new ErrorPage(HttpStatus.INTERNAL_SERVER_ERROR, "/error-page/500");

    ErrorPage errorPageEx = new ErrorPage(RuntimeException.class, "/error-page/500"); //RuntimeException의 자식 예외까지 처리

    factory.addErrorPages(errorPage404, errorPage500, errorPageEx); //등록
  }
}

```
- `response.sendError(404):` `errorPage404` 호출   
- `response.sendError(404):` `errorPage404` 호출   
- `RuntimeException` 또는 그 자식 타입의 예외 : `errorPageEx` 호출  
오류가 발생했을 때 처리할 수 있는 컨트롤러가 필요. 예를 들어RuntimeException 예외가 발생하면  
errorPageEx에서 지정한 `/error-page/500`이 호출된다.  

## 서블릿 예외 처리 - 오류 페이지 작동 원리
서블릿은 `Exception` 이 발생해서 서블릿 밖으로 전달되거나 `response.sendError()`가 호출  
되엇을 때 설정된 오류 페이지를 찾는다.  

예를 들어 `RuntimeException` 예외가 WAS까지 전달되면, WAS는 오류 페이지 정보를 확인한다.  
확인해보니 `RuntimeException`의 오류 페이지로 `/error-page/500`이 지정되어 있다.  
WAS는 오류 페이지를 출력하기 위해 `/error-page/500`을 다시 요청한다.  

**오류 페이지 요청 흐름**
```
WAS `/error-page/500` 다시 요청 -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러(/error-page/500) -> View
```  
**예외 발생과 오류 페이지 요청 흐름**
```
1. WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
2. WAS `/error-page/500` 다시 요청 -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러(/error-page/500) -> View
```  
**중요한 점은 웹 브라우저(클라이언트)는 서버 내부에서 이런 일이 일어나는지 전혀 모른다는 점이다.**  
**오직 서버 내부에서 오류 페이지를 찾기 위해 추가적인 호출을 한다.**  
정리하면 다음과 같다.
1. 예외가 발생해서 WAS까지 전파된다.  
2. WAS는 오류 페이지 경로를 찾아서 내부에서 오류 페이지를 호출한다. 이때 오류 페이지 경로로  
필터, 서블릿, 인터셉터, 컨트롤러가 모두 다시 호출된다.  

오류 정보를 `request`의 `attribute`에 추가해서 넘겨준다. 필요하면 사용 가능하다.  
```java
  private void printErrorInfo(HttpServletRequest request) {

    log.info("ERROR_EXCEPTION: ex=", request.getAttribute(ERROR_EXCEPTION));
    log.info("ERROR_EXCEPTION_TYPE: {}", request.getAttribute(ERROR_EXCEPTION_TYPE));
    log.info("ERROR_MESSAGE: {}", request.getAttribute(ERROR_MESSAGE)); // ex의 경우 NestedServletException 스프링이 한번 감싸서 반환
    log.info("ERROR_REQUEST_URI: {}", request.getAttribute(ERROR_REQUEST_URI));
    log.info("ERROR_SERVLET_NAME: {}", request.getAttribute(ERROR_SERVLET_NAME));
    log.info("ERROR_STATUS_CODE: {}", request.getAttribute(ERROR_STATUS_CODE));
    log.info("dispatchType={}", request.getDispatcherType());
  }
```

## 서블릿 예외 처리 - 필터
**예외 발생과 오류 페이지 요청 흐름**
```
1. WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
2. WAS `/error-page/500` 다시 요청 -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러(/error-page/500) -> View
```  
오류가 발생하면 오류 페이지 출력 위해 WAS내부에서 2번과 같이 다시 한번 호출이 발생한다.  
이때 필터, 서블릿, 인터셉터도 모두 다시 호출된다. 그런데 로그인 인증 체크 같은 경우,  
이미 한번 필터나 인터셉터에서 체크를 완료했다. 따라서 필터나 인터셉트가 한번 더 호출되는 것은 매우 비효율적이다.  

결국 `클라이언트로부터 발생한 정상 요청인지`, 아니면 `오류 페이지 출력을 위한 내부 요청인지` 구분할 수  
있어야 한다. 서블릿은 이런 문제를 해결하기 위해 `DispatcherType` 이라는 추가 정보를 제공한다.  

고객이 처음 요청하면 `dispatcherType=REQUEST`이다.  

 ```java
 filterFilterRegistrationBean.setDispatcherTypes(DispatcherType.REQUEST, DispatcherType.ERROR);
 ```
이렇게 두 가지를 모두 넣으면 클라이언트 요청은 물론이고, 오류 페이지 요청에서도 필터가 호출된다.  
아무것도 넣지 않으면 기본 값이 `DispatcherType.REQUEST`이다. 즉 클라이언트 요청이 있는 경우에만  
필터가 적용된다.  

결론 : 기본 값이 `DispatcherType.REQUEST` 이므로 오류 페이지 출력을 위한 2차 호출 때는 필터가  
호출되지 않는다.  

## 서블릿 예외 처리 - 인터셉터  
인터셉터는 서블릿이 제공하는 기능이 아니라 스프링이 제공하는 기능이다. 따라서  
`DispatcherType`과 무관하게 항상 호출된다.  

대신에 오류 페이지 경로를 `excludePathPatterns`를 사용해서 빼주면 된다.  
```java
@Override
  public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(new LogInterceptor())
        .order(1)
        .addPathPatterns("/**")
        .excludePathPatterns("/css/**", "*.ico", "/error", "/error-page/**"); //오류 페이지 경로
  }
```  

## 전체 흐름 정리
`/hello` 정상 요청  
```
WAS(/hello, dispatchType=REQUEST) -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러 -> View 
```

`/error-ex` 오류 요청  
- 필터는 `DispatchType`으로 중복 호출 제거(dispatchType=REQUEST)  
- 인터셉터는 경로 정보로 중복 호출 제거`(excludePathPatterns("/error-page/**))`  
```
1. WAS(/error-ex, dispatchType=REQUEST) -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러
2. WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
3. WAS 오류 페이지 확인
4. WAS(/error-page/500, dispatchType=ERROR) -> 필터(x) -> 서블릿 -> 인터셉터(x) -> 컨트롤러(/error-page/500) -> View
```  

## 스프링 부트 - 오류 페이지 1 
지금까지 예외 처리 페이지를 만들기 위한 과정
- `WebServerCustomizer` 만들기  
- 예외 종류에 따라서 `ErrorPage` 추가  
- 예외 처리용 컨트롤러 `ErrorPageController` 를 만듬  

**스프링 부트는 이런 과정을 모두 기본으로 제공한다.**
스프링 부트는 `ErrorPage`를 자동으로 등록한다. 이때 `/error`라는 경로로 기본 오류 페이지를 설정한다.  
`BasicErrorController` 라는 스프링 컨트롤러를 자동으로 등록한다.  

이제 오류가 발생했을 때 오류 페이지로 `/error`를 기본 요청한다. 스프링 부트가 자동 등록한  
`BasicErrorController`는 이 경로를 기본으로 받는다.  

### 개발자가 할 일 
오류 페이지 화면만 `BasicErrorController`가 제공하는 룰고 우선순위 따라서 등록하면 된다.  
정적 HTML이면 정적 리소스, 뷰 템플릿을 사용해서 동적으로 오류 화면을 만들고 싶으면 뷰 템플릿 경로에  
오류 페이지 파일을 만들어서 넣어두기만 하면 된다.  

참고 : `resources/static/templates/error/4xx.html` 은 400번대 에러를 모두 처리한다.  

### 뷰 선택 우선순위
`BasicErrorController`의 처리 순서  
1. 뷰 템플릿(자세한 순서)
- `resources/templates/error/500.html`
- `resources/templates/error/5xx.html`
2. 정적 리소스
- `resources/static/error/400.html`
- `resources/static/error/404.html`
- `resources/static/error/4xx.html`

3. 적용 대상이 없을 때 뷰 이름
- `resources/templates/error.html`  

구체적인 것이 덜 구체적인 것 보다 우선순위가 높다. 5xx, 4xx 라고 하면 각각 500대, 400대 오류를 처리해준다.  

## 스프링 부트 - 오류 페이지 2
`BasicErrorController` 컨트롤러는 다음 정보를 model에 담아서 뷰에 전달한다. 뷰 템플릿은 이 값을 활용 가능하다.  




  


















