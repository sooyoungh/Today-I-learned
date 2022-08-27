# Try with resources로 알아서 자원 해제하기

**목차**
1. [Try-with-resources](#try-with-resources)
2. [AutoCloseable](#autocloseable)
3. [정리하면](#정리하면)

> try-catch-finally에서는 finally블록에서 자원을 해제해주었다. 그러나 이럴 경우 번거롭고 코드도 복잡해져 Java 7부터는 자원을 알아서 해제시켜주는 `try-with-resources`가 등장하였다. 자세히 알아보자
> 

## Try-with-resources

- **try에 자원 객체를 전달하면, try 코드 블록이 끝나면 자동으로 자원을 종료해주는 기능**
- 즉, 따로 finally 블록이나 모든 catch 블록에 종료 처리를 하지 않아도 된다.

Java는 `try`문이 종료될 때 `try`문 안에서 선언된 객체의 `close()` 메소드를 호출한다. 개발자가 따로 finally에서 해제시켜줄 필요가 없다.

```java
// 기존 try-catch-fianlly
Scanner scanner = null;
try {
    scanner = new Scanner(new File("test.txt"));
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
} finally {
    if (scanner != null) {  // 해제되었는지도 체크해보아야 함! 
        scanner.close();
    }
}

// 자바7부터 try-with-resources
try (Scanner scanner = new Scanner(new File("test.txt"))) { // try문 이후에 알아서 해제된다.
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine()); 
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
}

```

## AutoCloseable

> Q : 그럼 `try`문에 선언된 모든 객체들이 자동으로 해제될까?

A : `java.lang.AutoCloseable`을 구현한 객체만 해당된다.
> 

`AutoCloseable`은 JDK1.7부터 추가된 인터페이스다. 

```java
package java.lang;

public interface AutoCloseable {
    void close() throws Exception;
}
```

개발자가 직접 `AutoCloseable`을 구현하는 클래스를 생성하여 사용할 수도 있다.

```java
// 
public class MyResource implements AutoCloseable {
    @Override
    public void close() throws Exception {
        System.out.println("Closed MyResource");
    }
}

// try-with-resources
private void orderOfClosingResources() throws Exception {
    try (AutoCloseableResourcesFirst af = new AutoCloseableResourcesFirst();
        AutoCloseableResourcesSecond as = new AutoCloseableResourcesSecond()) {

        af.doSomething();
        as.doSomething();
    }
}
```

## 정리하면

- 자바 7이후부터는 try문의 자원을 알아서 해제해주는 `try-with-resources`가 등장하였다.
- 단, `AutoCloseable`을 구현하는 클래스들만 해당된다.
- 개발자가 만드는 클래스에 `AutoCloseable`을 구현하여 활용해도 된다.

**Reference**

- [https://www.baeldung.com/java-try-with-resources](https://www.baeldung.com/java-try-with-resources)
- [https://codevang.tistory.com/142?category=827591](https://codevang.tistory.com/142?category=827591)



[위로가기⬆](#try-with-resources로-알아서-자원-해제하기)


