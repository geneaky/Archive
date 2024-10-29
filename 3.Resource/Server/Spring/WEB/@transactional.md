
[transaction blog](https://mangkyu.tistory.com/269#:~:text=%5B%20%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98%20%EC%A0%84%ED%8C%8C%20%EC%86%8D%EC%84%B1(Transaction%20Propagation)%EC%9D%B4%EB%9E%80%3F%20%5D&text=%EC%9E%91%EC%97%85%EC%9D%84%20%ED%95%98%EB%8B%A4%EB%B3%B4%EB%A9%B4%20%EA%B8%B0%EC%A1%B4,%EC%A0%84%ED%8C%8C%20%EC%86%8D%EC%84%B1(Propagation)%EC%9D%B4%EB%8B%A4.)

스프링에서 제공하는 애너테이션 기반 기능으로 런타임 시점에 프록시를 통해서 기능이 추가되는 경우
스프링에서 프록시 객체 안에서 타켓 객체를 직접 호출해야하기 때문에 public으로 선언되어야 해당 기능이 적용이 되는데

@transactional에서 noRollbackFor을 통한 롤백 제외와는 별개로 내부에서 터져 나가는 RuntimeException은 잡아서 처리해주어야함(자바 동작이기때문)

별도의 트랜잭선으로 (propagation required new) 특정 메서드를 실행시켜 예외시 해당 트랜잭션만 버리는 방식으로 사용 가능

트랜잭션이 롤백이 될때 해당 트랜잭션을 더 이상 사용할 수 없다고 체크하기 때문에 runtime exception을 발생하는 메서드에서 해당 runtime exception에 대한 롤백을 하지 않겠다고 (포함된 트랜잭션도(@Transactional이 걸려있는 별도 클래스의 public method도 같은 트랜잭션에 포함되지만 별개의 트랜잭션 래퍼?를 띄고있음) 별도의 트랜잭션으로 생각하는 듯)

하나의 트랜잭션에서 runtime exception을 던지고 try catch로 바로 잡으면 해당 트랜잭션 정상처리 함
runtime exception이 현재 트랜잭션을 뚫고 나가야 외부 트랜잭션을 구성하고 있는 프록시에게 닿기 때문
-> 그래서 public method에 @Transactional을 붙여서 트랜잭션 함수 A를 만들고 별도의 트랜잭션 함수 B에서 A함수를 호출하는 상황에서 각각의 함수에서 터지는 예외를 각각의 함수에서 try-catch로 잡는 경우는 해당 예외가 트랜잭션 함수를 감싸는 프록시에 전파되지 않기 때문에
롤백되지 않지만 트랜잭션 함수 A에서 터진 예외를 트랜잭션 함수 B에서 잡는다면 이미 A 트랜잭션 함수를 감싸는 트랜잭션 동작을 지원하는 프록시에 rollback이 check되었기 때문에 예외를 잡더라도 롤백이 진행된다.



