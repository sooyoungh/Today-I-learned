# if-else 지양하기 - 1. Enum

## Enum

Enum 타입을 사용하면 관련 상수/기능들을 한곳에 모아두고 사용할 수 있다. 관련 데이터들을 모아두니 수정/확장하기에 편리하다. 특히 분기문 처리가 필요할 때, 가독성이 떨어지는 if-else 대신 Enum을 활용하면 코드도 훨씬 깔끔해진다.

우선 유용하게 쓸 수 있는 메소드 몇 가지를 소개해보고, 실제 if-else를 대체해서 얼마나 코드가 깔끔해지는 지 정리해보았다.



### 1. 상수

아래 예제에선 Enum 타입별로 관련 상수(가격과 한글메뉴명)가 할당되어있다. 여기서 Enum 내장 메소드를 활용하면 여러 기능을 처리할 수 있다.

```java
public enum Menu {
    AMERICANO(4000, "아메리카노"),
    CAFE_LATTE(4500, "카페라떼"),
    CAFE_MOCHA(5000, "카페모카"),
    CAPPUCCINO(6000, "카푸치노"),
    TEA(5500, "티");

    private final int money;
    private final String korean;

    Menu(int money, String korean) {
        this.money = money;
        this.korean = korean;
    }
}
```

우선 가장 간단하게는, 아래 `getMoney()`처럼 타입별 상수값을 반환할 수도 있다. 타입별 상수값을 찾을 때 사용할 수 있다.

```java
// Enum 클래스
public int getMoney() {
    return money;
}

// Test 코드
@Test
void test() {
    Menu tea = Menu.TEA;
    int result = tea.getMoney();

    Assertions.assertThat(result).isEqualTo(5500);
}
```

그리고 Enum은 `values()` 라는 메소드를 지원하는 데, 이는 모든 Enum 타입들을 리스트로 반환해준다.

즉, Menu._values_()는 모든 Menu타입을 가진 리스트이다. 이 메소드를 활용하면 상수값으로 해당 Enum 타입을 찾을 수도 있다!

```java
System.*out*.println(Arrays.*toString*(Menu.*values*()));

// 출력 : [AMERICANO, CAFE_LATTE, CAFE_MOCHA, CAPPUCCINO, TEA]
```

#### 상수값으로 타입 반환

아래처럼 한글 메뉴명만으로 해당 타입을 반환할 수 있다. (→ 이 방식이 if-else를 사용하지 않고 분기처리를 할 수 있게 해준다. 글 마지막에 다시 예제를 써놓았다!)

```java
// Enum 클래스
public static Menu getMenuByKorean(String korean) {    
    return Arrays.stream(values()) // values() : 모든 Enum 타입 리스트 반환
            .filter(i -> korean.equals(i.korean))
            .findFirst()
            .orElse(null);
}

// Test 코드
@Test
void test() {
    Menu findMenu = Menu.getMenuByKorean("아메리카노");

    Assertions.assertThat(findMenu).isEqualTo(Menu.AMERICANO);
}
```

#### 타입의 존재여부

해당 타입의 존재여부도 확인할 수 있다.

```java
// Enum 클래스
public static Boolean isGameOptionExist(String gameOption) {
    return Stream.of(values())
            .anyMatch(value -> gameOption.equals(value.message));
}

// Test 코드
@Test
void test() {
    
}
```

#### 타입별로 같은 연산일 경우

이 예제처럼 메뉴별로 연산이 같을 경우, 갯수를 입력하면 메뉴 가격대로 곱해 최종 금액을 반환하는 메소드를 만들 수 있다.

```java
public enum Menu {
    AMERICANO(4000, "아메리카노"),
    CAFE_LATTE(4500, "카페라떼"),
    CAFE_MOCHA(5000, "카페모카"),
    CAPPUCCINO(6000, "카푸치노"),
    TEA(5500, "티");

    ...

    public int calculateMoney (int count){ // 
        return money * count;
    }
}

@Test
void test3() {
    int result = Menu.AMERICANO.calculateMoney(2);
    Assertions.assertThat(result).isEqualTo(8000);
}
```

