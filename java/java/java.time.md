# java.time 패키지 살펴보기

**목차**
1. [java.time 패키지](#javatime-패키지)
2. [LocalDateTime, OffsetDateTime, ZonedDateTime](#localdatetime-offsetdatetime-zoneddatetime)
3. [LocalDateTime](#localdatetime)
4. [OffsetDateTime](#offsetdatetime)
5. [Instant](#instant)

> 앞서 **java.util** 패키지의 Date, Calendar의 문제점을 보았다. 현재는 java.time를 사용하여 시간/날짜를 표현한다. 각각의 사용법에 대해서 알아보자.
>

##  java.time 패키지

Date와 Calendar 클래스들의 단점을 개선하여 java.time 패키지가 등장하였다. 개선된점을 알아보자.

1. **불변(Immutable)객체이다.**
    
    불변객체이기 때문에 기존의 객체를 변경하면, 새로운 객체를 생성하여 반환한다. 이전과 다르게 멀티 스레드 환경에서 안전하다. 불변객체가 아니라면, 동시에 여러 스레드에서 접근하여 값을 변경하여 문제가 생길 수 있다. 이러한 점에서 안전하다.(Thread-safe)
    
2. **국제화에 알맞다.** → Timezone(국가마다의 시간대)을 고려할 수 있음
    1. Timezone을 고려하지 않을 경우 (LocalDateTime)
        - 어떤 지역/국가이던 상관없이 표시되는 시간이 같다.
        - ex) 생년월일
    2. Timezone을 고려하는 경우 (OffsetDateTime, ZonedDateTime)
        - Timezone을 고려하여 표시되는 시간이 달라진다.
        - ex) 온라인 라이브 콘서트, 회의 시간

> **Timezone이란?** 
> 
>        국가마다의 시간을 뜻한다. (그리니치 천문대를 기준으로 +, - 로 표기)
>        KST = UTC+09:00
>        한국은 그리니치 천문대를 기준으로 동경 135°이다. 계산법에 따라 UTC에 9시간(15° = 1시간)을 더하면 한국 시간이다. 
>        참고로 GMT(그리니치 천문대 기준)와 UTC는 비슷하다. UTC가 오차가 더 적으나, 거의 비슷하여 혼용하여 사용한다.
> 

뿐만 아니라 값을 반환하는 방식이나 요일 인덱스 방식도 개선되어 직관적이고 개발자에게 혼동을 주지 않는다.

## LocalDateTime, OffsetDateTime, ZonedDateTime



![image](https://user-images.githubusercontent.com/77563814/187043908-6ab91743-4c16-456e-b7a1-7b380f502219.png)

각각을 구분하면 그림과 같다. T는 Date와 Time을 구분하기 위한 문자이다. ZoneOffset, ZoneId는 지역과 관련된 정보이다.

> **ZoneOffset**이란
> 
>       UTC와 현지 시간과의 차이이다. 한국의 경우 `UTC+09:00` 에서 `+09:00` 을 의미한다.
> 
> **ZonedId이란**
> 
>       타임존을 구분하는 문자열 코드이다. 나라/지역을 구분하기 위해 사용한다. 
>       `UTC+09:00` 인 나라/지역이 하나가 아니다. 일본-오사카/도쿄, 한국-서울 등이 있다. (한국의 경우, Asia/Seoul)
> 



## LocalDateTime

<aside>
  
    🌟 Local 단어 그대로, 지역에 대한 정보가 없는 시간/날짜 API이다.

</aside>

- 예를 들면 생일, 기념일, 개인 일정 등 국가/지역과 관련없는 시간을 표현할 때 사용한다.

- `LocalDate` : 날짜 정보(년, 월, 일)
- `LocalTime` : 시간 정보(시, 분, 초)
- `LocalDateTime` : 시간+날짜 정보

객체 생성은 다음과 같이 한다.

```java
// now()
LocalDate date = LocalDate.now();
LocalTime time = LocalTime.now();
LocalDateTime loc = LocalDateTime.now(); //2022-06-13T10:30:20

// of()
LocalDate date = LocalDate.of(2010, 10, 04);
LocalTime time = LocalTime.of(23, 59, 59);
LocalDateTime.of(2016, 6, 13, 10, 20, 30); // 2016-6-13T10:20:30

// parse()
LocalDate date = LocalDate.parse("1992-12-04");
```

## OffsetDateTime

<aside>
  
    🌟 LocalDateTime + ZoneOffset까지 포함한 API이다.

</aside>

- 타임존(시간대)에 대한 정보를 가지고 있다.
- ex) `OffsetDateTime off = OffsetDateTime.now(); // 2016-6-13T10:20:30.000000+09:00`


## ZonedDateTime

<aside>
  
    🌟 OffsetDateTime + ZoneId까지 포함한 API이다.

</aside>

- 타임존뿐만 아니라, 지역정보도 가지도 있다.
- 즉, `+09:00`와 어떤 지역인지 문자열(`Asia/Seoul`)로 정보를 가진다.
- ex) `ZonedDateTime zone = ZonedDateTime.now(); // 2016-6-13T10:20:30.000000+09:00[Asia/Seoul]`


## Instant

사람에게는 `2016-6-13T10:20:30`같은 표기가 알아보기 편리하다. 그러나 컴퓨터 입장에서는 이보다 하나의 수로 표현하여 연산하는 게 빠르다. Instant는 주로 두 시점 간의 차이를 연산할 때 사용한다. `until()`은 두 시점 간의 차이를 반환한다. 기본적으로 숫자 자료형으로 저장되기에, 다른 날짜 클래스보다 연산이 빠르다.

> **유닉스시간이란 (Eporch 시간)**
> 
>       UTC(1970년 1월 1일)로부터의 경과 시간을 초로 환산한 값이다.
>


```java
Instant instant1 = Instant.now();
Thread.sleep(1234);
Instant instant2 = Instant.now(); 

System.out.printf(instant1.until(instant2, ChronoUnit.NANOS)); // 1234000000
```


**Reference**

- [https://chowdera.com/2022/02/202202090314458713.html](https://chowdera.com/2022/02/202202090314458713.html)
- [https://umanking.github.io/2020/05/09/java-date-time/](https://umanking.github.io/2020/05/09/java-date-time/)
- [https://gngsn.tistory.com/167](https://gngsn.tistory.com/167)
- [https://perfectacle.github.io/2018/09/26/java8-date-time/](https://perfectacle.github.io/2018/09/26/java8-date-time/)
- [https://devlopsquare.tistory.com/248](https://devlopsquare.tistory.com/248)


[위로가기⬆](#javatime-패키지-살펴보기)


