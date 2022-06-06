### IOC
```
inversion of control
제어의 역전이다.

public class A {

    private B b;

    public A() {
        b = new B_1();
    }

    public void useB() {
        b.method_1();
        b.method_2();
    }

}
-----
interface B {
  void method_1();
  void method_2();
}
B_1도 있고 B_2도 있다
class B_1 implements B{

    @Override
    public void method_1() {
        System.out.println("B_1`s method_1");
    }
    @Override
    public void method_2() {
        System.out.println("B_1`s method_2");
    }

}

class B_2 implements B{
    @Override
    public void method_1() {
        System.out.println("B_2`s method_1");
    }
    @Override
    public void method_2() {
        System.out.println("B_2`s method_2");
    }

}


이 코드의 문제점은 무엇일까?
바로 클래스 A가 자신이 사용할 b를 관리해야한다는 점이다.
즉 A는 B에 의존적인 형태다.
만약 A가 사용하고자하는 B를 외부에서 관리해준다면?
A는 B관리에 대한 책임이 사라지며 자신의 책임에 집중할 수 있다.

아래처럼 만약 어떠한 B구현체를 사용할지에 대한 책임을 담당하는 Factory가 따로 존재한다면
A는 Factory가 정해준 B 구현체를 사용할뿐 관리에 대한 책임을 덜어낼 수 있다.
public class Factory {
    public A a() {
        return new A();
    }

    public B b() {
        return new B_1();
        // return new B_2();
    }
}

public class A {

    private B b;

    public A() {
        b = new Factory().b();
    }
    ..이하 동일..
}


⭐️ 결과적으로 IOC는 의존관계에 대한 책임을 제 3자에게 위임하는것이다.
장점 : 단일 책임 원칙 고수 가능
```

## DI
```
dependency injection
의존관계 주입

⭐️어떤 객체가 사용하는 의존 객체를 직접 만들어 사용하는게 아니라, 외부로부터 주입받아 사용하는 방법이자 IOC를 실현시키는 도구

public class A {

    private B b;

    public A(B b) {
        this.b = b;
    }
}


DI의 장점은 위 코드로 알아볼 수 있다.
느슨한 결합 : 어떠한 구체적인 B구현체에 의존하지 않고 인터페이스에 의존함으로써 결합도가 낮아진다
- 만약 구체적인 B구현체에 의존했다면 A는 구현체의 변동에 영향을 받을 것

```

## 스프링의 IOC DI가 특별한 이유는 무엇일까?
```
내 생각으론 지금 코드를 봤을 때 
ioc의 경우 책임을 위임할 제 3자 구현이 추가적으로 필요했다.
그러나 스프링은 IOC컨테이너가 존재하여 이를 담당하고 그만큼 관리 포인트를 줄여준다고 생각.

그리고 Bean을 활용하여 어떤 객체를 주입해줄지(DI)까지의 과정마저 간소화시켜준다고 생각
```
