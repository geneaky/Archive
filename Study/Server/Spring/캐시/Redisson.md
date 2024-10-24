
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

unlock을 하기전 현재 thread가 lock을 획득한 thread인지 확인하는 로직을 넣는 예제를 많이 보았는데
