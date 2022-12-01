# 12월 1일
## 프로젝트 동시성 이슈 해결을 위한 락 걸기. Pessimistic Lock? Optimistic Lock?
게시판의 좋아요를 엄청 빠르게 클릭했을 때, 에러 페이지가 뜨는 것을 목격했다.  
즉, `동시성 이슈`로 발생한 문제였다.  

상황을 표로 정리해보자면 아래와 같다.  
|시퀀스|트랜잭션 1|트랜잭션 2|
|------|---|---|
|1|게시글 A 찾음||
|2||게시글 A 찾음|
|3|좋아요 기록 존재하지 않는가? true||
|4||좋아요 기록 존재하지 않는가? true|
|5|게시글 좋아요 저장||
|6||게시글 좋아요 저장|
|7|트랜잭션 1 Commit||
|8||트랜잭션 2 Commit|  
  
원인은 3, 4번에 있는데 두 개의 트랜잭션이 검사를 했을 때 기록이 존재하지 않는가? 에서 둘다 TRUE를 반환했기 때문이었다.  

트랜잭션 1에서 게시글 A를 찾는 과정은 `select ... for update`가 아닌 `select ... from`이다. 따라서 락을 얻는 과정이 발생하지 않는다.  
트랜잭션 2에서 게시글 A를 찾는 과정 역시 단순 `select`이므로 트랜잭션1에서 락을 얻든 얻지 않든 접근이 가능하다.  

충분히 동시 접근이 가능한 쿼리이고, 좋아요를 과클하는 경우에 좋아요 기록이 존재하지 않는가? 과정을 거칠 때  
두 트랜잭션에서 모두 true가 나오게 돼 DB에 둘 다 저장되게 된다.  

## Pessimistic Lock(비관적 락)과 Optimistic Lock(낙관적 락)  
비관적 락을 건다고 생각하고 아예 트랜잭션을 시작할 때 `xxxRepository.findById`에 아래 옵션을 건다고 가정하자.  
```java
@Lock(value = LockModeType.PESSIMISTIC_WRITE)
@Query("select b from Board b where b.id = :id")
Stock findByIdWithPessimisticLock(Long id);
```
이 경우 `findById..`로 게시글을 찾을 때 `select ... from`이 아닌 `select ... for update`로 되고, 따라서 lock을 얻는다.  
다른 트랜잭션에서 `findById...`를 하기 위해서는 트랜잭션 1이 끝날 때 까지 대기해야 한다.  
<u>이렇게 트랜잭션에서 충돌이 날 것이라고 가정하고 미리 락을 걸어버리는 비관적 락 방법</u>으로 문제를 해결할 수도 있다.  
하지만 비관적 락은 지나치게 고립성이 높아 성능이 많이 저하된다는 단점이 존재한다.  




