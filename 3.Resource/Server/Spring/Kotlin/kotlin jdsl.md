
케이스 테스트

- entity 조회
	- 단건 조회 o
	- 엔티티 더티 체킹 확인 o
	- 페치조인 o 
	- 일반 조인 o
	- 페이징 조회 o
		- order by limit offset 직접 설정 가능한지?

- projection 조회
	- 단 건 조회 o
	- 일반 조인 o
	- 페이징 조회 o
		- order by limit offset 직접 설정 가능한지?

- from절 서브 쿼리 가능한지?(인라인뷰)
- join절 서브 쿼리 가능한지?(인라인뷰)
- where절 서브 쿼리 가능한지?
- select절 서브 쿼리 가능한지?(스칼라)

- native 쿼리 사용 o
	- postgresql specific query는 jdbctemplate으로 커버
- 배치 update o
	- 단 건은 jpa 더티 체킹 사용
- 배치 delete o
	- 단 건은 repository delete 사용