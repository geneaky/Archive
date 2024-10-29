두 방식에 내보내기, 불러오기 차이가 있다

##### CommonJS
파일 확장자를 .cjs로, package.json에 type프로퍼티에 "module"을 지정해줘야함
module이라는 객체 내 exports라는 키를 가진 프로퍼티를 사용해 객체를 내보낼 수 있음
단일 객체의 경우 module.exports로 내보낼 수 있고, 여러객체를 내보내야하는 경우 exports 객체에 각 객체 프로퍼티를 지정,할당하여 내보낼 수 있음.
```js
//export
const a = 3;
const b = () => console.log("b")

class C {
    constructor(name) {
        this.name = name
    }
}
const c = new C("c")

// module.exports = { a, b, c }
exports.a = a
exports.b = b
exports.c = c

//required
// const {a, b, c} = require("./commonjs-export.cjs")
const {a} = require("./commonjs-export.cjs")
const {b} = require("./commonjs-export.cjs")
const {c} = require("./commonjs-export.cjs")

console.log(a, b, c)
```


##### ES6
현재의 표준 스펙이라고 생각하면됨

import, from, export, default 모듈 전용 관리 키워드를 가지고 있음 -> 가독성이 더 좋음
비동기 방식으로 동작하고 , 모듈에서 실제로 사용되는 부분만 불러오기 때문에 메모리 사용에 이점이 있다

default는 하나의 객체만 내보내는 방식, 하나만 내보내기때문에 alias없이 원하는 이름으로 import 가능하다

``` js
//export
const a = 3
const b = () => console.log("b")
class C {
    constructor(name) {
        this.name = name
    }
}
const c = new C("c")

export { a, b, c }

//import
import { a, b, c as d } from "./es6-export.js"

console.log(a, b, d)

//default export
const a = 3
const b = () => console.log("b")
class C {
    constructor(name) {
        this.name = name
    }
}
const c = new C("c")

export default c

//import
import d from "./es6-export.js"

console.log(d)
```

