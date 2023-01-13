# 1월 10일
## 배열의 성질
1. O(1)에 k번째 원소 확인/변경 가능  
2. 추가적으로 소모되는 메모리의 양(overhead)가 거의 없음  
3. Cache hit rate이 높다  
4. 메모리상에 연속한 공간을 잡아야 해서 할당에 제약이 걸린다  
  
임의의 위치에 있는 원소를 확인/변경 = O(1)  
원소를 끝에 추가 = O(1)  
마지막 원소를 제거 = O(1)  
임의의 위치에 원소를 추가/임의 위치의 원소 제거 = O(N)  
  
```c++
#include <bits/stdc++.h>
using namespace std;

//idx 위치에 num을 넣음
void insert(int idx, int num, int arr[], int& len){
  for (int i = len; i > idx; i--) {
    arr[i] = arr[i-1];
  }
  arr[idx] = num;
  len++;
}
//idx 위치에서 num 삭제
void erase(int idx, int arr[], int& len){
  for (int i = idx; i < len; i++) {
    arr[i] = arr[i+1];
  }
  len--;
}

void printArr(int arr[], int& len){
  for(int i = 0; i < len; i++) cout << arr[i] << ' ';
  cout << "\n\n";
}

void insert_test(){
  cout << "***** insert_test *****\n";
  int arr[10] = {10, 20, 30};
  int len = 3;
  insert(3, 40, arr, len); // 10 20 30 40
  printArr(arr, len);
  insert(1, 50, arr, len); // 10 50 20 30 40
  printArr(arr, len);
  insert(0, 15, arr, len); // 15 10 50 20 30 40
  printArr(arr, len);
}

void erase_test(){
  cout << "***** erase_test *****\n";
  int arr[10] = {10, 50, 40, 30, 70, 20};
  int len = 6;
  erase(4, arr, len); // 10 50 40 30 20
  printArr(arr, len);
  erase(1, arr, len); // 10 40 30 20
  printArr(arr, len);
  erase(3, arr, len); // 10 40 30
  printArr(arr, len);
}

int main(void) {
  insert_test();
  erase_test();
}
```
  
## vector
배열과 마찬가지로 원소가 동일하게 저장되어있기 때문에 O(1)로 각 원소 접근이 가능.  
배열과 달리 크기를 늘이거나 줄일 수 있는 장점.  
vector에서 = 를 사용하면 **deep copy**가 발생한다.  
```c++
#include <bits/stdc++.h>
using namespace std;
int main() {
  ios::sync_with_stdio(0);
  cin.tie(0);
  vector<int> v1(3, 5); // 5, 5, 5
  cout << v1.size() << '\n';
  v1.push_back(7); // 5, 5, 5, 7

  vector<int> v2(2); // 0, 0
  v2.insert(v2.begin() + 1, 3); // 0, 3, 0

  vector<int> v3 = {1, 2, 3, 4};
  v3.erase(v3.begin() + 2); // 1, 2, 4

  vector<int> v4;
  v4 = v3; // 1, 2, 4
  cout << v4[0] << v4[1] << v4[2] << '\n'; //124
  v4.pop_back(); //12
  v4.clear();// 
  return 0;
}
```
## range-based for loop
```c++
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;  //-2^63 ~ 2^63-1
typedef unsigned long long llu;
typedef pair<int, int> pii;
typedef pair<double, double> pdd;
typedef pair<int, pii> piii;
typedef pair<ll, ll> pll;
typedef pair<ll, int> pli;
typedef pair<int, ll> pil;
typedef pair<string, int> psi;
typedef pair<int, char> pic;
int INF = 1e9 + 7;
//512MB = 1.2억개 int
//if(nx<0||nx>=n||ny<0||ny>=m) continue;
/*int dz[6]={1,-1,0,0,0,0};
int dx[6]={0,0,1,-1,0,0};
int dy[6]={0,0,0,0,1,-1};*/ // 3차원 bfs
#define X first
#define Y second
int dx[4] = {1, 0, -1, 0};
int dy[4] = {0, 1, 0, -1};

int main() {
  ios::sync_with_stdio(0);
  cin.tie(0);
  vector<int> v1 = {1, 2, 3, 4, 5, 6};
  // 1.range-based for loop
  for (int e : v1) {
    cout << e << ' ';
  }
  // 2. for loop
  for (int i = 0; i < v1.size(); i++) {
    cout << v1[i] << ' ';
  }
  // 3. wrong
  for (int i = 0; i < v1.size() - 1; i++) {
    cout << v1[i] << ' ';
  }

  return 0;
}
```
기본적으로 vector의 size 메서드는 unsigned int 또는 unsigned long long을 반환합니다.  
그렇게 때문에 3번과 같이 사용하면 v1이 빈 벡터일 때 v1.size() - 1이 unsigned int 0에서 int 1을 빼는  
식이 되고, unsigned int로 자동 형변환이 일어나 (unsigned int)0 - (int)1은 -1이 아니라 4294967295 이 되어버립니다.  
  
