# 자바의 동기화 4 - ABA 문제

**목차**

1. [ABA 문제란](aba.md#aba-문제란)
   * [ABA 문제가 발생하는 참조자료형](aba.md#aba-문제가-발생하는-참조자료형)
2. [ABA 해결방법](aba.md#aba-해결방법)
3. [정리하면](aba.md#정리하면)

> 앞서 `synchonized` 동기화 기법보다 효율적인 Non-Blocking 동기화 방식에 대해 알아보았다. 그 중에서 CAS 알고리즘은 연산 전에 내가 알고 있는 값이 실제 메모리에 업데이트되어 있는 값이 맞는 지 확인하고 연산하는 방식이었다. 이는 Non-Blocking한, 효율적인 동기화 방식이었다. 단, 이 방식에는 ABA 문제가 발생할 수 있다. 이에 대해 알아보았다.❕

이번 내용은 다른 예시들이 어려워서 [이 글](https://www.baeldung.com/cs/aba-concurrency#4-the-aba-scenario)을 많이 참고하였다.

## ABA 문제란

CAS 알고리즘으로 자료구조의 요소를 추가/삭제할 때 발생하는 문제이다. 중간에 다른 스레드가 변경하여도 내(현재 스레드)가 모를 수 있다. 그럼에도 CAS 연산은 정상 처리되어 문제가 발생한다. 다음의 예시를 보자.

> 복습) CAS이란
>
> ```
>   내(현재 스레드)가 알고 있는 값, 실제 메모리에 저장된 값을 비교한 뒤에 연산하는 과정이다. 
>   이렇게 값을 비교할 때, 포인터 변수(주소값)를 가지고 비교하게 된다.
> ```

다음의 예시는 은행계좌를 연산하는 스레드1, 스레드2가 있다.

1. 최초의 계좌 잔액은 50이었다.
2. 스레드 1은 계좌 잔액이 50임을 확인했다.
3. 스레드 2가 중간에 60으로 바꾸었다가 다시 50으로 바꾸었다.
4. 스레드 1은 이러한 중간 과정을 모른채, 다시 계좌 잔액이 50임을 확인한다.

![image](https://user-images.githubusercontent.com/77563814/188419380-80495ff1-23d3-4833-8efe-3a412b2c239a.png)

[이미지 출처](https://www.baeldung.com/cs/aba-concurrency#4-the-aba-scenario)

스레드1은 중간에 스레드2의 연산 내용을 모른채, 처음과 마지막에 계좌가 동일하게 50임을 확인한다. 앞서 CAS는 내가 알던 값과 실제 메모리에 저장된 값이 같은지를 확인한 뒤 연산한다고 했다. 그럼 스레드1은 처음의 계좌 잔액(50)을 확인하고, 어떤 연산을 수행하려 할 때 값을 확인해보니 마지막 잔액(50)이 같으니 연산을 실행한다.

CAS의 알고리즘 상 다른 스레드가 끼어들지 않다고 판단하여 동기처리 되었다고 생각한다. 사실 처음 50과 마지막 50은 중간에 다른 스레드2가 작업한 상황인데, CAS 알고리즘에서는 이를 알지 못한다. 여기서는 어차피 전이나 후나 50이니까 문제될게 없지 않을까?

### ABA 문제가 발생하는 참조자료형

만약 위 상황처럼 변수가 `기본 자료형`이라면, 문제가 되지 않는다. 이러한 ABA 문제는 주소값으로 접근하는 `참조 자료형`(reference type)을 사용할 때 문제가 발생한다. 포인터가 원래 값(객체)이 아닌 다른 값(객체)을 가리킬 수 있기 때문이다. 왜냐하면 주소값은 이전과 같지만, 중간에서 다른 스레드가 어떤 작업을 했을 수 있기에 실제 heap에 할당된 데이터는 다를 수 있다.

**동기화 처리하는 이유**는 중간에 다른 스레드가 접근해서 데이터의 일관성이 헤칠수 있기 때문에 이를 방지하고자 한다. CAS는 이전과 지금 변수값이 같으니, `중간에 다른 스레드가 접근 안 했구나, 그럼 내(현재 스레드)가 값을 바꾸어도 되겠지?` 하는 생각에서 나온 알고리즘이다. `synchronized`는 아예 다른 스레드가 접근 못하게 `Blocking` 하여 안전하지만 비효율적이기에 이러한 CAS 알고리즘이 나왔다. 근데 위의 상황일때 `참조 자료형`이라면, 아예 다른 스레드가 접근한지도 모를 수 있는 것이다. 이런 경우에는 Blocking 되는 게 아깝다고 CAS를 사용했다가 아예 자원의 일관성을 깨뜨리는 상황이 된다.

다시 말해, `참조 자료형`을 비교할 때는 주소값으로 비교하기 때문에, 실제 값은 다른 스레드가 바꿔놓을 수 있는 것이다. 그럼 동기화가 아니다. 자원의 일관성은 이미 깨져있다. ABA 문제 해결방법을 알아보기전에, 위 상황의 코드를 가져왔다.

* 코드는 다음과 같다. 이전 글에서 [AtomicInteger](https://www.baeldung.com/java-atomic-variables) 는 CAS 연산을 언어수준에서 제공하는 Integer 자료형이라고 하였다.

```java
// 1) CAS 사용하는 Atomic 자료형들
public class Account {
    private AtomicInteger balance;
    private AtomicInteger transactionCount;
    private ThreadLocal<Integer> currentThreadCASFailureCount;
    ...
}

// 2) 예금
public boolean deposit(int amount) {
    int current = balance.get();
    boolean result = balance.compareAndSet(current, current + amount); // 내부적으로 compareAndSwap() 메소드 실행
    if (result) {
        transactionCount.incrementAndGet();
    } else {
        int currentCASFailureCount = currentThreadCASFailureCount.get();
        currentThreadCASFailureCount.set(currentCASFailureCount + 1);
    }
    return result;
}

// 3) 인출
public boolean withdraw(int amount) {
    int current = getBalance();
    maybeWait();
    boolean result = balance.compareAndSet(current, current - amount);
    if (result) {
        transactionCount.incrementAndGet();
    } else {
        int currentCASFailureCount = currentThreadCASFailureCount.get();
        currentThreadCASFailureCount.set(currentCASFailureCount + 1);
    }
    return result;
}
```

* ABA 문제가 발생하는 시나리오 코드
  1. `스레드1` : 처음에 계좌 잔액을 읽고 잠시 딜레이된다(실제로는 다른 작업을 하고 있거나 sleep() 상태).
  2. `스레드2` : 계좌잔액을 변경한다. (`스레드1`이 딜레이되는 동안)
  3. `스레드1` : 딜레이를 마치고, 다시 계좌 잔액 확인한다. 똑같으니 CAS 연산을 실행한다.

```java
// 스레드1
@Test
public void abaProblemTest() {
    // ...
    Runnable thread1 = () -> {
        assertTrue(account.withdraw(amountToWithdrawByThread1));

        assertTrue(account.getCurrentThreadCASFailureCount() > 0); // test will fail!
    };
    // ...
}

// 스레드2
@Test
public void abaProblemTest() {
    // ...
    Runnable thread2 = () -> {
        assertTrue(account.deposit(amountToDepositByThread2));
        assertEquals(defaultBalance + amountToDepositByThread2, account.getBalance());
        assertTrue(account.withdraw(amountToWithdrawByThread2));

        assertEquals(defaultBalance, account.getBalance());

        assertEquals(0, account.getCurrentThreadCASFailureCount());
    };
    // ...
}

// 스레드1은 기댓값과 메모리값이 같다고 판단
@Test
public void abaProblemTest() {
    // ...

    assertEquals(defaultBalance - amountToWithdrawByThread1, account.getBalance());
    assertEquals(4, account.getTransactionCount());
}
```

위에서 설명했듯이, AtomicInteger처럼 기본 자료형은 문제가 되지 않는다. 문제가 되는 건 참조 자료형이다.

아래는 ABA 문제 해결 방법이다. 이 역시 [같은 글](https://www.baeldung.com/cs/aba-concurrency#4-the-aba-scenario)에서 가져왔다.

## ABA 해결방법

1. **Garbage Collection**
   * GC 알고리즘에 의하면,
   * 스레드1이
2. Hazard Pointers
   * GC가 없는 언어라면, Hazard Pointers를 사용할 수 있다.
3. **Immutability**
   * 불변 객체를 사용하면 변경 시 매번 새로운 객체가 할당된다.
   * 같은 주소값인데, 데이터가 변경된 지 모르는 ABA 문제는 발생X
4. **Double Compare and Swap**
   * CAS 사용시, 버전 넘버를 함께 사용한다.
   * 이는 `AtomicStampedReference`와 `AtomicMarkableReference` 에 내부적으로 구현되어 있는 방식이다.

## 정리하면

* Non-Blocking한 동기화 방식인 CAS 알고리즘의 경우, ABA 문제가 발생할 수 있다.
* ABA 문제는 CAS가 실제 Blocking으로 동기화 하는게 아닌, 각자 개별 스레드에서 독립적으로 변수값을 판단하기에 문제가 발생한다.
* 기본 자료형이 아닌, 참조 자료형일 때 주소값은 같지만 실제 값이 변경되었을 수 있기 때문에 이러한 문제가 발생한다.
* ABA 문제는 JVM GC로 해결할 수 있다.

**Reference**

* [https://www.baeldung.com/cs/aba-concurrency](https://www.baeldung.com/cs/aba-concurrency)

[위로가기⬆](aba.md#자바의-동기화-4---aba-문제)
