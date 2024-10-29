[참고: 랜딧 개발 블로그](https://lenditkr.github.io/spring/transactional-event-listener/index.html)

- transaction과 연동되어 실행되는 eventlistener를 쉽게 정의할 수 있는 annotation
- event가 발생하면 TransactionApplicationListenerMethodAdaptor에 의해 해당 event가 event를 발생시킨 transaction이 지정된 [[transaction phase]]에서 실행되도록 구현되어 있음 >> 테스트 필요
- transaction phase는 annotation의 옵션으로 제공되며 default는 TransactionPhase.AFTER_COMMIT 이다. >> 해당 이벤트를 발생시킨 트랜잭션 메서드가 커밋된 이후 발생 >> event listener 처리 로직에서 에러핸들링 

##### 처리 절차 

1. @Transactional이 붙은 서비스 메서드를 실행 
2. proxy로 트랜잭션을 처리
	1. 트랜재션 관리를 위한 PlatformTransactionManager를 통해 트랜잭션을 생성후 트랜잭션 내에서 서비스 메서드를 실행
3. 트랜잭션 커밋
	1. 트랜잭션을 커밋하면(doCommit()) triggerXXX 형태의 메서드가 실행
	2. triger 시리즈는 `TransactionSynchronizationUtils` 에 정의된 invoke~ 시리즈 호출
4. `TransactionSynchronizationUtils` 추상 클래스 내부에 정의된 triggerAfterCompletion()을 메서드를 호출하는데 @TransactionEventListener를 통해 실행됨을 유추가능.
	1. 현재 트랜잭션에 등록된 [[TransactionSynchronization]]객체를 불러온 후 triggerAfterCompletion()를 실행시킨다
5.TransactionSynchronization의 구현체인 TransactionalApplicationListenerSynchronization의 afterCompletion을 실행시켜 이벤트 로직을 동작시킨다.

@TransactionEventListener는 이벤트 publish 시점에 listener를 읽어 TransactionalApplicationListenerMethodAdapter.onApplicationEvent()에서 TransactionApplicationListenerSynchronization 구현체를 등록한다

