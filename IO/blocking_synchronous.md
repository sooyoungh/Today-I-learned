# Blocking/Non-Blocking, Synchronous/Asynchronous


**목차**
1. [Blocking과 Non-Blocking](#blocking과-non-blocking)
2. [Synchronous와 Asynchronous](#synchronous와-asynchronous)
3. [동기/비동기 + 블로킹/논블로킹 조합](#동기비동기--블로킹논블로킹-조합)
4. [항상 비동기 & 논블로킹이 좋을까?](#항상-비동기--논블로킹이-좋을까)




> 동기와 비동기, 블로킹과 논블로킹의 차이를 I/O와 함께 알아보자. 유저 프로세스가 I/O를 호출한 경우가 이해하기 쉬워서 예시로 사용하였다.
> 

## Blocking과 Non-Blocking

<aside>
  
    🌟 A→ B에게 일을 시켰을 때, 호출한 쪽(A)이 계속 일을 하나? 아니면 기다리나? 

       ⇒ A의 제어권을 누가 가지는 지

</aside>

- 만약 A가 B를 호출했을 때 (일을 시켰을 경우)
    - Non-Blocking : A가 B에게 일을 시키고, 바로 자기 원래 하던 일을 계속 한다.
    - Blocking : A는 B가 일을 다 할 때 까지 기다린다. B 작업이 다 끝나야 A는 자기 할일을 마저 한다.

다음은 유저 프로세스 진행중, I/O 작업이 호출되었을 경우의 그림이다. 이때 유저 프로세스는 자신의 작업을 중단하고 대기하는가? 아니면  계속 진행하는 가❔

![image](https://user-images.githubusercontent.com/77563814/186850280-98793c45-882d-4ff4-89a6-194dc198b568.png)



- Non-Blocking : 유저 프로세스가 I/O를 시키고 자신의 일을 계속 한다.
- Blocking : 유저 프로세스가 I/O 작업이 끝날때까지 중단한다.

## Synchronous와 Asynchronous

<aside>
  
    🌟 A→ B에게 일을 시켰을 때, 작업 결과를 누가 알려주는가? 

       ⇒ B의 결과값 처리를 누가 하는가?

</aside>

- 만약 A → B 호출하였을 때,
    - Synchronous는 A가 계속 결과를 확인한다.
    - Asynchronous는 B가 알려준다. A는 자기 일만 하다가 B가 알려주면 그때 처리한다.

아래는 똑같이 유저 프로세스가 I/O 작업을 요청할 경우이다. 유저 프로세스가 I/O의 결과를 기다리는 가❔

![image](https://user-images.githubusercontent.com/77563814/186850352-c3438f9e-2199-453c-b99f-9432ae30e40d.png)


- Synchronous : 유저 프로세스가 I/O 결과를 계속 기다린다. 계속해서 I/O 작업이 완료되었는 지 확인한다.
- Asynchronous : 유저 프로세스가 결과를 기다리지 않는다. I/O가 알아서 작업 후, 결과를 알림으로 알려준다. 유저 프로세스는 알림이 오면 그때 I/O 결과를 가지고 필요한 처리를 한다.

## 동기/비동기 + 블로킹/논블로킹 조합

- 동기 & 블로킹
    - A가 B를 호출할 경우, A는 대기하면서 B의 결과를 계속 물어본다(기다린다).
    - ex) 자바 콘솔창에서 입력 받는 경우
- 동기 & 논블로킹
    - A가 B를 호출할 경우, A는 자기 할일을 하면서 B의 결과를 계속 물어본다(기다린다).
    - ex) 대규모 사용자들에게 푸시 메세지를 전송할 경우
- 비동기 & 논블로킹
    - A가 B를 호출할 경우, A는 다른 일을 하면서 B가 알아서 자기 결과를 알려준다.
    - ex) ajax, 콜백함수

## 항상 비동기 & 논블로킹이 좋을까?

서비스마다 다르다고 볼 수 있다. 대부분은 비동기/논블로킹이 스레드가 일을 쉬지 않고 계속하므로 효율적이지만, 다음의 배달 서비스에서 주문하는 상황을 보자.

>  1️⃣ 지역 선택 → 2️⃣ 가게 선택 → 3️⃣ 메뉴 선택 → 4️⃣ 결제
> 
> 이 순서로 선택/검색하고 진행해야한다. 앞 단계가 끝나지 않으면, 뒷 단계를 진행할 수 없고(동기), 사용자의 선택을 기다리는 동안 할 다른 작업도 없으니 대기한다(블로킹).
> 

이는 동기적인 작업이다. 즉, 1 → 2 → 3→ 4 가 각 스텝에서의 결과값을 받아서 일해야 한다. 위 예시에서 가게를 선택하지 않으면, 메뉴를 선택할 수 없다. 이처럼 앞 단계의 결과값을 못 받으면, 어차피 다음 일을 하지 못한다. 그리고 중간중간 이러한 대기 시간이 아까워서 아예 다른 작업을 수행한다면, 그 비용이 더 비싸다. (오히려 논블로킹이라면 오버헤드가 발생한다) 차라리 사용자의 선택을 기다리는 게 더 효율적이다.

반면 웹서비스라면, 유저 프로세스에서 I/O 작업도 많이 이루어지고, 비동기적으로 여러 서비스를 오가며 사용한다. 이런 경우라면 한 스레드를 많이 활용할 수 있는 비동기/논블로킹 방식이 유리하다. 따라서 비동기/논블로킹이 스레드를 많이 활용하여 효율적이나, 동기적인 작업에서는 예외이다.

**Reference**

- [https://inpa.tistory.com/entry/👩‍💻-동기비동기-블로킹논블로킹-개념-정리#동기_&_비동기_+_블로킹_&_논블로킹_조합](https://inpa.tistory.com/entry/%F0%9F%91%A9%E2%80%8D%F0%9F%92%BB-%EB%8F%99%EA%B8%B0%EB%B9%84%EB%8F%99%EA%B8%B0-%EB%B8%94%EB%A1%9C%ED%82%B9%EB%85%BC%EB%B8%94%EB%A1%9C%ED%82%B9-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC#%EB%8F%99%EA%B8%B0_&_%EB%B9%84%EB%8F%99%EA%B8%B0_+_%EB%B8%94%EB%A1%9C%ED%82%B9_&_%EB%85%BC%EB%B8%94%EB%A1%9C%ED%82%B9_%EC%A1%B0%ED%95%A9)
- [https://jiwondev.tistory.com/262](https://jiwondev.tistory.com/262)
- [https://intrepidgeeks.com/tutorial/blocking-nonblocking-sync-async](https://intrepidgeeks.com/tutorial/blocking-nonblocking-sync-async)
- [https://velog.io/@tkadks123/Java-NIO에-대해-알아보자-22](https://velog.io/@tkadks123/Java-NIO%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-22)
- [https://musma.github.io/2019/04/17/blocking-and-synchronous.html](https://musma.github.io/2019/04/17/blocking-and-synchronous.html)
- [https://deveric.tistory.com/99](https://deveric.tistory.com/99)



[위로가기⬆](#blockingnon-blocking-synchronousasynchronous)


