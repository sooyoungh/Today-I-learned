# Java Collections - List 🔍

**목차**
1. [배열과 ArrayList의 차이](#배열과-arraylist의-차이)
2. [`LinkedList` vs `ArrayList`](#linkedlist-vs-arraylist)

> List 인터페이스를 구현한 `ArrayList`, `LinkedList`에 대해 정리해보았다. ⇒ 기본적인 개념 정리는 여기로(자바의 신)
> 

![image](https://user-images.githubusercontent.com/77563814/186736656-107127de-13ad-46fc-9216-c8f929b7e743.png)

## 배열과 ArrayList의 차이

- **배열** : `String[] weeks = new String[10];` // 크기 없으면 컴파일 에러
- **ArrayList** : `ArrayList list = new ArrayList();` // 크기 없어도 되고 있어도 됨

배열과 ArrayList의 차이점은 **크기가 고정적/가변적**이라는 점이다.

ArrayList는 선언시에 크기를 정해줄 수 있지만, 원소를 추가하면서 해당 크기가 넘을 경우 동적으로 크기를 늘려준다. (ArrayList는 선언시에 크기를 안 정해주어도 된다.)

[참고](https://zorba91.tistory.com/287) → **실제로는 가지고 있던 용량이 꽉 찼을 때, 용량이 기존의 1.5배를 늘린 새로운 배열에 기존 배열을 copy하는 것이다.**

## LinkedList vs ArrayList

- ArrayList : 확장 가능한 ***배열*** (***순서***가 있는 자료 구조)
- LinkedList : 노드가 데이터와 포인터를 가지고 있고 양방향(앞/뒤 노드)으로 가리키는 연결 리스트이다.

`ArrayList`는 (동적으로 확장가능한) **배열**이다. 따라서 조회시 인덱스로 접근한다. 즉, 조회는 `O(1)`이다. 반면 삭제나 삽입은 복잡해진다. `ArrayList`는 순서가 있는 배열이다. 만약 어떤 노드를 삭제하면, 뒤의 노드들을 한 칸씩 앞으로 shift해주야 한다. 새로운 노드를 중간에 삽입하면, 뒤의 노드들을 한칸씩 뒤로 shift 해주어야 한다. 따라서 삽입/삭제 시 O(n)이 걸린다. 

`LinkedList`는 중간에 데이터를 삽입/삭제할 때 유리하다.(`O(1)`) 순서가 있지 않고 각 노드들이 앞/뒤 노드를 가리키기 때문이다. 한 노드를 삭제/삽입하면 해당 노드의 앞/뒤 노드들의 포인터를 수정해주면 된다. 반면 조회는 느리다. 인덱스로 접근하는 배열구조가 아니기에, 앞에서 부터 순차적으로 접근해주어야 하기 때문이다. (`O(n)`) 

단! `LinkedList`는 삽입/삭제 자체는 `O(1)`가 맞지만, 데이터를 조회 후 삽입/삭제해야된다면 시간복잡도는 달라진다. 그때는 조회 시의 시간복잡도를 그대로 가져간다. (`O(n)`) 즉, `LinkedList`는 **맨 앞** 데이터를 삽입/삭제 할 경우에만 `O(1)`이다.

결론적으로 조회가 많고 순차적으로 데이터를 삽입/삭제할 경우, `ArrayList`를 사용하고

중간 순서에 삽입/삭제가 많을 경우, `LinkedList`를 사용해주는 게 좋다.

|  | ArrayList  | LinkedList |
| --- | --- | --- |
| 조회 (get) | O(1) | O(n) |
| 추가 (add) | O(1) | 맨 앞이면 O(1), 조회 필요 시 O(n) |
| 삭제 (remove) | O(n) | 맨 앞이면 O(1), 조회 필요 시 O(n) |
