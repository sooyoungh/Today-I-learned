# List<> = new ArrayList<> 쓰는 이유


**목차**
- [자바의 형변환](#자바의-형변환)
- [업캐스팅/다운캐스팅 하는 이유?](#업캐스팅다운캐스팅-하는-이유)
  - [1. 선언 시](#1-선언-시)
  - [2. 매개변수](#2-매개변수)
  - [3. 배열](#3-배열)
- [정리하면](#정리하면)


> ArrayList<> arr = new ArrayList<>();  이 아니라
>   
> List<> arr = new ArrayList<>  처럼 부모클래스로 선언하는 걸 '업캐스팅'이라 한다.
> 
> 이렇게 선언 시 말고도 업캐스팅이 유용하게 쓰이는 상황이 있다.
> 
> 각 상황과 사용하는 이유를 알아보자❕ 우선 자바의 형변환 개념부터 알아보자



## 자바의 형변환

형변환은 다른 타입으로 변환하는 작업을 뜻한다.

- **업캐스팅** : 자식 클래스의 객체가 부모 클래스 타입으로 형변환되는 것
    - `Parent obj = new Child();`
- **다운캐스팅** : 업캐스팅된 것을 다시 원래로 돌리는 것
    - `Parent p = new Child();` // 업캐스팅
    - `Child c = (Child) p;` // 다운캐스팅

## 업캐스팅/다운캐스팅 하는 이유?

<aside>
  
    🌟 다형성과 관련있다!

</aside>

이유를 각각의 케이스와 함께 알아보자. 

우선 업캐스팅/다운캐스팅을 사용하는 경우는 주로 다음과 같다.

1. 선언 시
    - 선언부는 인터페이스로, 구현부는 자식클래스로 하는 경우
    - ex) `List<Object> arr = new ArrayList<>();`
2. 매개변수
    - 매개변수로 인터페이스를 받을 경우
    - ex) `public void ...(CharSequence x)`
3. 배열타입
    - 배열타입을 부모클래스로 선언할 경우
    - ex) `Parent[] arr = new Parent[10];`

## 1. 선언 시

```java
// bad
ArrayList<Object> arr = new ArrayList<>();

//  good 업캐스팅
List<Object> arr = new ArrayList<>();
// 선언부는 List, 구현부는 ArrayList인 경우
```

(업캐스팅한 경우, ArrayList만이 가진 메소드는 사용할 수 없다.)

이처럼 인터페이스로 선언하는 이유? (업캐스팅 하는 이유)

- 후에 구현체를 List의 다른 자식 클래스로 변경할 경우가 있을 수 있다.
    
    그때 구현체를 바꾸어도 내부 코드를 바꿀 필요가 없다! 즉, **변경에 유연하다.**
    
    ```java
    // 최초 선언 - 업캐스팅
    List<Object> arr = new ArrayList<>();
    
    // 아래처럼 구현체를 바꾸어도 된다.
    List<Object> arr = new LinkedList<>();
    ```
    

## 2. 매개변수

```java
// good - 인터페이스를 매개변수로
public void ...(CharSequence x)

// bad - 각각의 구체 클래스를 매개변수로
public void ...(String x)
public void ...(StringBuilder x)
public void ...(StringBuffer x)
```

만약 메소드에서 문자열과 관련된 처리를 한다고 하자. 

매개변수에서 `CharSequence` 인터페이스로 받을 경우, `CharSequence`를 구현하는(상속받는) `String`, `StringBuilder`, `StringBuffer`을 모두 매개변수로 받을 수 있다. 즉, 각각의 메소드를 따로 만들지 않아도 된다. **코드의 중복 없이 다형성을 구현할 수 있다**. 그리고 함수 내부에서 `instanceof` 로 어떤 자식 클래스인지 구분해주면 된다.

## 3. 배열

```java
Parent[] arr = new Parent[10];

arr[0] = new Parent();
arr[1] = new Child1(); // Parent의 자식 클래스들도 담길 수 있다.
arr[2] = new Child2(); // 업캐스팅
```

부모 클래스로 배열을 선언하면 부모 자신과 자식 클래스 객체들을 모두 담을 수 있다. 

**여러 타입들을 한번에 처리해야 하는 경우,** 배열을 통해서 반복문을 돌릴 수 있다. 혹은 **여러 타입들을 한번에 매개변수로 보내야 할 경우**에도 사용한다. 배열과 매개변수를 부모 타입으로 하면, 여러 자식 타입들을 한번에 배열에 담아 매개변수 하나에 보낼 수 있다. 
그리고 실제 사용할 때는 다운캐스팅해서 원객체로 사용하면 된다.


## 정리하면

<aside>
  
    🌟 업캐스팅을 하는 이유는 코드의 중복없이 다형성을 구현할 수 있기 때문이다. 혹은 변경에 유연한 코드를 쓰기 위해서 사용한다.

        따라서
        
        매개변수/리턴값/멤버필드/배열 등을 선언 시, 인터페이스를 사용하자.
        만약 적합한 인터페이스가 없다면 최상위 부모클래스를 사용하자!

</aside>

### Reference

- https://stackoverflow.com/questions/17059139/listobject-listobject-new-arraylistobject
- [[Java] List 리스트 = ArrayList 로 선언해 사용하는 이유 (tistory.com)](https://bibi6666667.tistory.com/236)
- 책 <이펙티브 자바> - 아이템 64 객체는 인터페이스를 사용해 참조하라

[위로가기⬆](#list--new-arraylist-쓰는-이유)
