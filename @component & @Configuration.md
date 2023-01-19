## @component & @Configuration의 차이
```
두 어노테이션 모두 빈을 등록하기위한 어노테이션이다

@component : 개발자가 직접 작성한 class를 Bean으로 등록 할 수 있게 만들어 준다
@bean : 개발자가 직접 제어가 불가능한 외부 라이브러리를 빈으로 등록할 때 사용한다
```

### @Bean + @Configuration
```
@bean : 개발자가 직접 제어가 불가능한 외부 라이브러리를 빈으로 등록할 때 사용한다
- 1개 이상의 @Bean을 제공하는 클래스의 경우 반드시 @Configuration을 명시해 주어야 함
예시를 통해 살펴보자

passwordEncoder와 같은 외부라이브러리를 사용한다고해보자
우리는 해당 라이브러리의 코드를 직접 수정할 수 없고 이러한것들을 빈으로 등록하여 사용하는경우가 많다.
이 때 우리는 @Bean 어노테이션을 통해 빈으로 등록하며 @Configuration 어노테이션을 통해 
스프링에게 아래 클래스가 bean 팩토리를 위한 오브젝트 설정을 담당하는 class임을 알린다.

@Configuration
public class AppConfig {
    @Bean
    public ModelMapper modelMapper() {
        ModelMapper modelMapper = new ModelMapper();
        modelMapper.getConfiguration()
                .setDestinationNameTokenizer(NameTokenizers.UNDERSCORE)
                .setSourceNameTokenizer(NameTokenizers.UNDERSCORE);
        return modelMapper;
    }
    @Bean
    public PasswordEncoder passwordEncoder() {
        return PasswordEncoderFactories.createDelegatingPasswordEncoder();
    }
}
```

### @Component와 @Configuration의 차이
#### 무분별한 @Component 사용을 주의하자
```
@Configuration의 매서드는 싱글톤을 보장한다!!!!

그러나. @Component의 매서드는 싱글톤을 보장하지 못한다고한다.
```
#### 1. @Component를 활용하는 경우
- <img width="344" alt="스크린샷 2022-06-11 오후 6 25 12" src="https://user-images.githubusercontent.com/62214428/173182036-4dc67fba-4ca6-4a72-964e-401b3972a9ad.png">
- <img width="496" alt="스크린샷 2022-06-11 오후 6 25 15" src="https://user-images.githubusercontent.com/62214428/173182037-7b81dc67-23e5-4315-95f7-ac42dc437a23.png">
- <img width="534" alt="스크린샷 2022-06-11 오후 6 35 44" src="https://user-images.githubusercontent.com/62214428/173182407-5ebde0d7-4cc4-4393-89bc-dc6e95561939.png">

```
그래서 @Component의 매서드를 직접 호출하는경우 싱글톤 보장이 안되기때문에 매 번 다른 빈을 생성하고
이제는 이를 막고자 컴파일에러가 발생한다.
```
#### 2. @Configuration을 활용하는 경우
- <img width="383" alt="스크린샷 2022-06-11 오후 6 26 35" src="https://user-images.githubusercontent.com/62214428/173182088-78188fb2-c89e-4aa8-8987-7cc10f732c6d.png">
- <img width="484" alt="스크린샷 2022-06-11 오후 6 26 40" src="https://user-images.githubusercontent.com/62214428/173182089-6099aeef-18e4-4b8d-b35d-da0dd5d63d78.png">
- <img width="558" alt="스크린샷 2022-06-11 오후 6 35 47" src="https://user-images.githubusercontent.com/62214428/173182413-c6b9f3be-0e98-482c-9772-8ce0790e2eda.png">

### 따라서 
```
싱글톤을 의도했지만 @Component의 남용으로 인한 문제를 방지하기위해선
결과적으로 한 번 생성된 객체를 활용해야한다.
그리고 이 방법이 바로 의존관계 주입-생성자 주입이다.
```

- <img width="515" alt="스크린샷 2022-06-11 오후 6 36 46" src="https://user-images.githubusercontent.com/62214428/173182425-53afe9ea-21ce-46d1-a209-cce9edbb1771.png">
```
바로 위의 simpleBeanConsumer() 매서드에서 이번에는 최초생성된 SimpleBean을 활용할 수 있도록
의존관계 주입을 받아 사용하고 정상상태 확인
```

### 그런데 왜 @Component를 사용해?
```
@Component @Configuration 차이는 빈 싱글톤 보장차이가 맞는데 @Component를 사용하는 이유는 스프링의 스코프가 싱글톤말고 prototype도 존재
```
