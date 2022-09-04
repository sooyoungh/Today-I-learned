# 자바의 동기화 - synchronized


**목차**
1. [synchronized method](#1-synchronized-method)
2. [static synchronized method](#2-static-synchronized-method)
3. [synchronized block](#3-synchronized-block)
4. [static synchronized block](#4-static-synchronized-block)
5. [정리하면](#정리하면)


> 자바의 동기화 방식 중 *`synchronized`* 에 대해 정리해보았다. *`synchronized`* 를 사용하면, 공유자원에 동시에 한 스레드만 접근할 수 있도록 할 수 있다. 이는 메소드 전체와 블록 단위로 적용할 수 있다. 또한, 접근 level은 instance-level lock 혹은 static을 사용하여 class-level lock의 level로 사용할 수 있다. 이에 대해 정리해보았다.
> 

## 1. synchronized method

해당 메소드를 호출한 인스턴스 단위로 동기화해줄 수 있다. 해당 인스턴스의 한 스레드만 접근 가능하다. 여러 인스턴스라면, 각각 한 스레드만 접근할 수 있다.

- 코드 영역 : 메소드 전체를 동기화한다.
- 대상 : 해당 메소드를 호출한 인스턴스 자체를 동기화한다. 즉, 해당 인스턴스의 한 스레드만 접근 가능하다.

`synchronized`메소드(임계 영역)에 접근하면 lock이 걸리고, 이 코드를 실행 후 메소드가 종료되면 unlock된다. 그 후에 다른 스레드가 접근할 수 있다.

```java
public synchronized void func( ) {
  // 메소드 전체가 동기화
}
```

만약 한 인스턴스에 여러 `synchronized`메소드들이 있다면 모두 lock을 공유한다. 한 스레드가 `synchronized` 메소드를 호출하는 순간, 다른 모든  `synchronized` 메소드는 lock이 걸려 호출할 수 없다(`synchronized`가 아닌 일반 메소드는 상관없이 호출 가능하다).

```java
public synchronized void func1( ) {
  // 메소드 전체가 동기화
}

public synchronized void func2( ) {
  // 메소드 전체가 동기화
}

public void func3( ) {
  // 언제나 호출 가능
}
```

단, 이 방식은 인스턴스 전체에 lock이 걸린다. 메소드 영역 전체에 lock이 걸리기 때문에 불필요한 영역도 함께 동기화된다. 따라서 자바에서는 필요에 따라 메소드 전체가 아니라 block 단위로 동기화 할 수 도 있다. block 단위 동기화를 알아보기전에, static 동기화에 대해 알아보자

## 2. static synchronized method

`synchronized method`는 이 메소드를 실행한 인스턴스에 대해서 동기화 처리를 한다. (instance-level lock) 따라서, 여러 인스턴스들이 각각 1개씩 메소드를 실행할 수 있다.

`static synchronized method`는 해당 클래스에 동기화 처리를 한다. 여러 인스턴스가 동시에 해당 메소드를 실행할 수 없다. 클래스 전체에서 오직 하나의 스레드만이 동작할 수 있다.

```java
// static synchronized method
public static synchronized void func() { 

}
```

## 3. synchronized block

위의 두가지 synchronized method는 메소드 단위로 동기화를 한다. 따라서 불필요한 코드 영역까지 동기화되는 단점이 있다. 블록 단위로 지정할 경우, 필요한 범위만 동기화할 수 있다. 이또한 해당 인스턴스의 한 스레드만 접근할 수 있다.

- 코드 영역 : 지정한 블록만큼만 동기화한다.
- 대상 : 지정하는 범위만 동기화할 수 있다.
    - 블록 내부의 멤버필드(`Object` )
    - 해당 클래스의 클래스 객체(`TargetClass.class`) ⇒ static synchronized와 같음
    - 해당 인스턴스 전체(`this`)  ⇒ synchronized method와 같음

만약  `Object` 객체를 선언하면, 해당 블록의 멤버 필드들만 동기화할 수 있다. 변수 `c1`과 `c2`는 동기화 처리가 필요하지만, 로직이 겹치지 않는다. 따라서 `c1`이 동기화될 때 `c2`는 lock될 필요없고, 반대도 마찬가지이다.  ⇒ [예시 참고](https://docs.oracle.com/javase/tutorial/essential/concurrency/locksync.html)

```java
// synchronized block - Object
public class MsLunch {
    private long c1 = 0;
    private long c2 = 0;
    private Object lock1 = new Object();
    private Object lock2 = new Object();

    public void inc1() {
				// 동기화 안해도 되는 부분
				// ...
        synchronized(lock1) { // 동기화해줄 부분
            c1++;
        }
    }

    public void inc2() {
				// 동기화 안해도 되는 부분
				// ...
        synchronized(lock2) { // 동기화해줄 부분
            c2++;
        }
    }
}
```

이처럼 코드 영역과 인스턴스의 일부 멤버필드만 동기화해줄 수 있다. 

또한 해당 클래스의 클래스 객체(`TargetClass.class`)단위로 동기화해줄 수도 있다. 이 방식은 static synchronized와 같게 할 수 있다(코드 영역이 전범위라면 같음).

```java
// 1,2번은 같다.
// 1) synchronized method - TargetClass.class
public static void func() { 
	synchronized(TargetClass.class) { 

	} 
}

// 2) static synchronized method 
public static synchronized void func() { 

}
```

그리고 블록 단위로 `synchronized` 해줄 수도 있다. 이때 `this` 를 사용하면, 메소드를 호출한 인스턴스 전체에 lock이 걸린다. 이 방식은 synchronized method와 같게 할 수 있다(코드 영역이 전범위라면 같음).

```java
// 1,2번은 같다.
// 1) synchronized block - this
public void func() {
	synchronized(this) { 
		// 동기화해줄 부분
	}
}

// 2) synchronized method
public synchronized void func( ) {
  // 메소드 전체가 동기화
}
```

## 4. static synchronized block

static synchronized method처럼 클래스 단위로 동기화하면서, 메소드 전체가 아닌 block단위로 동기화하는 방식이다. 단, static이기에 해당 인스턴스를 가리키는 `this`를 사용할 수 없다. 

```java
// static synchronized block
public static void func( ) {
	synchronized (TargetClass.class) { // this 안된다!
	// 동기화
	}
}
```

## 정리하면

- 자바에서는 `synchronized` 를 사용하여 공유자원에 동시에 여러 스레드가 접근하지 못하도록 동기화를 할 수 있다. 해당 인스턴스의 한 스레드만이 임계영역에 접근할 수 있다.
- 일반 : instance-level lock (해당 인스턴스의 한 스레드만 접근가능)
    - `synchronized method` : 메소드 단위로 동기화하는 방식
        - 메소드 전체와 메소드를 호출한 인스턴스 객체 전체를 동기화해준다.
    - `synchronized block` : 블록 단위로 동기화하는 방식
        - 필요한 코드 영역과 필요한 멤버필드만 동기화해줄 수 있어 효율적이다.
- Static : class-level lock (해당 클래스의 인스턴스들 중 한 스레드만 접근가능)
    - `static synchronized method` : // 위와 동일
    - `static synchronized block` :  // 위와 동일

**Reference**

- [https://tourspace.tistory.com/54](https://tourspace.tistory.com/54)
- [https://jeong-pro.tistory.com/227?category=773575](https://jeong-pro.tistory.com/227?category=773575)



[위로가기⬆](#자바의-동기화---synchronized)

