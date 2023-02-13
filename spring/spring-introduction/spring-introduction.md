# 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
스프링을 배우는 이유 - 실무에서 제대로 동작하는 웹 어플리케이션을 개발하기 위해서  
- 스프링 프로젝트 생성  
- 스프링 부트로 웹 서버 실행  
- 회원 도메인 개발  
- 웹 MVC 개발  
- DB연동 - JDBC, JPA, 스프링 데이터 JPA  
- 테스트 케이스 작성  
  
https://start.spring.io/ - 스프링 부트 기반으로 프로젝트를 만들어주는 사이트  
  
## 스프링 부트 라이브러리
spring-boot-starter-web  
- spring-boot-starter-tomcat : 톰캣(웹서버)  
- spring-webmvc : 스프링 웹 MVC  
  
spring-boot-starter-thymeleaf : 타임리프 템플릿 엔진(View)  
  
spring-boot-starter(공통) : 스프링 부트 + 스프링 코어 + 로깅  
spring-boot
  - spring-core  
spring-boot-starter-logging
  - logback(구현체), slf4j(인터페이스)  
  
## 스프링 부트가 제공하는 Welcome Page 기능
`static/index.html`을 올려두면 Welcom page 기능을 제공한다.  
  
웹 애플리케이션에서 첫 번째 진입점 - Controller.  
  
컨트롤러에서 리턴 값으로 문자를 반환하면 뷰 리졸버(viewResolver)가 화면을 찾아서 처리한다.  
- 스프링 부트 템플릿엔진 기본 viewName 매핑  
- resources:temlates + {viewName} + .html  
  
## 스프링 웹 개발 기초  
웹을 개발한다는 것? -> 세 가지가 있다.  
1. 정적 컨텐츠  
2. MVC와 템플릿 엔진  
3. API  
  
### 정적 컨텐츠
정적 컨텐츠 : 파일을 그대로 웹 브라우저에 내려주는 것  
MVC와 템플릿 엔진 : 서버에서 변형을 해서 내려주는 방식  
API : JSON 이라는 데이터 포맷으로 클라이언트에게 데이터를 전달.  
  
localhost:8080/hello-static.html  
내장 톰캣 서버가 요청을 받음 -> 스프링에게 넘김 -> hello-static 관련 컨트롤러 찾아봄  
-> resources: static/hello-static.html을 찾음 -> 웹 브라우저에 반환.  
  
### MVC와 템플릿 엔진
MVC :  Model, View, Controller  
관심사를 분리해야 한다. 역할과 책임.  
뷰는 화면을 그리는 데 집중해야 함. 컨트롤러와 모델은 비지니스 로직, 내부 처리 중심.  

localhost:8080/hello-mvc  
내장 톰캣 서버를 먼저 거침 -> helloController에 매핑 되어있는 것을 보고 호출.  
-> viewResolver가 동작. templates/hello-template.html -> 변환 한 HTML을 반환.  
  
### API
@ResponseBody : HTTP BODY 부분에 데이터를 직접 넣어주겠다. viewResolver를 사용하지 않음.  
객체 반환하고 ResponseBody로 반환하면 기본은 JSON으로 반환한다.  
  
localhostL8080/hello-api -> 내장 톰캣 서버 -> 스프링에게 전달 -> @ResponseBody가 붙어있음 ->  
HttpMessageConverter가 동작(기존에는 viewResolver가 동작)  
  
