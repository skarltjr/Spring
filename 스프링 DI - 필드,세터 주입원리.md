- https://github.com/skarltjr/Spring/blob/main/스프링%20DI-생성자주입%20동작원리.md
```
빈 생성시 의존관계 주입에 따라 달리 동작한다.
생성자 주입과 그외 주입

이때 빈 생성을 위한 AbstractAutowireCapableBeanFactory.doCreateBean()를 시작으로 살펴보면
createBeanInstance()매서드를 호출하는데 
이때 생성자 주입의 경우 아래 과정을 통해 필요한 의존성을 주입 후 빈을 생성한다.

Constructor<?>[] ctors = determineConstructorsFromBeanPostProcessors(beanClass, beanName);
if (ctors != null || mbd.getResolvedAutowireMode() == AUTOWIRE_CONSTRUCTOR ||
    mbd.hasConstructorArgumentValues() || !ObjectUtils.isEmpty(args)) {
  return autowireConstructor(beanName, mbd, ctors, args);
}

반면 그외 의존성 주입의 경우 의존성 주입 후 빈 생성이 아닌 빈 생성 이후 의존성을 주입.
따라서 AbstractAutowireCapableBeanFactory의 doCreateBean() 내부의 createBeanInstance()에서
instantiateBean()을 호출하는데...

```

### AbstractAutowireCapableBeanFactory.doCreateBean()
- <img width="951" alt="스크린샷 2022-10-10 오후 9 09 06" src="https://user-images.githubusercontent.com/62214428/194863253-01d3d2e1-57b1-4708-a36d-1ab6e004b7e9.png">
```
위에서 말했듯이

생성자 주입의 경우 createBeanInstance()의 리턴값으로 의존관계가 주입된 빈이 반환된다.
반면 그외 주입의 경우 createBeanInstance()의 리턴값으로 의존성이 주입되지 않은 빈이 반환된다.

따라서 이제 의존관계를 주입해줄 차례로 
아래 두 과정을 통해 진행된다.

populateBean()
initializeBean()
```
- <img width="710" alt="스크린샷 2022-10-10 오후 9 13 08" src="https://user-images.githubusercontent.com/62214428/194864137-a7012f79-07b8-4ca4-873b-990a1ad9b973.png">

### AbstractAutowireCapableBeanFactory.populateBean()
```
해당 매서드 중간에 
InstantiationAwareBeanPostProcessor.postProcessProperties()를 확인할 수 있는데

먼저!!! beanPostProcessor가 뭐랬나?
- 빈 생성 후 등록전 빈으로 등록할 객체를 조작할 수 있는데
- 생성자주입또한 이 beanPostProcessor를 통해 생성자, 파라미터 정보를 가져왔다.
- 필드,세터 주입또한 빈 등록전 어떤 행위를 추가하지 않을까????
```
```
InstantiationAwareBeanPostProcessor의 구현체인
AutowiredAnnotationBeanPostProcessor.postProcessProperties()를 살펴보자!!
```

### AutowiredAnnotationBeanPostProcessor.postProcessProperties()
- <img width="1035" alt="스크린샷 2022-10-10 오후 9 30 47" src="https://user-images.githubusercontent.com/62214428/194866862-9b18db0b-d47e-451f-ae26-3adc4e1db859.png">

```
앞에서도 말했듯이 필드,세터 주입의 경우 빈을 생성한 후 의존관계를 주입한다.
여기 findAutowiringMetadata()매서드를 보자

지금 빈은 생성된 상태고 앞으로 우리가 해야할일은? 바로 의존관계 주입
그러기 위해선 의존관계 주입을 위한 정보가 필요하고 findAutowiringMetadata() 매서드로부터 시작한다
```

```
메타데이터를 얻는 과정을 살펴보기위해 
AutowiredAnnotationBeanPostProcessor().findAutowiringMetadata() 중
AutowiredAnnotationBeanPostProcessor.buildAutowiringMetadata()를 호출하는걸 살펴보자
```

### AutowiredAnnotationBeanPostProcessor.buildAutowiringMetadata()
- <img width="984" alt="스크린샷 2022-10-10 오후 9 56 11" src="https://user-images.githubusercontent.com/62214428/194871416-728e747a-7814-4354-899c-89bd6c4fd032.png">
```
필드, 매서드 주입 부분을 확인할 수 있다!!!
```
```
그 중 공통으로 보이는 부분이 바로 findAutowiredAnnotation()
```

### AutowiredAnnotationBeanPostProcessor.findAutowiredAnnotation()
- <img width="984" alt="스크린샷 2022-10-10 오후 9 56 11" src="https://user-images.githubusercontent.com/62214428/194871601-b4bbec8a-1527-4885-a331-501c490612ba.png">
```
@Autowired 어노테이션 붙어있는지를 검사하는 부분이다.
```

--------
```
결국 위 과정을 통해 
필드,매서드 주입의 경우 
1. 빈을 먼저 생성
2. 해당 빈에 의존관계를 주입하기위해 AbstractAutowireCapableBeanFactory.populateBean()
- AutowiredAnnotationBeanPostProcessor.postProcessProperties()
- AutowiredAnnotationBeanPostProcessor().findAutowiringMetadata()
- AutowiredAnnotationBeanPostProcessor.buildAutowiringMetadata()
- 위 단계를 거쳐 의존관계 주입을 위한 정보를 수집

이제 inject 할 차례인데..
```
--------
### AutowiredAnnotationBeanPostProcessor.postProcessProperties()
```
다시 돌아와 inject를 살펴보자
```
- <img width="867" alt="스크린샷 2022-10-10 오후 10 04 34" src="https://user-images.githubusercontent.com/62214428/194873018-cd82335a-1cfb-4d92-96c2-9c75c8ce5c62.png">
- <img width="991" alt="스크린샷 2022-10-10 오후 10 06 29" src="https://user-images.githubusercontent.com/62214428/194873388-42d1a86a-7c60-4260-8157-9b47dd939b0c.png">
```
마찬가지로 리플렉션을 통해 접근 후 의존관계를 주입한다.
```


