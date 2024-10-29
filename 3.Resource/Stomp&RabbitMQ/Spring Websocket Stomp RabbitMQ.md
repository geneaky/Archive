
Websocket Stomp



RabbitMQ

채널, 채널 id
subscriber는 각각의 queue를 가진다. - rabbitmq에서 별도로 생성해야할 queue이고
각각의 queue의 리스너가 받아서 해당 channelid를 가진 channel에 보내는것
subscriber channel은 채팅방과 동등하게 봐도 될듯
여기서 queue와 websocket connection의 관계가 있나 생각을 했지만 없을듯하다

publisher는 메시지를 보낼때, channel id, room id를 함께 보내야한다
 

websockethandler
textwebsockethandler를 확장해서 구현하는데 현재 연결되어있는 websocket session과 text형 message를
넘겨받아 어떻게 메시지를 처리할지에대한 구현을 작성하면된다. 
session manager로부터 현재 서버에 웹소켓 커넥션을 연결한 clients를 받은 후 전달 받은 message를 
전체 clients에게 넘긴다거나 하는등 가능한데 이 방식은 하나의 서버에 웹소켓 커넥션을 연결한 
전체 clients session으로 message 전달에 대한 쿵짝쿵짝을 진행하게됨

websocketmessagebrokerconfigurer를 configure로 사용할건데
여기에 websocket connection handshake에 대한 interceptor를 추가할 수 있음.
연결시에 log를 남긴다거나 등등 첫 연결 수립에 이뤄져야할 작업이 있다면 여기서 하면될듯?
before, after method로 전,후처리 가능

spring websocket은 mvc에 의존적이지는 않고 websockethttprequesthandler의 도움으로 websockethandler
를 http 전달환경에 통합시킴


stomp는 신뢰가능한 양방향 streaming network protocl인 tcp와 websocket위에서 사용됨.

