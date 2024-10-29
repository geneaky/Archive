
``` java
class LockClientTest extends IntegrationTest {

    private final LockClient lockClient;

    LockClientTest(LockClient lockClient) {
        this.lockClient = lockClient;
    }

    @Test
    @DisplayName("redis lock 획득 성공")
    public void getLock() throws Exception {
        int threadCount = 3;
        long blockingTime = 500;
        CountDownLatch countDownLatch = new CountDownLatch(threadCount);
        ExecutorService executorService = Executors.newFixedThreadPool(threadCount);

        long start = System.currentTimeMillis();
        for (int i = 0; i < threadCount; i++) {
            executorService.execute(() -> action(blockingTime, countDownLatch));
        }
        countDownLatch.await();
        long end = System.currentTimeMillis();

        assertThat(end + (blockingTime * threadCount)).isGreaterThanOrEqualTo(start + (blockingTime * threadCount));
    }

    private void action(long blockingTime, CountDownLatch countDownLatch) {
        Lock lock = lockClient.getLock("getLockTest");
        try {
            if (lock.tryLock(30, TimeUnit.SECONDS)) {
                Thread.sleep(blockingTime);
            }
        } catch (Exception ignore) {
            System.out.println(ignore.getMessage());
        } finally {
            countDownLatch.countDown();
            lock.unlock();
        }
    }
}
```

unlock을 하기전 현재 thread가 lock을 획득한 thread인지 확인하는 로직을 넣는게 best practice라는 [issue](https://github.com/redisson/redisson/issues/4878)
를 찾았다
하지만 RLock의 isHeldByCurrentThread()와 unlock()이 원자적으로 동작하지 않기 때문에 이는 옳은 방식은 아닌듯하다
isHeldByCurrentThread()가 있는 이유는 redisson 내부적으로 lock을 획득한 thread가 어떤 thread인지 알아야 같은 스레드가 중복으로 lock을 획득하는 일을 방지하기 위해, 확인을 위해 사용된다


### tryLock()의 leaseTime 있는 경우

RLock 구현체의 tryLock()을 실행하면 leaseTime을 인자로 설정할 수 있는데 이 값을 설정하면 얼마나 lock을 점유하고 있을지 고정적으로 설정이 가능하다.

만약 lock을 점유하고 있는 동안 task를 끝내지 못한 경우 unlock전에 leaseTime이 다 소요되어 `IllegalMonitorStateException`(RunTimeException) 예외가 발생하게된다.

### tryLock()의 leaseTime이 없는 경우 
tryLock()의 인자로 leaseTime을 설정하지 않은 경우 기본 -1이 설정되어 lock watch dog이 동작하게 되는데
default 30초 lock 점유 시간동안 현재 스레드가 작업을 종료하지 못한 경우 `RedissonBaseLock` 클래스의 `renewExpiration()`을 호출해서 leaseTime을 조금씩 늘려준다.
lock ttl을 늘려주는 네트워크 요청이 실패하는 경우 `CompletionException`(RunTimeException)이 발생되므로 TransactionRollback에도 영향이 없다.

```java
    @Test
    @DisplayName("redis watch dog은 thread가 아직 실행중인 경우 leaseTime을 늘려준다")
    public void withLeaseTime() throws Exception {
        int threadCount = 3;
        long blockingTime = 40000; // 40초 블록킹
        CountDownLatch countDownLatch = new CountDownLatch(threadCount);
        ExecutorService executorService = Executors.newFixedThreadPool(threadCount);

        long start = System.currentTimeMillis();
        for (int i = 0; i < threadCount; i++) {
            executorService.execute(() -> {
                RLock lock = redissonClient.getLock("withLeaseTimeTest");
                try {
                    if (lock.tryLock(1300000, TimeUnit.SECONDS)) { // lock wating 130초, lease time default 30초, blocking 40초 - 총 120 초
                        Thread.sleep(blockingTime);
                        lock.unlock();
                    }
                } catch (Exception ignore) {
                    System.out.println(ignore.getMessage());
                } finally {
                    countDownLatch.countDown();
                }
            });
        }
        countDownLatch.await();
        long end = System.currentTimeMillis();

        assertThat(end).isGreaterThanOrEqualTo(start + (blockingTime * threadCount));
    }

    @Test
    @DisplayName("redis lock 점유 시간 leaseTime을 명시한 경우 작업중인 task를 중단하고 lock을 해제한다")
    public void withoutLeaseTime() throws Exception {
        int threadCount = 3;
        long blockingTime = 40000; // 40초 블록킹
        long leaseTime = 30; // lock 점유 시간 30초
        CountDownLatch countDownLatch = new CountDownLatch(threadCount);
        ExecutorService executorService = Executors.newFixedThreadPool(threadCount);

        long start = System.currentTimeMillis();
        for (int i = 0; i < threadCount; i++) {
            executorService.execute(() -> {
                RLock lock = redissonClient.getLock("withoutLeaseTimeTest");
                try {
                    if (lock.tryLock(130, leaseTime, TimeUnit.SECONDS)) { // lock wating 130초, lease time 30초, blocking 40초 - 총 120초
                        Thread.sleep(blockingTime);
                        lock.unlock();
                    }
                } catch (Exception ignore) {
                    System.out.println(ignore.getMessage());
                } finally {
                    countDownLatch.countDown();
                }
            });
        }
        countDownLatch.await();
        long end = System.currentTimeMillis();

        assertThat(end).isLessThan(start + (blockingTime * threadCount));
    }

```

==출처==
[redisson base lock github](https://github.com/redisson/redisson/blob/3596f47cd601a588ce9c4dbfe4b2be5b8320d4ba/redisson/src/main/java/org/redisson/RedissonBaseLock.java#L118)
[stack over flow](https://stackoverflow.com/questions/70112709/rlocklocklong-leasetime-timeunit-unit-leasetime-lower-than-execution-time)