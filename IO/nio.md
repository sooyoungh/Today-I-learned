# I/O 대신 NIO?

**목차**
- [NIO 동작 과정](#nio-동작-과정)
- [1. NIO는 채널/버퍼 기반이다.](#1-nio는-채널버퍼-기반이다)
- [2. NIO는 비동기, Non-blocking이 가능하다.](#2-nio는-비동기-non-blocking이-가능하다)
- [NIO에서 개선한 점](#nio에서-개선한-점)
- [그럼 항상 NIO가 더 좋을까?](#그럼-항상-nio가-더-좋을까)
- [정리하면](#정리하면)


> IO는 스트림 기반, 동기/블로킹 방식만 지원한다. 때문에 속도가 느리고 입력/출력스트림을 따로 만들어주어야 하는 등의 단점이 있다. 이를 개선하기 위해 등장한 NIO를 알아보자.
> 그리고 각각 어떤 상황에 사용하면 좋을 지도 알아보자❕
> 

## NIO 동작 과정

여기서는 간단하게 어떤 방식으로 동작하는 지만 살펴보았다. (자세한 건 다음 포스팅..)

![image](https://user-images.githubusercontent.com/77563814/186948835-3a4bab52-1b3d-4311-a412-dbd9039c42b7.png)


일단 NIO는 **Selector**/**Channel**/**Buffer**를 사용하여 입출력 처리를 한다. 각각의 역할은 다음과 같다.

- **Channel** : 데이터를 읽고 쓰는 통로이다. IO의 `stream`역할을 하는 NIO의 입출력 클래스이다. 
  - 채널은 항상 버퍼에서부터 읽거나 버퍼로 쓴다.
- **Buffer** : 입출력 데이터를 임시 저장하는 클래스이다.
- **Selector** : 한 스레드에서 여러 채널을 모니터링하는 객체(멀티플렉서). 여러 채널에서의 이벤트들을 모니터링한다.

NIO는 Selector/Channel/Buffer를 사용하여 I/O의 단점을 개선하였다. 자세히 알아보자.

## 1. NIO는 채널/버퍼 기반이다.

NIO는 채널/버퍼로 I/O 스트림의 단점을 극복하였다.

- `Channel`로 양방향 IO 통신을 할 수 있다. 
    
    즉, 따로 입력/출력 스트림을 만들어주는 IO와 다르게 하나의 채널로 입출력이 가능하다.
- `Buffer`에 입출력 데이터를 임시 저장한다. 
    
    `Channel`은 읽은 데이터를 무조건 내부의 `Buffer`에 저장한다. 스트림과 다르게 `Buffer`에서 필요한 부분만 읽고 사용할 수 있다.
- `Direct Buffer`를 사용할 경우 더 빠르다. 
    
    I/O의 스트림에도 버퍼가 있다. 하지만 스트림의 버퍼는 JVM 버퍼이다. 커널 버퍼에 직접 접근하지 않고 JVM의 버퍼를 거치므로 속도가 느렸다. 하지만 NIO의 `Direct Buffer`는 커널 버퍼에 직접 접근이 가능하기 때문에 더 빠르다.


> **Direct Buffer VS Non-Direct Buffer**
> 
> I/O에서도 버퍼를 사용할 수 있는데, 동작 과정은 다음과 같다. [이미지 출처](https://howtodoinjava.com/java/io/how-java-io-works-internally/)
> 
> ![image](https://user-images.githubusercontent.com/77563814/187010534-747a1ab2-49f1-4119-87dd-798fb1a50a1e.png)
> 
> 1. disk controller가 **disk로부터 데이터를 fetch해온다.**
> 2. disk controller는 읽어온 데이터를 kernel memory buffer에 write한다. (DMA=CPU 없이 메인메모리 접근)
> 3. (버퍼가 용량이 차고, read 요청이 오면) kernel은 buffer 데이터를 process의 buffer로 복사한다.
> 
> 즉, I/O의 버퍼 동작과정은 커널 메모리를 직접 접근하지 않고 JVM 버퍼에 복사하는 과정이 필요하다. 이 때문에 속도가 느려진다. NIO에서는 이를 개선하여 커널 메모리 버퍼를 사용한> 다. JVM 버퍼를 거치지 않고, 직접 DMA한다. 이를 **Direct Buffer**라 한다.
> 
> Direct Buffer는 C 함수를 호출해야하는 등 버퍼 생성 시간은 오래 걸린다(대용량의 데이터를 주고 받을 경우 불리). 그러나 입출력이 빠르고 버퍼의 크기가 크다. 따라서 한번 버퍼 생성 후 재사용하기 적합하다. Non-Direct  Buffer는 JVM의 메모리를 사용하므로 버퍼의 크기가 크지 않고 버퍼 생성이 빠르다. 그러나 입출력 속도가 느리다. 
> 
> 
> 

## 2. NIO는 비동기, **Non-blocking이 가능하다.**

- `Selector`를 통해 한 스레드에서 **Non-blocking**으로 여러 채널을 관리한다.
    
    셀렉터는 여러 채널에서의 이벤트를 모니터링한다.  입출력 작업이 준비가 되면 채널만 선택해서 처리하기 때문에 입출력 작업시 스레드가 멈추지 않는다. ⇒ 앞선 포스트에서 비동기/논블로킹에 대해 알아보았다.
    
    - 단, 파일 I/O는 Blocking 방식만 가능하다. 네트워킹의 경우 논블로킹을 사용하여 성능 향상한다.

## **NIO에서 개선한 점**

I/O의 단점을 개선한 점을 정리하면 다음과 같다.

1. IO는 스트림 기반이다. → **NIO는 채널/버퍼 기반이다.**
    - 입출력 스트림을 따로 만들어주지 않아도 된다.
    - 캐시되어 있어 데이터 앞뒤로 이동 가능하여 원하는 위치만 읽을 수 있다.
    - 커널 버퍼를 사용할 수 있어 빠르다.
2. 동기, **Blocking** 방식만 지원한다 → **NIO는 비동기, Non-blocking도 가능하다.**
    - 스레드가 채널을 통해 요청하고, 스레드가 블로킹 되지는 않는다. Selector로 여러 채널을 모니터링하기에 가능하다.

## 그럼 항상 NIO가 더 좋을까?

[이는 비동기/논블로킹 방식이 항상 좋은 가? 와 같은 질문이다.](https://github.com/sooyoungh/Today-I-learned/blob/main/IO/blocking_synchronous.md#%ED%95%AD%EC%83%81-%EB%B9%84%EB%8F%99%EA%B8%B0--%EB%85%BC%EB%B8%94%EB%A1%9C%ED%82%B9%EC%9D%B4-%EC%A2%8B%EC%9D%84%EA%B9%8C) 웹서비스처럼 불특정 다수의 클라이언트가 사용하는 경우라면 유리하다. 여러 스레드를 생성하지 않고 싱글 스레드로 처리하기에 효율적이기 때문이다.

그러나 NIO는 모든 연결 시 버퍼를 할당해야하고, 버퍼 할당 시 오버헤드가 크다(입출력은 빠르지만, 버퍼 생성 시간이 오래 걸린다). 반면 IO는 버퍼를 사용하지 않아도 된다. 또한 동기적으로 처리해야 하는 작업은 차라리 스레드의 수를 늘리는 방식이 유리하다. 

따라서 상황에 맞게 사용하자

<aside>
  
    🌟 클라이언트 수가 많고 + I/O 작업이 길지 않고 + 비동기적인 작업 ⇒ NIO

       클라이언트 수가 적고 + 데이터가 대용량 + 동기적인 작업 ⇒ I/O

</aside>

## 정리하면

> I/O의 단점들을 개선한 NIO가 등장하였다. 스트림의 단점을 보완한 방식으로, Selector/Channel/Buffer를 기반으로 입출력 처리를 한다. 속도/비동기/Non-blocking 등의 장점을 가져서 I/O보다 우수하다. 그러나 NIO의 버퍼 생성 과정의 오버헤드가 더 크므로 항상 유리하지는 않다. 오히려 IO로 여러 스레드를 만들어 처리하는 게 유리할 수 있다.
> 

**Reference**

- [https://junghyungil.tistory.com/131](https://junghyungil.tistory.com/131)
- [https://jiwondev.tistory.com/262](https://jiwondev.tistory.com/262)
- [https://dev-coco.tistory.com/42](https://dev-coco.tistory.com/42)


[위로가기⬆](#io-대신-nio)

