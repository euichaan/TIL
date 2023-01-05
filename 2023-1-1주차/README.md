# 1월 2일
## STL
```c++
vector<int> v(100);
v[20] = 10;
v[60] = -4;
```
vector는 일종의 가변배열로 크기를 마음대로 늘렸다 줄였다 할 수 있습니다.  
  
type이 int이고 0으로 초기화된 100칸짜리 가변배열 v가 선언되고, 02, 03번째 줄처럼 일반적인 배열처럼 인덱스에 접근이 가능합니다.  
## STL을 함수 인자로 넘길 때
```c++
void func1(vector<int> v) {
  v[10] = 7;
}

int main() {
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  vector<int> v(100);
  func1(v);
  cout << v[10];
  return 0;
}
```
답은 0입니다. STL도 구조체와 비슷하게 함수 인자로 실어 보내면 복사본을 만들어서 보내기 때문에  
func1 함수에서 바꾼 것은 원본에 영향을 주지 않습니다.  
```c++
bool cmp1(vector<int> v1, vector<int> v2, int idx) {
  return v1[idx] > v2[idx];
}
```
두 vector를 인자로 넘겨받아 idx번째 원소의 값을 비교한 결과를 반환하는 함수입니다.  
두 vector의 크기가 N이라고 할때 이 함수의 시간복잡도는 충격적이게도 **O(N)** 이 됩니다.  
v1, v2를 인자로 실어서 보낼 때 원본으로부터 복사본을 만드는 비용을 생각해야 합니다.  
v1, v2의 크기가 N이니까 N개의 원소들을 하나하나 복사하는 과정은 O(N)이 듭니다.  
  
```c++
bool cmp2(vector<int>& v1, vector<int>& v2, int idx) {
  return v1[idx] > v2[idx];
}
```
이럴 때 참조자를 이용할 수 있습니다.  
cmp가 호출될 때 복사본을 따로 만들어내지 않고 **참조 대상의 주소 정보만 넘어가기** 때문에  
시간복잡도는 의도한대로 O(1)이 됩니다.  
  
공백이 포함된 문자열을 받아야 할 때 단순하게 scanf나 cin을 쓰면 안됩니다.  
  
## ios::sync_with_stdio(0), cin.tie(0)
cin/cout의 입출력으로 인한 시간초과를 막기 위해서 두 명령을 실행시켜야 합니다.  
기본적으로 프로그램에서는 C++ stream과 C stream을 동기화하고 있습니다.  
따라서, C++ stream만 사용할 것이라면 굳이 두 stream을 동기화하고 있을 필요가 없습니다.  
  
동기화를 끊는 명령이 sync_with_stdio입니다.  
동기화를 끊었다면 cout과 printf를 섞어쓰면 안됩니다.  
  
추가적으로, endl은 개행문자를 출력하고 출력 버퍼를 비우라는 명령입니다.  
어차피 프로그램이 종료될 때 출력이 어떻게 생겼는지를 가지고 채점을 진행하니까 중간 버퍼를 지우라고 명령을 줄 필요가 없습니다.  
  
# 1월 4일 
## Optional
객체를 Optional 객체로 감싸기 위해서는 Optional에서 제공하는 of와 ofNullable 메서드를 사용합니다.  
둘의 차이점은 **of는 인자로서 null값을 받지 않는다는 것이고 ofNullable은 null값을 허용한다는 것입니다.**  
```java
@Test
  public void givenNonNull_whenCreatesNonNullable() throws Exception {
    String name = "Chan";
    Optional<String> opt = Optional.of(name);
    assertEquals("Optional[Chan]", opt.toString());
  }
```
아래 코드를 보면 null값을 of 메서드의 입력으로 받을 시 NullPointerException을 일으킵니다.  
```java
@Test
  public void givenNull_whenThrowsErrorOnCreate() throws Exception {
    String name = null;
    assertThrows(NullPointerException.class, () -> {
      Optional<String> opt = Optional.of(name);
    }); //assertThrows 에 필요한 클래스를 등록하고, 람다식으로 예외를 던질 실행문을 작성 
```
junit4의 @Test(expected = NullPointerException.class)와는 다르게 assertThrows에 필요한 클래스를 등록하고,  
람다식으로 예외를 던질 실행문을 작성해야 합니다.  
  
