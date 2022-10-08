# 🧀 스프링의 IoC와 생성자 주입해야하는 이유

> 스프링의 IoC와 DI(의존 관계 주입 방식)에 대해 알아보았다. 또한 DI 방식 중 생성자 주입을 사용해야하는 이유에 대해 정리해보았다.



### 제어의 역전(IoC)이란

IoC는 객체의 생성, 객체 생명 관리, 흐름 제어, 의존 관계 설정을 제 3자에게 위임하는 프로그래밍 방식이다. 프레임워크에서 쓰는 방법으로, 개발자는 필요한 부분을 구현해서 프레임워크에 끼워 넣는 식으로 개발한다. 프로그램은 개발자에 의해서 제어되는 것이 아니라 프레임워크의 내부에서 결정된 대로 이뤄지게 되는데, 이를 "**제어의 역전**"이라 한다.

아래는 개발자가 직접 객체를 생성하는 경우와 IoC가 적용된 경우이다. A 객체가 B 객체에 의존하는 상황이다.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption><p>IoC 적용 예시</p></figcaption></figure>

1. **개발자가 직접 객체를 생성하여 코드를 제어하는 경우 (IoC X)**

```java
// 구현체에 의존
// A객체는 B객체에게 의존한다
public class A {

    private B b;

    public A()
        b = new B(); // 구현체 변경 시 클래스 내부 코드를 변경해야한다.
	// b = new B`();
    }
}
```

**2. 컨테이너에 의해 생성된 객체를 주입받는 경우 (IoC O)**

```java
// 인터페이스에만 의존
class A {
    private B b;

    @Autowired // IoC 컨테이너에서 주입해준다.
    public A(B b) {
        this.b = b;
    }
}

// 클라이언트 코드 : DI, 의존관계를 외부에서 주입
new A(new BImpl1());
new A(new BImpl2());
```

A 객체가 B 객체에 의존하는 상황에서 IoC를 적용할 경우와 개발자가 직접 의존 객체 생성할 때를 비교해보았다.

**1.  개발자가 직접 객체를 생성하여 코드를 제어하는 경우 (IoC X)**

우선 A 객체 내부에서 B 객체를 생성할 경우, A가 B 구현체에 의존한다고 볼 수 있다. B 객체에서 B\` 객체로 **변경하려면 A 클래스 내부 코드가 변경되어야 한다.**

**2.  컨테이너에 의해 생성된 객체를 주입받는 경우 (IoC O)**

반면, A 객체가 B 인터페이스에 의존할 경우, A가 B 추상체에 의존한다고 볼 수 있다. 이 경우 외부에서 B 구현체를 결정해서 주입한다. **따라서 BImpl1에서 BImpl2로 변경되어도 A 클래스 내부 코드가 변경되지는 않는다. 이때 스프링에서 @Autowired 어노테이션이 붙으면, 해당하는 객체를 자동 주입해준다.**

****

### IoC 사용하는 이유

IoC를 사용하면, 객체 A가 직접 B 객체를 생성하는 게 아니라, 외부(IoC 컨테이너)에서 객체를 생성하고 관계 설정해준다. 클래스 내부의 응집도는 높이고 클래스들 간 결합도는 낮추어 변경에 유연한 구조를 가지게 된다.&#x20;

* 객체 A는 B의 구현체가 아니라 추상체에 의존하여, 기능 확장에 열려있고 코드 변경이 필요없다. 즉, OCP를 지킨다.&#x20;
* 또한 객체 생성/관리하는 부분과 개발자가 구현하는 부분을 나눈다. 객체 관리는 컨테이너에게 맡기고, 개발자는 자신이 개발하는 부분에만 집중한다.



#### **Spring에서의 IoC, DI 작동 방식 -** @Autowired

스프링 서버가 올라갈 때

1. 애플리케이션 컨텍스트(빈 팩토리, IoC 컨테이너)가 @Bean, @Service, @Controller 등 어노테이션을 이용하여 등록한 스프링 빈을 생성한다.
2. 생성자, 수정자에 @Autowired 어노테이션이 붙으면 해당하는 객체를 컨테이너에서 주입해준다.



### **DI 의존관계 주입** 방식

DI 방식에는 생성자 삽입, 수정자 삽입, 필드 주입이 있다.

#### 1. **필드 주입 (Field Injection)**

```java
@Service
public class AImpl implements A {

    @Autowired
    private B b;

    @Override
    public void bmethod() {
        b.method();
    }
}
```

#### 2. 수정자 (**setter) 주입 (Setter Injection)**

```java
@Service
public class AImpl implements A {

    private B b;

    @Autowired
    public void setB(B b) {
        this.b = b;
    }

    @Override
    public void bmethod() {
        b.method();
    }
}
```

그러나 위의 2가지 방법에서는 구현체(`BImpl`)가 주입되지 않아도 `객체 A`가 생성될 수 있다. 이때, 구현체(`BImpl`)의 메소드를 실행하면 `NullPointerException`가 발생한다.

이 코드에서는 B가 주입되지 않아도 A 객체가 생성될 수 있다. `bmethod()`의 경우, B를 참조하여 B의 메소드가 실행된다. B가 주입되지 않았는데 객체 A에서 해당 메소드 실행 시 `NPE`가 발생한다.

따라서 A가 B에 의존하는 경우, **B가 반드시 주입되어야 A 객체가 생성되는 생성자 주입**을 사용하는 게 안전하다.

#### 3. **생성자 주입 (Constructor Injection)**&#x20;

```java
@Service
public class AImpl implements A {

    private final B b;

    @Autowired
    public AImpl(B b) {
        this.b = b;
    }

    @Override
    public void bmethod() {
        b.method();
    }
}

// 클라이언트 코드 : 구현체를 주입받아야 객체가 생성된다.
new A(new BImpl1());
```

생성자 주입의 경우, `AImpl(B b)`처럼 반드시 A가 B 객체를 주입받아야 생성된다.



### 생성자 주입을 사용해야 하는 이유

생성자를 통해서 구현체를 주입받을 경우, 반드시 구현체(`BIml1`)를 주입받아야 객체 A가 생성된다. 따라서 <mark style="background-color:yellow;">객체 A는 객체 B가 항상 있다. 필드 주입이나 수정자 주입처럼 B 객체가 없어</mark> <mark style="background-color:yellow;"></mark><mark style="background-color:yellow;">`bmethod()`</mark> <mark style="background-color:yellow;"></mark><mark style="background-color:yellow;">실행시 NPE가 발생하는 일이 없다</mark>. 또한 A와 B의 의존관계를 클라이언트 코드에 노출시켜 <mark style="background-color:yellow;">컴파일 타임에 에러</mark>를 잡을 수 있다.





**Reference**

*   생성자 주입을 해야하는 이유

    [https://yaboong.github.io/spring/2019/08/29/why-field-injection-is-bad/](https://yaboong.github.io/spring/2019/08/29/why-field-injection-is-bad/)





