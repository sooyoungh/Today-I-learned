# Today-I-learned

```
🌟 CS, Java, Spring 공부하며 중요하거나 헷갈린 파트들 주제별로 묶어 정리한 글들.
```

## 🎪 Java

**자바의 JVM** 🚕

* JVM의 구조
* JVM의 GC 작동 방식
* [자바에 Call By Value만 있는 이유 (JVM의 GC)](java/call\_by\_value.md)
* [인코딩 - Switch문은 if문보다 빠른가?](java/switch\_encoding.md)
* [인코딩 - 문자열 인코딩 방식 알아보기](java/encoding.md)

**자바의 다형성** 🍇

* [자바의 다형성 간단 정리](java/undefined.md)
* [List<> = new ArrayList<> 쓰는 이유 (업캐스팅)](java/upcasting.md)
* Generic - 자세히 알아보기
* [Generic - 자바의 변성](java/undefined-1.md)
* 상속 조심🙅‍ 1 - 추상클래스 vs 인터페이스
* [상속 조심🙅‍ 2 - Prefer composition than inheritance](java/prefer\_composition\_than\_inheritance.md)

**예외 처리 주의** ❗

* [Checked Exception을 Unchecked Exception으로 throw하자](java/wrapping\_checked\_exception\_into\_unchecked\_exception.md)
* [try/catch/finally와 return문](java/try-catch-finally-\_return.md)
* [Try with resource로 자동 자원 해제하기](java/try\_with\_resource.md)
* Null 파헤치기
* [Optional 잘 쓰는 법1 - 언제 쓸까?](java/optional.md)
* Optional 잘 쓰는 법2 - orElse/orElseGet

**Java Collections 자세히** 🔎

* [Java Collections - List](java/collections\_list.md)
* [Java Collections - Hash](java/collections\_hash.md)
* [Thread-Safe 클래스는 필요할 경우에만 쓰자](java/collections\_warning.md)
* [equals()과 hashcode()는 함께 오버라이딩해주자](java/equals.md)

**Java와 I/O** 💿

1. [Blocking/Non-Blocking, Sync/Async](io/blocking\_synchronous.md)
2. [Java I/O 알아보기](io-1/java\_io.md)
3. [I/O 대신 NIO?](io-2/nio.md)
4. NIO2의 파일
5. [자바의 직렬화(Serialize)](java/undefined-2.md)

**Java와 동기화** 💭

1. [자바의 동기화 1 - volatile](java/volatile.md)
2. [자바의 동기화 2 - synchronized](java/synchronized.md)
3. [자바의 동기화 3 - Lock-Free와 Atomic(CAS)](java/atomic.md)
4. [자바의 동기화 4 - ABA 문제](java/aba.md)

**Java와 시간** 🕜

* [Date, Calendar 쓰지 말자](java/date\_calendar\_.md)
* [java.time 패키지 살펴보기](java/java.time.md)

**Java와 Web** 🕹

* [자바와 웹프로그래밍 - JSP/서블릿/MVC](java/web.md)
* [자바와 DB - JDBC/DB pool](java/db.md)

## 🔮 객체 지향

**Java와 객체 지향** 🎲

* [객체 지향 4가지 특징](java/oop.md)
* [객체 지향 SOLID 5원칙](java/solid.md)

**Java Spring 디자인 패턴** 🖼

* [Singleton 패턴](oop-2/singleton.md)
* [Adapter 패턴](oop/adpater.md)
* [Decorator 패턴](oop-1/decorator.md)

## 🌺 Spring

* Spring의 IoC/DI
* Web Architecture (MVC, Servlet, Spring MVC, Spring Boot)
* Filter / Intercepter / AOP

## 🌎 Network

1. TCP와 UDP 알아보기
2. TCP의 연결과 해제 (3/4-way-handshake)
3. [TCP LINGER 옵션](network-1/linger.md)
4. [TCP/IP 소켓 프로그래밍 알아보기](java/socket\_programming.md)
5. TCP 다중 클라이언트 통신(스레드)

* [HTTP 알아보기](network/http.md)
* [HTTPS와 SSL](network-2/ssl.md)
* [쿠키와 세션](network-3/cookie\_session.md)

## 💌 Database

* [JDBC - Class.forName() 동작 방식](database/classforname.md)

## 💻 OS

* [프로세스와 스레드](os/proc\_thread.md)
* [교착상태 알아보기](os-1/\_.md)

## 📕 Book

* [자바의 신 (GodOfJava) 정리](godofjava-13/undefined.md)
