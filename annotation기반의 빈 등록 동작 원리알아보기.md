## annotation기반의 빈 등록 동작 원리알아보기

- AnnotationConfigApplicationContext 클래스
- <img width="788" alt="스크린샷 2022-09-29 오전 12 18 52" src="https://user-images.githubusercontent.com/62214428/192825823-94cd569a-85bd-4808-b790-f1d11a22653e.png">


### 1. AnnotationConfigApplicationContext.register
- 무엇을 빈으로 등록할지, 등록을 시작하는 매서드
- <img width="1010" alt="스크린샷 2022-06-04 오후 7 34 42" src="https://user-images.githubusercontent.com/62214428/171995547-d02048ed-da09-4528-9c29-dde5df0f18ac.png">

### 2. AnnotatedBeanDefinitionReader.register & registerBean
- 여기서부터 느낀건 굉장히 작은 단위로 매서드르 분리했다고 생각
- <img width="1074" alt="스크린샷 2022-06-04 오후 7 41 19" src="https://user-images.githubusercontent.com/62214428/171995652-83be27e7-e41f-4caa-98d7-242dea1df8e4.png">

### 3. AnnotatedBeanDefinitionReader.doRegisterBean
- 실질적으로 빈 등록을 시작하는 매서드
```
1. AnnotatedGenericBeanDefinition : BeanDefinition의 구현체, Bean의 속성, 생성자 등의 정보를 갖는다.
예를들어 아래같은 빈 config를 통해 등록될 빈의 정보를 정의하는 클래스
@Configuration
public class BeanConfig {

    @Bean
    public AccountService accountService() {
        AccountService accountService = new AccountService();
        accountService.setAccountRepository(accountRepository());
        return accountService;
    }
}

2. ScopeMetadata : Bean Scope의 정보
3. BeanDefinitionHolder : BeanDefinition와 name, alias 정보를 추가적으로 갖는다. 
4. BeanDefinitionReaderUtils : Bean을 IoC Container 즉, ApplicationContext에 저장하는 역할을 하는 utils 클래스 이다.
```
- <img width="935" alt="스크린샷 2022-06-04 오후 7 42 52" src="https://user-images.githubusercontent.com/62214428/171995694-998a48b5-2c5f-4e2f-804e-d42a8092d554.png">
- 결과적으로 여기선 빈 등록을 위한 정보를 구성했고 맨 마지막줄에서 빈 등록을 위한 매서드를 호출한다.
- 그런데 맨 마지막 줄 BeanDefinitionReaderUtils.registerBeanDefinition(definitionHolder,this.registry)를 보자
- 즉 빈 등록 정보(definition)을 구성했고 registerBeanDefinition()을 호출해서 등록하겠다는건데 this.registry를 넘겨준다. 아마 이 registry가 실질적으로 등록을할텐데
- 이게 도대체 누구냐? : this.registry를 찾기위해 현재 AnnotatedBeanDefinitionReader클래스의 생성자를 봐보자
- <img width="804" alt="스크린샷 2022-09-29 오전 1 00 57" src="https://user-images.githubusercontent.com/62214428/192828773-fc7dff4a-5b09-4fae-9896-888adcddef32.png">
- 외부에서 beandefinitionRegistry를 받아오는데
- 이를 확인하기위해 AnnotationConfigApplicationContext 생성자까지 다시 올라가보면
- <img width="792" alt="스크린샷 2022-09-29 오전 1 02 35" src="https://user-images.githubusercontent.com/62214428/192829146-0bd97237-d9cf-4a64-bb38-04cec3569ced.png">
- AnnotationConfigApplicationContext은 스스로를 생성할때 AnnotatedBeanDefinitionReader를 생성하기위해 자기자신!!!을 넘긴다.
- 결국에 ⭐️BeandefinitionRegistry가 AnnotationConfigApplicationContext이었다.
- 그럼 이 그림을 다시 봐보자
- <img width="788" alt="스크린샷 2022-09-29 오전 12 18 52" src="https://user-images.githubusercontent.com/62214428/192831169-d642e81a-52ab-436f-abb3-a95e4b612b3d.png">
```
바로 위위 AnnotationConfigApplicationContext 사진과 함께 바로 위 사진을 봐보면
결국 AnnotationConfigApplicationContext은 자기 자신을 생성하는 생성자에서 AnnotatedBeanDefinitionReader를 생성하는데
AnnotatedBeanDefinitionReader는 생성자의 파라미터로 BeandefinitionRegistry를 필요한데 
AnnotationConfigApplicationContext가 사실은 BeandefinitionRegistry을 구현한 GenericApplicationContext을 상속받은 BeandefinitionRegistry고
그래서 자기자신을 넘겨준다
```

