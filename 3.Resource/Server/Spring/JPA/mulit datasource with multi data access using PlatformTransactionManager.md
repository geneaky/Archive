데이베이스 성능, 장애상황을 대비해서 master, slave db를 사용하는 경우 spring에서 datasource를 2개 등록해야하는데 data 접근 기술로 jpa와 mybatis를 둘 다 사용하는 경우 트랜잭션의 관리는 어떻게 해야할지 고민이된다.


##### 우선 궁금한점.. 2개의 transaction manager를 각각 등록하는 경우 하나의 transaction에서 각각 다른 data access 기술에서 예외가 발생하면 롤백은 어떻게 진행될까?

##### 실험 결과

![[Pasted image 20231208143257.png]]


1. @Transactional에서 transaction manager를 지정하지 않은 경우
	![[Pasted image 20231208142726.png]]

2. mybatis용 transaction manager에서 jpa와 함께 사용한 경우
	![[Pasted image 20231208142831.png]]
	> jpa 동작을 처리하기 위한 persistence context를 열수 없어서 예외 발생
	
3. jpa용 transaction manager를 mybatis와 함께 사용한 경우
	![[Pasted image 20231208143325.png]]
	jpa용 transaction manager가 mybatis의 transaction 동작까지 지원하고이 있다


##### jpa와 mybatis 둘 다 platformtransactionmanager를 구현하는 transaction manager를 사용하기 때문에 jpa용 transaction manager 하나를 등록해서 사용해보았다.

##### 실험 결과

![[Pasted image 20231201160309.png]]

![[Pasted image 20231201160418.png]]

insert 시에 강제로 예외를 터트리로 로그를 확인해봤다

![[Pasted image 20231201160553.png]]

mybatis를 사용했음에도  platformtransaction manager로 설정해둔 jpa transaction manager가 정상 동작한다

같은 datasource를 사용하는 jpa, mybatis에 대한 transaction관리를 모두 해준다


