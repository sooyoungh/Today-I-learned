# 자바의 다형성 간단 정리

> 객체지향의 다형성이란 하나의 타입/메소드에 여러 객체를 대입할 수 있는 성질이다. 이를 통해 코드를 간결하고 효율적으로 작성할 수 있다. 자바의 다형성에는 크게 3가지 종류가 있다. (오버라이딩, 오버로딩, 제네릭)
> 
> 자바의 형변환(업/다운캐스팅)은 다음 글에서, 제네릭은 후에 더 자세히 알아보자. 여기서는 개념/용어 정리용으로 간단하게 알아보자.
> 

- [서브타입 다형성](#서브타입-다형성)
- [애드혹(임시) 다형성](#애드혹임시-다형성)
- [파라미터 다형성](#애드혹임시-다형성)

## 서브타입 다형성

- 상속받은 상위 클래스의 메소드를 하위 클래스에서 재정의하여 사용할 수 있는 기술
- 오버라이딩

```java
// 클래스 정보
class Animal {
    public void cry(){
        System.out.println("울음 소리");
    }
}

class Cat extends Animal{
    public void cry(){
        System.out.println("MEOW~");
    }
}

class Dog extends Animal{
    public void cry(){
        System.out.println("Woof!");
    }
}

// 실행
...
Animal cat = new Cat();
Animal dog = new Dog();
        
cat.cry(); // MEOW~
dog.cry(); // Woof!
```

## 애드혹(임시) 다형성

- 메소드명은 같고 매개변수의 개수/타입이 다를 때, 서로 다르게 동작할 수 있는 기술. (리턴 타입만 다를 수는 없다)
- 오버로딩(Overloading)
    - 구현은 각각, 실행은 한 메소드로 다 처리할 수 있음

```java
public void plus(int a, int b) { ... }
public void plus(float a, float b) { ... }
public void plus(int a, int b, int c) { ... }

// 실행
plus(10 + 10);
plus(10.0f + 10.0f);
plus(10 + 10 + 10);
```

## 파라미터 다형성

- 데이터 타입을 범용적으로 작성하여, 세부 타입에 상관없이 동일하게 처리할 수 있는 기술
- 자바의 제너릭
    - 클래스/메소드에서 사용할 내부 데이터 타입을 컴파일 시에 미리 지정하는 방법입니다.
    - 구현은 하나, 실행 시 원하는 타입 정해서 실행

```java
// 클래스 정보
class Box<T> {
    T item;
    void setItem(T item) { this.item = item;}
    T getItem() {return item;}
}

// 실행부
Box<String> a = new Box<String>();
Box<Integer> b = new Box<Integer>();
```
