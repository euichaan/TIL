# 3장 - ArrayList 클래스
## 3.1 MyArrayList 메서드 분류하기
get 메서드 - 상수 시간  
```java
public E set(int index, E elemnet) {
  E old = get(index);
  array[index] = element;
  return old;
}
```
이 해법에서 약간 똑똑한 부분은 명시적으로 배열의 범위를 검사하지 않는다는 것입니다.  
set 메서드는 인덱스가 유효하지 않으면 예외를 던지는 get 메서드를 호출합니다.  
  
set 메서드 - 상수 시간  
equals 메서드 - 상수 시간  
```java
public int indexOf(Object target) {
  for (int i = 0; i < size; i++) {
    if (equals(target, array[i])) {
      return i;
    }
  }
  return -1;
}
```
운이 좋다면 대상 객체를 단번에 찾아서 한 개의 요소만 테스트한 후 반환하고, 운이 없다면 모든 요소를 테스트해야 합니다.  
평균적으로 요소 개수의 절반을 테스트하기를 기대합니다. 따라서 indexOf 메서드느 선형입니다.  
  
remove 메서드의 분석도 비슷합니다.  
```java
public E remove(int index) {
  E element = get(index);
  for (int i = index; i < size - 1; i++) {
    array[i] = array[i+1];
  }
  size--;
  return element;
}
```
리스트의 마지막 요소를 제거하면 반복문은 실행되지 않고 이 메서드는 상수 시간이 됩니다.  
첫 번째 요소를 제거하면 나머지 모든 요소에 접근하여 선형이 됩니다. 따라서 remove 메서드는 선형으로 간주합니다.  
  
## 3.2 add 메서드 분류하기
다음은 인덱스와 요소를 인자로 받는 add 메서드입니다.  
```java
public void add(int index, E element) {
  if (index < 0 || index > size) {
    throw new IndexOutOfBoundsException();
  }

  // 크기 조정을 통해 요소를 추가
  add(element);

  // 다른 요소를 시프트합니다.
  for (int i = size-1; i>index; i--) {
    array[i] = array[i-1];
  }

  array[index] = element;
}
```
add(int, E) 메서드를 분류하려면 먼저 add(E) 메서드를 분류해야 합니다.  
  
단일 인자 버전은 배열에 미사용 공간이 있다면 add 메서드는 상수 시간입니다.  
하지만 배열의 크기를 변경하면 System.arraycopy 메서드 호출 시 실행 시간이 배열의 크기에 비례하기 때문에 add 메서드는 선형입니다.  
  
- 4번의 add 메서드 호출 후에는 요소 4개를 저장하고 2번 복사합니다.  
- 8번의 add 메서드 호출 후에는 요소 8개를 저장하고 6번 복사합니다.  
- 16번의 add 메서드 호출 후에는 요소 16개를 저장하고 14번 복사합니다.  
  
즉, n번 추가하면 요소 n개를 저장하고 n-2개를 복사해야 합니다. 따라서 총 연산 횟수는 n + n - 2 = 2n-2 가 됩니다.  
  
add 메서드의 평균 연산 횟수를 구하려면 합을 n으로 나눠야 해서 결과는 2-2/n 입니다.  
n이 커지면 2/n은 작아집니다. n의 가장 큰 지수에만 관심 있다는 원칙을 적용하면 add 메서드는 상수 시간으로 간주합니다.  
  
일련의 호출에서 평균 시간을 계산하는 알고리즘 분류 방법을 분할 상환 분석이라고 합니다.  
핵심 개념은 일련의 호출을 하는 동안 배열을 복사하는 추가 비용이 분산되거나 분할 상환되었다는 것입니다.  
  
add(int ,E)메서드는 선형입니다.  
  
## 3.3 문제 크기
마지막 예제는 removeAll 메서드입니다.  
```java
public boolean removeAll(Collection<?> collection) {
  boolean flag = false;
  for (Object obj : collection) {
    flag |= remove(obj);
  }
  return flag;
}
```
반복문을 돌 때마다 removeAll 메서드는 선형인 remove 메서드를 호출합니다.  
그래서 removeAll 메서드를 이차로 생각하기 쉽습니다. 하지만 반드시 그렇지는 않습니다.  
  
이 메서드에서 반복문은 collection 인자의 각 요소를 한 번씩 순회합니다.  
collection의 요소가 m개고 제거할 리스트에 요소가 n개 있다면 이 메서드는 O(nm)입니다.  
collection의 크기가 상수라면 removeAll 메서드는 n에 대해 선형입니다.  
하지만, collection의 크기가 n에 비례한다면 removeAll 메서드는 이차입니다.  
  
