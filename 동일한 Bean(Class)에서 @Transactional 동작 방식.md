## 개요
```
동일한 빈(클래스)에서 @Transactional을 사용할 때 발생할 수 있는 문제점
- AOP의 특성을 고려하라
```

## 문제 상황
- <img width="407" alt="스크린샷 2022-07-03 오후 5 36 02" src="https://user-images.githubusercontent.com/62214428/177031926-735ae1fb-19d7-4f56-ad50-09971145e8e0.png">
- <img width="576" alt="스크린샷 2022-07-03 오후 5 34 07" src="https://user-images.githubusercontent.com/62214428/177031870-b4f34815-1c0b-43a3-b1ea-fcd904fdb308.png">
```
1.현재 컨트롤러에서 service.something()을 호출
2. something()은 동일 클래스에 존재하는 save()를 호출

위 사진에서처럼 service.save()에서 exception이 발생했을 때 모두 롤백이 될까?
```
- <img width="303" alt="스크린샷 2022-07-03 오후 5 37 19" src="https://user-images.githubusercontent.com/62214428/177031963-2e960c7f-fa20-48b2-ad98-0a98498f0ac1.png">
```
Input으로 10을 넘겼을 때 9에서 예외가 발생할 것
⭐️그런데 1~8까진 모두 rollback되지 않았다??!!!!!
```

```
즉 내가 기대한것은 AOP를 활용한 @Transactional을 통해 something()매서드를 트랜잭션이라는 부가기능으로 감싸서 동작하는것을 기대
 == > something() 매서드는 하나의 트랜잭션으로 수행되어 all or nothing - 하나라도 실패하는 경우 모두 롤백될것을 기대했다
 
그러나 여기 결과를 고려했을 때 something()은 단일 트랜잭션으로 동작하지 않았으면
repo.save()마다 단일 트랜잭션을 수행한 것
즉 Bean에 대해서 AOP가 동작하지 않았다고 판단할 수 있다.
```

## 무엇이 문제일까? - 의심
```
눈에띄는것은 바로 something()에서 내부 매서드를 호출
즉 외부에서 바로 service.save()에 접근한것이 아니다
```

## 원인
```
일단 스프링부트는 AOP Proxy 생성 방법으로 CGLIB을 기본 채택

그리고 Proxy Default Mode에서는 외부 매서드(즉 외부bean / 서로 다른 bean)에서 호출하는 경우에만 프록시가 동작

결과적으로 지금 나의 코드는 다른 bean이 아닌 // myService에서 동일한 bean(myService) 내부 매서드를 호출하는 형태고
그렇기에 프록시가 동작하지 않았고  // 프록시가 동작하지 않기에 AOP를 통한 @Transactional이 동작하지 않은것이다
```

## 해결 방법
```
그렇다면?? 
해결은 어떻게??

바로 bean을 분리하는것
즉 myService에서 동일 bean 내부 매서드를 호출하지 않고
save() 매서드를 별도의 bean으로 빼고 이를 호출하는 형태를 가져가면 된다.
```
- <img width="366" alt="스크린샷 2022-07-03 오후 5 55 50" src="https://user-images.githubusercontent.com/62214428/177032607-4101c64b-e471-45fc-9cf3-030ed456507d.png">
- <img width="358" alt="스크린샷 2022-07-03 오후 5 55 57" src="https://user-images.githubusercontent.com/62214428/177032608-477397e7-cdd2-4a85-b586-8bc739d0a873.png">
- <img width="546" alt="스크린샷 2022-07-03 오후 5 56 01" src="https://user-images.githubusercontent.com/62214428/177032612-b7e1b37f-951f-47df-89e8-80fa382854cd.png">
- <img width="271" alt="스크린샷 2022-07-03 오후 5 55 39" src="https://user-images.githubusercontent.com/62214428/177032623-854d70c7-8006-4513-9e04-8eb95da50a7b.png">
