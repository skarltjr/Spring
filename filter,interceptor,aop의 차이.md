## filter,interceptor,aop의 차이
- <img width="763" alt="스크린샷 2022-07-16 오후 4 21 54" src="https://user-images.githubusercontent.com/62214428/179344813-6305f0c2-ab0e-4689-855f-3c5bbb310069.png">

## 개념

- Filter
```
- 서블릿 필터는 DispatcherServlet 이전에 실행이 되는데 필터가 동작

쉽게 말해 입구와 출구에서 요청과 응답을 걸러내며, 정제한다
대표적으로 보안측면에서 사용자의 req을 시스템에 전달하기전 허용해도 되는가를 판단하고 걸러낸다
response에 대해서도 변경등의 처리를 수행할 수 있다.

ServletRequest객체를 통해 요청의 IP, hostname, 프로토콜 형식, 서버이름, 포트번호, ContentType 등을 가져올 수 있다
```

- Interceptor
```
- Interceptor는 스프링의 DispatcherServlet이 컨트롤러를 호출하기 전, 후로 끼어들기
때문에 스프링 컨텍스트 내부에서 Controller(Handler)에 관한 요청과 응답에 대해 처리하며 스프링의 모든 빈 객체에 접근할 수 있다

filter에 의해 걸러진 요청( 처리해야할 요청)들을 처리하기전 해당 요청을 가로채 필요한 행위를 한다
예를들어 나같은 경우는 모든 요청이 처리되기전 먼저 유저가 확인해야할 알림이 있는지 확인하고 
있다면 알림 표시를위해  모델에 알림 내용을 추가하여 전달한다.
-> 그러면 사용자가 중간중간 ex) 다른 페이지로 넘어간다는 요청을한 경우 
-> 해당 요청을 인터셉터가 먼저 가로채고 만약 알림이 있다면 알림 표시를 추가된 페이지로 이동한다
```
- https://github.com/skarltjr/allhourstudy/blob/0056c8759665e397c3fb0076b4aeb4df27126ce5/src/main/java/com/allhour/allhourstudy/modules/notification/NotificationInterceptor.java 

- AOP
```
aop는 결이 약간 다르다
로직을 핵심 관심사, 부가적인 관심사로 분리하고 각 관심사를 모듈화하여 추후 재사용성을 도모하는 객체지향적 관점에서 바라보자
요청의 처리를위해 매서드를 직접호출하는것이 아닌 가짜 객체인 프록시가 이를 처리함으로써 부가기능을 주입하거나 서비스에 직접 접근을 방지할 수 있다.

Interceptor나 Filter와는 달리 "메소드" 전후의 지점에 자유롭게 설정이 가능하다.
```


