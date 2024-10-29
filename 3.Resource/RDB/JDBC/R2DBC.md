
#### 1.1 R2DBC 이전
전통적인 방식의 JDBC(Java Database Connectivity) 드라이버는 하나의 커넥션에 하나의 스레드를 사용하는 Thread Per Connection 방식이다.

> Thread Per Connection 방식은 데이터베이스로 부터 응답을 받기 전까지 스레드가 블로킹이된다.
> 높은 처리량과 대규모 애플리케이션을 위해 비동기-논블로킹 데이터베이스 API에 대한 요구사항이 생기게되면
> 애플리케이션 로직이 비동기-논블로킹 이더라도 DB 드라이버가 JDBC라면 필연적으로 블로킹이 발생하므로 100% 비동기-논블로킹의 성능을 내기 어렵다.

#### 1.2 R2DBC
R2DBC(Reactive Relational Database Connectivity)는 리액티브 기반의 비동기-논블로킹 데이터베이스 드라이버이다.
지원 DB
- Oracle
- Postgres
- H2
- MSSQL
- MariaDB
리액티브 스트림 구현체인 Project Reactor, RxJava등을 지원한다.

