# ch21 : Generic

**목차**

1. [Generic 사용하는 이유?](ch21\_generic.md#generic-사용하는-이유?)
2. [Wildcard](ch21\_generic.md#wildcard)
3. [Bounded Wildcard](ch21\_generic.md#bounded-wildcard)

## Generic 사용하는 이유?

> 만약 DTO의 멤버필드를 `Object`로 할 경우,
>
> 리턴값을 `Object`로 받아서 실제 사용시에는 형변환을 해주어야한다.
>
> 다만, 이때 실제 어떤 자료형인지 혼동될 수 있다. (`instanceof`를 통해 직접 if-else문으로 타입을 점검해줄 수도 있지만 번거롭다.)
>
> ⇒ 이때 이 자료형을 컴파일 시점에 점검할 수 있도록 해주는 것이 `Generic`이다!

예시)

```java
```

```
💡 이런 식으로 형변환을 직접 해주지 않고, 해당 자료형이 그대로 리턴된다. 이때 만약 자료형이 다르면, 컴파일 에러가 발생한다.
```

**제네릭 타입 이름 정하기**

* E : 요소 (자바 Collections)
* K : 키
* N : 숫자
* T : 타입
* V : 값
* S, U, V : 각각 2,3,4 번째 선언된 타입

## Wildcard

* `<?>`
* **매개변수로** 여러 타입을 받고 싶을 경우 \<?>을 사용한다.
  * 제네릭 클래스의 타입만 바꾼다고 오버로딩할 수는 없기에 와일드카드를 사용함.
* 그리고 해당 메소드 내에서는 `instanceof`를 통해서 타입을 확인하면 된다.
*   예시) 메소드에서 매개변수로 한가지 타입만 받을 수 있도록 되어있다.

    ```java
    // 제네릭 클래스
    public class WildcardGeneric<W> {
      W wildcard;

    public void setWildcard(W wildcard) {
        this.wildcard = wildcard;
    	}

    public W getWildcard() {
        return wildcard;
    	}
    }

    // 제너릭 객체 생성
    WildcardGeneric<String> tmp = new WildcardGeneric<String>();
    wildcardMethod(tmp); // 함수 호출

    // 함수-> 매개변수 String타입만 받을 수 있음
    public void wildcardMethod(WildcardGeneric<String> c) {
    	String val = c.getValue();
      // ...
    }
    ```
*   단, \<?> 와일드카드는 매개변수로만 사용할 것! 객체 선언 시에도 사용할 수는 있지만, 특정 타입으로 값을 할당할수는 없다.

    ```java
    // 매개변수로 여러 타입을 받을 수 있음
    public void wildcardMethod(WildcardGeneric<?> c) {
    	Object val = c.getValue();
    	if(val instanceof String) {
      // 
    	}
    }
    ```

## Bounded Wildcard

* `<? extends 부모클래스>`
* 와일드카드의 타입 범위를 정할 수 있다.
* 매개변수/리턴값에서 `<?>` 대신 `<? extends 부모클래스>` 을 사용하면, 상속받은 부모클래스만 받을 수 있다.

```java
public void callWildcardMethod(WildcardGeneric<T extends Car> c) {
	Car val = c.getValue();
	//...
}
```

*   단, 이 경우에는 매개변수로 온 변수에 값을 추가할 수 없다.

    * _와일드카드로 선언된 변수는 특정 타입으로 값이 할당될 수 없기 때문!_
    * 매개변수로 온 변수에 값을 추가하고 싶으면,
    * 메소드 선언 시 리턴 타입 앞에 제너릭한 타입을 선언해 주고, 그 타입을 매개 변수에서 사용하면 된다.

    ```java
    public<T> void callWildcardMethod(WildcardGeneric<T> c, T addValue) {
      c.setWildcard(addValue);
      T value = c.getWildcard();
      System.out.println(value);
    }
    ```
