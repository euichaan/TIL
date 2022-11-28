# 11월 21일
## HTTP 상태 코드 405 에러
`405 Method Not Allowed`  
메서드 매칭이 되지 않아서 생기는 오류.  
`/matching/new` 가 POST 방식인데 해당 URL을 GET으로 호출하면 405 error 발생.  
프로젝트에서는 `templates/error/4xx.html` 와 같이 400번 대 error를 처리하는 view 템플릿으로 해결함  

# 11월 25일
영어 텍스트가 줄바꿈 되지 않을 때  
```css
style="word-wrap: break-word"
```

# 11월 26일 
## javascript
Number("문자열") 을 이용하면 문자형 데이터를 숫자형 데이터로 바꿀 수 있다.  
```javascript
let t = Number("500") //"500" -> 500
```  
Boolean() 메서드에 데이터를 입력하면 true 또는 false를 반환한다.  
Boolean() 메서드는 숫자 0과 null, undefined, 빈 문자(" ")를 제외한 모든 데이터에 대해 true를 반환한다.  

undefined는 다음과 같이 변수에 값이 등록되기 전의 기본값이고, null은 변수에 저장된 값이 null인 경우.  
null은 변수에 저장된 데이터를 비우고자 할 때 사용하는 값이다.  
```javascript
let s; //undifined
let t = hello;
t = null;
```  
typeof 를 이용해 지정한 데이터 또는 변수에 저장된 자료형을 알고 싶을 때 사용한다.  

# 11월 27일
## 동시성 문제를 해결하기 위한 방법 - 1부
[동시성 문제 해결 1부](https://chan9.tistory.com/157)  

# 11월 28일
## 동시성 문제를 해결하기 위한 방법 - 2부
[동시성 문제 해결 1부](https://chan9.tistory.com/158)  
1. Synchronized 키워드를 활용하는 방법
2. Database Lock을 사용하는 방법
3. Redis를 사용하는 방법  


