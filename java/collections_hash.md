# Java Collections - Hash 🔍

**목차**
1. [Hash 사용하는 컬렉션 비교하기](#hash-사용하는-컬렉션-비교하기)
2. [HashTable](#hashTable)


> Hashing을 사용하는 `Hashtable`, `HashMap`, `HashSet`에 대해 정리해보았다.
> 


![image](https://user-images.githubusercontent.com/77563814/186735509-98e4141f-c0c1-4cf9-b2c7-250b2c3cb8cd.png)


## Hash 사용하는 컬렉션 비교하기

이 컬렉션들은 모두 Hash 알고리즘을 사용한다. `Hashtable`과 `HashMap`은 Map 인터페이스를 구현한다. 즉, Key-Value 형식의 자료구조이다. Map 구현체에 Hash 알고리즘은, **Key값을 Hashing하여 인덱스로 사용**한다는 뜻이다.

반면 `HashSet` 은 Set 인터페이스의 구현체이다. 즉 순서/중복없는 자료구조이다. 삽입되는 객체를 Key값으로 하고, 내부 구현 코드에서 필드로 선언한 객체를 Value 값으로 사용한다. 따라서 

- `Hashtable`
    - Map 인터페이스의 구현체
    - Thread-safe하다 ⇒ 멀티 스레드 환경에서 사용한다. 그러나 성능이 떨어지므로 멀티 스레드 환경이 아니라면 지양하자
- `HashMap`
    - Map 인터페이스의 구현체
    - Thread-safe하지 않다 ⇒ 성능이 좋지만 멀티스레드에서는 사용X
- `HashSet`
    - Set 인터페이스의 구현체 ⇒ 객체 자체로 저장하기에 값 중복이 안된다.
    - `HashSet` 은 내부적으로 `HashMap`을 사용한다. 즉, `Hashtable`과 유사하게 데이터 저장한다.
    - 객체 자체를 Key값으로 저장하고, Value값은 dummy data를 사용한다.

> `Hashtable` vs `HashMap`
> 
> `Hashtable`과 `HashMap`은 Map을 구현하는 거의 비슷한 자료구조들이다. 그러나 `Hashtable`은 동기화를 지원하고, `HashMap`은 동기화를 지원하지 않는다. 따라서 멀티 스레드 환경에서는 `Hashtable`는 멀티 스레드 환경일 때 사용하고, 싱글 스레드 환경일 경우 `HashMap`을 사용해야 성능면에서 유리하다. (`Hashtable`는 성능이 떨어진다.) 또한 `HashMap`은 보조 해시 함수를 사용하여 해시 충돌이 덜하다는 장점이 있다.
> 
> `HashSet`vs `HashMap`
> `HashSet`은 객체 자체를 Key값으로, Value값은 dummy data으로 두 객체가 저장되기 때문에 `HashSet`이 더 느리다.
> 

## HashTable

<aside>
  
    🌟 `key - value`로 이루어진 배열이다. 배열이기에 빠른 검색 속도를 가진다. ‼

        이때의 key는 해시함수를 사용하여 해시값으로 매핑한 인덱스이다.

</aside>

### Hash Function : `hashCode()`

- 키값을 입력받아 해시주소를 생성
- key를 **고정된 길이**의 hash로 변경해주는 역할
- 만들어진 해시 주소가 해시 테이블의 인덱스가 됨

### **검색 속도 ‼**

- `hash`는 내부적으로 `배열`을 사용하여 데이터를 저장하기 때문에 빠른 검색 속도를 갖는다.
- **해시 충돌이 일어나지 않는 경우, 해시테이블의 시간 복잡도는 O(1)**
- 해시 충돌이 일어날 경우 시간 복잡도는 O(1)이 아니다. (어떤 충돌 해결 기법을 사용하였느냐에 따라 다른 시간 복잡도를 가짐)

### **Load Factor란?**

- 해시 테이블에 데이터가 많아지면 탐색 효율을 위해 더 큰 해시 테이블로 바꿔야 하는 시점이 온다. 이때의 기준이 **Load Factor이다.**
- 기본 **Load Factor는** **0.75이다.** 즉, 75%의 버킷이 차면 현재 버킷 개수의 2배만큼 늘린다.
- 정확히는 `Threshold` 값이 넘어가면 re-hashing 된다. (`Threshold = 용량 * Load Factor`이다. )

### **해시 충돌**

- 해시 충돌 : 두 개의 다른 key가 동일한 hash 값을 갖는 경우
- 해시 함수로 해시를 만드는 과정에서 **서로 다른 key가 같은 해시로 변경**되면, 같은 공간에 2개의 value가 저장되므로 key-value가 1:1로 매핑되어야 하는 해시 테이블의 특성에 위배된다.
- 해시 충돌은 필연적으로 나타날 수 밖에 없다. Collision이 많아질 수록 Search 에 필요한 Time Complexity 가 O(1)에서 O(n)에 가까워진다. ⇒ 좋은 `hash function`를 선택하는 것은 hash table 의 성능 향상에 필수적인 것이다.
- **좋은 해시 함수의 기준?**
    1. 충돌이 적고(중복이 적고)
    2. 해시 함수값이 해시 테이블의 주소 영역 내에서 고르게 분포되어야 하고
    3. 속도가 빨라야 좋은 해시 함수이다.

### 해시 **충돌 해결방법**

1. **Open Address (개방주소법)**
    - ![image](https://user-images.githubusercontent.com/77563814/186735860-369c813f-65a3-49e8-b2af-634f17294b97.png)
    - 충돌 발생 시 해시 함수로 얻은 주소가 아닌 **다른 해시 버킷에 해당 자료를 삽입하는 방식**
    - **빈 버킷이 나올 때까지 탐색 후, 찾으면 해당 자료를 삽입**
    - 장점 : 추가 저장 공간을 이용하지 않고 해시 테이블 내에서 데이터 저장함.
    - 단점 : 데이터의 양이 늘어나면 새 **저장소를 미리 확보**해두어야 한다. (새 해시 테이블 준비)
    - 시간복잡도
        - 탐색, 삽입, 삭제 모두 최상의 경우 O(1), 최악의 경우 O(n)
    - 해결 기법
        - 선형 프로빙(Linear probing) : 비어있는 인덱스를 선형적으로 탐색하는 방식
        - 이차식 프로빙(Quadratic probing) : 배열을 2차 함수꼴로 건너 뛰며 빈공간을 탐색하는 방식
        - 이중 해싱 (Double Hashing, 재해싱) :
            - 위의 프로빙의 경우, 클러스터링 문제(데이터가 밀집) 발생하여 클러스터링 크기만큼 탐색 속도 지연.
            - 서로 다른 두개의 해시 함수를 이용하여, 충돌 시 간격을 결정하여 균일하게 저장함.
2. **Separate Chaining (체이닝)**
    - ![image](https://user-images.githubusercontent.com/77563814/186735895-a219c60c-a6c5-4f87-9942-506216cd26fd.png)
    1. **Linked List를 사용하는 방식**
        - 각각의 버킷(bucket)들을 연결리스트(Linked List)로 만들어 Collision이 발생하면 해당 bucket 의 list 에 추가하는 방식이다.
        - 버킷을 계속해서 사용하는 Open Address 방식에 비해 테이블의 확장을 늦출 수 있다.
        - 장점 : 연결 리스트의 특징을 그대로 이어받아 삭제 또는 삽입이 간단하다.
        - 단점 : 연결 리스트 자체의 오버헤드가 부담이 된다.
        - 시간 복잡도
            - 삽입 : O(1)
            - 탐색이나 삭제 : 키에 해당하는 리스트의 길이에 비례함, 최악의 경우 O(K) (K는 키 값의 갯수)
    2. **Tree를 사용하는 방식**
        - 각각의 버킷(bucket)들을 **트리**를 사용하여, Collision이 발생하면 해당 bucket 의 list 에 추가하는 방식이다.
        - **연결 리스트 혹은 트리를 사용할 것인가에 대한 기준은?**
            - 하나의 해시 버킷에 할당된 key-value 쌍의 개수이다.
            - **데이터의 개수가 적다면 링크드 리스트를 사용하는 것이 맞다.**
            - 트리는 기본적으로 메모리 사용량이 많기 때문이다. 데이터 개수가 적을 때 Worst Case 를 살펴보면 트리와 링크드 리스트의 성능 상 차이가 거의 없다.
            - 따라서 메모리 측면을 봤을 때 **데이터 개수가 적을 때는 링크드 리스트를 사용**한다.

**Reference**

- [https://github.com/wjdrbs96/Today-I-Learn/blob/master/Java/Collection/Map/Java HashMap 동작원리.md](https://github.com/wjdrbs96/Today-I-Learn/blob/master/Java/Collection/Map/Java%20HashMap%20%EB%8F%99%EC%9E%91%EC%9B%90%EB%A6%AC.md)
- [https://hsp1116.tistory.com/35](https://hsp1116.tistory.com/35)
- [https://siahn95.tistory.com/entry/Java-HashMap-HashSet-이란-3-HashMap이란?category=861991](https://siahn95.tistory.com/entry/Java%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-HashMap-HashSet-%EC%9D%B4%EB%9E%80-4-HashSet%EC%9D%B4%EB%9E%80?category=861991#equals--%--%--%--hashCode--%---%--HashSet)
- [https://papimon.tistory.com/74](https://papimon.tistory.com/74)
- [Difference between HashSet and HashMap - Javatpoint](https://www.javatpoint.com/difference-between-hashset-and-hashmap)


[위로가기⬆](#java-collections---hash-🔍)
