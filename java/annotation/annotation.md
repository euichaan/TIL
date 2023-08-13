# 애노테이션
`JDK 1.5`부터 추가  
프로그램의 소스코드 안에 다른 프로그램을 위한 정보를 미리 약속된 형식으로 포함시킨 것이 바로 애노테이션이다.  
애노테이션은 주석(comment)처럼 프로그래밍 언어에 영향을 미치지 않으면서도 다른 프로그램에게 유용한 정보를 제공할 수 있다는 장점이 있다.  
  
JDK에서 제공하는 표준 애노테이션은 주로 컴파일러를 위한 것으로 컴파일러에게 유용한 정보를 제공한다.  
  
## 표준 애노테이션
다음 설명은 `오라클 Java API 스펙, 자바의 정석`을 참고.  
### @Override
```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}
```
`Target`을 보면 메서드 앞에만 붙일 수 있다는 것을 알 수 있다.  
메서드 선언이 subtype(상위 유형)의 메서드 선언을 override(재정의)하기 위한 것임을 나타낸다.  
메서드가 이 주석 유형으로 주석이 달린 경우 컴파일러는 다음 조건 중 하나 이상이 유지되지 않는 한 오류 메시지를 생성해야 한다.  
- 메서드는 상위 유형에서 선언된 메서드를 대체하거나 구현한다.  
- 메서드에는 Object에 선언된 public method의 signature와 재정의되는 signature가 있다.  
  
1번 경우와 같이 상위 유형에서 선언된 메서드가 아닌데 `@Override`를 붙이면 컴파일 타임 에러가 발생한다.  
<img width="397" alt="스크린샷 2023-08-13 오후 12 45 19" src="https://github.com/euichaan/TIL/assets/98090620/fc2bf7d5-db38-474e-a5b9-68677baa2e38">  
  
2번 경우와 같이 Object의 public method의 signature와 재정의되는 signature라면 `@Override`를 붙일 수 있다.  
<img width="417" alt="스크린샷 2023-08-13 오후 12 43 09" src="https://github.com/euichaan/TIL/assets/98090620/f3d95714-48eb-4a22-a3f4-d43183087613">  
### @Deprecated
@Deprecated 주석이 달린 프로그램 요소는 프로그래머가 사용하지 않도록 권장하는 요소이다. 요소(element)는 여러 가지 이유로 더 이상 사용되지 않을 수 있다.  
- 예를 들어 사용 시 오류가 발생할 수 있다.     
- 호환되지 않게 변경되거나 향후 버전에서 제거될 수 있다.  
- 더 새롭고 일반적으로 선호되는 대안으로 대체되었다.  
- 구식이다.  
  
컴파일러는 더 이상 사용되지 않는 프로그램 요소가(deprecated program element) 사용되거나 non-deprecated code에서 재정의될 때 경고를 발행한다.  
지역 변수 선언이나 매개변수 선언 또는 패키지 선언에서 @Deprecated 주석을 사용해도 컴파일러에서 발생하는 경고에는 영향을 미치지 않는다.  
  
@Deprecated가 붙은 대상을 사용하지 않도록 권할 뿐 강제성은 없다.  

### @FunctionalInterface
`JDK 1.8`에 추가  
다음은 이펙티브 자바 아이템 44, '표준 함수형 인터페이스를 사용하라'에 나온 글이다.  
```
이 애너테이션을 사용하는 이유는 @Override를 사용하는 이유와 비슷하다. 프로그래머의 의도를 명시하는 것으로, 크게 세 가지 이유가 있다.
첫 번째, 해당 클래스의 코드나 설명 문서를 읽을 이에게 그 인터페이스가 람다용으로 설계된 것임을 알려준다.
두 번째, 해당 인터페이스가 추상 메서드를 오직 하나만 가지고 있어야 컴파일되게 해준다.
세 번째, 그 결과 유지보수 과정에서 누군가 실수로 메서드를 추가하지 못하게 막아준다.
```
인터페이스 유형 선언이 JLS에 정의된 대로 funtional interface가 되도록 의도되었음을 나타내는 데 사용되는 유익한 annotation type이다.  
개념적으로, funtional interface에는 정확히 하나의 추상 메서드가 있다. default method에는 구현이 있으므로 추상 메서드가 아니다.  
인터페이스가 java.lang.Object의 공용 메서드 중 하나를 재정의하는 추상 메서드를 선언하는 경우 인터페이스의 모든 구현이 java.lang.Object 또는 다른 곳에서 구현되기 때문에 인터페이스의 추상 메서드 수에 포함되지 않는다.  
  
