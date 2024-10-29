
#### 사건
모니터링 , 브로커 서버가 죽음

#### 원인
rabbitmq, grafana 도커 컨테이너에서 쌓은 로그가 많아져 전체 디스크 용량이 20kb정도 남게되어 이후 각 컨테이너에서 디스크 사용이 불가능해짐

#### 해결 순서

우선 디스크 사용량을 확인하기 위해 아래 명령어로 전체 디스크 사용량 확인
``` bash
df -h
```

이후 /etc/docker 디렉토리에서 현 디렉토리 사용량을 확인 

```
du -h --max-depth=1 2>/dev/null
```

docker/containers/${container_id}/${container_id}-json.log 로그 용량 확인 후 삭제

이후 로그 파일을 최대 보관 사이즈와 개수를 지정

/etc/docker/daemon.json에 설정 지정

``` json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

1일 혹은 n일 로그씩 묶어서 압축후 s3에 백업 후 로그 삭제 cron을 추가하는 방식으로 용량 관리 
