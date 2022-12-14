# 어플리케이션 계층 3 - 쿠키와 세션

## 쿠키와 세션을 사용하는 이유?

> HTTP 프로토콜은 비연결성(Connectionless)이다. 따라서 한번 요청-응답 후 연결이 끊긴다. 또한 HTTP 는 상태를 유지/저장하지 않기(Stateless) 때문에, 클라이언트의 이전 상태를 서버측에서 저장하지 않는다. 따라서 서버는 클라이언트의 정보/상태를 매번 확인해야 한다. 이를 보완하기 위해 쿠키와 세션을 활용한다.

## 쿠키란

```
🍪 웹 브라우저가 보관하는 데이터
```

* **구성 요소**
  * 이름 : 쿠키를 식별하는 이름으로, 아스키 문자로 구성
  * 값 : 쿠키의 값
  * 유효시간 : 쿠키 유지 시간으로, 설정 안하면 브라우저 종료시 자동으로 삭제된다.
  * 도메인 : 쿠키를 전송할 도메인 (해당 쿠키를 생성한 서버에만 전송됨, 다른 사이트로 연결될 때는 해당 쿠키 전송X)
  * 경로 : 쿠키를 전송할 요청 경로
* Set-Cookie 헤더에 저장되어 전달된다.
* 쿠키는 웹 브라우저에 저장되므로 외부에서 값을 탈취/변경할 수 있다. → 보안에 취약
* 기본적으로 쿠키는 그 쿠키를 생성한 서버에만 전송되고, 다른 사이트에 연결할 때는 전송되지 않는다. → 필요시(어떤 도메인의 모든 서버에 저장할 경우 등) `setDomain`으로 설정
* 클라이언트 당 300개까지, 하나의 도메인 당 20개의 쿠키를 가질 수 있다. 또한 하나의 쿠키의 용량은 4KB까지 가능하다.

## 쿠키 동작 방식

![슬라이드3](https://user-images.githubusercontent.com/77563814/189523158-aee5e18f-71ea-487f-8c9a-d76b69dac8a2.PNG)

1. 쿠키 생성 : 웹 서버에서 쿠키 생성 후 헤더를 통해 웹 브라우저에 전송
2. 쿠키 저장 : 웹 브라우저는 받은 쿠키를 쿠키 저장소에 보관
3. 쿠키 전송 : 요청 시마다 쿠키도 함께 전송

***

## 세션이란

```
📨 서버측에 저장하는 클라이언트의 상태 정보
```

* 웹 브라우저마다 하나의 세션을 생성한다.. 즉, 웹 브라우저마다 별도의 세션을 가짐 (같은 JSP 페이지라도 브라우저별로 다른 세션 사용!)
* 클라이언트를 구분하기 위해 세션 ID를 발급한다.
* 세션은 서버에 저장되므로 쿠키보다 보안에 우수하다.
* 세션은 서버당 생성되는 데이터이므로, 여러 서버에서 공유하지 못한다. 반면 쿠키는 도메인이 공유 가능하다.

> 세션 ID
>
> * 웹 브라우저마다 별도의 세션을 가진다.
> * 웹 브라우저는 서버에 연결할 때마다 세션 ID를 전송한다. 이때 쿠키에 담아서 전송한다.
> * 서버는 세션 ID를 통해 해당 클라이언트의 세션을 찾는다.

## 세션 동작 방식

![슬라이드4](https://user-images.githubusercontent.com/77563814/189523147-ea93ca69-1d12-42bf-a3b8-f2ee03e64839.PNG)

웹 브라우저가 첫 접속하면 서버에서 생성 후, 그 세션을 계속 사용한다.

1. **최초 요청** : 서버에 최초 접속 시, 서버는 세션 ID를 발급하고 쿠키에 담아서 전송한다.
2. **이후 요청** : 클라이언트가 서버에 요청할 때, 세션 ID를 쿠키에 담아서 보낸다. 서버는 세션 ID를 통해 해당 클라이언트의 세션을 찾는다. 필요한 처리 후에 서버가 다시 클라이언트에게 응답을 한다.

## 쿠키와 세션의 차이

* **저장 공간** : 쿠키는 클라이언트 측에, 세션은 서버측에 저장한다.
* **보안** : 브라우저에 저장되는 쿠키는 외부에서 탈취/변조의 위험이 있어 보안상으로 위험하다. 반면 세션은 서버에만 저장되어 안전하다.

## 정리하면

> HTTP 프로토콜의 Connectionless와 Stateless하다. 따라서 클라이언트가 서버에 요청을 할 때마다 모든 상태/정보를 보내야한다. 이러한 점을 보완하기 위해 쿠키와 세션을 사용한다. 쿠키는 웹 브라우저, 세션은 서버 측에 데이터를 저장한다. 따라서 쿠키는 보안상 취약하고 세션은 비교적 안전하다.
