# 어플리케이션 계층 1 - HTTP 알아보기

**목차**

1. [HTTP 특징](http.md#http-특징)
2. [HTTP 동작과정](http.md#http-동작과정)
3. [TCP 소켓 통신 vs HTTP 통신](http.md#tcp-소켓-통신-vs-http-통신)
   * [TCP는 연결지향(connection-oriented), HTTP는 비연결성(connectionless)?](http.md#tcp는-연결지향connection-oriented-http는-비연결성connectionless)
4. [HTTP의 보안](http.md#http의-보안)
5. [정리하면](http.md#정리하면)

> HTTP는 인터넷에서 클라이언트와 서버가 데이터를 송수신할 때 사용하는 프로토콜이다. HTTP의 몇가지 특징과 동작 과정을 정리해보았다. 특히 개인적으로 헷갈렸던 TCP 소켓과 HTTP의 연결성에 대해 살펴보았다. 그리고 HTTP의 보안 문제에 대해서도 짚고 넘어가보았다.

## HTTP 특징

HTTP는 인터넷에서 데이터를 주고 받는 프로토콜로, 몇가지 특징을 정리해보았다.

1. **클라이언트 - 서버 구조**
   * 클라이언트가 요청하면 서버가 응답하는 단방향 통신이다.
   * Request/Response 구조
2. **Connectionless** (비연결성)
   * 클라이언트와 서버의 연결을 유지하지 않는다.
   * 클라이언트가 요청하면 서버는 응답을 하고 맺었던 연결을 끊는다.
   * 트래픽이 많지 않고, 빠른 응답을 제공할 수 있는 경우에 효율적이다.
   * 그러나, 연결 성립/해제 과정이 더 비쌀 수도 있다.
   * Keep Alive 옵션으로 일정 기간 연결을 유지할 수 있다.
3. **Stateless**
   * 서버가 클라이언트의 상태를 저장하지 않는다.
   * 장점 : 서버에 이전 내용이 저장되어 있는 게 아니라 많은 서버를 운영해도 된다.
   * 단점 : 요청 한번에 필요한 모든 데이터를 보내기에 비효율적이다.
   * 쿠키/세션으로 Stateful하게 사용할 수 도 있다.

## HTTP 동작과정

HTTP는 어플리케이션 계층이고, TCP는 전송 계층이다. HTTP는 TCP 기반으로 동작한다. 따라서 연결/해제하는 과정에서 TCP 3/4-way handshake를 한다. TCP 3-way handshake으로 연결을 성립하면, HTTP 메세지를 주고 받고 다시 TCP 4-way handshake으로 연결을 해제한다. HTTP의 동작과정은 다음과 같다.

![image](https://user-images.githubusercontent.com/77563814/189072074-938d083c-c0d3-4d39-bab7-429f95d97fd3.png)

1. **사용자가 웹 브라우저에 URL 주소 입력**
2. **DNS 서버에 웹 서버의 호스트 이름을 IP 주소로 변경 요청**
3. **웹 서버와 TCP 연결 시도**
   * **3 way-handshaking**
4. **클라이언트가 서버에게 요청**
   * HTTP Request Message
5. **서버가 클라이언트에게 데이터 응답**
   * HTTP Response Message
6. **서버 클라이언트 간 연결 종료**
   * 4way-handshaking
7. **웹 브라우저가 웹 화면 출력**

## TCP 소켓 통신 vs HTTP 통신

TCP 소켓과 HTTP 각각 어떻게 동작하는지 정리해보았다.

우선 **TCP 소켓 통신**은 OSI 계층으로 4계층, 전송 계층 프로토콜로 데이터를 주고 받는다. 즉, 4계층에서 패킷을 생성한다. 클라이언트 혹은 서버가 `close`를 요청하기 전까지 서로 데이터를 계속 주고 받을 수 있다.

반면 **HTTP 통신 시,** HTTP 프로토콜은 7계층의 프로토콜로, TCP 계층(4계층)위에서 동작한다. 즉, HTTP는 TCP보다 상위 계층에서 동작한다. 따라서 TCP의 연결 과정(3-way handshake)을 거친다. 이 연결 과정을 거친 뒤, HTTP 프로토콜에 따라 클라이언트와 서버가 데이터를 송수신(`Request`/`Response`)한다. OSI 계층으로 7계층, 어플리케이션 계층에서부터 패킷이 생성된다.

이 내용과 함께 헷갈렸던 부분이 TCP는 연결지향 프로토콜인데, HTTP는 비연결성이라는 점이었다.

### TCP는 연결지향(connection-oriented), HTTP는 비연결성(connectionless)?

위에서 HTTP는 TCP 계층 위에서 동작한다고 했다. 그런데 TCP는 연결지향인데, HTTP는 비연결적이라는 게 어떤 의미일까?

> * **TCP의 연결지향**이란
>   * UDP와 대비되는 개념이다(UDP는 서버와 연결 성립을 하지 않고, 그냥 클라이언트가 전송함).
>   * 클라이언트와 서버의 연결을 성립 후 데이터를 송수신한다는 의미이다.
> * **HTTP의 비연결성**이란
>   * 소켓과 대비되는 개념이다(소켓은 `close`를 요청 전까지 클라이언트/서버가 데이터를 송수신함).
>   * 클라이언트와 서버가 데이터를 한번 송수신(`Request`/`Response`)하고 나면 연결을 해제한다.

## HTTP의 보안

HTTP는 암호화가 되지 않은 데이터를 주고 받는 프로토콜이다. 따라서 클라이언트와 서버의 통신 과정 중에 다른 누군가에 의해 도청/변조될 수 있다. 이를 방지하기 위해 `SSL` 을 통해 보안기능을 추가한 프로토콜이 HTTPS이다. HTTPS와 SSL은 다음 장에서 자세히 정리해보았다.

## 정리하면

* HTTP의 특징과 동작 과정에 대해 알아보았다.
* HTTP는 TCP 위에서 동작하는 프로토콜이다. 따라서 TCP의 3-way handshake 과정으로 연결을 먼저 성립하고, HTTP 프로토콜에 따라 데이터를 송수신한다. 이때 HTTP는 클라이언트와 서버가 데이터를 한번 송수신하면 연결을 끊는 비연결적인 프로토콜이다.
* 단, HTTP 자체는 보안 기능이 없기에, 도청/변조될 수 있다. 따라서 SSL을 통해 보안기능이 있는 HTTPS가 등장하였다.

**Reference**

* [https://hanamon.kr/네트워크-http-http란-특징-무상태-비연결성/](https://hanamon.kr/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-http-http%EB%9E%80-%ED%8A%B9%EC%A7%95-%EB%AC%B4%EC%83%81%ED%83%9C-%EB%B9%84%EC%97%B0%EA%B2%B0%EC%84%B1/)
* [https://hwan-shell.tistory.com/271](https://hwan-shell.tistory.com/271)

[위로가기⬆](http.md#http-알아보기)
