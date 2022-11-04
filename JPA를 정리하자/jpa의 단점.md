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
jdbc template을 통해 보다 간편한 동작을 만들어낸다

jdbc template은 jdbc에서 driverManager가 하는 일들을 담당해준다.
디비 커넥션, 트랜잭션 관리, 예외처리등을 담당해준다.
```
```
⭐️ jdbc의 단점을 극복하고자 등장한 sql mapper가 바로 jdbc template

즉 
1. jdbc
2. jdbc의 단점 극복 => sql mapper (jdbc template)
3. sql mapper의 단점 극복 => orm (jpa)
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
