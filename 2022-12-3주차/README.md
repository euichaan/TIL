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

## 오버로딩과 오버라이딩  
오버로딩 (overloading) : 클래스 내에 이미 사용하려는 이름과 같은 이름을 가진 메서드가 있더라도 `매개변수의 개수 또는 타입`이 다르면,  
같은 이름을 사용해서 메서드를 정의할 수 있습니다.  

메서드의 이름이 같고, 메개변수의 개수나 타입이 달라야 합니다. '리턴 값만' 다른 것은 오버로딩 할 수 없습니다.  
또한 접근 제어자만 다르게 한다고 오버로딩이 가능하지 않습니다.   

오버라이딩 (overriding) : 부모 클래스의 메서드 동작 방법을 변경`(재정의)`하여 우선적으로 사용하는 것입니다.  

## c++ STL
`max_element`는 가장 큰 원소가 있는 곳의 포인터를 반환하는 STL. *를 달아 그 주소의 값을 반환할 수 있습니다.  
```c++
int solution3(int arr[]) {
  return *max_element(arr, arr + 4);
}
```

## 트랜잭션
트랜잭션이란 `한꺼번에 모두 수행되어야 할 일련의 연산들` 또는 `데이터베이스의 상태를 변화시키는 하나의 논리적 기능을 수행하기 위한 작업의 단위` 입니다.  
  
# 12월 14일
## parseInt와 valueOf
parseInt는 String 타입의 숫자를 int 타입의 문자열로 변환해줍니다.    
문자가 섞여있다면 `NumberFormatException`이 발생합니다.  
  
parseInt의 리턴타입은 기본 자료형(int)입니다.  
valueOf의 리턴타입은 객체입니다.  

# 12월 15일 
## Comparable과 Comparator
Comparable의 CompareTo는 자기 자신을 기준으로 삼아 대소관계를 파악합니다.  
compareTo는 정수를 반환하며, 자기 자신을 기준으로 상대방과의 차이 값을 비교하여 반환합니다.  
  
### 주의해야 할 점
뺄셈 과정에서 자료형의 범위를 넘어버리는 경우가 발생할 수 있습니다.  
int 자료형은 32비트(4바이트)자료형으로, -2의 31승 ~ 2의 31승 -1으로 -2147483648 ~ 2147483647 입니다.  
  
**만약 해당 범위 밖을 넘게 되면 반대편의 값으로 넘어가게 됩니다.**
쉽게 말하자면 -2147483648 - 1 = -2147483649 일 것입니다. 하지만 int 자료형에서 표현할 수 없는 수로  
2147483647으로 int형의 최댓값으로 반환합니다. 주어진 범위의 하한선을 넘어버리는 것을 `Underflow`라고 합니다.  
  
반대로 2147483647 + 1 = 2147483648 일 것입니다. 하지만 마찬가지로 int 자료형에서 표현할 수 없는 수로  
-2147483648 이 되어 int 형의 최솟값으로 반환됩니다. 이렇게 주어진 범위의 상한선을 넘어버리는 것을 `Overflow`라고 합니다.  
  
```java
int min = Integer.MIN_VALUE;
int max = Integer.MAX_VALUE;

System.out.println(min - 1); //2147483647 underflow 
System.out.println(max + 1); //-2147483648 overflow
```
예를 들어 다음과 같은 두 값이 있다고 가정해봅니다.  
o1 = 1, o2 = -2147483648  
그리고 두 수를 return o1 - o2 형식으로 한다면 1 - (-2147483648) = 2147483648 이 되어야 하지만 `Overflow`가 나서  
음수형 최솟값인 -2147483648이 나옵니다. 따라서 1인 o1이 -2147483648인 o2보다 작다는 상황이 와버립니다.  
  
그렇기 때문에 compareTo를 구현할 때는 Overflow 또는 Underflow가 발생할 여지가 있는지를 반드시 확인하고 사용해야 합니다.  
  
일반적으로 <, >, == 으로 대소비교 해주는 것이 권장됩니다.  
  
## Comparator
**두 매개변수 객체를 비교**합니다.  
  
Comparable의 compareTo는 선행 원소가 자기 자신이 되고, 후행 원소가 매개 변수로 들어오는 o가 되는 반면에,  
Comparator의 compare는 선행 원소가 o1이 되고, 후행 원소가 o2가 됩니다.  
  
