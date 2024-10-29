
@EnableAsnyc를 통해 해당 애네터이션을 사용한 비동기 처리 방식을 설정한다.

public 메서드에 붙여 해당 메서드의 태스크를 비동기로 처리할 수 있다.
AsyncExecutionAspectSupport에 실행됩니다.

별도의 thread pool를 생성하지 않으면 기본 동작 방식인 `SimpleAsyncTaskExecutor` 를 사용해서 동작

#### SimpleAsyncTaskExecutor

- 각 작업마다 새로운 스레드를 생성하고 비동기 방식으로 동작
- concurrencyLimit 프로퍼티를 사용해서 요청 수 제한 (default는 unlimit)
- 스레드를 재사요하지 않아서 스레드 생성, 작업할당에 오버헤드가 있음

### multiple thread pool

스레드 풀을 여러개 등록하는 경우 하나의 @Primary 스레드 풀을 @Bean으로 등록 후 나머지 스레드 풀을 빈으로 등록해서 @Async 실행시 애너테이션 안에 threadPoolExecutor의 이름을 지정하여 해당 스레드 풀을 사용하여 비동기 동작하도록 할 수 있다

### ThreadPoolExecutor

기본적으로 정해진 core pool size만큼 평소 유지가 되지만 요청 수 대비 스레드 수가 부족해지면
내부 queue에 요청을 넣는다.
queue size는 queue capacity 옵션으로 설정할 수 있다
queue size보다 요청 수가 많아진 경우라면 pool size를 늘리는데 max pool size만큼 늘어날 수 있다.

이후 요청수가 적어진다면 생성한 스레드 만큼 pool size를 유지하는 것은 낭비이므로 keepAliveTime만큼 대기 후 스레드를 정리해서 다시 core pool size를 유지하게 된다