# JUnit 
method 찾아보고 정리하기  

## Assumption
전제문이 true라면 실행, false라면 종료  
- assumeTrue : false일 때 이후 테스트 전체가 실행되지 않음  
- assumingThat : 파라미터로 전달된 코드블럭만 실행되지 않음  
```java
@Test
void dev_env_only() {
  assumeTrue("DEV".equals(System.getenv("ENV")), () -> "개발 환경이 아닙니다.");
  assertEquals("A", "A"); // 단정문이 실행되지 않음
}

@Test
void some_test() {
  assumingThat("DEV".equals(System.getenv("ENV")),
  () -> {
    assertEquals("A", "A"); // 단정문이 실행되지 않음
  });
  assertEquals("A", "A"); // 단정문이 실행된다.
}
```
