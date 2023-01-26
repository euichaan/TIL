# Clean Code 9장 - 단위 테스트
## TDD : 실제 코드를 짜기 전에 단위 테스트부터 짜라고 요구  
첫째 법칙 : 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.  
둘째 법칙 : 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.  
셋째 법칙 : 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.  
  
하지만 실제 코드와 맞먹을 정도로 방대한 테스트 코드는 심각한 관리 문제를 유발하기도 한다.  
  
테스트 슈트가 없으면 개발자는 자신이 수정한 코드가 제대로 도는지 확인할 방법이 없다.  
테스트 코드는 실제 코드 못지 않게 중요하다.  
  
## **테스트 코드는 유연성, 유지보수성, 재사용성을 제공한다**  
테스트 케이스가 있으면 변경이 쉬워진다.  
  
깨끗한 테스트 코드를 만들려면? 가독성, 가독성, 가독성  
테스트 코드는 최소의 표현으로 많은 것을 나타내야 한다.  
  
BUILD-OPERATE-CHECK 패턴. 각 테스트는 명확히 세 부분으로 나눠진다.  
첫 부분은 테스트 자료를 만든다.  
두 번째 부분은 테스트 자료를 조작한다.  
세 번째 부분은 조작한 결과가 올바른지 확인한다.  
  
## 이중 표준 
테스트 API 코드에 적용하는 표준은 실제 코드에 적용하는 표준과 확실히 다르다.  
단순하고, 간결하고, 표현력이 풍부해야 하지만 실제 코드만큼 효율적일 필요는 없다.  
  
```java
@Test
public void turnOnLoTempAlarmAtThreashold() throws Exception {
  hw.setTemp(WAY_TOO_COLD);
  controller.tic();
  assertTrue(hw.heaterState());
  assertTrue(hw.blowerState());
  assertFalse(hw.coolerState());
  assertFalse(hw.hiTempAlarm());
  assertTrue(hw.loTempAlarm());
}
```
heaterState라는 상태를 보고서는 왼쪽으로 눈길을 돌려 assertTrue를 읽는다.  
coolerState라는 상태를 보고서는 왼쪽으로 눈길을 돌려 assertFalse를 읽는다. => 테스트 코드를 읽기가 어렵다.  
다음과 같이 변환해 코드 가독성을 높였다.  
```java
@Test
public void turnOnLoTempAlarmAtThreashold() throws Exception {
  wayTooCold();
  assertEquals("HBchL", hw.getState());
}
```
{heater, blower, cooler, hi-temp-alarm, lo-temp-alarm} 순서이며 대문자는 켜짐(on), 소문자는 꺼짐(off)을 뜻한다.  
  
다음과 같이 테스트 코드를 만들면 테스트 코드를 이해하기 너무도 쉽다는 사실이 분명히 드러난다.  
```java
@Test
public void turnOnCoolerAndBlowerIfTooHot() throws Exception {
  tooHot();
  assertEquals("hBChl", hw.getState());
}
...
```
getState함수는 다음과 같다.  
```java
public String getState() {
  String state = "";
  state += heater ? "H" : "h";
  state += blower ? "B" : "b";
  state += cooler ? "C" : "c";
  state += hiTempAlarm ? "H" : "h";
  state += loTempAlarm ? "L" : "l";
  return state;
}
```
테스트 환경은 자원이 제한적일 가능성이 낮다.  
## 테스트 당 assert 하나
JUnit으로 테스트 코드를 짤 때는 함수마다 assert문을 단 하나만 사용해야 한다고 주장하는 학파가 있다.  

## 테스트 당 개념 하나
이것저것 잡다한 개념을 연속으로 테스트하는 긴 함수는 피한다.  
가장 좋은 규칙  
1. 개념 당 assert 문 수를 최소로 줄여라  
2. 테스트 함수 하나는 개념 하나만 테스트하라  
  
## F.I.R.S.T
- Fast(빠르게) : 테스트는 빨라야 한다.  
- Idependent(독립적으로) : 각 테스트는 서로 의존하면 안 된다.  
- Repetable(반복가능하게) : 테스트는 어떤 환경에서도 반복 가능해야 한다.  
- Self-Validating(자가검증하는) : 테스트는 bool 값으로 결과를 내야 한다. 성공 아니면 실패다.  
- Timely(적시에) : 테스트는 적시에 작성해야 한다. **단위 테스트는 실제 코드를 구현하기 직전에 구현한다.**  
  
## 결론
테스트 코드는 실제 코드의 유연성, 유지보수성, 재사용성을 보존하고 강화한다.  
테스트 코드를 꺠끗하게 유지하자.  



