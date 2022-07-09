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

## 3. Dispatcher servelt의 구조
```
Dispatcher Servelt의 생명주기는 init - service - destroy로 
init시 아래 요소들을 생성한다.
```
- <img width="804" alt="스크린샷 2022-07-09 오후 5 15 00" src="https://user-images.githubusercontent.com/62214428/178097681-21d92235-b14f-47de-a3f3-09a4e7e599bf.png">

## 4. 그럼 어떻게 Dispatcher servelt에서 컨트롤러를???
```
생각해보자.
Dispatcher servelt은 핸들러 매핑을 통해 해당 요청을 처리할 컨트롤러를 찾는다.
그런데 이 컨트롤러 정보를 어떻게 알고 매핑하는가?

 
```
- <img width="961" alt="스크린샷 2022-07-09 오후 5 31 45" src="https://user-images.githubusercontent.com/62214428/178098289-1aed4bac-2ee0-4aa8-9620-7a22cb25c69a.png">
