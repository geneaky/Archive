[MySQL Docs](https://dev.mysql.com/doc/refman/8.0/en/innodb-locks-set.html)

locking read, update, delete는 일반적으로 레코드 락이 걸린다.
row을 제외하는 구문이 where 조건이 있는것은 상관없이 index range scan을 함

lock은 일반적으로 next key lock인데 `gap` 공간에 insert를 막는다.

격리레벨이 `serializable`이 아니면 일반적으로 일관된 읽기로 동작을 하는데 serializable level에서는 
검색같은 경우 shared next-key lock을 인덱스 레코드에 건다. 
하나의 인덱스 레코드 락이 구문의  필요한 정보인데 그 구문에서 unique row을 검색하기 위한 unique index에 잠금을 건다.

select for update나 select for share 구문의 경우 유니크 인덱스를 사용하여 row를 스캔하기 위해 락을 취득한다.
result set에 포함되지않는 다면 잠금 해제한다.(주어진where절에 맞는게 없으면)

그런데 어떤 경우에는 row에 대한 unlock을 즉시 하면 안될 수 있는데 왜냐하면 result row와 row의 original source사이의 관계가 쿼리 실행중 손실될 수 있기 때문, 예로 union으로 테이블에서 검색된 행이 결과 집합에 적합한지 여부를 평가하기 전에 임시 테이블에 삽입될 수 있음. 이 경우 임시 테이블의 행과 원래 테이블의 행의 관계가 손실되고 후자의 행은 쿼리 실행이 끝날 때까지 잠금이 해제되지 않는다.

innodb에서 잠금 읽기에 경우 고유 인덱스의 경우는 발견된 인덱스 레코드만 잠그고 gap은 잠그지 않는데
고유하지 않은 인덱스의 경우는 gap, next key 잠금을 통해 다른 세션에서 검색 범위에 값을 삽입하는 것을 차단한다. 

update where 문은 배타적인 next key 락을 모든 레코드에 건다(unique index record에만)
update문이 clustered index record를 수정할때 secondary index 레코드에 암시적 잠금이 적용되고 , update 작업은 새 secondary index record를 삽입하기 전 중복 확인 스캔을 수행할때 와 새 secondary index record를 삽입할때 영향을 받는 secondary index record에 대한 shared lock을 건다.

만약 foreign key 제약조건이 선언된 테이블이 있으면, insert, update, delete처럼 제약조건 상태가 shared record-level lock이 레코드에 걸렸는지 확인하기를 요구한다.

