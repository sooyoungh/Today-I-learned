# 자바의 동기화 3 - Lock-Free와 Atomic(CAS)

**목차**
1. [Non-Blocking 알고리즘](#non-blocking-알고리즘)
2. [Lock-Free 알고리즘](#lock-free-알고리즘)
3. [CAS 알고리즘이란](#cas-알고리즘이란)
4. [Atomic 클래스](#atomic-클래스)
5. [정리하면](#정리하면)


> 이전 장에서 `synchronized` 을 사용한 동기화 방식에 대해 정리해보았다. 이 방식은 한 스레드가 메소드/블락 전체를 lock하기 때문에 다른 스레드는 대기해야 하고(Blocking), 또 끝나고 바로 들어갈 수 없어(OS의 스케쥴링에 따라 결정) 비효율적이다. 이런 성능저하 외에도 Starvation이 발생할 수 있다. 이처럼 Lock을 사용한 Blocking방식은 비효율적이다.
> 
> 대안으로 Non-Blocking 방식을 사용한다. 
> - Non-Blocking 한 방식으로 가장 일반적으로 쓰이는 수준(등급)은 Lock-Free이다. 
> - Lock-Free는 Atomic 연산으로 구현한다. 
> - 이 Atomic에는 CAS 알고리즘이 사용된다. 각각에 대해 정리해보았다.✏
> 

## **Non-Blocking 알고리즘**

<aside>
  
    🌟 Blocking 알고리즘이란, 하나의 스레드가 임계영역에 접근하여 Lock을 걸면, 나머지 스레드들이 Blocking(진행X) 되는 방식이다.

       Non-Blocking 알고리즘이란, 임계영역으로 여러 스레드가 접근했을 경우 각자 중단 없이 진행되는 방식이다. (스레드들끼리 의존적이지 않다)

</aside>


아래는 Non-Blocking 알고리즘의 종류이다. 3번으로 갈수록 약한 정의이고, 포함관계에 있다. (모든 Wait-Free는 Lock-Free이지만, 반대는 아니다)

> 1. Wait-Free : 이상적인 방법
>     - 어떤 스레드도 Wait을 하지 않고 항상 모든 쓰레드가 실행되는 것
> 2. **Lock-Free** : 일반적으로 사용하는 방법
>     - Lock을 쓰지 않고, 스레드 충돌시 최소한 하나 이상의 스레드가 실행되는 것
>     - Wait-Free와 다른점은, 스레드 개별적으로는 Starvation이 있을 수 있다는 것이다. 전체적으로 항상 어떤 스레드는 진행된다는 점은 같다.
> 3. Obstruction-Free : 
>     - 최소한 하나의 스레드는 언젠가 독립적으로 실행된다는 것

Wait-Free는 이상적인 방식이지만, 구현이 어렵다. 또한 실제 성능이 떨어질 수 있고, 일반적으로 Lock-Free로 충분히 효과적이다. 

Lock-Free이 구체적으로 어떤 알고리즘일까?

## Lock-Free 알고리즘

쉽게 말해 Lock-Free 알고리즘은 

- Lock을 사용하지 않으며,
- **다른 스레드와 독립적으로 진행하며**
- 연산할 때, 내가 알고있는 변수 값이랑 같으면 연산하고 다르다면 대기했다가 재시도하는 방식

단순히 Lock을 사용하지 않아서 Lock-Free 알고리즘이 아니다! Lock의 문제점인 '**스레드 충돌시 Blocking 되는 것**'을 방지하는 알고리즘이다. 이를 방지하고자 다른 스레드와는 독립적으로 진행하며, 연산 시 내가 알고 있는 변수 값과 같을 때만 연산한다. 다르다면, 대기 후 재시도한다. ⇒ 후에 예시로 설명

이러한 Lock-Free 알고리즘은 대표적으로 CAS 알고리즘으로 구현할 수 있다. CAS 알고리즘은 Java, C++ 등이 지원한다.

## CAS 알고리즘이란

- **CAS 알고리즘 동작 방식**
    1. `Expected Value` (현재 스레드의 예상 값)과 `New Value`(변경할 값)을 전달한다.
    2. `Expected Value`이 `Variable` (현재 메모리의 값)과 같은지 비교한다.
        - 같으면, `New Value`으로 변경하고 true를 반환한다.
        - 다르면, 값을 변경하지 않고 false 혹은 `Variable`를 반환한다. ⇒ 다른 스레드가 값변경을 한 경우이다. (재시도해야 한다!)
    
    ![image](https://user-images.githubusercontent.com/77563814/188278732-a9e640e4-18e4-4632-aee8-1b65ab0529ef.png)

    
    ```cpp
    if ( *variable == expectedValue ) 
    { 
    	*variable = newValue;
    	return true;
    } 
    else 
    	return false;
    ```
    

즉, 변수에 값을 쓰기전에 다른 스레드가 중간에 변수값을 바꾸지 않았는 지 확인하는 과정이다. 이를 위해 내가 알고있는 값이 실제 메모리에 저장된 값이 맞나 체크해본다. 이 방식으로 Lock을 사용하지 않으면서 동기화 처리를 할 수 있다.

`synchronized`의 경우, 하나의 스레드가 공유자원을 사용하면 다른 스레드들은 Blocking 되었다(Busy Waiting). `CAS 알고리즘`은 하나의 스레드가 공유자원에 접근할 때, 다른 스레드를 Blocking 하지 않기 때문에 효율적이다. 

## Atomic 클래스

이러한 CAS 알고리즘은 Atomic 클래스에 사용된다. Atomic 클래스는 기본형 int, boolean 등을 동기화 처리한 자료형이다. 예를 들어 AtomicBoolean, AtomicInteger, Atomiclong, AtomicIntegerArray, AtomicIongArray 등에 사용된다.

단, CAS 알고리즘에는 ABA 문제가 발생할 수 있다. 이는 다음장에 더 자세히 정리해보았다.

## 정리하면

- `synchronized`를 사용하는 Blocking 알고리즘은 성능이 저하된다. Non-Blocking 방식이 보다 더 효율적이다. (단, 동기화 상황에 따라 `synchronized`의 Blocking 방식이 필요할 수 있다.)
- Non-Blocking은 일반적으로 Lock-Free 수준으로 사용된다.
- Lock-Free 알고리즘에는 Atomic 연산이 있고, 이는 대표적으로 CAS 알고리즘으로 구현된다.
- CAS 알고리즘에는 ABA 문제가 발생하는 데, 이는 다음장에서..!

**Reference**

- [https://narakit.tistory.com/194](https://narakit.tistory.com/194)
- [https://effectivesquid.tistory.com/62](https://effectivesquid.tistory.com/62)
- [https://chfhrqnfrhc.tistory.com/entry/Lockfree-VS-Waitfree](https://chfhrqnfrhc.tistory.com/entry/Lockfree-VS-Waitfree)
- [https://effectivesquid.tistory.com/62](https://effectivesquid.tistory.com/62)
- [https://bluekms21.gitbooks.io/femp/content/06_Non-BlockingAlgorithm.html](https://bluekms21.gitbooks.io/femp/content/06_Non-BlockingAlgorithm.html)
- [https://ozt88.tistory.com/38](https://ozt88.tistory.com/38)
- [https://parkcheolu.tistory.com/33](https://parkcheolu.tistory.com/33)


[위로가기⬆](#자바의-동기화-3---lock-free와-atomiccas)