그래서 아무 출력도 하지 않고 종료되는 것이 아닌 i가 계속 커지다가 런타임에러가 발생하게 됩니다.  
  
# 1월 10일
## 연결 리스트의 성질
1. k번째 원소를 확인/변경하기 위해 O(k)가 필요합니다.  
2. **임의의 위치에 원소를 추가/ 임의 위치의 원소 제거는 O(1).**    
3. 원소들이 메모리 상에 연속해있지 않아 Cache hit rate가 낮지만 할당이 다소 쉽습니다.  
  
C++ STL의 연결 리스트(list)는 이중 연결 리스트입니다.  
  
## 배열 vs 연결 리스트
k번째 원소의 접근의 경우 배열은 O(1), 연결 리스트는 O(k)입니다.  
두 번째로 임의 위치에 원소를 추가하거나 제거하는 건 배열의 경우 O(N), 연결 리스트의 경우 O(1)입니다.  
  
엄밀히 말하면, 연결 리스트에서도 3번째 원소 뒤에 20이라는 원소를 추가하고 싶다고 하면  
일단 3번째 원소까지는 찾아간 뒤에야 O(1)에 추가가 가능한거라 상황이 조금 다릅니다.  
  
연결 리스트에서는 각 원소가 다음 원소, 혹은 이전과 다음 원소의 주소값을 가지고 있어야 합니다.  
그래서 32비트 컴퓨터면 주소값이 32비트(=4바이트) 단위이면 4N 바이트가 추가로 필요하고, 64비트 컴퓨터라면  
주소값이 64비트(=8바이트) 단위이니 8N 바이트가 추가로 필요하게 됩니다. 즉, N에 비례하는 만큼의 메모리를 추가로 쓰게 됩니다.  
  
## 연결 리스트 기능과 구현 
### 임의의 위치에 있는 원소를 확인/변경, O(N)
첫 번째 원소의 주소만 알고 있으므로 네 번째 원소의 값을 확인하고 싶다고 할 때  
첫 번째 원소에 기록된 주소를 보고 두 번째 원소로 넘어가고, 두 번째 원소에 기록된 주소를 보고 세 번째 원소로 넘어가고,  
세 번째 원소에 기록된 주소를 보고 네 번째 원소로 넘어가서 도착합니다.  
  
그렇기 때문에 k번째 원소를 보기 위해서는 O(k)의 시간이 필요하고, N개의 원소가 있다면 평균적으로 N/2 , 즉 O(N)입니다.  
  
### 임의의 위치에 원소를 추가, O(1)
배열처럼 그 뒤의 원소를 전부 옮기는 작업을 할 필요가 없고 그냥 21과 84에서 다음 원소의 주소만 변경을 해주면 됩니다.  
단, **추가하고 싶은 위치의 주소를 알고 있을 경우에만 O(1)입니다.** 만약 주소를 모른다면 세 번째 원소까지 찾아가야 하는 시간이  
추가로 걸려서 O(1)이라고 말할 수 없습니다.  
  
### 임의 위치의 원소를 제거, O(1)

정리해보면, 임의의 위치에 있는 원소를 확인/변경 = O(N), 임의의 위치에 원소를 추가/임의 위치의 원소 제거 = O(1)  
임의의 위치에서 원소를 추가하거나 제거하는 연산을 많이 해야 할 경우 연결 리스트의 사용 고려.(ex:메모장)  
  
STL List -> 이중 연결 리스트 (Doubly Linked List 구조)  

