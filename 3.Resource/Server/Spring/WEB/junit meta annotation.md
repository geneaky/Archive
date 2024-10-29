

@springboottest와 @RunWith(SpringRunner.class)를 메타애너테이션으로 갖는 커스텀 애너테이션을 생성해서 사용하려는 도중 발생한 문제 정리.


일단 두 개를 같이 사용할 수 없는 이유는

다른 주석으로 주석이 달린 "메타 주석"을 가질 수 있는 이 메커니즘은 메타 주석을 넣은 클래스에 적용되며 Spring 프레임워크에 고유한 기능이고,  Java 주석의 표준 기능이 아니다.

그래서 동작하지 않는데 junit은 이 스프링 고유의 메커니즘을 모르기 때문에
junit의 @runwith annotation은 @springboottest애너테이션을 바라볼 수 없다.
