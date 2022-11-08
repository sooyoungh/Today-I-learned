# ch19 : JVM

**목차**

1. [JVM, JRE, JDK](ch19\_jvm.md#jvm-jre-jdk)
2. [JIT(Just-In-Time)](ch19\_jvm.md#jit-컴파일러)
3. [Hotspot](ch19\_jvm.md#Hotspot)
4. GC

## JVM JRE JDK

![image](https://user-images.githubusercontent.com/77563814/184497383-7e95d8d7-923e-45ee-9e5d-3d96f885dfe6.png)

* **JVM** (Java Virtual Machine)
  * 자바 프로그램이 수행되는 프로그램이다. 어플리케이션은 JVM 위에서 동작한다.
* **JRE** (Java Runtime Environment) :
  * (어플리케이션 일반 사용자들을 위한) 실행을 위한 환경
  * 자바 클래스 라이브러리(Java class libraries) + 자바 가상 머신(JVM)
* **JDK** (Java Develope Kit)
  * 개발자를 위한 환경
  * JRE에 추가로 자바 프로그램을 개발하는 데 필요한 컴파일러, 디버거와 같은 명령어행 개발 도구도 포함된다.

> **자바는** **OS에 독립적이다**
>
> * 어떤 OS로 접근해도 JAVA를 사용하려면 JVM를 통해서 동작하기 때문에 (즉, OS위에 JRE/JDK를 다운 받아서 동작시킴) OS에 독립적이게 코딩할 수 있다. **(Write Once Run Anywhere)**
> * c/c++등 자바 이전의 프로그래밍 언어들의 컴파일러는 기계어 코드로 변환할 때 프로그램이 실행될 대상 컴퓨터의 cpu와 운영체제에 종속적인 코드로 변환하는 과정을 거친다.

## JIT 컴파일러

> Interpreter (인터프리트) 방식 : 소스코드를 컴파일시에 변환하지 않고, 프로그램을 실행할 때(런타임 시)마다 변환 → 간편하나 속도가 느림 Static Complie (정적 컴파일) 방식 : 실행 전에 전부 기계어로 변환한다.

### **JIT (Just-In-Time)란**

* 자바에서 사용하는 바이트코드를 기계어로 변환하는 방식으로, 인터프리트와 정적 컴파일 방식을 혼합한 방식이다.
* 인터프리터에 의해 새로운 코드를 기계어로 변환하는 작업을 계속하고 + 한번 변환된 코드는 캐시에 저장하였다가 필요시 재사용한다.
  * 따라서 반복적으로 수행되는 코드는 빠르게 수행되고, 처음 시작하는 코드는 변환해야하므로 속도가 느리다.
* 동적 변환이라고도 하며, Java와 C#에서 사용한다.

### JVM **동작 과정 - 여기서 JIT는 언제?**

![image](https://user-images.githubusercontent.com/77563814/184497392-e7d6ccae-086c-473f-baf4-5ae28a14862f.png)

1. 자바 소스(.java)를 자바 컴파일러를 통해 자바 바이트 코드(.class)로 컴파일한다.
2. 자바는 클래스 파일을 **동적으로 읽어온다**. 즉, JVM이 동작하다가 클래스 파일을 참조하는 순간 동적으로 읽어서 메모리에 로드한다.
3. 실행 엔진(Execution Engine)은 JVM 메모리(Runtime Data Area)에 올라온 바이트 코드들을 명령어 단위로 하나씩 가져와서 실행합니다.

> 위 과정에서 JVM의 Execution Engine이 바이트코드를 기계어로 변환하는 방식이 JIT 컴파일러 방식이다.

## Hotspot

* Hotspot JVM은 성능이 저하가 될만한 부분(HotSpot)을 찾고, 해당 부분에서 JIT 컴파일러 방식을 사용한다.
* 이때 기계어로 변환하는 방법은 Client와 Server 두 가지가 있다.
  * HotSpot 클라이언트 컴파일러
    * CPU 코어가 하나뿐인 사용자를 위해 만들어졌다.
    * 애플리케이션 시작 시간을 빠르게하고, 적은 메모리를 점유하도록 한다.
  * HotSpot 서버 컴파일러
    * 코어가 많은 장비에서 애플리케이션을 돌리기 위해 사용한다.
    * 애플리케이션 수행 속도에 초점이 맞춰져 있다.