hello 객체를 JSON 포맷으로 바꿔서 웹 브라우저에 응답.  
클라이언트의 `HTTP Accept 헤더`와 `서버의 컨트롤러 반환 타입 정보` 둘을 조합해서 HttpMessageConverter` 선택.  
  
## 회원 관리 예제 - 백엔드 개발  
데이터 : 회원ID, 이름  
기능 : 회원 등록, 조회  
아직 데이터 저장소가 선정되지 않음(가상의 시나리오)  
  
컨트롤러 : 웹 MVC의 컨트롤러 역할  
서비스 : 핵심 비즈니스 로직 구현  
리포지토리 : 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리  
도메인 : 비즈니스 도메인 객체. 예) 회원,주문,쿠폰 등등 주로 데이터베이스에 저장하고 관리됨  
  
아직 데이터 저장소가 선정되지 않아서, 우선 인터페이스로 구현 클래스를 변경할 수 있도록 설계  
초기 개발 단계에서는 구현체로 가벼운 메모리 기반의 데이터 저장소 사용(MemoryMemberRepository)  
  
`@AfterEach` : 한번에 여러 테스트를 실행하면 메모리 DB에 직전 테스트의 결과가 남을 수 있다.  
이전 테스트 때문에 다음 테스트가 실패할 가능성이 있다. @AfterEach를 사용하면 각 테스트가 종료될 때 마다  
이 기능을 실행한다.  
  
서비스는 비즈니스에 의존적으로 설계.  
빌드 될 때 테스트 코드는 실제 코드에 포함되지 않는다.  
  
기존에는 회원 서비스가 MemoryMemberRepository를 직접 생성하게 했다.  
```java
public class MemberService {
  private final MemberRepository memberRepository = new MemoryMemberRepository();
}
```
바뀐 구조는 회원 리포지토리의 코드가 회원 서비스 코드를 DI 가능하게 변경한다.  
MemberService입장에서 new를 하는게 아니라 외부에서 주입받는다.  
```java
public class MemberService {
  private final MemberRepository memberRepository;

