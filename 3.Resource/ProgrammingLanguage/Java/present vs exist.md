
위 키워드로 메서드명을 만들다 보면 드는 고민이다.
[참조](https://stackoverflow.com/questions/13186722/what-is-the-difference-between-using-exists-and-present-in-ruby)

present는 자원이 존재하지만 blank이거나 특정 상황에서 원치 않는 상태인 경우 
exist는 자원이 존재하지 않는 경우 사용하면 될듯

null을 `자원이 웞다` , `자원을 찾을 수 없다` 로 나눈다고 생각해보면
자원이 없는 경우는 null이나 empty optional을 반환하고 이 응답값에 대한 검증을 exist로 하는게 맞다고 생각이되고, 찾을 수 없는 경우도 null로 표현할 수 있는데 자원이 표현하는 상태가 전체가 null이 아니라 일부가 null이고 일부는 존재한다고 가정하면 이때는 일부가 null인 자원의 부분을 검증하기 위해  present를 사용하는 방식으로 해봐야겠다.