``` stomp frame structure
COMMAND --option [ SEND, MESSAGE, CONNECT(STOMP), CONNECTED, SUBSCRIBE, UNSUBSCRIBE, AC, NACK, BEGIN, DISCONNECT, ERROR 등등]
header: value // 이런식으로 요청 메시지에 http와 같이 header를 포함할 수 있음

Body^@

``

client에서 send나 subscribe로 관심사(특정 방, 사용자 등등?) 를 메시지에 담아서 보내는 방식
send와 subscribe는 `destination`이라는 필요함 -> 어디로 메시지를 보낼지 명시하기 위해

``` stomp frame structure
SUBSCRIBE
id: sub-1
destination: /topic/price.stock.*
^@
```
이런식으로


MESSAGE command는 broadcast용도로 사용.


RabbitMQ 정리

rabbitmq는 tcp기반으로 동작.

채널당 하나의 queue를 가질 수 있다.

stomp를 서브 프로토콜로 사용하면 스프링이랑 스프링 시큐리티가 유용한 프로그래밍 모델을 제공해줌.

custom messaging protocol 이랑 message format만들 필요 없음.
stomp client는 스프링의 java client를 포함 가능함

netty에서 stomp codec를 제공해주므로 netty의존성이 필요함.

message brokcer를 사용하여 구독 및 브로드캐스트 관리 가능

stomp destination header 기반으로 메시지가 라우팅될 수 있고 @controller로 묶일수있음.
websockethandler가 제공해주는 connection을 사용해서 직접 websocket raw message 사용하는 것 보다는
쉬울듯

spring security랑 연동해서 sotmp destination이랑 message type기반의 메시지들을 보안할 수 있음

registerStompEndpoints를 사용해서 등록한 end point로 websocket handshake가 발생함
이 핸드쉐이크 과정에 조작을 원한다면 handshakehandler를 그 과정 전후로 부가작업을 하고 싶다면
handshakeinterceptor를 사용하면 될듯함 (마지막 end point만 일치하면 되나? 확인해봐야함)

stomp message의 destinatinon이 "/app"이라고 한다면 해당 url이 @messagemapping이 붙은 method로
@controller로 지정된 class에 라우팅되어 있어야한다
이 '/app' url은 configureMessageBroker에 application destination preifiex로 "/app" 이렇게 지정해둬야
해당 url을 stomp destination url로 사용 가능.

내장된 구독과 브로드캐스팅을 위한 메시지 브로커를 사용하면되는데
해당 메시지를 destination header가 "/topic"로 라우팅하고싶다면 enableSimpleBroker("/topic")으로
브로커 url을 지정해주면된다

interface Message가 header와 payload를 표현한다

interface MessageHandler는 interface Message를 핸들링하기위한 동작을 정의하고 따라서 
Message를 파라미터로 받는다

interface MessageChannel은 message를 send하는데 producer & consumer를 낮은 결합도가 되게한다

interface SubscribableChannel 구독자를 관리하고, 이 채널을 사용해서 메시지를 보내도록 핸들링

class ExecutorSubscribableChannel 구독자 각각에게 메시지 전달 (executor를 사용해서)

broker relay를 사용해서 외부 stomp broker(rabbitmq)에 메시지를 주고받는데  tcp로 주고받음

stomp message의 destination을 prefix로 지정하면 @messagemapping에 연결된 url로 라우팅되는데
반로 destination url을적어주면 그때는 stompbrokerrealy를 통해 바로 message broker로 전달된다.

@PostMapping으로도 가능

@Payload 애너테이션이 붙은 파라미터는 converter(json default?)로 변경되어 @Valid로 검증할 수 있음
@Header도 똑같이 사용가능
@DestinationVariable message의 destination으로부터 추출한 변수를 할당해줌

@MessageMapping이 값을 반환할때는 설정된 message converter로 변환하여 `Message`로 담겨서
brockerChannel로 전송되고 subscriber들에게 브로드캐스팅됨.
이때 반환을 void로하고 SimpleMessagingTemplate을 사용해서 메시지를 보내는 방식도 가능한데 굳이?쓸까

나가는 메시지의 목적지는 들어오는 메시지와 동일하지만 `/topic`으로 고정됨

@SendTo 메서드 애너테이션을 사용해서 목적지 커스터마이징이 가능하다.
@SendToUser는 사용자에게 메시지 전달만 가능(브로커로는 ㄴㄴ)

@SubscribeMapping
@MessageMapping과 조합으로 사용하며 구독 메시지 범위를 좁히기 위해 사용.
@MessageMapping의 목적지에 @SubscribeMessaing으로 구독하고있는 관심 메시지만 가리키도록 가능

@MessageExceptionHandler
이건 @MessageMapping 메서드로부터 발생하는 exception을 처리하기 위해 사용

Send Message

어플리케이션 특정 부분에서 연결된 클라이언트에게 메시지를 보내고 싶은 경우 
어플리케이션의 sometihng 컴포넌트가 brokerChannel에 메시지를 보낼 수 있음
가장 쉽게 구현하는 방법으로는 `SimpleMessagingTemplate을 주입받고 사용하면됨

인 메모리 방식으로 브로커 사용시 simple broker를 사용하고

외부 브로커를 사용할 경우 
stomp broker relay는 spring의 메시지 핸들러인데 메시지를 외부 브로커로 포워딩하는 방식으로 핸들링함.

하트비트 시간을 설정할 수 있는데 default는 10초. >> "system" tcp connection으로 주고받음
브로커와 연결이 끊기면 연결될때까지 5초마다 재시도함

다중 커넥션을 하고 싶은 경우 stomp broker relay에 별도의 setTcpClient로 tcpClient를 넘겨주면됨

stomp messaging session 은 http request와 같이 시작된다.

웹 어플리케이션에서 이미 http request에대한 인증 인가를 했다면 특이 spring security를 사용한 인증
메커니즘이라면 사용자 인증을 위한 security context는 http session에 저장되는데
인증 후 다음 요청부터 시큐리티 컨텍스트에 할당이 됨.

