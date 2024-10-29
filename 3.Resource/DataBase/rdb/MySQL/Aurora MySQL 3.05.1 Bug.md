
MySQL 8.0.32 version에 올라온 이슈로 instant add 방식의 DDL 작업을 하게되면 예상 못하게 서버가 종료된다고 한다.

![[Pasted image 20240308173536.png]]

일반적으로 사용한 add column과 instant add 방식을 비교하자면

##### default add column

``` sql
ALTER TABLE employees ADD COLUMN birth_date DATE;
```

위에처럼 nullable한 컬럼을 추가하는 경우 데이터베이스가 내부적으로 테이블의 모든 데이터를 복사하고 새 컬럼을 추가한 뒤, 복사본을 원본 테이블로 대체하는 과정을 거친다.
그래서 대규모 테이블의 경우, 이 작업은 시간이 오래 걸리고 많은 시스템 자원을 소모할 수 있다.

##### instant add column

``` sql
ALTER TABLE employees ADD COLUMN hire_date DATE DEFAULT '1970-01-01';
```

추가하는 컬럼이 NOT NULL인 컬럼인 경우 DEFAULT로 기본값을 지정해주면, 기본적으로 컬럼 추가하는 방식과 다르게 테이블의 메타데이터만 변경해서 컬럼을 즉시 추가하므로, 실행시간이 훨씬 단축된다.

