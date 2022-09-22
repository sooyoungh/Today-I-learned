# equals()를 오버라이딩하면, hashcode()도 오버라이딩 해주어야 한다

**목차**

1. [동일성과 동등성](equals.md#동일성과-동등성)
2. [equals()를 오버라이딩하여 동등성 비교할 수 있다](equals.md#equals를-오버라이딩하여-동등성-비교할-수-있다)
3. [equals()를 오버라이딩하면, hashcode()도 오버라이딩해주어야 한다!](equals.md#equals를-오버라이딩하면-hashcode도-오버라이딩해주어야-한다)

> 두 객체를 비교할 때, 두가지 방법으로 비교할 수 있다. 완전히 같은 객체인지 혹은 두 객체의 내용이 같은 지 비교할 수 있다. 이와 관련한 동일성과 동등성의 개념과 `equals()`를 오버라이딩하는 방법 및 주의 사항을 정리해보았다.

## 동일성과 동등성

```
🌟 동일성(Identity) : 두 객체가 같은 객체인지
    ➡ == 연산자로, 객체가 참조하고 있는 주소값을 비교한다.

    동등성(Equality) : 두 객체의 내용이 같은지
    ➡ equals 연산자로, 객체의 인스턴스 내용을 비교한다.
```

## equals()를 오버라이딩하여 동등성 비교할 수 있다

`equals()`는 기본적으로는 주소값을 비교한다. 즉, 동일성을 비교한다. 그러나 필요시 두 객체의 내용이 같은 지 비교하려면 `equals()`을 오버라이딩하여 비교한다.

* 기본 `equals()` 내부 : 동일성 비교

```java
public boolean equals(Object obj) {
	return (this == obj);
}
```

* 아래처럼 오버라이딩하면 내용이 같은지 동등성(equality) 비교하는데 사용할 수 있다. 형변환할 때 `instanceof` 를 활용하면 좋다.

```java
// 1. 기본
@Override
public boolean equals(Object obj) {
	return this.속성 == ((클래스명)obj).속성;
}

// 2. 형변환
@Override
public boolean equals(Object obj) {

	boolean result = false;
        
	// Animal 클래스의 equals() 메소드일 경우, 자식Dog/Cat 구분하기 위해
	// instanceof 사용함
	if (obj instanceof Dog) {
		if (((Dog)obj).dogNum == dogNum ) {
			result = true;
		}
	} elif (obj instanceof Cat) {
		if (((Cat)obj).catNum == catNum ) {
			result = true;
		}
	}
	return result;
}
```

## equals()를 오버라이딩하면, hashcode()도 오버라이딩해주어야 한다!

`equals()` 비교시, 내부적으로는 `hashCode()` 을 먼저 비교한다. 즉, 두 객체의 `hashCode()` 메서드의 리턴 값을 우선 비교한다.

만약 `hashCode()` 값이 다르면, `equals()`의 오버라이딩한 코드로 넘어가지 않고 두 객체는 다르다고 판단한다. 객체의 내용이 같아도, 서로 다른 해시값을 가지기에 서로 다르다고 나온다. 즉, 동등성 비교를 위해 `equals()`을 오버라이딩한 의미가 없어진다! 따라서 객체의 동등성 비교를 위해서는 Object의 equals() 메소드만 재정의하지 말고, hashCode() 메소드도 함께 재정의해야한다.

아래처럼 `equals()` 을 오버라이딩하면, 다음과 같이 `hashCode()`도 오버라이딩해주어야 한다. (intellij 의 Generate 기능을 쓰면 자동으로 다음과 같이 오버라이딩된다)

```java
@Override
public int hashCode() {
	return Objects.hash(name);
}
```

단, 이 방법은 속도가 느려져서 빠른 속도가 필요하면 직접 오버라이딩을 해주는 게 좋다고 한다. ⇒ [참고 링크](https://tecoble.techcourse.co.kr/post/2020-07-29-equals-and-hashCode/)

**Reference**

* [https://velog.io/@kekim20/JAVA-hashCode메서드-오버라이딩](https://velog.io/@kekim20/JAVA-hashCode%EB%A9%94%EC%84%9C%EB%93%9C-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9)
* [https://jiwondev.tistory.com/113?category=882002](https://jiwondev.tistory.com/113?category=882002)
* [https://tecoble.techcourse.co.kr/post/2020-07-29-equals-and-hashCode/](https://tecoble.techcourse.co.kr/post/2020-07-29-equals-and-hashCode/)
