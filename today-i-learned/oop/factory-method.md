# Factory Method 패턴 자세히

> Factory Method 패턴은 객체의 생성 방식과 관련된 디자인 패턴이다. 객체를 사용하는 클래스와 생성하는 클래스를 분리한다. 이를 통해 새로운 구현 클래스 추 시, 기존 프로그램 구조가 변경되지 않는다. 즉, 기존 코드는 바뀌지 않으면서 기능 확장에는 열려있도록 하는 패턴이다(SOLID OCP 원칙을 지킨다).



디자인 패턴 중에 Factory Method 패턴이 가장 이해하는데 오래 걸렸던 것 같다. 객체 생성을 위임하는 클래스를 따로 만드는 이유랑 예제가 안 와닿았던 것 같아 아래처럼 이전 방식(Factory 사용X, Simple Factory 방식)들도 정리해보았다.&#x20;

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p>Factory Method 패턴을 사용하는 이유를 알아보기 위해 Fac 없는 방식과 Simple Fac 방도 함께 정</p></figcaption></figure>

위처럼 Factory Method Pattern을 알기 위해 우선 Factory 없이 객체 생성하는 방식과 Simple Factory 방식도 함께 정리하였다.

중요한 건 **객체 생성을 담당하는** **Factory 클래스**를 따로 만든다는 점이다. 이로 인해 확장에 열려 있으면서, 기존 코드를 변경하지 않게 된다❕ 아래에서 각각을 자세히 정리해보았다.

### 1. Factory 없이 객체 생성 시



```
💡 객체 생성 시 클라이언트단에서 구체화에 의존하는 방법
   (Factory 사용X, 객체 사용부와 생성부가 분리되지 않음)
```



<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption><p>No Factory</p></figcaption></figure>

* **Product 클래스 → 인스턴스 생성하는데 사용함**

```java
public interface Toy {
		void playToy();
}

public class TeddyBear implements Toy { // toy 인스턴스 생성 시, 이 구체클래스로 직접 생성
		@Override
    public void playToy() {
        System.out.println("인형 눈알 붙이기");
    }
}

public class MiniCar implements Toy {
		@Override
    public void playToy() {
        System.out.println("미니카 달리기");
    }
}
```

* **Client 클래스 (Product 사용하는 객체)**

```java
public class ToyStore {
		public Toy orderToy(String toyType) { // toy 추가/변경 시, if문 코드도 변경된다.
				Toy toy;
				if (toyType.equals("TeddyBear")) {
					toy = new TeddyBear();
				elif (toyType.equals("MiniCar")) {
					toy = new MiniCar();
				}
    }
}
```

* **클라이언트 코드**

```java
// 생성한 Toy 인스턴스 사용
ToyStore toyStore = new ToyStore();
toyStore.orderToy(new TeddyBear());
```

`Toy` 객체 생성 시, `Factory 클래스` 없이 `Toy 구현 클래스`를 통해 직접 생성한 경우이다. 이때 만약 새로운 `Toy` 종류를 추가하려면, `ToyStore` 클래스 코드 내부를 변경해주어야 한다.

이를 보완하기 위해 객체의 생성을 분리하여 담당하는 `Factory 클래스`를 사용한다. 우선 Simple Factory 방식 또한 OCP를 지키지 못하고 디자인 패턴이라 분류하지는 않지만, Factory Method 패턴을 이해하기 위한 전단계이므로 정리해보았다.





### 2. Simple Factory

```
💡 객체 생성용 서브 클래스(Factory)를 분리하는 방법
```

<figure><img src="../../.gitbook/assets/image (1) (2).png" alt=""><figcaption><p>Simple Factory</p></figcaption></figure>

객체 생성하는 Factory 클래스를 따로 만드는 방법이다. ToyStore은 Factory를 통해 Toy를 생성한다. 단, ToyFactory 클래스 코드를 보면 OCP가 지켜지지 않음을 알 수 있다.



* **Product 클래스**

```java
public interface Toy {
		void playToy();
}

public class TeddyBear implements Toy {
		@Override
    public void playToy() {
        System.out.println("인형 눈알 붙이기");
    }
}

public class MiniCar implements Toy {
		@Override
    public void playToy() {
        System.out.println("미니카 달리기");
    }
}
```

*   **Factory 클래스 (Creator)** : 객체 생성을 담당한다.

    단, `Simple Factory 방식`은 새로운 구현 클래스를 추가하려면, 이 Factory 클래스의 코드 변경이 필요하다. 따라서 이러한 Simple Factory 방식은 OCP가 지켜지지 않고, 디자인 패턴이라 부르지 않는다. `Factory Method 패턴`을 이해하기 위한 중간 과정이라 볼 수 있다.

