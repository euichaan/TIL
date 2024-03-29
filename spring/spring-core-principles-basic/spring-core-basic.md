# 스프링 핵심 원리 - 기본편

## 역할과 구현을 분리

역할과 구현으로 구분하면 세상이 단순해지고, 유연해지며 변경도 편리해진다.

- 클라이언트는 대상의 역할(인터페이스)만 알면 된다.
- 클라이언트는 구현 대상의 **내부 구조를 몰라도 된다.**
- 클라이언트는 구현 대상의 내부 구조가 변경되어도 영향을 받지 않는다.
- 클라이언트는 구현 대상 자체를 변경해도 영향을 받지 않는다.

자바 언어의 다형성을 활용

- 역할 = 인터페이스
- 구현 = 인터페이스를 구현한 클래스, 구현 객체
- 객체를 설계할 때 역할과 구현을 명확히 분리
- 객체 설계 시 역할(인터페이스)을 먼저 부여하고, 그 역할을 수행하는 구현 객체 만들기

객체의 협력이라는 관계부터 생각

클라이언트 : 요청, 서버 : 응답

수 많은 객체 클라이언트와 객체 서버는 서로 협력 관계를 가진다.

## 자바 언어의 다형성

오버로딩과 오버라이딩

오버로딩은 이름은 같지만 시그니처(파라미터 수, 타입)는 다른 메서드를 중복으로 선언하는 것을 의미한다.

오버라이딩은 부모 클래스 메서드의 동작 방법을 변경(재정의)하여 우선적으로 사용하는 것이다.

- 오버라이딩을 떠올려보자
- 오버라이딩 된 메서드가 실행
- 다형성으로 인터페이스를 구현한 객체를 실행 시점에 유연하게 변경할 수 있다.
- 물론 클래스 상속 관계도 다형성, 오버라이딩 적용가능

## 다형성의 본질

- 인터페이스를 구현한 객체 인스턴스를 실행 시점에 유연하게 변경할 수 있다.
- 다형성의 본질을 이해하려면 협력이라는 객체사이의 관계에서 시작해야함
- 클라이언트를 변경하지 않고, 서버의 구현 기능을 유연하게 변경할 수 있다.

역할과 구현을 분리하면, 유연하고 변경이 용이하며 확장 가능한 설계라는 장점을 얻을 수 있다.

클라이언트에 영향을 주지 않는 변경이 가능하다.

인터페이스를 안정적으로 잘 설계하는 것이 중요하다.

역할(인터페이스)자체가 변하면, 클라이언트, 서버 모두에 큰 변경이 발생한다.

⇒ 인터페이스를 안정적으로 잘 설계하는 것이 중요하다.

## 스프링과 객체 지향

- 다형성이 가장 중요하다.
- 스프링은 다형성을 극대화해서 이용할 수 있게 도와준다.
- 스프링에서 이야기하는 제어의 역전(IoC), 의존관계 주입(DI)은 다형성을 활용해서 역할과 구현을 편리하게 다룰 수 있도록 지원한다.

---

# 좋은 객체 지향 설계의 5가지 원칙(SOLID)

SRP : 단일 책임 원칙 (single responsiblility principle)

OCP : 개방-폐쇄 원칙 (Open/closed principle)

LSP : 리스코프 치환 원칙 (Liskov substitution principle)

ISP : 인터페이스 분리 원칙 (Interface segregation principle)

DIP : 의존관계 역전 원칙 (Dependency Inversion principle)

## SRP 단일 책임 원칙 : Single responsibility principle

- 한 클래스는 하나의 책임만 져야 한다.
- 하나의 책임이라는 것은 모호하다.
  - 클 수 있고, 작을 수 있다.
  - 문맥과 상황에 따라 다르다.
- **중요한 기준은 변경이다.** 변경이 있을 때 파급 효과가 적으면 단일 책임 원칙을 잘 따른 것
- 예) UI 변경, 객체의 생성과 사용을 분리

## OCP 개방 - 폐쇄 원칙 : Open/closed principle

- 소프트웨어 요소는 **확장에는 열려 있으나 변경에는 닫혀 있어야** 한다.
- 이런 거짓말 같은 말이? 확장을 하려면, 당연히 기존 코드를 변경?
- 다형성을 활용해보자
- 인터페이스를 구현한 새로운 클래스를 만들어서 새로운 기능을 구현
- 지금까지 배운 역할과 구현의 분리를 생각해보자
- MemberService 클라이언트가 구현 클래스를 직접 선택
- **구현 객체를 변경하려면 클라이언트 코드를 변경해야 한다.**
- **분명 다형성을 사용했지만 OCP 원칙을 지킬 수 없다.**
- 객체를 생성하고, 연관관계를 맺어주는 별도의 조립, 설정자가 필요하다. (스프링 컨테이너)

## LSP 리스코프 치환 원칙 Liskov substitution principle

- **프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다**
- 다형성에서 하위 클래스는 인터페이스 규약을 다 지켜야 한다는 것, 다형성을 지원하기 위한 원칙, 인터페이스를 구현한 구현체는 믿고 사용하려면, 이 원칙이 필요하다.
- 단순히 컴파일에 성공하는 것을 넘어서는 이야기
- 예) 자동차 인터페이스의 엑셀은 앞으로 가라는 기능, 뒤로 가게 구현하면 LSP 위반, 느리더라도 앞으로 가야함

## ISP 인터페이스 분리 원칙 Interface segregation principle

- **특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다**
- 자동차 인터페이스 → 운전 인터페이스, 정비 인터페이스로 분리
- 사용자 클라이언트 → 운전자 클라이언트, 정비사 클라이언트로 분리
- 분리하면 정비 인터페이스 자체가 변해도 운전자 클라이언트에 영향을 주지 않음
- 인터페이스가 명확해지고, 대체 가능성이 높아진다.

## DIP 의존관계 역전 원칙 Dependency inversion principle

- 프로그래머는 “추상화에 의존해야지, 구체화에 의존하면 안된다.”, **의존성 주입**은 이 원칙을 따르는 방법 중 하나다.
- 쉽게 이야기해서 구현 클래스에 의존하지 말고, 인터페이스에 의존하라는 뜻.
- 앞에서 이야기한 **역할(Role)에 의존해야 한다**는 것과 같다. 클라이언트가 인터페이스에 의존해야 유연하게 구현체를 변경할 수 있다. 구현체에 의존하게 되면 변경이 아주 어려워진다.
- OCP에서 설명한 MemberService는 인터페이스에 의존하지만(코드에 대해 알지만), 구현 클래스도 동시에 의존한다.
- MemberServie m = new MemoryMemberRepository();
- **DIP 위반**

## 정리