따라서 웹소켓 핸드쉐이크 전송 요청, 특히 이미 getUserPrincipal()을 통한 접근 허용된 사용자로 되어 있다.
스프링은 자동적으로 웹소켓 세션을 가진 유저에 할당해준다 (security context에 인증된 유저를)
이후 stomp 메시지들은 모두 유저 헤더를 통한 세션위에서 전송됨(그래서 나는 인증정보(jwt)를 헤더에 넣어줌)
근데 이 헤더로 인증처리를 다시해줘야할지는 모르겠음.
사용중에 세션이 다한경우를 세션을 끊도록 처리 , 사용중 refresh 토큰 발급도 진행

stomp는 connect frame에 `login`, `passcode` 헤더를 가짐
근데 스프링은 기본적으로 stomp 프로토콜의 해당 헤더를 무시함 , 유저가 이미 인증되었다는 가정하에 동작

웹소켓 프로토콜은 웹소켓 핸드쉐이크중 서버가 클라이언트를 인증할 수 있는 특정 방법을 규정하지는 않음.

jwt 토큰으로 stomp websocket 커넥션을 맺을 때 header의 Authorization: 에서 jwt토큰 뽑아와서 
인증 진행하면될듯 (이때 interceptor로 하네, new ChannelInterceptorAdaptor())

spring security의 인증을 사용할때는 인증을 위한 channelinterceptor 설정을 확실히 해줄 필요가 있음
spring security보다 먼저(?) << 이부분 다시 확인.
AbstractSecurityWebSocketMessageBrokerConfigurer ?? 이걸로??

특정사용자에게 직접 보내고싶은 경우를 위해 stomp는 `/user/`로 시작하는 destinations의 경우 이러한
목적을 가졌다고 생각하고 UserDestinationMessageHandler가 핸들링한다.

channelinterceptor에서 sessions disconnected에 대한 후처리로 client와 연결이 끊겼다는 정보를 db에
반영해두고 끊겼을때 이후 시간의 채팅에 대해 카운트를 할 수 있을 듯하다.

websocketstompclient로 client 생성후 stompsessionshandleradapter 이용해서 테스트 가능할 듯?


stompsessionhandler는 그 자체로 stompframehandler라서 error frame 허용하고 handleexceptoin 호출해준다.


dispatcherservlet에 `/stomp-connection`으로 get요청이 들어왔음 stomp는 http위에서 동작하는 프로토콜이므로
dispatcherservlet에서 동작하는것이 타당하다고 생각됨.

-> 해당 요청이 WebSocketHttpRequestHandler Mapping됨.
-> WebSocketHttpRequestHandler에게 GET `/stomp-connection`으로 넘어감
-> 기본 핸드쉐이크 핸들러가 WebSocket으로 업그레이드함.
-> 101 (switching_protocols)
-> 

servletserverhttpresponse를 close()해서 해당 응답을 끝내고 자원할당을 해제한다.

custom handshaker에서 before handshake에서 return true을 지정해주어야 afterhandshake를 호출함

abstractsecuritystompconfigure 사용하는거랑 비교
simpUser 사용하는거랑 비교

spring security를 사용하면 abstract security websocket message broker configure를 확장하는 
websocket 보안 설정 클래스를 생성할 경우 security context holder에서 웹소켓 inbound요청에 대해
simpUser라는 속성정보에 사용자 정보를 채우는데

securityconfig에 antmather에  websocket connection을 위한 endpoint로 jwt token authentication filter를
경유하지 않게 바꾸면 securitycontextholder에 사용자의 principal이 적재되지 않음.
하지만 interceptor에서 stomp헤더의 jwt token을 통해 직접적으로 security context에 user principal을 
넣어주기 때문에 문제가 되지 않음.

spring에서는 웹소켓 커넥션을 맺기전에 이미 사용자가 인증,인가가 끝난 사용자이기를 가정하고 구성하는것을
추천함.