이런 경우는 가능하다.  
<img width="381" alt="스크린샷 2023-08-13 오후 1 03 14" src="https://github.com/euichaan/TIL/assets/98090620/22a7f6d8-fc51-4159-ade9-e5fd747f68cf">  
  
funtional interface의 인스턴스는 람다 식, 메서드 참조 또는 생성자 참조를 사용하여 만들 수 있다.  
컴파일러는 인터페이스 선언에 **FuntionalInterface 주석이 있는지 여부에 관계없이** funtional interface의 정의를 충족하는 모든 인터페이스를 funtional interface로 처리한다.  
### @SuppressWarnings
주석이 달린 요소(및 주석이 달린 요소에 포함된 모든 프로그램 요소)에서 명명된 컴파일러 경고가 억제되어야 함을 나타낸다.  
지정된 요소에서 억제된 경고 집합은 포함하는 모든 요소에서 억제된 경고의 상위 집합이다.  
**예를 들어 클래스에서 주석을 달아 하나의 경고를 억제하고 메서드에 주석을 달아 다른 경고를 억제하면 메서드에서 두 경고가 모두 억제된다.**  

### @SafeVarargs
컴파일 후에 타입이 유지되는, 제거되지 않는 타입을 refiable 타입, 제거되는 타입을 non-refiable 타입이라고 한다.  
**메서드에 선언된 가변인자 타입이 non-refiable 타입인 경우, 해당 메서드를 선언하는 부분과 호출하는 부분에서 "unchecked" 경고가 발생한다.**  
해당 코드에 문제가 없다면 이 경고를 억제하기 위해 @SafeVarargs를 사용해야 한다.  

이 애노테이션은 static이나 final이 붙은 생성자에만 붙일 수 있다. 즉, 오버라이드될 수 있는 메서드에는 사용할 수 없다.  
  
java.util.Arrays의 asList()는 다음과 같이 정의되어 있으며, 이 메서드는 매개변수로 넘겨받은 값들로 배열을 만들어서 새로운 ArrayList 객체를 만들어서 반환한다.  
```java
public static <T> List<T> asList(T... a) {
  return new ArrayList<T>(a); // ArryaList(E[] array)를 호출. 경고발생 
}
``` 
asList()의 매개변수가 가변인자인 동시에 제네릭 타입(non-refiable)타입이다. 메서드에 선언된 타입 T는 컴파일 과정에서 Object로 바뀐다. 즉, Object[]로 바뀌는데, Object[]에는 모든 타입의 객체가 들어있을 수 있으므로 이 배열로 ArrayList<T>를 생성하는 것은 위험하다고 경고하는 것이다. 그러나 asList()가 호출되는 부분을 컴파일러가 체크해서 타입 T가 아닌 다른 타입이 들어가지 못하게 할 것이므로 위의 코드는 아무런 문제가 없다.  
이럴 때는 메서드 앞에 @SafeVaragrs를 붙여서 이 메서드의 가변인자는 타입 안정성이 있다고 컴파일러에게 알려서 경고가 발생하지 않도록 해야 한다.  
메서드를 선언할 때 @SafeVarargs를 붙이면, 이 메서드를 호출하는 곳에서 발생하는 경고도 억제된다. 반면에 @SuppressWarnings("unchecked")로 경고를 억제하려면, 메서드 선언뿐만 아니라 메서드가 호출되는 곳에서도 애노테이션을 붙여야한다. 그리고 @SafeVarargs로 'varargs' 경고는 억제할 수 없기 때문에 습관적으로 다음과 같이 사용한다.  
```java
@SafeVarargs // unchecked 경고 억제
@SuppressWarnings("varargs") // varargs 경고 억제
public static <T> List<T> asList(T... a) {
  return new ArrayList<>(a);
}
```
## 메타 애노테이션 
메타 애노테이션은 애노테이션을 위한 애노테이션. 즉 애노테이션에 붙이는 애노테이션으로 애노테이션을 정의할 때 적용대상(target)이나 유지기간(retention)등을 지정하는 데 사용된다.  
메타 애노테이션은 `java.lang.annotation` 패키지에 포함되어 있다.  
### @Target
애노테이션이 적용가능한 대상을 지정하는데 사용된다.  
```java
@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE, MODULE})
@Retention(RetentionPolicy.SOURCE)
public @interface SuppressWarnings {
```
@Target 으로 지정할 수 있는 애노테이션 적용대상의 종류는 아래와 같다.  
<img width="382" alt="images_gkskaks1004_post_86cab7b6-4e60-4200-b41c-f7249190cc06_스크린샷 2021-07-21 오전 2 06 10" src="https://github.com/euichaan/TIL/assets/98090620/19a6e341-a97e-4149-874f-421b1367c007">
  
