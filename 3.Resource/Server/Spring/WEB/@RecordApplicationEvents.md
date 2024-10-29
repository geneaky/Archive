
어플리케이션에서 발생하는 event를 record해주는 애너테이션으로 클래스레벨에서 작성 가능하다.

``` java

@SpringBootTest
@RecordApplicationEvents
class AsyncMessageEventTest {

	@Autowired
	SomeService service;
	@Autowired
	ApplicationEvents events;

	@Test 비동기_메시징_테스트() {
	  //특정 비동기 발송 비즈니스 로직
	  service.do();

	  int count = (int) events.stream(MessageEvent.class).count();

	 assertThat(count).isOne();
	}
}
```