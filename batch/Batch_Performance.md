# Batch Performance 극한으로 끌어올리기
[Batch Performance 극한으로 끌어올리기: 1억 건 데이터 처리를 위한 노력 / if(kakao)dev2022](https://www.youtube.com/watch?v=2IIwQDIi3ys)  
Batch는 일괄 처리를 의미한다.  
  
배치를 언제 사용할까?  
기존의 정보를 조합해서 새로운 정보를 만들 때 사용할 수 있다. (READ-CREATE-WRITE 순서)  
일괄 수정할 때 사용할 수 있다. (READ-UPDATE-WRITE 순서)  
통계 형식의 데이터를 만들 때 사용할 수 있다.  (주로 GroupBy, SUM, READ -> WRITE)  
  
그러나 Batch Performace 를 얼마나 신경써보았는가?  
  
## 대량 데이터 READ
대부분 배치에서 Reader가 차지하는 비중이 더 높다.  
예를 들어, 10억 개의 주문 정보 중 환불이 발생한 100만 개를 찾으라고 한다면?  
  
Batch에서 데이터를 읽는 절대적인 방법 = Chunk Processing  
대량 배치에서는 항상 Chunk Processing을 사용할 수 밖에 없다.  
-> 짝꿍으로 Pagination Reader를 사용한다.  
```sql
select *
from orders
where category = 'BOOK'
limit 3600, 100
```  
  
그러나 이러한 방식은 한계점이 존재한다.  
`select count(1) from orders where category = 'BOOK`이 1억 건 있다고 가정하자.  
`select * from orders where category = 'BOOK' limit 0, 100` 은 조회 속도가 매우 빠르지만  
`select * from orders where category = 'BOOK' limit 50000000, 100`은 조회 속도가 매우 느리다.  
  
Offset 이 커지면 커질수록 느려진다.  
이런 문제를 해결하기 위한 ZeroOffsetItemReader 를 만들었다.  
  
다른 방법으로, 데이터를 조금씩 가져오는 Cursor를 사용할 수 있다.  
JpaCursorItemReader는 데이터를 모두 읽고 서버에서 직접 Cursor하는 방식이기 때문에 OOM을 유발할 수도 있다. 사용해야 한다면 JdbcUrsorItemReader, HibernateCursorItemReader를 사용하자. 그러나 쿼리를 구현할 때 문자열 방식을 사용해야 한다.  
  
QueryDslZeroOffsetItemReader를 사용하면 쿼리 구현은 QueryDsl을 사용하고, PK를 where 조건에 추가해서 Offset을 항상 0으로 유지한다. 따라서 첫 Page를 읽었을 때와 동일하게 항상 일관된 조회 성능을 가진다.  
  
## 데이터 Aggregation 처리
Join + GroupBy + Sum 쿼리를 사용하면  
- 연산 과정이 쿼리에 의존적이라 Database 부하가 증가한다  
- 데이터가 누적돼 쿼리 실행 계획의 변경이 생겨 쿼리 튜닝 난이도가 증가한다  
- 쿼리 튜닝을 위한 과도한 인덱스가 추가된다  
  
GroupBy를 포기하자. 직접 Aggregation 하자.  
Redis를 사용한 방식 도입  

## 대량 데이터 WRITE
1. Batch Insert를 사용할 것, 즉 일괄로 쿼리 요청할 것  
2. 명시적 쿼리, 즉 필요한 컬럼만 Update 하고 영속성 컨텍스트를 사용하지 않는 것  
이런 점을 봤을 때 JPA는 단점이 많다.  
  
꼭 영속성 관리와 Dirty Checking이 필요한가..?  
Read할 때부터 Dirty Checking과 영속성을 버린다. (예를 들어 Querydsl Projections를 사용한다.)  
  
Update할 때 불필요한 다른 컬럼도 UPDATE한다.  
Dynamic UPDATE를 하기 위해 동적 쿼리를 생성해서 오히려 성능이 저하될 수 있다.  
  
JPA는 Batch Insert 지원이 어렵다. (지원하긴 하지만 ID 생성 전략을 IDENTITY로 하게 되면 불가능하다.)  
  
성능 개선을 위해서 쿼리를 모아서 처리하는 Batch Insert가 필수이다.  
Writer에서 JPA를 포기하고 Batch Insert하자.  
  



