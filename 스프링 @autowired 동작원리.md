### @Autowired
```
도대체 어떻게 의존관계가 자동으로 주입되는가.
DI가 어떻게 작동하는건지 알아보자

@Autowired는 의존 객체의 타입에 해당하는 빈을 찾아 주입해주는데...

나는 여기서 생성자 주입 Di를 알아보고자한다.
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

### 7. SmartInstantiationAwareBeanPostProcessor.determineCandidateConstructors()
```
SmartInstantiationAwareBeanPostProcessor 인터페이스는 결과적으로 BeanPostProcessor인터페이스를 상속받는데

즉 
```



