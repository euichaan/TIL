# 스프링 5 프로그래밍 입문
# Chapter 02 스프링 시작하기
스프링 프레임워크의 주요 특징  
- 의존 주입(DI) 지원  
- AOP(Aspected-Oriented Programming) 지원  
- MVC 웹 프레임워크 제공  
- JDBC, JPA 연동, 선언적 트랜잭션 처리 등 DB 지원  
  
`@Configuration`: 해당 클래스를 스프링 설정 클래스로 지정한다.  
스프링이 생성하는 객체를 빈(Bean) 객체라고 부르는데, 이 빈 객체에 대한 정보를 담고 있는 메서드가 `greeter()` 메서드이다. 이 메서드에는 @Bean 애노테이션이 붙어 있다.  
@Bean 애노테이션을 메서드에 붙이면 **해당 메서드가 생성한 객체를 스프링이 관리하는 빈 객체로 등록한다.**  

@Bean 애노테이션을 붙인 **메서드의 이름은 빈 객체를 구분**할 때 사용한다. 예를 들어 스프링이 10~14행에서 생성한 객체를 구분할 때 greeter라는 이름을 사용한다. 이 이름은 빈 객체를 참조할 때 사용된다.  
@Bean 애노테이션을 붙인 메서드는 객체를 생성하고 알맞게 초기화해야 한다.  
  
`AnnotationConfigApplicationContext` 클래스는 자바 설정에서 정보를 읽어와 빈 객체를 생성하고 관리한다.  
`AnnotationConfigApplicationContext` 는 AppContext에 정의한 @Bean 설정 정보를 읽어와 Greeter 객체를 생성하고 초기화한다.  
  
## 스프링은 객체 컨테이너
스프링의 핵심 기능은 객체를 생성하고 초기화하는 것이다. 이와 관련된 기능은 ApplicationContext라는 인터페이스에 정의되어 있다.  
BeanFactory 인터페이스는 **객체 생성과 검색**에 대한 기능을 정의한다. 에를 들어 getBean() 메서드가 정의되어 있다. 객체를 검색하는 것 이외에 싱글톤/프로토타입 빈인지 확인하는 기능도 제공한다.  
ApplicationContext 인터페이스는 메시지, 프로필/환경 변수 등을 처리할 수 있는 기능을 추가로 정의한다.  
  
구현 클래스는 설정 정보로부터 빈이라고 불리는 객체를 생성하고 그 객체를 내부에 보관한다. 그리고 getBean() 메서드를 실행하면 해당하는 빈 객체를 제공한다.  
```java
// 1. 설정 정보를 이용해서 빈 객체를 생성한다
final AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(
			AppContext.class);

// 2. 빈 객체를 생성한다
final Gretter g = ctx.getBean("greeter", Greeter.class);
```
ApplicationContext(또는 BeanFactory)는 빈 객체의 **생성, 초기화, 보관, 제거** 등을 관리하고 있어서 ApplicationContext를 컨테이너라고도 부른다.  
```
스프링 컨테이너는 내부적으로 빈 객체와 빈 이름을 연결하는 정보를 갖는다. 예를 들어 Greeter 타입의 객체를 greeter라는 이름의 빈으로 설정했다고 하자.
이 경우 컨테이너는 다음 그림처럼 greeter 이름과 Greeter 객체를 연결한 정보를 관리한다.
이름과 실제 객체의 관계뿐만 아니라 실제 객체의 생성, 초기화, 의존 주입 등 스프링 컨테이너는 객체 관리를 위한 다양한 기능을 제공한다.
```
## 싱글톤(Singleton) 객체
별도 설정을 하지 않을 경우 스프링은 한 개의 빈 객체만을 생성하며, 이때 빈 객체는 '싱글톤(singleton)' 범위를 갖는다고 표현한다.  
싱글톤은 단일 객체를 의미하는 단어로서 스프링은 기본적으로 한 개의 @Bean 애노테이션에 대해 한 개의 빈 객체를 생성한다.  
  
# Chapter 03 스프링 DI
DI는 Dependency Injection의 약자로 의존 주입이라고 번역한다. 여기서 말하는 의존은 객체 간의 의존을 의미한다.  
  
**한 클래스가 다른 클래스의 메서드를 실행할 때 이를 '의존'한다고 표현한다.** 앞서 코드에서는 `MemberRegisterService`가 `MemberDao` 클래스에 의존한다고 표현할 수 있다.  
```
의존은 변경에 의해 영향을 받는 관계를 의미한다. 예를 들어 MemberDao의 insert() 메서드 이름을 insertMember()로 변경하면 이 메서드를 사용하는 MemberRegisterService 클래스의 소스 코드도 함께 변경된다. 이렇게 변경에 따른 영향이 전파되는 관계를 '의존'한다고 표현한다.
```
## DI를 통한 의존 처리
**DI(Dependency Injection)는 의존하는 객체를 직접 생성하는 대신 의존 객체를 전달받는 방식을 사용한다.**
```java
public class MemberRegisterService {

	private MemberDao memberDao;

	public MemberRegisterService(final MemberDao memberDao) {
		this.memberDao = memberDao;
	}
...
```
직접 의존 객체를 생성하지 않고 생성자를 통해서 의존 객체를 전달받는다. 즉 생성자를 통해 MemberRegisterService가 의존하고 있는 MemberDao 객체를 주입 받은 것이다.  
이 코드는 DI(의존 주입) 패턴을 따르고 있다. DI를 적용한 결과 MemberRegisterService가 클래스를 사용하는 코드는 다음과 같이 MemberRegisterService 객체를 생성할 때 생성자에 MemberDao 객체를 전달해야 한다.  
```java
MemberDao dao = new MemberDao();
MemberRegisterService svc = new MemberRegisterService(dao);
```
**변경의 유연함 때문이다.**  
## DI와 의존 객체 변경의 유연함
```java
MemberDao memberDao = new MemberDao();
MEmberRegisterService regSvc = new MemberRegisterService(memberDao);
ChagedPasswordService pwdSvc = new ChangePasswordService(memberDao);

// MemberDao 대신 CachedMemberDao 를 사용하도록 변경 
MemberDao memberDao = new CachedMemberDao();
MEmberRegisterService regSvc = new MemberRegisterService(memberDao);
ChagedPasswordService pwdSvc = new ChangePasswordService(memberDao);
```
## 객체 조립기
객체를 생성하고 의존 객체를 주입해주는 클래스를 따로 작성. 의존 객체를 주입한다는 것은 서로 다른 두 객체를 조립한다고 생각할 수 있는데, 이런 의미에서 이 클래스를 조립기라고도 표현한다.  
조립기는 객체를 생성하고 의존 객체를 주입하는 기능을 제공한다. 또한 특정 객체가 필요한 곳에 객체를 제공한다.  

## 스프링의 DI 설정 
스프링은 DI를 지원하는 조립기이다.  
AnnotationConfigApplicationContext를 사용해서 스프링 컨테이너를 생성한다. **스프링 컨테이너는 Assembler와 동일하게 객체를 생성하고 의존 객체를 주입한다.**  
AnnotationConfigApplicationContext는 설정 파일로부터 생성할 객체와 의존 주입 대상을 정한다.  
  
## DI 방식 1 : 생성자 방식
```java
public class MemberListPrinter {

	private MemberDao memberDao;
	private MemberPrinter printer;
  ...
}

// 설정 파일
@Bean
	public MemberListPrinter listPrinter() {
		return new MemberListPrinter(memberDao(), memberPrinter());
	}
```
listPrinter 빈 객체를 구한다. 이 빈 객체는 생성자를 통해서 MemberDao 객체와 MemberPrinter 객체를 주입 받았다.  
  
생성자 주입은 생성자 호출시점에 딱 1번만 호출되는 것이 보장된다.  
**불변, 필수** 의존관계에 사용된다.  
생성자가 딱 1개만 있으면 `@Autowired`를 생략해도 자동 주입된다.  
빈 등록하면서 의존관계 주입도 같이 일어난다.  
  
스프링 컨테이너 life cycle  
1. 스프링 빈 등록  
2. 의존관계 자등으로 주입  
  
## DI 방식 2 : 세터 메서드 방식
일반적인 세터(setter) 메서드의 자바빈 규칙은 다음과 같다.  
- 메서드 이름이 set으로 시작한다.  
- set 뒤에 첫 글자는 대문자로 시작한다.  
- 파라미터가 1개이다.  
- 리턴 타입이 void이다.  
```java
@Bean
public MemberInfoPrinter infoPrinter() {
	MemberInfoPrinter infoPrinter = new MemberInfoPrinter();
	infoPrinter.setMemberDao(memberDao());
	infoPrinter.setMemberPrinter(memberPrinter());
	return infoPrinter;
}
```
위 코드에서 infoPrinter 빈은 세터 메서드를 이용해서 memberDao 빈과 memberPrinter 빈을 주입한다.  
선택, 변경 가능성이 있는 의존관계에 사용한다.  
`@Autowired`의 기본 동작은 주입할 대상이 없으면 오류가 발생한다. 주입할 대상이 없어도 동작하게 하려면 `@Autowired(required=false)`로 지정하면 된다.  
  
