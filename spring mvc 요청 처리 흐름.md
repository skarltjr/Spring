## spring mvc 요청 처리 흐름

## 1. 먼저 살펴보자
```
서블릿 : 자바 진영에서 제공하는 웹 애플리케이션 개발 스펙 및 api
Dispatcher servlet : spring mvc는 단 하나의 서블릿이자 front controller 활용하는것이 바로 Dispatcher servlet
서블릿 컨테이너 : 서블릿을 관리하는 컨테이너로 톰캣,제티,언더토 등
```

## 2. spring mvc 요청 처리 흐름
```
1. 클라이언트의 요청
2. Dispatcher Servelt(front controller)에 요청 전달
3. Dispatcher Servelt은 요청 분석
4. Dispatcher Servelt이 해당 요청을 처리할 수 있는 핸들러 탐색( 핸들러 매핑 )
5. Dispatcher Servelt이 해다 요청을 처리할 수 있는 컨트롤러의 매서드 호출하여 요청 처리
6. 예외존재시 예외처리 or 응답 반환
```
- <img width="698" alt="스크린샷 2022-07-09 오후 5 08 10" src="https://user-images.githubusercontent.com/62214428/178097476-2accd9b5-cbde-4019-89ab-785ec461b73a.png">

## 3. Dispatcher servlet의 구조
```
Dispatcher Servelt의 생명주기는 init - service - destroy로 
init시 아래 요소들을 생성한다.
```
- <img width="804" alt="스크린샷 2022-07-09 오후 5 15 00" src="https://user-images.githubusercontent.com/62214428/178097681-21d92235-b14f-47de-a3f3-09a4e7e599bf.png">

## 4. 스프링컨테이너 / 서블릿 컨테이너의 관계
```
스프링은 웹 프레임워크가 아니다.
스프링 프레임워크 중 웹 애플리케이션을 만들고자 등장한 !!모듈!!이 spring mvc이다.
따라서 스프링mvc 사용은 크게 스프링을위한 스프링컨테이너1(루트 컨테이너) / 그리고 서블릿을 위한 스프링컨테이너2 총 2개의 스프링컨테이너와 서블릿 컨테이너의 상호작용을 이해하자
```
- <img width="745" alt="스크린샷 2022-07-09 오후 5 53 57" src="https://user-images.githubusercontent.com/62214428/178099040-eac78f1e-e223-43af-8c6c-8e95330d6c89.png">
```
1. 애플리케이션 실행시 tomcat(was)에 의해 ApplicationContext가 생성된다.
 - was에 의해 첫번째 Application context = 스프링 컨테이너(root) 생성 및 구동
2. 클라이언트로부터 요청이 전달될때 DispatcherServlet이 생성된다.
3. dispatcher servlet에 의해 두번째 스프링 컨테이너가 생성 및 동작한다.
```
