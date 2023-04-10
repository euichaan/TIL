# HTTP 상태코드
**클라이언트가 보낸 요청의 처리 상태를 응답에서 알려주는 기능**  
- 1xx(Informational) : 요청이 수신되어 처리중   
- 2xx(Successful) : 요청 정상 처리  
- 3xx(Redirection) : 요청을 완료하려면 추가 행동이 필요 
- 4xx(Client Error) : 클라이언트 오류, 잘못된 문법등으로 서버가 요청을 수행할 수 없음     
- 5xx(Server Error) : 서버 오류, 서버가 정상 요청을 처리하지 못함    

**모르는 상태 코드가 나타나면? -> 상위 코드로 해석해서 처리**   
## 상태 코드 상세 분석
### `1xx`
요청이 수신되어 처리중. 거의 사용하지 않음  
### `2xx`
클라이언트의 요청을 성공적으로 처리  
**200 OK(요청 성공)**  
```http
HTTP/1.1 200 OK 
Content-Type: application/json 
Content-Length: 34

{
"username": "young",
 "age": 20

}
```

**201 Created(요청 성공해서 새로운 리소스가 생성됨)**  
```http
HTTP/1.1 201 Created
Content-Type: application/json 
Content-Length: 34
Location: /members/100

{
"username": "young",
 "age": 20

}
```
생성된 리소스는 응답의 Location 헤더 필드로 식별  

**202 Accepted**   
요청이 접수되었으나 처리가 완료되지 않았음  
배치 처리 같은 곳에서 사용. 예)요청 접수 후 1시간 뒤에 배치 프로세스가 요청을 처리함  

**204 No Content**    
서버가 요청을 성공적으로 수행했지만, 응답 페이로드 본문에 보낼 데이터가 없음  
예)웹 문서 편집기에서 save버튼  
save 버튼의 결과로 아무 내용이 없어도 된다. save 버튼 눌러도 같은 화면을 유지해야 한다.  
결과 내용이 없어도 204 메시지(2xx)만으로 성공을 인식할 수 있다.  

### `3xx`
리다이렉션. 요청을 완료하기 위해 유저 에이전트의 추가 조치 필요  
웹 브라우저는 3xx응답의 결과에 Location 헤더가 있으면, Location 위치로 자동 이동(리다이렉트)  
![리다이렉션의 이해](https://github.com/euichanhwang/CS_study/blob/main/img/6.http-status-%EB%A6%AC%EB%8B%A4%EC%9D%B4%EB%A0%89%EC%85%98%EC%9D%98%20%EC%9D%B4%ED%95%B4.jpg)  

영구 리다이렉션 : 특정 리소스의 URI가 영구적으로 이동, 원래의 URL 사용하지 않고 검색 엔진에서도 변경을 인지   
- 예)/members ->/users  
- 예)/event -> /new-event  
- 301 Moved Permanently  
- 308 Permanent redirect  

**301 Moved Permanently**  
리다이렉트시 요청 메서드가 GET으로 변하고, 본문이 제거될 수 있음(MAY)  
![301](https://github.com/euichanhwang/CS_study/blob/main/img/6.http-status-301%20Moved%20Permanently.jpg)   

**308 Permanent Redirect**  
301과 기능은 같음. 리다이렉트 시 요청 메서드와 본문 유지(처음 POST를 보내면 리다이렉트도 POST 유지)  
![308](https://github.com/euichanhwang/CS_study/blob/main/img/6.http-status.308%20Permanent%20Redirect.jpg)  

일시 리다이렉션 : 일시적인 변경  
- 리소스의 URI가 일시적으로 변경, 따라서 검색 엔진 등에서 URL을 변경하면 안됨  
- PRG: Post/Redirect/Get  
- 예)주문 완료 후 주문 내역 화면으로 이동  

**302 Found**  
리다이렉트시 요청 메서드가 GET으로 변하고, 본문이 제거될 수 있음(MAY)  

**307 Temporary Redirect**  
302와 기능 같음. 리다이렉트시 요청 메서드와 본문 유지  

**303 See Other**  
302와 기능 같음. 리다이렉트 시 요청 메서드가 GET으로 변경  

**PRG:Post/Redirect/Get**  
POST로 주문 후에 웹 브라우저 새로고침하면? 새로고침은 다시 요청이므로 중복 주문이 될 수 있다.  
![PRG사용 전](https://github.com/euichanhwang/CS_study/blob/main/img/6.http-status-PRG%EC%82%AC%EC%9A%A9%EC%A0%84.jpg)  
결과 화면에서 새로고침 하면, 중복 주문이 들어간다.  
POST로 주문 후에 주문 결과 화면을 GET메서드로 리다이렉트  
새로고침해도 결과 화면을 GET으로 조회, 중복 주문 대신에 결과 화면만 GET으로 다시 요청  
![PRG사용 후](https://github.com/euichanhwang/CS_study/blob/main/img/6.http-status-PRG%EC%82%AC%EC%9A%A9%ED%9B%84.jpg)  
URL이 이미 POST->GET으로 리다이렉트. 새로고침 해도 GET으로 결과 화면만 조회  
307,303을 권장하지만 이미 많은 애플리케이션 라이브러리들이 302를 기본값으로 사용  

특수 리다이렉션 : 결과 대신 캐시를 사용

**304 Not Modified**  
캐시를 목적으로 사용.  
클라이언트에게 리소스가 수정되지 않았음을 알려준다. 따라서 클라이언트는 로컬 PC에 저장된 캐시를 재사용(캐시로 리다이렉트)  
304 응답은 응답에 메시지 바디를 포함하면 안된다.(로컬 캐시를 사용해야 하므로)  

### `4xx`
오류의 원인이 클라이언트에 있음  
**클라이언트가 이미 잘못된 요청, 데이터를 보내고 있기 때문에 똑같은 재시도가 실패함**  

**400 Bad Request**  
클라이언트가 잘못된 요청을 해서 서버가 요청을 처리할 수 없음  
요청 구문, 메시지 등등 오류. 클라이언트는 요청 내용을 다시 검토하고, 보내야함  
예)요청 파라미터가 잘못되거나, API 스펙이 맞지 않을 때  

**401 Unauthorized**  
클라이언트가 해당 리소스에 대한 인증이 필요함  
인증(Autentication) 되지 않음  
401 오류 발생시 응답에 WWW-Authenticate 헤더와 함께 인증 방법을 설명  

**403 Forbidden**  
서버가 요청을 이해했지만 승인을 거부함  
주로 인증 자격 증명은 있지만, 접근 권한이 불충분한 경우  
예) 어드민 등급이 아닌 사용자가 로그인은 했지만, 어드민 등급의 리소스에 접근하는 경우  

**404 Not Found**  
요청 리소스가 서버에 없음  
또는 클라이언트가 권한이 부족한 리소스에 접근할 때 해당 리소스를 숨기고 싶을 때  

### `5xx`  
서버 오류  
**서버에 문제가 있기 때문에 재시도 하면 성공할 수도 있음(복구가 되거나 등등)**  

**500 Internal Server Error**  
서버 문제로 오류 발생, 애매하면 500 오류  

**503 Service Unavailable**  
서비스 이용 불가  
서버가 일시적인 과부하 또는 예정된 작업으로 잠시 요청을 처리할 수 없음  
Retry-After 헤더 필드로 얼마뒤에 복구되는지 보낼 수도 있음  

 




