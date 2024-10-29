
기존에 servlet container(Assembler)에서 프론트 컨트롤러에서 직접 객체를 생성하여 사용하는 방식에서 spring container를 함께 사용하여 servlet container가 초기화될때 spring container를 초기화하여 servlet container에서 공통기능을 제외한 핵심로직을 위임받은 오브젝트를 spring container에 등록된 bean을 통해 처리하도록 함께 사용하게 되었는데

이렇게 spring container를 함께 사용함으로써 이후에 spring container가 할 수 있는 일들을 적용가능하도록 기본 구조가 잡히게 된다.
- 의존성 주입과 같은 기능을 적용 가능한 것
