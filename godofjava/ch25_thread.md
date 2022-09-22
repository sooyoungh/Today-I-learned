# ch25 : Thread

**목차**
1. [멀티 스레드를 사용하는 이유](#멀티-스레드를-사용하는-이유)
2. [스레드 실행 방식](#스레드-실행-방식)
3. [데몬 스레드](#데몬-스레드)


## 멀티 스레드를 사용하는 이유

<aside>
  
    ✏️ 스레드는 프로세스와 다르게, (같은 프로세스의) 스레드들끼리는 메모리를 공유하기에 공간 효율적이고 Context-Swithing이 빠르다.

</aside>

실행 방법 - 메인 함수에 여러 스레드를 시작시켜주면 된다.

## Runnable 인터페이스와 Thread 클래스

> **스레드를 생성하는 방법에는 두 가지가 있다.**
> 
> 1. `Runnable` 인터페이스
> 2. `Thread` 클래스 (`Runnable` 인터페이스를 상속받은 클래스. 즉, 구현함)
> 
> (둘다 java.lang에 있으므로 import 안해도 된다.)
> 

- `Runnable` 인터페이스에는 `void run()` 만 선언되어 있다. 이 메소드는 스레드가 시작되면 수행되는 메소드이다. 
    → `Thread` 클래스는 해당 메소드만 구현해주면 된다.

- `Thread` 클래스를 확장한 경우가 사용이 편리하다. 

- 그럼 `Runnable` 인터페이스**는 언제 사용할까?**
    클래스는 다중 상속이 불가능하다. 내가 만드는 클래스가 `Thread` 클래스 외에도 다른 클래스를 상속받아야 하는 경우에는 
    `Runnable` 인터페이스을 상속받으면 된다.

## 스레드 실행 방식

- 스레드가 수행되는 메소드는 `run()`이다. (그리고 이 메소드가 종료될 때 스레드가 종료된다.)
- 스레드를 시작하는 메소드는  `start()`이다.
- 두 가지 방식은 스레드를 실행하는 방식이 다르다.
- `Runnable` 인터페이스를 구현한 클래스의 예시
    
    ```java
    public class Test implements Runnable {
    
       public void run() {
    	   // 구현
       }
    }
    
    // Runnable 실행
    RunnableSample runnable = new RunnableSample();
    new Therad(runnable).start(); 
    ```
    
- `Thread` 클래스를 확장한 클래스의 예시
    
    ```java
    public class Test extends Thread {
    
     @Override
     public void run() {
    	 // 구현
     }
    }
    
    // Thread 실행
    ThreadSample thread = new ThreadSample();
    thread.start();  
    ```
    

**스레드의 실행**

- `start()`를 호출하면 `run()` 메서드 내용이 끝나든 말든 쓰레드를 시작한 메서드에서 그 다음 줄이 실행된다.
- 쓰레드 시작할 때 100 숫자를 넘겨주고 싶다면? -> 쓰레드를 상속받은 생성자에 매개변수로 받으면 된다.

**스레드의 종료**

- `sleep()`
    - 매개변수로 넘어온 시간만큼 대기한다.
    - 사용할 땐 항상 try-catch로 묶어준다. `InterrptedException` 혹은 상위 예외 클래스로 예외처리 해주어야 한다.

## 데몬 스레드

- 일반 스레드는 JVM이 종료할 수 없으나, 데몬 스레드는 JVM이 끝낼 수 있다.
- 스레드 실행(`start()`)전에 데몬스레드로 지정되어야 한다.
    - `스레드객체.setDaemon(true)`; // 데몬스레드 지정
- 데몬 스레드는 해당 스레드가 종료되지 않아도, 다른 실행중인 스레드가 없다면 종료된다.
- ex) 부가적인 작업을 데몬스레드로 만든다.
    - 모니터링 스레드를 데몬스레드로 만들고, 일반 스레드가 종료되면 자동으로 모니터링 스레드도 종료시켜 프로세스를 끝내는 경우
