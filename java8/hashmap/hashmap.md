# Java HashMap은 어떻게 동작하는가?
[Java HashMap은 어떻게 동작하는가?](https://d2.naver.com/helloworld/831311) 를 읽고 정리한 글입니다.  
  
Java 7, Java 8 기준으로 HashMap이 어떻게 구현되어 있는지 설명.  
HashMap 구현체는 성능을 향상시키기 위해서 지속적으로 변화해 왔다.  
  
이 글에서는 **어떤 방식으로 HashMap 구현체 성능을 향상시켰는지 소개한다.**  
구체적으로 다루는 내용은 Amortized Constant Time을 위하여 어떻게 해시 충돌(hash collision) 가능성을 줄이고 있는 가에 대한 것이다.  
  
## HashMap과 HashTable
이 글에서 말하는 HashMap과 HashTable은 Java의 API 이름이다. HashTable이란 JDK 1.0부터 있던 Java의 API이고, HashMap은 Java 2에서 처음 선보인 Java Collections Framework에 속한 API다. HashTable 또한 Map 인터페이스를 구현하고 있기 때문에 HashMap과 HashTable이 제공하는 기능은 같다. 다만 HashMap은 **보조 해시 함수(Additional Hash Function)를 사용하기 때문에 보조 해시 함수를 사용하지 않는 HashTable에 비하여 해시 충돌(hash collision)이 덜 발생할 수 있어** 상대으로 성능상 이점이 있다. 보조 해시 함수가 아니더라도, HashTable 구현에는 거의 변화가 없는 반면, HashMap은 지속적으로 개선되고 있다. HashTable의 현재 가치는 JRE 1.0, JRE 1.1 환경을 대상으로 구현한 Java 애플리케이션이 잘 동작할 수 있도록 하위 호환성을 제공하는 것에 있기 때문에, 이 둘 사이에 성능과 기능을 비교하는 것은 큰 의미가 없다고 할 수 있다.  
  
HashMap과 HashTable을 정의한다면, '키에 대한 해시 값을 사용하여 값을 저장하고 조회하며, 키-값 쌍의 개수에 따라 동적으로 크기가 증가하는 associate array'라고 할 수 있다. 이 associate array를 지칭하는 다른 용어가 있는데, 대표적으로 Map, Dictionary, Symbol Table 등이다.  
```java
public class Hashtable<K,V>
    extends Dictionary<K,V>
    implements Map<K,V>, Cloneable, java.io.Serializable {

public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {
```
associative array를 지칭하기 위하여 HashTable에서는 Dictionary라는 이름을 사용하고, HashMap에서는 그 명칭이 그대로 말하듯이 Map이라는 용어를 사용하고 있다.  
  
map(또는 mapping)은 원래 수학 함수에서의 대응 관계를 지칭하는 용어로, 경우에 따라서는 함수 자체를 의미하기도 한다. 즉 HashMap이란 이름에서 알 수 있듯이, HashMap은 키 집합인 정의역과 값 집합인 공역의 대응에 `해시 함수`를 이용한다.  
  
![사상](https://github.com/euichaan/TIL/assets/98090620/9ac0924f-bec3-4f8a-9873-111354d937c7)
  
## 해시 분포와 해시 충돌
Boolean같이 서로 구별되는 객체의 종류가 적거나, Integer, Long, Double 같은 Number 객체는 객체가 나타내려는 값 자체를 해시값으로 사용할 수 있기 때문에 완전한 해시 함수 대상으로 삼을 수 있다. 하지만 String이나 POJO에 대하여 완전한 해시 함수를 제작하는 것은 사실상 불가능하다.  
  
적은 연산만으로 빠르게 동작할 수 있는 완전한 해시 함수가 있다고 하더라도, 그것을 HashMap에서 사용할 수 있는 것은 아니다. HashMap은 기본적으로 각 객체의 hashCode() 메서드가 반환하는 값을 사용하는 데, 결과 자료형은 int다. 32비트 정수 자료형으로는 완전한 자료 해시 함수를 만들 수 없다. 논리적으로 생성 가능한 객체의 수가  2³²보다 많을 수 있기 때문이며, 또한 모든 HashMap 객체에서 O(1)을 보장하기 위해 랜덤 접근이 가능하게 하려면 원소가 2³²인 배열을 모든 HashMap이 가지고 있어야 하기 때문이다.  
  
따라서 HashMap을 비롯한 많은 해시 함수를 이용하는 associative array 구현체에서는 메모리를 절약하기 위하여 실제 해시 함수의 표현 정수 범위![helloworld-831311-3](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/713c56fb-7525-46ea-be26-30b55d13a504)보다 작은 M개의 원소가 있는 배열만을 사용한다. 따라서 다음과 같이 **객체에 대한 해시 코드의 나머지 값을 해시 버킷 인덱스 값으로 사용한다.**  
```java
int index = X.hashCode() % M;
``` 
이 코드와 같은 방식을 사용하면, 서로 다른 해시 코드를 가지는 서로 다른 객체가 `1/M`의 확률로 같은 해시 버킷을 사용하게 된다. 이는 해시 함수가 얼마나 해시 충돌을 회피하도록 잘 구현되었느냐에 상관없이 발생할 수 있는 또 다른 종류의 해시 충돌이다.  
  
이렇게 해시 충돌이 발생하더라도 키-값 쌍 데이터를 잘 저장하고 조회할 수 있게 하는 방식에는 대표적으로 두 가지가 있는데, 하나는 **Open Addressing**이고, 다른 하나는 **Separate Chaining**이다. 이 둘 외에도 해시 충돌을 해결하기 위한 다양한 자료 구조가 있지만, 거의 모두 이 둘을 응용한 것이라고 할 수 있다.  
  
## Java 8 HashMap에서의 Separate Chaining

## 해시 버킷 동적 확장 

## 보조 해시 함수

## String 객체에 대한 해시 함수

## Java 7에서 String 객체에 대한 별도의 해시 함수
