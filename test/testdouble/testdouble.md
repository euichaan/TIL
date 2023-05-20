# Test Double
테스트를 진행하기 어려운 경우 이를 대신해 테스트를 진행할 수 있도록 만들어주는 객체  
  
테스트하려는 객체와 연관된 객체를 사용하기가 어렵고 모호할 때 대신해 줄 수 있는 객체를 테스트 더블이라 한다.  
## 테스트 더블의 종류
### 1. Dummy
- 가장 기본적인 테스트 더블이다.  
- 인스턴스화 된 객체가 필요하지만 기능은 필요하지 않은 경우에 사용한다.  
- Dummy 객체의 메서드가 호출되었을 때 정상 동작은 보장하지 않는다.  
- 객체는 전달되지만 사용되지 않는 객체이다.  
  
인스턴스화된 객체가 필요해서 구현한 가짜 객체일 뿐이고, 생성된 Dummy 객체는 정상적인 동작을 보장하지 않는다.  
```java
public interface PrintWarning {
  void print();
}

public class PrintWarningDummy implements PrintWarning {
  @Override
  public void print() {
     // 아무런 동작을 하지 않는다.
  }
}
```
실제 객체는 PrintWarning 인터페이스의 구현체가 필요하지만, 특정 테스트에서는 해당 구현체의 동작이 전혀 필요하지 않을 수 있다.  
실제 객체가 로그용 경고만 출력한다면 테스트 환경에서는 전혀 필요 없기 때문이다.  
이처럼 동작하지 않아도 테스트에는 영향을 미치지 않는 객체를 Dummy 객체라고 한다.  
  
### 2. Fake
- 복잡한 로직이나 객체 내부에서 필요로 하는 다른 외부 객체들의 동작을 단순화하여 구현한 객체이다.  
- 동작의 구현은 가지고 있지만 실제 프로덕션에는 적합하지 않은 객체이다.  
    
정리하면 동작은 하지만 실제 사용되는 객체처럼 정교하게 동작하지는 않는 객체를 말한다.  
```java
public class FakeUserRepository implements UserRepository {
    private Collection<User> users = new ArrayList<>();
    
    @Override
    public void save(User user) {
        if (findById(user.getId()) == null) {
            user.add(user);
        }
    }
    
    @Override
    public User findById(long id) {
        for (User user : users) {
            if (user.getId() == id) {
                return user;
            }
        }
        return null;
    }
}
```
테스트해야 하는 객체가 데이터베이스와 연관되어 있다고 가정해보자.  
그럴 경우 실제 데이터베이스를 연결해서 테스트해야 하지만, 실제 데이터베이스 대신 가짜 데이터베이스 역할을 하는 FakeUserRepository를 만들어 테스트 객체에 주입하는 방법도 있다. 이렇게 하면 테스트 객체는 데이터베이스에 의존하지 않으면서도 동일하게 동작을 하는 가짜 데이터베이스를 가지게 된다.  
  
이처럼 실제 객체와 동일한 역할을 하도록 만들어 사용하는 객체가 Fake이다.  
### 3. Stub
- Dummy 객체가 실제로 동작하는 것 처럼 보이게 만들어 놓은 객체이다.  
- 인터페이스 또는 기본 클래스가 최소한으로 구현된 상태이다.  
- 테스트에서 호출된 요청에 대해 **미리 준비해둔 결과**를 제공한다.  
  
정리하면 테스트를 위해 프로그래밍된 내용에 대해서만 준비된 결과를 제공하는 객체이다.  
  
```java
public class StubUserRepository implements UserRepository {
  //...
  @Override
  public User findById(long id) {
    return new User(id, "Test User");
  }
}
```
위의 코드처럼 StubUserRepository는 findById() 메서드를 사용하면 언제나 동일한 id값에 Test User라는 이름을 가진 User 인스턴스를 반환받는다.  
물론 이러한 방식의 단점은 테스트가 수정될 경우(findById() 메서드가 반환해야 할 값이 변경되야 할 경우) Stub 객체도 함께 수정해야 하는 단점이 있다.  
이처럼 테스트를 위해 의도한 결과만 반환되도록 하기 위핸 객체가 Stub이다.  
  
### 4.Spy
- Stub의 역할을 가지면서 호출된 내용에 대해 약간의 정보를 기록한다.  
- 테스트 더블로 구현된 객체에 자기 자신이 호출 되었을 때 확인이 필요한 부분을 기록하도록 구현한다.  
- 실제 객체처럼 동작시킬 수도 있고, 필요한 부분에 대해서는 Stub로 만들어서 동작을 지정할 수도 있다.  
  
정리하면 실제 객체로도 사용할 수 있고 Stub 객체로도 활용할 수 있으며 필요한 경우 특정 메서드가 제대로 호출되었는지 여부를 확인할 수 있다.  
```java
public class MailingService {
    private int sendMailCount = 0;
    private Collection<Mail> mails = new ArrayList<>();

    public void sendMail(Mail mail) {
        sendMailCount++;
        mails.add(mail);
    }

    public long getSendMailCount() {
        return sendMailCount;
    }
}
```
MailingService는 sendMail을 호출할 때마다 보낸 메일을 저장하고 몇 번 보냈는지를 체크한다. 그리고 나중에 메일을 보낸 횟수를 물어볼 때 sendMailCount 변수에 저장된 값을 반환한다.  
이처럼 자기 자신이 호출된 상황을 확인할 수 있는 객체가 Spy이다.  
Mockito 프레임워크의 verify() 메서드가 같은 역할을 한다.  
  
## 5. Mock
- 호출에 대한 기대를 명세하고 내용에 따라 동작하도록 프로그래밍 된 객체이다.  
  
```java
@ExtendWith(MockitoExtension.class)
public class UserServiceTest {
    @Mock
    private UserRepository userRepository;
    
    @Test
    void test() {
        when(userRepository.findById(anyLong())).thenReturn(new User(1, "Test User"));
        
        User actual = userService.findById(1);
        assertThat(actual.getId()).isEqualTo(1);
        assertThat(actual.getName()).isEqualTo("Test User");
    }
}
```
