
ssl을 적용하다보면 위와 같은 형식파일을 보게되는데 차이를 적어본다

PEM은 privacy enhanced mail의 약자로 base64로 인코딩한 텍스트 형식의 파일이고
binary 형식의 파일을 전송할 때 손상될 수 있으므로 인코딩하며, 소스 파일로는 모든 바이너리가 가능하지만 주로 인증서나 개인키가 된다.
(aws ssh접속용 개인키도 pem 형식)

pem키로 변환 후 어떤 바이너리 소스를 pem으로 변환했는지 구분하기 위한 표시로
``` sh
-----BEGIN 땡땡-----
어쩌구저쩌구
-----END 땡땡-----
```
이렇게 표현됨

CRT는 cert약자로 pem 형식의 인증서를 의미하고, linux, unix계열에서 .crt확장자를 많이 사용함
인증서는
``` sh
-----BEGIN CERTIFICATE-----
어쩌구저쩌구
-----END CERTIFICATE-----
```
구분이고
(CER은 Windows에서 인증서 내보내기할때 사용하는 확장자로 pem형식의 인증서를 의미)

CSR은 Certificate Signing Request의 약자로 인증기관에 인증서 발급 요청하는 ASN.1 형식 파일로
이 안에 공개키 정보와 사용하는 알고리즘 정보가 들어있음
이것도 보통 pem 형식으로 인코딩해서 전달하는데 `인증서 발급 요청`을 의미하다 보니 내용에 request가 들어감
``` sh
-----BEGIN 무슨무슨 certificate REQUEST-----
어쩌구저쩌구
-----END 무슨무슨 certificate REQUEST-----
```
