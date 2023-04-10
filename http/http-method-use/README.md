# HTTP 메서드 활용 
1. **클라이언트에서 서버로 데이터 전송** 
2. **HTTP API 설계 예시**
## 클라이언트에서 서버로 데이터 전송
데이터 전달 방식은 크게 2가지
1. 쿼리 파라미터를 통한 데이터 전송(GET) - 주로 정렬 필터(검색어)  
2. 메시지 바디를 통한 데이터 전송(POST,PUT,PATCH) - 회원 가입, 상품 주문, 리소스 등록, 리소스 변경 등  

## `클라이언트에서 서버로 데이터 전송하는 4가지 상황`
1. 정적 데이터 조회(쿼리 파라미터 미사용)  
- 이미지, 정적 텍스트 문서  
- 정적 데이터는 일반적으로 쿼리 파라미터 없이 리소스 경로로 단순하게 조회가 가능하다(조회시 GET 사용)
```http
GET /static/star.jpg HTTP/1.1
Host: localhost:8080
```
2. 동적 데이터 조회    
- 주로 검색, 게시판 목록에서 정렬 필터(검색어)  
- 쿼리 파라미터 사용
```http
GET /search?q=hello&hl=ko HTTP/1.1
Host: www.google.com
```
- 서버에서 쿼리 파라미터를 기반으로 정렬 필터해서 결과를 동적으로 생성한다  
- 조회 조건을 줄여주는 필터, 조회 결과를 정렬하는 정렬 조건에 주로 사용  
- GET은 쿼리 파라미터를 사용해서 데이터를 전달  

3. HTML Form을 통한 데이터 전송
- 주로 회원 가입, 상품 주문, 데이터 변경 등에 사용  
- POST 전송으로 저장  
- Content-Type: application/x-www-form-urlencoded 사용   
- form의 내용을 메시지 바디를 통해 전송(key=value, 쿼리 파라미터 형식)  
- **HTML Form 전송은 GET,POST 만 지원**   
```http
POST /save HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded

username=kim&age=20
```
- GET 전송으로 조회 (**GET은 리소스 변경이 발생하는 곳에서 사용하면 안된다**)
```http
GET /members?username=kim&age=20 HTTP/1.1
Host: localhost:8080
```

4. HTTP API를 통한 데이터 전송 
- 서버 to 서버(백엔드 시스템 통신)  
- 앱 클라이언트(아이폰,안드로이드)  
- 웹 클라이언트(HTML Form 대신 자바스크립트 통한 통신에 사용)  
- POST,PUT,PATCH : 메시지 바디를 통해 데이터 전송   
- GET: 조회, 쿼리 파라미터로 데이터 전달  
- Content-Type:application/json 주로 사용  
```http
POST /members HTTP/1.1
Content-Type: application/json
{
"username": "young", 
"age": 20
}
```

## `HTTP API 설계 예시`
**HTTP API - 컬렉션**  
- POST 기반 등록  
- 예) 회원 관리 API 제공  
- 회원 목록 /members ->GET  
- 회원 등록 /members ->POST  
- 회원 조회 /members/{id} ->GET  
- 회원 수정 /members/{id} ->PATCH,PUT,POST  
- 회원 삭제 /members/{id} ->DELETE  
- `클라이언트는 등록될 리소스의 URI를 모른다`  
- `서버가 새로 등록된 리소스 URI를 생성해준다`  
```http
HTTP/1.1 201 Created
Location: /members/100
```  
- 컬렉션은 서버가 관리하는 리소스 디렉토리를 말한다  
- 서버가 리소스의 URI을 생성하고 관리  
- 여기서 컬렉션은 /members  


**HTTP API - 스토어**  
- PUT 기반 등록  
- 예) 정적 컨텐츠 관리, 원격 파일 관리  
- 파일 목록 /files ->GET  
- 파일 조회 /files/{filename} ->GET  
- 파일 등록 /files/{filename}->PUT
- 파일 삭제 /files/{filename} ->DELETE
- 파일 대량 등록 /files ->POST  
- `클라이언트가 리소스 URI를 알고 있어야 한다`  
- PUT /files/star.jpg  
- 클라이언트가 직접 리소스의 URI를 지정한다  
- 스토어는 클라이언트가 관리하는 리소스 저장소를 말한다  
- 클라이언트가 리소스의 URI를 알고 관리  
- 여기서 스토어는 /files  

**HTML FORM 사용**  
- 웹 페이지 회원 관리  
- GET,POST 만 지원. 제약이 있다  
- 회원 목록 /members -> GET    
- 회원 등록 폼 /members/new -> GET    
- 회원 등록 /members/new, /members -> POST    
- 회원 조회 /members/{id} -> GET    
- 회원 수정 폼 /members/{id}/edit -> GET  
- 회원 수정 /members/{id}/edit, /members/{id} -> POST  
- 회원 삭제 /members/{id}/delete -> POST  

**컨트롤 URI,컨트롤러(controller)**  
- 동사로 된 리소스 경로를 사용  
- POST의 /new, /edit, /delete가 컨트롤 URI  
- HTTP 메서드로 해결하기 애매한 경우 사용  





 




