# 상속보다는 컴포지션을 사용하자

**목차**

1. [상속의 문제점?](#상속의-문제점)
2. [컴포지션을 쓰자](#컴포지션을-쓰자)
3. [상속과 컴포지션](#상속과-컴포지션)
4. [정리하면](#정리하면)

상속보다 컴포지션을 사용하는 게 좋은 이유를 알아보자.

우선 상속과 컴포지션은 다음과 같다.

![image](https://user-images.githubusercontent.com/77563814/185729802-9a1529e8-ec07-4700-a673-6360d87ca4ed.png)



- 상속 : 부모 클래스를 자식클래스가 재사용+확장하는 것 (IS-A 관계)
- 컴포지션 : 기존 클래스가 새로운 클래스의 구성요소가 되는 것 (HAS-A 관계)

상속은 부모 클래스의 메소드를 재사용+확장할 수 있다. 컴포지션은 코드 재사용만 하고, 부모 메소드를 확장할 수는 없다.

상속 관계에서는 여러 문제가 발생하기도 한다.

## 상속의 문제점?

다음과 같은 상황이 있을 수 있다.

```java
public class Engine {
    protected int engineCapacity;

    public Engine(int engineCapacity) {
        this.engineCapacity = engineCapacity;
    }

    public startEngine() {
        // engineCapacity 사용해서 기능 처리
    }
}

public class Car extends Engine {
    ...

    public Car(int engineCapacity, ...) {
        super(engineCapacity);
        // 상위 Engine 클래스의 변수/메소드 사용해서 기능 처리
    }

}
```

이 경우 하위 클래스가 상위 클래스에 강하게 결합, 의존하게 된다. 캡슐화를 깨뜨린다(즉, 상위 클래스의 구현이 하위 클래스에 노출된다). 이 구조는 몇 가지 문제가 생길 수 있다.

- 만약 부모클래스에서 필드의 타입를 바꾸면, 자식 클래스들도 해당 필드 타입을 일일이 모두 수정해주어야 한다. 이런식으로 변경에 유연하지 못하다.
- 부모 클래스 메소드 사이의 로직을 모르고 자식 클래스에서 오버라이딩한다면, 자식 클래스의 메소드가 원하지 않게 잘못 동작할 수 있다. [참고 링크](https://happy-playboy.tistory.com/entry/Item-18-%EC%83%81%EC%86%8D%EB%B3%B4%EB%8B%A4%EB%8A%94-%EC%BB%B4%ED%8F%AC%EC%A7%80%EC%85%98%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)

이를 해결하기 위해 컴포지션을 사용하자.


## 컴포지션을 쓰자

<aside>

	🌟 기존 클래스를 private 인스턴스로 만들어서 새로운 클래스에서 참조하는 방식.
	    즉, 기존 클래스가 새로운 클래스의 구성요소로 쓰인다

</aside>

Car클래스에 Engine 클래스가 인스턴스로 들어가있다.

```java
public class Car {
    private Engine engine; // 컴포지션
}
```

Engine 클래스의 코드를 재사용하려면, engine 인스턴스의 메소드를 호출하는 방식으로 동작한다. 기존 클래스(Engine 클래스)의 코드가 바뀌더라도 새로운 클래스(Car)의 영향이 적다. 예를 들어, Engine 클래스의 어떤 필드 타입이 변경되더라도, Car 클래스에서는 코드를 바꿀 필요없다.

즉, 클래스 간 의존 관계가 없으니 상위 클래스의 코드가 바뀌어서 하위 클래스의 코드를 다시 변경해주어야 하는 방식이 아니다. 메소드 호출을 통해 사용하면 된다. 변경에 유연하고 안전하다.

이처럼 컴포지션은 메소드를 재사용은 할 수 있으나, 오버라이딩(확장)할 수는 없다. 따라서 확장 관련하여 발생하는 문제에서 자유롭다.

The composition also provides code reusability but the difference here is we do not extend the class for this. [참고 링크](https://www.geeksforgeeks.org/difference-between-inheritance-and-composition-in-java/?ref=gcse)


## 상속과 컴포지션

상속과 컴포지션을 적절하게 사용하면 다음과 같다.

![image](https://user-images.githubusercontent.com/77563814/185729831-cafd989e-c1b1-4651-bd80-d5701716d243.png)

- Engine 기능을 재사용하기 위해서 Car은 컴포지션을 사용했다.
- Tesla는 Car의 기능을 재사용 + 확장하기 위해서 상속 받았다.
- **is-a 와 has-a 관계 비교**
  - **"is-a"** relationship (재사용+확장)
    - a senior citizen is a citizen
    - a Manager is an Employee
  - “**has-a"** relationship (기능만 받겠다)
    - a car has an engine, a transmission, gas tank
    - a Manager has a PayrollID

## 정리하면

- 상속은 코드 재사용+확장할 수 있으나, 상속관계에서 문제가 발생할 수 있으니 확실한 is-a 관계일 때만 상속을 사용하자.
- 단순 기능을 재사용하고자 한다면(has-a 관계), 컴포지션을 사용하자. 컴포지션은 코드 재사용만 하고 확장할 수 없어 여러 문제에서 자유롭다.
  


**상속 관계는 필요할 때만 사용하자 → 인터페이스나 컴포지션을 쓰자**

> 개발 설계 단계에서 부모를 *추상클래스 혹은 인터페이스*로 만들지 둘 중 고민할 때는 인터페이스를 사용하자. 다중 상속이 가능하고 상속 관계의 문제가 없기 때문이다. 만약 이미 부모가 (추상)클래스라면 자식 클래스는 상속 관계보다는 컴포지션을 사용하자.
>
> 확실한 is-a 관계이고 **코드의 재사용+확장**이 필요한 경우일 때만 상속을 사용하자. 부모가 변경될 때 자식들도 다 변경이 되어야 하는 상황일 때 사용하자. 아니라면 인터페이스나 컴포지션을 사용하자❕

**Reference**

- [https://www.softwaretestinghelp.com/composition-in-java/](https://www.softwaretestinghelp.com/composition-in-java/)
- [https://tecoble.techcourse.co.kr/post/2020-05-18-inheritance-vs-composition/](https://tecoble.techcourse.co.kr/post/2020-05-18-inheritance-vs-composition/)
- [https://www.geeksforgeeks.org/difference-between-inheritance-and-composition-in-java/?ref=gcse](https://www.geeksforgeeks.org/difference-between-inheritance-and-composition-in-java/?ref=gcse)
- 책 <이펙티브 자바> 아이템 18 상속보다는 컴포지션을 사용하라
- [https://dev.to/romansery/why-you-should-favor-composition-over-inheritance-57m6](https://dev.to/romansery/why-you-should-favor-composition-over-inheritance-57m6)

[위로가기⬆](#상속보다는-컴포지션을-사용하자)