생성자 방식은 빈 객체를 생성하는 시점에 모든 의존 객체가 주입된다.  
세터 방식은 세터 메서드 이름을 통해 어떤 의존 객체가 주입되는지 알 수 있다.  
  
세터 방식은 필요한 의존 객체를 전달하지 않아도 빈 객체가 생성되기 때문에 객체를 사용하는 시점에 NPE가 발생할 수 있다.  

## DI 방식 3 : 필드 주입
외부에서 변경이 불가능해서 **테스트하기 힘들다**는 치명적인 단점이 있다.  
**DI 프레임워크가 없으면 아무것도 할 수 없다.**    
사용하지 말자.  
- 프로덕션 코드와 관계 없는 테스트 코드에 사용할 수 있다.  
- 스프링 설정을 목적으로 하는 `@Configuration` 같은 곳에서만 특별한 용도로 사용한다.  
  
순수한 자바 테스트 코드에는 당연히 `@Autowired`가 동작하지 않는다. `@SpringBootTest` 처럼 스프링 컨테이너를 테스트에 통합한 경우에만 가능하다.  

## DI 방식 4 : 일반 메서드 주입 
한 번에 여러 필드를 주입 받을 수 있다.  
일반적으로 잘 사용하지 않는다.  
  
의존관계 주입은 스프링 컨테이너가 관리하는 스프링 빈이어야 동작한다.  
  
## 옵션 처리
주입할 스프링 빈이 없어도 동작해야 할 때가 있다.  
`@Autowired(required-true)`가 기본 옵션. 자동 주입 대상이 없면 오류가 발생한다.  
  
자동 주입 대상을 옵션으로 처리하는 방법은 다음과 같다.  
- `@Autowired(required=false)`: 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출 안됨  
- `org.springframwork.lang.@Nullable`: 자동 주입할 대상이 없으면 null이 입력된다.  
- `Optional<>`: 자동 주입할 대상이 없으면 Optional.empty 가 입력된다.  
  
불변, 누락, final 등의 이유로 생성자 주입이 권장된다.(생성자 주입 방식만 final 키워드를 사용할 수 있다.)  
수정자 주입을 포함한 나머지 주입 방식은 모두 생성자 이후에 호출되므로, 필드에 final 키워드를 사용할 수 없다.  
오직 생성자 주입 방식만 final 키워드를 사용할 수 있다.  
```
JLS 8.3.1.2.
비어있는 final 인스턴스 변수는 선언된 클래스의 모든 생성자의 끝에서 확실히 할당되어야 하며 더욱이 확실히 할당 해제되어서는 안 됩니다. 그렇지 않으면 컴파일 타임 오류가 발생합니다.
```
final 인스턴스 변수는 모든 '생성자의 끝'에서 확실히 할당되어야 하며~  
  
## 롬복과 최신 트렌드
`@RequiredArgsConstructor` 기능을 사용하면 final이 붙은 필드를 모아서 생성자를 자동으로 만들어준다.  
롬복이 자바의 `애노테이션 프로세서` 라는 기능을 이용해서 컴파일 시점에 생성자 코드를 자동으로 생성해준다.  
  
## 조회한 빈이 2개 이상일 떄 문제 - NoUniqueBeanDefinitionException
`@Autowired`는 타입으로 조회한다.  
```java
@Autowired
private DiscountPolicy discountPolicy;
```
ac.getBean(DiscountPolicy.class)와 유사하게 동작한다.  
  
## @Autowired 필드 명, @Qualifier, @Primary
- @Autowired 필드 명 매칭  
- @Qualifier -> @Qualifier 끼리 매칭 -> 빈 이름 매칭  
- @Primary 사용  
  
@Autowired는 타입 매칭을 시도하고, 이때 여러 빈이 있으면 **필드 이름, 파라미터 이름**으로 빈 이름을 추가 매칭한다.  
```java
public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy rateDiscountPolicy) {
  ...
}
```
필드 명 매칭은 먼저 타입 매칭을 시도하고 그 결과에 여러 빈이 있을 때 추가로 동작하는 기능이다.  
1. 타입 매칭  
2. 타입 매칭의 결과가 2개 이상일 때 필드 명, 파라미터 명으로 빈 이름 매칭  
  
**@Qulifier 사용(추가 구분자)**  
주입시 추가적인 방법을 제공하는 것이지 빈 이름을 변경하는 것은 아니다.  
1. @Qualifier 끼리 매칭  
2. 빈 이름 매칭  
3. NoSuchBeanDefinitionException 예외 발생  
  
**@Primary**  
@Primary는 우선순위를 정하는 방법이다. @Autowired 시에 여러 빈이 매칭되면 @Primary가 우선권을 가진다.  
```
메인 데이터베이스의 커넥션을 획득하는 스프링 빈은 @Primary를 적용해서 조회하는 곳에서 @Qualifier 지정 없이 편리하게 조회하고, 서브 데이터베이스 커넥션 빈을 획득할 때는 @Qualifier를 지정해서 명시적으로 획득 하는 방식으로 사용하면 코드를 깔끔하게 유지할 수 있다.
```
스프링은 자동보다는 수동이, 넓은 범위의 선택권보다는 좁은 범위의 선택권이 우선순위가 높다. 따라서 @Qualifier가 우선권이 높다.  
  
## 스프링 컨테이너를 생성하면 스프링 빈 등록하기
스프링 컨테이너는 생성자에 클래스 정보를 받는다. 여기에 클래스 정보를 넘기면 해당 클래스가 스프링 빈으로 자동 등록된다.  
```java
new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class);
```
- new AnnotationConfigApplicationContext()를 통해 스프링 컨테이너를 생성한다.  
- AutoAppConfig.class, DiscountService.class를 파라미터로 넘기면서 해당 클래스를 자동으로 스프링 빈으로 등록한다.  
  
정리하면 스프링 컨테이너를 생성하면서, 해당 컨테이너에 동시에 `AutoAppConfig`, `DiscountService`를 스프링 빈으로 자동 등록한다.  
  
## 수동 빈 등록을 언제 사용하면 좋을까?
애플리케이션은 크게 업무 로직과 기술 지원 로직으로 나눌 수 있다.  
업무 로직 빈: 컨트롤러, 서비스, 리포지토리. 비즈니스 요구사항을 개발할 때 추가, 변경된다(자동 빈 등록 우선)  
기술 지원 빈: 기술적인 문제나 공통 관심사(AOP)를 처리할 때 주로 사용된다. 데이터베이스 연결이나, 공통 로그 처리처럼 업무 로직을 지원하기 위한 하부 기술이나 공통 기술들이다.  
  
기술 지원 로직들은 가급적 수동 빈 등록을 사용해서 명확하게 들어내는 것이 좋다.  
설정정보(Configuration)은 root에 두는 것이 좋다. root를 보면 기술 지원 로직과 관련된 스프링 빈이 나온다.  
  
## 빈 생명주기 콜백 시작
데이터베이스 커넥션 풀이나, 네트워크 소켓처럼 애플리케이션 시작 시점에 필요한 연결을 미리 해두고, 애플리케이션 종료 시점에 연결을 모두 종료하는 작업을 진행하려면, **객체의 초기화와 종료 작업이 필요하다.**  
콜백 함수  
1. 다른 함수의 인자로서 이용되는 함수  
2. 어떤 이벤트에 의해 호출되어지는 함수  
  
스프링 빈은 간단하게 다음과 같은 라이프사이클을 가진다.(생성자 주입은 예외)  
**객체 생성 -> 의존관계 주입**  
  
스프링 빈은 객체를 생성하고, 의존관계 주입이 다 끝난 다음에야 필요한 데이터를 사용할 수 있는 준비가 완료된다. 따라서 초기화 작업은 의존관계 주입이 모두 완료되고 난 다음에 호출해야 한다. 그런데 개발자가 의존관계 주입이 모두 완료된 시점을 어떻게 알 수 있을까?  
  
스프링은 의존관계 주입이 완료되면 스프링 빈에게 콜백 메서드를 통해서 초기화 시점을 알려주는 다양한 기능을 제공한다.  
또한 스프링은 스프링 컨테이너가 종료되기 직전에 소멸 콜백을 준다.  

**스프링 빈의 이벤트 라이프사이클**    
**스프링 컨테이너 생성 -> 스프링 빈 생성 -> 의존관계 주입 -> 초기화 콜백 -> 사용 -> 소멸전 콜백 -> 스프링 종료**  
  
객체의 생성과 초기화를 분리하자.  
생성자는 필수 정보(파라미터)를 받고, 메모리를 할당해서 객체를 생성하는 책임을 가진다.  
반면에 초기화는 이렇게 생성된 값들을 활용해서 외부 커넥션을 연결하는등 무거운 동작을 수행한다.  
따라서 생성자 안에서 무거운 초기화 작업을 함께 하는 것 보다는 객체를 생성하는 부분과 초기화 하는 부분을 명확하게 나누는 것이 유지보수 관점에서 좋다.  
  
무거운 작업, 별도 연결 -> 초기화로 분리  
  
스프링은 크게 3가지 방법으로 빈 생명주기 콜백을 지원한다.  
- 인터페이스(InitializingBean, DisposableBean)  
- 설정 정보에 초기화 메서드, 종료 메서드 지정  
- @PostConstruct, @PreDestroy 애노테이션 지원  
  
