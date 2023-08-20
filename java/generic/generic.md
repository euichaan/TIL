# 제네릭
제네릭이란? **컴파일 타임에 타입을 체크함으로써 코드의 안전성을 높여주는 기능**    
  
```java
List<T> // T는 타입 매개변수
List<String> stringList = new ArrayList<String>(); // 매개변수화 된 타입(String)
```
  
## 제네릭은 왜 사용할까?
### 1. 컴파일 타임에 강력한 타입 검사
**제네릭 미사용**  
```java
List stringList = new ArrayList();
stringList.add("javalivestudy");
stringList.add(1);
String result = (String) stringList.get(0) + (String) stringList.get(1); // ClassCastException(RuntimeException)
```
실행을 시켜 꺼낼 때 String 타입으로 캐스팅 할 시 `ClassCastException`(언체크 예외, 런타임 예외)가 발생한다.  
  
**제네릭 사용**  
<img width="416" alt="스크린샷 2023-08-20 오후 12 49 24" src="https://github.com/euichaan/TIL/assets/98090620/1c8e3d24-57ee-40b7-bb46-443a0dd6a547">
  
List의 타입 매개변수를 String으로 지정하면 String 타입을 넣어야 한다는 것을 컴파일러가 알게 된다.  
  
### 2. 캐스팅(타입 변환 제거)
**제네릭 미사용**  
```java
List stringList = new ArrayList();
stringList.add("javalivestudy");
String result = (String) stringList.get(0);
```
제네릭이 없는 경우에는 리스트에 어떤 타입이 들어있는지 모르기 때문에 꺼낼 때 캐스팅이 필요하다.  
매번 타입 변환을 하면 프로그램 성능에 안좋은 영향을 끼칠 수 있다.  
**제네릭 사용**
```java
List<String> stringList = new ArrayList<>();
stringList.add("javalivestudy");
String result = stringList.get(0);
```
타입 변환을 제거할 수 있다. List에 저장되는 요소를 String 타입으로 제한했기 때문에 캐스팅이 필요하지 않다.  
  
## List<Object> objectList = new ArrayList<Integer>(); ?
불가능하다.  
```java
Object[] objectArray = new Integer[1];

List<Object> objectList = new ArrayList<Integer>(); //Compile Error!
```
배열은 Integer가 Object의 하위 타입이면 Integer 배열도 Object 배열의 하위 타입이 성립된다.  
반면에 제네릭에서는 Integer가 Object의 하위 타입이더라도 List<Object>와 ArrayList<Integer>는 아무런 관계가 없다.  
따라서 둘은 서로 다른 타입 매개변수이기 때문에 컴파일 오류를 발생시킨다.  
  
배열과 같은 특징을 공변, 제네릭과 같은 특징을 무공변이라 한다.  
  
## 변성(variance)
타입 계층 관계에서 서로 다른 타입간에 어떤 관계가 있는지를 나타내는 개념  
  
### 무공변(Invariance), 불공변 - <T>
타입 B가 타입 A의 하위 타입일 때, T<B>가 T<A>의 하위 타입이 아닌 경우. 즉, 아무런 관계가 없다.  
### 공변(Covariance) - <? extends T>
타입 B가 타입 A의 하위 타입일 때, T<B>가 T<A>의 하위 타입인 경우.  
### 반공변(Contravariance) - <? super T>
타입 B가 타입 A의 하위 타입일 때, T<B>가 T<A>의 상위 타입인 경우.  
  
## 제네릭 타입(Generic Types)
타입을 파라미터로 가지는 클래스와 인터페이스  
```java
// 비제네릭
public class Category {
	
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
//제네릭
public class Category<T> {

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

<img width="1271" alt="스크린샷 2023-08-20 오후 7 42 43" src="https://github.com/TEAM-cafe-in/cafe-in-be/assets/98090620/87420778-2a18-4dff-bea5-e1bb794a9a82">
  
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
  