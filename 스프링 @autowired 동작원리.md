### @Autowired
```
도대체 어떻게 의존관계가 자동으로 주입되는가.
DI가 어떻게 작동하는건지 알아보자

@Autowired는 의존 객체의 타입에 해당하는 빈을 찾아 주입해주는데...
```

### 1. DefaultListableBeanFactory.preInstantiateSingletons()
```
DefaultListableBeanFactory.preInstantiateSingletons()매서드에서 빈의 이름을 토대로 빈을 생성한다
```
- <img width="885" alt="스크린샷 2022-10-02 오후 12 22 12" src="https://user-images.githubusercontent.com/62214428/193436468-7efc7067-c989-4927-9b27-228be444a7bc.png">
```
먼저 실제로 내가 등록하고자한 빈들의 이름을 확인할 수 있었고
이러한 빈 이름들이 들어있는 beanNames를 반복 돌면서 하나씩 생성한다
```
- <img width="1573" alt="스크린샷 2022-10-02 오후 12 25 28" src="https://user-images.githubusercontent.com/62214428/193436522-9aa59eed-c8b8-4dbe-8db1-ce13d04ab5df.png">
```

```