  public MemberService(MemberRepository memberRepository) {
    this.memberRepository = memberRepository;
  }
}
```  
MemberController가 MemberService를 의존.  
@Controller : 스프링 컨테이너가 컨트롤러 애노테이션이 붙은 클래스의 객체를 생성해서 넣어둠. 스프링이 관리  
스프링 컨테이너로부터 받아서 쓰도록 바꿈. new로 memberSerivce를 만들 필요 없이 하나만 생성해서 공유.  
  
스프링 컨테이너에 하나만 등록 @Autowired  
생성자에 @Autowired가 있으면 스프링이 연관된 객체를 스프링 컨테이너에서 찾아서 넣어준다.  
이렇게 객체 의존관계를 외부에서 넣어주는 것을 DI(Dependency Injection, 의존성 주입)이라 한다.    
  
스프링 컨테이너에서 관리하는 memberService를 넣어줘야 하는데 memberService가 스프링 빈으로 등록되어 있지 않다.  
  
MemberController가 생성될 때 스프링 빈에 등록되어있는 MemberService 객체를 가져다가 넣어준다. DI  
  
스프링 빈을 등록하는 2가지 방법  
- 컴포넌트 스캔과 자동 의존관계 설정  
- 자바 코드로 직접 스프링 빈 등록  
@Component 애노테이션이 있으면 스프링 빈으로 자동 등록된다.  
@Component 를 포함하는 다음 애노테이션도 스프링 빈으로 자동 등록된다. (@Controller, @Service, @Repository)  
  
@ComponentScan의 대상은 HelloSpringApplication를 포함한 하위 패키지만 해당된다.  
  
@Configuration을 사용해서 자바 코드를 직접 스프링 빈으로 등록할 수 있다.  
DI에는 필드 주입, setter 주입, 생성자 주입이 있다. 생성자 주입을 권장한다.  
생성자 주입을 사용하면 애플리케이션 조립될 때 주입되고 끝난다.  
  
실무에서 주로 정형화된 컨트롤러, 서비스, 리포지토리 같은 코드는 컴포넌트 스캔을 사용한다.  
그리고 정형화 되지 않거나, 상황에 따라 구현 클래스를 변경해야 하면 설정을 통해 스프링 빈으로 등록한다.  
  
`@Autowired` 를 통한 DI는 `helloController`, `memberService` 등과 같이 스프링이 관리하는 객체에서만 동작.  
스프링 빈으로 등록하지 않고 내가 직접 생성한 객체에서는 동작하지 않는다.  
  
컨트롤러가 정적 파일보다 우선순위가 높다.  
  
html의 name : 서버로 보내는 키  
스프링이 setter 메서드로 name을 넣어준다.  
  
DataSource는 데이터베이스 커넥션을 획득할 때 사용하는 객체이다.  
스프링부트는 데이터베이스 커넥션 정보를 바탕으로 DataSource를 생성하고 스프링 빈으로 만들어둔다.  
그래서 DI를 받을 수 있다.  
  
다형성을 활용 - 인터페이스를 두고, 구현체 바꿔끼기.  
DI 덕분에 편리하게 활용.  
  
개방-폐쇄 원칙(OCP, Open-Closed Principle)  
확장에는 열려있고, 수정, 변경에는 닫혀있다.  
스프링의 DI를 사용하면 기존 코드를 전혀 손대지 않고, 설정만으로 구현 클래스를 변경할 수 있다.  
  
테스트할 때는 필드 주입 가능.  
`@SpringBootTest` : 스프링 컨테이너와 테스트를 함께 실행한다.  
`@Transactional` : 테스트 케이스에 이 애노테이션이 있으면, 테스트 시작 전에 트랜잭션을 시작하고,  
테스트 완료 후에 항상 롤백한다. 이렇게 하면 DB에 데이터가 남지 않으므로 다음 테스트에 영향을 주지 않는다.  
  
## JPA
기존의 반복 코드는 물론이고, 기본적인 SQL도 JPA가 직접 만들어서 실행해준다.  
JPA를 사용하면, SQL과 데이터 중심의 설계에서 `객체 중심의 설계`로 패러다임을 전환을 할 수 있다.  
JPA를 사용하면 개발 생산성을 크게 높일 수 있다.  
  
spring.jpa.show-sql=true // JPA가 날리는 sql을 볼 수 있음  
spring.jpa.hibernate.ddl-auto=none // 자동 테이블 생성 기능은 끔  
  
Object relational mapping(ORM) : 객체와 RDB의 테이블을 매핑 -> 애노테이션으로  
JPQL : 객체를 대상으로 쿼리를 날림. SQL로 번역  
PK 기반이 아닌 나머지는 JPQL을 작성해야 함.  
  
## 스프링 데이터 JPA
스프링 데이터 JPA는 JPA를 편리하게 사용하도록 도와주는 기술. 따라서 JPA를 먼저 학습한 후에 사용해야 한다.  
스프링 데이터 JPA가 SpringDataJpaMemberRepository를 스프링 빈으로 자동 등록해준다.  
  
스프링 컨테이너에서 memberRepository를 찾는데, SpringDataJpaRepository를 보고 넣어준다.  
- 인터페이스를 통한 기본적인 CRUD  
- `findByName()`, `findByEmail()` 처럼 메서드 이름 만으로 조회 기능 제공  
- 페이징 기능 자동 제공  
복잡한 동적 쿼리는 Querydsl이라는 라이브러리를 사용하면 된다.  
  
## AOP  
모든 메서드의 호출 시간을 측정하고 싶다면?  
  
회원가입, 회원 조회에 시간을 측정하는 기능은 핵심 관심 사항이 아니다.  
시간을 측정하는 로직은 공통 관심 사항이다.  
시간을 측정하는 로직과 핵심 비즈니스 로직이 섞여서 유지보수가 어렵다.  
시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵다.  
시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 한다. 

AOP : Aspect Oriented Programming  
공통 관심 사항(cross-cutting concern) vs 핵심 관심 사항(core concern) 분리  
원하는 곳에 공통 관심 사항을 적용  
  
회원가입, 회원 조회 등 핵심 관심사항과 시간을 측정하는 공통 관심 사항을 분리한다.  
시간을 측정하는 로직을 별도의 공통 로직으로 만들었다.  
  
AOP 적용 후에는 가짜 스프링 빈을 앞에 세워둠. joinPoint.proceed()를 호출하면 실제 memberService 호출.  
