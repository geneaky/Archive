
ClassPathResource를 사용하여 resource를 불러와 사용하는 경우

```java

ClassPathResource resource = new ClassPahtResource("/test.properites");

resource.getFile(); //NullPointerExceptoin, IOException
```

파일형태로 resource를 불러와 사용하려는 경우 jar, war와 같이 배포된 상황에서 resource내에 있는 파일에 ==직접적인 접근이 불가능하다.==

그래서 이경우 resource 를 파일로 받는것이 아닌 InputStream으로 받아서 사용하도록 한다.