- 객체 지향의 핵심은 다형성
- 다형성 만으로는 쉽게 부품을 갈아 끼우듯이 개발할 수 없다.
- 다형성 만으로는 구현 객체를 변경할 때 클라이언트 코드도 함께 변경된다.
- **다형성 만으로는 OCP, DIP를 지킬 수 없다.**
- 뭔가 더 필요하다.

---

## 다시 스프링으로

- 스프링은 다음 기술로 다형성 + OCP, DIP를 가능하게 지원
  - **DI(Dependency Injection) : 의존관계, 의존성 주입**
  - DI 컨테이너 제공(Java 객체 컨테이너에 넣은 후 의존관계 연결, 주입)
- **클라이언트 코드의 변경 없이 기능 확장**
- 순수하게 자바로 OCP, DIP 원칙을 지키면서 개발을 해보면, 결국 스프링 프레임워크를 만들게 된다.(더 정확히는 DI 컨테이너)
- 모든 설계에 ************************************************************역할과 구현을 분리하자************************************************************
- 이상적으로는 모든 설계에 인터페이스를 부여하자
- 하지만 인터페이스를 도입하면 추상화라는 비용이 발생한다.
- 기능을 확장할 가능성이 없다면, 구체 클래스를 직접 사용하고, 향후 꼭 필요할 때 리팩터링해서 인터페이스를 도입하는 것도 방법이다.

---

## 회원 서비스

- 회원가입
- 회원조회

`HashMap`은 동시성 이슈가 발생할 수 있다. `ConcurrentHashMap`을 사용하자.

애플리케이션 로직으로 이렇게 테스트하는 것은 좋은 방법이 아니다. JUnit 테스트를 사용하자.

## 도메인 설계 상 문제점

- 다른 저장소로 변경할 때 OCP 원칙을 잘 준수할까요?
- DIP를 잘 지키고 있을까요?
- **의존관계가 인터페이스 뿐만 아니라 구현까지 모두 의존하는 문제점이 있음**

## 주문 도메인 협력, 역할, 책임
1. 주문 생성 : 클라이언트는 주문 서비스에 주문 생성을 요청한다.
2. 회원 조회 : 할인을 위해서는 회원 등급이 필요하다. 주문 서비스는 회원 저장소에서 회원을 조회한다.
3. 할인 적용 : 주문 서비스는 회원 등급에 따른 할인 여부를 할인 정책에 위임한다.
4. 주문 결과 반환 : 주문 서비스는 할인 결과를 포함한 주문 결과를 반환한다.

할인 정책 변경하려면 클라이언트인 `OrderServiceImpl`코드를 고쳐야 한다. → **문제점**

- OCP, DIP 같은 객체지향 설계 원칙을 충실히 준수한것처럼 보이지만 사실은 아니다.
- 추상(인터페이스) 뿐만 아니라 구체(구현) 클래스에도 의존하고 있다. → **OCP 위반, DIP 위반**

⇒ **인터페이스에만 의존하도록 설계를 변경하자**

구현체가 없는데 ? 누군가가 `OrderServiceImpl`에 `DiscountPolicy`의 구현 객체를 대신 생성하고 주입해주어야 한다.

## AppConfig 등장

애플리케이션 전체 동작 방식을 구성(config)하기 위해, **구현 객체를 생성하고, 연결**하는 책임을 가지는 별도의 설정 클래스를 만들자.

- AppConfig는 애플리케이션의 실제 동작에 필요한 구현 객체를 생성한다.
- AppConfig는 생성한 객체 인스턴스의 참조(레퍼런스)를 생성자를 통해서 주입(연결)해준다.
- 객체의 생성과 연결은 `AppConfig` 가 담당한다.
- 관심사의 분리 : 객체를 생성하고 연결하는 역할(AppConfig)와 실행하는 역할(MemberServiceImpl)이 명확히 분리되었다.
- `AppConfig`객체는 `memoryMemberRepository`객체를 생성하고 그 참조값을 `memberServiceImpl`을 생성하면서 생성자로 전달한다.
- 클라이언트인 memberServiceImpl 입장에서 보면 의존관계를 마치 외부에서 주입해주는 것 같다고 해서 DI(Dependency Injection) 우리말로 의존관계 주입 또는 의존성 주입이라고 한다.
- `AppConfig`는 구체 클래스를 선택한다.
- `AppConfig`의 등장으로 애플리케이션이 크게 사용 영역과, 객체를 생성하고 구성(Configuration)하는 영역으로 분리

⇒ 할인 정책을 변경해도, 애플리케이션의 구성 역할을 담당하는 AppConfig만 변경하면 된다.

`OrderServiceImpl`을 포함해서 사용 영역의 어떤 코드도 변경할 필요가 없다.

구성 영역은 당연히 변경된다. 공연 기획자는 공연 참여자인 구현 객체들을 모두 알아야 한다.

### SRP 단일 책임 원칙 / 한 클래스는 하나의 책임만 가져야 한다.

- 클라이언트 객체는 직접 구현 객체를 생성하고, 연결하고, 실행하는 다양한 책임을 가지고 있음
- SRP 단일 책임 원칙을 따르면서 관심사를 분리함
- 구현 객체를 생성하고 연결하는 책임 - `AppConfig` , 실행하는 책임 - `클라이언트 객체`

### DIP 의존관계 역전 원칙 / 프로그래머는 추상화에 의존해야지, 구체화에 의존하면 안된다. 의존성 주입은 이 원칙을 따르는 방법 중 하나다.

- 클라이언트 코드가 `DiscountPolicy` 추상화 인터페이스에만 의존하도록 코드를 변경했다.
- 하지만 클라이언트 코드는 인터페이스만으로는 아무것도 실행할 수 없다. → NPE
- AppConfig가 `FixDiscountPolicy` 객체 인스턴스를 클라이언트 코드 대신 생성해서 클라이언트 코드에 의존관계를 주입했다. 이렇게 DIP 원칙을 따르면서 문제도 해결했다.

### OCP 개방-폐쇄 원칙 / 소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다

- AppConfig가 의존관계를 `FixDiscountPolicy` → `RateDiscountPolicy`로 변경해서 클라이언트 코드에 주입하므로 클라이언트 코드는 변경하지 않아도 됨
- **소프트웨어 요소를 새롭게 확장해도 사용 영역의 변경은 닫혀 있다!**

---

## **IoC, DI, 그리고 컨테이너**

### 제어의 역전 IoC(Inversion of Control)

- AppConfig가 등장한 이후에 구현 객체는 자신의 로직을 실행하는 역할만 담당한다. 프로그램의 제어 흐름은 이제 AppConfig가 가져간다. 예를 들어서 `OrderServiceImpl`은 팔요한 인터페이스들을 호출하지만 어떤 구현 객체들이 실행될지 모른다.
- 제어 흐름에 대한 권한은 모두 AppConfig가 가지고 있다. `OrderServiceImpl`은 자신의 로직만 실행한다.
- **프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전(IoC)이라 한다.**

