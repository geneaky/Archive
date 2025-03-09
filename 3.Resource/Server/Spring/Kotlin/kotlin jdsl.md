
케이스 테스트

- entity 조회
	- 단건 조회
	- 엔티티 더티 체킹 확인
	- 페치조인
	- 페이징 조회


- projection 조회
	- 페이징 조회
		- order by limit offset 직접 설정 가능한지?
	- count, exist
	- json 컬럼, varchar[] 컬럼

- from절 서브 쿼리 가능한지?
- where절 서브 쿼리 가능한지?
- select절 서브 쿼리 가능한지?
- join절 서브 쿼리 가능한지?


- native 쿼리 사용
	- postgresql specific query는 jdbctemplate으로 커버

- 배치 update
	- 단 건은 jpa 더티 체킹 사용
- 배치 delete
	- 단 건은 repository delete 사용