## STL List
```java
int main() {
  ios::sync_with_stdio(0);
  cin.tie(0);
  list<int> L = {1,2}; // 1 2
  list<int>::iterator  t = L.begin();
  L.push_front(10); // 10 1 2
  cout << *t << '\n'; // 1
  L.push_back(5); // 10 1 2 5
  L.insert(t, 6); // 10 6 1 2 5 . t가 가리키는 곳 앞에 6을 삽입
  t++; // t가 가리키는 곳은 2
  t = L.erase(t); // 10 6 1 5 erase는 제거한 다음 원소의 위치를 반환 
  cout << *t << '\n'; // 5
  for (auto i : L) cout << i << ' ';
  cout << '\n';
  for (list<int>::iterator it = L.begin(); it != L.end(); it++) {
    cout << *it << ' ';
  }
  return 0;
}
```
insert는 iterator가 가리키는 곳 앞에 값을 삽입합니다. erase는 제거한 다음 원소의 위치를 반환합니다.  

## 손코딩 문제
1. 원형 연결 리스트 내의 임의의 노드 하나가 주어졌을 때 해당 List의 길이를 효율적으로 구하는 방법?  
=> 동일한 노드가 나올 때 까지 계속 다음 노드로 가면 됩니다. 공간복잡도 O(1), 시간복잡도 O(N)  
  
2. 중간에 만나는 두 연결 리스트의 시작점들이 주어졌을 때 만나는 지점을 구하는 방법은?  
일단 두 시작점 각각에 대해 끝까지 진행시켜서 각각의 길이를 구합니다.  
그 후 다시 두 시작점으로 돌아와서 더 긴 쪽을 둘의 차이만큼 앞으로 먼저 이동시켜놓고  
두 시작점이 만날 때 까지 두 시작점을 동시에 한 칸씩 전진시키면 됩니다. 공간복잡도 O(1), 시간복잡도 O(A+B)  

3. 주어진 연결 리스트 안에 사이클이 있는지 판단하는 방법  
=> Floyd's cycle-finding algorithm. 한 칸씩 가는 커서와 두 칸씩 가는 커서를 동일한 시작점에서 출발시키면  
사이클이 있을 경우 두 커서는 반드시 만나게 됩니다. 반대로 사이클이 없으면 두 커서가 만나지 않고 연결 리스트의 끝에 도달합니다.  
공간복잡도 O(1), 시간복잡도 O(N)  
  
# 1월 11일
## 연결 리스트 구현 : 단일 연결 리스트
```c++
#pragma once
#include <iostream>
struct Node {
    int data;
    struct Node* link;
};

struct HeadNode {
    Node* head;
};

class SingleLinkedlist {
public:
    //리스트 생성, 헤드 노드
    HeadNode* createList() {
      HeadNode *H = new HeadNode;
      H->head = nullptr;
      return H;
    }
    //==리스트의 마지막에 노드 삽입==//
    void addLastNode(HeadNode* H, int x) {
      Node* newNode = new Node;
      Node* lastNode;
      newNode->data = x;
      newNode->link = nullptr;
      if (H->head == nullptr) { //리스트가 비어있을 경우
         H->head = newNode;
        return;
      }
      //리스트가 비어있지 않을 경우
      lastNode = H->head;
      while (lastNode->link != nullptr) lastNode = lastNode->link; //연결리스트의 마지막 노드를 찾는다
      lastNode->link = newNode;
    }


    void deleteLastNode(HeadNode *H) {
      Node* prevNode; //삭제되는 노드의 이전 노드
      Node* deleteNode; //삭제되는 노드
      if (H->head == nullptr) return; //리스트가 빈 경우
      if (H->head->link == nullptr) { //헤드노드 한 개만 가진 경우
        delete H->head;
        H->head = nullptr;
        return;
      }
      else { //리스트에 노드 여러 개 있는 경우
        prevNode = H->head;
        deleteNode = H->head->link;
        while (deleteNode->link != nullptr) {
          prevNode = deleteNode;
          deleteNode = prevNode->link;
        }
        free(deleteNode); //마지막 노드의 메모리 공간을 반환
        prevNode->link = nullptr;
      }
    }
    void addThisNode(HeadNode* H, int afterThisData, int data) {
      Node* prevNode;//삽입하려는 노드의 이전 노드
      prevNode = H->head;

      while (prevNode->data != afterThisData) {
        prevNode = prevNode->link;
      }
      Node* newNode = new Node;
      newNode->data = data;
      newNode->link = prevNode->link;
      prevNode->link = newNode;
      return;
    }

    void deleteThisNode(HeadNode* H, int delData) {
      Node* deleteNode;
      Node* prevNode;
      prevNode = H->head;
      while (prevNode->link->data != delData) {
        prevNode = prevNode->link;
      }
      deleteNode = prevNode->link;
      prevNode->link = deleteNode->link;
      free(deleteNode);
      std::cout << delData << "데이터 값을 가진 노드 삭제 완료" << '\n';
    }

    void searchNode(HeadNode* H, int findData) {
      Node* findNode = H->head;
      while (findNode->data != findData) {
        findNode = findNode->link;
      }
      std::cout << findData << " 데이터 검색 성공" << '\n';
    }

    void printList(HeadNode* L) {
      Node* p;
      std::cout << "연결리스트 목록 = ( ";
      p = L->head;
      while (p != nullptr) {
        std::cout << p->data;
        p = p->link;
        if (p != nullptr) std::cout << " --> ";
      }
      std::cout << " )" << '\n';
    }
};
```

