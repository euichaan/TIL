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
