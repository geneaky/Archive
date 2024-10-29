
Serverless와 유사한 의미를 가지는데..

WebContainer는 web component를 관리해주는데 서버가 실행되는 동안 여러개의 web component가 정상동작되도록 라이프 사이클을 관리하며,

web client로부터 들어온 web request를 어떤 web component가 요청을 처리할 수 있는지 결정하고 전달하는지(routing, mapping) 판단한다.

**web component == servlet**
**web container == servlet container**

spring에도 container있는데 이는 servlet container 뒷단에 존재하며 servlet container를 통해 웹에서 들어온 요청을 container가 관리하는 bean중에서 해당 요청을 처리할 수 있는 bean에게 전달한다. (이과정에서 여러 bean들 간에 협력이 있음)

그래서 spring을 사용할 때 servlet container를 사용해야하는데 servlet container를 설치하고 관리하는데 너무 수고스러우니 이러한 servlet container 없이 spring을 개발할 수 있도록 하면 좋겠다 하여
Spring Boot를 통해  `standalone application` 을 만들고자 하였음

spring application을 동작하려면 어쨋든 servlet container는 존재해야하기 때문에 spring boot main method에서 어플리케이션 실행시에 자동으로 띄워준다.
