# 데코레이터 패턴

**목차**
1. [데코레이터 패턴이란](#데코레이터-패턴이란)
2. [데코레이터 패턴 사용하는 이유?](#데코레이터-패턴-사용하는-이유)
3. [예시](#예시)
4. [정리하면](#정리하면)


## 데코레이터 패턴이란

<aside>
  
    ✨ 기존 객체에 기능(책임) 추가하는 패턴이다. 새 Decorator 클래스를 만들어 상속과 컴포지션으로 구현한다.

</aside>

![image](https://user-images.githubusercontent.com/77563814/190975559-a09132ca-eaed-4b4c-8f72-c717b5a883ce.png)


기존 객체(`ComponemtImpl`)에 새로운 기능을 추가하고 싶은 경우,

1. 데코레이터 클래스(`Decorator`)를 생성한다. 
2. 데코레이터 클래스를 새로운 기능이 구현된 구체 클래스(`DecoratorImpl`)가 상속받는다.
3. 클라이언트단에서 부가 기능객체로 기존 객체를 감싼다.
    - `Copoment x = new DecoratorImpl1(new ComponentImpl)`

이 패턴을 사용하여, 기존 객체에 새 기능을 추가하였다.

## 데코레이터 패턴 사용하는 이유?

1. **기존 클래스의 코드를 변경하지 않으면서 새 기능을 추가할 수 있다.**

이 패턴은 기존 객체에 새로운 기능을 추가하고 싶을 때 사용한다. 만약 기존 클래스에 새 메소드를 추가해줄 수도 있다. 하지만, 이는 기존 코드를 변경하게 되므로 복잡해진다. 반면 이 패턴은 기존 클래스의 코드를 변경하지 않으면서 기능을 추가할 수 있다(위 예시에서 `ComponentImpl` 클래스 코드를 변경하지 않고 해당 객체에 기능을 추가하였다).

2. **동적으로 기능 추가할 수 있다.**

또한, 클라이언트 코드에서 새 기능을 추가하므로, 런타임시에(동적으로) 기능 추가를 할 수 있다.


> 따라서, 데코레이터 패턴은 여러 기능들이 계속 추가되어야 하는 경우, 여러 기능들을 조합해서 사용하는 경우에 사용하는 게 좋다.

## 예시

- [예시 참고](https://www.baeldung.com/java-decorator-pattern)
![image](https://user-images.githubusercontent.com/77563814/190975793-1f57e402-ee4f-4cd5-ab35-5c3125fff3df.png)



- **기존 객체 클래스** : 새 기능을 추가하고 싶지만, 이 클래스의 코드는 변경하고 싶지 않은 상황

```java
public interface ChristmasTree {
    Stringdecorate();
}

// 기존 객체
public class ChristmasTreeImpl implements ChristmasTree {

    @Override
    public String decorate() {
        return "Christmas tree";
    }
}
```

- **데코레이터 추상 클래스** : 이를 상속받는 구체 클래스에 새 기능이 담겨있다.

```java
public abstract class TreeDecorator implements ChristmasTree {
		// 컴포지션
    private ChristmasTree tree;
    
    public TreeDecorator(ChristmasTree tree) {
        this.tree = tree;
    }
    
    @Override
    public String decorate() {
        return tree.decorate();
    }

}
```

- **데코레이터 구체 클래스** : 데코레이터 추상 클래스를 상속받아서 새로운 기능을 구현한 구체 클래스

```jsx
// 구체 클래스 1
public class BubbleLights extends TreeDecorator {

    public BubbleLights(ChristmasTree tree) {
        super(tree);
    }
    
		// 새 기능 추가
    public String decorate() {
        return super.decorate() + decorateWithBubbleLights();
    }
    
    private String decorateWithBubbleLights() {
        return " with Bubble Lights";
    }
}

// 구체 클래스 2
public class Garland extends TreeDecorator {

    public Garland(ChristmasTree tree) {
        super(tree);
    }
    
    public String decorate() {
        return super.decorate() + decorateWithGarland();
    }

    private String decorateWithGarland() {
        return " with Garland";
    }
}
```

- **클라이언트 코드** : 부가 기능 객체로 기존 객체를 감싼다. 이를 통해 기존 객체에 새로운 기능을 추가/조합할 수 있다.

```java
@Test
public void whenDecoratorsInjectedAtRuntime_thenConfigSuccess() {
    ChristmasTree tree1 = new Garland(new ChristmasTreeImpl());
    tree1.decorate(); // "Christmas tree with Garland"
     
    ChristmasTree tree2 = new BubbleLights(
      new Garland(new Garland(new ChristmasTreeImpl())));
      tree2.decorate(); // "Christmas tree with Garland with Garland with Bubble Lights"
}
```

## 정리하면

- 데코레이터 패턴은 기존 객체에 새 기능을 추가할 때 사용한다.
- 기존 클래스의 코드 변경 없이, 동적으로 새 기능을 추가할 수 있다.
- 따라서 계속 기능을 추가해주어야하는 상황 혹은 여러 기능을 조합해주어야 하는 상황에서 사용하는 게 좋다.

**Reference**

- [https://www.baeldung.com/java-decorator-pattern](https://www.baeldung.com/java-decorator-pattern)
- [https://coding-factory.tistory.com/713](https://coding-factory.tistory.com/713)


[위로가기⬆](#데코레이터-패턴)