## @Configuration 설정 클래스의 @Bean 설정과 싱글톤
```java
@Configuration
public class AppCtx {

	@Bean
	public MemberDao memberDao() { // memberDao 라는 이름으로 스프링에 등록된다
		return new MemberDao();
	}

	@Bean
	public MemberRegisterService memberRegisterService() {
		return new MemberRegisterService(memberDao());
	}

	@Bean
	public ChangePasswordService changePasswordService() {
		ChangePasswordService changePasswordService = new ChangePasswordService();
		changePasswordService.setMemberDao(memberDao());
		return changePasswordService;
	}
```
`memberRegisterService()` 메서드와 `changePasswordService()` 메서드는 둘다 memberDao()메서드를 실행하고 있다. 그리고 memberDao() 메서드는 매번 새로운 MemberDao 객체를 생성해서 리턴한다. 여기서 다음과 같은 궁금증이 생긴다.  
- memberDao()가 새로운 MemberDao 객체를 생성해서 리턴하므로  
- memberRegisterService()에서 생성한 MemberRegisterService 객체와 changePasswordService()에서 생성한 ChangePasswordService 객체는 서로 다른 MemberDao 객체를 사용하는 것 아닌가?  
- 서로 다른 객체를 사용한다면 회원 정보를 저장할 때 사용하는 MemberDao와 수정할 회원 정보를 찾을 때 사용하는 MemberDao는 다른 객체 아닌가?  
  
**스프링 컨테이너는 @Bean이 붙은 메서드에 대해 한 개의 객체만 생성한다.** 이는 다른 설정 메서드에서 memberDao()를 몇 번을 호출하더라도 항상 같은 객체를 리턴한다는 것을 의미한다.  
```java
class SingletonBeanTest {

	private static final ApplicationContext ctx = new AnnotationConfigApplicationContext(AppCtx.class);

	@Test
	void Bean이_붙은_메서드는_한_개의_객체만_생성한다() {
		MemberDao memberDao1 = ctx.getBean("memberDao", MemberDao.class);
		MemberDao memberDao2 = ctx.getBean("memberDao", MemberDao.class);

		assertThat(memberDao1).isSameAs(memberDao2);
	}
}
```
스프링은 설정 클래스를 그대로 사용하지 않는다. 대신 설정 클래스를 상속한 새로운 설정 클래스를 만들어서 사용한다. 스프링이 런타임에 생성한 설정 클래스는 다음과 유사한 방식으로 동작한다(이해를 돕기 위한 가상의 코드일 뿐 실제 스프링 코드는 이보다 훨씬 복잡하다).  
```java
public class AppCtxExt extends AppCtx {

	private Map<String, Object> beans = ...;

	@Override
	public MemberDao memberDao() {
		if (!beans.containsKey("memberDao")) {
			beans.put("memberDao", super.memberDao());
		}
		return (MemberDao)beans.get("memberDao");
	}
}
```
**스프링이 런타임에 생성한 설정 클래스의 memberDao() 메서드는 매번 새로운 객체를 생성하지 않는다. 대신 한 번 생성한 객체를 보관했다가 이후에는 동일한 객체를 리턴한다.**  
따라서 동일한 MemberDao를 반환한다.  
  
## 두 개 이상의 설정 파일 사용하기
스프링 설정 클래스의 필드에 @Autowired 애노테이션을 붙이면 해당 타입의 빈을 찾아서 필드에 할당한다.
```java
ctx = new AnnotationConfigApplicationContext(AppConf1.class, AppConf2.class); // 설정 클래스가 두 개 이상일 때
```
@Autowired 애노테이션은 스프링 빈에 의존하는 다른 빈을 자동으로 주입하고 싶을 때 사용한다.  
  
## @Import 애노테이션 사용
두 개 이상의 설정 파일을 사용하는 또 다른 방법은 @Import 애노테이션을 사용하는 것이다.  
@Import 애노테이션은 함께 사용할 설정 클래스를 지정한다.  
```java
@Configuration
@Import(AppConf2.class)
public class AppConfImport {
	
	@Bean
	public MemberDao memberDao() {
		return new MemberDao();
	}
	
	@Bean
	MemberPrinter memberPrinter() {
		return new MemberPrinter();
	}
}
```
AppConfImport 설정 클래스를 사용하면, @Import 애노테이션으로 지정한 AppConf2 설정 클래스도 함께 사용하기 때문에 스프링 컨테이너를 생성할 때 AppConf2 설정 클래스를 지정할 필요가 없다.  
AppConf2 클래스의 설정도 함께 사용해서 컨테이너를 초기화한다.  
  
@Import 애노테이션은 다음과 같이 배열을 이용해서 두 개 이상의 설정 클래스도 지정할 수 있다.  
```java
@Configuration
@Import({AppConf1.class, AppConfig2.class})
public class AppConfImport {
	...
}
```
@Import를 사용해서 포함한 설정 클래스가 다시 @Import를 사용할 수 있다.  
  
## getBean() 메서드 사용
```java
VersionPrinter versionPrinter = ctx.getBean("versionPrinter", VersionPrinter.class); // 빈의 이름, 빈의 타입
```
존재하지 않는 빈 이름을 사용하면 `NoSuchBeanDefinitionException`이 발생한다.  
```
빈 이름을 잘못 지정하면 NoSuchBeanDefinitionException
빈 타입을 잘못 지정하면 BeanNotOfRequiredTypeException
같은 타입의 빈 객체가 두 개 이상 존재할 때 타입으로 조회하면 NoUniqueBeanDefinitionException 이 발생한다.
```
getBean() 메서드는 BeanFactory 인터페이스에 정의되어 있다. 실제 구현 클래스는 AbstractApplicationContext이다.  
  
객체를 스프링 빈으로 등록할 때와 등록하지 않을 때의 차이는 **스프링 컨테이너가 객체를 관리하는지 여부이다.**  
스프링 컨테이너는 자동 주입, 라이프사이클 관리 등 단순 객체 생성 외에 객체 관리를 위한 다양한 기능을 제공하는데 빈으로 등록한 객체에만 기능을 적용한다.  
  
# Chaptor 04 의존 자동 주입
## @Autowired 애노테이션을 이용한 의존 자동 주입
자동 주입 기능을 사용하면 스프링이 알아서 의존 객체를 찾아서 주입한다.  
필드에 @Autowired 애노테이션을 붙임으로 설정 클래스에서 의존을 주입하지 않아도 된다.  
```
의존을 주입하지 않아도 스프링이 @Autowired가 붙은 필드에 해당 타입의 빈 객체를 찾아서 주입한다
```
빈 객체의 메서드에 @Autowired 애노테이션을 붙이면 스프링은 해당 메서드를 호출한다. **이때 메서드 파라미터 타입에 해당하는 빈 객체를 찾아 인자로 주입한다.**  

**@Autowired 애노테이션을 필드나 세터 메서드에 붙이면 스프링은 타입이 일치하는 빈 객체를 찾아서 주입한다.**  
  
## 일치하는 빈이 없는 경우
```
org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'memberRegisterService': Unsatisfied dependency expressed through field 'memberDao';
NoSuchBeanDefinitionException: No qualifying bean of type 'spring.MemberDao' available 
```
memberRegisterService 빈을 생성하는데 에러가 발생했다는 내용이 나온다. 이어서 'memberDao' 필드에 대한 의존을 충족하지 않는다는 내용이 나오고 적용할 수 없는 MemberDao 타입의 빈이 없다는 내용이 나온다.  
  
반대로 @Autowired 애노테이션을 붙인 주입 대상에 일치하는 빈이 두 개 이상이면?  
```
No qualifying bean of type 'me.euichan.javap.spring.chapter4.spring.MemberPrinter' available: expected single matching bean but found 2: memberPrinter1,memberPrinter2
```
MemberPrinter 타입의 빈을 한정할 수 없는데 해당 타입 빈이 한 개가 아니라 두 개의 빈을 발견했다는 사실을 알려준다.  
  
## @Qaulifier 애노테이션을 이요한 의존 객체 선택
자동 주입 가능한 빈이 두 개 이상아면 자동 주입할 빈을 지정할 수 있는 방법이 필요하다. 이때 @Qualifier 애노테이션을 사용한다.  
@Qualifier 애노테이션을 사용하면 자동 주입 대상 빈을 한정할 수 있다.  
  
@Qualifier 애노테이션은 두 위치에서 사용 가능하다. 첫 번째는 @Bean 애노테이션을 붙인 빈 설정 메서드이다.  
```java
	@Bean
	@Qualifier("printer")
	public MemberPrinter memberPrinter1() {
		return new MemberPrinter();
	}

	@Bean
	public MemberPrinter memberPrinter2() {
		return new MemberPrinter();
	}
```
이 설정은 해당 빈의 한정 값으로 "printer"를 지정한다. 이렇게 지정한 한정 값은 @Autowired 애노테이션에서 자동 주입할 빈을 한정할 때 사용한다.  
```java
	@Autowired
	@Qualifier("printer")
	public void setPrinter(final MemberPrinter printer) {
		this.printer = printer;
	}
```
setPrinter 메서드에 @Autowired 애노테이션을 붙였으므로 MemberPrinter타입의 빈을 자동 주입한다. 이때 @Qualifier 애노테이션 값이 "printer"이므로 한정 값이 "printer"인 빈을 의존 주입 후보로 사용한다.  
  
