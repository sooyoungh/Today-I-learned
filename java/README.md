---
description: Java 중요하거나 헷갈린 내용 글로 정리. 넘버링된 글은 시리즈로 작성한 글
---

# 🎪 Java

### [**자바의 JVM** 🚕](jvm/)

* JVM의 구조
* JVM의 GC 작동 방식
* [자바에 Call By Value만 있는 이유 (JVM의 GC)](jvm/call\_by\_value.md)
* [인코딩 - Switch문은 if문보다 빠른가?](jvm/switch\_encoding.md)
* [인코딩 - 문자열 인코딩 방식 알아보기](jvm/encoding.md)

### [**자바의 다형성** 🍇](undefined/)

* [자바의 다형성 간단 정리](undefined/undefined-1.md)
* [List<> = new ArrayList<> 쓰는 이유 (업캐스팅)](undefined/upcasting.md)
* Generic - 자세히 알아보기
* [Generic - 자바의 변성](undefined/undefined.md)
* 상속 조심🙅‍ 1 - 추상클래스 vs 인터페이스
* [상속 조심🙅‍ 2 - Prefer composition than inheritance](undefined/prefer\_composition\_than\_inheritance.md)

### [**예외 처리 주의** ❗](undefined-1/)

* [Checked Exception을 Unchecked Exception으로 throw하자](undefined-1/wrapping\_checked\_exception\_into\_unchecked\_exception.md)
* [try/catch/finally와 return문](undefined-1/try-catch-finally-\_return.md)
* [Try with resource로 자동 자원 해제하기](undefined-1/try\_with\_resource.md)
* Null 파헤치기
* [Optional 잘 쓰는 법1 - 언제 쓸까?](undefined-1/optional.md)
* Optional 잘 쓰는 법2 - orElse/orElseGet

### [**Java Collections 자세히** 🔎](java-collections/)

* [Java Collections - List](java-collections/collections\_list.md)
* [Java Collections - Hash](java-collections/collections\_hash.md)
* [Thread-Safe 클래스는 필요할 경우에만 쓰자](java-collections/collections\_warning.md)
* [equals()과 hashcode()는 함께 오버라이딩해주자](java-collections/equals.md)

### [**Java와 I/O** 💿](io/)

1. [Blocking/Non-Blocking, Sync/Async](io/blocking\_synchronous.md)
2. [Java I/O 알아보기](io/java\_io.md)
3. [I/O 대신 NIO?](io/nio.md)
4. NIO2의 파일
5. [자바의 직렬화(Serialize)](io/undefined.md)

### [**Java와 동기화** 💭](java-1/)

1. [자바의 동기화 1 - volatile](java-1/volatile.md)
2. [자바의 동기화 2 - synchronized](java-1/synchronized.md)
3. [자바의 동기화 3 - Lock-Free와 Atomic(CAS)](java-1/atomic.md)
4. [자바의 동기화 4 - ABA 문제](java-1/aba.md)

### [**Java와 시간** 🕜](java/)

* [Date, Calendar 쓰지 말자](java/date\_calendar\_.md)
* [java.time 패키지 살펴보기](java/java.time.md)

### [**Java와 Web**](java-web/) 🕹

* [자바와 웹프로그래밍 - JSP/서블릿/MVC](java-web/web.md)
* [자바와 DB - JDBC/DB pool](java-web/db.md)

###
