# 12월 19일
## 시간, 공간복잡도 
컴퓨터는 대략 1초에 3-5억 개 정도의 연산을 처리할 수 있습니다. (난해)  
```c++
int func1(int arr[], int n) {
  int cnt = 0;
  for (int i = 0; i < n; i++) {
    if (arr[i] % 5 == 0) cnt++;
  }
  return cnt++;
}
```
arr[0]부터 arr[n-1]까지 돌면서 5로 나눈 나머지가 0이면, 즉 5의 배수이면 cnt 변수의 값을 1 증가시킵니다.  
몇 번의 연산을 수행하는지 살펴봅시다.  
```c++
int func1(int arr[], int n) {
  int cnt = 0; // 1번 
  for (int i = 0; i < n; i++) { //int i =0; 1번, 그 후 n 번에 걸쳐 반복 
  // i가 n보다 작은지 확인 1번, i 증가 1번 총 2번 
    if (arr[i] % 5 == 0) cnt++; // 5로 나눈 나머지 계산 1번, 0과 일치하는지 확인 2번, cnt 증가 총 3번
    // 2 + 3 = 5번 
  }
  return cnt++; // 1번 
}
```
1 + 1 + n * (2 + 2 + 1) + 1 = 5n + 3번의 연산이 필요하다는 것을 알 수 있습니다.  
n이 100만 정도였으면 대충 500만 번의 연산이 필요하니 1초 안에 충분히 돌아가고, n이 10억이었다면 50억 번 연산이  
필요하니 1초 안에 다 돌 수가 없습니다.  
  
보통 상수를 제외하고 n에 비례한다고 말합니다.  
  
`시간복잡도`의 정의는 **입력의 크기와 문제를 해결하는데 걸리는 시간의 상관관계를 의미**합니다.  
`O(1) < O(lgN) < O(N) < O(NlgN) < O(N²) < O(2ⁿ) < O(N!)`
```c++
#include <bits/stdc++.h>
using namespace std;

int func1(int N){
  int ret = 0;
  for (int i = 1; i <= N; i++) {
    if (i % 3 == 0 || i % 5 == 0) ret += i;
  }
  return ret;
}

int func2(int arr[], int N){
  for (int i = 0; i < N; i++) {
    for (int j = i + 1; j < N; j++) {
      if (arr[i] + arr[j] == 100) return 1;
    }
  }
  return 0;
}

int func3(int N){
  for (int i = 1; i*i <= N; i++) {
    if (i*i == N) return 1;
  }
  return 0;
}

int func4(int N){
  int val = 1;
  while (2 * val<=N) val *= 2;
  return val;
}

void test1(){
  cout << "****** func1 test ******\n";
  int n[3] = {16, 34567, 27639};
  int ans[3] = {60, 278812814, 178254968};
  for(int i = 0; i < 3; i++){
    int result = func1(n[i]);
    cout << "TC #" << i << '\n';
    cout << "expected : " << ans[i] << " result : " << result;
    if(ans[i] == result) cout << " ... Correct!\n";
    else cout << " ... Wrong!\n";
  }
  cout << "*************************\n\n";
}

void test2(){
  cout << "****** func2 test ******\n";
  int arr[3][4] = {{1,52,48}, {50,42}, {4,13,63,87}};
  int n[3] = {3, 2, 4};
  int ans[3] = {1, 0, 1};
  for(int i = 0; i < 3; i++){
    int result = func2(arr[i], n[i]);
    cout << "TC #" << i << '\n';
    cout << "expected : " << ans[i] << " result : " << result;
    if(ans[i] == result) cout << " ... Correct!\n";
    else cout << " ... Wrong!\n";
  }
  cout << "*************************\n\n";
}

void test3(){
  cout << "****** func3 test ******\n";
  int n[3] = {9, 693953651, 756580036};
  int ans[3] = {1, 0, 1};
  for(int i = 0; i < 3; i++){
    int result = func3(n[i]);
    cout << "TC #" << i << '\n';
    cout << "expected : " << ans[i] << " result : " << result;
    if(ans[i] == result) cout << " ... Correct!\n";
    else cout << " ... Wrong!\n";
  }
  cout << "*************************\n\n";
}

void test4(){
  cout << "****** func4 test ******\n";
  int n[3] = {5, 97615282, 1024};
  int ans[3] = {4, 67108864, 1024};
  for(int i = 0; i < 3; i++){
    int result = func4(n[i]);
    cout << "TC #" << i << '\n';
    cout << "expected : " << ans[i] << " result : " << result;
    if(ans[i] == result) cout << " ... Correct!\n";
    else cout << " ... Wrong!\n";
  }
  cout << "*************************\n\n";
}

int main(void){
  test1();
  test2();
  test3();
  test4();
}
```
공간복잡도는 **입력의 크기와 문제를 해결하는데 필요한 공간의 상관관계를 의미합니다.**  
예를 들어 크기 N짜리 2차원 배열이 필요하면 O(N²)이고, 따로 배열이 필요 없으면 O(1)이 됩니다.  

## Integer Overflow
char 자료형 기준으로 127에 1을 더하면, 컴퓨터는 시킨 대로 계산을 하기 때문에  
01111111(127) + 1 = 10000000 즉, -128이 됩니다.  
  
Integer Overflow를 막는 방법은 아주 쉽습니다. 각 자료형의 범위에 맞는 값을 가지게끔 연산을 시키면 됩니다.  

