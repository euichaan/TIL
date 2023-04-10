# URI와 웹 브라우저 요청 흐름  
## URI(Uniform Resource Identifier)
- Uniform: 리소스 식별하는 통일된 방식  
- Resource: 자원,URI로 식별할 수 있는 모든 것(제한 없음)  
- Identifier: 다른 항목과 구분하는데 필요한 정보  
## URI의 분류
URI는 로케이터(locator), 이름(name) 또는 둘 다 추가로 분류될 수 있다  
URL: Resource Locator. 리소스의 위치를 지정    
URN: Resource Name. 리소스에 이름을 부여  
URN 이름만으로 실제 리소스를 찾을 수 있는 방법이 보편화 되지 않음  
**앞으로 URI를 URL 과 같은 의미로 생각**  
![URI](https://github.com/euichanhwang/CS_study/blob/main/img/2.uri-webbrowser.pdf-5.jpg)  
## URL,URN 예시
URL: foo://example.com:8042/over/there?name=ferret#nose  
URN: urn:example:animal:ferret:nose  

## `URL 분석`
scheme://[userinfo@]host[:port][/path][?query][#fragment]   
https://<hi1>www<hi5>.<hi4>google<hi2>.com:443/search<hi3>?q=hello&hl=ko   
- 프로토콜(https)  
- 호스트명(<hi1>www<hi5>.<hi4>google<hi2>.com)  
- 포트 번호(443)  
- 패스(/search)  
- 쿼리 파라미터(q=hello&hl=ko)  

### scheme 
- `scheme:`//[userinfo@]host[:port][/path][?query][#fragment]  
`https:`//<hi1>www<hi5>.<hi4>google<hi2>.com:443/search<hi3>?q=hello&hl=ko  
- 주로 프로토콜 사용  
- **프로토콜: 어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙.** https,http,ftp 등등  
- http는 80 포트, https는 443 포트를 주로 사용, 포트는 생략 가능  
- https는 http에 보안 추가(HTTP Secure)  

### userinfo
- URL에 사용자정보를 포함해서 인증  
- 거의 사용하지 않음  

### host
scheme://[userinfo@]`host`[:port][/path][?query][#fragment]   
https://`www.google.com`:443/search?q=hello&hl=ko  
- 호스트명  
- 도메인명 또는 IP 주소를 직접 사용가능  

### PORT  
scheme://[userinfo@]host`[:port]`[/path][?query][#fragment]  
<hi1>https://<hi2>www<hi3>.<hi4>google.com<hi5>: `443`/search?q=hello&hl=ko  
- 포트(PORT)  
- 접속 포트  
- 일반적으로 생략, 생략 시 http는 80, https는 443  

### path
scheme://[userinfo@]host[:port]`[/path]`[?query][#fragment]  
<hi1>https://<hi2>www<hi3>.<hi4>google.com<hi5>: 443/`search`?q=hello&hl=ko 
- 리소스 경로, 계층적 구조  
- 예)home/file1.jpg , /members , /members/100, /items/iphone12  

### query
scheme://[userinfo@]host[:port][/path]`[?query]`[#fragment]  
<hi1>https://<hi2>www<hi3>.<hi4>google.com<hi5>: 443/search?`q=hello&hl=ko`  
- key=value 형태  
- **?로 시작, &로 추가 가능. ?keyA=valueA&keyB=valueB**    
- query parameter, query string 등으로 불림. 웹서버에 제공하는 파라미터, 문자 형태  

### fragment
scheme://[userinfo@]host[:port][/path][?query]`[#fragment]`  
<hi1>https<hi2>://<hi3>docs.<hi4>spring.<hi5>io/spring-boot/docs/current/reference/html/getting-
started.html`#getting-started-introducing-spring-boot`
- fragment  
- html 내부 북마크 등에 사용  
- 서버에 전송하는 정보 아님  

## `웹 브라우저 요청 흐름`  
https://<hi1>www<hi5>.<hi4>google<hi2>.com:443/search<hi3>?q=hello&hl=ko  
1. <hi1>www.<hi2>google.<hi3>com 을 보고 DNS 조회. HTTP PORT는 생략하는 경우 많음  
2. 웹 브라우저에서 HTTP 요청 메시지 생성  
```http
GET /search?q=hello&hl=ko HTTP/1.1 
Host: www.google.com //헤더 부분 
```  
URL의 path부터 query 정보가 들어간다.  
3. HTTP 메시지 전송  
![HTTP 메시지 전송 과정](https://github.com/euichanhwang/CS_study/blob/main/img/2.uri-webbrowser.pdf-22.jpg)  
패킷을 전송할 때 , 패킷 정보는  
출발지 IP, PORT  
목적지 IP, PORT  
**전송 데이터** -> 전송 데이터에 위의 HTTP 메시지가 들어간다.  
![패킷 정보](https://github.com/euichanhwang/CS_study/blob/main/img/2.uri-webbrowser.pdf-24.jpg)  
4. 웹 브라우저에서 구글 서버로 요청 패킷 전달  
5. 요청 패킷 도착. 구글 서버는 TCP/IP 패킷을 까서 버리고 HTTP 메시지 꺼낸 후 분석한다.  
6. HTTP 응답 메시지 만든다  
```http
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Length: 3423

<html> 
 <body>...</body>
</html>
```
7. 구글 서버에서 웹 브라우저로 응답 패킷을 전달  
8. 웹 브라우저로 응답 패킷 도착  
9. 웹 브라우저에서 HTML을 렌더링해서 화면에 보여준다.  








