### @Autowired
```
도대체 어떻게 의존관계가 자동 주입되는가.
DI가 어떻게 작동하는건지 알아보자

@Autowired는 의존 객체의 타입에 해당하는 빈을 찾아 주입해주는데...

여기서는 @Autowired사용하는 필드주입이나 @Autowired를 사용하는 경우말고 생성자 주입 Di를 알아보고자한다.
왜? 내가 많이 사용하니까

```

```
우선 큰 흐름은 getBean -> doGetBean -> createBean -> doCreateBean -> createBeanInstance 
순서임을 기억하고 가보자
```

### 1. DefaultListableBeanFactory.preInstantiateSingletons()
```
DefaultListableBeanFactory.preInstantiateSingletons()매서드에서 빈의 이름을 토대로 빈을 생성한다
```
- <img width="885" alt="스크린샷 2022-10-02 오후 12 22 12" src="https://user-images.githubusercontent.com/62214428/193436468-7efc7067-c989-4927-9b27-228be444a7bc.png">
```
먼저 실제로 내가 등록하고자한 빈들의 이름을 확인할 수 있었고
```
- <img width="1573" alt="스크린샷 2022-10-02 오후 12 25 28" src="https://user-images.githubusercontent.com/62214428/193436522-9aa59eed-c8b8-4dbe-8db1-ce13d04ab5df.png">
```
이러한 빈 이름들이 들어있는 beanNames를 반복 돌면서 하나씩 생성하기 위해 getBean()을 호출한다.
```
### 2. AbstractBeanFactory.getBean()
- <img width="730" alt="스크린샷 2022-10-02 오후 1 11 42" src="https://user-images.githubusercontent.com/62214428/193437464-d5289738-d971-41b2-8eca-54ac2a907b25.png">

### 3. AbstractBeanFactory.doGetBean()
- <img width="976" alt="스크린샷 2022-10-02 오후 4 08 25" src="https://user-images.githubusercontent.com/62214428/193442405-85bb9a6b-a488-4108-b2c9-8eb4424e87fd.png">
```
doGetBean()매서드에서 스코프에따라 빈을 생성하기 위해 createBean()매서드를 호출하는데
```
```
createBean() -> doCreateBean()
```

### 4. AbstractAutowireCapableBeanFactory.doCreateBean()
- <img width="855" alt="스크린샷 2022-10-02 오후 4 11 41" src="https://user-images.githubusercontent.com/62214428/193442508-541f5365-755e-40ee-833e-69cb7cdf5efe.png">
```
참고 : AbstractAutowireCapableBeanFactory의 역할


Abstract bean factory superclass that implements default bean creation,
with the full capabilities specified by the RootBeanDefinition class.
해석 : 기본 빈 생성을 구현하는 추상 빈 팩토리 상위클래스이며,
RootBeanDefinition 클래스에서 지정한 모든 기능을 사용할 수 있다. 

Implements the AutowireCapableBeanFactory interface
in addition to AbstractBeanFactory's createBean(java.lang.Class<T>) method.
해석 : AutowireCapableBeanFactory 인터페이스를 구현하며
빈 생성을 담당하는 메서드인  AbstractBeanFactory 클래스의 createBean 메서드가 있다.

Provides bean creation (with constructor resolution), property population,
wiring (including autowiring), and initialization.
⭐️해석 : 어떤 생성자 기반의 빈 생성을 할지, 엔티티에서 프로퍼티 설정은 어떻게 할지,
빈 간의 관계 설정(자동설정 포함)과 초기화에 대한 기능을 제공한다.

Handles runtime bean references, resolves managed collections, calls initialization methods, etc.
Supports autowiring constructors, properties by name, and properties by type.
⭐️해석 : 런타임 빈 참조, 스프링에서 관리되는 컬렉션 타입 선택, 초기화 메서드 호출 등을 다룬다.
생성자 autowiring, 이름에 의한 프로퍼티 설정, 타입에 의한 프로퍼티 설정을 지원한다.
```

### 5. AbstractAutowireCapableBeanFactory.createBeanInstance()
```
⭐️ 여기서 이제 우리가 주입하고자하는/ DI의 대상이 되는 생성자 정보를 바탕으로 빈을 생성하고 주입해주는데
```
- <img width="942" alt="스크린샷 2022-10-02 오후 4 25 25" src="https://user-images.githubusercontent.com/62214428/193442997-8de85741-82d8-4dfb-8305-676b2ca1454d.png">

### 6. AbstractAutowireCapableBeanFactory.determineConstructorsFromBeanPostProcessors()
```
먼저 DI 대상이 되는 생성자 정보를 가져와야한다.
```
- <img width="1028" alt="스크린샷 2022-10-02 오후 4 27 32" src="https://user-images.githubusercontent.com/62214428/193443074-59c6a26a-c450-4734-a947-8882b28ced96.png">

```
어떤 정보를 가져왔는지 확인해보면
Di 대상이 되는 놈이 어떤 클래스인지, 생성자 정보(파라미터등)을 가져온다
```
- <img width="1465" alt="스크린샷 2022-10-02 오후 5 10 56" src="https://user-images.githubusercontent.com/62214428/193444671-3725d59f-b673-4f23-9197-f8132cebe46f.png">
```
확인해보면
```
- <img width="495" alt="스크린샷 2022-10-02 오후 5 14 24" src="https://user-images.githubusercontent.com/62214428/193444723-ba0aa49a-394c-45d2-9f28-8773548869c4.png">


