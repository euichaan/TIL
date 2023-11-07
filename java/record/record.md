# Java record
Java record를 사용하여 생성자를 초기화할 때, 레코드를 사용하면 각 필드에 대한 인수가 포함된 공용 생성자가 생성된다.  
따라서, Api에서 사용할 Response를 위해 다음과 같이 생성자를 만들 수 있다.  
```java
public record Response(

        String email,
        String firstName,
        String lastName,
        String password,
        String address1,
        String address2,
        String zip
) {

    public Response(Account account) {
        this(account.getEmail(), account.getFirstName(), account.getLastName(),
                account.getPassword(), account.getAddress1(), account.getAddress2(), account.getZip());
    }
}

```