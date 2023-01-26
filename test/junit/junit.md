# JUnit 
method 찾아보고 정리하기  

## Assumption
전제문이 true라면 실행, false라면 종료  
- assumeTrue : false일 때 이후 테스트 전체가 실행되지 않음  
- assumingThat : 파라미터로 전달된 코드블럭만 실행되지 않음  
```java
@Test
void dev_env_only() {
  assumeTrue("DEV".equals(System.getenv("ENV")), () -> "개발 환경이 아닙니다.");
  assertEquals("A", "A"); // 단정문이 실행되지 않음
}

@Test
void some_test() {
  assumingThat("DEV".equals(System.getenv("ENV")),
  () -> {
  assertEquals("A", "A"); // 단정문이 실행되지 않음
  });
  assertEquals("A", "A"); // 단정문이 실행된다.
}
```
## @Nested
`@Nested` 애노테이션이 있는 클래스를 둘러싸고 있는 클래스의 인스턴스와 설정 및 상태를 공유할 수 있는 중첩된 비정적(non-static) 테스트 클래스입니다.  
둘러싸는 클래스는 최상위 클래스일수도 있고 @Nested 테스트 클래스일수도 있으며, 중첩은 깊어질 수 있습니다.  
  
@TestClassOrder 또는 전역 ClassOrder 를 이용해 순서를 지정할 수 있습니다.  
@Nested 테스트 클래스는 둘러싸는 테스트 클래스와 다를 수 있는 자체 `TestInstance.Lifecycle` 모드를 구성할 수 있습니다.  
  
비정적(non-static) 중첩된 테스트 클래스를 의미합니다.  
@BeforeAll, @AfterAll 사용이 불가하며, 자체적으로 TestInstance LifeCycle을 가질 수 있습니다. 해당 애노테이션은 상속이 되지 않습니다.  
@Nested 테스트는 여러 테스트 그룹간의 관계를 표현할 수 있꼬 계층적으로 나타낼 수 있습니다.  
```java
import static org.junit.jupiter.api.Assertions.*;

import java.util.EmptyStackException;
import java.util.Stack;

import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;

public class TestingStack {
	Stack<Object> stack;

	@Test
	@DisplayName("is instantiated with new Stack()")
	void isInstantiatedWithNew() {
		new Stack<>();
	}

	@Nested
	@DisplayName("when new")
	class WhenNew {
		@BeforeEach
		void createNewStack() {
			stack = new Stack<>();
		}

		@Test
		@DisplayName("is empty")
		void isEmpty() {
			assertTrue(stack.isEmpty());
		}

		@Test
		@DisplayName("throw EmptyStackException when popped")
		void throwsExceptionWhenPopped() {
			assertThrows(EmptyStackException.class, stack::pop);
		}

		@Test
		@DisplayName("throws EmptyStackException when peeked")
		void throwsExceptionWhenPeeked() {
			assertThrows(EmptyStackException.class, stack::peek);
		}

		@Nested
		@DisplayName("after pushing an element")
		class AfterPushing {
			String element = "element";

			@BeforeEach
			void pushAnElement() {
				stack.push(element);
			}

			@Test
			@DisplayName("it is no longer empty")
			void isNotEmpty() {
				assertFalse(stack.isEmpty());
			}

			@Test
			@DisplayName("returns the element when popped and is empty")
			void returnElementWhenPopped() {
				assertEquals(element, stack.pop());
				assertTrue(stack.isEmpty());
			}

			@Test
			@DisplayName("returns the element when peeked but remains not empty")
			void returnElementWhenPeeked() {
				assertEquals(element, stack.peek());
				assertFalse(stack.isEmpty());
			}
		}

	}
}
```
## @TestInstance(Lifcycle.PER_CLASS), @TestInstance(...)
테스트 클래스 또는 테스트 인터페이스에 대한 테스트 인스턴스의 수명 주기를 구성하는 데 사용합니다.  
default 값으로는 PER_METHOD로 설정이 되며, 모든 중첩 테스트 클래스내 상속이 됩니다.  
  
@TestInstance(Lifcycle.PER_CLASS) 로 설정하면 테스트 클래스의 비정적(non-static)의 @BeforeAll 및 @AfterAll을 사용할 수 있고  
테스트 클래스의 테스트 메서드 간에 인스턴스 상태를 공유하게 됩니다.  
  
@TestInstance의 LifeCycle Mode를 PER_CLASS로 설정하면 아래와 같은 기능이 활성화 됩니다.  
- 테스트 클래스의 @BeforeAll, @AfterAll 메서드 뿐만 아니라 테스트 클래스의 테스트 메서드 간에 인스턴스 상태를 공유합니다.  
- @Nested 클래스에서 @BeforeAll, @AfterAll 을 사용가능하게 됩니다.  
  
LifeCycle이란?  
```java
enum Lifecycle {

        /**
         * When using this mode, a new test instance will be created once per test class.
         *
         * @see #PER_METHOD
         */
        PER_CLASS,

        /**
         * When using this mode, a new test instance will be created for each test method,
         * test factory method, or test template method.
         *
         * <p>This mode is analogous to the behavior found in JUnit versions 1 through 4.
         *
         * @see #PER_CLASS
         */
        PER_METHOD;

    }
```
PER_CLASS : 이 모드를 사용할 때 새 테스트 인스턴스는 테스트 클래스당 한 번 생성됩니다.  
PER_METHOD : 이 모드를 사용하면 테스트 메서드에 대해 새 테스트 인스턴스가 생성됩니다.  