### 프레임워크 vs 라이브러리

- 프레임워크가 내가 **작성한 코드를 제어하고, 대신 실행하면** 그것은 프레임워크가 맞다(JUnit)
- **반면에 내가 작성한 코드가 직접 제어의 흐름을 담당하면** 그것은 프레임워크가 아니라 라이브러리다.

### 의존관계 주입 DI(Dependency Injection)

- 의존관계는 **정적인 클래스 의존 관계와, 실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계** 둘을 분리해서 생각해야 한다.

**정적인 클래스 의존관계**

클래스가 사용하는 import 코드만 보고 의존관계를 쉽게 판단할 수 있다. 정적인 의존관계는 애플리케이션을 실행하지 않아도 분석할 수 있다.

`OrderServiceImpl`은 `MemberRepository`, `DiscountPolicy`에 의존한다는 것을 알 수 있다.

그런데 이러한 클래스 의존관계 만으로는 실제 어떤 객체가 `OrderServiceImpl`에 주입될지 알 수 없다.

**동적인 객체 인스턴스 의존 관계**

애플리케이션 **실행 시점에** 실제 생성된 객체 인스턴스의 참조가 연결된 의존 관계다.

애플리케이션 실행 시점마다 동적으로 바뀐다.

- 애플리케이션 실행 시점(런타임)에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결되는 것을 **의존관계 주입이라 한다.**
- 객체 인스턴스를 생성하고, 그 참조값을 전달해서 연결된다.
- 의존관계 주입을 사용하면 클라이언트 코드를 변경하지 않고, 클라이언트가 호출하는 대상의 타입 인스턴스를 변경할 수 있다.
- 의존관계 주입을 사용하면 정적인 클래스 의존관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽게 변경할 수 있다.

### IoC 컨테이너, DI 컨테이너 = IoC 를 해주는 컨테이너, DI 를 해주는 컨테이너

- AppConfig 처럼 객체를 생성하고 관리하면서 의존관계를 연결해 주는 것을
- IoC 컨테이너, DI 컨테이너라 한다.
- 최근에는 주로 DI 컨테이너라 한다.

---

@Bean을 붙이면 스프링 컨테이너에 스프링 빈으로 등록된다.

`ApplicationContext`를 스프링 컨테이너라 한다. 인터페이스이다.

스프링 컨테이너는 @Configuration이 붙은 AppConfig를 설정(구성) 정보로 사용한다. 여기서 `@Bean`이라 적힌 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다. 이렇게 스프링 컨테이너에 등록된 객체를 스프링 빈이라 한다.

스프링 빈은 `ApplicationContext.getBean()`메서드를 통해 찾을 수 있다.

스프링 컨테이너는 XML 기반으로 만들 수 있고 애노테이션 기반의 자바 설정 클래스로 만들 수 있다.

**자바 설정 클래스를 기반으로 스프링 컨테이너를 만들어보자.**

- `new AnnotationConfigApplicationContext(AppConfig.class);`

## 스프링 컨테이너의 생성 과정

1. 스프링 컨테이너 생성
  - 스프링 컨테이너를 생성할 때는 구성 정보를 지정해주어야 한다.
  - 여기서는 `AppConfig.class`를  구성 정보로 지정했다.
2. 스프링 빈 등록
  - 스프링 컨테이너는 파라미터로 넘어온 설정 클래스 정보를 사용해서 스프링 빈을 등록한다.
3. 스프링 빈 의존관계 설정 - 준비
4. 스프링 빈 의존관계 설정 - 완료 (동적인 인스턴스 의존관계 연결)
  - 스프링 컨테이너는 설정 정보를 참고해서 의존관계(DI)를 주입한다.

스프링은 빈을 생성하고, 의존관계를 주입하는 단계가 나누어져 있다.

모든 빈 출력하기

- `ac.getBeanDeifinitionNames()`: 스프링에 등록된 모든 빈 이름을 조회한다.
- `ac.getBean()`: 빈 이름으로 빈 객체(인스턴스)를 조회한다.

애플리케이션 빈 출력하기

- 스프링이 내부에서 사용하는 빈은 `getRole()`로 구분할 수 있다.
  - ROLE_APPLICATION : 일반적으로 사용자가 정의한 빈
  - ROLE_INFRASTRUCTURE : 스프링이 내부에서 사용하는 빈

## 스프링 빈 조회 - 기본

스프링 컨테이너에서 스프링 빈을 찾는 가장 기본적인 조회 방법

- `ac.getBean(빈이름, 타입)`
- `ac.getBean(타입)`

구체 타입으로 조회하면 변경시 유연성이 떨어진다.

타입으로 조회 시 같은 타입의 스프링 빈이 둘 이상이면 오류가 발생한다. 이때는 빈 이름을 지정하자.

- `ac.getBeansOfType()`을 이용하면 해당 타입의 모든 빈을 조회할 수 있다.

## 스프링 빈 조회 - 상속 관계

- 부모 타입으로 조회하면, 자식 타입도 함께 조회한다.
- 그래서 모든 자바 객체의 최고 부모인 Object로 조회하면, 모든 스프링 빈을 조회한다.

## BeanFactory와 ApplicationContext

**BeanFactory**

- 스프링 컨테이너의 최상위 인터페이스
- 스프링 빈을 관리하고 조회(검색)하는 역할을 담당한다.
- getBean()을 제공한다
- 사용했던 대부분의 기능이 BeanFactory가 제공하는 기능이다.

**ApplicationContext**

- BeanFactory 기능을 모두 상속받아서 제공한다.
- 수 많은 부가기능을 제공한다.

**ApplicationContext의 부가 기능**

- 메시지소스를 활용한 국제화 기능
- 환경변수
- 애플리케이션 이벤트
- 편리한 리소스 조회

XML을 사용하면 컴파일 없이 빈 설정 정보를 변경할 수 있는 장점도 있다.

`GenericXmlApplicationContext` 를 사용하면서 xml 설정 파일을 넘기면 된다.

## 스프링 빈 설정 메타 정보 - BeanDefinition

다양한 설정 형식을 지원 - BeanDefinition이라는 추상화가 있기에 가능. (빈 정보에 대한 자체를 추상화 시킴)

쉽게 이야기해서 역할과 구현을 개념적으로 나눈 것.

- XML을 읽어서 BeanDefinition을 만든다.
- 자바 코드를 읽어서 BeanDefinition을 만든다.
- 스프링 컨테이너는 XML인지, 자바 코드인지 몰라도 된다. BeanDefinition만 알면 된다.

