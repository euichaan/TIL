# Enum과 애노테이션

## enum 정의하는 방법

**열거형이란 서로 관련된 상수를 편리하게 선언하기 위한 것으로 여러 상수를 정의할 때 사용하면 유용하다.**

`JDK1.5` 부터 새로 추가

```java
class card {
	enum Kind { CLOVER, HEART, DIAMOND, SPADE } // 열거형 Kind를 정의
	enum Value { TWO, THREE, FOUR } // 열거형 Value를 정의

	final Kind kind;
	final Value value;
	// 생성자 생략 
}
```

자바의 열거형은 ‘타입에 안전한 열거형(`typesafe enum`)’ 이라서 실제 값이 같아도 타입이 다르면 컴파일 에러가 발생한다. 이처럼 **값뿐만 아니라 타입까지 체크**하기 때문에 타입에 안전하다고 하는 것이다.

더 중요한 것은 상수의 값이 바뀌면, **해당 상수를 참조하는 모든 소스를 다시 컴파일해야 한다는 것이다.**

하지만 열거형 상수를 사용하면, 기존의 소스를 다시 컴파일하지 않아도 된다.

열거형을 정의하는 방법은 간단하다.

```java
enum 열거형이름 { 상수명1, 상수명2, ...}
```

예를 들어 동서남북 4방향을 상수로 정의하는 열거형 Direction은 다음과 같다.

```java
enum Direction { EAST, SOUTH, WEST, NORTH }
```

이 열거형에 정의된 상수를 사용하는 방법은 ‘열거형이름.상수명’이다. 클래스의 static 변수를 참조하는 것과 동일하다.

```java
class Unit {
	int x, y;
	Direction dir;

	void init() {
		dir = Direction.EAST;
	}
}
```

열거형 상수의 비교에는 ‘==’를 사용할 수 있다.

