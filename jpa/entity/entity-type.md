# 엔티티 필드에 Wrapper 타입 사용
[Hibernate ORM User Guide](https://docs.jboss.org/hibernate/orm/5.3/userguide/html_single/Hibernate_User_Guide.html#entity-pojo-identifier)

We recommend that you declare consistently-named identifier attributes on persistent classes and that you use a nullable (**i.e., non-primitive**) type.
primitive type의 기본 값은 0인데, 매핑된 값이 0일 때 실제 데이터가 정말로 존재하는지 여부를 알기 어렵다.  
예시를 들어보면, 0이 있다면 실제 값이 0인지 없어서 0인지 알 수 없다.  
데이터가 존재하면 null을 사용해 표현하는 것이 Hibernate의 방식이다.  
  
[영한님 의견](https://www.inflearn.com/questions/111851/기본값-타입에-대해서-강사님이-사용하시는-방법이-궁금-합니다)  
not null 제약조건이 필수로 걸리는 경우 primitive를 사용하고 null을 허용해야 하면 Wrapper를 사용한다.  
  
[JPA 책 147page]  
@Column을 생략하게 되면 대부분 @Column 속성의 기본값이 적용되는데, 자바 기본 타입일 때는 nullable 속성에 예외가 있다.  
```java
@Column(nullable = true) // 이게 기본값이다.
```
```sql
int data1;
data1 integer not null // DDL

Integer data2;
data2 integer // DDL

@Column // Column 적용, 자바 기본 타입
int data3;
data3 integer // DDL
```
int data1같은 자바 기본 타입에는 null 값을 입력할 수 없다. Integer data2처럼 객체 타입일 때만 null 값이 허용된다.  
  
JPA는 이런 상황을 고려해서 DDL 생성 기능을 사용할 때 int data1 같은 기본 타입에는 not null 제약조건을 추가한다. 그런데 data3처럼 @Column이 붙어있으면 nullable = true가 기본값이므로 not null 제약조건을 설정하지 않는다. 따라서 자바 기본 타입에 @Column을 사용하면 `nullable = false`로 지정하는 것이 안전하다.  
