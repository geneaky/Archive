##### proto buffers
구글에서 개발한 이진 직렬화 포맷으로 플랫폼과 언어에 독립적이다.
다양한 언어와 시스템에 효율적으로 전송하기위해 사용한다.
작은 메시지 크기, 빠른 직렬화/역직렬화 속도, 타입 안전성 제공한다.

[protocol buffers docs](https://protobuf.dev/overview/#work)

protobuf를 사용해보면서 장점을 확인해보자..

##### project gradle config

우선 protobuf 의존성을 가져오기 위해 buildscript에 별로로 google() respository를 등록해주고 플러그인 의존성을 받아와서 등록해준다.

![[Pasted image 20231126212900.png]]

![[Pasted image 20231126213037.png]]

이제 실제 개발에서 필요한 java 의존성을 추가해주고 빌드 과정에서 main/test에서 사용하게될 protofile의 위치를 source set으로 지정해준다.

build시에 task를 등록해야하는데 proto file에 작성한 내용을 protoc compiler를 사용해서 java 언어로 생성하겠다는 의미이다

이렇게 작성하면 gradle task에 generateProto가 생성이된다.

![[Pasted image 20231126213329.png]]

응답으로 사용할 message를 .proto파일에 명시해준 후

![[Pasted image 20231126213406.png]]

proto 생성 task를 실행시켜주면 

![[Pasted image 20231126213454.png]]

build결과 디렉토리 하위 generated폴더에 위 이미지처럼 proto file의 이름과 동일한 이름의 클래스가 생성이 된다.


#### 성능 차이?

![[Pasted image 20231126213634.png]]

100만개의 데이터를 각각 proto와 jackson을 사용해 직렬화/역직렬화 해본다.

테스트 결과는 protobuf를 역/직렬화했을때 속도가 2배이상 빨랐고, 직렬화 결과로 나타나는 바이트 결과도 jackson 직렬화 결과가 더 많았다.

protobuf는 데이터를 직렬화할때 binary format을 사용하기 때문에 json과 비교했을때 결과물이 더 작다.

#### gRPC가 아닌 HTTP환경에서 resposne로 사용하기에 적합한가?

protobuf를 회사 신규 프로젝트에서 응답 스펙을 명시해서, 생성하면 관리가 용이하고 dto생성이 편할것이라는 의견이 있었서 http response로 사용하기에 적합한가에 대해 고민해볼 필요가 있다.

proto를 사용했을때의 이점인 직렬화(우리는 data를 직렬화해서 응답으로 보내는 용도로만 사용하기 때문)시에 시간 감소 이점이 사라지진 않은가?
이진 포맷을 사용해서 바이트로 직렬화 했기 때문에 빠른 속도를 보여줬는데 우리는 결국 http json으로 응답을 보내주어야한다.
그렇다면 proto의 byte 직렬화 결과물을 다시 jackson을 사용해서 wrapping해주어야하나?? 이러면 그냥 json응답을 내려줄때보다 시간이 더 걸릴 것 같다는 생각이 든다.

![[Pasted image 20231126215131.png]]

우선 응답 클래스를 그대로 반환하기엔 spring에 http message converter가 예외를 뱉어내는 모습이다.

http message converter를 custom하게 설정해주어야할 것 같다.

`ProtoHttpMessageConverter` 를 bean으로 등록해준다.

![[Pasted image 20231126230731.png]]

rest api 호출 결과가 조금 이상하다,,

http accept header를 명시적으로 붙여줘야 결과가 정상적으로 나오는 것 같다.

![[Pasted image 20231126230920.png]]

mockMvc로 호출한 결과뿐 아니라 일반 http client로 호출하는 결과도 마찬가지다.

![[Pasted image 20231126231030.png]]

일반 호출시에는 content-type이 proto로 자동으로 세팅된다.

![[Pasted image 20231126231128.png]]

accept를 application/json으로 명시적으로 설정하고 호출한 경우에는 정상적으로 받는다

front에서 어떤 기술을 사용할지는 모르겠지만 web, app에서 사용하는 http client관련 라이브러리 사용시에 명시적으로 accept를 매번 지정해야한다면 불편할 것 같다
하지만 뭔가 http client 관련 객체를 만들때 accept를 지정하고 만들수도 있을 것 같다는 예상,느낌(?)

![[Pasted image 20231126233720.png]]

protobuf를 json으로 직렬화하는 시간이 일반 pojo를 json으로 직렬화하는 시간보다 대략 2배 더 소요된다..

사용하기 위한 의도를 생각할때 적합하지 않다는 생각이 든다

![[Pasted image 20231126234142.png]]

기본적으로 protobuf를 지원하는 응답이 아닌 이외의 응답일 경우 (application/json) merge메서드를 호출해서 jsonformat을 직접 맞추는데 이 행위가
jackson objectmapper를 통해 json응답을 내려주는 것보다 과정과 처리가 더 길어서 이러한 결과가 나온것 같다.
