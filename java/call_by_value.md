# 자바에 Call By Value만 있는 이유 - JVM의 GC

**목차**

1. [Call by Value와 Call by Reference이란?](call\_by\_value.md#call-by-value와-call-by-reference이란)
2. [자바에 포인터가 없는 이유](call\_by\_value.md#자바에-포인터가-없는-이유)
3. [C++의 Call By Value와 Call By Reference](call\_by\_value.md#c의-call-by-value와-call-by-reference)
4. [Java의 Call By Value](call\_by\_value.md#java의-call-by-value)
5. [자바에서도 Call By Reference 흉내는 낼 수 있다.](call\_by\_value.md#자바에서도-call-by-reference-흉내는-낼-수-있다)
6. [정리하면](call\_by\_value.md#정리하면)

자바에는 Call by Reference가 없고 Call by Value만 있다. 이유를 C++이랑 비교해서 알아보자.

우선 Call by Value, Call by Reference이란 다음과 같다.

## Call by Value와 Call by Reference이란?

> Call by Value : 값을 넘겨주는 호출 방식
>
> Call by Reference : 참조에 의한 호출 방식

이 두 가지 방식은 매개변수로 전달받을 경우 다르게 동작한다. 매개변수로 전달받았을 때 Call by Value는 값을 복사해서 처리하여, 전달받은 값을 변경해도 원본은 바뀌지 않는다. 반면 Call by Reference는 직접적인 참조를 넘기므로 전달받은 값을 변경하면 원본도 변경된다.

C++의 경우, 일반 변수는 Call by Value, 참조형은 Call by Reference이다. 반면 Java는 기본/참조 자료형 모두 Call by Value이다.

자바는 C++과 다르게 포인터가 없기 때문에 참조를 넘기는 Call by Reference 방식을 구현할 수 없다.

그럼 왜 자바는 _포인터_를 사용하지 않을까❓

## 자바에 포인터가 없는 이유

```
🌟 Java에 포인터가 없는 이유는 JVM의 GC와 관련있다.
```

* C++과 Java의 메모리 관리
  * C++은 개발자가 포인터에 임의의 메모리 주소를 할당할 수 있다. 개발자가 잘못된 주소값을 할당할 수도 있고, 그 책임은 개발자의 몫이다.
  * 반면 자바에서는 주소값 처리를 개발자가 아닌 JVM이 한다. 즉 개발자가 직접 참조자료형에 임의의 메모리 주소를 저장하지 못한다. 개발자가 객체 생성 시, JVM이 알아서 객체는 heap영역에 생성하고 주소값은 stack영역에 할당한다.
* 자바에 포인터가 없는 이유 - GC
  * 아래는 `Integer x = new Integer(10);` 으로 초기화해주었을 때의 메모리 영역이다.
  *   JVM에 의해 ‘알아서’ 객체는 heap 영역에 생성되고, 주소값은 stack 영역에 저장된다.

      ![image](https://user-images.githubusercontent.com/77563814/185362851-2ebedad6-55ea-4d26-87f2-e1c7549b6fbc.png)
  * JVM의 GC가 알아서 메모리를 관리한다. 이때 객체는 heap영역 내부에서 계속 이동하고, 따라서 주소값이 계속 바뀐다.(그림처럼)
  * 만약 포인터를 사용한다면, 실제 객체는 주소값이 바뀌었는데 개발자가 이전 주소값으로 접근할 수 있다. 즉, 의도치 않게 다른 객체에 접근할 위험이 있다.
  * 이렇게 GC 과정에서 **주소값이 바뀌기 때문에**, 자바에선 **개발자가 직접 주소값을 할당하는 포인터**를 사용하지 않는다.
  * 따라서 포인터가 없기에 자바에는 Call By Reference가 없다. (Call By Reference = 직접적인 참조를 넘기는 호출 방식)

## C++의 Call By Value와 Call By Reference

*   C++에서는 값을 넘기는 Call By Value와 포인터(참조)를 넘기는 Call By Reference가 모두 쓰인다.

    ```java
    // 1. call by value
    void Swap(int x, int y)
    {
    	int tmp = x;
    	x = y;
    	y = tmp;
    }

    int main()
    {
    	int a = 111;
    	int b = 222;

    	Swap(a, b); // swap함수 외부에서는 변경이 반영X
    	cout << "a : " << a << endl; // 111
    	cout << "b : " << b << endl; // 222
    }

    // 2. call by reference
    void Swap(int *a, int *b)
    {
    	int tmp = *a;
    	*a = *b;
    	*b = tmp;
    }

    int main()
    {
    	int a = 111;
    	int b = 222;

    	Swap(&a, &b); // swap함수 외부에서도 변경이 반영된다
    	cout << "a : " << a << endl; // 222
    	cout << "b : " << b << endl; // 111
    }
    ```

## Java의 Call By Value

* 자바에는 Call By Value만 있다. 아래와 같이 `swap()` 함수를 작성할 경우, 기본/참조 자료형 모두 원본 객체가 변경되지 않는다.
* 값이 복사되어 전달되기에 `swap()` 함수 내부에서만 처리되고 `swap()` 함수 외부에서는 변화가 없다.

```java
// 1. 기본 자료형 swap할 때
public class Test {

    public static void swap(int x, int y) {
        int tmp = x;
        x = y;
        y = tmp;
        }

    public static void main(String[] args) {
        int a = 111;
        int b = 222;
        swap(a, b);
        System.out.println(a); // 111
	System.out.println(b); // 222
    }
}

// 2. 참조 자료형 swap할 때
public class Test {

    public static void swap(Integer x, Integer y) {
        Integer temp = x;
        x = y;
        y = temp;
        }

    public static void main(String[] args) {
        Integer a = new Integer( 111 );
        Integer b = new Integer( 222 );

        swap(a, b); // 주소값만 복사되어서 swap 함수 밖에서는 적용X
        System.out.println(a); // 111
	System.out.println(b); // 222
    }
}
```

## 자바에서도 Call By Reference 흉내는 낼 수 있다.

* 참조 자료형은 주소값을 복사해 넘기기 때문에 해당 주소값으로 원본 객체에 접근할 수 있다. 아래처럼 `swap()` 함수 내부에서 원본 객체를 변경시키는 코드를 작성할 수도 있다.
* 단, 이렇게 멤버 필드를 직접 접근하여 변경시키는 코드는 **멤버 필드가 많아졌을 경우**에는 구현하기 어려워진다.
* ⇒ _따라서 Call By Reference와 완전 똑같이 구현할 수 있다고 보기는 어렵다!!_

```java
// 3. Call by Reference처럼 사용하기
public class Test {
    int value;

    public Test(int value) {
        this.value = value;
    }

    public static void swap(Test x, Test y) {
        int temp = x.value;
        x.value = y.value;
        y.value = temp;
        }

    public static void main(String[] args) {
        Test a = new Test(111);
        Test b = new Test(222);

        swap(a, b); // swap 함수 안에서 참조해서 데이터 변경할 경우, 외부에서도 반영됨
        System.out.println(a); // 222
				System.out.println(b); // 111
    }
}
```

## 정리하면

* Java는 GC 과정에서 객체의 주소값이 계속 바뀌기 때문에, 개발자가 주소값을 임의로 할당하면 위험할 수 있다. 객체의 주소값이 바뀌었는 데, 개발자가 이를 모르고 이전 주소값으로 다른 객체에 접근할 수 있기 때문이다. 따라서 자바에는 포인터가 없다.
* 자바는 메모리 관리를 개발자가 못하고 JVM이 ‘알아서’ 한다. 포인터를 안 쓰기 때문에, Call By Reference가 없다. Call By Value 방식만 있다.
* C++은 메모리 관리를 개발자가 한다. 포인터를 사용하기에 포인터(참조)를 매개변수로 넘겨주는 Call By Reference를 사용한다.

### Reference

* [https://you88.tistory.com/34](https://you88.tistory.com/34)
* [https://sorjfkrh5078.tistory.com/278](https://sorjfkrh5078.tistory.com/278)
* [https://deveric.tistory.com/92](https://deveric.tistory.com/92)

[위로 가기⬆](call\_by\_value.md#자바에-call-by-value만-있는-이유---jvm의-gc)