**근데 만약 이 예제처럼 연산식이 다 `money * count` 이 아니라, Enum 타입별로 다르면 어떻게 될까? ⇒ 아예 연산식을 상수화해주거나 오버라이딩해주어야 한다!**



### 2. 연산**식**

Enum 타입별로 연산식이 다를 경우, 연산식 자체를 상수화해주거나, 오버라이딩해줄 수 있다. 아래는 사격 게임에서 사용자가 맞춘 과녁이 어느 구역이냐에 따라 얻는 점수가 다르다고 가정한 상황이다.

#### 연산식 상수화

```java
public enum Prize {

	BASIC(i -> i),
	DOUBLE(i -> i * 2),
	BONUS(i -> i + 50),
	DOUBLE_AND_BONUS(i -> i * 3);

    private Function<Integer, Integer> expression;

    Prize(Function<Integer, Integer> expression) {
        this.expression = expression;
    }

    public Integer calculate(int count) {
        return expression.apply(count);
    }
}
```

#### 오버라이딩

```java
public enum Prize {

	BASIC{
        @Override
        Integer calculate(int count) {
            return count;
        };
    },
	DOUBLE{
        @Override
        Integer calculate(int count) {
            return count * 2;
        };
    },
	BONUS{
        @Override
        Integer calculate(int count) {
            return count + 50;
        };
    },
	DOUBLE_AND_BONUS{
        @Override
        Integer calculate(int count) {
            return count * 3;
        };
    };

    abstract Integer calculate(int count);
}
```

두가지 방법 모두 `해당 타입.calculate()` 으로 사용하면 된다.

```java
@Test
void test() {
    int resultDouble = Prize.DOUBLE.calculate(100);
		int resultBONUS = Prize.BONUS.calculate(100);

    Assertions.assertThat(resultDouble).isEqualTo(200);
    Assertions.assertThat(resultBONUS).isEqualTo(150);
}
```



### 3. Enum으로 if-else 대신하기

위에서 사격 구역 별로 점수가 다르게 반환되는 상황에서 if-else와 Enum을 비교해보았다.

#### if-else만 사용

if- else문만으로 구현하면 아래처럼 코드가 길고 복잡해진다. 가독성이 떨어지고, 각 분기 상황마다 뭔가를 수정해줄 때도 불편하다.

```java
// String korean: 맞춘 구역 (“베이직”, "더블", "보너스", "더블보너스")
// int count: 몇번 맞췄는 지
void getResult(String korean, int count) {
    int result;

    if (korean.equals("베이직")) {
        result =  count;
    } else if (korean.equals("더블")) {
        result =  count * 2;
    } else if (korean.equals("보너스")) {
        result =  count + 50;
    } else if (korean.equals("더블보너스")) {
        result =  count * 3;
    }
		return result;
}
```

#### Enum 사용

Enum을 사용해서 관련 상수/연산식을 다 모아두었다. 여기서는 연산식 자체를 상수화했다.

`calculateByKorean(String korean, int count)`메소드를 보면, `korean`값(한글 구역명)으로 Enum 타입을 찾고, 그 타입에 맞는 연산을 한다.

```java
public enum Prize {

		BASIC("베이직", i -> i),
		DOUBLE("더블", i -> i * 2),
		BONUS("보너스", i -> i + 50),
		DOUBLE_AND_BONUS("더블보너스", i -> i * 3);

    private Function<Integer, Integer> expression;
    private String korean;

    Prize(String korean, Function<Integer, Integer> expression) {
        this.korean = korean;
        this.expression = expression;
    }

    public Integer calculate(int count) {
        return expression.apply(count);
    }

    public static Integer calculateByKorean(String korean, int count) {
        return Arrays.stream(values())
                .filter(i -> i.korean.equals(korean))
                .findFirst()
                .orElse(null)
                .calculate(count);
    }
}
```

이렇게 Enum을 활용하면, 아래처럼 if-else 분기문 없이 한줄로 값을 찾을 수 있다!

```java
// String korean: 맞춘 구역 (“베이직”, "더블", "보너스", "더블보너스")
// int count: 몇번 맞췄는 지
Integer getResult(String korean, int count) {
    return Prize.calculateByKorean(korean, count);
}
```



