# try-catch-finally와 return문

**목차**
1. [try-catch문에 return문이 있을 경우](#1-try-catch문에-return문이-있을-경우)
2. [finally 안에 return문이 있을 경우](#2-finally-안에-return문이-있을-경우)
3. [try-catch문 안에만 `return 변수;` 일 경우](#3-try-catch문-안에만-return-변수-일-경우)

    a. [return 기본 자료형](#3-a-return-기본-자료형)
    
    b. [return 참조 자료형](#3-b-return-참조-자료형)

- [정리하면](#정리하면)
- [참고 - 리턴문은 항상 있어야 한다](#참고---리턴문은-항상-있어야-한다)


> try/catch/finally문에서 finally블록은 예외 발생여부와 상관없이 항상❕ 실행된다.
> 
> 따라서 주로 스트림을 종료하거나 리소스를 해제하는 등 정리하는 용도로 사용한다.
> 
> 그럼 만약 try/catch문에 return문이 있다면, 그래도 finally 블록이 실행될까? 그리고 finally 블록에 return문이 있다면 어떻게 동작할까?
> 

## 1. try-catch문에 return문이 있을 경우

(try/catch 블록에만 있고, finally 블록에는 없는 경우)

```java
try {

  // try 로직
  return 30;
   
  } catch(Exception e) {
  
  // catch 로직
  return 40;
  
} finally {

  // finally 로직
  
}

// 예외 발생시 40, 예외 없을 시 30 반환하고 종료한다.
```

실행 순서는

1. try 혹은 catch 블록 실행하다가 return을 만나면
2. finally 블록을 실행한다.
3. 그리고 try 혹은 catch 블록의 return문 반환하며 종료된다.

## 2. finally 안에 return문이 있을 경우

(try/catch문에 return이 있던 없던 결과는 같다.)

```java
try {

  // try 로직
  return 30;
   
  } catch(Exception e) {
  
  // catch 로직
  return 40;
  
} finally {

  // finally 로직
  return 50;
  
}

// 50을 반환한다.
```

실행 순서는 

1. try 혹은 catch 블록 실행하다가 return을 만나면,
2. finally 블록 실행하다가, finally 블록의 return을 반환하며 종료된다.

(try/catch문에 return이 있더라도) **finally의 리턴문을 반환하여 정상종료된다.** 원래 try/catch문에서 return을 만나면, finally 블록을 실행하다가 다시 돌아가서 try/catch문의 return을 반환하고 종료된다. 하지만 이때 finally블록에서 return을 만나면 finally블록에서의 return을 반환하고 종료된다. (try/catch문의 return은 무시된다)

**즉, try/catch의 예외나 리턴문은 무시된다. 만약 예외가 있더라도 finally의 return문을 만나면 정상 종료된다는 의미이므로 예외가 무시된다. try/catch의 예외나 리턴문이 무시되므로 finally블록에 return문은 지양하는 것이 좋다.**

또한 만약 finally블록 안에서 또 한번 예외를 던지면(re-throw), 원래의 예외는 무시된다. 따라서 finally블록 안에서의 또다시 예외를 발생시키는 것도 지양하는 게 좋다.

---

## 3. try-catch문 안에만 `return 변수;` 일 경우

앞선 2가지 경우를 정리해보자. try/catch문에 return문이 있다면 finally 블록을 우선 실행한다. 이때 만약 finally 블록에 리턴문이 있으면, finally의 리턴을 반환하며 종료한다. 없다면 try/catch문의 리턴문을 반환하며 종료한다. 단, 위의 케이스들은 리턴값이 변수(기본/참조형)가 아닌 케이스이다( ex) `return 10;` ) . 만약 변수를 리턴할 경우는 어떨까? ( ex) `return x;` ) 

예를 들어, try/catch문에서 변수 `x` 에 2을 저장했다. 그리고 finally문에서 `x`를 10으로 변경하였다. 이때 finally블록에 return문이 있다면 그냥 x (10)을 반환하고 종료된다. 하지만 try/catch에만 return문이 있다면, finally 블록에서의 수정사항이 반영될까? 결론부터 말하면, 어떤 자료형이냐에 따라 다르다. 기본자료형은 finally에서의 수정사항이 반영되지 않고, 참조자료형은 반영된다. 

try/catch문에서 return을 만나면 임시변수에 해당 변수값(`x`)을 저장한다. 그리고 finally 블록 실행 후 try/catch문에서의 리턴값을 반환한다. 이때 기본/참조 자료형의 메모리 저장 방식이 다르기 때문에 결과가 다르다.

> 참고로 아래 두 가지 케이스는 모두 try/catch문에만 return이 있고, finally블록에는 return이 없는 경우이다. (finally 블록에 return이 있다면 무조건 finally에서 변경한 값이 그대로 리턴된다)
> 

## 3-a. return 기본 자료형

```java
...
int i = 1;

try{

  i = 2;
  System.out.println("try block, i = "+i);
  return i;
  
} catch(Exception e) {

  i = 3;
  System.out.println("catch block i = "+i);
  return i;
  
} finally {

  i = 10;
  System.out.println("finally block i = "+i);
}

// 출력
try block, i = 2
finally block i = 10
2 // 이 메소드의 최종 반환값
```

try/catch문에서의 리턴 변수값을 **임시 변수**에 저장한다. 그리고 finally 블록 실행 후 임시 변수값을 반환하며 종료된다. 즉, finally블록에서의 수정사항이 반영되지 않고, try/catch문에서의 원래 리턴값이 반환된다.

## 3-b. return 참조 자료형

```java
...
List<Object> list = new ArrayList<>();

try{

  list.add("try");
  System.out.println("try block");
  return list;
  
} catch (Exception e) {

  list.add("catch");
  System.out.println("catch block");
  return list;
  
} finally {

  list.add("finally");
  System.out.println("finally block ");
  
}

// 출력
try block
finally block 
[try, finally] // 이 메소드의 최종 반환값
```

finally블록에서의 수정사항이 리턴값에 반영되었다. 참조 자료형은 실제 데이터는 heap영역에, 주소값은 stack영역에 저장된다. 따라서 try/catch문에서 리턴문을 만났을 때 임시 변수에 주소값을 저장한다. 그리고 finally 블록에서 해당 주소값을 참조하여 데이터를 수정한다. 따라서 최종 리턴값에 finally의 수정사항이 반영되는 것이다.

## 정리하면

- try/catch문에서 return이 있으면 finally 실행 후 리턴이 반환되어 종료된다. 이때 finally에도 return이 있다면, 해당 리턴문을 반환하며 종료된다.
- ❗ **finally 블록 안에서의 return은 지양하자**. try/catch의 return이나 exception을 무시하기 때문이다. 또한 finally 블록에서 **또다시 예외를 발생시키는 것(re-throwing exceptions in finally)도 지양**하자. 이는 원래 try/catch문의 예외를 무시하기 때문이다.
- 따라서, finally블록은 **스트림을 종료하거나 리소스를 해제하는 용도로만 사용하자**❕
- try/catch문에만 `return 변수값;`이 있고 finally문에서 해당 변수를 변경하면 기본 자료형은 반영되지 않고, 참조자료형은 반영되어 리턴된다.

## 참고 - 리턴문은 항상 있어야 한다

참고로 메소드 안에 return문은 **반드시** 있어야 하므로 다음과 같은 케이스들이어야 한다. (어떤 케이스더라도 return문은 있어야 된다)

1. try/catch문에 각각 있거나,
2. try/catch문 이후에 있거나, (메소드 안에)
3. finally문에 있어야 한다.

만약 아래 4번처럼, try문에’만’ 있으면 예외 발생시 return이 없으므로 컴파일 되지 않는다.

```java
// 1) try/catch문에 각각 있는 경우
try {

  // ...
  return 10;
  
} catch (Exception e) {

  // ...
  return 20;
  
}

// 2) try/catch문 이후에 있는 경우 (메소드 안에)
try {

  // ...
  
} catch (Exception e) {

  // ...
  
}

return 10;

// 3) 3. finally문에 있는 경우
try {

  // ...
  
} catch (Exception e) {

  // ...
  
} finally {

  return 10;
  
}

// 4) 컴파일 에러! -> 예외 발생 시 리턴문이 없다.
try {

  // ...
  return 10;
  
  } catch(Exception e) {
  
  // ...
  
} finally {

  // ...
  
}
```

Reference

- [https://www.programmersought.net/article/325138902.html](https://www.programmersought.net/article/325138902.html)
- [try/catch/finally 에 각각 return이 있을 경우](https://www.scientecheasy.com/2020/09/return-statement-in-try-catch-finally-block.html/)


[위로 가기⬆](#try-catch-finally와-return문)