### 4. BeanDefinitionReaderUtils.registerBeanDefinition
- 그래서 위에서 전달받은 registry로 진짜 빈을 등록하는데..
- <img width="689" alt="스크린샷 2022-06-04 오후 7 51 04" src="https://user-images.githubusercontent.com/62214428/171995924-eb7a69ed-99ae-4c54-afc8-a9624e2f9ed7.png">
- 지금까지 봐온결고 registry는 AnnotationConfigApplicationContext 자기자신이었고 
- AnnotationConfigApplicationContext은 beanDefinitionRegistry를 구현한 GenericApplicationContext을 상속받았다
- 즉 진짜 마지막 등록을 위해 GenericApplicationContext을 살펴보면

### 5. GenericApplicationContext.registerBean
- <img width="745" alt="스크린샷 2022-09-29 오전 1 24 28" src="https://user-images.githubusercontent.com/62214428/192834026-36d98984-bebd-4b2c-8675-3e74f4def5ac.png">
- <img width="714" alt="스크린샷 2022-09-29 오전 1 25 09" src="https://user-images.githubusercontent.com/62214428/192834227-7c07648c-bdde-435d-822c-df6546dfb712.png">

```
스프링에서 빈을 생성하는놈이 누구냐?
빈 팩토리다. 그게 저 빈 팩토리다
```
- GenericApplicationContext 생성자를 봐보면
- <img width="1054" alt="스크린샷 2022-09-29 오전 1 26 49" src="https://user-images.githubusercontent.com/62214428/192834492-4e7bdbe2-9ef6-4675-9dc0-efb9e4062340.png">



### 5. DefaultListableBeanFactory.registerBeanDefinition
- 내가 생각했을 때 application context는 설정정보에 기반하여 빈을 등록 및 제어하는데
- application context는 beanFactory의 확장으로 설정정보읽어 이를 바탕으로!! / 실질적으로 등록코드 자체는 beanFactory라고 생각했고 이를 확인할 수 있었다.
- <img width="861" alt="스크린샷 2022-06-04 오후 8 00 04" src="https://user-images.githubusercontent.com/62214428/171996159-6ae72363-8dee-40c8-8a88-b50b17bee29d.png">
- 중요한건
- <img width="908" alt="스크린샷 2022-09-29 오전 1 30 02" src="https://user-images.githubusercontent.com/62214428/192835561-f32b23d3-b80e-442e-a533-88e615590c0f.png">
```
Map 형태로 빈을 관리하는데 그 타입이 <String,BeanDefinition>이다
즉 진짜 빈을 생성해서 맵에 저장하는게 아니라 여전히 빈 정보를 맵에 저장 왜?
이는 스코프와 관련있다고 한다.
```
### 느낀점
```
일단 너무 잘 쪼개진 매서드들과 각 인터페이스,클래스들을 아름답게 조립했다고 느꼈다.
특히 자신의 책임을 명확히하고 책임이 아닌 행위는 다른 클래스의 도움을 받는다.

1. 실제 Map에 빈을 등록하여 생성하는 DefaultListableBeanFactory는 BeanFactory 인터페이스를 구현하고

2. AnnotatedBeanDefinitionReader는 등록할 빈의 정보를 정의한다.
    - 그리고 AnnotatedBeanDefinitionReader는 정리한 빈의 정보를 토대로 빈을 등록하기 위해 registry를 호출하여 도움을 받는데
    - 빈 생성은 자신의 책임이 아니니 registry를 호출
    - 이 registry는 바로 AnnotationConfigApplicationContext인데
    - AnnotationConfigApplicationContext은 GenericApplicationContext를 상속받는다.
    
3. 즉 GenericApplicationContext가 BeanDefinitionRegistry를 구현한 구현체고 이 친구가 registry 역할을 수행한다
    - 이때 GenericApplicationContext는 정의된 빈 정보를 갖고 빈을 등록하는데
    - 빈 생성 자체는 자신의 역할이 아니니 beanFactory를 호출한다
    
4. 그리고 이때 1번에서 봤듯이 BeanFactory를 구현한 DefaultListableBeanFactory가 호출되고
    - Map에 빈 정보를 등록하는 빈 생성을 수행한다.
  
```
