
stomp conenction을 맺을 클라이언트와의 갑작스러운 연결 종료(에: 갑작스런 네트워크 단절)시 
현재 사용자의 연결 상태를 db에 가지고있고 그 정보로 notification을 보내기위한 처리를 하기 위해
WebSocketHandlerDecorator를 확장해서 connection end time에 세션에서 사용자 정보를 가져와서
업데이트하는 방향으로 처리를 진행했다.

WebSocketTransportRegistration에 decorator factory를 등록할 수 있기 때문에 message broker configuration에서 configureWebSocketTransport 메서드를 override하여 작성한 custom websocket handler decorator를 등록해줌으로써 session 연결 종료 상태가 normal 상태가 아닐때 유저의 접속여부를 업데이트 해줄 수 있다.


``` java 
@Override  
public void configureWebSocketTransport(WebSocketTransportRegistration registration) {  
	registration.addDecoratorFactory(  
		handler -> 
		new CustomWebSocketHandlerDecorator(participantRepository, handler));  
		super.configureWebSocketTransport(registration);  
}
```

