# ConcurrentHashMap


# 개선된 ConcurrentHashMap
ConcurrentHashMap 클래스는 `동시성 친화적`이며 최신 기술을 반영한 HashMap 버전이다.  
ConcurrentHashMap은 **내부 자료구조의 특정 부분만 잠궈 동시 추가, 갱신 작업을 허용**한다.  
따라서 동기화된 Hashtable 버전에 비해 읽기 쓰기 연산이 월등하다(참고로, 표준 HashMap은 비동기로 동작함).  
  
ConcurrentHashMap은 스트림에서 봤던 것과 비슷한 종류의 세 가지 새로운 연산을 지원한다.  
- forEach : 각 (키, 값) 쌍에 주어진 액션을 실행  
- reduce: 모든 (키, 값) 쌍을 제공된 리듀스 함수를 이용해 결과로 합침  
- search : 널이 아닌 값을 반환할 때까지 각 (키, 값) 쌍에 함수를 적용  
  
다음처럼 네 가지 연산 형태를 지원한다.  
- 키, 값으로 연산(forEach, reduce, search)  
- 키로 연산(forEachKey, reduceKeys, searchKeys)  
- 값으로 연산(forEachValue, reduceValues, searchValues)  
- Map.Entry 객체로 연산(forEachEntry, reduceEntries, searchEntries)  
  
이들 연산은 ConcurrentHashMap의 상태를 잠그지 않고 연산을 수행한다는 점을 주목하자.  
따라서 이들 연산에 제공한 함수는 계산이 진행되는 동안 바뀔 수 있는 객체, 값, 순서 등에 의존하지 않아야 한다.  
  
또한 이들 연산에 병렬성 기준값(threshold)를 지정해야 한다. 맵의 크기가 주어진 기준값보다 작으면 순차적으로 연산을 실행한다.  
기준값을 1로 지정하면 공통 스레드 풀을 이용해 병렬성을 극대화한다.  
Long.MAX_VALUE를 기준값으로 설정하면 한 개의 스레드로 연산을 실행한다.  
  
고급 수준의 자원 활용 최적화를 사용하고 있지 않다면 기준값 규칙을 따르는 것이 좋다.  
  
int, long, double 등의 기본값에는 전용 each reduce 연산이 제공되므로 reduceValuesToInt, reduceKeysToLong 등을 이용하면 박싱 작업을 할 필요가  
없고 효율적으로 작업을 처리할 수 있다.  