BeanDefinition을 빈 설정 메타정보라 한다. 스프링 컨테이너는 이 메타정보를 기반으로 스프링 빈을 생성한다.

1. 설정 정보를 읽어서
2. 빈 메타정보(BeanDefinition)을 생성한다.

스프링이 다양한 형태의 설정 정보를 BeanDefinition으로 추상화해서 사용하는 것 정도만 이해.

---

## Singleton

- 웹 애플리케이션은 보통 여러 고객이 동시에 요청을 한다.

순수한 DI 컨테이너인 AppConfig는 요청을 할 때 마다 객체를 새로 생성한다.

고객 트래픽이 초당 100 → 초당 100개의 객체 생성, 소멸 → 메모리 낭비가 심하다.

해결방안은 해당 객체가 딱 1개만 생성되고, 공유하도록 설계하면 된다.

클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴이다.

그래서 객체 인스턴스가 2개 이상 생성되지 못하도록 막아야 한다. → private 생성자 통해 new 막아야함.

1. static 영역에 객체 Instance를 미리 하나 생성해서 올려둔다.
2. 이 객체 인스턴스가 필요하면 오직 `getInstance()`메서드를 통해서만 조회할 수 있다. 이 메서드를 호출하면 항상 같은 인스턴스를 반환한다.
3. private 생성자로 외부에서 new 키워드로 객체 인스턴스가 생성되는 것을 막는다.

고객이 요청이 올 때 마다 객체를 생성하는 것이 아니라,  이미 만들어진 객체를 공유해서 효율적으로 사용할 수 있다.

## 문제점

- 의존관계상 클라이언트가 구체 클래스에 의존한다. DIP 위반 (getInstance())
- 클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높다.
- 테스트하기 어렵다. 유연성이 떨어진다.
- 내부 속성을 변경하거나 초기화 하기 어렵다.
- private 생성자로 자식 클래스를 만들기 어렵다.

## 싱글톤 컨테이너

스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하면서, 객체 인스턴스를 싱글톤(1개만 생성)으로 관리한다.

지금까지 학습한 스프링 빈이 싱글톤으로 관리되는 빈이다.

스프링은 싱글턴 패턴을 적용하지 않아도, 객체 인스턴스를 싱글톤으로 관리한다.

스프링 컨테이너는 싱글톤 컨테이너 역할을 한다. 이렇게 싱글톤 객체를 생성하고 관리하는 기능을 싱글톤 레지스트리라고 한다.

스프링 컨테이너 덕분에 고객의 요청이 올 때마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 효율적으로 재사용할 수 있다.

기본 빈 등록 방식은 싱글톤이지만, 싱글톤 방식만 지원하는 것은 아님. 요청할 때 마다 새로운 객체를 생성해서 반환하는 기능도 제공한다.

## 싱글톤 방식의 주의점

여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지(stateful)하게 설계하면 안된다.

**무상태(stateless)로 설계해야 한다!**

- 특정 클라이언트에 의존적인 필드가 있으면 안된다.
- 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.
- 가급적 읽기만 가능해야 한다.(가급적 값의 수정을 못하게 한다)
- 필드 대신 자바에서 공유되지 않는 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.

스프링 빈의 필드에 공유 값을 설정하면 정말 큰 장애가 발생할 수 있다!!

StatefulService의 price 필드는 공유되는 필드인데(Singleton이라), 특정 클라이언트가 값을 변경한다.

**진짜 공유필드는 조심해야 한다. 스프링 빈은 항상 무상태로 설계하자.**

## @Configuration과 바이트코드 조작의 마법

스프링 컨테이너는 싱글톤 레지스트리다. (싱글톤 객체를 생성하고 관리)

따라서 스프링 빈이 싱글톤이 되도록 보장해주아야 한다. 그런데 자바 코드까지 어떻게 하기는 어렵다.

그래서 스프링은 클래스의 바이트코드를 조작하는 라이브러리를 사용한다

예상과는 다르게 클래스명에 `xxxCGLIB` 이 붙으면서 복잡해졌다.

“이것은 내가 만든 클래스가 아니라 스프링이 CGLIB이라는 바이트코드 조작 라이브러리를 사용해서 AppConfig를 상속받은 임의의 다른 클래스를 만들고, 그 다른 클래스를 스프링 빈으로 등록한 것이다”

그 임의의 다른 클래스가 바로 싱글톤이 보장되도록 해준다.

```java
@Bean
public MemberRepository memberRepository() {
	if (memberRepository가 이미 스프링 컨테이너에 등록되어 있으면?) {
		return 스프링 컨테이너에서 찾아서 반환;
	} else {
	기존 로직을 호출해서 MemoryMemberRepository 생성하고 스프링 컨테이너에 등록
	return 반환
	}
}
```

**@Bean이 붙은 메서드마다 이미 스프링 빈이 존재하면 존재하는 빈을 반환하고, 스프링 빈이 없으면 생성해서 스프링 빈으로 등록하고 반환하는 코드가 동적으로 만들어진다.**

정리

- @Bean 만 사용해도 스프링 빈으로 등록되지만, 싱글톤을 보장하지는 않는다.
- 스프링 관련 설정 정보는 `@Configuration`을 사용하자.

---

## 컴포넌트 스캔과 의존관계 자동 주입

- 빈을 직접 등록하면 설정 정보가 커지고, 누락하는 문제가 발생한다.
- 그래서 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 컴포넌트 스캔이라는 기능을 제공한다.
- 의존관계도 자동으로 주입하는 `@Autowired` 기능도 제공한다.

`@ComponentScan`은 `@Component`가 붙은 클래스를 찾아서 자동으로 스프링 빈으로 등록한다.

컴포넌트 스캔은 이름 그대로 `@Component` 애노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록한다.

이전 AppConfig에서는 `@Bean`으로 직접 설정 정보를 작성했고, 의존관계도 직접 명시했다.

이제는 이런 설정 정보 자체가 없기 때문에, 의존관계 주입도 이 클래스 안에서 해결해야 한다.

`@Autowired` 는 의존관계를 자동으로 주입해준다. 생성자에서 여러 의존관계도 한번에 주입받을 수 있다.

1. @ComponentScan
  - @ComponentScan은 @Component가 붙은 모든 클래스를 스프링 빈으로 등록한다.
  - 이때 스프링 빈의 기본 이름은 클래스명을 사용하되 맨 앞글자만 소문자를 사용한다.
    - MemberServiceImpl → memberServiceImpl
    - 빈 이름을 직접 지정하고 싶으면 @Component(”memberService2”) 이런 식으로 지정한다.
