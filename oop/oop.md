# 객체지향 4가지 특징

**목차**

1. [추상화(Abstraction)](oop.md#추상화abstraction)
2. [캡슐화(Encapsulation)](oop.md#캡슐화encapsulation)
3. [상속(Inheritance)](oop.md#상속inheritance)
4. [다형성(Polymorphism)](oop.md#다형성polymorphism)
5. [정리하면](oop.md#정리하면)

> 자바는 객체지향언어이기에, 큰 프로그램을 개발할 때 유지보수/재사용성이 좋다. 이러한 객체지향은 4가지 특징을 가진다.
>
> 우선 필요한 기능만을 모아 클래스 단위로 추상화한다. 이 클래스 내부의 데이터를 보호하기 위해 접근 범위를 제한할 수 있다. 또한, 클래스들끼리 상속을 통해 재사용+확장을 한다. 마지막으로 같은 메소드이름이지만, 클래스(객체) 특성에 따라 다양하게 동작하게 할 수 있다. 이러한 객체지향 특징 4가지를 정리해보았다.

## 추상화(**Abstraction**)

* 하나의 클래스 역할에 맞는 기능만 최소한으로 구성/분리한다.
* 클래스/인터페이스로 추상화한다.
* 그리고 해당 기능(클래스)을 재사용+확장하고 싶으면 상속받아서 다형성을 구현한다.

## 캡슐화(**Encapsulation**)

* 접근 제어자로 클래스 내부를 보호(접근 제한)한다.
* 외부에서 함부로 클래스 내부의 데이터를 변경하지 못하도록 제한한다.
* 접근 제어자
  * `public` : 해당 클래스에서만 접근 가능
  * `protect` : 해당 패키지 내에서만 접근 가능
  * `default` : 동일 패키지 혹은 해당 클래스를 상속받은 클래스에서 접근 가능
  * `private` : 모든 클래스에서 접근 가능

```java
public class Car {

    // ...
    private int speed;

    public int getSpeed() {
        return color;
    }

    public void setSpeed(int speed) {
        this.speed = speed;
    }
    // ...
}
```

## 상속(**Inheritance**)

* 재사용 + 확장할 수 있다.
* The _Car_ `IS-A` _Vehicle_
* 사실 `is-a` 보다는 `is-a-kind-of` 가 적절하다(종류, 분류).

```java
public class Vehicle {
    private int wheels;
    private String model;
    public void start() {
        // the process of starting the vehicle
    }
    
    public void stop() {
        // process to stop the vehicle
    }
    
    public void honk() { 
        // produces a default honk 
    }

}

public class Car extends Vehicle {
    private int numberOfGears;

    public void openDoors() {
        // process to open the doors
    }
}
```

## 다형성(**Polymorphism**)

* 한 **메소드 이름으로 여러 동작 수행할 수 있다.**
* **오버로딩 / 오버라이딩이 있다.**
*   **오버로딩** : 메소드명은 같지만, 매개변수의 타입/갯수가 다르게 구현하기

    리턴값만 다르면 오버로딩 안된다. 메소드이름 하나로 다양한 객체를 받아 각자에 맞게 처리할 수 있다.

    ```java
    public String read() {
        return this.getContent()
          .toString();
    }
     
    public String read(int limit) {
        return this.getContent()
          .toString()
          .substring(0, limit);
    }
     
    public String read(int start, int stop) {
        return this.getContent()
          .toString()
          .substring(start, stop);
    }
    ```
*   **오버라이딩** : 상위 클래스의 메소드를 하위 클래스에서 재정의하기

    하위 클래스에서 부모의 메소드들을 재사용 + 확장할 수 있다.

    ```java
    // 부모 클래스
    public class GenericFile {

        public String getFileInfo() {
            return "Generic File Impl";
        }
    }

    // 자식 클래스
    public class ImageFile extends GenericFile {
    		// 오버라이딩
        public String getFileInfo() {
            return "Image File Impl";
        }
    }
    ```

## 정리하면

* 자바는 객체지향 언어로 유지보수/재사용성이 좋다. 객체 지향의 특징은 크게 4가지이다.
* 우선 필요한 기능/데이터만을 **클래스**로 추상화한다.
* 이 클래스 내부 데이터를 보호하기 위해 접근제어자로 **캡슐화**한다.
* 클래스들끼리 상속을 통해 기능을 재사용+확장을 한다.
* 그리고 오버로딩과 오버라이딩으로 통해 **다형성**을 구현한다.

[위로가기⬆](oop.md#객체지향-4가지-특징)
