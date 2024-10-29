
리액티브 프로그램의 표준 API 사양을 말한다.
`비동기 데이터 스트림`과 `논-블로킹 백프레셔`에 대한 사양을 제공한다.

리액티브 스트림은 TCK(Technology Compatibility Kit)을 지원하기 때문에 라이브러리가 정해진 사양에 맞게 구현되었는지 보장할 수 있다.
	라이브러리가 정해진 사양에 맞게 구현되었는지 보장하기 위해 만들어진 테스트 도구


#### Reactive Stream 구현체들

- Project Reactor
- RxJava
- Akka Streams
- Vert.x



#### reactive stream interface

| 인터페이스   | 설명                                                                                                                         |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------- |
| Publisher    | 데이터를 생성하고 구독자에게 통지                                                                                            |
| Subscriber   | 데이터를 구독하고 통지 받은 데이터를 처리                                                                                    |
| Subscription | Publisher, Subscriber간의 데이터를 교환하도록 연결하는 역할을 하며 전달받을 데이터의 개수를 설정하거나 구독을 해지할 수 있다 |
| Processor    | Publisher, Subscriber을 모두 상속받은 인터페이스                                                                             |

Publisher가 데이터를 생성하고 Subscriber들에게 데이터를 통지하면 Subscriber는 자신이 처리할 수 있는 만큼의 데이터를 요청하고 처리한다.
> 발행자가 제공할 수 있는 데이터의 양은 `무한(unbounded)` 하고 `순차적(sequentail)` 처리를 보장

Subscription은 Publisher와 Subscriber을 연결하는 매개체로 구독자가 데이터를 요청하거나 구독을 해지하는 등의 데이터 조절에 관련된 역할을 담당한다.

Processor는 발행자와 구독자의 기능을 모두 포함하는 인터페이스이며 데이터를 가공하는 중간 단계에서 사용한다.


#### [[리액티브 스트림의 데이터 처리 프로토콜]]

