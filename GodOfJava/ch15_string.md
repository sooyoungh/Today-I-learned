# ch15 : String



## String

String 클래스는 Serializable, Comparable, CharSequence 인터페이스를 상속받는다.(구현한다)

- Serializable : 직렬화할 때 사용
    - 객체를 파일로 저장하거나 다른 서버에 전송 가능한 상태로 변환
- Comparable : 객체의 순서를 처리할 떄 사용
- CharSequence : 해당 클래스가 문자열을 다루기 위한 클래스라는 것을 나타내기 위해 사용

**객체의 null 체크는 반드시 필요하다.**

- null인 겍체의 메서드에 접근하면 NullPointException이 발생한다.
    
    → 메서드로 넘어오는 객체가 널일 확률이 조금이라도 있다면 반드시 한 번씩 null체크를 하여 비정상적 에러를 방지할 것!
    

-------------메서드 추가…


## StringBuffer VS StringBuilder

> String은 불변(immutable)한 객체다. 즉, 처음에 만들어지면 더 이상 그 값을 바꿀 수 없다. 만약 '+'로 문자열을 더한자면, 새로운 String을 만들게 된다. → 새 String 객체를 생성하고, 기존 것은 버려지므로 비효율적이다!
> 

이런 단점을 보완하고자 `StringBuffer`와 `StringBuiler`가 나왔다. 

```java
// StringBuffer
StringBuffer sb = new StringBuffer();
sb.append("Hello");

// StringBuilder
StringBuilder sb = new StringBuilder();
sb.append("Hello").append(" World"); // append는 계속 붙이면서 사용가능하다.
```

둘다 문자열을 더하더라도 새 객체를 생성하지 않는다. 기능은 같으나, 스레드 측면에서는 차이가 있다.

- StringBuffer : Thread-safe하다
- StringBuilder : Thread-safe하지 않다. 속도는 더 빠르다.


<aside>
    
    💡 일반적으로 한 메소드 내부에서만 사용한다면 StringBuilder을 사용해도 문제 없다.
    반면에, 여러 Thread에서 동시 접근하는 일이 생긴다면 StringBuffer를 사용해야 한다.

</aside>


- String 더하기 연산을 할 경우, 컴파일 할때 자동으로 해당 연산을 StringBuilder로 변환해 준다.(JDK 5부터) 단, for루프 같은 반복 연산을 할 땐 자동으로 변환해주지 않는다.
- String, StringBufer, StringBuilder 모두 CharSequence 인터페이스를 상속받으므로
    
    매개 변수로 받을 경우 String, StringBuffer 각각의 타입으로 받기보다 `CharSequence` 타입으로 받는게 좋다.