2. @Autowired 의존관계 자동 주입
  - 생성자에 `@Autowired`를 지정하면, 스프링 컨테이너가 자동으로 해당 스프링 빈을 찾아서 주입한다.
  - 이때 기본 조회 전략은 타입이 같은 빈을 찾아서 주입한다.
    - `getBean(MemberRepository.class)`와 동일하다고 이해하면 된다.
  - 생성자에 파라미터가 많아도 다 찾아서 자동으로 주입한다.

탐색할 패키지의 시작 위치 지정

```java
@ComponentScan(
	basePackages = "hello.core",
)
```

basePackageClasses : 지정한 클래스의 패키지를 탐색 시작 위치로 지정한다.

만약 지정하지 않으면 `@ComponentScan`이 붙은 설정 정보 클래스의 패키지가 시작 위치가 된다.

**패키지 위치 지정하지 않고, 설정 정보 클래스의 위치를 프로젝트 최상단에 두는 것 - 권장하는 방법**

@SpringBootApplication 설정 안에 @ComponentScan이 들어있다.

- `@Controller` : 스프링 MVC 컨트롤러에서 사용
- `@Service` : 스프링 비즈니스 로직에서 사용
- `@Repository` : 스프링 데이터 접근 계층에서 사용
- `@Configuration` : 스프링 설정 정보에서 사용

애노테이션에는 상속관계라는 것이 없다. 애노테이션이 특정 애노테이션을 들고 있는 것을 인식할 수 있는 것은 스프링이 지원하는 기능이다. (자바 언어가 지원하는 기능이 아님)

- `@Controller` : 스프링 MVC 컨트롤러로 인식
- `@Repository` : 스프링 데이터 접근 계층으로 인식으로, **데이터 계층의 예외를 스프링 예외로 변환해준다.**
- `@Configuration` : 스프링 설정 정보로 인식하고, 스프링 빈이 싱글톤을 유지하도록 추가 처리를 한다.
- `@Service` : 의외로 특별한 처리를 하지 않는다. 대신 개발자들이 핵심 비즈니스 로직이 여기 있겠구나 라고 비즈니스 계층을 인식하는데 도움이 된다.

@Target : 유효한 자바 요소의 종류 정확히 지정

@Retension : 해당 애노테이션이 적용되고 유지되는 범위를 설정해줍니다.

RetensionPolicy.SOURCE : 컴파일 되기 전 소스레벨. @SuppressWarnings와 같음

@Documented : JavaDoc 생성 시 Annotation에 대한 정보도 생성하여 줍니다.

## 필터

includeFilters, excludeFilters

```java
@ComponentScan(
	includeFilters = @Filter(type = FilterType.ANNOTATION, classes = 
MyIncludeComponent.class),
...
```

ASSIGNABLE_TYPE : 지정한 타입과 자식 타입을 인식해서 동작한다.

특히 최근 스프링 부트는 컴포넌트 스캔을 기본으로 제공하는데, **개인적으로는 옵션을 변경하면서 사용하기보다는**

**스프링의 기본 설정에 최대한 맞추어 사용하는 것을 권장하고, 선호하는 편이다.**

## 중복 등록과 충돌

컴포넌트 스캔에서 같은 빈 이름을 등록하면?

### 1. 자동 빈 등록 vs 수동 빈 등록

`ConflictingBeanDefinitionException` 예외 발생

### 2. 수동 빈 등록 vs 자동 빈 등록

수동 빈 등록이 우선권을 가진다.(수동 빈이 자동 빈을 오버라이딩 해버린다).

어설픈 추상화보다는 명확한 것이 우선하도록 한다.

---

## 다양한 의존관계 주입 방법

1. 생성자 주입
2. 수정자 주입(setter 주입)
3. 필드 주입
4. 일반 메서드 주입

### **생성자 주입**

- 생성자 호출시점에 딱 1번만 호출되는 것이 보장된다.
- 불변, 필수 의존관계에 사용
- 생성자가 딱 1개만 있으면 `@Autowired`를 생략해도 자동 주입된다(물론 스프링 빈에만 해당)
- 빈 등록하면서 의존관계 주입도 같이 일어남

스프링 컨테이너 life cycle

1. 스프링 빈 등록
2. 의존관계 자동으로 주입

### 수정자 주입

- 선택, 변경 가능성이 있는 의존관계에 사용

`@Autowired`의 기본 동작은 주입할 대상이 없으면 오류가 발생한다. 주입할 대상이 없어도 동작하게 하려면

`@Autowired(required = false)` 로 지정하면 된다.

### 필드 주입

- **외부에서 변경이 불가능해서 테스트 하기 힘들다는 치명적인 단점이 있다.**
- DI 프레임워크가 없으면 아무것도 할 수 없다.
- 사용하지 말자.
  - 프로덕션 코드와 관계 없는 테스트 코드에 사용할 수 있다.
  - 스프링 설정을 목적으로 하는 `@Configuration` 같은 곳에서만 특별한 용도로 사용

순수한 자바 테스트 코드에는 당연히 `@Autowired`가 동작하지 않는다.`@SpringBootTest`처럼 스프링 컨테이너를 테스트에 통합한 경우에만 가능하다.

수동 등록시 자동 등록된 빈의 의존관계가 필요할 때 문제를 해결할 수 있다.

### 일반 메서드 주입

- 한번에 여러 필드를 주입 받을 수 있다.
- 일반적으로 잘 사용하지 않는다.

의존관계 자동 주입은 스프링 컨테이너가 관리하는 스프링 빈이어야 동작한다.

### 옵션 처리

주입할 스프링 빈이 없어도 동작해야 할 때가 있다.

`@Autowired(require=true)`가 기본 옵션. 자동 주입 대상이 없으면 오류가 발생한다.

자동 주입 대상을 옵션으로 처리하는 방법은 다음과 같다.

- @Autowired(required=false) : 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출 안됨
- org.springframework.lang.@Nullable : 자동 주입할 대상이 없으면 null이 입력된다.
- Optional<> : 자동 주입할 대상이 없으면 `Optional.empty`가 입력된다.

## 생성자 주입을 선택하라!

**불변**

- 대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료시점까지 의존관계를 변경할 일이 없다. 오히려 대부분의 의존관계는 애플리케이션 종료 전까지 변하면 안된다(불변해야 한다)
- setXxx 메서드를 public으로 열어두어야 한다.
- 생성자 주입은 객체를 생성할 때 딱 1번만 호출되므로 이후에 호출되는 일이 없다. 따라서 불변하게 설계할 수 있다.

**누락**

생성자 주입을 사용하면 다음처럼 주입 데이터를 누락 했을 때 `컴파일 오류`가 발생한다.

그리고 IDE에서 바로 어떤 값을 필수로 주입해야 하는지 알 수 있다.

**final**

생성자에서 혹시라도 값이 설정되지 않는 오류를 컴파일 시점에 막아준다.

