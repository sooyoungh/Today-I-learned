# Optional 잘 쓰는 법1 - 언제 쓸까?

**목차**
1. [Optional을 사용하는 이유?](#optional을-사용하는-이유)
2. [Optional 사용하기](#optional-사용하기)
    - [Optional 객체 생성하기](#optional-객체-생성하기)
    - [Null인지 체크하기](#null인지-체크하기)
    - [Null일 경우 대체할 값 정하기](#null일-경우-대체할-값-정하기)
3. [비싼 Optional은 잘 써야 한다.](#비싼-optional은-잘-써야-한다)
    - [Optional은 이렇게 쓰자](#optional은-이렇게-쓰자)
4. [정리하면](#정리하면)

## Optional을 사용하는 이유?

Optional은 null일 가능성이 있는 객체를 wrapping한다. (Optional 또한 객체이다)  왜 객체를 wrapping할까?

null 객체를 참조하여 메소드 호출 시, `NPE(NullPoiterException)`가 발생한다. 따라서 객체가 null일 **가능성**이 있다면, 객체 참조 전에 null 인지 검사하는 코드가 있어야 한다. 그러나 검사 코드를 추가하면 가독성이 떨어지고 복잡해진다(프로그램이 커질수록 더). Optional은 null일 수도 있는 객체를 NPE 없이, null 처리하기 위해 사용한다.

<aside>
  
    ✨ 반환값이 null일 가능성이 있을 때, NPE를 방지하고 null 처리를 간단하기 위해 사용한다.

</aside>

물론 NPE를 무조건 피해야 하는 건 아니다. NPE가 발생하여 개발자에게 보고하는 게 좋은 상황도 있다. [체크예외를 언체크 예외로 던져주는 게 좋은 이유랑 같다.](https://github.com/sooyoungh/Today-I-learned/blob/main/java/wrapping_checked_exception_into_unchecked_exception.md) [관련 설명 참고 (댓글 중에...) ](https://stackoverflow.com/questions/31696485/why-use-optional-of-over-optional-ofnullable), it's much better to use `[NPE가 발생하는 상황]` as you will see a NullPointerException which will indicate that your program has a bug 

아래처럼 객체가 null일 가능성이 있는 경우, `Optional`을 사용하여 간단하고 안전하게 null 처리를 할 수 있다.

```java
// 객체가 null이면 NPE 발생
Person p = <value or null> ; // 만약 반환값이 null이라면
p.doSomeThing(); // NPE 발생

// NPE 방지 위해 객체가 null인지 사전 검사해야 한다.
if (person != null) {
	Company compay = person.getCompany();
	if ()
	return person.doSomeThing();
} else {
	// ...
}
```

아래는 Optional 클래스이다. 감싸고자하는 객체를 value에 저장한다. 따라서 null 이더라도 객체 참조 시 NPE가 발생하지 않는다. 

```java
public final class Optional<T> {

  private final T value;
  ...
}
```

## Optional 사용하기

### **Optional 객체 생성하기**

1. `Optional.empty()` : 비어있는 객체 생성용
2. `Optional.of()` : 값이 있는 객체 생성용
    - 이 메소드로 null 객체를 생성할 수 없다. null을 넣으면 NPE 발생한다. [참고 링크](https://stackoverflow.com/questions/31696485/why-use-optional-of-over-optional-ofnullable)
    - 일부러 null이면 NPE를 발생시켜 개발자에게 보고해야 할 경우 사용할 수도 있다.
3. `Optional.ofNullbale()` : 값이 null일 수도, 아닐 수도 있는 경우
    - 값이 없을 때 NPE를 발생시키지 않고, 로직을 계속 진행할 때 사용

```java
// 1) null일 경우
Optional<String> opt = Optional.empty(); 

System.out.println(opt); // Optional.empty
System.out.println(opt.isPresent()); // false

// 2) 객체가 있는 경우
Optional<String> opt = Optional.of(value);

// 3) 객체가 있을 수도 있고, null일 수도 있는 경우
Optional<String> opt = Optional.ofNullable( <value or null> );
System.out.println(opt); //Optional.empty 혹은 객체
```

### **Null인지 체크하기**

- `isPresent()` : Optional에 값이 있으면 True
- `isEmpty()` : Optional에 값이 없으면 True
- `ifPresent()` : 만약 값이 있는 경우 ( 괄호 ) 내부 로직을 처리한다.

위 메소드들은 Optional에서 값을 꺼낼 때, `get()` 사용전에 값의 null여부를 체크하기 위해 사용한다. 만약 Optional 안에 null인데 `get()`을 호출할 경우 `NoSuchElementException` 이 발생한다. ⇒ Optional을 사용해서 NPE는 피했지만 다른 에러 발생하여, Optional을 쓰는 의미가 없다.

```java
Optional<Integer> opt = Optional.ofNullbale(<value or null>);

// Bad : Optional 안에 null이라면 NoSuchElementException 발생
Integer data = opt.get();

// Good : Optional 안에 null 여부를 체크 해주어야 한다.
if (opt.isPresent()){ 
	Integer data = opt.get();
}
```

여기서는 `isPresent()`로 null여부를 판단한 뒤, `get()`으로 값을 꺼냈다. 그러나 이 두 가지를 한번에❗ 처리할 수 있는 메소드가 있다. null 체크 후에 값을 꺼내야하는 상황이면 다음의 메소드를 사용하는 게 간결하고 가독성 좋다.

### **Null일 경우 대체할 값 정하기**

다음 메소드들은 Optional에 값이 있으면 가져오고, 

Optional 안에 null이라면,

- `orElse(T)` :  `괄호 내부 값`을 리턴한다.
- `orElseGet(Supplier)` : `괄호 내부의 메소드`를 실행한다.
- `orElseThrow()` : 에러를 던진다.

> orElse와 orElseGet의 차이 ? ⇒ 내용이 좀 있어서 다음 글에서 자세히 다룬다.
> 

## 비싼 Optional은 잘 써야 한다.

Optional은 객체를 한번 감싸기 때문에 오버헤드가 발생한다. 일반 객체를 한번 더 감싸기에 메모리도 더 사용하고, 접근도 오래 걸린다. 따라서 꼭 필요한 상황에서만 써야한다. 잘못 사용하는 경우, 비싼 Optional보다 null 검사(`if문`)이 더 효율적일 수 있다.

글 초반에 Optional을 사용하는 이유를 다음과 같이 이야기했다.

<aside>
	
	❕ 반환값이 null일 가능성이 있는 객체인 경우, NPE를 방지하고 null 처리를 간단하기 위해 사용한다.

</aside>

위 문장에 언제 사용해야 하는 지가 다 담겨있다.

1. **반환값이 → 반환값에만O, 멤버필드/파라미터/생성자 등에 사용X**
2. **null일 가능성이 있는 객체인 경우 → 확실히 null인 경우 사용X**
3. **NPE를 방지하고 → NPE를 발생시키는 게 더 좋은 상황은 사용X**
4. **null 처리를 간단하기 위해 → Optional 사용해서 null 처리가 더 번거로워 질 경우 사용X**

구체적인 예를 보자

### Optional은 이렇게 쓰자

- `Optional` 변수가 값이 없다면 null을 할당하지 말자. (단, 단순 값이 없음을 표현하려면 null을 쓰자! 참조할 상황에서는 NPE 발생할 수 있으니까 Optional을 쓰는 것이다!)
    대신에 `Optional.empty()`를 사용하자. `Optional.empty()`는 싱글톤 패턴으로 되어있기 때문에 효율적으로 사용할 수 있다.
    
- **파라미터/생성자/필드 타입 등에는 쓰지 말자.** 파라미터로 받은 함수 내부에서는 어차피 null인지 검사해주어야 한다. 굳이 비싼 `Optional`을 쓰지 말자. → **반환값**에만 사용하자.
    
    ```java
    // Bad
    public void countCookie(Optional<Cookie> myCookie) {
    	myCookie.ifPresent(~).get(~);
    }
    
    // Good : 파라미터는 그냥 null체크를 해주자
    public void countCookie(Cookie myCookie) {
    	if (myCookie != null) {
    		// 로직
    	}
    }
    ```
    
- `Optional`은 직렬화할 수 없다. 따라서 `직렬화 + Optional`이 둘다 필요하다면 ⇒  멤버필드로 설정하지 말고, 다음처럼 메소드의 리턴값을 Optional으로 설계하자.
    
    ```java
    // Bad : Optional + 직렬화X
    public class Person {
        private String name;
        private Optional<String> email = Optional.empty(); // 직렬화X!
    }
    
    // Good : Optional + 직렬화O
    public class Person {
        private String name;
        public Optional<Car> getCarAsOptional() {
            return Optional.ofNullable(car);
        }
    }
    ```
    
- `Optional`**은 비싸기 때문에** 단순 값을 꺼낼 거라면 사용하지 말자. (참조해서 NPE가 발생할 경우에만 사용하자) 혹은 Collection/배열의 비어있음을 표현하려면 `Optional`이 아닌 **빈 Collection/배열**을 사용하자.

## 정리하면

> Optional은 null인 객체에 잘못 접근하여 NPE가 발생하는 상황을 방지하고, 효율적으로 대처할 수 있다. Optional을 사용하면 빈 객체이더라도 참조하여 메소드를 실행시킬 때  NPE를 발생시키지 않는다. 뿐만 아니라 null일 때의 대처 전략을 효율적으로 세울 수 있다. (단순 if문의 반복이 아니라 메소드 하나로 처리 가능)
> 
> 그러나 Optional은 객체를 한번 더 감싸기에 오버헤드가 있다. 따라서 꼭 필요한 상황들에 적용해야 한다. ⇒ 반환값이 null일 가능성이 있는 객체인 경우, NPE를 방지하고 null 처리를 간단하기 위해 사용하자❗
> 

**Reference**

- [https://madplay.github.io/post/java-optional-advanced](https://madplay.github.io/post/java-optional-advanced)
- [https://velog.io/@hope1213/Optional은-왜-사용하는지-사용시-주의사항](https://velog.io/@hope1213/Optional%EC%9D%80-%EC%99%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94%EC%A7%80-%EC%82%AC%EC%9A%A9%EC%8B%9C-%EC%A3%BC%EC%9D%98%EC%82%AC%ED%95%AD)
- [http://homoefficio.github.io/2019/10/03/Java-Optional-바르게-쓰기/](http://homoefficio.github.io/2019/10/03/Java-Optional-%EB%B0%94%EB%A5%B4%EA%B2%8C-%EC%93%B0%EA%B8%B0/)
- [https://mangkyu.tistory.com/203](https://mangkyu.tistory.com/203)
- [https://www.daleseo.com/java8-optional-after/](https://www.daleseo.com/java8-optional-after/)
- [https://codechacha.com/ko/java8-stream-optional/](https://codechacha.com/ko/java8-stream-optional/)
- [https://jdm.kr/blog/234](https://jdm.kr/blog/234)




[위로가기⬆](#optional-잘-쓰는-법1---언제-쓸까)

