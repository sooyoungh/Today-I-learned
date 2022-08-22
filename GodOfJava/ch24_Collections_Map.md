# ch24 : Collections - Map

**목차**
1. [Collections](#collections)
2. [Map](#map)
3. [HashMap](#hashmap)
4. [TreeMap](#treemap)
5. [Prperties 클래스](#prperties-클래스)


## Collections
![image](https://user-images.githubusercontent.com/77563814/184531194-660d1c90-a1d1-4d14-a1c8-0c09d5e06d59.png)


## Map

<aside>
  
    ⚡ 키와 값이 1:1로 저장되는 자료구조로, 값은 중복이 가능하나 키는 중복이 안된다.

</aside>

- 주로 HashMap 클래스를 사용하고, 키값이 정렬되어야하면 TreeMap을 사용한다.
- HashTable
    - HashTable은 Map 인터페이스를 구현하지만, HashMap, TreeMap와는 차이가 있다.
    - HashMap은 스레드-세이프하지 않은 반면, HashTable은 스레드 세이프하다.
    - 또한, HashMap은 키 혹은 값에 null 저장가능하나, HashTable은 불가능하다.

HashTable을 제외한 Map(을 구현한) 클래스들은 스레드-세이프하지 않다. Map(을 구현한) 클래스들에서 스레드-세이프하게 구현하려면 다음과 같이 선언해주어야 한다.

`Map m = Collections.synchronized( new HashMap(..) );`

[HashTable 자세히 알아보기❕🔎](https://github.com/Cs-Java-Interview/Tech-Interview/blob/main/data_structure/hash.md)

## HashMap

- 키는 기본자료형/참조자료형 모두 될 수 있음
    - 단, 키가 되는 자료형의 equals(), hashcode()를 잘 구현해야함
    - hashcode()값이 키값으로 저장되기에, 다른 객체(키)인데 hashcode()값이 같으면 같은 버킷을 사용하게 될 수 있음 // ???
- 초기화
    - `HashMap<String, String> map = new HashMap<String, String>();`
    - 많은 데이터를 담을 경우, 크기를 초기화해주는 것이 좋음! (크기 초기화 안해도 OK)
- 추가 (Map은 넣어준다는 표현을 많이 사용함)
    - `put(키, 값)`
    - 이미 있는 `키-값` 쌍에 새로운 값 등록하면, 새 값으로 대체된다.
- 조회
    - `get(키)`
        - 없는 `키-값` 쌍에 접근/조회하면 null 값이 나옴.
        - 참고) 리스트/배열의 경우 없는 인덱스로 접근하면, 개발자에게 해당 위치가 없다는 걸 알려주기 위해서 `ArrayIndexOutOfBoundsException` 이라는 예외를 발생시킨다.
    - `keySet()` : Map 객체의 키들을 Set 타입으로 리턴함
    - `values()` : Map 객체의 값을 Collection 타입으로 리턴함
    - `entrySet()` : Map 객체의 키-값쌍을 Entry 타입으로 리턴함 (중복 X)
        - `getKey()` : Entry 타입의 키 조회
        - `getValue()` : Entry 타입의 값 조회
- 삭제
    - `remove()`

## TreeMap

<aside>
  
    ⚡ 키값을 정렬하여 저장하는 Map 자료구조

</aside>

- 기본적으로 `숫자 > 알파벳 대문자 > 알파벳 소문자 > 한글` 순으로 정렬된다.
- SortedMap 인터페이스를 구현하였기에 정렬되어 저장된다.

## Prperties 클래스

- System 클래스에 Properties 라는 클래스가 있다.
- Properties는 `Hashtable`을 확장하였기에, Map 인터페이스에 있는 메소드들을 모두 사용할 수 있다.
- Prperties 클래스을 사용하는 이유?
    - Hashtable, HashMap 보다 제공하는 메소드가 많기 때문
    - `load()`, `store()` 등
- 데이터 저장과 읽기를 한줄로 할 수 있다.
