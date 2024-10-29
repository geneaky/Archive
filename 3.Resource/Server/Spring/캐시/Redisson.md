
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


RLock 구현체의 tryLock()을 실행하면 leaseTime을 인자로 설정할 수 있는데
lock을 획득 후 process를 실행하는 기간이 길어져 아직 process 처리중에 leaseTime이 끝난 경우 redisson 내부적으로 watch dog이 현재 lock을 점유한 스레드가 활성 상태인지 확인하여 leasetime을 늘려준다
tryLock의 인자로 leaseTime을 설정하지 않으면 기본  `-1` 이 설정되어 watchdog으로 동작한다
