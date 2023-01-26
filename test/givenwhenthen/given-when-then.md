# given-when-then
[GivenWhenThen : martin Fowler](https://martinfowler.com/bliki/GivenWhenThen.html)  
Behavior-Driven-Development(BDD)의 부분으로 Daniel Terhorst-North 와 Chris Matts 가 개발한 접근 방식입니다.  
핵심적인 아이디어는 시나리오(테스트) 작성을 세 부분으로 분해하는 것입니다.  
  
`given` : 시나리오(테스트)에서 지정하는 동작을 시작하기 전의 상태를 설명합니다. 테스트의 전제 조건이라고 생각할 수 있습니다.  
`when` :  사용자(당신)이 지정한 동작입니다.
`then` : 지정된 동작으로 인해 예상되는 변경 사항을 설명합니다.  
  
어떤 사람들은 단위 테스트 내에서 비공식 블록을 표시하기 위해 주석으로 Given-When-Then을 넣는 것을 좋아합니다.  
  
사전 상태에 대한 설명으로 `given` 절을 특정지었습니다.  
그러나 테스트 프레임워크는 when 명령을 실행하기 전에 테스트 중인 시스템을 올바른 상태로 가져오는 일련의 명령으로 givens를 해석합니다.  
테스트 프레임워크는 then명령에 대한 다양한 쿼리 메서드를 제공합니다. 이러한 메서드는 side-effect 가 없어야 합니다.