빈 설정에 @Qualifier 애노테이션이 없으면 빈의 이름을 한정자로 지정한다.  
**@Autowired는 타입 매칭을 시도하고, 이때 여러 빈이 있으면 필드 이름, 파라미터 이름으로 빈 이름을 추가 매칭한다.(필드나 파라미터 이름을 한정자로 사용한다.)**  
```java
public class MemberInfoPrinter2 {

	@Autowired
	private MemberPrinter printer; // 한정자로 필드 이름인 printer를 사용한다.
}
```
## @Autowired 애노테이션의 필수 여부
```java
public class MemberPrinter {

	private DateTimeFormatter dateTimeFormatter;

	public void print(Member member) {
		if (dateTimeFormatter == null) {
			System.out.printf(
				"회원 정보: 아이디=%d, 이메일=%s, 이름=%s, 등록일=%tF\n",
				member.getId(), member.getEmail(), member.getName(), member.getRegisterDateTime());
		} else {
			System.out.printf(
				"회원 정보: 아이디=%d, 이메일=%s, 이름=%s, 등록일=%s\n",
				member.getId(), member.getEmail(), member.getName(),
				dateTimeFormatter.format(member.getRegisterDateTime()));
		}
	}

	@Autowired
	public void setDateTimeFormatter(final DateTimeFormatter dateTimeFormatter) {
		this.dateTimeFormatter = dateTimeFormatter;
	}
}
```
print() 메서드는 dateTimeFormatter가 null인 경우에도 알맞게 동작한다. 즉 반드시 setDateTimeFormatter()를 통해서 의존 객체를 주입할 필요는 없다. 그런데 **@Autowired 애노테이션은 기본적으로 @Autowired 애노테이션을 붙인 타입에 해당하는 빈이 존재하지 않으면 익셉션을 발생한다.**  
이렇게 자동 주입할 대상이 필수가 아닌 경우에는 @Autowired 애노테이션의 required 속성을 false로 지정하면 된다.  
  
@Autowired 애노테이션의 required 속성을 false로 지정하면 매칭되는 빈이 없어도 익셉션이 발생하지 않으며 자동 주입을 수행하지 않는다. 스프링 5 버전부터는 required 속성을 false로 하는 대신에 자바 8의 Optional을 사용해도 된다.  
```java
	// @Autowired(required = false)
	// public void setDateTimeFormatter(final DateTimeFormatter dateTimeFormatter) {
	// 	this.dateTimeFormatter = dateTimeFormatter;
	// }

	@Autowired
	public void setDateTimeFormatter(Optional<DateTimeFormatter> formatterOpt) {
		if (formatterOpt.isPresent()) {
			this.dateTimeFormatter = formatterOpt.get();
		} else {
			this.dateTimeFormatter = null;
		}
	}
```
또 다른 방법은 `@Nullable` 애노테이션을 사용하는 것이다.  
```java
@Autowired
	public void setDateTimeFormatter(@Nullable final DateTimeFormatter dateTimeFormatter) {
		this.dateTimeFormatter = dateTimeFormatter;
	}
```
세터 메서드에 @Nullable 애노테이션을 의존 주입 대상 파라미터에 붙이면, 스프링 컨테이너는 세터 메서드를 호출할 때 자동 주입할 빈이 존재하면 해당 빈을 인자로 전달하고, 존재하지 않으면 인자로 null을 전달한다.  
```
(required=false) 의 경우는 대상 빈이 존재하지 않으면 세터 메서드를 호출하지 않는다.
@Nullable의 경우 자동 주입할 빈이 존재하지 않아도 메서드가 호출된다.
```
앞서 설명한 세 가지 방식은 필드에도 그대로 적용된다.  
  
@Autowired 애노테이션의 required 속성이 false이면 일치하는 빈이 없으면 값 할당을 하지 않는다.  
@Nullable 애노테이션은 일치하는 빈이 없을 때 null 값을 할당한다.  
Optional 타입은 매칭되는 빈이 없으면 값이 없는 Optional을 할당한다.  
  
## 6. 자동 주입과 명시적 의존 주입 간의 관계
설정 클래스에서 의존을 주입했는데 자동 주입 대상이면 어떻게 될까?  

설정 클래스에서 세터 메서드를 통해 의존을 주입해도 해당 세터 메서드에 @Autowired 애노테이션이 붙어 있으면 `자동 주입을 통해 일치하는 빈`을 주입한다. 따라서 @Autowired 애노테이션을 사용했다면 설정 클래스에서 객체를 주입하기보다는 스프링이 제공하는 자동 주입 기능을 사용하는 편이 낫다.  
  
# Chapter 05 컴포넌트 스캔
자동 주입과 함꼐 사용하는 추가 기능이 컴포넌트 스캔이다. 컴포넌트 스캔은 **스프링이 직접 클래스를 검색해서 빈으로 등록해주는 기능이다.** 설정 클래스에 빈으로 등록하지 않아도 원하는 클래스를 빈으로 등록할 수 있으므로 컴포넌트 스캔 기능을 사용하면 설정 코드가 크게 줄어든다.  
  
## 1. @Component 애노테이션으로 스캔 대상 지정
스프링이 검색해서 빈으로 등록할 수 있으려면 클래스에 @Component 애노테이션을 붙여야 한다. @Component 애노테이션은 해당 클래스를 스캔 대상으로 표시한다.  
```java
@Component("infoPrinter")
public class MemberInfoPrinter {
```
@Component 애노테이션에 값을 주었는지에 따라 빈으로 등록할 때 사용할 이름이 결정된다. @Component 애노테이션에 값을 주지 않으면 클래스 이름의 첫 글자를 소문자로 바꾼 이름을 빈 이름으로 사용한다. 예를 들어 클래스 이름이 MemberDao이면 빈 이름으로 "memberDao"를 사용하고 클래스 이름이 "MemberRegisterService"이면 빈 이름으로 "memberRegisterSerivce"를 사용한다.  
  
@Component 애노테이션에 값을 주면 그 값을 빈 이름으로 사용한다. @Component("infoPrinter") 의 경우 클래스 이름은 MemberInfoPrinter이지만 빈 이름으로 "infoPrinter"를 사용한다.  
  
## 2. @ComponentScan 애노테이션으로 스캔 설정
@Component 애노테이션을 붙인 클래스를 스캔해서 스프링 빈으로 등록하려면 설정 클래스에 @ComponentScan 애노테이션을 적용해야 한다.  
```java
@Configuration
@ComponentScan(basePackages = {"spring"})
public class AppCtx {

	@Bean
	@Qualifier("printer")
	public MemberPrinter memberPrinter1() {
		return new MemberPrinter();
	}
	
	@Bean
	@Qualifier("summaryPrinter")
	public MemberSummaryPrinter memberPrinter2() {
		return new MemberSummaryPrinter();
	}

	@Bean
	public VersionPrinter versionPrinter() {
		VersionPrinter versionPrinter = new VersionPrinter();
		versionPrinter.setMajorVersion(5);
		versionPrinter.setMinorVersion(0);
		return versionPrinter;
	}
}
```
스프링 컨테이너가 @Component 애노테이션을 붙인 클래스를 검색해서 빈으로 등록해주기 때문에 설정 코드가 줄어들었다.  
basePackages 속성값은 {"spring"}이다. 이 속성은 스캔 대상 패키지 목록을 지정한다. 이는 spring 패키지와 그 하위 패키지에 속한 클래스를 스캔 대상으로 설정한다. **스캔 대상에 해당하는 클래스 중에서 @Component 애노테이션이 붙은 클래스의 객체를 생성해서 빈으로 등록한다.**  
  
## 3. 예제 실행
클래스 이름의 첫 글자를 소문자로 바꾼 이름을 빈 이름으로 사용하기 때문에 예제를 변경한다.  
두 타입의 빈을 구하는 코드를 다음과 같이 타입만으로 구하도록 변경한다.  
```java
MemberRegisterService regSvc = ctx.getBean(MemberRegisterService.class);
ChangePasswordService changePwdSvc = ctx.getBean(ChangePasswordService.class);
```
  
## 4. 스캔 대상에서 제외하거나 포함하기
excludeFilters 속성을 사용하면 스캔할 때 특정 대상을 자동 등록 대상에서 제외할 수 있다.  
```java
@Configuration
@ComponentScan(basePackages = {"spring"},
		excludeFilters = @ComponentScan.Filter(type = FilterType.REGEX, pattern = "spring\\..*Dao"))
public class AppCtxWithExclude {

	@Bean
	public MemberDao memberDao() {
		return new MemberDao();
	}
}
```
이 코드는 @Filter 애노테이션의 type 속성값으로 FilterType.REGEX를 주었다. 이는 정규표현식을 사용해서 제외 대상을 지정한다는 것을 의미한다. pattern 속성은 FilterType에 적용할 값을 설정한다. 위 설정에서는 "spring."으로 시작하고 Dao 로 끝나는 정규표현식을 지정했으므로 spring.MemberDao 클래스를 컴포넌트 스캔 대상에서 제외한다.  
  
