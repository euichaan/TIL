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