반복 횟수가 항상 n에 비례하지 않는다면 좀 더 고민해봐야 합니다.  
    
## 3.4 연결 자료구조
자료구조가 연결되었다 함은 노드라는 객체들이 다른 노드에 대한 참조를 포함한 형태로 저장된 것을 의미합니다.  
연결 리스트에서 각 노드는 리스트의 다음 노드에 대한 참조를 포함합니다.  
연결 구조의 다른 예로는 트리와 그래프가 있습니다.  
이때 노드는 둘 이상의 다른 노드에 대한 참조를 포함합니다.  
  
```java
package listinterface;

public class ListNode {

	public Object data;
	public ListNode next;

	public ListNode() {
		this.data = null;
		this.next = null;
	}

	public ListNode(Object data) {
		this.data = data;
		this.next = null;
	}

	public ListNode(Object data, ListNode next) {
		this.data = data;
		this.next = next;
	}

	@Override
	public String toString() {
		return "ListNode(" + data.toString() +")";
	}
}
```
ListNode 객체에는 두 개의 인스턴스 변수가 있습니다. data 변수는 어떤 Object에 대한 참조고,  
next 변수는 리스트에서 다음 노드에 대한 참조입니다. 리스트의 마지막 노드에서 관례상 next 변수는 null입니다.  
```java
package listinterface;

public class LinkedListExample {

	public static void main(String[] args) {
		ListNode node1 = new ListNode(1);
		ListNode node2 = new ListNode(2);
		ListNode node3 = new ListNode(3);
		
		node1.next = node2;
		node2.next = node3;
		node3.next = null;
		
		ListNode node0 = new ListNode(0, node1);
	}
}

```
일련의 작업을 수행하고 나면 데이터로 정수 0, 1, 2, 3이 들어 있는 노드들이 오름차순으로 연결되었음을 알 수 있습니다.  
마지막 노드에서 next 필드는 null입니다.  
  
## 3.5 실습3
myLinkedList 클래스의 인스턴스 변수와 생성자는 다음과 같습니다.  
```java
public class MyLinkedList<E> implements List<E> {

  private int size; // 요소의 개수를 추적합니다
  private Node head; // 첫 번째 노드에 대한 참조입니다

  public MyLinkedList() {
    head = null;
    size = 0;
  }
}
```
size 변수는 myLinkedList에 있는 요소 개수를 추적하고, head 변수는 리스트의 첫 번째 노드를 참조하거나  
리스트가 비었으면 null입니다.  
  
요소 개수를 꼭 저장할 필요는 없습니다. 그리고 일반적으로 중복 정보를 유지하는 것은 위험한데,  
정보를 올바르게 갱신하지 않으면 오류가 생길 수 있기 때문입니다. 또한, 약간의 추가 공간을 차지하게 됩니다.  
  
하지만 size 변수를 명시적으로 지정하면 상수 시간으로 size 메서드를 구현할 수 있습니다.  
그렇지 않으면 리스트를 순회하여 요소 개수를 세는 선형 시간이 필요합니다.  
  
MyLinkedList에 중첩된 Node 클래스의 정의를 살펴보겠습니다.  
```java
private class Node {
  public E data;
  public Node next;

  public Node(E data, Node next) {
    this.data = data;
    this.next = next;
  }
}
```
필자가 구현한 add 메서드를 살펴보겠습니다.  
```java
public boolean add(E element) {
  if (head == null) {
    head = new Node(element);
  } else {
    Node node = head;
    // 마지막 노드까지 반복합니다.
    for ( ; node.next != null; node = node.next) {}
      node.next = new Node(element);
  }
  size++;
  return true;
}
```
이 예제는 문제를 푸는 데 필요한 두 가지 패턴을 보여줍니다.  
1. 많은 메서드에서 리스트의 첫 번째 요소를 특별한 경우로 처리해야 합니다. 이 예제에서는 리스트에 첫 번째 요소를  
추가하면 head 변수를 변경해야 합니다. 그렇지 않으면 리스트를 순회하여 끝을 찾아 새로운 노드를 추가해야 합니다.  
2. add 메서드는 for 문으로 리스트에 있는 노드를 순회하는 방법을 보여줍니다. 반복문에 앞서 node를 선언해야 반복이 끝난 후  
접근할 수 있음에 주의하세요.  
