#### docker network 정리

##### 배경정리
1. docekr container는 격리된 환경에서 돌아가기 떄문에 각 컨테이너간 통신이 불가능하다는 것이 기본 전제  

##### 이 문제를 해결하기위해 docker network가 존재

여러개의 컨테이너를 하나의 네트워크로 묶으면 서로 통신이 가능함.    


1. docker network 조회 command

``` docker
$ docker network ls
```

docker daemon이 실행되면 기본적으로 bridge, host, none 네트워크가 생성된다. 

2. docker network 종류(지원하는 network driver 종류)

  * `bridge` : 하나의 호스트 컴퓨터 내에서 여러 컨테이들이 서로 소통할 수 있도록해준다.(default network)
  * `host` : 컨테이너를 호스트 컴퓨터와 동일한 네트워크에서 컨테이너를 돌리기 위해 사용한다.
  * `overlay` : 여러 호스트에 분산되어 돌아가는 컨테이너들간 네트워킹을 위해 사용한다.


3. containers in same network

	- 같은 virtual network로 묶인 컨테이너간 통신시 각 컨테이너의 서비스명이 hostname에 대응된다, 때문에 컨테이너간 통신시 컨테이너명으로 url을 지정해주어 통신할 수 있는 것
