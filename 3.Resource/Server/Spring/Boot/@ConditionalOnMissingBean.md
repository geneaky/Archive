
[[@Conditional]] 의 기본 동작을 활용해서 만들어둔 spring의 애너테이션으로 BeanFactory에 해당 애너테이션을 붙이 빈이 등록되어있지 않은 경우에만 Bean으로 등록한다

반대로 @ConditionalOnBean이 있다.
> 지정된 요구사항을 만족하는 Bean이 이미 BeanFactory에 등록된 경우 위 애너테이션이 붙은 빈을 등록 한다.