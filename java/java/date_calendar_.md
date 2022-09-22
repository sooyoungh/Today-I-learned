# Date, Calendar 쓰지 말자

**목차**

1. [java.util의 Date, Calendar을 쓰면 안되는 이유](date\_calendar\_.md#java.util의-date,-calendar을-쓰면-안되는-이유)
2. [정리하면](date\_calendar\_.md#정리하면)

> **java.util** 패키지의 Date, Calendar는 몇 가지 문제가 있어 현재는 java.time를 사용한다. 어떤 점이 문제가 되는 지 알아보자.

## java.util의 Date, Calendar을 쓰면 안되는 이유

> Date 클래스를 개선하여 Calendar가 등장했다. 그러나 둘다 개발자가 헷갈릴 수 있는 지점들이 너무 많다.
>
> 현재는 대부분이 Deprecated 되었다. 간단하게 알아보자.

1. 불변(immutable) 객체가 아니다.
   * 한번 만든 객체가 공유되어, 다른 스레드에서 값을 바꾸면 side effect를 줄 수 있다. 날짜/시간/돈 같은 객체는 VO이다. VO는 값을 표현하는 객체로, 전달할 목적으로 만드는 클래스이다. VO는 반드시 불변객체여야 한다.
2. 인덱스가 헷갈린다.
   * `Calendar`는 0부터 시작하여, 1월(January)이 0번이다.
   * `Calendar` 객체는 요일이 1부터 시작하는데 `Date`는 0부터 시작한다. (요일 시작 = 일요일)
3. Date와 Calendar의 불편한 혼용
   * Date를 보완하기 위해 Calendar가 나왔지만, Calendar만으로는 부족하여 혼용하게 된다. 각자 요일 인덱스도 다르고 두 객체를 생성하여 사용하므로 비효율적이다.
4. java.util.Date 하위 클래스의 문제
   * java.sql.Date와 java.util.Date의 이름이 같다. 혼동하기 쉽다.

이외에도 개발자가 헷갈리고 불편한 점들이 많다.

## 정리하면

> Date를 개선하여 Calendar가 등장했지만, 여전히 단점들이 있다. JDK 1.8 이후로 단점들을 개선한 Java.time 패키지가 등장하였고, 현재는 이를 사용한다.

**Reference**

* [https://hyeon9mak.github.io/DTO-vs-VO/](https://hyeon9mak.github.io/DTO-vs-VO/)
* [https://gaebalsogi.tistory.com/66#��%B-�%A-%-C�%A-%--%----%--Date�%--%--%--Calendar�%-D%--%--�%B-%--�%-E%B-�%--%-C%--�%--��%--%A-%--�%B-%--�%-B%B-](https://gaebalsogi.tistory.com/66#%EB%AC%25B-%EC%25A-%25-C%EC%25A-%25--%25----%25--Date%EC%25--%25--%25--Calendar%EC%25-D%25--%25--%EB%25B-%25--%ED%25-E%25B-%ED%25--%25-C%25--%EC%25--%AD%ED%25--%25A-%25--%EB%25B-%25--%EB%25-B%25B-)
