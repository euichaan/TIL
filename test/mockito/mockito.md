## Mockito
Mock: 진짜 객체와 비슷하게 동작하지만 프로그래머가 직접 그 객체의 행동을 관리하는 객체  
Mockito: Mock 객체를 쉽게 만들고 검증할 수 있는 방법을 제공한다.  
  
데이터베이스, 외부 API 등을 호출한다고 가정하면 그 외부 API를 사용하면서 테스트할 수 없기 때문에 예측해서, Mock으로 만들어서 테스트한다.  
컨트롤하기 힘든 외부 서비스는 Mocking을 하는 것이 좋다고 생각.  
  
- [단위 테스트에 대한 고찰](https://martinfowler.com/bliki/UnitTest.html)  
- [Mockito 레퍼런스](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html)  
  
스프링 부트 2.2+ 프로젝트 생성시 spring-boot-starter-test에서 자동으로 Mockito 추가해 줌.  
  
다음 세 가지만 알면 Mock을 활용한 테스트를 쉽게 작성할 수 있다.  
- Mock을 만드는 방법  
- Mock이 어떻게 동작해야 하는지 관리하는 방법  
- Mock의 행동을 검증하는 방법  
  