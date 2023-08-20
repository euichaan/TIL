# 제네릭
제네릭이란? **컴파일 타임에 타입을 체크함으로써 코드의 안전성을 높여주는 기능**  
+ **타입을 지정하여 컴파일 시점에 안정성을 보장받는 방법**   
  
```java
List<T> // T는 타입 매개변수
List<String> stringList = new ArrayList<String>(); // 매개변수화 된 타입(String)
```

## 제네릭이 등장하기 이전
제네릭은 JDK 1.5에 등장하였는데, 제네릭이 존재하기 전에 컬렉션의 요소를 출력하는 메서드는 다음과 같이 구현할 수 있었다.  
```java
void printCollection(Collection c) {
	Iterator iterator = c.iterator();
	for (int i = 0; i < c.size(); i++) {
		System.out.println(iterator.next());
	}
}
```
하지만 위와 같이 컬렉션의 요소들을 다루는 메서드들은 **타입이 보장되지 않기** 때문에 문제가 발생하곤 했다.  
예를 들어 컬렉션이 갖는 요소들의 합을 구하는 메서드를 구현했다고 하자.  
```java
int sum(Collection c) {
  int sum = 0;
  Iterator iterator = c.iterator();
  for (int i = 0; i < c.size(); i++) {
    sum += Integer.parseInt(iterator.next());
  }
  return sum;
}
```
문제는 위와 같은 메서드가 String처럼 다른 타입을 갖는 컬렉션도 호출이 가능하다는 점이다. String 타입을 갖는 컬렉션은 컴파일 시점에 문제가 없다가 **런타임 시점에 메서드를 호출하면 에러가 발생하였다.** 그래서 Java 개발자들은 **타입을 지정하여 컴파일 시점에 안정성을 보장**받을 수 있는 방법을 고안하였고, 그렇게 제네릭이 등장하게 되었다.  
  
## 제네릭의 등장
제네릭이 등장하면서 컬렉션에 타입을 지정할 수 있게 되었고, 위의 메서드를 다음과 같이 수정할 수 있게 되었다.  
```java
void sum(Collection<Integer> c) {
  int sum = 0;
  for (Integer e : c) {
    sum += e;
  }
  return sum;
}
```
이제는 다른 타입을 갖는 컬렉션이 위와 같은 메소드를 호출하려고 하면 컴파일 에러를 통해 안정성을 보장 받을 수 있게 되었다. 하지만 제네릭이 불공변이기 때문에 또 다른 문제가 발생하였는데, printCollection처럼 모든 타입에서 공통적으로 사용되는 메소드를 만들 방법이 없는 것이다.  
  
printCollection의 타입을 Integer에서 Object로 변경하여도 제네릭이 불공변이기 때문에 Collection<Object>는 Collection<Integer>의 하위타입이 아니므로 컴파일 에러가 발생하는 것이다. 이러한 상황은 오히려 제네릭이 등장하기 이전보다 실용성이 떨어졌기 때문에, `와일드카드`라는 타입이 추가되었다.  
  
```java
@Test
void genericTest() {
  List<Integer> list = Arrays.asList(1, 2, 3);
  printCollection(list);   // 컴파일 에러
}

void printCollection(Collection<Object> c) { // 제네릭은 불공변이다
  for (Object e : c) {
    System.out.println(e);
  }
}
```
## 와일드카드의 등장
모든 타입을 대신할 수 있는 와일드카드 타입(<?>)을 추가하였다. 와일드카드는 정해지지 않은 **unknown type**이기 때문에 Collection<?>로 선언함으로써 모든 타입에 대해 호출이 가능해졌다.  
```java
void printCollection(Collection<?> c) { 
  for (Object e : c) {
    System.out.println(e);
  }
}
```
와일드카드로 선언된 타입은 **unknown type**이기 때문에 다음과 같은 경우에 문제가 발생하였다.  
```java
@Test
void genericTest() {
  Collection<?> c = new ArrayList<String>();
  c.add(new Object()); // 컴파일 에러
}
```
컬렉션의 add로 값을 추가하려면 제네릭 타입인 E 또는 E의 자식을 넣어줘야 한다. 그런데 와일드카드는 unknown type이므로 Integer, String 또는 개발자가 추가한 클래스까지 될 수 있기 때문에 범위가 무제한이다. 와일드카드의 경우 add로 넘겨주는 파라미터가 unknown 타입의 자식이여야 하는데, 정해지지 않았으므로 어떠한 타입을 대표하는지 알 수 없어서 자식 여부를 검사할 수 없는 것이다.  
  
