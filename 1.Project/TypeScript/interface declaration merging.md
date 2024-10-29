
typescript interface는 같은 이름으로 중복 선언한 경우 다른 서로 다른 선언부를 합쳐주는 기능이 있다
declaration merging인데 type alias는 지원되지 않는다

``` ts
interface Person {
	a: string;
}

interface Person {
	b: string;
}

const person: Person = {

```