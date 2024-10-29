
provider: 테라폼으로 생성할 인프라 종류
resource: 실제로 생성할 인프라 자원
state: 생성한 자원의 상태
output: 자원을 변수 형태로 state에 저장하는 것
module: 공통적으로 활용할 수 있는 코드를 문자 그대로 모듈의 형태로 정의하는 것 
remote: 다른 경로의 state를 참조하는 것(output변수를 불러올때 주로 사용)

init: 테라폼 명령어 사용을 위해 각종 설정을 진행
plan: 작성한 코드가 실제로 어떻게 만들어질지에 대한 예측 결과
apply: 실제 인프라 생성 명령어
import: 만들어진 자원을 테라폼 state 파일로 옮겨주는 명령어
state: 테라폼 state를 다루는 명령어, 하위 명령어로 mv, push가 있음
destroy: 생성된 자원 state파일 모두 삭제하는 명령어

### 명령어 process
init -> plan -> apply

plan이 100%로 결과를 보장해주지는 않음.
 