# 객체 지향 SOLID 5원칙

### 객체지향 SOLID 5원칙

**목차**

1. [SRP (single responsibility principle) 단일 책임 원칙](solid.md#srp-single-responsibility-principle)
2. [OCP (Open/closed principle) 개방 폐쇄 원칙](solid.md#ocp-openclosed-principle)
3. [LSP (Liskov substitution principle) 리스코프 치환 원칙](solid.md#lsp-liskov-substitution-principle)
4. [ISP (Interface segregation principle) 인터페이스 분리 원칙](solid.md#isp-interface-segregation-principle)
5. [DIP (Dependency inversion principle) 의존관계 역전 원칙](solid.md#dip-dependency-inversion-principle)

* [정리하면](solid.md#정리하면)

SOLID 5 원칙은 객체 지향적인 설계를 위한 규칙으로, 아래 문장을 규칙화하였다고 볼 수 있다❕

```
🌟 응집도는 높이고, 결합도는 낮추기
```

* **결합도** : 클래스 간의 상호 의존성 → 결합도가 낮아야 유지보수/재사용이 편리하다.
* **응집도** : 클래스 내부 요소들끼리의 관련성 → 응집도가 높으면 해당 기능/책임에 집중 가능하고 독립적이다. 따라서 유지보수/재사용이 편리하다.
* 쉽게 말하면, 클래스 내부 요소들끼리는 연관성이 크지만, 클래스끼리는 독립적이어야 한다. 이는 유지보수/재사용을 편리하게 한다.

프로그램의 유지보수/재사용을 편하게 하기위해 객체 지향적인 방법이 등장했다. 객체 지향적으로 개발하기 위해서는 `응집도는 높이고, 결합도는 낮추` 어야 한다. 이를 구체화한 규칙이 SOLID 5 원칙이다. 각각에 대해 정리해보았다.

### SRP (single responsibility principle)

> 단일 책임 원칙
>
> 역할/책임에 따라 클래스를 분리한다. 즉, 하나의 클래스는 하나의 역할/책임만 맡도록 한다.

추상화하는 단계에서 고민해볼 원칙이다. 클래스를 하나의 책임만 갖도록 분리하고 상속을 통해서 메소드를 각자 행위에 맞게 오버라이딩한다.

*   `BAD` 하나의 클래스 안에 여러 기능/역할이 있을 경우

    만약 메소드 내부에서 `if 분기문 후 instanceof` 이 있다면 SRP가 위배되었을 수 있다.

    ```java
    // 3가지 책임(역할)이 모두 있음
    public class Vehicle {
        public void printDetails() {}
        public double calculateValue() {}
        public void addVehicleToDB() {}
    }
    ```
*   `GOOD` 역할 별로 클래스를 분리하였다.

    ```java
    public class print{
        public void printDetails() {}
    }

    public class calculate {
        public double calculateValue() {}
    }

    public class addToDB {
        public double addVehicleToDB() {}
    }
    ```

### OCP (Open/closed principle)

> 개방 폐쇄 원칙
>
> (기능) 확장에는 열려있으나 (기존 코드) 변화에는 닫혀있어야 한다.

(클래스/메소드) 추가 확장 시에, 원래 클래스의 내부 코드는 바뀌지 않으면서, 해당 클래스를 상속받는 하위 클래스를 새롭게 만들어준다. 이를 통해 확장에는 열려있으나 변화에는 닫혀있을 수 있다. 대표적으로 IoC(제어의 역전), DI(의존성 주입)이 OCP를 따른다. 후에 나오는 DIP도 OCP와 연관있는 원칙이다.

*   `BAD` 아래 상황에서 새로운 Vehicle 종류인 Truck을 추가하려면, 이 클래스 내부의 코드를 수정해야 한다.

    ```java
    // Vehicle 내부 코드를 수정해주어야 함!
    public class Vehicle {
        public double calculateValue(Vehicle v) {
            if (v instanceof Car) {
                return v.getValue() * 0.8;
            if (v instanceof Bike) {
                return v.getValue() * 0.5;
    				// Truck을 추가하려면 새로운 분기문을 추가하는 등
    				// 기존 클래스의 코드를 변경해주어야 함
        }
    }
    ```
*   `GOOD` 아래의 경우, Vehicle에는 필요한 공통 기능만 남겨주고 Vehicle을 상속받는 하위클래스들을 구현하였다. Truck을 추가하려면 Vehicle을 상속받아 추가해주면 된다. 또한 이러한 확장 시 Vehicle의 내부 코드는 바뀌지 않는다. 이 예시 말고도 대표적으로 Spring의 IoC도 해당된다. 이는 DIP 원칙에서 예시로 정리해두었다!

    ```java
    // Vehicle의 내부 코드는 변경X
    public class Vehicle {
        public double calculateValue() {...}
    }

    // Vehicle를 상속받아 하위 클래스들 확장가능
    public class Car extends Vehicle {
        public double calculateValue() {
            return this.getValue() * 0.8;
    }
    public class Truck extends Vehicle{
        public double calculateValue() {
            return this.getValue() * 0.9;
    }
    ```

### LSP (Liskov substitution principle)

> 리스코프 치환 원칙
>
> sub type은 언제나 자신의 base type으로 교체할 수 있어야 한다. 즉, 하위 클래스의 인스턴스가 상위 클래스 타입으로 선언되었을 경우, 상위 클래스의 인스턴스 역할을 할 수 있어야 한다.

상속이 적절하게 이루어졌을 경우, LSP를 만족한다.

* 하위 클래스 _**is a kind of**_ 상위 클래스 - 하위 분류는 상위 분류의 한 종류이다.
* 구현 클래스 _**is able to**_ 인터페이스 - 구현 분류는 인터페이스할 수 있어야 한다.
* `GOOD` 하위 클래스 인스턴스가 상위 클래스 메소드를 수행할 수 있다.

```java
// 상위 클래스
public class Rectangle {
    private double height;
    private double width;
    public void setHeight(double h) { height = h; }
    public void setWidht(double w) { width = w; }
    ...
}

// 하위 클래스
public class Square extends Rectangle {
    public void setHeight(double h) {
	      super.setHeight(h); // 부모 메소드를 사용하는 데 문제 X
        super.setWidth(h);
    }
    public void setWidth(double w) {
        super.setHeight(w);
        super.setWidth(w);
    }
}
```

### ISP (Interface segregation principle)

> 인터페이스 분리 원칙
>
> 클라이언트는 자신이 사용하지 않는 메서드에 의존 관계를 맺으면 안 된다. 즉, 인터페이스는 자기 역할에 맞는 기능(메소드)으로만 최소한으로 구성되어야 한다. (인터페이스는 자식클래스에서 무조건 오버라이딩해야 하므로)

하나의 인터페이스는 범용 인터페이스보다 분리된 여러 인터페이스가 좋다. 인터페이스는 하위 클래스에서 모든 메소드를 필수 구현해야한다. 따라서 한 인터페이스 안에 많은 기능이 있기보다는 역할별로 분리되어 있어야 좋다.

*   `BAD` 한 인터페이스에 여러 기능

    ```java
    public interface BearKeeper {
        void washTheBear();
        void feedTheBear();
        void petTheBear();
    }
    ```
*   `GOOD` 클래스들이 상위 클래스/인터페이스를 상속받게 한다.

    ```java
    // 기능마다 인터페이스 여러개로 분리
    public interface BearCleaner {
        void washTheBear();
    }

    public interface BearFeeder {
        void feedTheBear();
    }

    public interface BearPetter {
        void petTheBear();
    }

    // 필요한 인터페이스 상속받아 필요한 기능 사용
    public class BearCarer implements BearCleaner, BearFeeder {

        public void washTheBear() {
            //I think we missed a spot...
        }

        public void feedTheBear() {
            //Tuna Tuesdays...
        }
    }
    ```

### DIP (Dependency inversion principle)

> 의존관계 역전 원칙
>
> 구체화에 의존하면 안되고 추상화에 의존해야 한다. 즉, 구체 클래스에 의존하지 말고, 상위 클래스/인터페이스에 의존해야 한다. 구현체에 의존하면 변경이 복잡해지기 때문이다.

* 추상체에 의존하고
* 실제 객체 주입시에 구현체를 주입

구체 클래스에 의존하면 변경이 어려워진다. 추상화에 의존할 경우, 확장과 변경이 쉽다. 새로운 하위클래스를 만들어 상속받게 할 수 있고, 실 구현체를 변경해주면 된다. 따라서 구체화가 아닌 추상화에 의존해야 한다. OCP와 연관되는 규칙이나, DIP가 더 구체적인 규칙이다.

*   `BAD` 구체 클래스를 의존하는 상황

    ```java
    // Engine 구체 클래스
    public class Engine {
       public void start() {...}
    }

    // 구체화(클래스)에 의존
    public class Car {
        private Engine engine;
        public Car(Engine e) {
            engine = e;
        }
        public void start() {
            engine.start();
        }
    }
    ```
*   `GOOD` 추상화(인터페이스)에 의존하는 상황 ([아래 예시](../java/undefined/prefer\_composition\_than\_inheritance.md)) 만약 엔진 종류를 추가하고 싶으면 Engine 인터페이스를 상속받는 새 엔진 클래스를 만들어주면 된다.

    ```java
    // Engine 인터페이스
    public interface Engine {
        public void start();
    }

    // 추상화(인터페이스)에 의존
    public class Car {
        private Engine engine;
        public Car(Engine e) {
            engine = e;
        }
        public void start() {
            engine.start();
        }
    }

    // Engine 인터페이스를 상속받은 클래스들
    // Car 사용시 필요한 클래스(PetrolEngine/DieselEngine)를 사용하면 된다.
    public class PetrolEngine implements Engine {
       public void start() {...}
    }
    public class DieselEngine implements Engine {
       public void start() {...}
    }

    // 의존성 주입 -> 여기서 엔진 종류 선택해서 주입
    Car myCar1 = new Car(new PetrolEngine);
    Car myCar2 = new Car(new DieselEngine);
    ```

### 정리하면

* 객체지향적인 설계를 하려면 `응집도는 높이고, 결합도는 낮추`어야 한다. 즉, 클래스 내부 요소들끼리는 연관성이 크지만, 클래스끼리는 독립적이어야 한다. 이러한 객제 치향적인 설계는 유지보수/재사용을 편리하게 한다.
* 객체지향 SOLID 5원칙은 `응집도는 높이고, 결합도는 낮추기`를 규칙화한 것이다.
* SRP (단일 책임 원칙)
  * 하나의 클래스에는 하나의 역할/책임만 주자. 필요시 클래스 분리하자
* OCP (개방 폐쇄 원칙)
  * 확장에는 Open, 변경에는 Closed되어 있어야 한다. 추상화를 통해
* LSP (리스코프 치환 원칙)
  * 하위클래스 인스턴스가 상위 클래스의 메소드를 사용할 수 있어야 한다. 기능상 상위클래스의 규약에 맞아야 한다.
* ISP (인터페이스 분리 원칙)
  * 인터페이스에는 해당 역할에 맞는 메소드로만 최소한으로 구성하자. 필요시 여러 인터페이스로 분리하자.
* DIP (의존관계 역전 원칙)
  * 구체화가 아닌 추상화에 의존하자.

**Reference**

* [https://www.baeldung.com/java-liskov-substitution-principle](https://www.baeldung.com/java-liskov-substitution-principle)
* 책 <스프링 입문을 위한 객제치향의 원리와 이해>
* [https://www.baeldung.com/solid-principles](https://www.baeldung.com/solid-principles)
* [https://www.educative.io/answers/what-are-the-solid-principles-in-java](https://www.educative.io/answers/what-are-the-solid-principles-in-java)

[위로가기⬆](solid.md#객체지향-solid-5원칙)
