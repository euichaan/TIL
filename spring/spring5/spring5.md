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
