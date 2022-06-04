### Bean이란?
```
Spring에서 스프링이 제어권을 갖고 직접 만들며 관계를 부여하는 오브젝트
```

### Bean Factory란?
```
Spring에는 빈의 생성과 관계 설정을 수행하는 Ioc 컨테이너인 ! Bean Factory !가 존재한다.
Bean Factory는 클라이언트의 요청에 의해서 Bean 객체가 사용되는 시점(Lazy Loading)에 load한다
```

### Application context란
```
- Application context란 Bean Factory를 상속받아 확장한 Ioc컨테이너이다.
- 빈의 생성자체나 관계를 맺어주는 코드는 없고 생성 정보와 연관관계 정보에 대한 설정을 읽어 처리한다
- 대표적인 예시로 @Configuration과 같은 어노테이션이 대표적인 IoC설정 정보이다.


즉 내가 생각하는바로는 Bean Factory를 확장한 Application Context가 
빈 생성 및 직접적으로 연결관계를 맺어주는것은 Bean Factory의 기능을 활용하는데 이때 
어떻게!! 할 것인지에 대한 설정정보(ex configuraion)를 읽어 이에 맞게 위 Bean Factory기능을 활용하는 확장판이 Application context
```

### Bean의 요청 처리 과정
- 특정 빈을 요청하면 application context는 다음과 같이 동작한다
```
1.Application context는 @configuration이 붙은 클래스를 설정정보로 등록 + @Bean이 붙은 매서드의 이름으로 빈 생성

--- 요청
2. 특정 빈을 요청한다
3. Application context는 해당 이름의 빈이 존재하는지 찾는다(그렇기에 같은 이름의 빈 생성이 불가능한것 - 중복x)
4. 있다면 이를 반환하고 없다면 설정를 바탕으로 생성 후 반환
```
