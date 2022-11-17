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


