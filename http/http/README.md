# HTTP(HyperText Transfer Protocol)  
**HTTP 메시지에 모든 것을 전송**  
- HTML, TEXT  
- IMAGE, 음성, 영상, 파일  
- JSON, XML(API)  
- 거의 모든 형태의 데이터 전송 가능  
- 서버간에 데이터를 주고 받을 때도 대부분 HTTP 사용  

**기반 프로토콜**  
TCP: HTTP/1.1, HTTP/2  
UDP: HTTP/3  
현재 HTTP/1.1 주로 사용, HTTP/2,HTTP/3 도 점점 증가  

## `HTTP 특징`
### 클라이언트 서버 구조
- 클라이언트와 서버를 분리. (비즈니스 로직,data -> 서버 / UI,사용성 -> 클라이언트) 각각 독립적 진화  
- Request Response 구조  
- 클라이언트는 서버에 요청을 보내고, 응답을 대기  
- 서버가 요청에 대한 결과를 만들어서 응답  

### `무상태 프로토콜(Stateless)`    
- 서버가 클라이언트의 상태를 보존 X  
- 장점: 서버 확장성 높음(스케일 아웃: 장비를 추가해서 확장하는 방식)  
- 단점: 클라이언트가 추가 데이터 전송  

#### Stateful, Stateless의 차이점(예시)
![차이](https://github.com/euichanhwang/CS_study/blob/main/img/3.http.pdf-9.jpg)  
![차이](https://github.com/euichanhwang/CS_study/blob/main/img/3.http.pdf-10.jpg)  
![차이](https://github.com/euichanhwang/CS_study/blob/main/img/3.http.pdf-11.jpg)  
![차이](https://github.com/euichanhwang/CS_study/blob/main/img/3.http.pdf-12.jpg)  
![차이](https://github.com/euichanhwang/CS_study/blob/main/img/3.http.pdf-13.jpg)  

#### Stateful, Stateless의 차이점 정리
- Stateful(상태 유지): 중간에 다른 점원으로 바뀌면 안된다.(중간에 다른 점원으로 바뀔 때 상태 정보를 다른 점원에게 미리 알려줘야 한다)  
- Stateless(무상태): 중간에 다른 점원으로 바뀌어도 된다. 갑자기 클라이언트 요청이 증가해도, 서버를 대거 투입할 수 있다.    
- 무상테는 응답 서버를 쉽게 바꿀 수 있다 -> **무한한 서버 증설 가능**  

![차이](https://github.com/euichanhwang/CS_study/blob/main/img/3.http.pdf-15.jpg)
![차이](https://github.com/euichanhwang/CS_study/blob/main/img/3.http.pdf-16.jpg)
![차이](https://github.com/euichanhwang/CS_study/blob/main/img/3.http.pdf-17.jpg)
![차이](https://github.com/euichanhwang/CS_study/blob/main/img/3.http.pdf-18.jpg)
![차이](https://github.com/euichanhwang/CS_study/blob/main/img/3.http.pdf-19.jpg)

#### Stateless의 실무 한계
모든 것을 무상태로 설계 할 수 있는 경우도 있고 없는 경우도 있다.  
- 무상태: 예)로그인이 필요 없는 단순한 서비스 소개 화면
- 상태 유지: 예)로그인  
- 로그인한 사용자의 경우 로그인 했다는 상태를 서버에 유지    
- 일반적으로 브라우저 쿠키와 서버 세션등을 사용해서 상태 유지  
- 상태 유지는 최소한만 사용  

## 비연결성
### 연결을 유지하는 모델
0. 서버와 TCP/IP 연결  
1. 클라이언트 1 요청  
2. 서버 응답  

`이 과정에서 서버는 연결을 계속 유지, 서버 자원을 소모`  

### 연결을 유지하지 않는 모델 
0. 서버와 TCP/IP 연결  
1. 클라이언트 1 요청  
2. 서버 응답  
3. TCP/IP 연결 종료  

`서버는 연결 유지 X, 최소한의 자원 유지`  

**HTTP 는 기본이 연결을 유지하지 않는 모델**  
- 일반적으로 초 단위 이하의 빠른 속도로 응답  
- 서버 자원을 매우 효율적으로 사용할 수 있음  
- 1시간 동안 수천명이 서비스를 사용해도 실제 서버에서 동시에 처리하는 요청은 수십개 이하로 매우 작음  
- 예) 웹 브라우저에서 계속 연속해서 검색 버튼을 누르지는 않는다.  

### 비 연결성 한계와 극복
- **TCP/IP 연결을 새로 맺어야 함 - 3 way handshake 시간 추가**  
- 웹 브라우저로 사이트를 요청하면 HTML, 자바스크립트, css, 추가 이미지 등 수 많은 자원이 함께 다운로드  
- **HTTP 지속 연결로 문제 해결**  
- HTTP/2, HTTP/3에서 더 많은 최적화  

![HTTP 초기](https://github.com/euichanhwang/CS_study/blob/main/img/3.http.pdf-33.jpg)
![HTTP 지속 연결](https://github.com/euichanhwang/CS_study/blob/main/img/3.http.pdf-34.jpg)

스테이스리스를 기억하자 - 서버 개발자들이 어려워하는 업무
- 정말 같은 시간에 딱 맞추어 발생하는 대용량 트래픽  
- 예) 학과 수업 등록, 선착순 할인 이벤트 -> 수천명 동시 요청  

## `HTTP 메시지`
### HTTP 메시지 구조 
![HTTP 메시지 구조](https://github.com/euichanhwang/CS_study/blob/main/img/3.http.pdf-39.jpg)  

### HTTP 요청 메시지
```http
GET /search?q=hello&hl=ko HTTP/1.1
Host: www.google.com
```
- start-line = **request-line** / status-line
- `request-line = method SP(공백) request-target SP HTTP-version CRLF(엔터)`  
- method SP = HTTP 메서드(GET: 조회)  
- request-target = 요청 대상(/search?q=hello&hl=ko)  
- HTTP-version  

#### 상세 설명 - method
- `method(HTTP 메서드)`  
- 종류: GET,POST,PUT,DELETE...
- 서버가 수행해야 할 동작 지정  
- GET: 리소스 조회  
- POST: 요청 내역 처리

#### 상세 설명 - request-target
- `request-target`  
- `/search?q=hello&hl=ko`  
- absolute-path<hi1>[<hi2>?query]<hi3>(절대경로[?쿼리])  
- 절대경로 = "/" 로 시작하는 경로  
- 참고로 <hi1>http<hi2>://<hi3>...?x=y 와 같이 다른 유형의 경로지정 방법도 있다.  

#### 상세 설명 - HTTP-version
- HTTP version 명시

### HTTP 응답 메시지 
```http
HTTP/1.1 200 OK 
Content-Type: text/html;charset=UTF-8
Content-Lentgh: 3423

<html>
 <body>...</body>
</html> 
```
- start-line = request-line / **status-line**
- `status-line = HTTP-version SP status-code SP reason-phrase CRLF`  
- HTTP-version = HTTP 버전  
- status-code = HTTP 상태 코드 : 요청 성공, 실패를 나타냄(200:성공, 400:클라이언트 요청 오류 500:서버 내부 오류)  
- reason-phrase(이유 문구) = 사람이 이해할 수 있는 짧은 상태 코드 설명 글  

### HTTP 헤더  
![HTTP 헤더](https://github.com/euichanhwang/CS_study/blob/main/img/3.http.pdf-46.jpg)  
- HTTP 전송에 필요한 모든 부가정보  
- 예)메시지 바디의 내용, 메시지 바디의 크기, 압축, 인증, 요청 클라이언트(브라우저) 정보,서버 애플리케이션 정보, 캐시 관리 정보...     
- 필요시 임의의 헤더 추가 가능  

### HTTP 메시지 바디  
- 실제 전송할 데이터  
- HTML문서, 이미지, 영상, JSON 등등 byte로 표현할 수 있는 모든 데이터 전송 가능  



  






