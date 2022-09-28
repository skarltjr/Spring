## annotation기반의 빈 등록 동작 원리알아보기

- AnnotationConfigApplicationContext 클래스

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
2. ScopeMetadata : Bean Scope의 정보
3. BeanDefinitionHolder : BeanDefinition와 name, alias 정보를 추가적으로 갖는다. 
4. BeanDefinitionReaderUtils : Bean을 IoC Container 즉, ApplicationContext에 저장하는 역할을 하는 utils 클래스 이다.
```
- <img width="935" alt="스크린샷 2022-06-04 오후 7 42 52" src="https://user-images.githubusercontent.com/62214428/171995694-998a48b5-2c5f-4e2f-804e-d42a8092d554.png">

### 4. BeanDefinitionReaderUtils.registerBeanDefinition
- 진짜 빈을 등록하는 로직
- 그런데 여기서 진짜 빈을 등록하는건 registry인데?? 누굴까?
- <img width="689" alt="스크린샷 2022-06-04 오후 7 51 04" src="https://user-images.githubusercontent.com/62214428/171995924-eb7a69ed-99ae-4c54-afc8-a9624e2f9ed7.png">
- 바로 아래의 DefaultListableBeanFactory
### 5. DefaultListableBeanFactory.registerBeanDefinition
- 내가 생각했을 때 application context는 설정정보에 기반하여 빈을 등록 및 제어하는데
- application context는 beanFactory의 확장으로 설정정보읽어 이를 바탕으로!! / 실질적으로 등록코드 자체는 beanFactory라고 생각했고 이를 확인할 수 있었다.
- <img width="861" alt="스크린샷 2022-06-04 오후 8 00 04" src="https://user-images.githubusercontent.com/62214428/171996159-6ae72363-8dee-40c8-8a88-b50b17bee29d.png">

```
여기까지 살펴본 것

application context는 bean factory의 확장으로 설정정보를 바탕으로 빈 생성 및 관계 설정등의 제어를 담당.
이 때 bean factory의 확장이기에 실질적인 빈 등록의 과정 코드는 bean factory 구현체를 활용
```