### 7. SmartInstantiationAwareBeanPostProcessor.determineCandidateConstructors()
```
솔직히 더 궁금했다.
어떻게 저 정보 가져왔는지 궁금했다
SmartInstantiationAwareBeanPostProcessor 인터페이스는 결과적으로 BeanPostProcessor인터페이스를 상속받는데
BeanPostProcessor를 알아볼 필요가 있었다.

그리고 이게 autowiring의 핵심이지 않을까한다.
```
⭐️ BeanPostProcessor
```
빈 후처리기는 스프링이 빈 저장소에 등록할 목적으로 생성한 객체를!!!
빈 저장소에 등록하기 전에!!!
조작하게 해주는 기능

객체를 조작할수도, 다른 객체로 교체할수도있다.

즉 생성자 주입이 일어나는 대상 ex) controller 빈 생성전 빈후처리기를 통해 
앞에서 가져온 생성자,파라미터 정보를 활용하여 필요한 의존관계 요소(myservice, myservice2..) 주입 후 생성하는데
```


```
마지막으로 생성자 타입 및 파리미터 정보를 다 구비했으니 이제 빈을 생성할 차례다
```

### 8. ConstructorResolver.autowireConstructor()
- <img width="923" alt="스크린샷 2022-10-02 오후 5 38 18" src="https://user-images.githubusercontent.com/62214428/193445862-639eec4e-3d19-41d7-a394-2e4d5682fa58.png">
```
그럼 빈을 생성하는 instantiate 매서드를 살펴보면
참고 : instantiate() 리플렉션 기반의 빈 생성을 시도한다.
```

### 9. ConstructorResolver.instantiate()
- <img width="1027" alt="스크린샷 2022-10-02 오후 5 53 43" src="https://user-images.githubusercontent.com/62214428/193446261-95298fe4-bb93-4b5c-9aa7-cc8e1b3edd05.png">

### 10. SimpleInstantiationStrategy.instantiate()
```
BeanUtils를 호출하여 최종적으로 빈을 생성
```
- <img width="851" alt="스크린샷 2022-10-02 오후 5 55 11" src="https://user-images.githubusercontent.com/62214428/193446352-01b0d453-b381-4105-bd67-3d199cce9a45.png">

### 11. BeanUtils.instantiateClass()
- <img width="1123" alt="스크린샷 2022-10-02 오후 5 55 35" src="https://user-images.githubusercontent.com/62214428/193446346-b26b8631-d42a-4fc8-aca0-09bcbbbfc532.png">
```
앞에서 생성을 위한 생성자 정보를 받아왔는데
빈 생성 대상이 되는 ex) controller 객체에 대해 먼저 반복문을 통해 service와 같은 의존관계를 주입한 후
최종적으로 controller 객체를 빈으로 생성해낸다.

리플렉션을 통한 빈 생성 완료
```


- https://github.com/skarltjr/Spring/blob/main/annotation기반의%20빈%20등록%20동작%20원리알아보기.md
```
정리해보자면 먼저 위 링크를 통해 알 수 있듯이 빈이 등록될 수 있는데
생성자 주입의 경우 빈 생성 과정을 살펴보면

defaultListableBeanFactory 클래스에 빈 denifition map에 저장된 정보를 바탕으로 
⭐️AbstractAutowireCapableBeanFactory 클래스에서 빈 인스턴스를 생성한다.
중요한것은 빈 후처리기를 통해 빈으로 등록할 객체를 생성 후 빈 저장소에 저장하기전 생성자 정보를 가져올 수 있는데
생성자주입의 경우 이를통해 DI의 대상이 되는(예를들어 service를 주입받은 controller)의 생성자 정보를 바탕으로
리플렉션을 통해 생성자를 makeAccessible 후 조작하여 빈을 생성해낸다.

즉 의존성을 주입하고 빈을 생성한다.

그럼 왜 생성자 주입은 의존관계 주입 후 빈을 생성하는걸까?
생각해보면 생성자 주입의 특징은 불변!
즉 변한일이 없으니 주입 후 생성

반면 필드,세터의 경우 변경될 여지가 있으니 
깡통 빈을 생성하고 이후에 의존관계를 주입한다고... 생각을한다..나는
```


```
참고로 생성자 주입의 경우도 단일 생성자가 아닌 경우 @Autowired를 명시한다.
중요한것은 생성자 주입이냐 / 필드,setter 주입이냐이고 두 방식의 동작 방식이 다르다

이는 injectionMetadata 클래스의 inject 매서드를 브레이크 포인트 걸고 디버깅해보면 알 수 있다.
생성자주입은 필드 주입이 아니기에 걸리지 않는다. 방식자체가 다르다
```
```
public class Controller {
    private final MyService myService;
    private final MyService2 myService2;
    private final ConcurrentService concurrentService;
    private final SampleServ sampleServ;

    @Autowired  // @Autowired가 있고없고를 떠나서 이 방식은 생 성 자 주입
    public Controller(MyService myService, MyService2 myService2, ConcurrentService concurrentService, SampleServ sampleServ) {
        this.myService = myService;
        this.myService2 = myService2;
        this.concurrentService = concurrentService;
        this.sampleServ = sampleServ;
    }
```

```
// 해당 방식은 필드 주입으로 
public class Controller {
    @Autowired
    private MyService myService;
    @Autowired
    private MyService2 myService2;
    @Autowired
    private ConcurrentService concurrentService;
    @Autowired
    private SampleServ sampleServ;
    
    public Controller(MyService myService, MyService2 myService2, ConcurrentService concurrentService, SampleServ sampleServ) {
        this.myService = myService;
        this.myService2 = myService2;
        this.concurrentService = concurrentService;
        this.sampleServ = sampleServ;
    }
```