수정자 주입을 포함한 나머지 주입 방식은 모두 생성자 이후에 호출되므로, 필드에 final 키워드를 사용할 수 없다. 오직 생성자 주입 방식만 final 키워드를 사용할 수 있다.

항상 생성자 주입을 선택하라! 필드 주입은 사용하지 않는 게 좋다.

## 롬복과 최신 트렌드

`@RequiredArgsConstructor` 기능을 사용하면 final이 붙은 필드를 모아서 생성자를 자동으로 만들어준다.

롬복이 자바의 `애노테이션 프로세서` 라는 기능을 이용해서 컴파일 시점에 생성자 코드를 자동으로 생성해준다.

## 조회 빈이 2개 이상 - 문제

@Autowired는 타입으로 조회한다.

```java
@Autowired
private DiscountPolicy discountPolicy

-> ac.getBean(DiscountPolicy.class) 와 비슷하게 동작 
```

`NoUniqueBeanDefinitionException` 오류가 발생한다.

하위 타입으로 지정할 수도 있지만, DIP를 위배하고 유연성이 떨어진다.

그리고 이름만 다르고, 완전히 똑같은 타입의 스프링 빈이 2개 있을 때 해결이 안된다.

스프링 빈을 수동 등록해서 문제를 해결해도 되지만, 의존 관계 자동 주입에서 해결하는 여러 방법이 있다.

해결 방법 3가지

1. @Autowired 필드 명 매칭
  - 타입 매칭을 시도하고, 이때 여러 빈이 있으면 필드 이름, 파라미터 이름으로 빈 이름을 추가 매칭한다.
  - 스프링은 본인과 같은 타입이거나 그 타입의 자식 타입을 다 끌고온다.
2. @Qualifier 사용. (추가 구분자 붙이기)
  - 주입시 추가적인 방법을 제공하는 것이지 빈 이름을 변경하는 것은 아니다.
  - `@Qualifier("mainDiscountPolicy”)` 를 못찾으면? mainDiscountPolicy라는 이름의 스프링 빈 추가로 찾는다. 하지만 경험상 Qualifier는 Qualifier를 찾는 용도로만 사용하는게 명확하고 좋다.
  - 1. Qualifier끼리 매칭 2. 빈 이름 매칭 3. `NoSuchBeanDefinitionException` 예외 발생
3. **@Primary 사용**
  - @Autowired 시에 여러 빈이 매칭되면 `@Primary`가 우선권을 가진다.

자세한 것이 우선권을 가져간다. 스프링은 자동보다는 수동이, 넓은 범위의 선택권보다는 좁은 범위의 선택권이 우선한다. `@Primary` 보다 `@Qualifier`가 우선권이 높다.

`@Qualifier(”mainDiscountPolicy”)`와 같이 적으면 컴파일시 타입 체크가 안된다.

→ 애노테이션을 만들어 해결할 수 있다.

애노테이션은 상속이라는 개념이 없다.

**이렇게 여러 애노테이션을 모아서 사용하는 기능은 스프링이 지원해주는 기능이다.**

다른 애노테이션들도 함께 조합해서 사용할 수 있다.

무분별하게 재정의 하는 것은 유지보수에 더 혼란만 가중할 수 있다.

## 조회한 빈이 모두 필요할 때, List, Map

스프링을 사용하면 전략 패턴을 매우 간단하게 구현할 수 있다.

**스프링 컨테이너를 생성하면서 스프링 빈 등록하기**

스프링 컨테이너는 생성자에 클래스 정보를 받는다. 여기에 클래스 정보를 넘기면 해당 클래스가 스프링 빈으로 자동 등록된다.

`new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService,class);`

- new AnnotationConfigApplicationContext() 를 통해 스프링 컨테이너를 생성한다.
- AutoAppConfig.class, DiscountService.class 를 파라미터로 넘기면서 해당 클래스를 자동으로 스프링 빈으로 등록한다.

정리하면 스프링 컨테이너를 생성하면서, 해당 컨테이너에 동시에 AutoAppConfig, DiscountService를 스프링 빈으로 자동 등록한다.

## 자동, 수동의 올바른 실무 운영 기준

점점 자동을 선호하는 추세. 자동 빈 등록을 사용해도 OCP, DIP를 지킬 수 있다.

애플리케이션은 크게 업무 로직과 기술 지원 로직으로 나눌 수 있다.

업무 로직 빈 : 웹을 지원하는 컨트롤러, 핵심 비즈니스 로직이 있는 서비스, 데이터 계층의 로직을 처리하는 리포지토리등이 모두 업무 로직이다. 보통 비즈니스 요구사항을 개발할 때 추가되거나 변경된다. **(자동 빈 등록 우선)**

기술 지원 빈 : 기술적인 문제나 공통 관심사(AOP)를 처리할 때 주로 사용된다. 데이터베이스 연결이나, 공통 로그 처리 처럼 업무 로직을 지원하기 위한 하부 기술이나 공통 기술들이다. **(수동 빈 등록 우선)**

기술 지원 로직은 업무 로직과 비교해서 그 수가 매우 적고, 보통 애플리케이션 전반에 걸쳐서 광범위하게 영향을 미친다. 그리고 업무 로직은 문제가 발생했을 때 어디가 문제인지 명확하게 잘 들어나지만, 기술 지원 로직은 적용이 잘 되고 있는지 아닌지 조차 파악하기 어려운 경우가 많다. **그래서 이런 기술 지원 로직들은 가급적 수동 빈 등록을 사용해서 명확하게 들어내는 것이 좋다.**

설정 정보는 root에 두는 것이 좋다. 기술 지원 객체는 수동 빈으로 등록해서 설정 정보에 바로 나타나게 하는 것이

유지보수 하기 좋다.

또한, 비즈니스 로직 중에서 다형성을 적극 활용할 때 수동 빈 등록 고려

**정리**

편리한 자동 기능을 기본으로 사용하자

직접 등록하는 기술 지원 객체는 수동 등록(데이터베이스 연결이나, 공통 로그 처리처럼)

다형성을 적극 활용하는 비즈니스 로직은 수동 등록을 고민해보자

---

## 빈 생명주기 콜백

콜백 함수

1. 다른 함수의 인자로써 이용되는 함수
2. **어떤 이벤트에 의해 호출되어지는 함수**

데이터베이스 커넥션 풀이나, 네트워크 소켓처럼 시작 시점에 필요한 연결을 미리 해두고, 애플리케이션 종료 시점

에 연결을 모두 종료하는 작업을 진행하려면, 객체의 초기화와 종료 작업이 필요하다.

스프링을 통해 이러한 초기화 작업과 종료 작업을 어떻게 진행하는지 알아보자.

객체를 생성하는 단계에서는 url이 없고, 객체를 생성한 다음에 외부에서 수정자 주입을 통해 `setUrl()` 이 호출되어야 url이 존재하게 된다.

