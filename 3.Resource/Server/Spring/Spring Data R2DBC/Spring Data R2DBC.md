R2DBC 기반의 스프링 데이터 프로젝트

- 스프링 데이터 프로젝트로서 스프링 애플리케이션과 쉽게 통합이 가능하며 스프링 데이터 JPA, 스프링 데이터 MongoDB같은 프로젝트처럼 추상화를 제공한다.
- [[spring webflux]]와 스프링 데이터 R2DBC를 같이 사용하면 전 구간 비동기-논블로킹 애플리케이션을 구현할 수 있다.
- ORM이 제공하는 LazyLoading, Dirty-Checking, Cache등을 지원하지 않는다.(ORM으로서의 기능으 적지만 심플하게 사용가능)


#### ReactiveCrudRepository
> `ReactiveCrudRepository` 는 리액티브를 지원하는 CRUD에 대한 인터페이스이다.
> 