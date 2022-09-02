# 자바의 동기화 - synchronized


**목차**
1. [synchronized method](#1-synchronized-method)
2. [synchronized block](#2-synchronized-block)
3. [정리하면](#정리하면)


> 자바의 동기화 방식 중 *`synchronized`* 에 대해 정리해보았다. *`synchronized`* 를 사용하면, 공유자원에 동시에 한 스레드만 접근할 수 있도록 할 수 있다. `synchronized`는 메소드 전체와 블록 단위로 적용할 수 있는데, 각각의 경우를 알아보았다.
> 

## 1. synchronized method

해당 메소드를 호출한 인스턴스 단위로 동기화해줄 수 있다.

- 코드 영역 : 메소드 전체를 동기화한다.
- 대상 : 해당 메소드를 호출한 인스턴스 자체를 동기화한다. 즉, 해당 인스턴스의 한 스레드만 접근 가능하다.

`synchronized` 메소드(임계 영역)에 접근하면 lock이 걸리고, 이 코드를 실행 후 메소드가 종료되면 unlock된다. 그 후에 다른 스레드가 접근할 수 있다.

```java
public synchronized void func( ) {
  // 메소드 전체가 동기화
}
```

만약 한 인스턴스에 여러 `synchronized`메소드들이 있다면 모두 lock을 공유한다. 한 스레드가 `synchronized` 메소드를 호출하는 순간, 다른 모든  `synchronized` 메소드는 lock이 걸려 호출할 수 없다(`synchronized`가 아닌 일반 메소드는 상관없이 호출 가능하다).

```java
public synchronized void func1( ) {
  // 메소드 전체가 같이 동기화
}

public synchronized void func2( ) {
  // 메소드 전체가 같이 동기화
}

public void func3( ) {
  // 언제나 호출 가능
}
```

단, 이 방식은 인스턴스 전체에 lock이 걸린다. 객체의 모든 부분에 lock이 걸리기때문에 관련없는 멤버필드들도 함께 동기화된다. 또한 메소드 영역 전체에 lock이 걸리기 때문에 불필요한 영역도 함께 동기화된다. 따라서 자바에서는 필요에 따라 메소드 전체가 아니라 block 단위로 동기화할 수 도 있다.

## 2. synchronized block

블록 단위로 지정할 경우, 필요한 범위만 동기화할 수 있다.

- 코드 영역 : 지정한 블록만큼만 동기화한다.
- 대상 : 해당 인스턴스 전체(`this`) 혹은 블록 내부의 멤버필드(`Object` 통해 )만 동기화할 수 있다.

다음처럼 블록 단위로 `synchronized` 해줄 수 있다. 이때 `this` 를 사용하면, 메소드를 호출한 인스턴스 전체에 lock이 걸린다. 아래 코드는 사실 메소드에 `synchronized` 해준 것과 같다.

```java
public void func() {
	synchronized(this) { 
		// 동기화해줄 부분
	}
}
```

만약  `Object` 객체를 선언하면, 해당 블록의 멤버 필드들만 동기화할 수 있다. 변수 `c1`과 `c2`는 동기화 처리가 필요하지만, 로직이 겹치지 않는다. 따라서 `c1`이 동기화될 때 `c2`는 lock될 필요없고, 반대도 마찬가지이다.  ⇒ [예시 참고](https://docs.oracle.com/javase/tutorial/essential/concurrency/locksync.html)

```java
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

이처럼 코드 영역과 인스턴스의 일부 멤버필드만 동기화해줄 수 있어 불필요한 부분은 제외하므로 효율적이다.

## 정리하면

- 자바에서는 `synchronized` 를 사용하여 공유자원에 동시에 여러 스레드가 접근하지 못하도록 동기화를 할 수 있다. 
해당 인스턴스의 한 스레드만이 임계영역에 접근할 수 있다.
- `synchronized` method : 메소드 영역에 동기화를 하는 방식
    - 메소드 전체와 메소드를 호출한 인스턴스 객체 전체를 동기화해준다.
- `synchronized` block : 블록 단위로 동기화하는 방식
    - 필요한 코드 영역과 필요한 멤버필드만 동기화해줄 수 있어 효율적이다.



[위로가기⬆](#자바의-동기화---synchronized)

