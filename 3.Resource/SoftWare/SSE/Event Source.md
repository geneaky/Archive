

> EventSource는 서버에서 전송하는 `이벤트` 들에 대한 인터페이스

EventSource의 인스턴스는 http 서버와 영구적인 connection을  열고
http 서버는 text/event-stream 포맷으로 이벤트를 보낸다

이 영구적인 connection은 EventSource의 close() 메서드를 호출할때까지 유지된다

> EventTarget <- EventSource

