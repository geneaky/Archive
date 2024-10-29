
rabbitmq 모니터링시 key metircs를 찾아보았다 
[참조](https://www.datadoghq.com/blog/rabbitmq-monitoring/#key-rabbitmq-metrics)


#### Exchange metrics

- message published in : throupghput
- message unroutable : errors


#### Node

- file descriptor used : resource
- network sockets : resource
- disk space : resource
- memory : resource


#### Connection

- data rates (number of octest or bytes sent/received within a TCP connection per secon) : resouce

#### Queue

- queue depth : saturation(큐의 포화도)
- message unacknowledge : error
- message ready
- persistent
- message bytes persistent/paged out
- message bytes RAM
- number of consumers
- consumer utilization

특정 threshold(임계값) 밑으로 떨어지는 상태에 대한 모니터링을 자주 언급