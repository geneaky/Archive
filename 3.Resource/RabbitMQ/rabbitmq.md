RabbitMQ QA List

#### 1. rabbitmq에서 트랜잭션을 지원하는데 이건 뭐지?

```java
  RabbitTemplate rabbitTemplate() {
    RabbitTemplate template = new RabbitTemplate();
    template.setChannelTransacted(true);
    ...
    ...
    return template;
  }
```

rabbitmq transaction config flag를 true로 지정하게되

@Transaction 지정한 메서드 내에서 사용한 rabbitTemplate이 예외가 발생한 시점에 rollback된다. 
publish -- delivery | listener 도착에서 publish --delivery 사이에 발생한 예외를 rollback하

listener가 메시지를 받은 상황에서 발생하는 예외 상황에서는 아래의 방식으로 처리할 수 있다.

1. retry 사용   
  하지만 무한 반복하면 성능상 문제가 될 듯하다 일정횟수 지정해야할듯

2. dlq로 전달   
  dlq에 전달하여 예외 발생한 메시지를 별도 처리하거나 관리

3. 수동 ACK처리   
  리스너에서 수동으로 ACK를 보내서 예외 발생한 메시지를 건너뛰고 진행

4. rabbitmq에서 제공하는 메시지 처리 중지, 큐에서 메시지 삭제 api 호출



#### 2. DLQ, DLX처리 방식

#### 3. back pressure 조절

#### 4.
