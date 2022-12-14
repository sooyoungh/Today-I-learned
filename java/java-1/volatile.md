# 자바의 동기화 1 - volatile

**목차**

1. [가시성 문제](volatile.md#가시성-문제)
2. [volatile](volatile.md#volatile)
3. [정리하면](volatile.md#정리하면)

> 멀티스레드 환경에서는 공유자원의 일관성을 위해 동기화가 필요하다. 자바에서는 synchronized, volatile, atomic 이렇게 크게 3가지 동기화 기법이 있다. 먼저 가시성 보장에 사용하는 volatile의 의미와 한계에 대해 정리해보았다❕

`volatile` 은 가시성을 보장하기 위해 사용한다. 가시성 문제란 무엇일까❔

## 가시성 문제

멀티스레드 환경에서 **CPU cache와 메인 메모리(RAM)의 변수값이 불일치하는 경우가 있다.** 이때 **자원의 일관성이 깨지고, 이를 가시성이 보장되지 않는다**고 한다.

다음은 x = 0 인 변수를 어떤 스레드에서 x = 1로 바꾸어 CPU cache에만 업데이트된 상황이다. x의 변경 내용을 메인 메모리로 저장하려는 순간, 다른 스레드가 메인 메모리에서 x = 0 값을 읽었다. 현재 첫번째 스레드는 x = 1, 두번째 스레드는 x = 0이다. 두 스레드의 x 값이 다르다. 자원의 일관성이 깨졌다.

![image](https://user-images.githubusercontent.com/77563814/188260813-d5da54bb-6d17-471f-add9-5eb7ede49aad.png)

이처럼 어떤 스레드가 CPU cache에서 메인 메모리에 저장하기 전에 다른 스레드에서 메인 메모리에서 값을 읽어올 경우, 자원의 일관성이 깨진다. 즉, 가시성이 보장되지 않는다.

따라서 가시성 문제를 해결하기 위해 변수를 CPU cache를 거치지 않고 메인 메모리에서만 읽는 방식이 `volatile`이다.

## volatile

`volatile` 키워드를 사용하면, 메인 메모리에서만 쓰고 읽는다. CPU Cache를 사용할 때보다 성능이 떨어지므로 가시성이 보장되어야 할 상황에만 사용하는 게 좋다.

```java
public volatile int x = 0;
```

단, `volatile` 은 하나의 스레드만 **쓰기**/읽기를 하고, 나머지 스레드들은 읽기만 한 상황에서 가시성을 보장한다. 반면 여러 스레드에서 쓰는 상황에서는 일관성이 보장되지 않는다. 이를 동시성이 보장되지 않는다고 한다. 만약 여러 스레드에서 읽기/쓰기를 할 경우, 동시에 여러 스레드가 접근하지 못하도록 해주어야 한다. 자바에서는 `synchronized` 를 이용하여 이러한 동기화 처리를 해준다.

## 정리하면

* 가시성이 보장되지 않는다? => 어떤 스레드가 CPU cache에서 RAM에 저장하기 전에 다른 스레드에서 RAM에서 값을 읽어올 경우
* 가시성을 보장해주기 위해 `volatile`을 사용한다. 그러나, 성능이 떨어지므로 꼭 필요한 상황에서만 사용하는 게 좋다.
* 또한 `volatile`은 가시성이 보장되지만, 동시성이 보장되지는 않는다.
* 즉, 한 스레드만 쓰기/읽기를 하고 나머지가 읽기만 할 경우에 사용해야 한다.
* 여러 스레드가 쓰기/읽기를 할 경우에는 아예 동시에 한 스레드만 접근할 수 있도록 하는 `synchronized`을 사용하여 동시성을 보장한다.

**Reference**

* [https://nesoy.github.io/articles/2018-06/Java-volatile](https://nesoy.github.io/articles/2018-06/Java-volatile)
* [https://www.blog.ecsimsw.com/781](https://www.blog.ecsimsw.com/781)

[위로가기⬆](volatile.md#자바의-동기화-1---volatile)
