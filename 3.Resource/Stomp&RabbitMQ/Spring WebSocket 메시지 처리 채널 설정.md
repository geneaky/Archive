스프링에서 [WebSocketMessageBrokerConfigurer](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/socket/config/annotation/WebSocketMessageBrokerConfigurer.html#configureClientInboundChannel-org.springframework.messaging.simp.config.ChannelRegistration-) 구현하게되면 제공된 메서드를 구현할 수 있는데
websocket client로부터 전달받은 메시지를 처리하기 위한 inbound message channel에 대한 설정을 할 수 있다.

- configureClientInboundChannel
> message channel은 thread pool로 동작하게되고 기본 pool size는 1이며 운영환경에 맞춰 커스터마이징 하는걸 추천한다.

- configureClientOutboundChannel
> websocket client에게 메시지를 전달하기위해 사용하는 message channel에 대한 설정으로 inboundchannel과 마찬가지로 default pool size는 1이며 운영환경에 맞춰 커스터마이징 하는 것을 추천한다.