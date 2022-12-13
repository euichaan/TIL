# 12월 12일
## c++ 함수
```c++
#include <algorithm>
void fill (ForwardIterator first, ForwardIterator last, const T& val);
```
- first : 채우고자 하는 자료구조의 시작위치 iterator 
- last : 채우고자 하는 자료구조의 끝위치 iterator이며 `last는 포함하지 않습니다`  
- val : first부터 last전까지 채우고자 하는 값으로 어떤 객체나 자료형을 넘겨줘도 템플릿 T에 의해 가능합니다  
  
# 12월 13일
## 객체의 생성
new 키워드는 클래스의 인스턴스를 생성하기 위해 사용됩니다.  
인스턴스란, 해당 클래스의 구조로 `컴퓨터 저장공간에 할당된 실체`를 의미합니다. 여기서 클래스란 `속성`과 `행위`로 구성된 일종의 설계도입니다.  
OOP에서 객체는 클래스와 인스턴스를 포함한 개념입니다.  
  
```java
String s = new String();
String name = "Kenneth";
Person p = new Person();
```
인스턴스화(instantiation)는 새로운 객체를 위해 힙 영역의 메모리를 할당하고, 변수에 할당된 메모리주소(reference)를 반환합니다.  
- 객체의 생성자가 실행됩니다.  
- 런타임에 새로운 객체를 위해 힙 영역의 메모리를 할당합니다.  
- 새로운 객체를 위해 힙 영역에 할당된 메모리 주소는 스택 영역의 변수에 저장됩니다.  

```java
String s = new String();
```
1. 메모리가 할당되며, 할당된 메모리의 주소(reference)가 반환됩니다.  
2. 필드가 기본값으로 초기화됩니다.  
3. super또는 this를 포함한 생성자가 호출됩니다.  





  



