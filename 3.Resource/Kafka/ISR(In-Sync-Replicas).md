
리더 파티션과 팔로워 파티션이 sync가된 상태 (두 파티션의 offset이 동일한 상태)

ISR이 완료되지 않은 상태에서 리더 파티션이 죽은 경우 팔로워 파티션이 리더 파티션으로 승급되어야 하는데
유실된 데이터가 생기기 때문에 이에 대한 설정을 지정해야한다 

``` yml
unclean.leader.election.enable=true // 유실을 감수함
unclean.leader.election.enable=false// 유실을 감수하지 않고 현재 죽은 리더 파티션이 복구할 때까지 기다리게된다.

```

