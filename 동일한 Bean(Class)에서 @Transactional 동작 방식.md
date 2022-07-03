## 개요
```
동일한 빈(클래스)에서 @Transactional을 사용할 때 발생할 수 있는 문제점
- AOP의 특성을 고려하라
```

## 문제 상황
- <img width="407" alt="스크린샷 2022-07-03 오후 5 36 02" src="https://user-images.githubusercontent.com/62214428/177031926-735ae1fb-19d7-4f56-ad50-09971145e8e0.png">
- <img width="576" alt="스크린샷 2022-07-03 오후 5 34 07" src="https://user-images.githubusercontent.com/62214428/177031870-b4f34815-1c0b-43a3-b1ea-fcd904fdb308.png">
```
1.현재 컨트롤러에서 service.somethind()을 호출
2. something()은 동일 클래스에 존재하는 save()를 호출

위 사진에서처럼 service.save()에서 exception이 발생했을 때 모두 롤백이 될까?
```
- <img width="303" alt="스크린샷 2022-07-03 오후 5 37 19" src="https://user-images.githubusercontent.com/62214428/177031963-2e960c7f-fa20-48b2-ad98-0a98498f0ac1.png">
```
Input으로 10을 넘겼을 때 9에서 예외가 발생할 것
⭐️그런데 1~8까진 모두 rollback되지 않았다??!!!!!
```