native app에서 csrf토큰을 사용해야하는가에 대한 의문
csrf라는게 결국 사용자가 특정 웹사이트에 로그인한 상태에서 악성유저가 보낸 메일 or 게시판 스크립트등을
통해 사용자의 권한으로 앞서말한 특정 웹사이트의 api를 호출한다던가(api를 호출하여 친구목록에 친구들에게)
특정 게시물 링크를 보냄)하는 방식으로 서버가 특정 브라우저에서 들어온 요청이 공격자의 요청인지 
사용자의 요청인지 분간할 수 없기 때문에 발생하는 해킹 기법인데
native app은 모바일 환경에서 이루어지기 때문에 특정 웹뷰와 같은 

abstract security websocket message broker configurer에서 csrf을 끄고싶으면 sameOriginDisabled를
사용하면됨.

csrf을 막기위한 방식으로 2가지가 있는데 하나는 referee(origin)을 확인하는 것 , 나머지 하나는 csrf 토큰을
사용하는거라 이렇게 한듯.

jwt token versioning
특정 단말기에서 토큰을 발급해서 사용하다가
새로운 곳에서 로그인 시도하면 또 토큰 테이블에 넣어 근데 넣기전에 사용자가 이미 발급 받았는지 확인해
발급 받았으면 사용자 이메일로 확인 링크를 보내 승인하면 해당 기기로 토큰 발급해 
근데 브라우저에 있는걸 탈취당했어 토큰 한 개를 2명이 사용해
그러면 사용 시간대랑 ip가지고 구분이 가능할 수 도 있어
근데 사용시간대가 완전히 달라 사용자는 낮에 사용하고 해커는 밤에 사용하면 구분 불가
isp를 사용하면 ip가 동적으로 변경되는 경우가 있기 때문에 ip로 판단 불가
다시 생각해보면 탈취라는 행위는 사용자를 뒤에서 기절시켜서 묶어두고 사용자의 브라우저를 사용하지 않는
이상 로컬에서 직접 침투해서 탈취한게 아닌 이상 (이런걸 막기 위해 은행에서 사용자 로컬에 별도의 프로그램을
설치 시키는듯)
네트워크를 한 번 거쳐가다가 탈취당하기 때문에 이미 사용자의 요청이 서버에 들어간 상태
그리고 토큰에 새롭게 uuid로 버저닝을 하고 사용하면 동시에 사용하는 것에 대해서는 막을 수 있음
이미 해커가 탈취한 토큰의 버전은 지나갔기 때문에
근데 요청을 탈취한게 아니라 응답을 탈취한 경우라면 (응답이 있다는 것 자체가 지금 사용하고 있는 상태)
사용중 탈취라면
사용자도 사용하고 있을거니까 한 번이라도 요청 틀리면 해당 토큰을 삭제해서 다시 로그인하게 만듬.
근데 그래도 해커가 먼저 탈취해서 탈취한 토큰으로 요청을 보내면 의미 없긴함.
근데 이거는 갑자기 ip가 바뀌는걸로 비교할 수 도 있겠지만 ip address도 수정해서 요청을 보내면?
뭐 요청마다 otp를 넣는다면 모르겠는데 그렇게까지 할 수 없으니까 보안을 희생해서 편의를 얻는게 아닌가

securityconfig에 websocket connection endpoint를 열어줘야 stompwebsockethandlermapper를 찾음

websocket connection은 본직적으로 영구적이기 때문에 맨처음 연결을 수립한 서버에 남아있게 된다. 
이러한 특성 때문에 사용자가 다시 재연결하거나 서버를 추가해서 connection을 분배하고싶은 경우에는
다시 시작해야한다.
다시 시작하는 타이밍도 중요 서버 3대중에 2대만 재시작하게되면 모든 커넥션이 로드밸런서와 연결되어있는
한대에 전부 수립이 되어 버림.