정규표현식 대신 AspectJ 패턴을 사용해서 spring 패키지에서 이름이 Dao로 끝나는 타입을 컴포넌트 스캔 대상에서 제외할 수 있다.  
```java
@Configuration
@ComponentScan(basePackages = {"spring"},
		excludeFilters = @ComponentScan.Filter(type = FilterType.ASPECTJ, pattern = "spring.*Dao"))
public class AppCtxWithExclude {

	@Bean
	public MemberDao memberDao() {
		return new MemberDao();
	}
}
```
특정 애노테이션을 붙인 타입을 컴포넌트 대상에서 제외할 수도 있다. 예를 들어 @NoProduct나 @ManualBean 애노테이션을 붙인 클래스는 컴포넌트 스캔 대상에서 제외하고 싶다고 하자.  
```java
@Configuration
@ComponentScan(basePackages = {"spring"},
		excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = {NoProduct.class, ManualBean.class}))
public class AppCtxWithExclude {

	@Bean
	public MemberDao memberDao() {
		return new MemberDao();
	}
}
```
이 두 애노테이션을 붙인 클래스를 컴포넌트 스캔 대상에서 제외하려면 다음과 같이 excludeFilters 속성을 설정한다.  
```java
@Configuration
@ComponentScan(basePackages = {"spring"},
		excludeFilters = @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, classes = MemberDao.class))
```
특정 타입이나 그 하위 타입을 컴포넌트 스캔 대상에서 제외하려면 ASSIGNABLE_TYPE 을 FilterType으로 사용한다.  
  
설정할 필터가 두 개 이상이면 excludeFilters 속성에 배열을 사용해서 @Filter 목록을 전달하면 된다.  
  
@Controller 애노테이션이나 @Repository 애노테이션 등은 컴포넌트 스캔 대상이 될뿐만 아니라 스프링 프레임워크에서 특별한 기능과 연관되어 있다. 예를 들어 @Controller 애노테이션은 웹 MVC와 관련 있고 @Repository 애노테이션은 DB 연동과 관련 있다.  
  
## 5. 컴포넌트 스캔에 따른 충돌 처리
컴포넌트 스캔 기능을 사용해서 자동으로 빈을 등록할 때는 `충돌`에 주의해야 한다. 크게 `빈 이름 충돌`과 `수동 등록에 따른 충돌`이 발생할 수 있다.  
  
```java
@Component
public class MemberDao {
	...
}
```
자동 등록된 빈의 이름은 "memberDao"이다. 그런데 다음과 같이 설정 클래스에 직접 memberDao 클래스를 "memberDao"라는 이름의 빈으로 등록하면 어떻게 될까?  
```java
@Bean
public MemberDao mmeberDao() {
	...
}
```
스캔할 때 사용하는 빈 이름과 수동 등록한 빈 이름이 같을 경우 **수동 등록한 빈이 우선한다.**  
다음과 같이 다른 이름을 사용하면 어떻게 될까?
```java
@Bean
public MemberDao memberDao2() {
	...
}
```
이 경우 스캔을 통해 등록한 "memberDao"빈과 수동 등록한 "memberDao2" 빈이 모두 존재한다.  
MemberDao 타입의 빈이 두 개가 생성되므로 자동 주입하는 코드는 @Qualifier 애노테이션을 사용해서 알맞은 빈을 선택해야 한다.  
  
# Chapter 06 빈 라이프사이클과 범위
## 1. 컨테이너 초기화와 종료
스프링 컨테이너는 초기화와 종료라는 라이프사이클을 갖는다.  
```java
// 1. 컨테이너 초기화
AnnotationConfigApplicationContext ctx = 
				new AnnotationConfigApplicationContext(AppContext.class);
// 2. 컨테이너에서 빈 객체를 구해서 사용
Greeter g = ctx.getBean("greeter", Greeter.class);
String msg = g.greet("스프링");

// 3. 컨테이너 종료
ctx.close();
```
위 코드를 보면 AnnotationConfigApplicationContext의 생성자를 이용해서 컨텍스트 객체를 생성하는데 이 시점에 스프링 컨테이너를 초기화한다.   
**스프링 컨테이너는 설정 클래스에서 정보를 읽어와 알맞은 빈 객체를 생성하고 각 빈을 연결(의존 주입)하는 작업을 수행한다.**  
  
컨테이너 초기화가 완료되면 컨테이너를 사용할 수 있다.  
  
컨테이너 사용이 끝나면 컨테이너를 종료한다. close() 메서드는 AbstractApplicationContext 클래스에 정의되어 있다.  
자바 설정을 사용하는 AnnotationConfigApplicationContext 클래스나 XML 설정을 사용하는 GenericXmlApplicationContext 클래스 모두 AbstractApplicationContext 클래스를 상속받고 있다.  
  
컨테이너를 초기화하고 종료할 때는 다음의 작업도 함께 수행한다.  
- 컨테이너 초기화 -> 빈 객체의 생성, 의존 주입, 초기화  
- 컨테이너 종료 -> 빈 객체의 소멸  
  
스프링 컨테이너의 라이프사이클에 따라 빈 객체도 자연스럽게 생성과 소멸이라는 라이프사이클을 갖는다.  
  
## 2. 스프링 빈 객체의 라이프사이클
**스프링 컨테이너는 빈 객체의 라이프사이클을 관리한다.** 컨테이너가 관리하는 빈 객체의 라이프사이클은 다음과 같다.  
  
객체 생성 -> 의존 설정 -> 초기화 -> 소멸  

스프링 컨테이너를 초기화할 때 스프링 컨테이너는 가장 먼저 빈 객체를 생성하고 의존을 설정한다. 의존 자동 주입을 통한 의존 설정이 이 시점에 수행된다.  
모든 의존 설정이 완료되면 빈 객체의 초기화를 수행한다. 빈 객체를 초기화하기 위해 스프링은 빈 객체의 지정된 메서드를 호출한다.  
  
스프링 컨테이너를 종료하면 스프링 컨테이너는 빈 객체의 소멸을 처리한다. 이때에도 지정한 메서드를 호출한다.  
  
### 2.1 빈 객체의 초기화와 소멸 : 스프링 인터페이스
스프링 컨테이너는 빈 객체를 초기화하고 소멸하기 위해 빈 객체의 지정한 메서드를 호출한다. 스프링은 다음의 두 인터페이스에 이 메서드를 정의하고 있다.  
- org.springframework.beans.factory.InitializingBean  
- org.springframework.beans.factory.DisposableBean  

초기화와 소멸 과정이 필요한 예가 데이터베이스 커넥션 풀이다. 커넥션 풀을 위한 빈 객체는 초기화 과정에서 데이터베이스 연결을 생성한다.   
컨테이너를 사용하는 동안 연결을 유지하고 빈 객체를 소멸할 때 사용중인 데이터베이스 연결을 끊어야 한다.  
```java
public class Client implements InitializingBean, DisposableBean {

	private String host;

	public void setHost(final String host) {
		this.host = host;
	}

	@Override
	public void afterPropertiesSet() throws Exception {
		System.out.println("Client.afterPropertiesSet() 실행");
	}

	public void send() {
		System.out.println("Client.send() to " + host);
	}

	@Override
	public void destroy() throws Exception {
		System.out.println("Client.destroy() 실행");
	}
}
```
콘솔에 출력된 메시지의 순서를 보면 먼저 afterPropertiesSet() 메서드를 실행했다. 즉 스프링 컨테이너는 빈 객체 생성을 마무리한 뒤에 초기화 메서드를 실행한다. 가장 마지막에 destroy() 메서드를 실행했다. 이 메서드는 스프링 컨테이너를 종료하면 호출된다는 것을 알 수 있다. ctx.close() 코드가 없다면 컨테이너의 종료 과정을 수행하지 않기 때문에 빈 객체의 소멸 과정도 실행되지 않는다.  
  
### 2.2 빈 객체 초기화와 소멸 : 커스텀 메서드
직접 구현한 클래스가 아닌 외부에서 제공받은 클래스를 스프링 빈 객체로 설정하고 싶을 때도 있다. 이 경우 소스 코드를 받지 않았다면 두 인터페이스를 구현하도록 수정할 수 없다.  
이렇게 InitializingBean 인터페이스와 DisposableBean 인터페이스를 구현할 수 없거나 이 두 인터페이스를 사용하고 싶지 않은 경우에는 스프링 설정에서 직접 메서드를 지정할 수 있다.  
  
