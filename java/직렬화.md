# 자바의 직렬화/역직렬화

**목차**
1. [직렬화란](#직렬화란)
2. [직렬화하는 방법](#직렬화하는-방법)
    - [Serializable](#Serializable)
    - [ObjectStream](#ObjectStream)
    - [Transient](#Transient)
4. [역직렬화](#역직렬화)
    - [serialVersionUID](#serialVersionUID)
6. [정리하면](#정리하면)



## 직렬화란




<aside>
  
    🌟 객체를 저장/전송 가능하도록 연속적인 형태로 변환하는 과정이다. 객체 데이터를 연속적인 흐름으로 만들어, Stream을 통해 데이터를 읽는다.

</aside>

> **직렬화를 하는 이유?**
> 
> 참조 자료형의 경우, stack에는 `주소`(실제 데이터가 담긴 heap영역 주소)가 담겨있다. 이 객체를 파일에 저장한다고 하면 주소값이 저장된다. 만약 프로그램 종료 후/혹은 네트워크 통신을 하게 될 경우, 이 메모리 주소값은 다른 객체를 가리키므로 의미없는 데이터가 된다. 따라서 객체를 저장/전송할 때는 `실제 데이터들` 을 찾아 저장해야 한다.
> 

## 직렬화하는 방법

### Serializable

- `java.io.Serializable` 인터페이스를 implements한다.
- 구현할 부분은 없고, 해당 **클래스가 직렬화가 제공되어야 함만을 JVM에게 알려주는** 역할을 한다.

```java
public class Book implements Serializable {
    private static final long serialVersionUID = -2936687026040726549L;
    private String bookName;
    private transient String description; // 직렬화 제외
    
    // getters and setters
}
public static void serialize(Book book) throws Exception {
    FileOutputStream file = new FileOutputStream(fileName);
    ObjectOutputStream out = new ObjectOutputStream(file);
    out.writeObject(book);
    out.close();
    file.close();
}
```

### ObjectStream

- 객체를 JVM 외부로 내보내거나, 외부 객체를 JVM 내부로 읽을 때 사용하는 `Stream`이다.
- **ObjectOutputStream :** 객체에 직렬화를 수행해준다.
- **FileOutputStream :** 직렬화한 내용을 저장할 파일을 생성해준다.

객체를 직렬화하고 파일로 저장하면 다음과 같다.

```java
public static void serialize(Book book) throws Exception {
    FileOutputStream file = new FileOutputStream(fileName);
    ObjectOutputStream out = new ObjectOutputStream(file);
    out.writeObject(book);
    out.close();
    file.close();
}
```

### Transient

- 직렬화에서 제외하고 싶은 경우 선언하는 키워드이다.
- 보안 상의 이유로 저장하지 않거나(비밀번호, 주민번호 등), 저장할 필요가 없는  임시 데이터인 경우 직렬화 과정에서 제외하기 위해 사용한다.
- `private transient String password;` // 필드는 유지되고, 값은 null로 대입된다.

## 역직렬화

<aside>
  
    🌟 직렬화된 파일들을 역으로 다시 객체 형태로 만드는 과정이다. 저장/전송된 데이터를 읽어 객체 형태로 복원한다.

</aside>

아래는 직렬화된 파일을 읽어, 객체 형태로 복원하였다.

```java
public static Book deserialize() throws Exception {
    FileInputStream file = new FileInputStream(fileName);
    ObjectInputStream in = new ObjectInputStream(file);
    Book book = (Book) in.readObject(); // 객체로 형변환
    in.close();
    file.close();
    return book;
}
```

이렇게 역직렬화 시, 직렬화한 클래스와 역직렬화하는 클래스의 멤버필드/타입이 일치해야 한다. 이를 판단하기 위해 `serialVersionUID`필드를 사용한다.

### serialVersionUID

- 직렬화와 역직렬화 과정에서 값이 서로 맞는지 확인하기 위해 사용하는 멤버 필드이다.
  - 직렬화할 때 직접 선언해주지 않으면, 자동으로 클래스의 기본 해쉬값을 `serialVersionUID`로 사용한다.
  - 직접 선언해줄 경우, 클래스 구조에 변경이 생기더라도 역직렬화를 사용할 수 있다.

클래스 정보가 달라지면, `serialVersionUID`가 변경된다. 역직렬화 시 `serialVersionUID`이 똑같지 않다면 `InvalidClassException`예외가 발생한다!
자바 직렬화는 클래스 속성 변화에 엄격하다. int → long으로 타입 하나만 바꾸어도 클래스 구조가 바뀌었다고 판단하여 `serialVersionUID`가 달라진다. 따라서 변경될 수 있는 클래스에서는 자바 직렬화 대신 XML, JSON과 같은 포맷을 이용한 직렬화를 사용하는 것이 좋다.

```java
public class Member implements Serializable {
    private static final long serialVersionUID = 1L; // 직접 선언
}
```

## 정리하면

> 자바 직렬화는 멤버 변수 추가/타입 변경 등 변경에 엄격하다. 따라서 자주 변경되거나 오래 저장될 클래스에서는 지양하는 것이 좋다. 자바 직렬화는 짧은 만료 시간을 가지거나, 변경되지 않는 데이터를 객체화할 때 사용하는 게 좋다. 
> 
> 변경될 여지가 많은 클래스는 XML, JSON과 같은 포맷을 이용한 직렬화를 사용하는 게 좋다.
> 

Reference

- [https://www.baeldung.com/java-transient-keyword](https://www.baeldung.com/java-transient-keyword)
- [https://naco.tistory.com/33](https://naco.tistory.com/33)
- [https://techblog.woowahan.com/2551/](https://techblog.woowahan.com/2551/)
- [https://madplay.github.io/post/java-serialization-advanced](https://madplay.github.io/post/java-serialization-advanced)
- [자바 직렬화 대신 JSON 직렬화](https://atoz-develop.tistory.com/entry/JAVA%EC%9D%98-%EA%B0%9D%EC%B2%B4-%EC%A7%81%EB%A0%AC%ED%99%94Serialization%EC%99%80-JSON-%EC%A7%81%EB%A0%AC%ED%99%94)

[위로가기⬆](#자바의-직렬화역직렬화)