반면에 get으로 값을 꺼내는 작업은 와일드카드로 선언되어 있어도 문제가 없다. 왜냐하면 값을 꺼낸 결과가 unknown 타입이여도 우리는 해당 타입이 어떤 타입의 자식인지 확인이 필요하지 않으며, 심지어 적어도 Object의 타입임을 보장할 수 있기 때문이다.  
  
결국 이러한 상황이 생기는 것은 결국 와일드카드가 any 타입이 아닌 unknown 타입이기 때문이다.  
  
## 제네릭은 왜 사용할까?
### 1. 컴파일 타임에 강력한 타입 검사
`제네릭 미사용`  
```java
List stringList = new ArrayList();
stringList.add("javalivestudy");
stringList.add(1);
String result = (String) stringList.get(0) + (String) stringList.get(1); // ClassCastException(RuntimeException)
```
실행을 시켜 꺼낼 때 String 타입으로 캐스팅 할 시 `ClassCastException`(언체크 예외, 런타임 예외)가 발생한다.  
  
`제네릭 사용`  
<img width="416" alt="스크린샷 2023-08-20 오후 12 49 24" src="https://github.com/euichaan/TIL/assets/98090620/1c8e3d24-57ee-40b7-bb46-443a0dd6a547">  
  
List의 타입 매개변수를 String으로 지정하면 String 타입을 넣어야 한다는 것을 컴파일러가 알게 된다.  
  
### 2. 캐스팅(타입 변환 제거)
`제네릭 미사용`  
```java
List stringList = new ArrayList();
stringList.add("javalivestudy");
String result = (String) stringList.get(0);
```
제네릭이 없는 경우에는 리스트에 어떤 타입이 들어있는지 모르기 때문에 꺼낼 때 캐스팅이 필요하다.  
매번 타입 변환을 하면 프로그램 성능에 안좋은 영향을 끼칠 수 있다.  
  
`제네릭 사용`  
```java
List<String> stringList = new ArrayList<>();
stringList.add("javalivestudy");
String result = stringList.get(0);
```
타입 변환을 제거할 수 있다. List에 저장되는 요소를 String 타입으로 제한했기 때문에 캐스팅이 필요하지 않다.  
  
  
## 제네릭은 불공변(무공변, invariant)이다.
`List<Object> objectList = new ArrayList<Integer>();` 는 가능할까?  
  
불가능하다.  
```java
Object[] objectArray = new Integer[1];

List<Object> objectList = new ArrayList<Integer>(); //Compile Error!
```
배열은 Integer가 Object의 하위 타입이면 Integer 배열도 Object 배열의 하위 타입이 성립된다.  
반면에 제네릭에서는 Integer가 Object의 하위 타입이더라도 List<Object>와 ArrayList<Integer>는 아무런 관계가 없다.  
따라서 둘은 서로 다른 타입 매개변수이기 때문에 컴파일 오류를 발생시킨다.  
  
배열과 같은 특징을 공변, 제네릭과 같은 특징을 무공변(불공변)이라 한다.  
  
## 변성(variance)
타입 계층 관계에서 서로 다른 타입간에 어떤 관계가 있는지를 나타내는 개념  
  
### 1. 무공변(Invariance), 불공변
타입 B가 타입 A의 하위 타입일 때, `T<B>`가 `T<A>`의 하위 타입이 아닌 경우. 즉, 아무런 관계가 없다.  
### 2. 공변(Covariance) - <? extends T>
타입 B가 타입 A의 하위 타입일 때, `T<B>`가 `T<A>`의 하위 타입인 경우.  
### 3. 반공변(Contravariance) - <? super T>
타입 B가 타입 A의 하위 타입일 때, `T<B>`가 `T<A>`의 상위 타입인 경우.  
  
## 제네릭 타입(Generic Types)
타입을 파라미터로 가지는 클래스와 인터페이스  
```java
public class Category { // 비제네릭 
	
	private Object object;
	
	public void set(Object object) {
		this.object = object;
	}
	
	public Object get() {
		return object;
	}
}
```

```java
public class Category<T> { // 제네릭 

	private T t;
	
	public void set(T t) {
		this.t = t;
	}
	
	public T get() {
		return t;
	}
}
```
## 제네릭 메서드(Generic Methods)
메서드의 선언부에 제네릭 타입이 선언된 형식. **타입 매개변수의 범위가 메서드 내로 한정된다.**  
```java
public class NoodleCategory<T> {

	private T t;

	public void set(T t) {
		this.t = t;
	}

	public T get() {
		return t;
	}

	public <T> void printClassName(T t) {
		System.out.println("클래스 필드에 정의된 타입 = " + this.t.getClass().getName());
		System.out.println("제네릭 메서드에 정의된 타입 = " + t.getClass().getName());
	}

	public static void main(String[] args) {
		NoodleCategory<Noodle> noodleNoodleCategory = new NoodleCategory<>();
		noodleNoodleCategory.set(new Noodle());
		noodleNoodleCategory.printClassName(new Pasta());
	}
}
```
출력 결과는 다음과 같다.  
<img width="416" alt="스크린샷 2023-08-20 오후 12 49 24" src="https://github.com/TEAM-cafe-in/cafe-in-be/assets/98090620/3ab9dc9c-a924-4aa1-9114-b0f611de9716">  
  
