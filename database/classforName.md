# JDBC - Class.forName() 동작 방식

**목차**
1. [리플렉션이란](#리플렉션이란)
2. [Class.forName() 동작 과정](#ClassforName-동작-과정)
3. [정리하면](#정리하면)


> JDBC를 사용하려면 가장 먼저 내가 사용하려는 DB의 드라이버를 로드해야한다. 이때 사용하는 게 `Class.forName()`이다. 이 메소드는 따로 객체를 생성하지도 않았는데 어떻게 드라이버가 로드된 걸까? 이는 리플렉션과 클래스의 static 블록과 관련있다. 각각에 대해 알아보면서 `Class.forName()`의 동작과정을 정리해보았다.
> 

아래는 JDBC를 통해 DB에 접근하여 쿼리를 실행하는 코드 중 일부이다.

```java
Class.forName("com.mysql.cj.jdbc.Driver"); // 1. JDBC 드라이버 로딩

Connection connection = DriverManager.getConnection(address, userName, password); // 2. 커넥션 생성

PreparedStatement ps = con.prepareStatement(query); // 3. 쿼리문 실행
... 
```

여기서 `Class.forName()`은 리플렉션의 메소드 중 하나이다. 여기서 리플렉션이란?

## 리플렉션이란

<aside>
  
    🌟 컴파일 시간(Compile Time)에 특정 클래스의 정보을 알지 못하더라도, 
        실행 시간(Run Time)에 동적으로 특정 클래스의 정보를 알아낼 수 있는 프로그래밍 기법이다.

        즉, 런타임 시점에 동적으로 클래스 정보에 접근하기 위해 리플렉션을 사용한다.

</aside>

- **사용하는 이유? : Java 정적 언어의 한계**

    Java는 컴파일 시점에 타입을 결정하는 정적인 언어라, 런타임 시점에 타입을 결정할 수 없다. 
      따라서, **컴파일 단계에서 모든 타입이 결정**되어 있어야 한다.
    즉, 코드에 타입에 대한 모든 내용이 작성되어 있어야 한다. (런타임때 동적으로 결정될 수 없으므로)
    이는 **구체화에 의존한다는 의미이고, 이로 인해 변경에 취약**해진다.
    이러한 정적 언어의 한계를 극복하기 위해, **런타임 시점에 동적으로 클래스 정보에 접근하기 위해 리플렉션**을 사용한다.
    

- **사용 예시**
    
    메소드들을 통해서 런타임에 해당 클래스 혹은 메소드의 행위를 접근(확인/수정/실행)할 수 있다. `Class.forName()` 는 리플렉션 메소드 중 하나이다.
    
    ```java
    // Class 찾기 (클래스 이름으로부터 클래스 찾기)
    Class clazz = Class.forName("test.Child");
    System.out.println(clazz.getName()); // 출력 >> test.Child
    ```
    

이제 `Class.forName()`이 리플렉션이고, 이를 통해 런타임에 JDBC 드라이버에 접근함을 알 수 있다. 

근데 `Class.forName("com.mysql.cj.jdbc.Driver");` 여기서는 접근만 했지, 드라이버 객체를 반환하거나 다른 처리를 하지 않았다. 어떻게 연결이 성립된걸까?

## Class.forName() 동작 과정

JDBC 연결코드만 다시 보자. 첫 줄에서 리플렉션에 의해 런타임 시점에 해당 경로의 클래스(`com.mysql.cj.jdbc.Driver`)를 동적으로 로드한다.

```java
Class.forName("com.mysql.cj.jdbc.Driver"); // 1. JDBC 드라이버 로딩

Connection connection = DriverManager.getConnection("..."); // 2. 커넥션 생성
```

여기서 로딩한 `Driver` 클래스 코드이다. 런타임 시점에 해당 클래스(`Driver`)를 JVM의 클래스로더가 로딩하게 된다. 

```java
public class Driver extends NonRegisteringDriver implements java.sql.Driver {
    static {
        try {
           java.sql.DriverManager.registerDriver(new Driver()); // registerDriver
        } catch (SQLException E) {
            throw new RuntimeException("Can't register driver!");
        }
    }
		...
}
```

이때, Driver 클래스 내부에 `static 블록`이 있다. `static 블록`은 클래스가 로딩될때, 바로 `메모리의 static 영역`에 올라가게 된다. **다시 말해, 인스턴스를 생성해주지 않고 클래스만 로딩해도 해당 부분은 실행되어 메모리에 올라간다. 

이 때문에 `Class.forName("com.mysql.cj.jdbc.Driver");` **이 코드만으로도, 즉 따로 객체를 반환하거나 다른 메소드를 호출하지 않아도 드라이버가 등록된다.**

따라서 알아서 자동 등록된 드라이버로 이후 작업을 실행할 수 있다. (커넥션 생성, 쿼리 실행 등)

```java
Class.forName("com.mysql.cj.jdbc.Driver"); // 1. JDBC 드라이버 로딩

Connection connection = DriverManager.getConnection(address, userName, password); // 2. 커넥션 생성

PreparedStatement ps = con.prepareStatement(query); // 3. 쿼리문 실행
... 
```

## 정리하면

- `Class.forName()` 은 런타임 시점에 동적으로 클래스 정보에 접근하는 리플렉션 메소드이다.
- 이때, `Driver`클래스의 `static` 블록에 드라이버를 등록하는 코드가 있다. static 블록은 클래스 로드할 때 메모리에 올라가서 실행된다(인스턴스를 만들지 않더라도).
- 따라서, `Class.forName("com.mysql.cj.jdbc.Driver");` 이 코드는 해당 드라이버 접근하면 자동으로 드라이버가 등록된다.
- 그래서 개발자가 따로 객체 생성 등을 하지 않더라도 드라이버가 등록되고, 이후 작업을 진행할 수 있다.




**Reference**
- [https://github.com/sooyoungh/Today-I-learned/blob/main/java/db.md](https://github.com/sooyoungh/Today-I-learned/blob/main/java/db.md)
- [https://github.com/Cs-Java-Interview/Tech-Interview/blob/main/java/reflection.md](https://github.com/Cs-Java-Interview/Tech-Interview/blob/main/java/reflection.md)
- [https://stackoverflow.com/questions/8053095/what-is-the-actual-use-of-class-fornameoracle-jdbc-driver-oracledriver-while](https://stackoverflow.com/questions/8053095/what-is-the-actual-use-of-class-fornameoracle-jdbc-driver-oracledriver-while)
- [https://www.baeldung.com/java-jdbc-loading-drivers](https://www.baeldung.com/java-jdbc-loading-drivers)
- [https://kyun2.tistory.com/23](https://kyun2.tistory.com/23)
