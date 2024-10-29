클라이언트와 인터페이스 사이의 의존성을 제거하는 방식으로 모듈화 수준을 높이는 것

클라이언트가 인터페이스에 의존을 하고 있더라도 어느 시점에는 인터페이스를 구현하는 구현체가 필요하다
결국 클라이언트가 구현체를 의존하고 있다는 사실은 바뀌지 않는다.

Service Locator는 중앙 집중화된 registry로써 인터페이스에 대한 구현체를 저장하고, 사용하도록 도와준다.
Service Locator를 사용해 구현체를 가져와서 클라이언트가 구현체와 더 느슨한 관계를 가질 수 있도록 한다.

[마틴 파울러 서비스 로케이터](https://martinfowler.com/articles/injection.html#UsingAServiceLocator)

아래는 스프링을 사용했을때 서비스 로케이터 구현 방식

handler interface 선언

![[Pasted image 20240306151417.png]]

handler interface를 구현하는 구현체를 등록

![[Pasted image 20240306151441.png]]

![[Pasted image 20240306151458.png]]

handler 구현체의 bean name으로 enum 생성
![[Pasted image 20240306151518.png]]

locator를 사용해서 동적으로 필요한 핸들러를 사용
![[Pasted image 20240306151538.png]]