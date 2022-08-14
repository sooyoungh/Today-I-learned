# ch22 : Collections - List

**목차**
1. [Collections - List]
2. [배열(array)과 ArrayList의 차이점](#배열(array)과-ArrayList의-차이점)
3. [ArrayList](#arraylist)
4. [Stack](#Stack)


## 배열(array)과 ArrayList의 차이점

배열(array)과 ArrayList의 차이점은 **크기가 고정적/가변적**이라는 점이다.

ArrayList는 선언시에 크기를 정해줄 수도 있지만, 원소를 추가하면서 해당 크기가 넘을 경우 동적으로 크기를 늘려준다.

[참고](https://zorba91.tistory.com/287) → **실제로는 가지고 있던 용량이 꽉 찼을 때, 용량이 기존의 1.5배를 늘린 새로운 배열에 기존 배열을 copy하는 것이다.**

## ArrayList

<aside>
  
    ✏️ 확장 가능한 배열 (크기가 고정X) + 순서가 있는 자료 구조

</aside>

- 메소드를 이용해서 요소 추가/삭제/조회
- **제너릭으로 한 가지 타입을 지정해서 사용한다.**
    - `ArrayList<String> list = ArrayList<String>();`
    - `ArrayList<String> list = ArrayList<>();` // new 뒤의 <>는 타입 생략 가능!
    - 보통 한 가지 타입만 지정하여 저장한다.  → 만약 여러 타입을 하나의 객체에 담으려면 DTO에 저장하는 것이 좋다.
    - 제너릭으로 타입 지정하면, 다른 타입을 추가하면 컴파일 에러가 발생한다. 컴파일 시점에 타입 실수를 예방할 수 있다.
        
        ```java
        ArrayList<String> list = ArrayList<String>();
        
        list.add(new Obect()); // 컴파일 에러, String만 가능!
        ```
        

- 추가
    - `add()` :
        - int 지정 시 해당 자리에 추가하고 나머지 요소들은 뒤로 shift 해줌
    - `addAll()` :
    - 리스트 전체 복사 시, `ArrayList<String> list2 = ArrayList<>(list1);`
    - 단, 리스트 전체 복사 시 `ArrayList<String> list2 = list1;` // 주소값이 복사됨, Shallow Copy ⇒ 따라서 Deep Copy를 원할 경우 addAll()이나, 생성자로 전체 복사할 것

- 크기
    - `배열.size()` : 들어가있는 원소의 갯수 // 비교 : String은 문자열 갯수 확인하기 위해서 `String배열.length()` 사용
    - `배열.length` : 배열에 넣을 수 있는 원소의 갯수

- 조회
    - `get(int index)` : 매개변수 위치의 데이터 조회
    - `indexOf(Object o)` : 매개변수로 넘어온 것과 같은 데이터의 인덱스 위치

- ArrayList → 배열로
    - `toArray(T[] a)` : ArrayList를 매개변수 T 타입 배열로 변경한다.
        
        ```java
        ArrayList<String> list = ArrayList<>();
        list.add("A");
        String[] strList = list.toArray(new String[0]); // 이렇게 크기가 0인 배열을 넘겨주는 것이 가장 좋음
        ```
        

- 삭제
    - `clear()` : 모두 삭제
    - `remove(int index)` : 매개변수의 위치에 있는 데이터 삭제
    - `remove(Object o)` : 매개변수와 같은 첫번째 데이터 삭제
    - `removeAll(Collection<?> c)` : 매개변수로 넘어온 컬렉션 객체의 데이터들 모두 삭제

추가로 Vector는 ArrayList와 비슷하지만, Thread-safe하다는 특징이 있다. 스레드-세이프한 ArrayList를 만드려면, 다음과 같이 해주어야 한다.

```java
List list = Collections.synchronisedList(new ArrayList(...));
```

## Stack

<aside>
  
    ✏️ LIFO(나중에 들어온게 먼저 나가는 방식)을 사용하기 위한 자료구조이다.

</aside>

> LIFO를 사용하기 위해서 Stack보다 더 빠른 ArrayQueue를 사용하는 것을 권장한다. 다만, 스레드-세이프하게 구현해야하는 경우 Stack을 사용해야 한다.
> 

- 조회
    - `peek()` : 조회한다.
    - `pop()` : 조회하고 해당 데이터를 삭제한다.
    - `push(E item)` : 추가한다
    - `empty()` :객체가 비었는지 확인한다.
