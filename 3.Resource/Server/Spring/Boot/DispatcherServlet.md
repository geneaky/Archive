
ServletContainer와 Spring Container를 함께 사용하는 상황에서도 결국 웹요청을 처리하기 위한 서블릿을 등록하는 로직에서 라우팅 작업이나, request parameter 추출하여 controller에게 전달하는 작업을 진행해야 하기 때문에 frontcontroller에서 spring과 관련된 로직을 계속해서 작성해 나가야하는 문제점이 있어서 이를 대체할 servlet인 dispatcherservlet을 spring에서 제공해주어 요청 매핑을 전달받은 spring container를 통해 container의 bean중에서 요청을 처리할 수 있는 bean에 설정 정보를 지정하여 그 정보를 바탕으로 요청 매핑을 해주는 것이다.

method level에 url mapping을 하게되면 추후 요청을 처맇라 메소드가 너무 많아지는 경우 하나의 요청을 처리하기 위해 모든 bean의 메서드를 뒤지는 행위는 비효율적이기 때문에 class level에도 매핑정보를 등록해주게된다. 