[Enum 비교 == 아니면 equals()?](https://chan9.tistory.com/174)

1. == 는 NPE를 발생시키지 않는다.
2. == 비교는 컴파일 타임에 타입 미스 매치를 잡아준다.

`equals()` 가 아닌 `==`로 비교가 가능하다는 것은 그만큼 빠른 성능을 제공한다는 얘기다.. ? - 자바의 정석 

`<, >`와 같은 비교연산자는 사용할 수 없고 `compareTo()`는 사용가능하다.

> compareTo()는 두 비교대상이 같으면 0, 왼쪽이 크면 양수, 오른쪽이 크면 음수를 반환한다.
> 

## enum이 제공하는 메서드

열거형 Direction에 정의된 모든 상수를 출력하려면, 다음과 같이 한다.

```java
// 외부 반복, external iteration
Direction[] dirs = Direction.values();
		for (Direction dir : dirs) {
			System.out.printf("%s, %d\n", dir.name(), dir.ordinal());
}

// 내부 반복, internal iteration with Stream API
Arrays.asList(Direction.values())
			.stream()
			.forEach(System.out::println);
```

[for-loop 를 Stream.forEach() 로 바꾸지 말아야 할 3가지 이유](https://homoefficio.github.io/2016/06/26/for-loop-를-Stream-forEach-로-바꾸지-말아야-할-3가지-이유/)

`values()`는 열거형의 모든 상수를 배열에 담아 반환한다. 이 메서드느 모든 열거형이 가지고 있는 것으로 컴파일러가 자동으로 추가해 준다. 그리고 ordinal()은 모든 열거형의 조상인 `java.lang.Enum` 클래스에 정의된 것으로, 열거형 상수가 정의된 순서(0부터 시작)을 반환한다.

Enum 클래스에는 다음과 같은 메서드가 정의되어 있다.

| 메서드 | 설명 |
| --- | --- |
| Class<E> getDeclaringClass() | 열거형의 Class 객체를 반환한다. |
| String name() | 열거형 상수의 이름을 문자열로 반환한다. |
| int ordinal() | 열거형 상수가 정의된 순서를 반환한다(0부터 시작) |
| T valueOf(Class<T> enumType, String name) | 지정된 열거형에서 name과 일치하는 열거형 상수를 반환한다. |

이외에도 values() 처럼 컴파일러가 자동으로 추가해주는 메서드가 하나 더 있다.

```
static E values()
static E valueOf(String name)
```

이 메서드는 열거형 상수의 이름으로 문자열 상수에 대한 참조를 얻을 수 있게 해준다.

컴파일러가 자동으로 추가..? →

![](https://humorous-bass-b9e.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd0e14a57-11be-4d73-8bef-05577aa782e5%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-08-10_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_3.11.46.png?table=block&id=ace48b76-e4f1-40cf-978f-19a7b15167d6&spaceId=e738b5df-6beb-459d-a4dd-ce360de05b7b&width=2000&userId=&cache=v2)    

`Enum.java` 의 내용이다. 

특정 유형 T의 경우 해당 열거형에서 암시적으로 선언된 `public static T valueOf(String)` 메서드를 이 메서드 대신 사용하여 이름에서 해당 열거형으로 매핑할 수 있다.

enum 유형의 모든 상수는 해당 유형의 암시적 `public static T[] values()` 메서드를 호출하여 얻을 수 있다.

## java.lang.Enum

모든 열거형의 조상 클래스. 이 enum의 바이트코드를 살펴보면 다음과 같다.

```java
public enum Direction {

	EAST, SOUTH, WEST, NORTH;
}
```

![](https://humorous-bass-b9e.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fdf1c8c93-bff1-4a2a-a019-c9fdcb97b278%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-08-10_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_3.17.28.png?table=block&id=4b9f986c-e961-44dd-b234-ed8933a6a587&spaceId=e738b5df-6beb-459d-a4dd-ce360de05b7b&width=2000&userId=&cache=v2)  

## 열거형에 멤버 추가하기

Enum 클래스에 정의된 `ordinal()`이 열거형 상수가 정의된 순서를 반환하지만, 이 값을 열거형 상수의 값으로 사용하지 않는 것이 좋다. 이 값은 내부적인 용도로만 사용되기 위한 것이기 때문이다.

열거형 상수의 값이 불연속적인 경우에는 이때는 다음과 같이 열거형 상수의 이름 옆에 원하는 값을 괄호()와 함께 적어주면 된다.

```java
public enum Direction {

	EAST(1), SOUTH(5), WEST(-1), NORTH(10)
}
```

그리고 지정할 값을 저장할 수 있는 **인스턴스 변수**와 **생성자**를 새로 추가해 주어야 한다.

이때 주의할 점은, 먼저 열거형 상수를 모두 정의한 다음에 다른 멤버들을 추가해야한다는 것이다.

그리고 열거형 상수의 마지막에 ; 도 붙여줘야 한다.

```java
public enum Direction {

	EAST(1), SOUTH(5), WEST(-1), NORTH(10);

	private final int value; // 정수를 저장할 필드 (인스턴스 변수) 를 추가

	Direction(final int value) {
		this.value = value;
	}

	public int getValue() {
		return value;
	}
}
```

열거형 생성자는 제어자가 묵시적으로 **private**이기 때문에 다음과 같이 열거형의 객체를 생성할 수 없다.

```java
Direction d = new Direction(1);
```

열거형에 추상 메서드를 선언하면 각 열거형 상수가 이 추상 메서드를 반드시 구현해야 한다.

```java
public enum Transportation {

	BUS(100),
	TRAIN(150),
	SHIP(100),
	AIRPLANE(300);

	private final int BASIC_FARE;

	Transportation(final int BASIC_FARE) { // private
		this.BASIC_FARE = BASIC_FARE;
	}

	int fare() {
		return BASIC_FARE;
	}
}
```

열거형에 추상 메서드 `fare(int distance)`를 선언하면 각 열거형 상수가 이 추상 메서드를 반드시 구현해야 한다.

```java
public enum Transportation {

	BUS(100) {
		@Override
		int fare(final int distance) {
			return distance * BASIC_FARE;
		}
	},
	TRAIN(150) {
		@Override
		int fare(final int distance) {
			return distance * BASIC_FARE;
		}
	},
	SHIP(100) {
		@Override
		int fare(final int distance) {
			return distance * BASIC_FARE;
		}
	},
	AIRPLANE(300) {
		@Override
		int fare(final int distance) {
			return distance * BASIC_FARE;
		}
	};

	protected final int BASIC_FARE; // protected 로 해야 각 상수에서 접근 가능

	Transportation(final int BASIC_FARE) { // private
		this.BASIC_FARE = BASIC_FARE;
	}

	abstract int fare(int distance);

	public int getBASIC_FARE() {
		return BASIC_FARE;
	}
}
```

## 열거형의 이해

열거형 Direction이 다음과 같이 정의되어 있을 때,

```java
enum Direction { EAST, SOUTH, WEST, NORTH }
```

사실은 **열거형 상수 하나하나가 Direction** 객체이다.

`javap -c Direction.class` 명령어를 통해 역어셈블해보면 다음과 같이 나온다.

```java
Compiled from "Direction.java"
public final class Direction extends java.lang.Enum<Direction> {
  public static final Direction EAST;

  public static final Direction SOUTH;

  public static final Direction WEST;

  public static final Direction NORTH;

  public static Direction[] values();
    Code:
       0: getstatic     #1                  // Field $VALUES:[LDirection;
       3: invokevirtual #2                  // Method "[LDirection;".clone:()Ljava/lang/Object;
       6: checkcast     #3                  // class "[LDirection;"
       9: areturn

  public static Direction valueOf(java.lang.String);
    Code:
       0: ldc           #4                  // class Direction
       2: aload_0
       3: invokestatic  #5                  // Method java/lang/Enum.valueOf:(Ljava/lang/Class;Ljava/lang/String;)Ljava/lang/Enum;
       6: checkcast     #4                  // class Direction
       9: areturn

  static {};
    Code:
       0: new           #4                  // class Direction
       3: dup
       4: ldc           #7                  // String EAST
       6: iconst_0
       7: invokespecial #8                  // Method "<init>":(Ljava/lang/String;I)V
      10: putstatic     #9                  // Field EAST:LDirection;
      13: new           #4                  // class Direction
      16: dup
      17: ldc           #10                 // String SOUTH
      19: iconst_1
      20: invokespecial #8                  // Method "<init>":(Ljava/lang/String;I)V
      23: putstatic     #11                 // Field SOUTH:LDirection;
      26: new           #4                  // class Direction
      29: dup
      30: ldc           #12                 // String WEST
      32: iconst_2
      33: invokespecial #8                  // Method "<init>":(Ljava/lang/String;I)V
      36: putstatic     #13                 // Field WEST:LDirection;
      39: new           #4                  // class Direction
      42: dup
      43: ldc           #14                 // String NORTH
      45: iconst_3
      46: invokespecial #8                  // Method "<init>":(Ljava/lang/String;I)V
      49: putstatic     #15                 // Field NORTH:LDirection;
      52: iconst_4
      53: anewarray     #4                  // class Direction
      56: dup
      57: iconst_0
      58: getstatic     #9                  // Field EAST:LDirection;
      61: aastore
      62: dup
      63: iconst_1
      64: getstatic     #11                 // Field SOUTH:LDirection;
      67: aastore
      68: dup
      69: iconst_2
      70: getstatic     #13                 // Field WEST:LDirection;
      73: aastore
      74: dup
      75: iconst_3
      76: getstatic     #15                 // Field NORTH:LDirection;
      79: aastore
      80: putstatic     #1                  // Field $VALUES:[LDirection;
      83: return
}
```

살펴볼 점들은 다음과 같다.

1. `final class` 로 선언되어있다. [https://docs.oracle.com/javase/specs/jls/se11/html/jls-8.html#jls-8.1.1.2](https://docs.oracle.com/javase/specs/jls/se11/html/jls-8.html#jls-8.1.1.2) 에 따르면, final 클래스는 하위 클래스를 가질 수 없다. **그러므로 enum은 상속 불가능하다. 또한, final 클래스의 메서드들은 절대 재정의되지 않는다.**
2. 열거형 상수 하나하나가 `Direction` 객체이다. public static final 로 선언되어있다.
3. `values()` 와 `valueOf(java.lang.String)` 메서드를 선언하지 않았는데, 바이트코드에 포함된 것을 알 수 있다.
4. `java.lang.Enum<Direction>` 을 상속받고있다.

1번에서 살펴볼 내용으로, 이펙티브 자바 아이템 3이 있다.

> 이펙티브 자바 아이템 3, private 생성자나 열거 타입으로 싱글턴임을 보증하라.
…
싱글턴을 만드는 세 번째 방법은 **원소가 하나인 열거 타입**을 선언하는 것이다.
> 

```java
public enum Elvis {
	INSTANCE;

	public void leaveTheBuilding() {
		System.out.println("Whoa baby, I'm outta here!");
	}
}
```

public 필드 방식과 비슷하지만, 더 간결하고, 추가 노력 없이 직렬화할 수 있다.

아주 복잡한 직렬화 상황이나 리플렉션 공격에서도 제 2의 인스턴스가 생기는 일을 완벽히 막아준다.

조금 부자연스러워 보일 수 는 있지만 대부분 상황에서는 **원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.** 단, 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다(열거 타입이 다른 인터페이스를 구현하도록 선언할 수는 있다.)

> 개인적인 통찰)
싱글턴을 만드는 방식은 보통 둘 중 하나다. 두 방식 모두 생성자는 private 으로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 하나 마련해둔다.
> 

```java
public class Elvis {
	
	public static final Elvis INSTANCE = new Elvis();
	
	private Elvis() {}
	
	public void leaveTheBuilding() {
		System.out.println("Whoa baby, I'm outta here!");
	}
}
```

> private 생성자는 public static final 필드인 Elvis.INSTANCE를 초기화할 때 딱 한 번만 호출된다.
public이나 protected 생성자가 없으므로 Elvis 클래스가 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다. 예외는 단 한가지, 권한이 있는 클라이언트는 `AccessibleObject.SetAccessible`을 사용해 private 생성자를 호출할 수 있다.
이러한 공격을 방어하려면 생성자를 수정하여 두 번째 객체가 생성되려 할 때 예외를 던지게 하면 된다.

Enum의 생성자는 묵시적으로 `private`이고, `public static final 객체` 와 같은 방식으로 선언되었기 때문에 비슷한 방식이라고 생각해볼 수 있다.

리플렉션 공격은 어떻게 막는걸까?
> 

![](https://humorous-bass-b9e.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0ec13d04-2f6d-41dd-92ba-7b3cbe77d713%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-08-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_5.53.12.png?table=block&id=183b6bd7-3a1b-4426-8263-b793b1bd620e&spaceId=e738b5df-6beb-459d-a4dd-ce360de05b7b&width=2000&userId=&cache=v2)  

`getDeclaredConstructor` 단계에서 기본 생성자를 찾을 수 없다며 `NoSuchMethodException`이 발생한다.

[Troubleshooting (The Java™ Tutorials >        
            The Reflection API > Arrays and Enumerated Types)](https://docs.oracle.com/javase/tutorial//reflect/special/enumTrouble.html)

Oracle의 Java Tutorial 을 살펴보자.

```java
public enum Charge {

	POSITIVE, NEGATIVE, NEUTRAL;

	Charge() {
		System.out.format("under construction%n");
	}
}
```

```java
public class EnumTrouble {

	public static void main(String[] args) {
		try {
			final Class<Charge> c = Charge.class;
			final Constructor<?>[] constructors = c.getDeclaredConstructors();
			for (Constructor<?> constructor : constructors) {
				System.out.format("Constructor: %s%n",  constructor.toGenericString());
				constructor.setAccessible(true);
				constructor.newInstance();
			}
		} catch (InstantiationException | IllegalAccessException | InvocationTargetException e) {
			e.printStackTrace();
		}
	}
}
```

![](https://humorous-bass-b9e.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F2ddb99d4-fdd6-4d25-bffe-626d5cdc04ff%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-08-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_5.46.00.png?table=block&id=9ebc12ba-041d-4823-8935-41c99511fbb1&spaceId=e738b5df-6beb-459d-a4dd-ce360de05b7b&width=2000&userId=&cache=v2)  

15번 line은 `constructor.newInstance()`이다. 예외 메시지를 보면 ‘enum 객체를 리플렉션으로 생성할 수 없다’라고 적혀있다.

> **열거형 상수를 명시적으로 인스턴스화하려고 시도하면 정의된 열거형 상수가 고유하지 않게 되므로 컴파일 타임 오류가 발생합니다.** 이 제한은 **리플렉티브 코드에서도 적용됩니다**. 기본 생성자를 사용하여 클래스를 인스턴스화하려는 코드는 먼저 `Class.isEnum()`을 호출하여 해당 클래스가 열거형인지 확인해야 합니다.
> 

[Java Singletons Using Enum - DZone](https://dzone.com/articles/java-singletons-using-enum)

모든 열거형은 추상 클래스 Enum의 자손이므로, Enum을 흉내 내어 MyEnum을 작성하면 다음과 같다.

```java
public abstract class MyEnum<T extends MyEnum<T>> implements Comparable<T> {
	
	static int id = 0; // 객체에 붙일 일련번호(0부터 시작)
	
	int ordinal;
	String name = "";
	
	MyEnum(String name) {
		this.name = name;
		ordinal = id++; // 객체를 생성할 때마다 id의 값을 증가 
	}
	
	public int compareTo(T t) {
		return ordinal - t.ordinal;
	}
}
```

`MyEnum<T extends MyEnum<T>>` 와 같이 선언함으로 타입 T가 MyEnum<T> 의 자손임을 알 수 있다.

타입 T가 MyEnum의 자손이므로 ordinal이 정의되어 있는 것은 분명하다.

그리고 추상 메서드를 새로 추가하면, 클래스 앞에도 ‘abstract’를 붙여주어야 하고, 각 static 상수들도 추상 메서드를 구현해주어야 한다.

```java
public abstract class Direction extends MyEnum {

	public static final Direction EAST = new Direction("EAST") {
		@Override
		Point move(final Point p) {
			return new Point();
		}
	};
	
	public static final Direction WEST = new Direction("WEST") {
		@Override
		Point move(final Point p) {
			return new Point();
		}
	};
	
	public static final Direction SOUTH = new Direction("SOUTH") {
		@Override
		Point move(final Point p) {
			return new Point();
		}
	};
	
	public static final Direction NORTH = new Direction("NORTH") {
		@Override
		Point move(final Point p) {
			return new Point();
		}
	};
	
	private String name;

	Direction(final String name) {
		super(name);
		this.name = name;
	}

	abstract Point move(Point p);
}
```

## EnumSet

```java
public abstract class EnumSet<E extends Enum<E>> extends AbstractSet<E>
    implements Cloneable, java.io.Serializable
{
```

`new` 연산자 사용이 불가능하다.(추상 클래스)

다음과 같은 Color 열거형이 있다고 가정하자.

```java
public enum Color {
	RED, YELLOW, GREEN, BLUE, BLACK, WHITE
}
```

먼저 `allOf()` 로 모든 요소를 포함하는 EnumSet을 만들 수 있다.

```java
EnumSet<Color> colorSet = EnumSet.allOf(Color.class);
colorSet.forEach(System.out::println);
```

`noneOf()`를 사용하면 빈 Color 컬렉션을 갖는 EnumSet을 만들 수 있다.

```java
EnumSet<Color> noneColorSet = EnumSet.noneOf(Color.class);
```

of()를 사용하면, **들어갈 요소를 직접 입력하여** EnumSet을 생성할 수 있다.

```java
EnumSet<Color> colorSet = EnumSet.of(Color.RED, Color.YELLOW);
```

of() 메서드의 구현은 상당히 간단해보인다.

> enum 타입의 class 타입을 얻은 후, noneOf로 비어있는 EnumSet을 생성한다.
그 후 비어있는 EnumSet에 argument로 넘어온 Enum들을 더해준다.
> 

재밌는 것은, 고정된 숫자의 요소(최대 열개까지)는 다양한 오버로드 버전으로 지원한다.

![](https://humorous-bass-b9e.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fe71c9139-2f74-4bee-8335-aadd16232c85%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-08-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_8.34.45.png?table=block&id=3f6106a6-c0e1-4bdd-9587-e9f7991d75c7&spaceId=e738b5df-6beb-459d-a4dd-ce360de05b7b&width=2000&userId=&cache=v2)

아래 사진과 같이 다중 요소를 받을 수 있도록 자바 API가 있는데도, 

![](https://humorous-bass-b9e.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff0d72253-394f-4515-a9a8-55be9f91c027%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-08-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_8.35.27.png?table=block&id=306c7e20-b082-408e-9189-0fc20a77d98e&spaceId=e738b5df-6beb-459d-a4dd-ce360de05b7b&width=2000&userId=&cache=v2)

다양한 오버로드 버전을 제공한 이유는 뭘까?

내부적으로 가변 인수 버전은 ******추가 배열을 할당해서 리스트로 감싼다.******

**따라서 배열을 할당하고 초기화하며 나중에 가비지 컬렉션을 하는 비용을 지불해야 한다.**

고정된 숫자의 요소(최대 열개까지)를 API로 정의하므로 이런 비용을 제거할 수 있다.

`List.of, Set.of, Map.of` 에서도 이와 같은 패턴이 등장함을 확인할 수 있다.

`complementOf()` 를 사용하면 원하는 요소를 제거하고 EnumSet을 생성할 수 있다.

```java
EnumSet<Color> colors = EnumSet.complementOf(EnumSet.of(Color.RED, Color.YELLOW));
```

> 이펙티브 자바 아이템 36)
EnumSet의 내부는 비트 벡터로 구현되었다. 원소가 총 64개 이하라면, 즉 대부분의 경우에 EnumSet 전체를 long 변수 하나로 표현하여 비트 필드에 비견되는 성능을 보여준다.

EnumSet 클래스가 비트 필드 수준의 명료함 + 성능 + 열거 타입의 장점을 선사한다.
> 

EnumSet을 사용할 때는 몇 가지 고려할 사항이 있다.

[EnumSet (Java Platform SE 7 )](https://docs.oracle.com/javase/7/docs/api///java/util/EnumSet.html)

1. 열거형 값만 포함할 수 있으며, 모든 값은 동일한 열거형이어야 한다.
2. null을 추가할 수 없다.
3. 스레드에 안전하지 않으므로, 필요한 경우 외부에서 동기화한다. (대부분의 컬렉션 구현과 마찬가지로.) Collections.synchronizedSet(java.util.Set<T>) 메서드를 사용해서 Set을 래핑하는 방법도 있다.
4. HashSet보다 훨씬 빠를 가능성이 있다(보장되지는 않음)
5. 복사본에 fail-safe iterator(장애 발생시 작업을 중단하지 않음) 를 사용하여 컬렉션을 순회할 때, 컬렉션이 수정되어도 ConcurrentModificationException이 발생하지 않는다.

EnumSet은 추상 클래스이며, 인스턴스 생성을 위한 다양한 정적 팩토리 메서드가 정의되어있다. JDK에서는 **RegularEnumSet**, **JumboEnumSet** 2가지의 EnumSet 구현체를 제공한다.

RegularEnumSet은 비트 벡터를 표현하기 위해 `단일 long 자료형`을 사용한다. long의 각 비트는  enum 값을 나타낸다. 열거 형의 i번째 값은 i번째 비트에 저장되므로 값이 있는지 여부를 쉽게 알 수 있다. long이 64 비트의 데이터이기 때문에 RegularEnumSet은 64개의 원소를 저장할 수 있다.

반면에 JumboEnumSet은 `long 요소의 배열`을 비트 벡터로 사용한다. 이를 통해 64개 이상의 원소를 저장한다. RegularEnumSet과 비슷하게 작동 하지만, 저장된 배열 인덱스를 찾기 위해  몇 가지 추가 계산을 수행한다.

이 떄문에 EnumSet 팩터리 메서드는 enum의 원소 수에 따라 구현체를 다르게 선택한다.

`EnumSet.java`의 `noneOf()` 메서드 중

![](https://humorous-bass-b9e.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fb0efbfaa-594f-4d3a-b49c-d6a652b2499a%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-08-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.04.11.png?table=block&id=831dea50-179c-4c82-83b2-f7c0e82cabbe&spaceId=e738b5df-6beb-459d-a4dd-ce360de05b7b&width=2000&userId=&cache=v2)  

```java
EnumSet<Color> colorSet = EnumSet.of(Color.RED, Color.YELLOW);
System.out.println(colorSet.getClass().getName()); // java.util.RegularEnumSet
```
---
# 요약
## Enum이란?
- JDK 1.5부터 생겨난 기능으로 열거체를 정의할 수 있는 클래스  
- 비교 시 실제 값 뿐만 아니라 타입까지 체크 가능  
- 상수값이 재정의 되어도 다시 컴파일 할 필요 X  
- 특정한 변수가 가질 수 있는 값을 제한할 수 있다. Type-Safety를 보장할 수 있다.  
  
## 주요 메서드
- static E values() : 해당 열거체의 모든 상수를 저장한 배열을 생성하여 반환  
- static E valueOf(String name) : 전달된 문자열과 일치하는 해당 열거체의 상수를 반환함  
- String name() : 해당 열거체 상수의 이름 반환  
- int ordinal() : 해당 열거체 상수가 열거체 정의에서 정의된 순서 반환  
  
## Singleton과 Enum
Singleton이란 한 JVM에 하나의 인스턴스만 존재하는 클래스이다.  
멀티 스레드에 의해 클래스의 같은 인스턴스가 재사용된다.  
  
enum이 Singleton이 보장되는 이유는 크게 3가지가 있다.  
  
### 1. clone 미지원
사람들이 일반적으로 clone() 메서드를 이용할 때, 값만 같고 서로 다른 객체가 반환되기를 기대한다.  
enum은 각 인스턴스의 값이 하나씩만 존재해야 하므로 clone을 지원하지 않는다.  
  
### 2. 역직렬화로 인한 중복 인스턴스 생성 방지
enum은 기본적으로 serializable 이 가능하다.  
serializable interface를 구현할 필요 없으므로 역직렬화 시 새로운 객체가 생성될 걱정을 안해도 된다.  
  
### 3. Reflection을 이용한 enum의 인스턴스화를 금지
Enum의 생성자는 Sole Constructor이다.  
컴파일러에서 사용하고 사용자가 직접 호출할 수는 없다.  
따라서 enum -> getConstructor -> newInstance로 사용하는 객체 생성 흐름이 적용되지 않는다.  
(Reflection은 new와 동일하게 클래스 내 생성자로 인스턴스를 사용하기 때문이다.)  
  
# EnumMap
- Enum 타입만 key로 사용 가능한 특별한 Map  
- Array를 이용하기 때문에 성능적으로 우수  
- 해싱 과정이 필요 없어 HashMap보다 빠름  
- null을 key로 넣는 경우 NullPointerException 발생  
- thread-safe하지 않음  
  
## EnumMap 사용 이유 1. 성능이 우수하다
EnumMap은 배열 형태로 이루어져있기 때문에 다른 Map에 비해 성능이 우수하다.  
  
HashMap의 경우 get / put/ remove complexity가 HashMap의 경우에는 O(1)이다.  
하지만 HashMap은 hashCode()를 사용해 키와 값을 저장하므로 해시 충돌 가능성이 존재한다.  
EnumMap은 해시를 사용할 필요가 없으므로 충돌 가능성이 없다.  
  
## 2. key를 제한할 수 있다.
Enum 타입 외에는 key로 만들 수 없다.  
HashMap에 null을 넣을 수 있는 대신, EnumMap에는 Null을 넣으면 NullPointerException이 발생한다.  
