
Spring에서 트랜잭션 관련 작업들을 동기화하는데 사용되는 유틸리티 클래스로, 현재 실행중인 트랜잭션의 상태를 관리하고 여러 트랜잭션 리소스 사이의 동기화를 담당한다.


- 트랜잭션 상태 관리 : 현재 실행중인 Thread의 트랜잭션 상태를 관리함, 트랜잭션 시작, 종료, 롤백과 같은 이벤트에 따라 트랜잭션 상태를 변경
- 트랜잭션 동기화 : 여러 트랜잭션 리소스 사이의 동기화를 담당한다. 이 클래스를 사용하여 트랜잭션 매니저, 데이터베이스 연결 등의 리소스 간에 동기화를 유지한다.
- 스레드 로컬 변수 사용 : ThreadLocal을 사용하여 현재 스레드의 트랜잭션 상태를 저장하고 유지한다.


##### 주요 메서드

- initSynchronization() : 트랜잭션 동기화를 초기화하고 현재 스레드의 트랜잭션 상태를 설정한다.
- bindResource() : 지정된 트랜잭션 리소스를 현재 스레드에 바인딩한다.
- unbindResource() : 현재 스레드에 지정된 트랜잭션 리소스를 언바인딩한다.
- isSynchronizatiionActive() : 현재 스레드의 트래잭션 동기화가 활성화되었는지 여부를 확인한다.
- getCurrentTransactionName() : 현재 실행중인 트랜잭션의 이름을 가져온다.
- isCurrentTransactoinReadOnly() : 현재 실행중인 트랜잭션이 읽기 전용인이 여부를 확인한다.


##### 사용 시 주의점

- 스프링 내부 클래스이므로 직접 인스턴스화 하지 말고, 정적 메서드를 사용하여 트랜잭션 동기화를 관리해야한다.
- ThreadLocal 변수를 직접적으로 조작한다면, 메서드 호출 순서와 스레드간의 동기화에 주의해야한다. 
- 트랜잭션 동기화를 시작한 후 적절하게 종료해야한다. 메모리 누수의 원인이 되기 때문