
[MySQL Docs](https://dev.mysql.com/doc/refman/8.0/en/innodb-consistent-read.html)

InnoDB가 multi versioning을 통해 특정 시점의 데이터베이스 스냅샷을 표현하는 것을 consistent read라고 하는데 
쿼리는 커밋되거 전의 시점은 확인하고 이후의 변화나 커밋되지 않은 트랜잭션의 변경사항은 확인하지 않는다.

isolation level이 default인 repeatable_read인 경우 동일 트랜잭션에서의 같은 스냅샷을 읽기 때문에 일관성을 보장해준다.

inno db에서는 consistent read가 read committed , repeatable read 격리레벨에서 기본 동작이다.

일관된 읽기에서는 조회시 락을 걸지 않아서 테이블에 접근하는 모든 세션에서 자유롭게 접근하여 수정을 할 수 있다.
