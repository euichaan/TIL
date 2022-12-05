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
### Pessimistic Lock
비관적 락을 건다고 생각하고 아예 트랜잭션을 시작할 때 `xxxRepository.findById`에 아래 옵션을 건다고 가정하자.  
```java
@Lock(value = LockModeType.PESSIMISTIC_WRITE)
@Query("select b from Board b where b.id = :id")
Stock findByIdWithPessimisticLock(Long id);
```
이 경우 `findById..`로 게시글을 찾을 때 `select ... from`이 아닌 `select ... for update`로 되고, 따라서 lock을 얻는다.  
다른 트랜잭션에서 `findById...`를 하기 위해서는 트랜잭션 1이 끝날 때 까지 대기해야 한다.  
**이렇게 트랜잭션에서 충돌이 날 것이라고 가정하고 미리 락을 걸어버리는 비관적 락 방법**으로 문제를 해결할 수도 있다.  
하지만 비관적 락은 지나치게 고립성이 높아 성능이 많이 저하된다는 단점이 존재한다.  

### Optimistic Lock
낙관적 락을 걸게 되면 트랜잭션1에서 해당 Version을 업데이트하면서 커밋을 하기 때문에 트랜잭션2 에서 동시에 작업을  
할 경우 커밋을 할 때 Version이 다르면 롤백되어 DB에 같은 좋아요가 2개 이상 쌓이지 않고 하나만 save되게 된다.  
따라서 Optimistic Lock으로도 해당 이슈를 충분히 해결할 수 있다. 또한 DB에 직접적으로 락을 거는 것이 아닌,  
애플리케이션 단에서 처리하는 것이므로 성능저하도 Pessimistic Lock에 비해 거의 일어나지 않는다고 판단했다.  
```java
  @Lock(value = LockModeType.OPTIMISTIC)
  @Query("select b from Board b where b.id = :id")
  Stock findByIdWithOptimisticLock(Long id);
```
또한 엔티티에 아래와 같이 @Version을 추가해주었다.  
```java
 @Version
  private Long version;// Optimistic Lock 을 위해 추가된 필드
```
이렇게 될 경우 아래처럼 진행된다.
|시퀀스|트랜잭션 1|트랜잭션 2|
|------|---|---|
|1|트랜잭션 1 시작||
|2||트랜잭션 2 시작|
|3|게시글 A 찾음, version = 0||
|4||게시글 A 찾음, version = 0|
|5|게시글 좋아요 기록 존재하지 않는가? true||
|6||게시글 좋아요 기록 존재하지 않는가? true|
|7|좋아요 수 증가, 게시글 좋아요 저장||
|8||좋아요 수 증가, 게시글 좋아요 저장|  
|9|version == 0 true, 트랜잭션 1 커밋, version += 1||  
|10||version == 0 false, 트랜잭션 **롤백**|  

### 추가로 생각해볼 점
외래키를 사용할 때 데드락이 발생할 수 있다. [외래키 사용 시 데드락](https://junghyungil.tistory.com/m/178)  
낙관적 락의 경우 기존에 존재하던 데이터의 경우 효과적이지만, 새로 추가되는 데이터는 컨트롤할 수 없다는 단점이 있다.  
낙관적 락 + 유니크 제약조건을 같이 거는 방법을 생각해 볼 수 있다.  
낙관적 락의 경우 동시성이 발생하고 영속성 컨텍스트가 flush 될 때 update 쿼리가 나가면서 OptimisticLockingFailureException이 발생한다.  

# 12월  2일
## Named Lock
이름을 가진 metadata locking. 이름을 가진 lock을 획득한 후 해제할때까지 다른 세션은 이 lock을  
획득할 수 없도록 한다. 주의할 점으로는 transaction이 종료될 때 lock이 자동으로 해제되지 않는다.  
별도의 명령어로 해제를 수행해주거나 선점시간이 끝나야 해제됩니다. Pessimistic lock과 비슷한데  
Pessimistic lock은 row나 table단위로 걸지만 Named lock은 metadata locking을 하는 방식.  




