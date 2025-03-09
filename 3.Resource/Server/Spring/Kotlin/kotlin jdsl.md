
케이스 테스트

- entity 조회
	- 단건 조회 o
	- 엔티티 더티 체킹 확인 o
	- 페치조인 o 
	- 일반 조인 o
	- 페이징 조회 o

- projection 조회
	- 단 건 조회 o
	- 일반 조인 o
	- 페이징 조회 o

- from 서브 쿼리 가능한지?(인라인뷰) o
- join 서브 쿼리 가능한지?(인라인뷰) o
- select절 서브 쿼리 가능한지?(스칼라) o
- where절 서브 쿼리 가능한지?
- order by limit offset 직접 설정 가능?
- group by, count, max

- jdsl을 사용하지 않
	- postgresql specific query, native query가 필요한 상황은 jdbctemplate으로 커버
	- 당장 jdsl로 하기 어렵거나, 학습이 필요하거나, 번거로운 부분은 jdbctemplate or @query로 커버
- 배치 update o
	- 단 건은 jpa 더티 체킹 사용
- 배치 delete o
	- 단 건은 repository delete 사용