```java
@Target({FIELD, TYPE, TYPE_USE})
public @interface MyAnnotation {}

@MyAnnotation // TYPE
public class MyClass {

	@MyAnnotation // FIELD
	int i;
	
	@MyAnnotation // TYPE_USE
	MyClass mc;
}
```
FIELD는 기본형에, TYPE_USE는 참조형에 사용된다.

### @Retention
애노테이션이 유지(retention)되는 기간을 지정하는데 사용된다. 애노테이션의 유지 정책(retention policy)의 종류는 다음과 같다.  
- SOURCE: 소스 파일에만 존재, 클래스파일에는 존재하지 않음  
- CLASS: 클래스 파일에 존재, 실행시에 사용불가. 기본값  
- RUNTIME: 클래스 파일에 존재. 실행시에 사용가능  
  
CLASS는 컴파일러가 애노테이션의 정보를 클래스 파일에 저장할 수 있게는 하지만, **클래스 파일이 JVM에 로딩될 때는 애노테이션의 정보가 무시되어 실행 시에 애노테이션에 대한 정보를 얻을 수 없다.**    
이것이 'CLASS'가 유지정책의 기본값임에도 불구하고 잘 사용되지 않는 이유이다.  
  
지역 변수에 붙은 애노테이션은 컴파일러만 인식할 수 있으므로 유지정책이 RUNTIME인 애노테이션을 지역 변수에 붙여도 실행 시에는 인식되지 않는다.  
  
<img width="812" alt="스크린샷 2023-08-13 오후 3 01 24" src="https://github.com/euichaan/TIL/assets/98090620/9586c634-ba9c-4166-adf8-f13b6785d969">  
  
### @Documented
애노테이션에 대한 정보가 javdoc으로 작성한 문서에 포함되도록 한다. 자바에서 제공하는 기본 애노테이션 중에 '@Override'와 '@SupressWarnings'를 제외하고는 모두 이 메타 애노테이션이 붙어있다.  
```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface FunctionalInterface {}
```
### @Inherited
애노테이션이 자손 클래스에 상속되도록 한다. '@Inherited'가 붙은 애노테이션을 조상 클래스에 붙이면, 자손 클래스도 이 애노테이션이 붙은 것과 같이 인식된다.  
```java
@Inherited
@interface SupperAnno {}

@SuperAno
class Parent {}

class Child extends Parent {} // Child에 애노테이션이 붙은 것으로 인식 
```
### @Repeatable
'@Repeatable'이 붙은 애너테이션은 여러 번 붙일 수 있다.  


