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
즉 동일빈내에서는 this로 매서드를 호출하고 이 경우 프록시 객체가 아닌 원본 객체의 매서드를 실행

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

------------
- 그런데 다음 상황도 한 번 예측해보자
- <img width="839" alt="스크린샷 2022-10-06 오후 7 37 12" src="https://user-images.githubusercontent.com/62214428/194292323-799cd87d-fbb4-4d65-8ed4-e87f78be1133.png">
```
전파레벨이 new라면 새로운 트랜잭션이 동작하고 서로 영향을 받지 않기에 1~number-1까진 저장이되어야할텐데...
모두 롤백처리된다. 즉 new 옵션이 작동하지 않는것
```
```
왜냐하면!!!

결국에 something() 매서드에서 맨 마직에 호출하는 매서드인 save()는 사실 this.save()이고 
이 경우 프록시 객체의 매서드가아닌 원본 객체의 매서드를 실행하기때문에 호출당하는쪽의 매서드의 @Transaction 어노테이션은 동작하지 않는다.
따라서 이 경우도 빈을 분리하는 방법을 통해 해결할 수 있다.
```
```
그런데 depth가 깊어지면?
빈을 계속 분리할 수 있나?
```

### 정확히 알고가자
- <img width="529" alt="스크린샷 2022-10-19 오후 4 47 56" src="https://user-images.githubusercontent.com/62214428/196629158-a5216ae8-9896-49c3-8cb5-c85ea5aa35fd.png">
```
외부에서 호출되는 매서드가 first()라고 했을때
second()의 @Transactional은 동작할까??


안한다. 이것도 결국 this.second()로 
서로다른 빈에서 호출하는게 아니라 동일빈 / this에 의해 호출되고
프록시가 아닌 원본 매서드가 활용되는것

그런데!!!! 
second()의 save()매서드의 동작 자체는 first()에서 시작한 트랜잭션에 묶여 
하나의 트랜잭션으로 처리된다

왜!!!!냐면
바로 jpaRepository가 상속받은 crudRepository의 구현체이 simpleJpaRepository를 살펴보면 알 수 있듯이
crudRepository의 매서드들은 기본적으로 @Transactionl이 붙어있다


즉 결과적으로 second()의 save()매서드는 서로다른빈에서 simpleJpaRepository빈의 save()매서드를 호출하는거기에
@Transactional이 동작하고 기본 required로 이미 시작된 트랜잭션이 전파된다
```
- <img width="744" alt="스크린샷 2022-10-19 오후 4 50 01" src="https://user-images.githubusercontent.com/62214428/196629597-bc41c5cb-571d-4d08-aa70-ed88d20633c1.png">

------

### 해결 방법2 - dependency lookup
- <img width="767" alt="스크린샷 2022-11-07 오후 7 28 13" src="https://user-images.githubusercontent.com/62214428/200288012-8b300edb-a39f-45bc-86a1-00a25ee742ef.png">
```
application context를 의존성으로 갖고 
현재 자기자신 빈을 가져오면
```
- <img width="1243" alt="스크린샷 2022-11-07 오후 7 32 21" src="https://user-images.githubusercontent.com/62214428/200289204-d4b88232-996a-4ac8-89b5-18cd16e3069d.png">
```
가져온 빈인 mock을 봐보자
this.save()의 경우 원본 매서드가 호출되지만
mock은 cglib에 의해 생성된 프록시 객체고 그렇기때문에 mock.save()의 transactional이 동작한다.
```

```
실제로 전파레벨 new도 동작하는데
```
- <img width="1199" alt="스크린샷 2022-11-07 오후 7 26 38" src="https://user-images.githubusercontent.com/62214428/200290358-6354f400-1fa2-4891-a8c9-5fac1ea0b740.png">

```
근데 왜 mock 변수는 프록시 객체일까?
- @transactional 즉 aop가 동작해야하는 빈 대상으론 프록시 객체가 생성되고 
- 빈 레지스트리에 해당 프록시 객체가 등록된다.

그래서 빈으로 등록될 객체에 aop 적용대상이 하나도 없으면
여기선 transactional 어노테이션이 하나도 없으면 원본 객체타입이다. mock이
```
```
근데 application context라는 거대한 스프링코드를 의존성으로 갖으면
테스트가 가능할까? 이 부분은 고려해봐야한다.
```
