# 12월 12일
## c++ 함수
```c++
#include <algorithm>
void fill (ForwardIterator first, ForwardIterator last, const T& val);
```
- first : 채우고자 하는 자료구조의 시작위치 iterator 
- last : 채우고자 하는 자료구조의 끝위치 iterator이며 `last는 포함하지 않습니다`  
- val : first부터 last전까지 채우고자 하는 값으로 어떤 객체나 자료형을 넘겨줘도 템플릿 T에 의해 가능합니다  
