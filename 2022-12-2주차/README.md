# 12월 5일
## Redisson을 사용한 분산 Lock
pub-sub 기반으로 lock 구현을 제공한다.  
pub-sub 기반은, 채널을 하나 만들고 lock을 점유중인 스레드가 lock을 획득하려고 대기 중인 스레드에게 해제를  
알려주면 안내를 받은 스레드가 lock 획득 시도를 하는 방식이다. 이 방식은 Lettuce와는 다르게 별도의  
Retry 로직을 작성하지 않아도 된다.  
```java
@Component
public class RedissonLockStockFacade {

  private RedissonClient redissonClient;

  private StockService stockService;

  public RedissonLockStockFacade(RedissonClient redissonClient, StockService stockService) {
    this.redissonClient = redissonClient;
    this.stockService = stockService;
  }

  public void decrease(Long key, Long quantity) {
    RLock lock = redissonClient.getLock(key.toString()); //key를 활용해서 lock 객체를 가져온다.

    try {
      boolean available = lock.tryLock(5, 1, TimeUnit.SECONDS);

      if (!available) { //lock 획득 실패 시
        System.out.println("lock 획득 실패");
        return;
      }
      //==정상적으로 lock 획득==//
      stockService.decrease(key, quantity);
    } catch (InterruptedException e) {
      throw new RuntimeException(e);
    } finally {
      lock.unlock(); //lock 해제 
    }
  }
}
```
# 12월 6일
## 락이란? 스핀 락, 분산 락에 대해서 정리
[락이란 무엇일까..?](https://chan9.tistory.com/159)  
Pessimistic Lock과 Optimistic Lock의 관심사는 `특정 엔티티에 대한 동시 접근 제어`이다.  
즉, 동시성을 제어하기 위한 엔티티가 존재해야 한다.  

일반적으로 자바에서는 공유된 자원에 여러 스레드가 접근할 때 락을 사용한다.  
하지만 여러 서버를 운영하는 분산환경에서는 한 서버에 락이 걸려있더라도 다른 서버로 동일한 요청이 가게 된다면 동기화를 보장할 수 없다.  
분산 락은 데이터베이스 등 공통된 저장소를 이용하여 자원이 사용 중인지를 체크한다. 그래서 전체 서버에서  
동기화된 처리가 가능해진다.