NoodleCategory<T>의 타입 매개변수 T와 제네릭 메서드에 해당하는 printClassName의 타입 매개변수 T는 서로 다른 것을 알 수 있다.  
## 제네릭 타입 제한
```java
public class NoodleCategory<T extends Noodle> {

	private T t;

	public void set(T t) {
		this.t = t;
	}

	public T get() {
		return t;
	}

	public static void main(String[] args) {
		NoodleCategory<Noodle> noodleCategory = new NoodleCategory<>();
		NoodleCategory<Ramen> ramenNoodleCategory = new NoodleCategory<>();
		NoodleCategory<Coke> cokeNoodleCategory = new NoodleCategory<>(); // Compile Error!
	}
}

class Ramen extends Noodle {}

class Coke {}
```
`T extends Noodle`과 같이 선언했을 때, Noodle을 상속한 타입만 올 수 있다.  
Noodle과 Noodle을 상속한 타입들만 올 수 있도록 제한된다. 이를 위쪽에 한계를 뒀다는 의미로 `상한 경계`라고 한다.  
  
## 와일드카드
### 1. <?> Unbounded Wildcards
모든 타입이 가능
### 2. <? extends Noodle> Upper Bounded Wildcards 상한 경계
Noodle과 Noodle의 하위 타입. 공변의 기능을 수행할 수 있게 한다.  
### 3. <? super Noodle> Lower Bounded Wildcards 하한 경계
Noodle과 Noodle의 상위 타입. 반공변의 기능을 수행할 수 있게 한다.  
  
상황을 이해하기 위해 다음과 같이 3가지 클래스가 존재한다고 가정하고 살펴보자.  
```java
class MyGrandParent {

}

class MyParent extends MyGrandParent {

}

class MyChild extends MyParent {

}
```
### 상한 경계 와일드카드
상한 경계 와일드카드는 와일드카드 타입에 extends를 사용해서 와일드카드 타입의 최상위 타입을 정의함으로써 상한 경계를 설정한다.  
예를 들어 다음과 같이 매개변수를 출력하는 메서드에 MyParent 라고 상한 경계를 주었다고 하자. MyChild 타입으로 꺼내는 경우에는 컴파일 에러가 발생하고, 나머지 타입으로 꺼내는 것은 가능하다.  
```java
void printCollection(Collection<? extends MyParent> c) {
    // 컴파일 에러
    for (MyChild e : c) {
        System.out.println(e);
    }

    for (MyParent e : c) {
        System.out.println(e);
    }

    for (MyGrandParent e : c) {
        System.out.println(e);
    }

    for (Object e : c) {
        System.out.println(e);
    }
}
```
`<? extends MyParent>`로 가능한 타입은 MyParent와 unknown 모든 MyParent 자식 클래스들이다. 미지의 MyParent 자식 클래스라는 것은 자식이 어떤 타입인지 알 수 없다는 것으로, 그 타입이 MyChild일 수도 있지만, 아닐 수도 있다. 예를 들어 또 다른 MyParent의 자식인 AnotherChild 라는 클래스가 있다고 하자.  
```java
class AnotherChild extends MyParent {

}
```
`<? extends MyParent>` 타입으로는 MyChild와 AnotherChild (또는 그 외의 타입)이 될 수도 있다. 컬렉션 c에서 꺼내서 만들어지는 객체(produce)가 반드시 MyChild 타입이 아닌 AnotherChild가 될 수도 있다. 그렇기 때문에 MyChild 타입으로 꺼내려고 시도하면 컴파일 에러가 발생한다. **하지만 적어도 MyParent 임은 확실하므로 MyParent와 그 부모 타입으로 꺼내는 것은 문제가 없다.**  
  
갖고 있는 원소를 사용 또는 소모(consume)하여 컬렉션에 추가하는 경우에는 상황이 달라진다. 다음과 같이 원소를 추가하는 코드는 모든 타입에 대해 컴파일 에러가 발생한다.  
```java
void addElement(Collection<? extends MyParent> c) {
		c.add(new MyChild());
		c.add(new MyParent());
		c.add(new MyGrandParent());
		c.add(new Object()); // 전부 컴파일 에러
}
```
왜냐하면 컬렉션의 타입인 `<? extends MyParent>` 으로 가능한 타입은 MyParent와 미지(unknown)의 모든 MyParent 자식 클래스들이므로, 우리는 c가 **MyParent의 하위 타입 중에서 어떤 타입인지 모르기 때문이다.** 먼저 하위 타입으로는 MyChild가 될 수도 있지만, AnotherChild와 같은 또 다른 하위 타입이 될 수도 있으므로 하위 타입을 결정할 수 없다. 또한 MyGrandParent와 같이 상위 타입은 적어도 MyParent 타입은 절대 아니므로, 상위 타입 역시 원소를 사용 또는 소모(consume)하는 경우는 불가능하다.  
  
