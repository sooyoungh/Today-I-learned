# Switch문은 if문보다 빠른가? (인코딩 방식 비교)

**목차**
1. [Switch문이 if문보다 빠르다?](#switch문이-if문보다-빠르다)
2. [Switch문 인코딩 방식](#switch문-인코딩-방식)
3. [Tableswitch](#tableswitch)
4. [Lookupswitch](#lookupswitch)
5. [Switch문에서 String](#switch문에서-string)
6. [참고 링크](#reference)



## Switch문이 if문보다 빠르다?

결론부터 말하면 *대부분*의 경우 인코딩 방식때문에 Switch가 빠르다고 할 수 있다. (대부분이라 쓴 이유는 아래에 나온다.)

if문의 경우, 매 조건들을 체크하기 때문에 최악의 경우 O(n)의 시간 복잡도를 가진다. 마지막 케이스가 답일 경우 모든 케이스의 경우를 검사하게 되는 상황이 발생한다. 반면 **Switch**는 컴파일되면, 조건 값들을 인덱스로 접근하는 jump table을 생성한다. 그리고 Switch문에 비교할 변수가 들어오면 해당 값을 인덱스로 하는 테이블에 바로 접근한다. 이 jump table을 만드는 방식에는 2가지(`Tableswitch`, `Lookupswitch`)이 있다. 두 가지 방식 모두 if문으로 조건 검색하는 방식보다 빠르다.

따라서…

값이 연속적이거나 케이스가 3개 이상있는 경우에는 Switch를 사용하는 것이 빠르다고 한다! 참고로 case가 2개인 경우, 오히려 jump table을 만드는 리소스가 크기 때문에 if문을 사용하는 것이 좋다. [참고링크](http://daplus.net/java-%EC%8A%A4%EC%9C%84%EC%B9%98%EA%B0%80-%EB%8D%94-%EB%B9%A0%EB%A5%B8-%EC%9D%B4%EC%9C%A0/)

## Switch문 인코딩 방식

> 자바소스 코드 파일을 컴파일하면 클래스 파일(바이트 코드)이 된다.
이 Switch문은 컴파일 과정에서 `Tableswitch` 혹은 `Lookupswitch` 방식으로 인코딩된다. 
둘의 차이점은 jump table을 만드는 방식이다. 그리고 이 차이가 속도에도 영향을 준다.
> 

## Tableswitch

<aside>
  
    ✏️  Switch문에서 각 조건들을 인덱스로 하는 jump table을 생성하여 처리하는 방식. 이때, 각 조건들을 범위로 인데싱하여 속도가 빠르다.

</aside>

예를 들어, switch문에서 ‘a’, ‘b’, ‘c’ 각각의 케이스별로 분기한다고 하면 소스 코드와 컴파일된 바이트 코드는 다음과 같다. 

(참고로 char형 타입은 int로 변환되어서 처리된다. 즉 ‘a’는 97로, ‘b’는 98로)

```java
// 자바 소스 코드
void switchFunc(char x) {
        switch (x) {
            case 'a': break;
            case 'b': break;
            case 'c': break;
            default : break;
        }
    }

// 컴파일된 바이트코드 -> javap로 확인해볼 수 있다.
void switchFunc(char);
    descriptor: (C)V
    flags: (0x0000)
    Code:
      stack=1, locals=2, args_size=2
         0: iload_1
         1: tableswitch   { // 97 to 99
                      97: 28
                      98: 31
                      99: 34
                 default: 37
            }
        28: goto          37
        31: goto          37
        34: goto          37
        37: return
      LineNumberTable:
        line 4: 0
        line 7: 28
        line 10: 31
        line 13: 34
        line 18: 37
      StackMapTable: number_of_entries = 4
        frame_type = 28 /* same */
        frame_type = 2 /* same */
        frame_type = 2 /* same */
        frame_type = 2 /* same */
```

여기서 생성된 jump table을 보면, case들마다 인덱싱한 jump table이 생성된 것을 확인할 수 있다.

```java
1: tableswitch   { // 97 to 99 ('a', 'b', 'c')
	97: 28
	98: 31
	99: 34
	default: 37
}
```

switch문에 **비교할 변수**가 들어오면, 이 jump table을 통해서 바로 해당 인덱스에 접근한다. 즉, if문처럼 각각의 조건문대로 비교연산을 하는 것이 아니라, 미리 생성된 jump table에 인덱스로 바로 접근할 수 있는 것이다. 따라서 switch문이 if문보다 빠를 수 있는 것이다.

 

다만, 이 방식은 case들의 전체 범위를 인덱싱하기에 case들 간의 간격이 큰 경우 비효울적일 수 있다. 예를 들어 (case 0 : case 400 : case 93748 : 등)처럼 case들 간의 간격이 큰 경우에는 `Tableswitch` 방식으로 jump table을 만드는 것이 비효율적이다. → 이 경우 `Lookupswitch` 방식이 더 효율적이다.

## Lookupswitch

<aside>
  
    ✏️ Switch문에서 각 조건들을 jump table로 인덱싱하여 처리하는 방식. 이때, 조건값을 범위로 인덱싱하지 않는다. 따라서 Switch문에 들어온 변수와 jump table의 인덱스를 비교연산하여 jump table에 접근한다.

</aside>

아래의 경우는 case들 간의 간격이 큰 경우로,

각각 1, 100, 1000인 case들의 switch문이다.

```java
// 자바 소스 코드
void switchFunc(int x) {
        switch (x) {
            case 1: break;
            case 100:break;
            case 1000: break;
            default: break;
        }
    }

// 컴파일된 바이트코드
void switchFunc(int);
    descriptor: (I)V
    flags: (0x0000)
    Code:
      stack=1, locals=2, args_size=2
         0: iload_1
         1: lookupswitch  { // 3
                       1: 36
                     100: 39
                    1000: 42
                 default: 45
            }
        36: goto          45
        39: goto          45
        42: goto          45
        45: return
      LineNumberTable:
        line 4: 0
        line 7: 36
        line 10: 39
        line 13: 42
        line 18: 45
      StackMapTable: number_of_entries = 4
        frame_type = 36 /* same */
        frame_type = 2 /* same */
        frame_type = 2 /* same */
        frame_type = 2 /* same */
```

이 경우에 생성된 jump table을 보면, 다음과 같다. (자동으로 `lookupswitch` 방식으로 처리되었다.)

```java
1: lookupswitch  { // 3
	1: 36
	100: 39
	1000: 42
	default: 45
}
```

`Lookupswitch`는 `Tableswitch`처럼 인덱스로 바로 접근하는 게 아니다. 

**switch문에 들어온 변수**와 **table의 key의** 비교연산이 이뤄진다. 비교연산이 일치하면 해당 case로 처리된다.

이때 빠른 처리를 위해 내부적으로 key들은 정렬되어 구성되고, 이진탐색등의 검색 알고리즘을 사용한다. 인덱스로 바로 접근하는 `Tableswitch`방식보다는 느리지만, if문의 시간복잡도(O(n))보다는 빠르게 처리된다. 

> `Tableswitch`이 jump table에서 인덱스로 바로 접근하기 때문에 `Lookupswitch`보다 효율적이다. 하지만 case들의 간격이 크면 jump table이 비효율적으로 생성되므로 이러한 경우에는 `Lookupswitch`을 사용하는 것이 좋다.
> 

## Switch문에서 String

`Tableswitch`와 `Lookupswitch`은 int 데이터에서만 작동한다(byte, char, short 타입은 내부적으로 int로 처리되어 동작함). jump table이 생성될 때, case문의 조건은 int 타입으로 처리된다.

String의 경우, (hashCode 메서드에 의해) int 타입의 hashcode값으로 jump table을 생성된다. 이때 hashcode는 case들간의 간격이 연속적이지 않으므로 (랜덤한 주소값들이므로) `Lookupswitch`방식을 사용한다.

## Reference

[[Java] switch 문이 if-else 보다 효율적인 이유 (tistory.com)](https://shanepark.tistory.com/395)

[[Java] Compiling Switches : tableswitch 와 lookupswitch (tistory.com)](https://jerry92k.tistory.com/54)

[https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-3.html#jvms-3.10](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-3.html#jvms-3.10)

[http://daplus.net/java-스위치가-더-빠른-이유/](http://daplus.net/java-%EC%8A%A4%EC%9C%84%EC%B9%98%EA%B0%80-%EB%8D%94-%EB%B9%A0%EB%A5%B8-%EC%9D%B4%EC%9C%A0/)