방법은 @Bean 태그에서 initMethod 속성과 destroyMethod 속성을 사용해서 초기화 메서드와 소멸 메서드의 이름을 지정하면 된다.  
```java
public class Client2 {

	private String host;

	public void setHost(final String host) {
		this.host = host;
	}

	public void connect() {
		System.out.println("Client2.connect() 실행");
	}

	public void send() {
		System.out.println("Client2.send() to " + host);
	}

	public void close() {
		System.out.println("Client2.close() 실행");
	}
}
```
Client2 클래스를 빈으로 사용하려면 초기화 과정에서 connect() 메서드를 실행하고 소멸 과정에서 close() 메서드를 실행해야 한다면 다음과 같이 @Bean 애노테이션의 initMethod 속성과 destroyMethod 속성에 초기화와 소멸 과정에서 사용할 메서드 이름인 connect와 close를 지정해주기만 하면 된다.  
```java
	@Bean(initMethod = "connect", destroyMethod = "close")
	public Client2 client2() {
		Client2 client2 = new Client2();
		client2.setHost("host");
		return client2;
	}
```
초기화 관련 메서드를 빈 설정 코드에서 직접 실행할 때는 이렇게 초기화 메서드가 두 번 호출되지 않도록 주의해야 한다.  
initMethod, destroyMethod 속성에 지정한 메서드는 파라미터가 없어야 한다. 이 두 속성에 지정한 메서드에 파라미터가 존재할 경우 스프링 컨테이너는 익셉션을 발생시킨다.  
## 3. 빈 객체의 생성과 관리 범위
별도 설정을 하지 않으면 빈은 싱글톤 범위를 갖는다. 사용 빈도가 낮긴 하지만 프로토타입 범위의 빈을 설정할 수도 있다. 빈의 범위를 프로토타입으로 지정하면 **빈 객체를 구할 때마다 매번 새로운 객체를 생성한다.**예를 들어 "client" 이름을 갖는 빈을 프로토타입 범위의 빈으로 설정했다면 다음 코드의 getBean() 메서드는 매번 새로운 객체를 생성해서 리턴하기 때문에 client1과 client2는 서로 다른 객체가 된다.  
```java
Client client1 = ctx.getBean("client", Client.class);
Client client2 = ctx.getBean("client", Client.class);
// client1 != client2 -> true
```
특정 빈을 프로토타입 범위로 지정하려면 다음과 같이 값으로 "prototype"을 갖는 @Scope 애노테이션을 @Bean 애노테이션과 함께 사용하면 된다.  
```java
@Configuration
public class AppCtxWithPrototype {
	
	@Bean
	@Scope("prototype")
	public Client client() {
		Client client = new Client();
		client.setHost("host");
		return client;
	}
}
```
프로토타입 범위를 갖는 빈은 **완전한 라이프사이클을 따르지 않는다는 점에 주의**해야 한다. 스프링 컨테이너는 프로토타입의 빈 객체를 생성하고 프로퍼티를 설정하고 초기화 작업까지는 수행하지만, 컨테이너를 종료한다고 해서 프로토타입 **빈 객체의 소멸 메서드를 실행하지는 않는다.** 따라서 프로토타입 범위의 빈을 사용할 때는 빈 객체의 소멸 처리를 코드에서 직접 해야 한다.  
  
# Chapter 07 AOP 프로그래밍 
```
		<dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.0.2.RELEASE</version>
        </dependency>
   	<dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.8.13</version>
    </dependency>
```
aspectjweaver 의존성을 추가한다. 이 모듈은 스프링이 AOP를 구현할 때 사용하는 모듈이다.  
스프링 프레임워크의 AOP 기능은 `spring-aop` 모듈이 제공하는데 spring-context 모듈을 의존 대상에 추가하면 spring-aop 모듈도 함께 의존 대상에 포함된다.  
따라서 spring-aop 모듈에 대한 의존을 따로 추가하지 않아도 된다. aspectjweaver 모듈은 AOP를 설정하는데 필요한 애노테이션을 제공하므로 이 의존을 추가해야 한다.  
  
## 2. 프록시와 AOP
계승 구현 클래스의 실행 시간을 출력하려면 어떻게 해야 할까? 쉬운 방법은 메서드의 시작과 끝에서 시간을 구하고 이 두 시간의 차이를 출력하는 것이다.  
```java
public class ImpeCalculator implements Calculator {

	@Override
	public long factorial(final long num) {
		long start = System.currentTimeMillis();
		long result = 1;
		for (long i = 1; i <= num; ++i) {
			result *= i;
		}
		long end = System.currentTimeMillis();
		System.out.printf("ImpeCalculator.factorial(%d) 실행 시간 = %d\n", num, (end - start));
		return result;
	}
}
```
RecCalculator 클래스는 약간 복잡해지는데, 다음가 같다. 실행 시간을 출력하는 메시지가 3번 출력된다.  
```java
public class RecCalculator implements Calculator {

	@Override
	public long factorial(final long num) {
		long start = System.currentTimeMillis();
		try {
			if (num == 0) {
				return 1;
			} else {
				return num * factorial(num - 1);
			}
		} finally {
			long end = System.currentTimeMillis();
			System.out.printf("RecCalculator.factorial(%d) 실행 시간 = %d\n", num, (end - start));
		}
	}
}
```
RecCalculator를 고려하면 실행 시간을 출력하기 위해 기존 코드를 변경하는 것보다는 차라리 다음 코드처럼 메서드 실행 전후에 값을 구하는 게 나을지도 모른다.  
```java
ImpeCalculator implCal = new ImpeCalculator();
long start1 = System.currentTimeMillis();
long fiveFact1 = implCal.factorial(5);
long end1 = System.currentTimeMillis();
System.out.printf("ImpeCalculator.factorial(5) 실행 시간 = %d\n", (end1 - start1));
		
RecCalculator recCal = new RecCalculator();
long start2 = System.currentTimeMillis();
long fiveFact2 = recCal.factorial(5);
long end2 = System.currentTimeMillis();
System.out.printf("RecCalculator.factorial(5) 실행 시간 = %d\n", (end2 - start2));
```
그런데 위 방식도 문제가 있다. 실행 시간을 밀리초가 아닌 나노초 단위로 구해야 한다면? 중복된 코드를 모두변경해야 한다.  
기존 코드를 수정하지 않고 코드 중복도 피할 수 있는 방법은 없을까? 이때 출현하는 것이 바로 **프록시 객체**이다.  
  
```java
public class ExeTimeCalculator implements Calculator {
	
	private final Calculator delegate;

	public ExeTimeCalculator(final Calculator delegate) {
		this.delegate = delegate;
	}

	@Override
	public long factorial(final long num) {
		long start = System.nanoTime();
		long result = delegate.factorial(num);
		long end = System.nanoTime();
		System.out.printf("%s.factorial(%d) 실행 시간 = %d\n", delegate.getClass().getSimpleName(), num, (end - start));
		return result;
	}
}
```
ExeTimeCalculator 클래스를 사용하면 다음과 같은 방법으로 ImpeCalculator의 실행 시간을 측정할 수 있다.  
```java
ImpeCalculator impeCal = new ImpeCalculator();
ExeTimeCalculator calculator = new ExeTimeCalculator(impeCal);
final long result = calculator.factorial(5);
System.out.println(result);
```
실제로 실행 시간을 출력하는지 확인하자.  
```java
public class MainProxy {

	public static void main(String[] args) {
		ExeTimeCalculator ttCal1 = new ExeTimeCalculator(new ImpeCalculator()); // ImpeCalculator 객체의 factorial(20) 실행 시간을 출력 
		System.out.println(ttCal1.factorial(20));

		ExeTimeCalculator ttCal2 = new ExeTimeCalculator(new RecCalculator()); // RecCalculator 객체의 factorial(20) 실행 시간을 출력 
		System.out.println(ttCal2.factorial(20));
	}
}
```
다음을 알 수 있다.  
  
- 기존 코드를 변경하지 않고 실행 시간을 출력할 수 있다.  
- 실행 시간을 구하는 코드의 중복을 제거했다. 나노초 대신에 밀리초를 사용해서 실행 시간을 구하고 싶다면 ExeTimeCalculator 클래스만 변경하면 된다.  
  
이것이 가능한 이유는 ExeTimeCalculator 클래스를 다음과 같이 구현했기 때문이다.  
- **factorial() 기능 자체를 직접 구현하기보다는 다른 객체에 factorial()의 실행을 위임한다.**  
- 계산 기능 외에 다른 부가적인 기능을 실행한다. 여기서 부가적인 기능은 실행 시간 측정이다.  
  
이렇게 **핵심 기능의 실행은 다른 객체에 위임하고 부가적인 기능을 제공하는 객체를 프록시(proxy)라고 부른다.** 실제 핵심 기능을 실행하는 객체는 대상 객체라고 부른다.  
ExeTimeCalculator 가 프록시이고 ImpeCalculator 객체가 프록시의 대상 객체가 된다.  
  
프록시의 특징은 핵심 기능은 구현하지 않는다는 점이다. 핵심 기능을 구현하지 않는 대신 여러 객체에 공통으로 적용할 수 있는 기능을 구현한다.  
  
정리하면 ImpeCalculator와 RecCalculator는 팩토리얼을 구한다는 **핵심 기능 구현**에 집중하고 프록시인 ExeTimeCalculator는 실행 시간 측정이라는 **공통 기능 구현**에 집중한다.  
이렇게 공통 기능 구현과 핵심 기능 구현을 분리하는 것이 AOP의 핵심이다.  
  
### 2.1 AOP
AOP는 Aspect Oriented Programming의 약자로, 여러 객체에 공통으로 적용할 수 있는 기능을 분리해서 재사용성을 높여주는 프로그래밍 기법이다.  
**AOP는 핵심 기능과 공통 기능의 구현을 분리함**으로써 핵심 기능을 구현한 코드의 수정 없이 공통 기능을 적용할 수 있게 만들어 준다.  
  