ofNullable은 일반 객체뿐만 아니라 null값까지 입력으로 받을 수 있다는 것을 아래 코드로 확인해 볼 수 있습니다.  
```java
@Test
public void givenNonNull_whenCreatesNullable() throws Exception {
  String name = "Chan";
  Optional<String> opt = Optional.ofNullable(name);
  assertEquals("Optional[Chan]", opt.toString());
}
  
@Test
public void givenNull_whenCreatesNullable() throws Exception {
  String name = null;
  Optional<String> opt = Optional.ofNullable(name);
  assertEquals("Optional.empty", opt.toString());
}
```
isPresent 메서드로 현재 Optional이 **보유한 값이 null인지 아닌지를 확인할 수 있습니다**.    
```java
@Test
  public void givenOptional_whenIsPresentWorks() throws Exception {
    Optional<String> opt = Optional.of("Chan");
    assertTrue(opt.isPresent());

    opt = Optional.ofNullable(null);
    assertFalse(opt.isPresent());
  }
```
Optional 메서드를 사용하면 if를 이용한 null 체크를 대체할 수 있습니다.  
if를 이용한 null 체크가 좋지 않은 이유는 크게 2가지가 있습니다.  
1. 코드가 길어짐에 따라 코드의 가독성이 떨어집니다.    
2. 각 변수마다 null을 체크해야하기 때문에 실수를 유발할 가능성이 높아집니다.  
  
if의 null 체크 방식을 다음과 같이 ifPresent로 간결하게 해결할 수 있습니다.  
```java
@Test
public void givenOptional_whenIfPresentWorks() throws Exception {
  Optional<String> opt = Optional.of("Chan");
  opt.ifPresent(name -> System.out.println(name.length())); //Consumer
}
```
참고로, ifPresent의 파라미터로는 Consumer<? super T> action 이 옵니다. Consumer는 T -> void 의 함수 디스크립터를 가집니다.  
  
## orElse, orElseGet으로 Optional 값 가져오기 
if에서 null값이 아닌 경우의 처리를 else 키워드 이하의 코드로 해결하지만 Optional에서는 orElse로 간단하게 해결할 수 있습니다.  
```java
@Test
public void whenOrElseWorks() throws Exception {
  String nullName = null;
  String name = Optional.ofNullable(nullName).orElse("Chan");
  assertEquals("Chan", name);
}
```
orElse와 orElseGet이 많이 사용되는 이유는 null값 체크를 할 수 있음과 동시에 null값일 경우 간단한 코드로 처리할 수 있어  
코드의 가독성이 좋아지고 코드 생산성이 올라간다는 장점이 있어서입니다.  
  
주의할 부분은 null일 때 어떤 값을 쓸 것이냐를 처리하는 로직에 함수를 사용했을 때입니다.  
orElseGet은 가지고 있는 값이 null일 경우에만 orElseGet에 주어진 함수를 실행합니다.  
하지만 orElse는 null값 유무와 상관없이 사용하게 되어있습니다.  
  
정리하면 다음과 같습니다.  
- orElse 메서드는 해당 값이 null 이든 아니든 관계없이 항상 불립니다.  
- orElseGet 메서드는 해당 값이 null 일 때만 불립니다.  
  
또한 get과 orElseThrow를 이용해서 Optional의 값을 얻을 수 있습니다.(대체로 권장X)  
```java
@Test
public void orElseThrowEx() throws Exception {
  String nullName = null;
  assertThrows(IllegalArgumentException.class, () -> {
  String name = Optional.ofNullable(nullName).orElseThrow(IllegalArgumentException::new);
  });
}
  
@Test
public void givenOptionalWithNull_whenGetThrowsException() throws Exception {
  Optional<String> opt = Optional.ofNullable(null);
  assertThrows(NoSuchElementException.class, () -> {
  String name = opt.get();
  });
}
```

