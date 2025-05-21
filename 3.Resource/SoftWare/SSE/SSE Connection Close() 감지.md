
client에서 close한 커넥션 정보를 서버가 즉각적으로 감지하지 못 할 수 있음
TCP 수준에서 연결 종료가 감지될 때까지 약간의 시간이 걸릴 수 있습니다
이를 보완하려면 서버가 **주기적으로 ping을 보내고**, 오류 발생 시 emitter를 정리하는 방법도 병행합니다

tomcat http connection timeout, keep alive timeout 조절로 테스트 -> tcp보다 상위레이어라 조절 불가능 하려면 os커널 파라미터에서 설정해야함

대안
sse connection timeout을 설정하고 주기적으로 ping을 보내 만료된 커넥션 정리 필요 
 