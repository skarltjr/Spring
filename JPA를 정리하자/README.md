- https://www.youtube.com/watch?v=Zwq2McbFOn4&list=PLwouWTPuIjUi9Sih9mEci4Rqhz1VqiQXX&index=1
- https://github.com/madvirus/jpa-basic

```
기초를 제대로

jpa 단독을 먼저 알아야 data jpa를 쓰는거다
- 첫 강의에서 느낀건 jpa 단독 사용시 아래처럼 설정파일이 필요한데 스프링부트가 이걸 다 해준다. 스프링부트의 편리성도 알수있다.
https://github.com/madvirus/jpa-basic/blob/main/jpa-01/src/main/resources/META-INF/persistence.xml
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
