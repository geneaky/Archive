
배치 계층 구조에서 가장 상위의 개념으로 하나의 배치 작업 자체를 의미
job 설정을 통해 생성되는 객체 단위

 반드시 한 개 이상의 [[Step]] 으로 구성된다


##### 제공되는 기본 구현체

###### Simple Job

- 순차적으로 step을 실행
- 보편적인 표준 기능 내재

###### Flow Job

- 특정 조건과 흐름에 따라 Step을 구성하여 실행
- Flow 객체를 실행시켜서 작업을 진행


Job Launcher가 Job Parameter와 Job객체 자체를 인자로 받아 실행(run)한다

Job객채의 execute() 메서드는 여러 Step을 실행한다


