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
동일하지 않은 어떤 객체 X와 Y가 있을 때, 즉 `X.equals(Y)`가 '거짓'일 때 `X.hashCode() != Y.hashCode()`가 같지 않다면, 이때 사용하는 해시 함수는 완전한 해시 함수(perfect hash functions)라고 한다(![perfect hash functions](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/aa2e5402-0add-4d16-9c2d-0706c15d63b3)S는 모든 객체의 집합, h는 해시 함수).  
    
<img width="654" alt="스크린샷 2023-08-18 오후 4 52 56" src="https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/0e24e5df-fa74-4e98-84f1-0002da79e535">
  
Boolean같이 서로 구별되는 객체의 종류가 적거나, Integer, Long, Double 같은 Number 객체는 객체가 나타내려는 값 자체를 해시값으로 사용할 수 있기 때문에 완전한 해시 함수 대상으로 삼을 수 있다. 하지만 String이나 POJO에 대하여 완전한 해시 함수를 제작하는 것은 사실상 불가능하다.  
  
적은 연산만으로 빠르게 동작할 수 있는 완전한 해시 함수가 있다고 하더라도, 그것을 HashMap에서 사용할 수 있는 것은 아니다. HashMap은 기본적으로 각 객체의 hashCode() 메서드가 반환하는 값을 사용하는 데, 결과 자료형은 int다. 32비트 정수 자료형으로는 완전한 자료 해시 함수를 만들 수 없다. 논리적으로 생성 가능한 객체의 수가  2³²보다 많을 수 있기 때문이며, 또한 모든 HashMap 객체에서 O(1)을 보장하기 위해 랜덤 접근이 가능하게 하려면 원소가 2³²인 배열을 모든 HashMap이 가지고 있어야 하기 때문이다.  
  
따라서 HashMap을 비롯한 많은 해시 함수를 이용하는 associative array 구현체에서는 메모리를 절약하기 위하여 실제 해시 함수의 표현 정수 범위![helloworld-831311-3](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/713c56fb-7525-46ea-be26-30b55d13a504)보다 작은 M개의 원소가 있는 배열만을 사용한다. 따라서 다음과 같이 **객체에 대한 해시 코드의 나머지 값을 해시 버킷 인덱스 값으로 사용한다.**    
```java
// 해시를 사용하는 associative array 구현체에서 저장/조회할 해시 버킷을 계산하는 방법 
int index = X.hashCode() % M;
``` 
이 코드와 같은 방식을 사용하면, 서로 다른 해시 코드를 가지는 서로 다른 객체가 `1/M`의 확률로 같은 해시 버킷을 사용하게 된다. 이는 해시 함수가 얼마나 해시 충돌을 회피하도록 잘 구현되었느냐에 상관없이 발생할 수 있는 또 다른 종류의 해시 충돌이다.  
  
이렇게 해시 충돌이 발생하더라도 키-값 쌍 데이터를 잘 저장하고 조회할 수 있게 하는 방식에는 대표적으로 두 가지가 있는데, 하나는 **Open Addressing**이고, 다른 하나는 **Separate Chaining**이다. 이 둘 외에도 해시 충돌을 해결하기 위한 다양한 자료 구조가 있지만, 거의 모두 이 둘을 응용한 것이라고 할 수 있다.  
  
