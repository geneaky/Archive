트랜잭션 전후의 실행시킬 작업을 트랜잭션과 동기화시키기위한 인터페이스

> [!docs]
> 트랜잭션 동기화 콜백을 위한 인터페이스로 `AbstractPlatfromTransactionMaanger` 를 통해 실행된다.
> 실행 순서에 영향을 줄 수 있는 순차적인 인터페이스를 구현 가능
> `synchronization` 은 순차적 추상화를 구현하지 않고 단지 synchronization chain 끝단에 추가된다.
> 시스템 동기화는 스프링 자체의 특정 순서 값에 의해 동작하며 필요하다면 그 특정 순서 값들의 세밀한 상호작용을 따른다


#### 제공된 추상화 메서드

- beforeCommit
- beforeCompletion
- afterCommit
- afterCompletion


구현체에 따라 처리 방법이 다르며 [[@TransactionalEventListener]]는 `TransactionalApplicationListenerSynchronization` 라는 구현체를 사용한다.