```java
public class ToyFactory { 
		// 새로운 toy 추가 시 이 클래스 내부를 수정해주어야 함!
    public Toy createToy(String toyType) {
        switch (toyType) {
            case TeddyBear:
                return new TeddyBear();
            case MiniCar:
                return new MiniCar();
            default:
                throw new IllegalArgumentException();
        }
    }
}
```

* **Client 클래스 (Product 사용하는 객체)**

```java
public class ToyStore {
		ToyFactory toyFactory;

		public ToyStore (ToyFactory toyFactory) {
				this.toyFactory = toyFactory;
    }

		public Toy orderToy(String toyType) {
				Toy toy;
				toy = toyFactory.createToy(toyType);
				return toy;
    }
}
```

* **클라이언트 코드**

```java
//  ToyStore에서 생성한 Toy 인스턴스 사용 시
ToyFactory toyFactory = new ToyFactory();
ToyStore toyStore = new ToyStore(toyFactory);
toyStore.orderToy("TeddyBear");
toyStore.orderToy("MiniCar");
```

이러한 Simple Factory 방식에서 구현체를 추가/변경하려면, Factory 클래스 내부 코드를 변경해야 한다. 다음의 Factory Method 방식은 기존 코드를 변경하지 않으면서 새로운 구현체를 추가할 수 있다.





### 3. 팩토리 메소드

```
💡 객체 생성용 Factory 클래스를 분리하고, 어떤 객체를 생성할지 서브 클래스가 결정하는 방식
```

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption><p>Factory Method Pattern</p></figcaption></figure>

객체를 생성하는 서브 클래스(`Factory`)를 만들어, 어떤 객체를 생성할지는 서브 클래스(`Factory`)가 결정한다. 기존 클래스에서는 객체 생성에 대한 정보를 가지지 않고, 서브 클래스(Factory)에서만 조작할 수 있다. 즉, 클래스의 생성과 사용을 분리하여 결합도를 낮춘다.

*   **Product 클래스 → 여기서 인스턴스 생성하지 않음**

    ```java
    // Product
    public interface Toy {
        void playToy();
    }
    // Product의 인스턴스(ex. TeddyBearProduct) 생성을 Product 클래스가 하지 않음
    public class TeddyBearToy implements Toy {
        @Override
        public void playToy() {
            System.out.println("인형 눈알 붙이기");
        }
    }
    ```
*   **Factory 추상 클래스,**

    **Factory의 구체 클래스 (Creator)** : 실제 인스턴스 생성을 담당한다.

    ```java
    // Factory 추상 클래스
    public abstract class ToyFactory {
        protected abstract Toy createToy();
    }

    // Factory의 구체 클래스 - 인스턴스 생성 담당
    public class TeddyBearToyFactory extends ToyFactory {
        @Override
        protected Product createToy() {
            return new TeddyBearToy();
        }
    }
    ```
*   **Client 클래스 (Product 사용하는 객체)**

    ```java
    public class ToyStore {
    		ToyFactory toyFactory;

    		public ToyStore (ToyFactory toyFactory) {
    				this.toyFactory = toyFactory;
        }

    		public Toy orderToy(String toyType) {
    				Toy toy;
    				toy = toyFactory.createToy(toyType);
    				return toy;
        }
    }
    ```
*   **클라이언트 코드**

    ```java
    // ToyStore에서 Toy 인스턴스 생성/사용
    ToyFactory teddyBearToyFactory = new TeddyBearToyFactory(); // Factory에서 인스턴스 종류 결정
    ToyStore toyStore = new ToyStore(teddyBearToyFactory.createToy());

    // 다른 toy를 생성하려면, Factory의 새 구현 클래스를 만든다.
    ToyFactory miniCarFactory = new MiniCarToyFactory();
    ToyStore toyStore = new ToyStore(miniCarFactory.createToy());
    ```



### 정리하면

* Factory Method Pattern은 객체의 생성을 서브 클래스에 위임하는 패턴이다.
* 쉽게 말해 객체의 사용과 생성을 분리한다.
* 새로운 객체를 추가해도 기존 코드를 바꾸지 않고, 객체 생성 담당 Factory 클래스의 새로운 구현체를 추가해주면된다.
* 즉, 기존 코드는 바꾸지 않으면서, 기능 확장에는 열려있게 된다.

****

**Reference**

* [https://bcp0109.tistory.com/366?category=1019841](https://bcp0109.tistory.com/366?category=1019841)
* [https://scorpio-mercury.tistory.com/18](https://scorpio-mercury.tistory.com/18)
* [https://gre-eny.tistory.com/179](https://gre-eny.tistory.com/179)
