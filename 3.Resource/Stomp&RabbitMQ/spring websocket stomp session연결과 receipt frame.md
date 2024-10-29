
websocket connection을 위해 설정해둔 url로 요청이오면 dispatcher servlet이 WebSocketHttpRequestHandler에게 처리를 요청함

WebSocketHttpRequestHandler는 DefaultHandshakeHandler를 사용해서 http -> websocket으로 프로토콜 변경을 위한 핸드쉐이킹을 진행한다 

핸드쉐이킹이 성공적으로 끝나면 등록된 핸드쉐이크 인터셉터들을 순환하며 인터셉터에서 처리를 함
이것들 모두 다 끝나면 StandardWebSocketSession이라는게 생성됨

client <- StandardWebSocketSession -> server

이후 DefaultStompSession 객체로 브로커와 stomp session이 수립이 된다. 

1. send frame으로 작성된 메시지가 서버에서 rabbitmq stomp plugin으로 전달될때 custom header를 사용하여 필요한 정보를 message에 포함시켜 전달하는 방식을 통해 메시지를 구분할 수 있다.

SubProtocolWebSocketHandler 내부에서 SubProtocolHandler Set, WebSocketHolder를 지정하여 여러 사용자의 websocket session을 저장하고 관리한다