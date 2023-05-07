# Representational State Transfer = REST
REST는 아키텍처 스타일이다.  
아키텍처 패턴: 반복되는 문제 상황을 해결하는 도구  
아키텍처 스타일: 반복되는 아키텍처 디자인  
  
## REST API의 일반적인 인식
우리가 일반적으로 알고 있는 REST API에 대한 내용은 다음과 같다.
1. URI를 통해 자원을 지정    
2. HTTP 메서드: 자원에 대한 행위 표현  
CREATE POST /user  
READ GET /user/1  
UPDATE PUT /user/1  
DELETE DELETE /user/1  
  
## REST 창시자가 말하는 본질(Roy T.Fielding)
내 논문 어디에도 CRUD에 대한 내용은 없다.  
HTTP 메서드는 REST가 아니라 웹의 아키텍처 스타일의 일부다.  
HTTP에서 정의한 방식대로만 잘 쓴다면 REST는 딱히 할 말이 없다.  
  
=> Roy Fielding이 말하는 본질은 다음과 같다.  
**REST 아키텍처 스타일에 부합하는 API**  
1. Client-Server  
리소스를 관리하는 서버가 존재하고 다수의 클라이언트가 리소스를 소비하려고 네트워크를 통해 서버에 접근하는 구조를 의미한다. 이런 구조 중 우리에게 가장 친숙한 것이 바로 웹 애플리케이션이다.  
2. Stateless  
상태가 없다는 것은 클라이언트가 서버에 요청을 보낼 때 이전 요청의 영향을 받지 않음을 의미한다.  
HTTP는 기본적으로 상태가 없는 프로토콜이다. 따라서 HTTP를 사용하는 웹 애플리케이션은 기본적으로 상태가 없는 구조를 따른다.  
3. Cache  
서버에서 리소스를 리턴할 때 캐시가 가능한지 아닌지 명시할 수 있어야 한다.  
HTTP에서는 cache-control이라는 헤더에 이라는 헤더에 리소스의 캐시 여부를 명시할 수 있다.  
4. Uniform Interface  
시스템 또는 애플리케이션의 리소스에 접근할 때 인터페이스가 일관적이어야 한다는 뜻이다.  
URI, 요청의 형태와 응답의 형태가 애플리케이션 전반에 걸쳐 일관적이어야 한다는 것이 일관적인 인터페이스 방침이다.  
5. Layered System  
클라이언트가 서버에 요청을 할 때 여러 개의 레이어로 된 서버를 거칠 수 있다. 예를 들어 서버가 인증 서버, 캐싱 서버, 로드 밸런서를 거쳐서 최종적으로 애플리케이션에 도착한다고 가정하자. 이 사이의 레이어들은 요청과 응답에 어떤 영향을 미치지 않으며 클라이어는 서버의 레이어 존재 유무를 알지 못한다.  
5. Code-On-Demand  
이 제약은 선택사항이다. 클라이언트는 서버에 코드를 요청할 수 있고 서버가 리턴한 코드를 실행할 수 있다.  
(javascript)  
  
REST는 HTTP와 다르다. REST는 HTTP를 이용해 구현하기 쉽고 대부분 그렇게 구현하지만 엄밀히 말하면 REST는 아키텍처이고, HTTP는 REST 아키텍처를 구현할 때 사용하면 쉬운 프로토콜이다.  

## Uniform Interface에 대해 자세하게 알아보기
1. identification of resources(자원에 대한 식별)  
리소스가 URI로 식별되면 된다.  
2. manipulation of resources through representations(표현을 통한 자원에 대한 조작)  
representation 전송을 통해서 resource를 조작해야 한다.  
resource를 만들거나, 업데이트하거나, 삭제하거나 이럴 때 HTTP message에다가 표현을 담아서 전송을 하면 된다.  
  
3,4 번을 지키지 못하는 경우가 많다.  
3. self-descriptive messages(자기 서술적 메시지)
메시지는 스스로를 설명해야 한다.  
```text/plain
GET / HTTP/1.1
```
이 HTTP 요청 메시지는 뭔가 빠져있어서 self-descriptive 하지 못하다.  
```text/plain
GET / HTTP/1.1
Host: www.example.org
```
목적지를 추가하면 이제 self-descriptive  
```text/plain
HTTP/1.1 200 ok
[{"op": "remove", "path": "/a/b/c"}]
```
클라이언트가 이 응답을 받고 해석해야 하는데 어떤 문법인지 해석 불가능.
```text/plain
HTTP/1.1 200 ok
Content-Type: application/json
[{"op": "remove", "path": "/a/b/c"}]
```
Content-Type을 넣어줘야 한다. 여기까지 하면 self-descriptive냐? 그것은 아니다.  
op와 path가 무슨 의미인지 모름.  
```text/plain
HTTP/1.1 200 ok
Content-Type: application/json-patch+json
[{"op": "remove", "path": "/a/b/c"}]
```
json patch 명세를 보고 이제서야 이해가 가능하다.  
self-descriptive message라고 하면 메시지를 봤을 때 메시지의 내용으로 온전히 해석이 가능해야 한다.  
4. HATEOAS(hypermedia as the engine of application state)  
애플리케이션의 상태는 Hyperlink를 이용해 전이되어야한다.  
  
JSON으로도 만족이 가능하다.  
```text/plain
HTTP/1.1 200 OK
Content-Type: application/json
Link: </articles/1>; rel = "previous",
      </articles/3>; rel = "next";
{
  "title": "The second article",
  "contents" "blah blah..."
}
```
### 왜 Uniform Interface?  
독립적 진화  
- 서버와 클라이언트가 각각 독립적으로 진화한다.  
- 서버의 기능이 변경되어도 클라이언트를 업데이트할 필요가 없다.  
- REST를 만들게 된 계기: "How do I improve HTTP without breaking the Web."  
  




