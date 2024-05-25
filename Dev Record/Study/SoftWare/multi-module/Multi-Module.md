멀티 모듈

하나의 서비스나 프로젝트를 여러개의 모듈로 분리한 것

분리한 이유? 
중복코드를 줄일 수 있고, 어떠한 기준에 맞춰 모듈을 분리했기 때문에 유지보수도 쉬움
어떠한 기준이라 하믄 개인이 판단하기에 도메인 기준, 백엔드 기준(서비스 관리/운영 측면)을 예시로 들 수 있겠고 개인이 더 명확하고 좋다고 생각하는 기준이 있으면 그걸 기준으로 세워도 좋을듯 하다

멀티보듈 프로젝트가 중요한 이유?
초기에 이루어져야 하는 일련의 설계 결정.
요소의 구조와 그 관계에 관한것.
나중에 변경하기 어렵다는 의미

모듈 간 역할, 책임, 협력 관계가 올바른지 고려하여 생각해야한다.

##### 주의
core & common
서비스 도메인 기반의 구조가 아니라 기술 베이스 지향적인 모듈 구조
데이터 접근 모듈을 분리
외부 api 연동 모듈 분리 

그렇지만 중복 코드를 없애자고 모든 모듈에서 core & common을 의존하고 있으면 사용하지 않는 의존성이 너무 많이 생기므로 일부 코드 중복을 수용하는 것이 좋다고 생각함


##### 멀미모튤 그룹 경계 나누기
- Boot(Server) : 서버모듈(잦은변화) - batch, admin, api
- Data(Domain) : 데이터모듈(+도메인) -  meata, user, chart
- Infra : 연동모듈(큰변화) - 유관부서 및 업체연동을 위한 모듈 vod, photo, billing
- Cloud(System) : 클라우드(시스템)모듈(변화적음) - config, gateway, discovery, aws, gcp, azure

각 멀티 모듈 그룹 하위에 모듈들이 있다


Data -> infra 모듈 관계 

Data에서 명시된 interface를 구현하는 구현체가 db접근을 하는 경우 Data모듈에 해당 접근 로직을 작성하고
infra 접근에 대한 로직만 infra 모듈에서 구현을 한다.

Server -> data 모듈 관계
service레이어의 구현은 어디에 있어야하나?
각각 모듈에 구현되어야 한다.
예)
server에서 특정 데이터 생성 이벤트를 발송하는 구현을 작성했다면
data에서는 해당 이벤트를 받아서 data가 책임을 맡고있는 데이터 생성 처리를 진행한다.

cloud 모듈과 server 모듈 관계


---



모듈 분리, 분리 기준, ddd
클린 아키텍처, 헥사고날아키텍처, 포트어댑터패턴
usecase
query, command