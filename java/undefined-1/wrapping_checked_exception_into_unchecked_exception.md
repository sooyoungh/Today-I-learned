# wrapping\_checked\_exception\_into\_unchecked\_exception

### Checked Exception을 Unchecked Exception으로 throw하자

> Checked Exception은 예외 발생 시, try/catch문으로 복구 전략을 세운다.
>
> 하지만 명확한 복구 전략이 없을 수도 있고, 개발자에게 문제를 보고 하기 위해서
>
> Checked Exception을 Unchecked Exception으로 throw하는 게 좋다고 한다❕ 이유를 자세히 알아보자

`Checked Exception`는 try/catch문으로 개발자가 복구 전략을 마련해둔다. 그러나 이렇게 복구 전략을 마련해놓을 수 있는 경우가 많지 않다. `Checked Exception`이지만 복구할 수 없는 경우(unrecoverable conditions) try/catch문에서 catch문을 비워두기도 한다. 혹은 굳이 비워둔 catch문이 아니더라도, 예외가 잡혀도 개발자에게 보고되지 않고 계속 프로그램이 정상 실행된다는 문제가 있다(Execution of the program continues after the catch block, _as if nothing had happened)._ [참고 링크](https://web.archive.org/web/20060113050955/http://onjava.com/pub/a/onjava/2003/11/19/exceptions.html)

이렇게 try/catch문은 개발자에게 보고되지 않으므로 문제 해결이 어렵다. (만약 클라이언트 코드에서 해결 가능하고, 보고될 필요가 없는 경우는 체크예외를 사용해도 되겠지만..) 이때 `Checked Exception`를 `Unchecked Exception`로 throw하면 개발자에게 보고되어 문제를 파악할 수 있다. 따라서 명확한 복구 전략을 세울 수 없는 경우, `Unchecked Exception`으로 throw해서 개발자에게 명확한 예외 정보를 주어 해결하자.

다음의 예는 `Checked Exception`인 `SQLException`이 발생한 상황이다. `SQLException`이 발생하면, 클라이언트 코드로 해결할 방법이 없다. 그러면 아래처럼 catch문을 비워두기도 한다. 이는 개발자가 어떤 문제인지 알지 못하고 프로그램이 계속 정상 진행되므로 위험하다.

```java
// 복구 전략이 없어 catch문을 비워둔 상황
public void dataAccessCode(){
  try{
      ..some code that throws SQLException
  }catch(SQLException ex){
      ex.printStacktrace();
  }
}
```

따라서 아래처럼 Unchecked Exception으로 throw 해주자. 아래 코드는 `SQLException`이 발생시 `RuntimeException`으로 throw해준다. 실행 중인 스레드는 멈추고, 개발자에게 어떤 예외인지 보고된다. 즉, Unchecked Exception으로 바꾸어 개발자에게 예외를 보고하고 문제를 해결할 수 있게 한다.

```java
// Unchecked Exception으로 throw 해준 상황
public void dataAccessCode(){
   try{
       ..some code that throws SQLException
   }catch(SQLException ex){
       throw new RuntimeException(ex);
   }
}
```

⇒ [예시 참고](https://web.archive.org/web/20060113050955/http://onjava.com/pub/a/onjava/2003/11/19/exceptions.html) 여기서는 클라이언트 코드가 해결할 수 있는 경우, 해당 계층으로 또다른 Checked Exception으로 넘겨주는 방식도 소개하고 있다. 하지만 대부분의 경우 클라이언트 코드가 해결하기 어려우므로 Unchecked Exception으로 throw 해주자. 따라서 위 방식처럼 `RuntimeException` 혹은 이를 상속받은 Unchecked Exception으로 throw 해주자.

### 정리하면

> 명확한 복구 전략을 세울수 있다면 Checked Exception을 사용해도 좋다. 하지만 대부분의 경우, 개발자에게 예외 상황을 보고 하는 게 안전하다. Unchecked Exception으로 throw하여 개발자에게 예외 상황을 보고하고 안전하게 문제를 해결하는 게 좋다.

**Reference**

* [https://web.archive.org/web/20060113050955/http://onjava.com/pub/a/onjava/2003/11/19/exceptions.html](https://web.archive.org/web/20060113050955/http://onjava.com/pub/a/onjava/2003/11/19/exceptions.html)
* [https://softwareengineering.stackexchange.com/questions/121328/is-it-good-practice-to-catch-a-checked-exception-and-throw-a-runtimeexception](https://softwareengineering.stackexchange.com/questions/121328/is-it-good-practice-to-catch-a-checked-exception-and-throw-a-runtimeexception)
* [https://www.baeldung.com/java-checked-unchecked-exceptions](https://www.baeldung.com/java-checked-unchecked-exceptions)
* [https://howtodoinjava.com/java/exception-handling/checked-vs-unchecked-exceptions-in-java/](https://howtodoinjava.com/java/exception-handling/checked-vs-unchecked-exceptions-in-java/)