원소를 소모하는 경우에는 상한 경계가 아닌 하한 경계를 지정하여 최소한 MyParent 타입 임을 보장하면 문제를 해결할 수 있다.  
  
### 하한 경계 와일드카드
상한 경계와 반대로 super를 사용해 와일드카드의 최하위 타입을 정의하여 하한 경계를 설정할 수도 있는데, 이를 하한 경계 와일드카드(Lower Bounded Wildcard)라고 한다. 예를 들어 `<? super Myparent>`으로 가능한 타입은 MyParent와 미지의 MyParent 부모 타입들이다. 갖고 있는 원소를 사용하여(consume) 컬렉션에 추가하는 경우를 살펴보도록 하자.  
```java
void addElement(Collection<? super MyParent> c) {
	c.add(new MyChild());
	c.add(new MyParent());
	c.add(new MyGrandParent()); // 컴파일 에러
	c.add(new Object()); // 컴파일 에러
}
```
컬렉션 C가 갖는 타입은 적어도 MyParent의 부모 타입들이다. 그러므로 해당 컬렉션에는 MyParent의 자식 타입이라면 안전하게 컬렉션에 추가할 수 있고, 부모 타입인 경우에만 컴파일 에러가 발생할 것이다.  
  
하지만 상한 경계와 반대로 컬렉션에서 값을 꺼내서 원소를 만드는(produce) 경우에는 상황이 다르다.  
```java
	void printCollection(Collection<? super MyParent> c) {
		// 불가능(컴파일 에러)
		for (MyChild e : c) {
			System.out.println(e);
		}

		// 불가능(컴파일 에러)
		for (MyParent e : c) {
			System.out.println(e);
		}

		// 불가능(컴파일 에러)
		for (MyGrandParent e : c) {
			System.out.println(e);
		}

		for (Object e : c) {
			System.out.println(e);
		}
	}
```
우선 상한 타입부터 살펴보도록 하자. `<? super MyParent>`으로 가능한 타입은 MyParent와 미지의 MyParent 부모 타입들이므로, 부모 타입을 특정할 수 없어 모든 부모 타입들에 제약(컴파일 에러)이 발생한다. Object 같은 경우에는 Java에서 지원하는 모든 객체의 부모임이 명확하므로, 특별히 Object 타입의 객체로 원소를 만드는(produce) 경우에는 컴파일 에러가 발생하지 않는다.  
  
하위 타입인 경우에도 문제가 되는데, `<? super MyParent>`으로 가능한 타입은 MyParent와 미지의 MyParent 부모 타입들이므로 MyChild와 같이 경계 아래의 하위 타입들은 당연히 추가될 수 없기 때문이다.  
## 와일드카드 - 상한, 하한 제한 언제 무엇을 써야할까? 
이펙티브 자바 아이템 31, "PECS 공식을 기억하자"  
즉, 생산자(producer)는 extends를 소비자(consumer)는 super를 사용한다. Comparable과 Comparator는 모두 소비자라는 사실도 잊지 말자.  

<img width="696" alt="스크린샷 2023-08-20 오후 7 49 01" src="https://github.com/TEAM-cafe-in/cafe-in-be/assets/98090620/b6c30424-623d-4b87-aa8e-63c705c62330">
  
<img width="695" alt="스크린샷 2023-08-20 오후 7 49 49" src="https://github.com/TEAM-cafe-in/cafe-in-be/assets/98090620/6d9bed28-bc2e-4670-b6e0-1ff017b8ca40"> 

## 제네릭 타입 소거
제네릭은 컴파일 타임에 타입을 검사하고, 런타임에는 타입을 소거해서 해당 정보를 알 수 없다.  
자바 5에서 등장했기 때문에 기존 코드와의 호환성을 위해 `타입 소거`라는 개념을 도입했다.  
  
1. 타입 매개변수의 경계가 없는 경우에는 Object로, 경계가 있는 경우에는 경계 타입으로 타입 파라미터를 변경  
2. 타입 안전성을 유지하기 위해, 필요한 경우 타입 변환 추가  
3. 제네릭 타입을 상속받은 클래스의 다형성을 유지하기 위해 Bridge method 생성  
  