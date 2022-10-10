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