클라이언트가 메시지 객체에 routing_key를 내용과 함께 서버로 전송
서버에서는 rabbit_broker에게 전달
rabbit_broker에서 exchange가 routing_key를 바탕으로 바인딩된 queue에게 전달
queue에 push된 메시지는 해당 queue를 구독하고 있는 클라이언트에게 소비됨.

example))
사용자 (입장, 탈퇴, 일반 메시지)를 해당 채팅방(queue)로 전달하기 위해 서버가 rabbit_broker에게
전달 rabbit_broker의 exchange는 메시지에 담긴 routing_key로 해당 채팅방 queue로 메시지를 전달함
그러면 해당 queue를 구독하고있는 consumer가 메시지를 가져가면서 채팅 내용이 보이는 것.


rabbitmq

메시지를 출발지에서 도착지로 전달하는 것이 기본 목적.
exchange는 direct, fanout, topic, headers 타입을 가질 수 있다.

direct는 명확한 binding key를 가지는 queue에게 메시지의 routing key를 가지고 완전일치하는 경우
해당 queue에게 메시지를 전달한다.

fanout은 자신과 연결된 모든 queue에게 메시지를 broadcasting하는 exchange type으로 broadcasting하기
때문에 딱히 목적지로 가는 경로 조건(routing key, binding key)가 필요없다

topic은 자유롭게 커스텀 가능한 exchange type으로 생각함 쓰는 방식에 따라 fanout, direct가 될 수 있음.
다수의 queue또는 다수의 exchange에게 message를 multicast할 수 있음.
multicast기준은 message에 routing key가 기준임.
exchange와 바인딩 하기 위해 패턴이 포함된 라우팅키를 넘겨줘야하는데 바인딩시 topic exchange에게
넘겨준 패턴이 포함된 routing key를 binding key라고 표현함.
이용 패턴 표현식은 '*' <<이거는 하나의 문자로 치환이 가능 , '#' << 이거는 아무것도 없는 문자부터
문자열까지 어떠한 문자들과도 치환 가능을 의미.
그래서 topic exchange는 메시지와 함께온 routing key(패턴을 포함)에 부합하는 binding key가 있는
모든 queue또는 exchange에게 메시지를 전달한다.

headers?
다수의 queue또는 exchange에게 multicast를 하는데 topic처럼 routing key의 패턴을 이용해서
패턴에 부합하는 연결된 모든 queue와 exchange에 multicast하는 방식이 아니라 message의 header에 
포함된 key , value값을 넘겨주어(binding header라고함) exchange와 queue가 가지고있는
binding header와 key value가 일치하는 경우 전달됨.
x-match option이 있어서 부분일치 , 전체일치등 옵션 선택 가능

---구상

사용자 -> 메시지 전송 -> 서버 -> 브로커 전달 -> 브로커 큐로 전달 -> 서버에서 큐를 구독하는 곳으로 전달
-> 사용자에게 전달

|사용자 -> 메시지전송 -> 서버 -> 브로커 전달| 에서 예외 발생시 전송실패 응답 / 유저에게 다시보내달라
|브로커 -> 큐 전달|  rabbitmq qos 설정으로 at least once 전달되도록 설정
|큐에서 해당 큐를 구독하는 서버로 전달| 전달받았을때 stomp connection이 끊겨있으면 nack응답해서 requeue
후 retry하도록 설정
근데 무한 재시도가 할 필요가 있나 싶기도하네.
재시도하고있는동안 대화가 진행되면 계속 retry하고있는 message가 나중에 뜬금없이 전달될것 같은데
재한시간동안만 retry하고 이후에는 채팅 스크롤을 위로 올리면 메시지를 restapi로 조회하도록 구현


rabbitmq connection과 channel에 대해
connection은 client가 브로커와 상호작용하기 위해 설립하는 커넥션이다.
이러한 connection을 하나의 어플리케이션에서 여러개 맞는것은 바람직하지 않음
connection 연결은 비싼 자원이기 때문에 하나 생성해서 계속 사용하는게 더 효율적이라고봄.
rabbitmq는 channel이라는 개념을 통해 하나의 tcp connection을 공유해서 사용할 수 있는 기능을 제공함.

