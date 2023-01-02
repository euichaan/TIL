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


  
