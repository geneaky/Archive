# 생성
BigDecimal로 생성할때는 생성자, factory method로 생성이 가능한데 방식에 따라 유의해야할 부분이 있다.

- 생성자 생성 방식
``` java
String num = "3140.000001";  
double numDouble = Double.parseDouble(num);  
BigDecimal numDecimal = new BigDecimal(num);  
BigDecimal numDoubleDecimal = new BigDecimal(numDouble);  
  
System.out.println(numDecimal);  
System.out.println(numDoubleDecimal);
```
![[Pasted image 20231012154305.png]]

생성자 생성방식은 String 값으로 생성하는 것을 권장
생성자 파라미터로 소수점 값을 그대로 넘기기 때문에 결과 또한 기대값과 다르다.

- factory method 생성 방식
``` java
String num = "3140.000001";  
double numDouble = Double.parseDouble(num);  
BigDecimal numDecimal = new BigDecimal(num);  
BigDecimal numDoubleDecimal = BigDecimal.valueOf(numDouble);  
  
System.out.println(numDecimal);  
System.out.println(numDoubleDecimal);
```
![[Pasted image 20231012154543.png]]
입력값이 String이 아닌 long, double 타입의 경우 factory mehtod 방식을 사용해야한다.


- 지수로 생성하는 경우
``` java
BigDecimal currNo = BigDecimal.valueOf(Double.parseDouble("1.0E7")); //지수 붙음 
BigDecimal b = new BigDecimal("2.0E7"); //지수 붙음
```

BigDecimal은 입력값이 지수인경우 출력또한 지수로 출력한다.

수학적으로 동일한 숫자일지라도 bigdecimal간에 지수표현식, 숫자표현식 비교는 다르다.

- equals() 비교시 다르다.
![[Pasted image 20231012151346.png]]
BigDecimal의 equals 내부에서 scale비교를 하는데 지수표현식은 소수점이 존재하기 때문
![[Pasted image 20231012151639.png]]
![[Pasted image 20231012152515.png]]
이경우 compareTo()를 통해 비교하면된다.
![[Pasted image 20231012151824.png]]
compareTo()에는 동일 scale이 아닌경우에 대한 비교 로직이 존재하기 때문
![[Pasted image 20231012151928.png]]

scale로 조건이 들어가기 때문에 비교시 소수점을 맞춰주어야함

Mybatis는 역직렬화를 할때 역직렬화하고자하는 객체의 생성자 > 팩토리 메서드 > setter 순으로 사용을 하기 때문에
BigDecimal의 생성자는 String type이라서 double타입의 컬럼 데이터의 경우 팩토리 메서드를 통해 역직렬화를 해서
위에서 발생한 문제가 생기지 않는다.
##### BigDecimal
최대 34자리까지 10진수로 저장가능한 형식

| 용어      | 뜻                                                    |
| --------- | ----------------------------------------------------- |
| precision(== unsacle) | 소수점을 제외하고 숫자의 양끝이 0이아닌 부분의 자리수 |
| scale(== fraction)     | 소주점 아래 부분 끝이 0이아닌 범위의 자리수           |
|           |                                                       |

#### js Number <-> java BigDecimal(지수표현식)

api요청에 대한 응답으로 지수표현식이 넘어가더라도
Number는 이 값을 숫자 표현식으로 보여준다

![[Pasted image 20231012124357.png]]

==그래서 BigDecimal을 응답으로 보낼때 표현형식을 걱정할 필요가 없음.==

BigDecimal은 자주쓰는 숫자, 소수점 자리수를 캐시하여 static으로 주어진 값 사용시 성능적 이점이 있다.
예) 0에서 10, scale 0~15