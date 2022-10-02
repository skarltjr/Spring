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


### 4. AbstractAutowireCapableBeanFactory.doCreateBean()


