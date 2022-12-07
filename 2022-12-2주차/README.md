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
분산 락은 데이터베이스 등 공통된 저장소를 이용하여 자원이 사용 중인지를 체크한다. 그래서 전체 서버에서 동기화된 처리가 가능해진다.

# 12월 7일
## 간단한 분산 락 구현
레디스 클라이언트로 `Lettuce`를 사용하였습니다.  
  
1. 락을 획득한다는 것은 "락이 존재하는지 확인한다", "존재하지 않는다면 락을 획득한다" 두 연산이 atomic하게 이루어져야 합니다.  
Redis는 "값이 존재하지 않으면 세팅한다"라는 `setnx`명령어를 지원합니다. 이 `setnx`를 이용하여 Redis에 값이 존재하지 않으면 세팅하게 하고,  
값이 세팅되었는지 여부를 리턴 값으로 받아 락을 획득하는데에 성공했는지 확인합니다.  
2. try 구문 안에서 lock을 획득할때까지 계속 lock 획득을 시도합니다. Redis에 너무 많은 부하를 주지 않기 위해 잠깐의 sleep을 걸어두었습니다.  
3. lock을 획득한 후 핵심 로직을 실행합니다.  
4. lock 사용 후에는 꼭 해제하도록 `finally`에서 락을 해제해줍니다.  

```java
void doProcess() {
  String lockKey = "lock";

   try {
        while (!tryLock(lockKey)) { // (2) 
            try {
                Thread.sleep(50); // lock을 획득하지 못하면 약간의 sleep 
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
        
        // (3) do process
    } finally {
        unlock(lockKey); // (4)
    }
}
boolean tryLock(String key) {
  return command.setnx(key, "1"); //(1)
}
void unlock(String key) {
  command.del(key); 
}
```

## 분산 락 개발시 나타날 수 있는 문제점과 해결 방법 
### 1. Lock의 타임아웃이 지정되어있지 않다.
스핀 락을 구현했을시에 락을 획득하지 못하면 무한 루프를 돌게 됩니다. 만약 `tryLock`에 성공했는데 어떤 오류로 어플리케이션이 종료된다면?  
다른 모든 어플리케이션까지 영원히 락을 획득하지 못한 채 락이 해제되기만을 기다리는 무한정 대기상태가 되어 전체 서비스의 장애가 발생할 것입니다.  
  
그래서 일반적인 로컬 스핀 락과는 다르게 일정 시간이 지나면 락이 만료되도록 구현해야 합니다. 그러려면 `expire time`을 설정해줘야만 합니다.  
`setnx`명령어는 `expire time`을 지정할 수 없기에 이 문제를 해결하기 힘듭니다.  
  
또한 무한정으로 락의 획득을 시도한다면 문제가 될 수 있습니다. 만약 연산이 오래 걸릴 경우 대부분의 스레드가 락을 대기하는 상태가 되어 클라이언트에  
응답하는 속도가 늦어지고, 동시에 레디스에 엄청난 트래픽을 보낼 수 있기 때문입니다. 그래서 락을 획득하는 최대 허용시간을 정해주거나, 최대 허용 횟수를  
정해주는 것이 좋습니다. 만약 락 획득에 실패한다면 `Exception`을 던집니다.

### 2. tryLock 로직은 try-finally 구문 밖에서 수행해야 한다.
1번을 해결했다고 가정하면 특정 시간 혹은 횟수 내에 락을 획득하지 못하면 Exception이 발생하게 됩니다.  
  
`Exception`이 발생한다면 `finally`구문의 `unlock()`이 실행되어 락을 해제할 타이밍이 아닌데도 락을 해제시키기 때문에 작업이 수행중이더라도  
다른 곳에서도 연산을 수행할 수 있게되어 동기화를 보장할 수 없게됩니다.  
  
이 문제는 단순히 `try-finally`구문 밖에서 락 획득을 시도함으로써 해결할 수 있습니다.  
```java
int maxRetry = 3;
int retry = 0;

//==tryLock로직은 try-finally 구문 밖에서==//
while (!tryLock(lockKey)) {
    if (++retry == maxRetry) {
        throw new LockAcquisitionFailureException();
    }
    
    try {
        Thread.sleep(50);
    } catch (InterruptedException e) {
        throw new RuntimeException(e);
    }
}

try {
    // do Process
} finally {
  unlock(lockKey);
}
```
### 3. Redis에 많은 부하를 가하게 된다.
300ms가 걸리는 동기화된 작업에 동시에 100개의 요청이 왔다고 가정해보겠습니다.(분산 락이므로 서버의 대수는 무관합니다.)  
처음으로 락을 획득하는 데 성공한 1개의 요청은 제외하고, 나머지 99개의 요청은 작업이 완료되는 300ms동안 무려 594회의 락 획득 요청을 하게 됩니다.  
락 획득 실패 시 50ms만큼 쉬고 재요청하므로, 99 * (300/50) = 594회의 요청이 들어옵니다. 즉 1초 동안 약 2000회라는 많은 요청을 Redis에 보내게 됩니다.  
  
만약 레디스에 부담을 덜 주기 위해 sleep 시간을 300ms로 늘린다면 어떨까요? 50ms가 걸리는 작업에 이 동기화를 적용하면 락을 획득하지 못할 경우  
50ms 걸리는 작업을 하기 위해 300ms를 대기해야하는 다른 비효율적인 상황이 생기게 됩니다.  

## Redisson을 활용한 분산 락 
Redisson은 Jedis, Lettuce 같은 자바 Redis 클라이언트입니다.  
  
Redisson의 특이한 점은 직접 Redis의 명령어를 제공하지 않고, `Bucket`이나 `Map`같은 자료구조나 `Lock`같은 특정한 구현체의 형태로 제공한다는 것입니다.  
  
### 1.Lock에 타임아웃이 구현되어있습니다.

### 2.스핀 락을 사용하지 않습니다.

### Lua 스크립트를 사용합니다.