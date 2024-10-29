[api 문서](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/event/TransactionPhase.html) 에 따르면 transaction phase의 default값인 after commit으로 사용하게 되면
최초 호출 지점인 transaction이 commit되어 종료된 다음 @TransactinoalEventListener를 지정한 메서드가 체인 형태로 호출되기 때문에
@TransactionEventListener 메서드에서 트랜잭션을 사용해도 동작하지 않는다 따라서 사용시에 @Transactional(propagation = Propagation.REQUIRES_NEW)을 설정해 두어야 한다.

[[Transactional outbox pattern]] 작성 예정
[참고](https://findstar.pe.kr/2022/09/17/points-to-consider-when-using-the-Spring-Events-feature/)
