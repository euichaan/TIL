## 11월 17일
### thymeleaf th:id
th:each 문과 같은 경우, onclick으로 값을 빼내고 싶어도 쉽지 않다.
id를 다르게 구별하는 전략을 주로 쓰는데, th:id를 통해 객체의 값을 id로 쓰면 구분 가능하다.
```html
th:id="'g' + ${question.id}"
```
이런식으로 문자를 추가하고 싶을 때는 '문자열' + 를 붙이면 된다.

### jQuery
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

## 11월 18일
### LocalDate를 LocalDateTime으로 변환하기
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

## 11월 19일 
### 서블릿 예외 처리
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
#### `Exception`의 경우 
서버 내부에서 처리할 수 없는 오류가 발생한 것으로 생각. HTTP 상태 코드 500을 반환한다.  
#### `response.sendError(HTTp 상태 코드, 오류 메시지)`
호출한다고 당장 예외가 발생하는 것은 아니지만, 서블릿 컨테이너에게 오류가 발생했다는 점을 전달할 수 있다.  
이 메서드를 사용하면 HTTP 상태 코드와 오류 메시지도 추가할 수 있다.  
```java
WAS <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(response.sendError())
```
`response` 내부에 오류가 발생했다는 상태 저장. 서블릿 컨테이너는 고객에게 응답 전에 response에 sendError()가  
호출되었는지 확인한다. 그리고 호출되었다면 설정한 오류 코드에 맞추어 기본 오류 페이지를 보여준다.  

## 11월 19일
### jQuery
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

- `focusout과 함께 사용하기`#  
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




















