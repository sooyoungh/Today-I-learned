# Thread-Safe 클래스는 필요할 경우에만 쓰자

**목차**

* [Thread-Safe 클래스는 필요할 경우에만 쓰자](collections\_warning.md#thread-safe-클래스는-필요할-경우에만-쓰자)

![image](https://user-images.githubusercontent.com/77563814/186737043-96e19b29-fb04-40d1-bad2-8891cbfe079e.png)

## Thread-Safe 클래스는 필요할 경우에만 쓰자

Collection 자료구조들 중에서 Thread-Safe한 클래스들이 있다. 멀티 스레드 환경에서 동기화를 보장하는 클래스들이다. 예를 들면 `Vector, Stack, Hashtable`등.

> Q : Thread-Safe한 클래스들은 항상 유리할까?
>
> A : 성능이 떨어지므로 단일 스레드 환경에서는 지양해야 한다.

Thread-Safe하다는 의미는 동기화(Synchronization)처리를 했다는 뜻이다. 어떤 스레드가 Critical Section에서 작업 중이라면, 다른 스레드는 해당 영역에 접근하지 못한다. 그러므로 Thread-Safe는 성능면에서 유리하지 않다.

특히 단일 스레드 환경이라면 Thread-Safe 클래스들은 지양해야 한다. 예를 들어, 메소드 내부의 지역 변수는 공유 자원(Critical Section)이 아니다. 스레드 자신만의 고유한 변수이므로 다른 스레드들과 공유할 필요가 없다. 따라서 메소드 안에서는 Thread-Safe하지 않은 변수, 자료구조를 사용해야 한다.

따라서 싱글/멀티 스레드인지에 따라 적절한 자료구조를 선택해주어야 한다.

|           | Thread-Safe (멀티 스레드 환경일 경우) | NOT Thread-Safe (싱글 스레드 환경일 경우) |
| --------- | --------------------------- | ------------------------------- |
| 배열 자료구조   | Vector                      | ArrayList / LinkedList          |
| LIFO 자료구조 | Stack                       | Deque                           |
| 해시 자료구조   | Hashtable                   | HashMap                         |
| 문자열       | String                      | StringBuffer / StringBuilder    |
