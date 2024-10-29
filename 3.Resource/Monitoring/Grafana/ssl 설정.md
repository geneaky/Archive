certbot을 통해 발급 받은 인증서와 비밀키를 grafana.ini의 cert_key, cert_file property에 경로 지정을 해두면 됨
[참고1](https://grafana.com/docs/grafana/latest/setup-grafana/set-up-https/#before-you-begin)
[참고2](https://afsdzvcx123.tistory.com/entry/Grafana-ssl-key-%EB%B0%9C%EA%B8%89-%EB%B0%8F-https-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0)


certbot 인증서 갱신
``` sh
sudo certbot renew
```