### @Native
네이티브 메서드(native method)에 의해 참조되는 상수 필드(constant field)에 붙이는 애노테이션이다. 아래는 java.lang.Long 클래스에 정의된 상수이다.  
```java
@Native public static final long MIN_VALUE = 0x8000000000000000L;
```
네이티브 메서드를 실제로 호출하는 것은 OS의 메서드이다.  
자바에 정의된 네이티브 메서드와 OS의 메서드를 연결해주는 작업은 JNI(Java Native Interface)가 한다.  
## 애노테이션 정의하는 방법
```java
@interface 애노테이션이름 {
  타입 요소이름(); // 애노테이션의 요소를 선언한다 
  ...
}
```
다음과 같이 정의할 수 있다.  
```java
public @interface MyAnnotation {
  String value();
}
```
엄밀히 말해서 '@Override'는 애노테이션이고 'Override'는 애노테이션의 타입이다.  
애노테이션에도 인터페이스처럼 상수를 정의할 수 있지만, 디폴트 메서드는 정의할 수 없다.  
    
아래에 선언된 TestInfo 애노테이션은 다섯 개의 요소(애너테이션 내에 선언된 메서드)를 갖는다.  
```java
public @interface TestInfo {
	int count();
	String testedBy();
	String[] testTools();
	TestType testType(); // enum
	DateTime testDate(); // 다른 애너테이션 포함 가능 
}

@interface DateTime {
	String yymmdd();
	String hhmmss();
}

enum TestType {
	FIRST, FINAL
}
```
애노테이션의 요소는 **반환값이 있고 매개변수는 없는** 추상 메서드의 형태를 가지며, 상속을 통해 구현하지 않아도 된다.  
다만, 애노테이션을 적용할 때 이 요소들의 값을 빠짐없이 지정해주어야 한다. 요소의 이름도 같이 적어주므로 순서는 상관없다.  
```java
@TestInfo(
	count = 3,
	testedBy = "Kim",
	testTools = {"JUnit", "AutoTester"},
	testType = TestType.FIRST,
	testDate = @DateTime(yymmdd = "210831", hhmmss = "235959")
)
class NewClass {
	
}
```
애노테이션의 각 요소는 기본값을 가질 수 있으며, 기본값이 있는 요소는 애노테이션을 적용할 때 값을 지정하지 않으면 기본값이 사용된다.  
기본값으로 null을 제외한 모든 리터럴이 가능하다.  
```java
@interface TestInfo {
  int count() default 1; // 기본값을 1로 지정
}

@TestInfo // @TestInfo(count = 1)과 동일 
public class NewClass { ... }
```
애노테이션 요소가 오직 하나뿐이고 이름이 value인 경우, 애노테이션을 적용할 때 요소의 이름을 생략하고 값만 적어도 된다.  
```java
@interface TestInfo {
  String value();
}
@TestInfo("passed")
public class NewClass { ... }
```
요소의 타입이 배열일 때도 요소의 이름이 value이면, 요소의 이름을 생략할 수 있다.  
  
요소의 타입이 배열일 경우, 괄호를 사용해서 여러 개의 값을 지정할 수 있다.  
```java
public @interface TestInfo {

	String[] testTools();
}

@TestInfo(testTools = {"JUnit", "AutoTester"})
class NewClass {

}
```
기본값을 지정할 때도 마찬가지로 괄호를 사용할 수 있다.  
```java
public @interface TestInfo {

	String[] info() default {"aaa", "bbb"}; 
}
```
## java.lang.annotation.Annotation
모든 애노테이션의 조상은 Annotation이다. 그러나 애노테이션은 상속이 허용되지 않으므로 **명시적으로 Annotation을 조상으로 지정할 수 없다.**  
모든 애노테이션 객체에 대해 `equals()`, `hashCode()`, `toString()`과 같은 메서드를 호출하는 것이 가능하다.  
  
## Marker Annotation
요소(애노테이션 내에 선언된 메서드)가 하나도 정의되지 않은 애노테이션을 마커 애노테이션(Marker Annotation)이라고 한다.  
  
## 애노테이션 요소의 규칙 
- 요소의 타입은 기본형, String, enum, 애노테이션, Class만 허용된다.  
- ()안에 매개변수를 선언할 수 없다.  
- 예외를 선언할 수 없다.  
- 요소를 타입 매개변수로 정의할 수 없다.  
