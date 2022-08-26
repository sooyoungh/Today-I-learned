# Java I/O 알아보기

**목차**
1. [I/O 알아보기](#io-알아보기)
2. [스트림이란](#스트림이란)
3. [Java I/O의 단점](#java-io의-단점)


> Java의 I/O는 스트림 기반으로 입출력을 처리한다. 스트림은 몇가지 단점을 가지고 있는데, 이를 알아보았다.
> 

## I/O 알아보기

![image](https://user-images.githubusercontent.com/77563814/186914320-844e9800-62d7-4ce0-8f75-37a1f8db1080.png)


[이미지 출처](https://www.scientecheasy.com/2021/05/stream-classes-in-java.html/)

## 스트림이란

Java I/O는 입출력을 스트림 단위로 한다. 스트림은 Byte 형태로 데이터를 운반하는 통로를 뜻한다. 스트림은 단방향이라 입력/출력 스트림을 따로 만들어주어야한다. 또한 먼저 보낸 데이터를 먼저 수신하는 FIFO 구조(큐)로 되어 있다.

이때 Byte 혹은 Char 단위로 두가지 스트림 방식이 있다. 

- ****Byte 단위 (****1byte씩 읽는다****)****
    - Input/OutputStream가 최상위 부모클래스
    - ByteArrayInput/OutputStream : byte 배열 입출력
    - FileInput/OutputStream : 파일 데이터 입출력
- ****Character 단위 (2****byte씩 읽는다)
    - Reader/Writer가 최상위 부모클래스
    - FileReader/Writer : 문자 기반 파일 입출력

또한 실제 데이터를 주고 받는 스트림은 아니지만, 스트림 기능 향상/추가를 위해 사용하는 보조스트림이 있다. 

스트림 생성 후에 보조 스트림을 생성한다.

- **보조 스트림**
    - FilterInput/OutputStream : 필터를 이용한 입출력 처리
        - 인풋 데이터를 Buffer에 임시로 담아두고, 한꺼번에 Buffer에 있는 값을 전달한다.
        - 버퍼가 없다면❓ 사용자가 입력하는 순간마다 전달되어 전달되는 횟수가 많다. 즉, 프로그램의 성능 저하를 유발한다.
        - 단, 이때의 버퍼는 커널 버퍼가 아니라 JVM의 버퍼이다 -> 한번 거쳐가기에 속도 느리다.
    - BufferedInput/OutputStream : 버퍼를 이용한 입출력 성능향상
    - DataInput/OutputStream : int, float와 같은 기본형 단위로 데이터를 처리

## Java I/O의 단점

Java I/O는 스트림 기반으로 이루어진다. 단, 스트림은 몇가지 단점을 가지고 있다.

1. 스트림은 한방향으로 흐른다.
    
    한방향으로 흐르는 통로이기에, 캐시되지 않아 데이터를 앞뒤로 오가면서 원하는 위치에서 읽을 수 없다. 데이터를 시작부터 끝(EOF)까지 순차적으로 읽으며, 한 번 읽고 나면 되돌아갈 수 없다.
    
2. I/O는 느리다.
    
    스트림은 **동기적/Blocking** 방식으로 동작한다. 즉, 스레드가 I/O요청을 받으면 I/O 작업이 완료될 때까지 대기해야 한다. 그리고 Java I/O의 버퍼는 C/C++과 다르게 커널 버퍼에 직접 접근할 수 없다. JVM 내부 버퍼로 복사 후 사용한다. 이 과정에서 속도가 느려진다. ⇒ [자세히](https://ohjongsung.io/2019/09/07/java-nio-%ED%86%BA%EC%95%84%EB%B3%B4%EA%B8%B0)
    

I/O의 이러한 몇가지 단점들을 극복하기 위해 JDK 1.4부터는 NIO가 추가되었다. 

다음 장에서 더 자세하게 다루겠지만, NIO가 IO와 다른 점을 간단하게 보면 다음과 같다.

|  | IO | NIO |
| --- | --- | --- |
| 입출력 방식 | Stream | Buffer |
| 버퍼 방식 | Non-Buffer (커널 버퍼X) | Buffer |
| Blocking/Non-blocking | Blocking IO만 지원 | Non-blocking IO/ Blocking IO |
| 동기/비동기 | 동기만 지원 | 동기/비동기 |


Reference

- [https://terianp.tistory.com/19](https://terianp.tistory.com/19)
- [https://victorydntmd.tistory.com/134](https://victorydntmd.tistory.com/134)
- [https://developer-yeony.tistory.com/m/146](https://developer-yeony.tistory.com/m/146)
- [https://velog.io/@jihoson94/BIO-vs-NIO](https://velog.io/@jihoson94/BIO-vs-NIO)
- [https://youngjinmo.github.io/2019/12/java-buffer-io/](https://youngjinmo.github.io/2019/12/java-buffer-io/)
- [https://ohjongsung.io/2019/09/07/java-nio-톺아보기](https://ohjongsung.io/2019/09/07/java-nio-%ED%86%BA%EC%95%84%EB%B3%B4%EA%B8%B0)


[위로가기⬆](#java-io-알아보기)

