# String Immutable
[String Immutable](https://www.baeldung.com/java-string-immutable)
James Gosling(creator of Java)의 말 :  
He further supports his argument stating features that immutability provides, such as caching, security,   easy reuse without replication, etc.  
immutablity가 캐싱, 보안, 복제없는 쉬운 재사용을 제공한다고 말하고 있다.  
  
immutable Object는 완전히 생성된 후에도 내부 상태가 일정하게 유지되는 객체이다.  
즉, object가 variable에 할당되면, 어떤 방법으로도 참조를 업데이트하거나 내부 상태를 변경할 수 없다.  
  
그렇다면 String은 왜 Java에서 Immutable일까?  
Immutable(불변)으로 유지하는 것의 이점은 캐싱, 보안, 동기화, 성능이다.  
  
## String Pool의 소개
String은 가장 널리 쓰이는 자료구조. String literal을 캐싱하고 재사용한다면 다른 문자열 변수가 String Pool  
에서 동일한 개체를 참조하기 때문에 많은 힙 공간이 절약된다.  

Java String Pool은 JVM에서 문자열을 저장하는 특수 메모리 영역이다.  
Strings는(문자열은) Java에서 Immutable이기 때문에 JVM은 각 리터럴 문자열의 복사본 하나만 풀에 저장하여  
문자열에 할당된 메모리 양을 최적화한다. 이 프로세스를 인턴이라고 한다.  
  
```java
String s1 = "Hello World";
String s2 = "Hello World";
         
assertThat(s1 == s2).isTrue(); // true
```
String pool이 있기 때문에 두 개의 다른 변수가 풀에서 동일한 개체를 가리키고 있다.중요한 메모리 리소스 절약!  

## why Immutable?
1. Security(보안)  
2. Synchronization(동기화)  
3. Hashcode Caching(해시코드 캐싱)  
4. Performance(성능)  
  
### 1. Security
String은 사용자 이름, 암호, 네트워크 연결 같은 민감한 정보를 저장하기 위해 Java 애플리케이션에서 사용된다.  
또한 클래스를 로드하는 동안 JVM 클래스 로더에서 광범위하게 사용된다.  
  
따라서 일반적으로 전체 응용 프로그램의 보안과 관련하여 String 클래스의 보안은 중요하다.  
```java
void criticalMethod(String userName) {
    // perform security checks
    if (!isAlphaNumeric(userName)) {
        throw new SecurityException(); 
    }
	
    // do some secondary tasks
    initializeDatabase();
	
    // critical task
    connection.executeUpdate("UPDATE Customers SET Status = 'Active' " +
      " WHERE UserName = '" + userName + "'");
}
```
신뢰할 수 없는 소스로부터 String Object를 받았다고 가정해본다.  
처음에는 문자열이 영숫자인지 확인하기 위해 필요한 모든 보안 검사를 수행한 다음 추가 작업을 수행한다.  
  
문자열이 변경 가능한 경우 업데이트를 실행할 때까지 보안 검사를 수행한 후에도 받은 문자열이 안전한지 확신할 수 없습니다.  
  
신뢰할 수 없는 호출자 메서드는 여전히 참조를 가지고 있으며 무결성 검사 간에 문자열을 변경할 수 있습니다.  
따라서 이 경우 쿼리가 SQL injection에 취약해집니다. 따라서 변경 가능한 문자열은 시간이 지남에 따라 보안이 저하될 수 있습니다.  
  
username이 다른 스레드에 표시되어 무결성 검사 후 값이 변경될 수도 있습니다.  

### 2. Synchronization
Immutable은 자동적으로 String을 thread safe하게 만듭니다.  
여러 스레드에서 접근할 때 String 스레드가 변경되지 않습니다.  

**따라서 불변 객체는 일반적으로 동시에 실행되는 여러 스레드에서 공유될 수 있습니다. thread-safe합니다.**  
스레드가 값을 변경하면 동일한 값을 수정하는 대신, 문자열 풀에 새 문자열이 생성되기 때문입니다.  
따라서 문자열은 멀티스레딩에 안전. 

### 3. Hashcode Caching
String 객체는 데이터 구조로 많이 사용되기 때문에 HashMap, HashTable, HashSet 등과 같은 해시 구현에서도 널리 사용된다. 이러한 해시 구현체에서 동작할 때 해시코드() 메서드는 버킷링을 위해 상당히 자주 호출된다.  
  
불변성은 Strings의 값이 변하지 않음을 보장합니다. 따라서 캐시를 용이하게 하기 위해 string 클래스에서 hashCode() 메서드가 재정의되어 첫 번째 hashCode() 호출 중에 해시가 계산되고 캐시되며 이후 동일한 값이 반환됩니다.  
  
결과적으로 String 개체와 함께 작동할 때 해시 구현을 사용하는 컬렉션의 성능이 향상됩니다.  
  
반면 변경 가능한 문자열은 작업 후 문자열의 내용이 수정된 경우 삽입 및 검색 시 두 개의 다른 해시 코드를 생성하여 맵에서 값 개체를 잃을 가능성이 있습니다.  
  
### 4. Performance
String이 Immutable이기 때문에 String pool이 존재한다. Strings(문자열)과 함께 작동할 때 힙 메모리를 절약하고  
해시 구현에 더 빠르게 엑세스하여 성능을 향상시킨다.  
  
## 요약 정리
```java
String name = "Shane";
name = "ShanePark";
```
String name으로 생성한 name 변수자체는 String 객체가 아니다. 단지 메모리에 있는 "Shane"이라는 String 변수의  
reference(참조) 일 뿐이다.  
그렇기 때문에 그 다음 줄의 `name="ShanePark"`의 경우에도 "Shane이라는 값을 가진 String Object에 새로운 값을  
할당하는 것이 아닌 Heap 메모리의 String Pool에 "ShanePark"이라는 String Object를 생성 한 후에 name변수가  
"Shane" 대신 "ShanePark" 오브젝트를 참조하도록 변경하는 것 뿐이다.  
  
정리하자면 Java에서 말하는 String의 Immutable은 name 변수에 대해 말하는 것이 아닌 메모리의 "Shane" 혹은  
"ShanePark"이라는 String Object에 대한 것. 해당 String Object의 값은 한번 생성된 후에는 값이 절대 변하지 않는다.  
  
Immutable의 이점
1. 메모리 절약  
2. Security  
3. thread safe  
  
Java 7 전에는 Java String Pool을 고정된 사이즈의 PermGen space에 보관했었기 때문에 확장이 불가능.  
garbage collection의 대상이 될 수도 없었다. 이로 인해 너무 많은 String들이 intern(String Pool에 등록)  
되는 경우 OutOfMemoryError를 발생시킬 수도 있었다.  
  
Java 7 이후부터는 Java String Pool이 Heap Space에 보관되어 JVM으로부터 garbage collect도 가능해져  
참조되지 않은 String들을 pool에서 제거할 수 있게 되어 단점도 점점 사라지고 있음.  
  
Java 8까지는 UTF-16으로 인코딩 된 char 배열로 표현 되었기 때문에 모든 Character가 2 Bytes의 메모리를  
차지해왔다. Java 9부터는 Compact Strings 라는 새로운 표현 방법이 제공 됨으로서 char[]와 byte[] 중  
저장된 컨텐츠에 따라 적절한 방식을 선택하고 UTF-16 인코딩은 필요할 때만 사용하게 되며  
heap memory의 총 사용량이 현저히 적어졌으며 그로 인해 JVM Garbage Collector의 오버헤드도 줄어들게 되었다.  
