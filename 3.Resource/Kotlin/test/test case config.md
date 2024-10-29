##### invocation

``` kotlin
class TestCaseConfigTest: DescribeSpec({  
    describe("test case config"){  
        context("multiple run test case") {  
            it("run multiple time").config(invocations = 3) {  
                println("run multiple time")  
            }  
        }
    }
})
```

`invocation` 값으로 n번 실행


##### threads

```kotlin
class TestCaseConfigTest: DescribeSpec({  
    describe("test case config"){  
        context("multiple run test case") {  
            it("run multiple time").config(invocations = 3, threads = 3) {  
                println("run multiple time")  
            }  
        }    
    }
})
```

각 테스트 실행이 별도의 스레드에 할당되어 병렬 실행된다.