AOP의 기본 개념은 핵심 기능에 공통 기능을 삽입하는 것이다. 즉 핵심 기능의 코드를 수정하지 않으면서 공통 기능의 구현을 추가하는 것이 AOP다.  
핵심 기능에 공통 기능을 삽입하는 방법에는 다음 세 가지가 있다.  
- 컴파일 시점에 코드에 공통 기능을 삽입하는 방법  
- 클래스 로딩 시점에 바이트코드에 공통 기능을 삽입하는 방법  
- 런타임에 프록시 객체를 생성해서 공통 기능을 삽입하는 방법  
  
첫 번째 방법은 AOP 개발 도구가 소스 코드를 컴파일 하기 전에 공통 구현 코드를 소스에 삽입하는 방식으로 동작한다.  
두 번째 방법은 클래스를 로딩할 때 바이트 코드에 공통 기능을 클래스에 삽입하는 방식으로 동작한다.  
이 두 가지는 스프링 AOP에서는 지원하지 않으며 AspectJ와 같이 AOP 전용 도구를 사용해서 적용할 수 있다.  
  
스프링이 제공하는 AOP 방식은 프록시를 이용한 세 번째 방식이다. 두 번째 방식을 일부 지원하지만 널리 사용되는 방법은 프록시를 이용한 방식이다.  
프록시 방식은 앞서 살펴본 것처럼 중간에 프록시 객체를 생성한다. 실제 객체의 기능을 실행하기 전. 후에 공통 기능을 호출한다.  

**스프링 AOP는 프록시 객체를 자동으로 만들어준다.** 따라서 ExeTimeCalculator 클래스처럼 상위 타입의 인터페이스를 상속받은 프록시 클래스를 직접 구현할 필요가 없다.  
단지 공통 기능을 구현한 클래스만 알맞게 구현하면 된다.  
  
AOP에서 공통 기능을 Aspect라고 하는데 Aspect 외에 알아두어야 할 용어는 다음과 같다.  
- Advice: **언제 공통 관심 기능을 핵심 로직에 적용할 지를 정의하고 있다.** 예를 들어 '메서드를 호출하기 전'(언제)에 '트랜잭션 시작'(공통 기능) 기능을 적용한다는 것을 정의한다.  
- JoinPoint: Advice를 적용 가능한 지점을 의미한다. 메서드 호출, 필드 값 변경 등이 JoinPoint에 해당한다. 스프링은 프록시를 이용해서 AOP를 구현하기 때문에 메서드 호출에 대한 JoinPoint만 지원한다.  
- PointCut: JointPoint의 부분 집합으로서 **실제 Advice가 적용되는 JoinPoint를 나타낸다.** 스프링에서는 정규 표현식이나 AspectJ의 문법을 이용하여 PointCut을 정의할 수 있다.  
- Weaving: Advice를 핵심 로직 코드에 적용하는 것을 Weaving이라고 한다.  
- Aspect: 여러 객체에 공통으로 적용되는 기능을 Aspect라고 한다. 트랜잭션이나 보안 등이 Aspect의 좋은 예이다.  
  
### 2.2 Advice의 종류
Advice(언제 공통 기능을 핵심 로직에 적용할지. 예를 들어 '메서드 호출하기 전')  
스프링은 프록시를 이용해서 메서드 호출 시점에 Aspect를 적용하기 때문에 구현 가능한 Advice의 종류는 다음과 같다.(Before Advice, After Returning Advice, After Throwing Advice, After Advice, Around Advice)  
이 중 가장 널리 사용되는 것은 Around Advice이다. 이유는 대상 객체의 메서드를 실행하기 전/후, 익셉션 발생 시점 등 다양한 시점에 원하는 기능을 삽입할 수 있기 때문이다.  
`캐시 기능, 성능 모니터링 기능`과 같은 Aspect를 구현할 때에는 Around Advice를 주로 이용한다. 이 책에서도 Around Advice의 구현 방법에 대해서만 살펴볼 것이다.  
```
Around Advice: 대상 객체의 메서드 실행 전, 후 또는 익셉션 발생 시점에 공통 기능을 실행하는데 사용된다.
```
## 3. 스프링 AOP 구현 
스프링 AOP를 이용해서 공통 기능을 구현하고 적용하는 방법은 단순하다. 다음과 같은 절차만 따르면 된다.  
- Aspect로 사용될 클래스에 @Aspect 애노테이션을 붙인다.  
- @Pointcut 애노테이션으로 공통 기능을 적용할 Pointcut을 적용한다.  
- 공통 기능을 구현한 메서드에 @Around 애노테이션을 적용한다.  
  
## 3.1 @Aspect, @Pointcut, @Around를 이용한 AOP 구현
개발자는 공통 기능을 제공하는 Aspect 구현 클래스를 만들고 자바 설정을 이용해서 Aspect를 어디에 적용할지 설정하면 된다. Aspect는 @Aspect 애노테이션을 이용해서 구현한다.  
프록시는 스프링 프레임워크가 알아서 만들어준다.  
  
아래 코드는 메서드 실행 전/후(Around Advice)에 사용할 공통 기능(Aspect)이다.  
```java
@Aspect
public class ExeTimeAspect {

	@Pointcut("execution(public * me.euichan.chap07..*(..))")
	private void publicTarget() {
	}

	@Around("publicTarget()") // 대상 메서드 실행 전, 후 또는 익셉션 발생 시점에 공통 기능 실행
	public Object measure(ProceedingJoinPoint joinPoint) throws Throwable {
		long start = System.nanoTime();

		try {
			final Object result = joinPoint.proceed();
			return result;
		} finally {
			long finish = System.nanoTime();
			final Signature sig = joinPoint.getSignature();
			System.out.printf("%s.%s(%s) 실행 시간 : %d ns\n",
				joinPoint.getTarget().getClass().getSimpleName(),
				sig.getName(), Arrays.toString(joinPoint.getArgs()), (finish - start));
		}
	}
}
```
@Aspect 애노테이션을 적용한 클래스는 `Advice`와 `Pointcut`을 함께 제공한다.  
@Pointcut은 공통 기능을 적용할 대상을 설정한다. 현재 설정은 chap07 패키지와 그 하위 패키지에 위치한 타입의 public 메서드를 Pointcut으로 설정한다는 정도만 이해하고 넘어가자.  
@Around 애노테이션은 Around Advice를 설정한다. @Around 애노테이션의 값이 "publicTarget()"인데 이는 publicTarget() 메서드에 정의한 Pointcut에 공통 기능을 적용한다는 것을 의미한다.  
publicTarget() 메서드는 chap07 패키지와 그 하위 패키지에 위치한 public 메서드를 Pointcut(실제 Advice가 적용되는 JoinPoint)으로 설정하고 있으므로, chap07 패키지나 그 하위 패키지에 속한 빈 객체의 public 메서드에 @Around가 붙은 measure() 메서드를 적용한다.  
  
ProceedingJoinPoint 타입 파라미터는 프록시 대상 객체(실제 핵심 기능을 실행하는 객체)의 메서드를 호출할 때 사용한다. proceed 메서드를 사용해서 실제 대상 객체의 메서드를 호출한다.  
이 메서드를 호출하면 대상 객체의 메서드가 실행되므로 이 코드 이전과 이후에 공통 기능을 위한 코드를 위치시키면 된다.  
```
자바에서 메서드 이름과 파라미터를 합쳐서 메서드 시그니처라고 한다.
메서드 이름이 다르거나 파라미터 타입, 개수가 다르면 시그니처가 다르다고 표현한다.
자바에서 메서드의 리턴 타입이나 익셉션 타입은 시그니처에 포함되지 않는다.
```
설정 클래스는 다음과 같다.  
```java
@Configuration
@EnableAspectJAutoProxy
public class AppCtx {

	@Bean
	public ExeTimeAspect exeTimeAspect() {
		return new ExeTimeAspect();
	}

	@Bean
	public Calculator calculator() {
		return new RecCalculator();
	}
}
```
@Aspect 애노테이션을 붙인 클래스를 공통 기능으로 적용하려면 @EnableAspectJAutoProxy 애노테이션을 설정 클래스에 붙여야 한다.  
이 애노테이션을 추가하면 스프링은 @Aspect 애노테이션이 붙은 빈 객체를 찾아서 빈 객체의 @Pointcut 설정과 @Around 설정을 사용한다.  
  
@EnableAspectJAutoProxy 애노테이션은 프록시 생성과 관련된 AnnotationAwareAspectJAutoProxyCreator 객체를 빈으로 등록한다.  
```java
@Pointcut("execution(public * chap07..*(..))")
public void publicTarget() {

}

@Around("publicTarget()")
public Object measure(ProceedingJointPoint joinPoint) throws Throwable {
	...
}
```
calculator 빈에 공통 기능이 적용되는지 확인해보자.  
```java
public class MainAspect {

	public static void main(String[] args) {
		final AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppCtx.class);
		final Calculator cal = ac.getBean("calculator", Calculator.class);
		final long fiveFact = cal.factorial(5);
		System.out.println("cal.factorial(5) = " + fiveFact);
		System.out.println(cal.getClass().getName());
		ac.close();
	}
}
```
실행 결과는 다음과 같다.  
```
RecCalculator.factorial([5]) 실행 시간 : 29750 ns
cal.factorial(5) = 120
com.sun.proxy.$Proxy19
```
이 출력 결과를 보면 Calculator 타입이 RecCalculator 클래스가 아니고 $Proxy17이다. 이 타입은 스프링이 생성한 프록시 타입이다.  
AOP를 적용하지 않았으면 리턴한 객체는 프록시 객체가 아닌 RecCalculator 타입이었을 것이다.  
  
