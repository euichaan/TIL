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
  
## Mock 객체 만드는 법
- Mockito.mock() 메서드로 만들기  
- @Mock 애노테이션으로 만들기(@ExtendWith(MockitoExtension.class) 클래스 레벨에 추가 필요)  
```java
MemberService memberService = mock(MemberService.class);
StudyRepository studyRepository = mock(StudyRepository.class);
```
  
```java
@ExtendWith(MockitoExtension.class)
class StudyServiceTest {

    @Mock MemberService memberService;

    @Mock StudyRepository studyRepository;
```
다음과 같이 파라미터로도 넣을 수 있다.  
```java
@ExtendWith(MockitoExtension.class)
class StudyServiceTest {
    
    @Test
    void createStudyService(@Mock MemberService memberService,
                            @Mock StudyRepository studyRepository) {
        StudyService studyService = new StudyService(memberService, studyRepository);
        assertNotNull(studyService);
    }

}
```
## Mock 객체 Stubbing(Mock 객체의 행동을 조작하는 것)
모든 Mock 객체의 행동  
- Null을 리턴한다. Optional은 Optional.empty로 나온다.  
- Primitive 타입은 기본 Primitive 값.  
- 컬렉션은 비어있는 컬렉션  
- void 메서드는 예외를 던지지 않고 아무 일도 발생하지 않는다.  
```java
when(memberService.findById(1L)).thenReturn(Optional.of(member));
```
  
## Stubbing 연습
```java
// TODO memberService 객체에 findById 메서드를 1L 값으로 호출하면 Optional.of(member) 객체를 리턴하도록 Stubbing
when(memberService.findById(1L)).thenReturn(Optional.of(member));

// TODO studyRepository 객체에 save 메소드를 study 객체로 호출하면 study 객체 그대로 리턴하도록 Stubbing
when(studyRepository.save(study)).thenReturn(study);
```
  
## Mockito BDD 스타일 API
BDD(Behavior Driven Development): 애플리케이션이 어떻게 "행동"해야 하는지에 대한 공통된 이해를 구성하는 방법으로, TDD에서 창안했다.  
Mockito는 BddMockito라는 클래스를 통해 BDD 스타일의 API를 제공한다.  
```java
given(memberService.findById(1L)).willReturn(Optional.of(member));
given(studyRepository.save(study)).willReturn(study);

then(memberService).should(times(1)).notify(study);
then(memberService).shouldHaveNoMoreInteractions();
```
