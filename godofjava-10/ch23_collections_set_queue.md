# ch23 : Collections - Set, Queue

**목차**
1. [Collections](#collections)
2. [Set](#set)
3. [Queue](#queue)
4. [LinkedList](#linkedlist)

## Collections

![image](https://user-images.githubusercontent.com/77563814/184531336-763b525c-7110-41cc-9171-6c6b0957be0a.png)


## Set

<aside>
  
    ✏️ 집합, 순서 없고 중복 없는 자료구조

</aside>

- 어떤 값이 존재하는 지 아닌 지만 판단할 경우에 사용
- 즉, 중복 데이터를 거르기 위해서 사용함
- HashSet
    - Set 중 가장 성능이 좋음
    - 조회하기
        - for 루프 사용
        - Iterator 객체 활용
            
            ```java
            Iterator<String> iterator = 해쉬셋.iterator();
            while(iterator.hasNext()) { // 다음이 있는 지 확인
            	iterator.next();
            }
            ```
            
- TreeSet
    - 레드블랙 트리 타입으로 값이 저장되며, HashSet보다 느림
- LinkedHashSet
    - 연결된 목록 타입으로 값이 저장, 가장 성능 안좋음

## Queue

<aside>
  
    ✏️ FIFO, 먼저 들어온 것부터 먼저 처리하는 자료구조

</aside>

- 여러 사용자들의 요청을 들어온 순서대로 처리할 때 사용한다.
- ex) 티켓팅

반대로 Stack은 한 사용자의 웹브라우저 탐색 기록을 뒤로가기 해야할 경우 사용한다.

## LinkedList

> **링크드 리스트 사용 이유?**
> 
> 
> 
> 리스트 중간 값들을 삭제/추가해주어야 하는 경우, 
> 
> 리스트/배열의 경우 shift 비용이 너무 크다. (인덱스로 접근하고, 순서가 의미가 있기에)
> 
> 반면 링크드 리스트의 경우, 중간 데이터가 삭제되면 해당 데이터의 앞/뒤 요소를 서로 연결해주면 된다. 중간에 새로운 데이터가 추가될 경우, 해당 위치의 앞/뒤 요소가 새 데이터를 가리키게 해주면 된다. 
> 
> ⇒ 이런식으로 중간에 데이터가 삭제/추가될 경우 효율적이므로 링크드리스트를 사용한다.
> 

<aside>
  
    ✏️ 노드가 데이터와 포인터를 가지고 있고, 노드의 포인터가 이전/다음 노드를 가리키는 자료구조

</aside>

- List, Queue, Deque 인터페이스를 구현한다.
- Deque도 구현하기에, 맨 앞뒤 데이터도 쉽게 처리할 수 있다.
- 초기화
    - 다른 리스트/배열처럼 처음부터 크기를 지정하지 않음
- 추가
    - 여러 인터페이스를 구현하여 메소드 종류가 많아 혼동 → add 위주로 사용할 것
    - `addFirst(Object)` : 맨 앞에 추가
    - `addLast(Object)` : 맨 뒤에 추가
    - `add(int, Object)` :  중간에 추가
- 조회 -> get 위주로 사용할 것
    - `getFirst()`
    - `getLast()`
    - `get(int)`
- 삭제 -> remove 위주로 사용할 것
    - `removeFirst()`
    - `removeLast()`
    - `remove(int)`
