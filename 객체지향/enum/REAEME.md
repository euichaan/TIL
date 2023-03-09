# Enumeration
## Enum이란?
- enumeration type = 열거형  
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