스프링 빈의 라이프사이클

**객체 생성 → 의존관계 주입(단, 생성자 주입은 예외) → 초기화 작업**

초기화 작업 : ~~객체 생성이 아닌~~, 처음 제대로 일을 시작하는 것

스프링 빈은 객체를 생성하고, 의존관계 주입이 다 끝난 다음에야 필요한 데이터를 사용할 수 있는 준비가 완료된다. 따라서 초기화 작업은 의존관계 주입이 모두 완료되고 난 다음에 호출해야 한다.

그런데 개발자가 의존관계 주입이 모두 완료된 시점을 어떻게 알 수 있을까?

⇒ 스프링은 의존관계 주입이 완료되면 스프링 빈에게 콜백 메서드를 통해서 초기화 시점을 알려주는 다양한 기능을 제공한다. 또한 스프링은 스프링 컨테이너가 종료되기 직전에 소멸 콜백을 준다.

### 스프링 컨테이너 생성 → 스프링 빈 생성 → 의존관계 주입 → 초기화 콜백 → 사용 → 소멸전 콜백 → 스프링 종료

초기화 콜백 : 빈이 생성되고, 빈의 의존관계 주입이 완료된 후 호출

소멸전 콜백 : 빈이 소멸되기 직전에 호출

객체의 생성과 초기화를 분리하자.

생성자 - 필수 정보(파라미터)를 받고, 메모리를 할당해서 객체를 생성하는 책임

초기화 - 생성된 값들을 활용해서 외부 커넥션을 연결하는 등 무거운 동작

참고 : 싱글톤 빈은 스프링 컨테이너가 종료될 때 싱글톤 빈들도 함께 종료되기 때문에 스프링 컨테이너가 종료되기 직전에 소멸전 콜백이 일어난다.

싱글톤처럼 컨테이너의 시작과 종료까지 생존하는 빈도 있지만, 생명주기가 짧은 빈들도 있는데 이 빈들은 컨테이너와 무관하게 해당 빈이 종료되기 직전에 소멸전 콜백이 일어난다.

### 스프링의 3가지 빈 생명주기 콜백 지원

1. 인터페이스(InitializingBean, DisposableBean)
2. 설정 정보에 초기화 메서드, 종료 메서드 지정
3. @PostConstruct, @PreDestroy 애노테이션 지원

`InitializingBean` 은 `afterPropertiesSet()` 메서드로 초기화를 지원한다.

`Disposable` 은 `destroy()` 메서드로 소멸을 지원한다.

초기화, 소멸 인터페이스의 단점

- 이 인터페이스는 스프링 전용 인터페이스. 해당 코드가 스프링 전용 인터페이스에 의존한다.
- 내가 코드를 고칠 수 없는 외부 라이브러리에 적용할 수 없다.

설정 정보에 `@Bean(initMethod = “init”, destroyMethod = “close”)` 처럼 초기화, 소멸 메서드를 지정할 수 있다.

- 메서드 이름을 자유롭게 줄 수 있다.
- 스프링 빈이 스프링 코드에 의존하지 않는다.
- 코드가 아니라 설정 정보를 사용하기 때문에 코드를 고칠 수 없는 외부 라이브러리에도 초기화, 종료 메서드를 적용할 수 있다.
- destroyMethod에는 아주 특별한 기능이 있다. (inferred) 추론 기능
- 이 추론 기능은 `close, shutdown` 이라는 이름의 메서드를 자동으로 호출해준다. 이름 그대로 종료 메서드를 추론해서 호출해준다.
- 따라서 직접 스프링 빈으로 등록하면 종료 메서드는 따로 적어주지 않아도 잘 동작한다.

## @PostConstruct, @PreDestroy

javax.annotation.PostConstruct → `의존성 주입 후 초기화 수행`

javax → 자바 표준 (JSR-250). 인터페이스의 모음

컴포넌트 스캔과 잘 어울린다.

*유일한 단점은 외부 라이브러리에는 적용하지 못한다는 것이다. 외부 라이브러리를 초기화, 종료 해야 하면 @Bean의 기능을 사용하자.*

정리

- @PostConstruct, @PreDestroy를 사용하자.
- 코드를 고칠 수 없는 외부 라이브러리를 초기화, 종료해야 하면 빈의 initMethod, destroyMethod를 사용하자.

---

## 빈 스코프

스코프 : 빈이 존재할 수 있는 범위

싱글톤 : 기본 스코프, 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프.

프로토타입 : 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입 + 초기화 까지만 관여하고 더는 관리하지 않는 매우 짧은 범위의 스코프.

웹 관련 스코프 (Spring web 관련 기능이 있어야 사용 가능)

- request : 웹 요청이 들어오고 나갈때 까지 유지되는 스코프.
- session : 웹 세션이 생성되고 종료될 때 까지 유지되는 스코프.
- application : 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프.

## 프로토타입 스코프

싱글톤 : 같은 객체 인스턴스의 스프링 빈을 반환.

프로토타입

1. 프로토타입 스코프의 빈을 스프링 컨테이너에 요청한다.
2. 스프링 컨테이너는 이 시점에 프로토타입 빈을 생성하고, 필요한 의존관계를 주입한다.
3. 스프링 컨테이너는 생성한 프로토타입 빈을 클라이언트에 반환한다. 관리 X
4. 이후에 스프링 컨테이너에 같은 요청이 오면 항상 새로운 프로토타입 빈을 생성해서 반환한다.

핵심 - 스프링 컨테이너는 프로토타입 빈을 생성하고, 의존관계 주입, 초기화까지만 처리한다.

프로토타입 빈을 관리할 책임은 프로토타입 빈을 받은 클라이언트에 있다. 그래서 `@PreDestroy` 같은 종료 메서드가 호출되지 않는다.

[정리]

싱글톤 빈은 스프링 컨테이너 생성 시점에 초기화 메서드가 실행.

프로토타입 스코프의 빈은 스프링 컨테이너에서 빈을 조회할 때 생성, 초기화 메서드도 실행.

프로토타입 빈은 **스프링 컨테이너에 요청할 때 마다 새로** 생성된다.

스프링 컨테이너는 프로토타입 빈의 의존관계 주입 그리고 초기화까지만 관여한다.

종료 메서드가 호출되지 않는다.

그래서 프로토타입 빈은 프로토타입 빈을 조회한 클라이언트가 관리해야 한다. 종료 메서드에 대한 호출도 클라이언트가 직접 해야 한다.

## 프로토타입 스코프 - 싱글톤 빈과 함께 사용 시 문제점

싱글톤 빈과 함께 사용할 때는 의도한 대로 잘 동작하지 않는다.