![open addressing and separate chaining](https://github.com/GDSC-Hongik/2023-2-OC-Java-Study/assets/98090620/df05b056-965b-457a-acbb-3fcaab74b0f4)  
**Open Addressing은 데이터를 삽입하려는 해시 버킷이 이미 사용 중인 경우 다른 해시 버킷에 해당 데이터를 삽입하는 방식이다.** 데이터를 저장/조회할 해시 버킷을 찾을 때는 Linear Probing, Quadratic Probing 등의 방법을 사용한다.  
  
**Seperate Chaining에서 각 배열의 인자는 인덱스가 같은 해시 버킷을 연결한 링크드 리스트의 첫 부분(head)이다.**    
  
둘 모두 Worst Case O(M)이다. 하지만 Open Addressing은 연속된 공간에 데이터를 저장하기 때문에 Separate Chaining에 비하여 캐시 효율이 높다. 따라서 데이터 개수가 충분히 적다면 Open Addressing이 Separate Chaining보다 더 성능이 좋다. 하지만 배열의 크기가 커질수록(M 값이 커질수록) 캐시 효율이라는 Open Addressing의 장점은 사라진다. 배열의 크기가 커지면, L1, L2 캐시 적중률(hit ratio)이 낮아지기 때문이다.  
  
**Java HashMap에서 사용하는 방식은 Separate Channing이다.** Open Addressing은 데이터를 삭제할 때 처리가 효율적이기 어려운데, HashMap에서 `remove()` 메서드는 매우 빈번하게 호출될 수 있기 때문이다. 게다가 HashMap에 저장된 키-값 쌍 개수가 일정 개수 이상으로 많아지면, 일반적으로 Open Addressing은 Separate Chaining보다 느리다. Open Addressing의 경우 해시 버킷을 채운 밀도가 높아질수록 Worst Case 발생 빈도가 더 높아지기 때문이다. **반면 Separate Chaining 방식의 경우 해시 충돌이 잘 발생하지 않도록 '조정'할 수 있다면 Worst Case 또는 Worst Case에 가까운 일이 발생하는 것을 줄일 수 있다(여기에 대해서는 "보조 해시 함수"에서 설명하겠다).**  
  
```java
// Java 7에서의 해시 버킷 관련 구현 
transient Entry<K,V>[] table = (Entry<K,V>[]) EMPTY_TABLE;  
// transient로 선언된 이유는 직렬화(serializ)할 때 전체, table 배열 자체를 직렬화하는 것보다
// 키-값 쌍을 차례로 기록하는 것이 더 효율적이기 때문이다.

static class Entry<K,V> implements Map.Entry<K,V> {  
        final K key;
        V value;
        Entry<K,V> next;
        int hash;

Entry(int h, K k, V v, Entry<K,V> n) {  
            value = v;
            next = n;
            key = k;
            hash = h;
        }

        public final K getKey() { … }
public final V getValue() { …}  
        public final V setValue(V newValue) { … }
        public final boolean equals(Object o) { … }
        public final int hashCode() {…}
        public final String toString() { …}

void recordAccess(HashMap<K,V> m) {… }

void recordRemoval(HashMap<K,V> m) {…}  
}
```
Separate Chaining 방식을 사용하기 때문에, Java 7에서의 put() 메서드 구현은 예제 4에서 보는 것과 같다.  
```java
// 예제 4 put() 메서드 구현
public V put(K key, V value) { 
  if (table == EMPTY_TABLE) { inflateTable(threshold); // table 배열 생성 } // HashMap에서는 null을 키로 사용할 수 있다. if (key == null) return putForNullKey(value); // value.hashCode() 메서드를 사용하는 것이 아니라, 보조 해시 함수를 이용하여 // 변형된 해시 함수를 사용한다. "보조 해시 함수" 단락에서 설명한다.  
  int hash = hash(key);

  // i 값이 해시 버킷의 인덱스이다.
  // indexFor() 메서드는 hash % table.length와 같은 의도의 메서드다.
  int i = indexFor(hash, table.length);



  // 해시 버킷에 있는 링크드 리스트를 순회한다.
  // 만약 같은 키가 이미 저장되어 있다면 교체한다.
 for (Entry<K,V> e = table[i]; e != null; e = e.next) {
    Object k;
  if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
    V oldValue = e.value;
    e.value = value;
    e.recordAccess(this);
    return oldValue;
  }
 }            

  // 삽입, 삭제 등으로 이 HashMap 객체가 몇 번이나 변경(modification)되었는지
  // 관리하기 위한 코드다.
  // ConcurrentModificationException를 발생시켜야 하는지 판단할 때 사용한다.
  modCount++;


  // 아직 해당 키-값 쌍 데이터가 삽입된 적이 없다면 새로 Entry를 생성한다. 
  addEntry(hash, key, value, i);
  return null;
}
```


그러나 Java 8에서는 더 발전된 방식을 사용한다.  
## Java 8 HashMap에서의 Separate Chaining
Java 2부터 Java 7까지의 HashMap에서 Separate Chaining 구현 코드는 조금씩 다르지만, 구현 알고리즘 자체는 같았다. 만약 객체의 해시 함수 값이 균등 분포(uniform distribution) 상태라고 할 때, get() 메서드 호출에 대한 기댓값은 `E(N/M)`이다. 그러나 Java 8에서는 이보다 더 나은 `E(logN/M)`을 보장한다.    
데이터의 개수가 많아지면, **Separate Chaining에서 링크드 리스트 대신 트리를 사용하기 때문이다.**  
  
게다가 실제 해시 값은 균등 분포가 아닐뿐더러, 설사 균등 분포를 따른다고 하더라도 birthday problem이 설명하듯 일부 해시 버킷 몇 개에 데이터가 집중될 수 있다. 그래서 데이터의 개수가 일정 이상일 때에는 링크드 리스트 대신 트리를 사용하는 것이 성능상 이점이 있다.  
  
링크드 리스트를 사용할 것인가 트리를 사용할 것인가에 대한 기준은 **하나의 해시 버킷에 할당된 키-값 쌍의 개수이다.** 예제 5에서 보듯 Java 8 HashMap에서는 상수 형태로 기준을 정하고 있다. 즉 하나의 해시 버킷에 8개의 키-값 쌍이 모이면 링크드 리스트를 트리로 변경한다. 만약 해당 버킷에 있는 데이터를 삭제하여 개수가 6개에 이르면 다시 링크드 리스트로 변경한다. 트리는 링크드 리스트보다 메모리 사용량이 많고, 데이터의 개수가 적을 때 트리와 링크드 리스트의 Worst Case 수행 시간 차이 비교는 의미가 없기 때문이다. 8과 6으로 2 이상의 차이를 둔 것은, 만약 차이가 1이라면 어떤 한 키-값 쌍이 반복되어 삽입/삭제되는 경우 불필요하게 트리와 링크드 리스트를 변경하는 일이 반복되어 성능 저하가 발생할 수 있기 때문이다.
```java
static final int TREEIFY_THRESHOLD = 8;

static final int UNTREEIFY_THRESHOLD = 6;  
```
Java 8 HashMap에서는 Entry 클래스 대신 Node 클래스를 사용한다. Node 클래스 자체는 사실상 Java 7의 Entry 클래스와 내용이 같지만, 링크드 리스트 대신 트리를 사용할 수 있도록 하위 클래스인 TreeNode가 있다는 것이 Java 7 HashMap과 다르다.  
  
이때 사용하는 트리는 `Red-Black Tree`인데, Java Collections Framework의 TreeMap과 구현이 거의 같다. 트리 순회 시 사용하는 대소 판단 기준은 해시 함수 값이다. 해시 값을 대소 판단 기준으로 사용하면 Total Ordering에 문제가 생기는데, Java 8 HashMap에서는 이를 `tieBreakOrder()` 메서드로 해결한다.  
  
```java
transient Node<K,V>[] table;


static class Node<K,V> implements Map.Entry<K,V> {  
  // 클래스 이름은 다르지만, Java 7의 Entry 클래스와 구현 내용은 같다. 
}


// LinkedHashMap.Entry는 HashMap.Node를 상속한 클래스다.
// 따라서 TreeNode 객체를 table 배열에 저장할 수 있다.
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {

        TreeNode<K,V> parent;  
        TreeNode<K,V> left;
        TreeNode<K,V> right;
        TreeNode<K,V> prev;   

        // Red Black Tree에서 노드는 Red이거나 Black이다.
        boolean red;

        TreeNode(int hash, K key, V val, Node<K,V> next) {
            super(hash, key, val, next);
        }

        final TreeNode<K,V> root() {
        // Tree 노드의 root를 반환한다. 
        }

        static <K,V> void moveRootToFront(Node<K,V>[] tab, TreeNode<K,V> root) {
        // 해시 버킷에 트리를 저장할 때에는, root 노드에 가장 먼저 접근해야 한다.
        }


        // 순회하며 트리 노드 조회 
        final TreeNode<K,V> find(int h, Object k, Class<?> kc) {}
        final TreeNode<K,V> getTreeNode(int h, Object k) {}


        static int tieBreakOrder(Object a, Object b) {
         // TreeNode에서 어떤 두 키의comparator 값이 같다면 서로 동등하게 취급된다.
         // 그런데 어떤 두 개의 키의 hash 값이 서로 같아도 이 둘은 서로 동등하지 
         // 않을 수 있다. 따라서 어떤 두 개의 키에 대한 해시 함수 값이 같을 경우, 
         // 임의로 대소 관계를 지정할 필요가 있는 경우가 있다. 
        }


        final void treeify(Node<K,V>[] tab) {
          // 링크드 리스트를 트리로 변환한다.
        }



        final Node<K,V> untreeify(HashMap<K,V> map) {
          // 트리를 링크드 리스트로 변환한다.
        }

        // 다음 두 개 메서드의 역할은 메서드 이름만 읽어도 알 수 있다.
        final TreeNode<K,V> putTreeVal(HashMap<K,V> map, Node<K,V>[] tab,
                                       int h, K k, V v) {}
        final void removeTreeNode(HashMap<K,V> map, Node<K,V>[] tab,
                                  boolean movable) {}


        // Red Black 구성 규칙에 따라 균형을 유지하기 위한 것이다.
        final void split (…)
        static <K,V> TreeNode<K,V> rotateLeft(…)
        static <K,V> TreeNode<K,V> rotateRight(…)
        static <K,V> TreeNode<K,V> balanceInsertion(…)
        static <K,V> TreeNode<K,V> balanceDeletion(…)



        static <K,V> boolean checkInvariants(TreeNode<K,V> t) {
        // Tree가 규칙에 맞게 잘 생성된 것인지 판단하는 메서드다.
        }
    }
```
## 해시 버킷 동적 확장 

## 보조 해시 함수

## String 객체에 대한 해시 함수

## Java 7에서 String 객체에 대한 별도의 해시 함수
