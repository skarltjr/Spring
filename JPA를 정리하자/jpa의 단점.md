```
jpa를 사용하면 비즈니스 로직이 복잡해집니다!

이게 무슨 의미인지 와닿지 않는다.
```

### jdbc 
```
기술이 발전해온 순서대로 이해해보자
```

```
jdbc란 
java database connectivity
즉 db에 접근할 수 있도록 자바에서 제공하는 api다
기존에는 db마다 접근방식이 다 달랐지만!!
아래 그림과 같이 우리는 api를 활용하면 
jdbc의 driveManager가 데이터베이스 제품에 따른 드라이버를 통해 
어떤 디비든지 알맞은, 동일 동작을 가능하게 해준다.
```
- <img width="515" alt="스크린샷 2022-11-04 오후 3 49 07" src="https://user-images.githubusercontent.com/62214428/199909534-12599842-b0be-40d8-a36f-597a5b3e42b8.png">
```
⭐️ 그런데 여기에는 아주 큰 단점이 있다.
- 디비 연결
- 쿼리문
- 결과물 획득 등
모든 행동에 대해 방대한 코드가 필요하다.
또한 디비 커넥션 관리 및 예외처리등에 불편함이 있고
스프링에서 이를 해결하고자 나온게 spring jdbc
```

### spring jdbc / sql mapper
```
⭐️ jdbc의 단점을 극복하고자 등장한 sql mapper가 바로 jdbc template

jdbc template은 jdbc에서 driverManager가 하는 일들을 담당해준다.
디비 커넥션, 트랜잭션 관리, 예외처리등을 담당해준다.
```
```
중요한것은 sql mapper의 정의
sql의 결과를 객체의 필드!!!에 매핑하는것이 sql mapper
```
- <img width="606" alt="스크린샷 2022-11-04 오후 4 05 31" src="https://user-images.githubusercontent.com/62214428/199912489-bbae797e-3459-4faf-b6a5-931440c18131.png">
```
객체와 관계를 매핑하는것이 orm과는 다르다
예시로 jpa의 특장점 중 하나는 바로 우리의 객체를 기반으로 테이블 및 관계를 만들어주는등

반면 sql mapper는 말 그대로 sql문의 결과를 객체의 필드에 매핑
```
---------

```
그런데..

특정 데이터베이스의 종속되지 않고자하는것이 jdbc의 목표였지만
jdbc template은 결국 직접적인 sql문을 사용하고 이는 특정 데이터베이스에 종속될 수 밖에 없다.
또한
이러한 sql문으로 중복 코드가 발생하며
테이블 필드 변경시 관련된 모든 sql등의 수정이 발생한다.

그리고 sql의 경우 문자열로 컴파일시점에 오타 발생 등 파악이 어려운 유지보수 어려움이 존재
```
```
그러다보니 발생하는 가장 큰 문제점은 바로 
객체지향이 아니라 관계형 db 테이블에 맞춰가는 프로그래밍 형태가 발생
즉 데이터베이스에 의존적인 설계가 진행된다.
```


### jpa / orm
```
객체 중심의 설계를 도모하면서 데이터베이스의 접근

- 직접적인 sql문 없이 자바 코드를 사용해서 db에 접근 및 조작
- 내부적으로 jdbc를 사용
```
```
장점 : 
- 반복적인 crud 쿼리를 하나의 매서드로 줄일 수 있다.
- 자바 코드를 통한 db 접근 및 조작
- 객체 중심의 설계가 가능하다
  - 예를들어 테이블의 관계는 외래키를 통해 표현
  - 반면 객체는 참조를 사용
  - 만약 아래처럼 테이블 중심의 설계의 경우 crew.getTeam();과 같은 객체 중심의 코드가 사용이 가능할까? 
  - 그렇기 때문에 객체 중심의 설계가 가능하다는것은 큰 장점이다.
  
class Crew{
  Long id;
  Long teamId;
}

class Team{
  Long id;
}
```
```
하지만 단점도 분명히 존재한다.

단점 :
- 자바 코드. 즉 매서드로 모든 복잡한 쿼리문을 절대 커버할 수 없다.
- 오히려 sql이 훨씬 이해,구현하기 쉬울 수 있는 경우가 발생

```



```
즉 
1. jdbc
2. jdbc의 단점 극복 => sql mapper (jdbc template)
3. sql mapper의 단점 극복 => orm (jpa)
```
