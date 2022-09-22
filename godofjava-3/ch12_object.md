# ch12 : Object

**목차**

1. [Object](ch12\_object.md#Object)
2. [toString()](ch12\_object.md#toString)
3. [equals()](ch12\_object.md#equals)
4. [hashCode()](ch12\_object.md#hashCode)

## **Object**

* 모든 클래스는 Object 클래스를 상속받는다.
* 즉, 모든 클래스는 **Object 클레스에 있는 메서드들을 사용 가능**함 (toString, equals, hashCode 등)
* 주로 사용하는 메소드는 toString, equals, hashCode이고, 필요시 오버라이딩하여 사용한다.

참고로 자바는 다중 상속을 받을 수는 없지만, 여러 단계로 상속 받을 수는 있다. (예시. Obect → Parent → Child)

## toString

* 어떤 객체인지 나타낼 수 있다.
* toString()이 자동으로 호출되는 경우
  1. System.out.println()
  2. 객체(String을 제외)에 더하기 연산하는 경우
*   toString() 내부

    ```java
    System.out.println(obj); // 자동 obj.toString() 호출!
    System.out.println(obj.toString());

    // 기본 toString() 내부
    getClass().getName() + '@' + Integer.toHexString(hashcode());
    // 출력
    // 패키지,클래스명 + '@' + 객체의 해시코드
    ```
*   오버라이딩

    * DTO 클래스에서 내부 필드 데이터를 확인하고 싶을 경우, 오버라이딩하여 사용할 것 → IDE 활용해서 오버라이딩하면 간단!

    ```java
    public clas DTO {
    	// 오버라이딩
    	public String toString() {
    			return "name = " + name + "phone" + phone;
    	}
    }
    ```

## equals

* 참조자료형의 값비교
  * `==` : 주소값을 비교함.
  *   `equals()` : 오버라이딩해서 객체의 값 비교할 수 있음.

      오버라이딩 안하면 기본적으로 hashCode()값(객체의 주소값)을 비교하기에, 두 클래스의 인스턴스변수가 같아도 다르다고 나옴!

단, `equals()` 오버라이딩할 때, `hashCode()`도 같이 오버라이딩 해줘야한다.

## hashCode

* 객체의 메모리 주소를 리턴한다. (16진수)

> equals(), hashCode() 오버라이딩은 툴의 자동완성을 이용할 것!
