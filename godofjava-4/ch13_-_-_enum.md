# ch13 : 인터페이스와 추상클래스, enum

목차
1. [인터페이스](#인터페이스)
2. [추상클래스](#추상클래스)
3. [final](#final)
4. [enum](#enum)

> 
> 💡 인터페이스와 추상클래스를 사용하는 이유?
> 
> : 공통적으로 구현해햐하는 기능들을 설계 단계에 정해두어, 
개발 시에는 기능 구현에만 집중할 수 있다!
> 

## 인터페이스

```java
public interface MemberManager {
	public boolean addMember(MemberDTO member); // 몸통이 없어야 함
	public boolean removeMember(String name, String num);
}
```

- 특징
    - `implements`를 통해서 상속받는다
    - 인터페이스는 다중 상속 가능하하고, static이나 final 선언하지 못한다.
    - 상속받을 경우, 해당 클래스에서 인터페이스의 메소드들을 구현해주어야 한다. (오버라이딩) → 안하면 컴파일 에러
    - 사용
        
        ```java
        MemberManager member = MemberManagerImpl();
        // 인터페이스                 구현체 
        //                    (상속받은 인터페이스의 메소드 모두 구현한 상태)
        ```
        

## 추상클래스

```java
public abstract class MemberManagerAbstract {
  
	public abstract boolean addMember(MemberDTO member);
	public abstract boolean removeMember(String name, String num);
  
	public void printLog(String data) {    // 일부 구현이 되어 있어도 됨
			System.out.println(data)
		}
}
```

- 특징
    - `abstract` 클래스 안에 `abstract` 메소드가 0개 이상, 몸통이 있는 메소드가 0개 이상이면 된다.
    - extends를 통해서 상속 받는다.
    - 상속받은 경우, 해당 클래스에서 `abstract` 메소드는 반드시 구현해야한다. → 안하면 컴파일 에러


> 
>    ✏️ 인터페이스와 추상 클래스의 차이?
>    
>    설계 시 기능의 선언을 하고, 개발 시에는 기능 구현에만 집중하기 위해 인터페이스를 사용한다. 같은 인터페이스를 상속받은 자식 클래스들끼리는 같은 기능을 가지고 있다.
>    이때, 일부 공통 기능들은 미리 구현해놓아야 편한 경우, 인터페이스보다 추상클래스를 사용한다. 
>    
>	-> ‼ java 8버전부터는 인터페이스에서도 default 키워드로 구현된 메소드를 선언할 수 있다!
>

- 인터페이스는 **부모는 다른** 자식 클래스들에서 공통 기능들이 있는 경우
    - ex) jdbc
        
        jdbc는 Oracle, MySQL 등 각 DBMS들이 각자에 맞게 인터페이스의 메소드들을 구현한다.
        
- 추상클래스는 **부모가 같은** 자식 클래스들에서 같은 기능들을 제공할 경우
    - ex) 동물 상속 관계에서 동물(부모)→ 고양이, 강아지(자식)
        
        부모 추상클래스의 동물 기능들을 자식 클래스들에서 각자에 맞게 재사용 + 확장하는 경우
        
        울음소리(), eat() 등등…
        

## final

- final 클래스 : 상속해줄 수 없다
- final 메소드 : 오버라이딩 할 수 없다.
- final 변수 : 더 이상 변수를 바꿀 수 없다. 선언 동시에 초기화할 것!
    - 참고로 참조자료형을 final로 선언하면 새로운 객체를 생성할 수 없을 뿐이지,
    - 해당 객체의 필드값은 final이 아니다.
    - 즉, 어떤 클래스가 final이라고 해서, 그 안에 있는 인스턴스 변수나 클래스 변수가 final은 아니다.

## enum

1. 상수 집합 클래스 (상수만) → 선언은 간단하나 구현이 복잡해짐

```java
public enum OverTimeValues { //클래스 종류
	THREE_HOUR,
	FOUR_HOUR,
	FIVE_HOUR;
}
```

- 사용 - switch문 활용
    
    ```java
    // switch문에서 유용하게 활용할 수 있음
    public int getOverTimeAmount(OverTimeValues val) {
    	int amount = 0;
    	switch(val) {
    		case THREE_HOUR:
    			amount = 300;
    			break;
    		case FOUR_HOUR:
    			amount = 400;
    			break;
    		case FIVE_HOUR:
    			amount = 500;
    			break;
    	}
    	return amount;
    }
    
    // -----메인 함수에서----
    
    // 'enum 클래스이름.상수이름'으로 접근
    int myAmount = getOverTimeAmount(OverTimeValues.THREE_HOUR);
    // 이때, String으로 THREE_HOUR을 입력하면 컴파일 에러남
    ```
    

2. 상수마다 숫자값 지정 → 수정하려면 해당 클래스를 다시 컴파일해야 하나, 성능이 더 좋음,,
    
    ```java
    public enum OverTimeValues2 { //클래스 종류
    	THREE_HOUR(300),
    	FOUR_HOUR(400),
    	FIVE_HOUR(500); // 값을 동적으로 할당하는 건 불가능
    		
    	private final int amount;
    
    	OverTimeValues2(int amount) { // 생성자 -> package-private 이나 private만 가능!
    		this.amount = amount;       // 상수를 enum 클래스 안에서 선언할떄만 사용할 수 있음?????
    	}
    
    	public int getAmount() {
    		return amount;
    	}
    }
    ```
    

- enum 클래스는 무조건 java.lang.Enum을 상속받는다.(자동)
    - 개발자가 임의로 다른 클래스를 extemds 할수 없음 (다중 상속 금지)
    - Enum은 Object를 상속받기에, `equals(), hashcode(), toString()` 를 사용할 수 있음
    - Enum 클래스 메소드들
        - compareTo(E e) : 매개변수로 enum 타입과의 순서차이를 리턴한다.
