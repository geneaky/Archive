- describe
	- 테스트의 그룹을 정의하는데 사용.
	- 하나의 테스트 스펙에 대한 설명을 제공
	- 하위의 테스트들을 특정 기능, 모듈로 그룹화하는데 사용
- context
	- 테스트들을 특정 조건, 상황으로 그룹화하기 위해 사용
- it
	- 함수를 실제로 테스트하기 위해 사용

```kotlin
internal class MySecondTest: DescribeSpec({  
    describe("로또 생성기") {  
        context("로또를 생성하면") {  
            it("6개의 숫자가 생성된다") {  
                val lotto = Lotto(listOf(1, 2, 3, 4, 5, 6))  
                lotto.size shouldBe 6  
            }  
}}})
```

