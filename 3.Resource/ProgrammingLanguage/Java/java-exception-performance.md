
java로 코드를 작성할때 try-catch를 상당히 많이 사용하는데 이 try-catch문에서 발생하는 퍼포먼스 문제에 대해 알아보았다.

[참고 링크](https://www.baeldung.com/java-exceptions-performance)

비교 상황
1 예외가 발생하지 않은 상황
2 예외를 던지고 잡는 상황
3 예외를 던지는 상황
4 에외를 던지고 잡지만 예외를 Stack Trace에 포함시키지는 않는 상황
5 예외를 던지고 잡아서 Stack Trace를 풀어보는 상황

``` java
Benchmark                                                 Mode  Cnt    Score   Error  Units
ExceptionBenchmark.createExceptionWithoutThrowingIt       avgt   10   16.605 ± 0.988  ms/op
ExceptionBenchmark.doNotThrowException                    avgt   10    0.047 ± 0.006  ms/op
ExceptionBenchmark.throwAndCatchException                 avgt   10   16.449 ± 0.304  ms/op
ExceptionBenchmark.throwExceptionAndUnwindStackTrace      avgt   10  326.560 ± 4.991  ms/op
ExceptionBenchmark.throwExceptionWithoutAddingStackTrace  avgt   10    1.185 ± 0.015  ms/op
```

예외가 발생하지 않은 상황은 당연히 속도가 가장 빨르다.

예외를 던지고 잡는 상황과, 던지기만한 상황을 비교해보면 둘의 수행시간이 비슷하다 -> 잡는 곳에서 발생하는 퍼포먼스 저하는 미미하다 -> 생성하고 해당 예외를 stack trace에 추가하는 과정에서 퍼포먼스 저하가 발생하는 것으로 jvm 옵션을 주어 스택을 살펴보고 해당 프레임에 예외를 추가하는 행위를 막으니 실행시간이 다시 감소한다.(즉,예외를 스택 추적하는 경우도 성능 저하가 발생할 수 있음)


##### Stack Trace

프로그램이 시작된 시점부터 현재 위치까지의 메서드 호출 목록이다.

JVM에서 스택에는 프레임이 쌓이는데 예외 발생시 JVM이 어떤 프레임에서 예외가 발생했는지 Stack Trace에 포함하여 알려준다.

- 프레임은 무엇인가?
	- 프레임은 메서드가 호출될 때마다 만들어지며, 메서드 상태 정보를 저장한다.
		- 메서드 상태 정보란 Local Variables, Operand Stack, Constant Pool Reference이다.
		- 로컬 변수 배열은 메서드의 지역변수를 갖는데 primitive 타입은 그냥 프레임에 저장되고 reference는 heap의 레퍼런스를 저장한다.
		- 오퍼랜드 스택은 메서드 내 계산을 위한 작업 공간으로 아래와 같은 바이트 코드 메서드를 연산하기 위한 stack 이다.
``` byte code 
public int test();
    Code:
       0: iconst_4
       1: istore_1
       2: iconst_3
       3: istore_2
       4: iload_1
       5: iload_2
       6: iadd
       7: ireturn
```

Constant Pool Reference는 상수 풀의 참조를 갖는다.