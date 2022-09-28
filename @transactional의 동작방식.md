```
AOP를 통해 어떻게 @transactional이 동작하는지 코드를 직접 확인해보자
```
- https://github.com/skarltjr/Spring/blob/main/AOP.md


## ⭐️Transactional의 동자 방식을 코드로 알아보자
- <img width="1888" alt="스크린샷 2022-09-27 오후 8 36 12" src="https://user-images.githubusercontent.com/62214428/192515127-acd188d6-3a25-4fa5-a4da-476a8e20b737.png">
```
먼저 위 사진에서 알아볼 수 있는것은 @Transactional 어노테이션이 사용되는곳에서 transactionalMethodExecution()를 호출하는데...
```
- <img width="1889" alt="스크린샷 2022-09-27 오후 8 47 53" src="https://user-images.githubusercontent.com/62214428/192517305-a6162ea1-7b1a-44a1-99a4-2917087ab63d.png">
```
앞서 JtaAnnotationTransactionAspect이 상속받고 있는 AbstractTransactionAspect를 살펴보면
@Transactional어노테이션이 붙은곳에선 transactionalMethodExecution(txObject)이 실행되는데, @Around = 즉 타겟 매서드 전,후로 영향을 줄 수 있다
그리고 transactionalMethodExecution() 내부에서 invokeWithinTransaction()을 호출하는데


@Aspect는 기능적인 역할을 하는 모듈로 실제 비즈니스 로직을 제외한 기능적인 역할을 하는 코드들을 분리, 재사용 할 수 있도록한 모듈
@Around는 Around는 traget 메서드를 감싸는 Advice.즉, 앞과 뒤에 모두 영향을 미칠 수 있다.
Advice : AOP에서 실제 실행되는 코드, 여기서는 트랜잭션 로직
```
- <img width="861" alt="스크린샷 2022-09-27 오후 9 04 23" src="https://user-images.githubusercontent.com/62214428/192520630-a8a31ab6-6b06-4e48-893c-57717f28995d.png">

```
따라서 실질적으로 동작하는 코드(advice =. invokeWithinTransaction())를 살펴보기 위해 AbstractTransactionAspect이 상속받고 있는 TransactionAspectSupport를 살펴보자

@Transactional 어노테이션이 사용되는 곳에서 transactionalMethodExecution()을 호출하고 이 매서드는 타겟매서드 전,후로 영향을 줄 수 있는데
이때 동작하는 코드(advice = invokeWithinTransaction())의 로직을 살펴보면 
1. 트랜잭션을 만든다.(시작한다)
2. proceedWithInvocation = 타겟 매서드를 실행시킨다. / 즉 우리가 작성한 로직을 수행한다
3. 그 과정에서 만약 exception이 발생하면 -> 예외를 던지고 트랜잭션을 종료한다.
  - completeTransactionAfterThrowing을 따라가보면 rollback을 수행하는 코드가 존재
4. 만약 예외없이 잘 처리되었다면 트랜잭션 커밋
```


## PSA
- 내가 어떤기술을 쓰던 상관없이 올바르게 transaction이 동작되는 이유가 뭘까?
- <img width="785" alt="스크린샷 2022-09-28 오후 5 29 35" src="https://user-images.githubusercontent.com/62214428/192729805-4c258ba2-5a69-4bd8-ab30-9463ceec5a7b.png">

```
스프링 트랜잭션은 최상위 platformTransactionManager 인터페이스에 의존하고 
psa = portable service abstraction / 즉 편리하 사용을 위해 그 구현체가 미리 구성되어있기까지하 추상화가 이루어져있다.
그래서 우리는 어떤 기술을사용하든 최상위 인터페이스에 의존하고 해당 기술에 맞게 DI가 이뤄져서 transaction 사용이 가능하다.
```
- <img width="927" alt="스크린샷 2022-09-28 오후 5 33 22" src="https://user-images.githubusercontent.com/62214428/192730718-8e298adc-4d60-403f-a51d-79a175c8fb3d.png">
```
트랜잭션을 생성하는 createTransactionIfNecessary()을 따라가보자
최상위 platformtransactionManager에게 트랜잭션 어트리뷰트를 제공하여 기술에 맞는 트랜잭션을 얻어온다.
결국 항상 예시로보던 아래 코드와 동일한데

-> @transactional이 붙은 곳에서는 transactionalMethodExecution()이 동작하고
-> transactionalMethodExecution()은 @Around로 타겟매서드 전후에 영향을끼치는데
-> 전 : 트랜잭션이 없으면 생성
-> 중간 : 비즈니스 로직 수행
-> 후 : 결과에 따라 롤백 및 커밋 후 트랜잭션 회수이다

그런데 이때 "전"에 해당하는 단계에서 PSA를 통해 우리는 최상위 
platformTransactionManager라는 인터페이스에 의존하고 상황에맞는 구현체가 DI로 주입된다.
```

```
class Test {
    private final PlatformTransactionManager transactionManager;

    public static void main(String[] args) {
        /**
         * 트랜잭션 시작
         */
        TransactionStatus status = transactionManager.getTransaction(new DefaultTransactionDefinition());
        try {
            logic();
            transactionManager.commit(status); //성공시 커밋
        } catch (Exception e) {
            transactionManager.rollback(status);
            throw new IllegalStateException(e);
        }
    }
}
```


```
참고:
.aj 파일은 aspectj를 말한다
```
