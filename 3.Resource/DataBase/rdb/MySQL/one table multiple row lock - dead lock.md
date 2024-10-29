하나에 테이블의 여러 row에 대한 lock을 취하는 작업 또한 여러 트랜잭션에서 row접근 순서가 달라진다면 deadlock이 걸릴 수 있다고 한다(?)

![[Pasted image 20231123175938.png]]

하지만 테스트 할때는 deadlock이 걸리지 않았음 흠..

동일 테이블에서 insert, delete하는 경우??