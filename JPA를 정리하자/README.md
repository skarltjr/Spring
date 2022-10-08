- https://www.youtube.com/watch?v=Zwq2McbFOn4&list=PLwouWTPuIjUi9Sih9mEci4Rqhz1VqiQXX&index=1
- https://github.com/madvirus/jpa-basic

```
기초를 제대로

jpa 단독을 먼저 알아야 data jpa를 쓰는거다
- 첫 강의에서 느낀건 jpa 단독 사용시 아래처럼 설정파일이 필요한데 스프링부트가 이걸 다 해준다. 스프링부트의 편리성도 알수있다.
https://github.com/madvirus/jpa-basic/blob/main/jpa-01/src/main/resources/META-INF/persistence.xml
```
commit 시점
```
기본적으로 jpa의 영속성 컨텍스트는 트랜잭션이 시작할때 열리고 트랜잭션 종료 후 커밋 시점에 
쿼리가 발생하여 영속성 컨텍스트 내용이 db에 반영된다.

물론 auto_increment등 일부는 persist()호출 ㅎ commit()전 쿼리가 발생하기도 한다.
```

⭐️ n+1 오해
```
fetchType Eager : 즉시 로딩
fetchType Lazy : 지연 로딩

이는 결국 fetch 전략이다
ex) Member(n) : (1)Team이 존재하며 Team에서 양방향으로 Member를 참조한다고 해보자

그럼 모든 Team을 조회할 때 
fetchType이 Eager이면 한 team을 조회할때마다 추가로 해당 team과 연관된 모든 Member를 조회한다.
fetchType이 Lazy이면 한 team을 조회하고 사용될때 추가로 해당 team과 연관된 모든 Member를 조회한다.

결국 Lazy라고 N+1 문제가 발생하지 않는게 아니다! 
다만 Team을 조회하되 멤버를 사용할필요가 없는경우 Lazy는 추가적인 조회가 발생하지 않을 수 있다는 안정성이 있다.
즉 Lazy는 최악의 상황을 모면하기위한 설정이지 해결책 자체가 아니다.

FetchJoin도 만능인가?
절대 아니다.

예를들어 페이징의 경우 fetchJoin은 join으로 팀,멤버 싹 다 가져온 후 메모리에 올려놓고 
메모리에서 이를 페이징한다.
그러니까 데이터가 엄청많으면? 엄청난 자원 소모가 발생할 수 있다.

```
```
추가로 

Team이 n개의 멤버뿐만아니라 n개의 스폰서도 갖는 연관관계라고 해보자
즉 자식이 2개

fetch join은 여러 자식이 있을 때 
즉, 한번에 2개 이상의 자식 엔티티에는 join fetch을 사용불가 MultipleBagFetchException
이 문제 해결 대안으로 default_batch_fetch_size를 생각해볼 수 있는데
```

### default_batch_fetch_size
```
batch-size 옵션은 하위 엔티티를 로딩할때 한번에 상위 엔티티 ID를 지정한 숫자만큼 in Query로 로딩 
예를들어 batch-size:1000으로 되어있으면, 상위 엔티티인 Team의 id 1000개를 in Query로 member,sponsor를 조회

그럼 team 조회 쿼리 1방
member 조회 쿼리 1방
sponsor 조회 쿼리 1방
총 3방으로 끝낼 수 있다.
```
주의점 : https://toongri.tistory.com/70?category=1232233


### jpa 커스텀 매서드는 트랜잭션 처리가 되어있지 않다
```
jpaRepository를 상속받은 레퍼지토리 중 save()와 같은 매서드는 기본적으로 @Transaction 어노테이션이 적용되어있는 반면
findBy~field() 등 커스텀한 매서드는 적용되어있지 않다.
```
- https://velog.io/@max9106/JPA-Repository-커스텀-메서드-트랜잭션-처리-Transactional

### 양방향 연관관계의 주의점
```
다대일 양방향 연관관계시 일단 주인되는쪽에 fk가 생긴다.
그런데 주인이 아닌쪽에서 제어하게되는 경우 의도한 결과가 나오지 않는다.

사실 대부분의경우 단방향으로 충분하며 양방향은 결국 관리 포인트가 2개가되는것
만약 테이블 하나하나가 엄청 복잡하다면? 
```
- https://www.youtube.com/watch?v=brE0tYOV9jQ

### 다대다 연관관계의 문제점
- <img width="825" alt="스크린샷 2022-10-09 오전 12 00 14" src="https://user-images.githubusercontent.com/62214428/194713881-86b0a538-d2e9-464d-8b9e-126196a2044b.png">
```
다대다 연관관계의 경우 두 테이블의 pk를 fk로 갖는 중간테이블이 생성된다.
즉 파악하지못한 중간 테이블이 생성되는것인데

이 테이블에 대해 테이블 수정이 불가능하다. 
이 테이블로인한 쿼리를 예상하기 어렵다.
```
