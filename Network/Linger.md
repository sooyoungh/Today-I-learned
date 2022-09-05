# 4. TCP LINGER 옵션

**목차**
1. [TCP LINGER 옵션 사용하는 이유? - TIME_WAIT](#tcp-linger-옵션-사용하는-이유?---time_wait)
    - [TIME_WAIT이 클라이언트말고 서버측에 남는 다면](#time_wait이-클라이언트말고-서버측에-남는-다면)
2. [TCP LINGER 옵션](#tcp-linger-옵션)
3. [정리하면](#정리하면)


> 이전에 TCP의 연결 해제 요청 과정인 TCP 4-way Handshake에 대해 알아보았다. 그중에 `TIME_WAIT`은 클라이언트가 서버측 잉여 패킷을 기다리기 위한 상태이다. 만약 서버측에서  `TIME_WAIT`이 발생하면 문제가 생길 수 있다. 이에 대해 알아보고, 이를 해결하기 위한 TCP LINGER 옵션에 대해 정리해보았다.
> 


## TCP LINGER 옵션 사용하는 이유? - TIME_WAIT

우선 `TIME_WAIT` 기간 동안 왜 기다리는 지 다시 정리해보았다.

![image](https://user-images.githubusercontent.com/77563814/188499613-aace553f-096f-4d61-9f0a-5de53f3a8a0d.png)

<aside>
  
    ❔ 만약 서버 측에서 FIN 패킷을 보냈는데, 클라이언트가 바로 ACK를 보내고 종료하면 어떻게 될까?

</aside>

서버 측에서 FIN 패킷을 보냈지만, 이전에 클라이언트에게 보낸 패킷이 아직 도착하지 못한 상태일 수 있다. 이 패킷들을 클라이언트에게 모두 전송 후 종료하기 위해서 클라이언트는 서버의 FIN을 받고 일정 시간동안 기다린다(TIME_WAIT).

> TIME_WAIT이란?
> 
>     클라이언트는 서버로부터 FIN 패킷을 받으면 바로 종료하지 않는다. 
>     일정시간(Default: 240s)동안 잉여 패킷을 기다린다. 이때를 TIME_WAIT이라 한다.
> 

### TIME_WAIT의 문제 - 클라이언트말고 서버측에 남는다면

이렇게 TIME_WAIT의 시간동안 대기하기 때문에, 서버가 클라이언트에 보낼 패킷들을 모두 정상 전송한 뒤 연결 종료하게 된다. 위의 상황은 클라이언트측에서 먼저 종료를 하는 상황이다. 이를 클라이언트가 `active close`(먼저 종료 FIN 패킷 보내기)한다고 한다.

하지만, 이처럼 정상적으로 클라이언트가 `active close`하여 종료되지 않는 경우도 있다. 예를 들어, 네트워크 연결 단절 등의 이유로 비정상 종료될 수 도 있고, 혹은 서버측에서 먼저 `active close`하기도 한다. 웹서비스는 여러 클라이언트의 요청을 받기 때문에 필요에 따라 `active close` 을 서버측에서 하기도 한다.

이렇게 서버 측에서 `active close`할 경우, 서버쪽에 TIME_WAIT이 발생하는데 이는 문제를 일으킬 수 있다. 서버측이 먼저 연결을 종료할때 마다 TIME_WAIT이 계속 발생하면, 부하가 발생하고 클라이언트의 연결을 받지 못할 수 있다.

이렇게 서버측이 먼저 `active close`하여 TIME_WAIT이 발생하여 문제를 일으키는 것을 방지하고자 TCP LINGER 옵션을 활용한다. TCP LINGER 옵션은 소켓이 `close`했을 때 남은 데이터를 버릴지/보낼지, 보낸다면 어느 정도로 기다릴지(TIME_WAIT) 정하는 옵션이다.

## TCP LINGER 옵션

<aside>
  
    🌟 소켓을 close 했을 때 전송되지 않은 데이터를 어떻게 처리할 지 정하는 옵션

        아직 전송하지 못한 데이터가 있다면 버릴지/보낼지, 보낸다면 얼마동안 기다릴지(TIME_WAIT) 정하는 옵션.

</aside>

서버측에서 데이터를 보낼 때 SendBuffer에 데이터를 담아 클라이언트에게 전송한다. 만약 클라이언트의 RenvBuffer가 꽉 차서 아직 서버측의 데이터를 받지 못하는 상황일 수 있다. → 이렇게 클라이언트가 받을 공간이 없을 떄는 ACK 패킷으로 확인한다(Sliding Window). 

그럼 서버의 SendBuffer에는 아직 전송하지 못한 데이터가 남아있게 된다. 그런데 이때 서버 소켓을 close()하게 되면 어떤 일이 발생할까? 이러한 상황에서 SendBuffer의 데이터를 어떻게 처리할 지 정하는 옵션이 **TCP LINGER 옵션**이다. 이 옵션으로 시간도 조정하고 데이터를 버릴지/아닐지도 정할 수 있다. 

아래는 C의 소켓 옵션에 사용되는 데이터 구조체이다. 총 3가지 case가 있다.

```java
// LINGER 구조체
struct linger
{
  int l_onoff;  // Linger 활성화 여부
  int l_linger; // TIME_WAIT 시간
}
```

1. `l_onoff == 0` 
    - Linger를 비활성화하는 정상종료 옵션. 서버 소켓의 SendBuffer에 남아있는 모든 데이터를 전송하고 정상 종료(Graceful Shutdown)가 이루어진다. LINGER 옵션을 설정해주지 않았을 때 기본값이다.
2. `l_onoff > 0` & `l_linger == 0` 
    - 바로 종료되고 서버 소켓 SendBuffer에 남아있는 데이터를 버린다. 비정상 종료된다(Abortive Shutdown).
3. `l_onoff > 0` & `l_linger > 0` 
    - 지정한 시간동안 대기하고 SendBuffer에 남아있는 데이터를 모두 전송한다. 지정된 시간동안 데이터를 모두 보냈다면 정상 종료(Graceful Shutdown)된다. 만약 지정 시간 내에 다 보내지 못하면 에러가 발생하며 비정상 종료(Abortive Shutdown)된다.

위에서 나온 정상 종료와 비정상 종료는 다음과 같다.

- **정상 종료(Graceful Shutdown)**
    - TCP 4-way Handshake 방식 그대로 TIME_WAIT를 기다린다.
    - 클라이언트는 TIME_WAIT동안 모든 데이터를 정상적으로 서버로부터 받고 종료한다.
- **비정상 종료(Abortive Shutdown)**
    - 서버 소켓 SendBuffer에 보내지 못한 데이터가 남아있는 상태에서 강제 종료하는 상황
    - SendBuffer에 있는 데이터가 전송되지 못하고 버려질 수 있다.

위의 3가지 case를 설정하여, 아래와 같이 옵션을 지정해줄 수 있다.

```java
// LINGER 구조체의 값 설정  
LINGER Linger;
Linger.l_onoff	= 1;
Linger.l_linger = 3; // 3초의 TIME_WAIT

// LINGER 옵션을 Socket에 적용  
setsockopt(mSocket, SOL_SOCKET, SO_LINGER, (char*) &Linger, sizeof(LINGER));   

// LINGER 옵션이 적용된 Socket을 close()한다.  
closesocket(mSocket);
```

이렇게 TCP LINGER 옵션을 활용하여, 데이터 전송 보장 여부와 대기 시간을 정할 수 있다.

## 정리하면

- TCP 4-way Handshake에서 클라이언트는 서버의 잉여 패킷을 기다리는  TIME_WAIT을 가진다.
- 만약, 서버측에서 TIME_WAIT을 가지면 서버 장애가 생길 수 있다.
- TCP LINGER 옵션으로 아직 전송하지 못한 데이터가 있다면 버릴지/보낼지, 보낸다면 얼마 동안 기다릴지(TIME_WAIT) 정할 수 있다.

**Reference**

- [https://linuxism.ustd.ip.or.kr/1397](https://linuxism.ustd.ip.or.kr/1397)
- [https://unsigned.tistory.com/16](https://unsigned.tistory.com/16)
- [https://sunyzero.tistory.com/269](https://sunyzero.tistory.com/269)
- [https://coderanch.com/t/206721/java/Ghost-socket](https://coderanch.com/t/206721/java/Ghost-socket)



[위로가기⬆](#4-tcp-linger-옵션)