싱글톤 빈은 생성 시점에만 의존관계 주입을 받기 때문에, 프로토타입 빈이 새로 생성되기는 하지만, 싱글톤 빈과 함께 계속 유지되는 것이 문제다.

프로토타입 빈을 주입 시점에만 새로 생성하는게 아니라, 사용할 때 마다 새로 생성해서 사용하는 것을 원할 것.

참고 : 여러 빈에서 같은 프로토타입 빈을 주입 받으면, 주입 받는 시점에 각각 새로운 프로토타입 빈이 생성된다.

## Provider로 문제 해결

가장 간단한 방법 - 싱글톤 빈이 프로토타입을 사용할 때 마다 스프링 컨테이너에 새로 요청하는 것.

**의존관계를 외부에서 주입(DI) 받는게 아니라 이렇게 직접 필요한 의존관계를 찾는 것을 Dependency Lookup(DL) 의존관계 조회(탐색) 이라한다.**

지금 필요한 기능은 지정한 프로토타입 빈을 컨테이너에서 대신 찾아주는 DL 정도의 기능.

```java
@Autowired
private ObjectProvider<PrototypeBean> prototypeBeanProvider;

//prototypeBeanProvider.getObject(); 로 사용 
```

`prototypeBeanProvider.getObject()`을 통해서 항상 새로운 프로토타입 빈이 생성되는 것을 확인할 수 있다.

`getObject()`를 호출하면 내부에서는 스프링 컨테이너를 통해 해당 빈을 찾아서 반환한다.

ObjectFactory 는 기능이 단순하고, ObjectProvider는 ObjectFactory 상속받아 편의 기능이 많음.

ObjectFactory, ObjectProvider 둘 다 스프링에 의존

`javax.inject.Provider` 라는 JSR-330 자바 표준

provider.get()을 통해서  항상 새로운 프로토타입 빈이 생성되는 것을 확인할 수 있다.

provider의 get()을 호출하면 내부에서는 스프링 컨테이너를 통해 해당 빈을 찾아서 반환한다.

프로토타입 빈을 언제 사용할까?

**매번 사용할 때 마다 의존관계 주입이 완료된 새로운 객체가 필요하면 사용하면 된다.**

`ObjectProvider`, `JSR330 Provider` 등은 DL이 필요한 경우는 언제든지 사용할 수 있다.

특별히 다른 컨테이너를 사용할 일이 없다면, 스프링이 제공하는 기능을 사용하면 된다.

## 웹 스코프

싱글톤 : 스프링 컨테이너의 시작과 끝까지 함께하는 매우 긴 스코프

프로토타입 : 생성과 의존관계 주입, 초기화까지만 진행하는 특별한 스코프.

웹 스코프는 웹 환경에서만 동작한다.

웹 스코프는 프로토타입과 다르게 스프링이 해당 스코프의 종료시점까지 관리한다. 따라서 종료 메서드가 호출된다.

[웹 스코프 종류]

request : HTTP 요청 하나가 들어오고 나갈 때 까지 유지되는 스코프, 각각의 HTTP 요청마다 별도의 빈 인스턴스가 생성되고, 관리된다.

session : HTTP Session과 동일한 생명주기를 가지는 스코프

application : 서블릿 컨텍스트(ServletContext)와 동일한 생명주기를 가지는 스코프

websocket : 웹 소켓과 동일한 생명주기를 가지는 스코프

스프링 부트는 웹 라이브러리가 없으면 `AnnotationConfigApplicationContext` 을 기반으로 애플리케이션을 구동. 웹 라이브러리가 추가되면 웹과 관련된 추가 설정과 환경들이 필요하므로 `AnnotationConfigServletWebServerApplicationContext`를 기반으로 애플리케이션을 구동한다.

`@Scope(value = “request”)` 를 사용하면 요청 당 하나씩 생성되고, HTTP 요청이 끝나는 시점에 소멸

스프링 컨테이너가 뜨는 시점이라 HTTP request 요청이 없음.

**웹과 관련된 부분은 컨트롤러까지만 사용해야 한다.**

서비스 계층은 웹 기술에 종속되지 않고, 가급적 순수하게 유지하는 것이 유지보수 관점에서 좋다.

스프링 애플리케이션을 실행하는 시점에서 싱글톤 빈은 생성해서 주입이 가능하지만,

request 스코프 빈은 아직 생성되지 않는다. 이 빈은 실제 고객의 요청이 와야 생성할 수 있다!

ObjectProvider 덕분에 `ObjectProvider.getObect()`를 호출하는 시점까지 **request scope 빈의 생성을 지연할 수 있다.**

`ObjectProvider.getObect()`를 호출하는 시점에는 HTTP 요청이 진행중이므로 request scope 빈의 생성이 정상 처리된다. `ObjectProvider.getObect()`를 LogDemoController, LogDemoService 에서 각각 한번씩 따로 호출해도 같은 HTTP 요청이면 같은 스프링 빈이 반환된다.

## 스코프와 프록시

`@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)`

→ MyLogger 상속받은 가짜 프록시 객체를 생성, 스프링 컨테이너에 “myLogger”라는 이름으로 진짜 대신에 이 가짜 프록시 객체를 등록

이렇게 하면 MyLogger의 가짜 프록시 클래스를 만들어두고 HTTP request와 상관 없이 가짜 프록시 클래스를 다른 빈에 미리 주입해 둘 수 있다.

CGLIB라는 라이브러리로 내 클래스를 상속받은 가짜 프록시 객체를 만들어서 주입한다.

의존관계 주입도 이 가짜 프록시 객체가 주입된다.

**가짜 프록시 객체는 요청이 오면 그때 내부에서 진짜 빈을 요청하는 위임 로직이 들어있다.**

가짜 프록시 객체는 request 스코프의 진짜 `myLogger.logic()`을 호출한다.

가짜 프록시 객체는 원본 클래스를 상속 받아서 만들어졌기 때문에 클라이언트 입장에서는 원본인지 아닌지도 모르게, 동일하게 사용할 수 있다(다형성)

[동작 정리]

- CGLIB라는 라이브러리로 내 클래스를 상속 받은 가짜 프록시 객체를 만들어서 주입한다.
- 이 가짜 프록시 객체는 실제 요청이 오면 그때 내부에서 실제 빈을 요청하는 위임 로직이 들어있다.
- 가짜 프록시 객체는 실제 request scope와는 관계가 없다. 그냥 가짜이고, 내부에 단순한 위윔 로직만 있고, 싱글톤 처럼 동작한다

**[특징 정리]**

- 핵심 아이디어는 **진짜 객체 조회를 꼭 필요한 시점까지 지연처리 한다는 점이다.**

이런 특별한 scope는 꼭 필요한 곳에만 최소화해서 사용하자, 무분별하게 사용하면 유지보수하기 어려워진다.