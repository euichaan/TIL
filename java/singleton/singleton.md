# Java Singletons Using Enum
[Java Singletons Using Enum](https://dzone.com/articles/java-singletons-using-enum) 을 번역한 글  
  
싱글톤은 JVM당 인스턴스가 하나만 있어야 하는 클래스입니다. 싱글톤 클래스의 동일한 인스턴스가 여러 스레드에서 재사용됩니다.  
## Traditional Method of Making Singletons
### Method 1 : Singleton With Public Static Final Field
```java
public class Singleton {
	
	public static final Singleton INSTANCE = new Singleton();
	
	private Singleton() {}
}
```
### Method 2: Singleton With Public Static Factory Method
```java
public class Singleton {

	private static final Singleton INSTANCE = new Singleton();

	private Singleton() {}

	public static Singleton getInstance() {
		return INSTANCE;
	}
}
```
### Method 3: Singleton With Lazy Initialization
```java
public class Singleton {

	private static Singleton INSTANCE = null;

	private Singleton() {}

	public static Singleton getInstance() {
		if (INSTANCE == null) {
			synchronized (Singleton.class) {
				if (INSTANCE == null) {
					INSTANCE = new Singleton();
				}
			}
		}
		return INSTANCE;
	}
}
```
# Pros and Cons of the Above Methods, 위 방법의 장단점 
위의 모든 방법은 private constructor를 사용해서 non-insatiability (인스턴스를 만들 수 없음)을 강제합니다.  
여기에서 우리는 내부에서 할 일이 없더라도 비공개 생성자를 생성하는 것을 피할 수 없습니다.  
비공개 생성자를 만들지 않으면, 클래스와 동일한 접근 제어자를 가진 암시적 매개변수 없는 기본 생성자가 생성되기 때문입니다.  
  
[Oracle Default Constructor](https://docs.oracle.com/javase/specs/jls/se7/html/jls-8.html#jls-8.8.9)  
  
위의 방법을 비교하면 처음 두 방법은 성능 차이가 전혀 없습니다. 방법 1이 더 명확하고 간단합니다.  
방법 2의 사소한 장점은 나중에 API를 변경하지 않고도 클래스를 비싱글톤으로 만들 수 있다는 것입니다.  
다음과 같이 동일한 인스턴스를 반환하는 대신 호출할 때마다 새 인스턴스를 생성하도록 팩토리 메서드의 구현을 변경하여 이를 수행할 수 있습니다.  
```java
public static Singleton getInstance() {
	return new Singleton();
}
```
static field는 클래스 로딩 시 초기화됩니다. 따라서 방법 1과 2 모두에서 싱글톤 인스턴스는 런타임에 사용되지 않는 경우에도 생성됩니다.  
싱글톤 객체가 너무 크지 않고 인스턴스 생성 비용이 너무 많이 들지 않는다면 이는 문제가 되지 않습니다.  
  
방법 3은 지연 초기화를 통해 이 문제를 방지합니다. 방법 3에서는 싱글톤 객체에 처음 액세스할 때 인스턴스가 생성됩니다.  
세분화된 동기화는 여러 동시 스레드로 하나 이상의 개체가 생성되지 않도록 하는 데 사용됩니다.  
  
위의 모든 방법은 싱글톤 클래스로 `직렬화` 및 `역직렬화`를 수행하지 않는 한 잘 작동합니다.  
위의 메서드에서 싱글톤 동작을 어떻게 구현했을까요? 생성자를 비공개로 만들고 클래스의 새 인스턴스를 생성할 때 생성자에 접근할 수 없게 만들었습니다.  
하지만 생성자 외에 클래스의 인스턴스를 생성할 수 있는 다른 방법은 없을까요? 대답은 '아니요'입니다. 몇 가지 다른 고급 방법이 있습니다.  
1. Serialization and deserialization  
2. Reflection  
  
# Problems With Serialization and Deserialization
위의 싱글톤 클래스를 직렬화하려면 해당 클래스를 직렬화 가능한 인터페이스로 구현해야 합니다. (`Serializable` 인터페이스를 구현해야 합니다.)  
하지만 그것만으로는 충분하지 않습니다. **클래스를 역직렬화하면 새로운 인스턴스가 생성됩니다.** 이제 생성자가 비공개인지 여부는 중요하지 않습니다.  
이제 JVM 내부에 동일한 싱글톤 클래스의 인스턴스가 두 개 이상 존재할 수 있으며, 이는 싱글톤 속성을 위반하는 것입니다.  
```java
public class SerializeDemo implements Serializable {

	public static void main(String[] args) {
		Singleton singleton = Singleton.INSTANCE;
		singleton.setValue(1);

		// Serialize
		try {
			final FileOutputStream fileOut = new FileOutputStream("out.ser");
			final ObjectOutputStream out = new ObjectOutputStream(fileOut);
			out.writeObject(singleton);
			out.close();
			fileOut.close();
		} catch (IOException e) {
			e.printStackTrace();
		}

		singleton.setValue(2);

		// Deserialize
		Singleton singleton2 = null;
		try {
			FileInputStream fileIn = new FileInputStream("out.ser");
			ObjectInputStream in = new ObjectInputStream(fileIn);
			singleton2 = (Singleton) in.readObject();
			in.close();
			fileIn.close();
		} catch (IOException | ClassNotFoundException e) {
			e.printStackTrace();
		}
		
		if (singleton == singleton2) {
			System.out.println("Two objects are same");
		} else {
			System.out.println("Two objects are not same");
		}

		System.out.println(singleton.getValue()); // 2
		System.out.println(singleton2.getValue());// 1
	}
}
```
Output:  
<img width="724" alt="스크린샷 2023-08-12 오후 1 23 31" src="https://github.com/euichaan/TIL/assets/98090620/0e14ce1d-1cc0-4ff9-b0c1-5002be730f9f">  
  
여기서 singleton과 singleton2는 필드 변수로 두 개의 다른 값을 가진 두 개의 다른 인스턴스입니다.  
이는 싱글톤 속성을 위반하는 것입니다. 해결책은 역직렬화된 객체를 호출자에게 반환하기 전에 준비할 때 호출하는 `readResolve` 메서드를 구현해야 한다는 것입니다. 해결책은 다음과 같습니다.  
```java
public class Singleton implements Serializable{

    public static final Singleton INSTANCE = new Singleton();

    private Singleton() {
    }

    protected Object readResolve() {
        return INSTANCE;
    }

}
```
Output:  
<img width="733" alt="스크린샷 2023-08-12 오후 1 28 45" src="https://github.com/euichaan/TIL/assets/98090620/4f555ecf-b9a8-4890-ab80-cbd263f04524">  
  
이제 싱글톤 속성이 보존되고 JVM 내에 싱글톤 클래스의 인스턴스가 하나만 존재합니다.  
  
# Problems With Reflection
advanced user는 리플렉션을 사용하여 생성자의 private access modifier를 런타임 시 원하는 모든 것으로 변경할 수 있습니다. **이런 일이 발생하면 인스턴스화 불가능성에 대한 유일한 매커니즘이 중단됩니다.** 이것이 어떻게 가능한지 살펴보겠습니다.  
```java
public class ReflectionDemo {

	public static void main(String[] args) throws Exception {
		Singleton singleton = Singleton.INSTANCE;

		Constructor<? extends Singleton> constructor = singleton.getClass()
			.getDeclaredConstructor(new Class[0]);
		constructor.setAccessible(true);

		Singleton singleton2 = constructor.newInstance();

		if (singleton == singleton2) {
			System.out.println("Two objects are same");
		} else {
			System.out.println("Two objects are not same");
		}

		singleton.setValue(1);
		singleton2.setValue(2);

		System.out.println(singleton.getValue()); // 1
		System.out.println(singleton2.getValue()); // 2
	}
}
```
Output:  
<img width="752" alt="스크린샷 2023-08-12 오후 1 36 15" src="https://github.com/euichaan/TIL/assets/98090620/bd1487dd-47ff-4169-bb59-56407e4837fb">  

이러한 방식으로 액세스할 수 없는 private constructor에 액세스할 수 있게 되고 클래스를 싱글톤으로 만들려는 전체 아이디어가 중단됩니다.  
  
# Making Singletons With Enum in Java
위의 모든 문제는 enum type을 사용하여 싱글톤을 만들면 매우 쉽게 해결할 수 있습니다.  
```java
public enum Singleton {
	INSTANCE;
}
```
위의 세 줄은 앞서 설명한 문제 없이 싱글톤을 만듭니다. enum은 본질적으로 직렬화 가능하므로 `Serializable` 인터페이스를 구현할 필요가 없습니다. 리플렉션 문제도 없습니다. 따라서 싱글톤의 인스턴스가 JVM 내에 하나만 존재한다는 것이 100% 보장됩니다. 따라서 이 방법은 Java에서 싱글톤을 만드는 가장 좋은 방법으로 권장됩니다.  
```java
public enum SingletonEnum {
	INSTANCE;

	private int value;

	public void setValue(final int value) {
		this.value = value;
	}

	public int getValue() {
		return this.value;
	}
}
```
다음과 같이 사용할 수 있습니다.  
```java
public class EnumDemo {

	public static void main(String[] args) {
		SingletonEnum singleton = SingletonEnum.INSTANCE;

		System.out.println(singleton.getValue());
		singleton.setValue(2);
		System.out.println(singleton.getValue());
	}
}
```
여기서 한 가지 기억해야 할 점은 열거형을 직렬화할 때 **필드 변수는 직렬화되지 않는다는 것입니다.**  
예를 들어 SingletonEnum 클래스를 직렬화했다가 역직렬화하면 int 값 필드의 값이 손실됩니다.  
[Serialization of Enum Constants](https://docs.oracle.com/javase/6/docs/platform/serialization/spec/serial-arch.html#6469)  
열거형 상수의 직렬화된 형식은 그 이름으로만 구성되며, `상수의 필드 값`은 형식에 존재하지 않습니다.  