이 말은, o1과 o2를 비교함에 있어 **자기 자신은 두 객체 비교에 영향이 없다는 뜻입니다.**
  
## Comparator 활용
compare 객체 호출을 위해서 쓰지 않는 객체를 생성해야 하는 경우가 있기 때문에, 익명 객체(클래스)를 활용합니다.  
즉, 이름은 정의 되지 않지만, Comparator를 구현하는 익명객체를 생성하면 되는 것입니다.  

## Comparable, Comparator 와 정렬의 관계  
객체를 비교하기 위해 Comparable 또는 Comparator를 쓴다는 것은 곧 사용자가 정의한 기준을 토대로 비교를 하여 양수, 0, 음수 중  
하나가 반환된다는 것입니다.  
  
Java에서의 정렬은 오름차순을 기준으로 합니다. 따라서 규칙을 일반화할 수 있습니다.  
음수일 경우 : **두 원소의 위치를 교환 안함**  
양수일 경우 : **두 원소의 위치를 교환 함**  
  
Arrays.sort()에는 단순히 배열만 파라미터로 받는 것이 아니라 Comparator 또한 파라미터로 받기도 합니다.  
간단하게 요약하자면, Comparator 파라미터로 넘어온 c의 비교 기준을 갖고 파라미터로 넘어온 객체배열 a를 정렬하겠다는 의미입니다.  

```java
Arrays.sort(array, comp);
```
이런 형태로도 쓸 수 있다는 것입니다.  
Arrays.sort(array)는 array 배열의 Comparable을 구현한 compareTo() 메서드를 활용하여 비교 및 정렬을 하게 됩니다.  
반대로 Arrays.sort(array, Comparator<? super T> c)의 경우 파라미터로 넘겨준 c를 기준으로 비교 및 정렬을 하게 됩니다.  
만약 c가 null이라면 `sort(array)`를 호출합니다.  
  
즉, Comparator로 넘겨받은 인자가 null일 경우 Arrays.sort(Object[] a)를 호출하여 a배열의 Comparable을 구현한 compareTo()  
로 비교 및 정렬을 한다는 것입니다.  
  
반대로 c가 null이 아니라면 Comparator을 구현한 객체의 compare() 메서드를 사용하여 비교 및 정렬을 한다는 말입니다.  
  
보통 Comparable은 가장 기본적인 설정(보통 오름차순)으로 구현하는 경우가 많고, Comparator는 여러개를 생성할 수 있다보니  
특별한 정렬을 원할 때 많이 쓰입니다.  
  
쉽게 말해 Comparable은 기본 순서를 정의하는데 사용되며, Comparator는 특별한 기준의 순서를 정의할 때 사용한다는 것입니다.  

## String 클래스는..?
String 클래스는 Comparable을 implements하여 compareTo() 메서드를 구현하고 있습니다.  

## 컬렉션을 순회하면서 원소 삭제하기 
자바의 공식 문서를 보면 Iterator의 remove를 사용하는 것이 컬렉션을 순회하면서 원소를 삭제할 수 있는 유일하게 안전한 방법  
이라고 가이드하고 있습니다.  
```java
List<Character> letters = new ArrayList<>(
    Arrays.asList('A', 'B', '1', '2', 'C', 'D', '3', 'E', '4', '5'));

for (Iterator<Character> iter = letters.iterator(); iter.hasNext(); ) {
  Character letter = iter.next();
    if (Character.isDigit(letter)) {
      iter.remove(); //숫자 원소 제거
    }
  }
System.out.println(letters);

```
Java8에서는 다음과 같이 사용 가능합니다.  
```java
List<Character> letters = new ArrayList<>(
    Arrays.asList('A', 'B', '1', '2', 'C', 'D', '3', 'E', '4', '5'));

letters.removeIf(Character::isDigit);
System.out.println(letters);
```
추가로 원본 리스트에 변경을 가하지 않고, 숫자 원소가 삭제된 새로운 리스트를 얻고 싶은 경우, 다음과 같이 스트림 API를 사용  
할 수 있습니다.  
```java
List<Character> alphabets = letters.stream()
    .filter(Character::isAlphabetic)
    .collect(Collectors.toList());
```

  











  