채널 구현은 thread safe하지 않기 때문에 thread당 하나의 채널을이 필요하다 혹은 정확한 동기화 접근으로
만들어주거나

재시작과 연결끊김으로부터 exchange와 queue를 살리기위해 durable하게 만들어야한다.
exchange를 선언할때 durable= true, auto-delete=false, internal=false 이렇게 생성하면 durable해지고
queue를 선언할때 durable=true, auto-delete=false, exclusive=false로 인자를 세팅하면 durable해짐

이러한 exchange와 queue설정에 은 클라이언트가(애플리케이션 서버) 재시작하거나 잠시 연결이 끊겨 없는
상황에서도 계속해서 존재할 수 있도록 해줌.

메시지 consuming이 실패할때 메시지는 일단 지워지는데 이 실패한 메시지를 잡기를 원한다면
dead-letter-queue에 세팅해둔다.

dead letter queue는 dead letter exchange와 함께 동작하는데 이 방법으로 큐에서 삭제된 메시지를 
잡을 수 잇음

보통 suffix를 exchange는 .dlx로 queue는 .dlq로 바인딩한다.
이것 두 개 또한 durable함.

메시지를 소비하기 위해 일반적으로 추천되는 방식은 consume메서드를 사용하는건데
auto-ack를 false로 설정했을때, ack 메시지를 확실해 보내줘야한다
message처리에 실패한다면 제거 후 dlq로 보내야한다.
ack또는 reject 메시지가 실패한다면 queue로 복귀할 것 이다.

안전한 publishing
영속성 queue를 가진다는게 영속성 메시지를 의미한는 것은 아님.
메시지는 여전히 메모리에 상주하고 재시작이나 라우트 불가 메시지는 잃어버리게된다.

메시지가 큐에 라우팅되는것을 명확하게하기 위해 mandatory=true 설정을 해준다.
이것은 무조건 성공을 반환하는 것을 의미(만일 메시지가 큐에서 끝나거나, 라우트 불가 메시지라
에러를 받는다면)

메시지가 디스크로 가게하기위해 persistent=true 설정을 해준다.

마지막으로 메시지가 확실히 작성되는것을 기다려야함

트랜잭션같은 메서드들이이 있지만 추천되는건 기다리는것이다(기다리는게 더 빠르고 추천됨)

consumer가 실패하는것에 대해 auto-ack 파라미터 설정을 해두었다면,
우리는 consumer가 메시지를 전달받고 성공적으로 처리했다는 것을 알고있다는 것을 보장한다

만일 특정 consumer가 메시지를 전달받고 성공적으로 처리했는지 주어진 시간동안 알지 못 한다면,
다른 consumer에게 메시지가 전달된다.

이러한 방식은 메시지가 최소 한 번은 처리될 수 있도록하고 시스템이 결과를 견뎌낼 수 있게 만들 수 있어서 
중요하다. 그리고 timeout 설정은 적당히 맞춰짐. 
긍정적으로 "at-least-once" 전달 보장을 가져다줌

처리가 실패한 메시지가 앞으로도 계속 실패할거란걸 알고있다면(외부 api 호출에 대한 응답이 없는것같은
예시 보다는 데이터의 부재나 유효하지 않은데이터일 경우) 이때는 해당 메시지를 포기하는것이 이후
처리 비용을 아낄수있음. 
그렇게 하기 위해 reject 응답을 보내면됨

message에 retry count를 표기하는 metadata를 넣어서 일정횟수 이상반복해서 해보고 아니다 싶으면
해당 message를 지우는 방식이나 (last_retry_time 같은걸로 빠른 message 응답이 필요한 경우 재시도해보게하고
 지우는 방식도 괜찮을듯)
수정하는게 아니라 새로운 객체를 만들어야하는구나 (불변성)
최종적으로 실패할때는 reject 응답을 해서 requeue가 실패하도록 하는듯.