# 1월 12일 
## 객체지향의 사실과 오해 - 객체지향의 본질
+ 객체지향이란 시스템을 상호작용하는 자율적인 객체들의 공동체로 바라보고 객체를 이용해 시스템을 분할하는 방법이다.  
+ 자율적인 객체란 상태와 행위를 함께 지니며 스스로 자기 자신을 책임지는 객체를 의미한다.  
+ 객체는 시스템의 행위를 구현하기 위해 다른 객체와 협력한다. 각 객체는 협력 내에서 정해진 역할을 수행하며 역할은 관련된 책임의 집합이다.  
+ 객체는 다른 객체와 협력하기 위해 메시지를 전송하고, 메시지를 수신한 객체는 메시지를 처리하는 데 적합한 메서드를 자율적으로 선택한다.  
  
# 1월 13일 
## JUnit 5 배우기
[JUnit5란?](https://chan9.tistory.com/164)  
  
## 코딩테스트용 연결 리스트 구현
```c++
const int MX = 1000005;
int dat[MX], pre[MX], nxt[MX];
int unused = 1;
int main() {
  ios::sync_with_stdio(0);
  cin.tie(0);
  fill(pre, pre+MX, -1);
  fill(nxt, nxt+MX, -1);
  
  return 0;
}
```
dat[i]는 i번째 원소의 값, pre[i]는 i번째 원소에 대해 이전 원소의 인덱스, nxt[i]는 다음 원소의 인덱스입니다.  
pre나 nxt의 값이 -1이면 해당 원소의 이전/다음 원소가 존재하지 않는다는 의미입니다.  
unused는 현재 사용되지 않는 인덱스, 즉 새로운 원소가 들어갈 수 있는 인덱스이고 원소가 추가된 이후에는 1씩 증가될 것입니다.  
  
특별히 0번지는 연결 리스트의 시작 원소로 고정되어 있습니다. 0번지는 값이 들어가지 않고 시작점을 나타내기 위한 dummy node입니다.  
전체 코드는 다음과 같습니다.  
```c++
#include <bits/stdc++.h>
using namespace std;
//pre[i]는 이전 원소의 인덱스, nxt[i]는 다음 원소의 인덱스
const int MX = 1000005;
int dat[MX], pre[MX], nxt[MX];
int unused = 1;

void insert(int addr, int num){ //addr 번지 뒤에 num 추가
  dat[unused] = num; //새로운 원소를 생성
  pre[unused] = addr; //새 원소의 pre 값에 삽입할 위치의 주소를 대입
  nxt[unused] = nxt[addr]; //새 원소의 nxt 값에 삽입할 위치의 nxt 값을 대입
  if (nxt[addr] != -1) pre[nxt[addr]] = unused; //삽입할 위치의 nxt값과 삽입할 위치의 다음 원소의 pre값을 새 원소로 변경
  nxt[addr] = unused;
  unused++;
}

void erase(int addr){
    nxt[pre[addr]] = nxt[addr];
    if (nxt[addr] != -1) pre[nxt[addr]] = pre[addr];
}

void traverse(){
  int cur = nxt[0];
  while (cur != -1) {
    cout << dat[cur] << ' ';
    cur = nxt[cur];
  }
  cout <<"\n\n";
}

void insert_test(){
  cout << "****** insert_test *****\n";
  insert(0, 10); // 10(address=1)
  traverse();
  insert(0, 30); // 30(address=2) 10
  traverse();
  insert(2, 40); // 30 40(address=3) 10
  traverse();
  insert(1, 20); // 30 40 10 20(address=4)
  traverse();
  insert(4, 70); // 30 40 10 20 70(address=5)
  traverse();
}

void erase_test(){
  cout << "****** erase_test *****\n";
  erase(1); // 30 40 20 70
  traverse();
  erase(2); // 40 20 70
  traverse();
  erase(4); // 40 70
  traverse();
  erase(5); // 40
  traverse();
}

int main(void) {
  fill(pre, pre+MX, -1);
  fill(nxt, nxt+MX, -1);
  insert_test();
  erase_test();
}
```
## 스택
자료구조에서의 스택 : 한쪽 끝에서만 원소를 넣거나 뺄 수 있는 자료구조입니다.(프링글스 통과 비슷함)  
FILO(First In Last Out) : 구조적으로 먼저 들어간 원소가 제일 나중에 나오게 됩니다.  
스택, 큐, 덱을 묶어 Restricted Structure라고 부르기도 합니다.  
  
## 스택의 성질
1. 원소의 추가가 O(1)  
2. 원소의 제거가 O(1)  
3. 제일 상단의 원소 확인이 O(1)  
4. 제일 상단이 아닌 나머지 원소들의 확인/변경이 원칙적으로 불가능  
원래 스택이라는 자료구조는 원소의 추가/제거/제일 상단의 원소 확인이라는 기능만 제공하는 자료구조입니다.  
  
## 스택 구현 : 배열을 이용
```c++
const int MX = 1000005;
int dat[MX];
int pos = 0;
```
스택의 값들은 dat의 0번지, 즉 dat[0]부터 저장되고 pos는 다음에 원소가 추가될 때 삽입해야하는 곳을 가리킵니다.  
pos의 값이 스택의 길이, 즉 스택 내의 원소 수를 의미합니다.  
```c++
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;  //-2^63 ~ 2^63-1
typedef unsigned long long llu;
typedef pair<int, int> pii;
typedef pair<double, double> pdd;
typedef pair<int, pii> piii;
typedef pair<ll, ll> pll;
typedef pair<ll, int> pli;
typedef pair<int, ll> pil;
typedef pair<string, int> psi;
typedef pair<int, char> pic;
int INF = 1e9 + 7;
//512MB = 1.2억개 int
//if(nx<0||nx>=n||ny<0||ny>=m) continue;
/*int dz[6]={1,-1,0,0,0,0};
int dx[6]={0,0,1,-1,0,0};
int dy[6]={0,0,0,0,1,-1};*/ // 3차원 bfs
#define X first
#define Y second
int dx[4] = {1, 0, -1, 0};
int dy[4] = {0, 1, 0, -1};
const int MX = 1000005;
int dat[MX];
int pos = 0;
void push(int x){
  dat[pos++] = x;
}

void pop(){
  pos--;
}

int top(){
  return dat[pos-1];
}

void test(){
  push(5); push(4); push(3);
  cout << top() << '\n'; // 3
  pop(); pop();
  cout << top() << '\n'; // 5
  push(10); push(12);
  cout << top() << '\n'; // 12
  pop();
  cout << top() << '\n'; // 10
}

int main() {
  ios::sync_with_stdio(0);
  cin.tie(0);
  test();

  return 0;
}
```
## STL stack
```c++
#include <bits/stdc++.h>
using namespace std;
int main() {
  ios::sync_with_stdio(0);
  cin.tie(0);
  stack<int> s;
  s.push(10);
  s.push(20);
  s.push(30);
  cout << s.size() << '\n'; // 3
  if (s.empty()) cout << "S is empty\n";
  else cout <<"S is not empty\n";
  s.pop(); // 10 20
  cout <<s.top() << '\n'; //20
  s.pop(); // 10
  cout <<s.top() << '\n'; //10
  s.pop();
  if (s.empty()) cout << "S is empty\n";
  else cout <<"S is not empty\n";
  return 0;
}
```
스택이 비어있는데 top을 호출하면 런타임 에러가 발생합니다. 스택이 비어있는데 pop을 호출해도 마찬가지입니다.  
그렇기 때문에 스택이 비어있을 때는 top이나 pop을 호출하지 않도록 해야합니다.  