## 예제
```java
  @Test
  public void givenOptional_whenMapWorks() throws Exception {
    List<String> companyNames = Arrays.asList("Samsung", "SK", "NAVER", "Daum");
    Optional<List<String>> listOptional = Optional.of(companyNames);

    int size = listOptional.map(List::size).orElse(0);
    assertEquals(4, size);
  }
  
  @Test
  public void givenOptional_whenMapWorks2() throws Exception {
    String name = "Chan";
    Optional<String> nameOptional = Optional.ofNullable(name);
    int len = nameOptional.map(String::length).orElse(0);
    assertEquals(4, len);
  }

  @Test
  public void givenOptional_whenMapWorksWithFilter() throws Exception {
    String password = " password ";
    Optional<String> passOpt = Optional.of(password);
    boolean correctPassword = passOpt.filter(
        pass -> pass.equals("password")).isPresent();
    assertFalse(correctPassword);

    correctPassword = passOpt
        .map(String::trim)
        .filter(pass -> pass.equals("password"))
        .isPresent();

    assertTrue(correctPassword);
  }
```
Optional.of는 null이 아님이 확실할 때만 사용, null이면 NPE가 터집니다.  
Optional.ofNullable은 null일 수도 있을 때만 사용해야 하며, null이 아님이 확실하면 of를 사용해야 합니다.  
  
# 1월 4일
백준 2234번 성곽 풀이
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
int dx[4] = {0, -1, 0, +1};
int dy[4] = {-1, 0, +1, 0};
int m, n; // m은 열, n은 행
int roomCnt, roomSize;
int board[52][52];
bool vis[52][52];

/**
 * 1. 이 성에 있는 방의 개수
 * 2. 가장 넓은 방의 크기
 * 3. 하나의 벽을 제거하여 얻을 수 있는 가장 넓은 방의 크기
 */
int bfs(int i, int j) {
  queue<pair<int, int>> q;
  q.push({i, j});
  vis[i][j] = 1;
  int size = 1;
  while (!q.empty()) {
    auto cur = q.front();
    q.pop();
    for (int dir = 0; dir < 4; dir++) {
      int nx = cur.X + dx[dir];
      int ny = cur.Y + dy[dir];
      if (nx < 0 || ny < 0 || nx >= n || ny >= m) continue;
      if (vis[nx][ny]) continue; // 이미 방문한 좌표. 건너뜀
      if (board[cur.X][cur.Y] & 1 << dir) continue; // 벽이 있다면 건너뜀
      vis[nx][ny] = 1;
      q.push({nx, ny});
      size++;
    }
  }
  return size;
}

int main() {
  ios::sync_with_stdio(0);
  cin.tie(0);
  cin >> m >> n;
  for (int i = 0; i < n; i++) {
    for (int j = 0; j < m; j++) {
      cin >> board[i][j];
    }
  }
  for (int i = 0; i < n; i++) {
    for (int j = 0; j < m; j++) {
      if (!vis[i][j]) {
        roomSize = max(roomSize, bfs(i, j));
        roomCnt++;
      }
    }
  }
  cout << roomCnt << '\n';
  cout << roomSize << '\n';

  for (int i = 0; i < n; i++) {
    for (int j = 0; j < m; j++) {
      for (int dir = 0; dir < 4; dir++) {
        memset(vis, false, sizeof(vis));
        board[i][j] = board[i][j] - (1 << dir);
        roomSize = max(roomSize, bfs(i, j));
        board[i][j] = board[i][j] + (1 << dir);
      }
    }
  }
  cout << roomSize << '\n';
  return 0;
}
```  
  
# 1월 5일
## Collectors의 toMap()
Book 클래스 정의는 다음과 같습니다.  
```java
package com.me.modernJavainAction.Collectors;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.Setter;

@Getter @Setter
@AllArgsConstructor
public class Book {
  private String name;
  private int releaseYear;
  private String isbn;

}
```
그리고 책 목록을 만들었습니다.  
```java
List<Book> bookList = new ArrayList<>();
    bookList.add(new Book("The Fellowship of the Ring", 1954, "0395489318"));
    bookList.add(new Book("The Two Towers", 1954, "0345339711"));
    bookList.add(new Book("The Return of the King", 1955, "0618129111"));
```
먼저, toMap() 메서드의 다음 오버로드를 사용합니다.  
```java
Collector<T, ?, Map<K,U>> toMap(Function<? super T, ? extends K> keyMapper,
  Function<? super T, ? extends U> valueMapper)
```
이 메서드를 활용해서 맵의 key와 value를 얻는 방법을 알 수 있습니다.  
```java
public Map<String, String> listToMap(List<Book> books) {
    return books.stream().collect(Collectors.toMap(Book::getIsbn, Book::getName));
  }
```
그리고 test 코드를 통해서 그것이 작동하는지 확인할 수 있습니다.  
```java
@Test
public void whenConvertFromListToMap() {
    assertTrue(convertToMap.listToMap(bookList).size() == 3);
}
```











  