### 3.2 ProceedingJoinPoint의 메서드
Around Advice에서 사용할 공통 기능 메서드는 대부분 파라미터로 전달받은 ProceedingJoinPoint의 proceed() 메서드만 호출하면 된다.  
```java
@Around("publicTarget()")
	public Object measure(ProceedingJoinPoint joinPoint) throws Throwable {
		long start = System.nanoTime();
		try {
			Object result = joinPoint.proceed();
			return result;
			...
```
물론 호출되는 대상 객체에 대한 정보, 실행되는 메서드에 대한 정보, 메서드를 호출할 때 전달된 인자에 대한 정보가 필요할 때가 있다.  
이들 정보에 접근할 수 있도록 ProceedingJoinPoint 인터페이스는 다음 메서드를 제공한다.  
- Signature getSignature(): 호출되는 메서드에 대한 정보를 구한다.  
- Object getTarget(): 대상 객체를 구한다.  
- Object[] getArgs(): 파라미터 목록을 구한다.  
  
org.aspectj.lang.Signature 인터페이스는 다음 메서드를 제공한다. 각 메서드는 호출되는 메서드의 정보를 제공한다.  
- String getName(): 호출되는 메서드의 이름을 구한다.  
- String toLongString(): 호출되는 메서드를 완전하게 표현한 문장을 구한다(메서드의 리턴 타입, 파라미터 타입이 모두 표시된다).  
- String toShortString(): 호출되는 메서드를 축약해서 표현한 문장을 구한다(기본 구현은 메서드의 이름만을 구한다).  
  
## 4. 프록시 생성 방식
```java
RecCalculator cal = ctx.getBean("calculator", RecCalculator.class);
```
다음과 같이 변경하면 익셉션이 발생한다.  
```
Bean named 'calculator' is expected to be of type 'me.euichan.aop.RecCalculator' but was actually of type 'com.sun.proxy.$Proxy17'
```
익셉션 메세지를 보면 getBean() 메서드에 사용한 타입이 RecCalculator인데 반해 실제 타입은 $Proxy17이라는 메시지가 나온다.  
$Proxy17은 스프링이 런타임에 생성한 프록시 객체의 클래스 이름이다. $Proxy17 클래스는 RecCalculator 클래스가 상속받은 Calculator 인터페이스를 상속받게 된다.  
  
스프링은 AOP를 위한 프록시 객체를 생성할 때 **실제 생성할 빈 객체가 인터페이스를 상속하면 인터페이스를 이용해서 프록시를 생성한다.**  
따라서 실제 빈의 타입이 RecCalculator이라고 하더라도 "calculator"이름에 해당하는 빈 객체의 타입은 Calculator 인터페이스를 상속받은 프록시 타입이 된다.  
```java
// 설정 클래스
// AOP 적용시 RecCalculator 가 상속받은 Calculator 인터페이스를 이용해서 프록시 생성
@Bean
public Calculator calculator() {
	return new RecCalculator();
}

// 자바 코드
// "calculator" 빈의 실제 타입은 Calculator를 상속한 프록시 타입이므로
// RecCalculator로 타입 변환을 할 수 없기 때문에 익셉션 발생
RecCalculator cal = ctx.getBean("calculator", RecCalculator.class);
```  
빈 객체가 인터페이스를 상속할 때 인터페이스가 아닌 클래스를 이용해서 프록시를 생성하고 싶다면 다음과 같이 설정한다.  
```java
@Configuration
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class AppCtx {
```
인터페이스가 아닌 자바 클래스를 상속받아 프록시를 생성한다. 실제 RecCalculator를 상속받는다.  
  
### 4.2 Advice 적용 순서
```java
public class MainAspectWithCache {

	public static void main(String[] args) {
		final AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(
			AppCtxWithCache.class);
		final Calculator calculator = ac.getBean("calculator", Calculator.class); 
		calculator.factorial(7); // CacheAspect 실행 -> ExeTimeAspect 실행 -> 대상 객체 실행 
		calculator.factorial(7);
		calculator.factorial(5);
		calculator.factorial(5);
		ac.close();
	}
}
```
어떤 Aspect가 먼저 적용될지는 스프링 프레임워크나 자바 버전에 따라 달라질 수 있기 때문에 적용 순서가 중요하다면 직접 순서를 지정해야 한다.  
이럴 때 사용하는 것이 @Order 애노테이션이다.  
@Order 애노테이션의 값이 작으면 먼저 적용하고 크면 나중에 적용한다.  
  
### 4.3 @Around의 Pointcut 설정과 @Pointcut 재사용
여러 Aspect에서 공통으로 사용하는 Pointcut이 있다면 다음과 같이 별도 클래스에 Pointcut을 정의하고, 각 Aspect 클래스에서 해당 Pointcut을 사용하도록 구성하면  
Pointcut 관리가 편해진다.  
```java
public class CommonPointcut {

	@Pointcut("execution(public * me.euichan.aop..*(..))")
	public void commonTarget() {

	}
}
...

@Aspect
@Order(2)
public class CacheAspect {

	private Map<Long, Object> cache = new HashMap<>();

	@Around("CommonPointcut.commonTarget()") // 같은 패키지에 위치하므로 패키지 이름이 없는 간단한 클래스 이름으로 설정할 수 있다.
	public Object execute(...)
```
CommonPointcut은 빈으로 등록할 필요가 없다. @Around 애노테이션에서 해당 클래스에 접근 가능하면 해당 Pointcut을 사용할 수 있다.  
  
# Chapter 08 DB 연동 
이 책에서는 JDBC를 위해 스프링이 제공하는 JdbcTemplate의 사용법을 설명한다.  
  
## 1. JDBC 프로그래밍의 단점을 보완하는 스프링
구조적인 반복을 줄이기 위한 방법은 템플릿 메서드 패턴과 전략 패턴을 함께 사용하는 것이다. 스프링은 바로 이 두 패턴을 엮은 JdbcTemplate 클래스를 제공한다.  
  
스프링이 제공하는 또 다른 장점은 트랜잭션 관리가 쉽다는 것이다. JDBC API로 트랜잭션을 처리하려면 다음과 같이 Connection의 setAutoCommit(false)을 이용해 자동 커밋을 비활성화하고 commit()과 rollback() 메서드를 이용해서 트랜잭션을 커밋하거나 롤백해야 한다.  
  
스프링을 사용하면 트랜잭션을 적용하고 싶은 메서드에 @Transactional 애노테이션을 붙이기만 하면 된다.  
  
## 2. 프로젝트 준비
- spring-jdbc: JdbcTemplate 등 JDBC 연동에 필요한 기능을 제공한다.  
- tomcat-jdbc: DB 커넥션풀 기능을 제공한다.  
- mysql-connector-java: MySQL 연결에 필요한 JDBC 드라이버를 제공한다.  
  
스프링이 제공하는 트랜잭션 기능을 사용하려면 spring-tx 모듈이 필요한데, spring-jdbc 모듈에 대한 의존을 추가하면 spring-tx 모듈도 자동으로 포함된다.  
  
### 커넥션 풀이란?
실제 서비스 운영 환경에서는 서로 다른 장비를 이용해서 자바 프로그램과 DBMS를 실행한다. 자바 프로그램에서 DBMS로 커넥션을 생성하는 시간은 컴퓨터 입장에서 매우 길기 때문에 DB 커넥션을 생성하는 시간은 전체 성능에 영향을 줄 수 있다. 또한 동시에 접속하는 사용자수가 많으면 DB 커넥션을 생성해서 DBMS에 부하를 준다.  
  
**최초 연결에 따른 응답 속도 저하와 동시 접속자가 많을 때 발생하는 부하를 줄이기 위해 사용하는 것이 커넥션 풀이다.**  
커넥션 풀은 일정 개수의 DB 커넥션을 미리 만들어두는 기법이다. DB 커넥션이 필요한 프로그램은 커넥션 풀에서 커넥션을 가져와 사용한 뒤 커넥션을 다시 풀에 반납한다.  
커넥션을 미리 생성해두기 때문에 커넥션을 사용하는 시점에서 커넥션을 생성하는 시간을 아낄 수 있다.  
또한 동시 접속자가 많더라도 `커넥션을 생성하는 부하가 적기 때문에` 더 많은 동시 접속자를 처리할 수 있다.  
커넥션도 일정 개수로 유지해서 DBMS에 대한 부하를 일정 수준으로 유지할 수 있게 해 준다.  
  
DB 커넥션 풀 기능을 제공하는 모듈로는 Tomcat JDBC, HikariCP, DBCP, c3p0 등이 존재한다.  
  
## 3. DataSource 설정 
