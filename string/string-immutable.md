## String Immutable
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
  
## String Immutable한 이유
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
