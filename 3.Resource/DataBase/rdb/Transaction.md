database를 변경시키기위한 논리 단위로 생각을 하는데

특정 목적을 위해(입금, 출금) 데이터베이스 테이블에 변경을 시도하게되는데 
시도 과정 중간 지점에 예외가 발생했을 때 이전까지 작업한 내용들도 함께 롤백 시킬 수 있어야 database의 consistency를 유지할 수 있기 때문이다.

데이터베이스에서 (혹은 db client - ex: dbiver) 직접 쿼리를 수행할 때는 기본 default값이 autocommit true인데 (아닌 dbms가 있는지는 모르겠음 일단 mysql 기준) 

java의 jdbc를 통해 쿼리를 수행하는 경우 connection의 autocommit을 false로 지정하면 transaction이 시작되고 이후 commit이 될때까지의 로직을 진행하게되는데 진행 과정에서 발생한 예외는 개발자가 직접 예외처리(재시도를 하던 , 500응답을 보내던)를 하게되는데 
spring의 @Transactional을 사용하게되면 별도의 부가로직 필요없이 핵심 로직에만 집중하여 코드를 작성할 수 있게된다. 

@Transaction을 사용하더라도 동시성문제는 처리해주어야하는데 
optimistic lock, pessimistic lock, namedlock, 분산락을 사용하여 동시성 문제를 처리할 수 있는데

optimistic lock은 테이블에 version column을 추가하여 race condition이 일어났을 때 버전을 확인하여 수정을 시도한 번호보다 database 테이블 row에 버전이 더 높은 경우 예외를 발생시켜 정책에 따라 예외를 처리
(재시도, 실패 등등))

read skew
A,B tx가 진행중일때
A tx에서 조회한 데이터가 이후 B tx에서 update -> commit 후 다시 A tx에서 read를 하게되면
처음 read한 데이터와 두 번째 read한 데이터가 다른 상황을 read skew라고 하고 non-repeatable-read상황에서 발생

write skew
Post와 Post_Detail(두 객체가 연관이되어 있는 Post를 작성한 사람이 Post_Detail을 작성한 사람과 동일인물)
A,B tx가 진행중일 때
B tx에서 Post 객체를 update하고 A tx에서 Post Detail객체를 update하면 Post와 PostDetail이 서로 Sync가 보장될 수 없음

서로다른 트랜잭션에서 동일한 객체를 update하는 경후 한 쪽이 무시되는 경우가 lost update인데
이 lost update 대처 방법은 아래와 같다.
1. 마지막 커밋만 인정 -> 기본
2. 최초 커밋만 인정 -> 버저닝(Optimistic lock)
3. 두 커밋 병합 -> application level에서 직접 구현

MySQL

innodb에서 record락을 사용하는데 index record에 락을 거는데 index가 없어도 clustered index를 생성해서 사용하기때문에
여기에 record락을 건다.

insert, update, delete에 대해서는 항상 동시성 문제를 고려해보자

mysql에서 일반 select는 mvcc로 일관된 읽기를 제공함
x,s lock을 통한 select는 mvcc와 달리 현재 가장 최신의 데이터를 가져옴
innodb 락킹 기법으로 record lock , gap lock, next-key(record + gap) lock 이 있음

일반적인 select는 non-locking-read로 mvcc로 동작


x-lock : FOR UPDATE (다른 세션에서 락을 가져가지 못하게 막는 락)
s-lock: LOCK IN SHARE MODE (여러 세션에서 동일한 데이터를 읽기 위해 inert, update, delete를 막는 락)

clustered index, secondary index
ACID 다시 정리

isolation level serializable에서
s락걸고 찾은다음 x락걸로 update칠때 dead락 걸리는거 확인
