# Socket Programming 알아보기

**목차**

1. [TCP/IP 소켓 프로그래밍이란](#TCPIP-소켓-프로그래밍이란)
2. [서버 소켓](#서버-소켓)
3. [클라이언트 소켓](#클라이언트-소켓)
4. [정리하면](#정리하면)


> TCP/IP 통신에서 소켓 프로그래밍으로 어떻게 클라이언트와 서버가 데이터를 주고 받는 지 정리해보았다. 소켓을 생성하고 종료하는 과정을 알아보았고, 이러한 1:1 통신을 보완하는 방법에 대해서도 정리해보았다.
> 

## TCP/IP 소켓 프로그래밍이란

<aside>
  
    🌟 소켓 프로그래밍이란,

        네트워크로 연결된 두 컴퓨터 사이에서 데이터를 주고 받는 방식을 의미한다. 클라이언트와 서버간의 1:1 양방향 통신을 한다.

</aside>

소켓 프로그래밍을 구현하는 데에는 TCP와 UDP 두가지 프로토콜이 있지만, 여기서는 주로 사용하는 TCP만 살펴보았다. UDP와의 가장 큰 차이는 **연결 지향** 프로토콜이라는 점이다. (TCP의 연결/해제 방식은 이전 장에서 살펴보았다) 클라이언트와 서버가 소켓 프로그래밍으로 연결되어 데이터를 송수신하는 과정은 다음과 같다. (여기서 클라이언트/서버는 고정된 게 아니라, 데이터를 주고/받는 쪽을 의미한다)

![a4사이즈그림_원본](https://user-images.githubusercontent.com/77563814/188360660-9deb3307-6973-49d5-857f-d43a1a60b9d8.png)

## 서버 소켓

> 클라이언트와 서버가 연결되기 위해서는 먼저 서버측 소켓이 열려있어야 한다.
> 

**서버 소켓 동작 순서**

1. `socket()` : **소켓 생성**
2. `bind()` : **소켓 바인딩**
    - IP 주소와 포트 번호를 소켓에 지정한다(바인딩한다).
    - 포트번호는 프로세스의 번호로, 서버 소켓은 일반적으로는 고정된 포트번호를 사용한다.
    - 바인딩하여 해당 소켓이 지정해준 포트 번호를 사용하게 한다.
3. `listen()` : **클라이언트의 요청 대기**
    - 해당 포트 번호로 클라이언트의 연결 요청이 올때까지 기다린다.
    - 요청을 수신하면, 수신 결과(SUCCESS)를 리턴한다.
4. `accept()` : **클라이언트와 연결 수립**
    - 클라이언트와 소켓 연결을 한다.
    - 단, 이제껏 요청확인에 사용한 Server Socket이 아니라,
    - `accept()` 로 새롭게 만들어지는 Socket이 클라이언트와 연결한다.
5. `read()`, `write()` : **데이터 송수신**
    - `accept()`로 새롭게 만든 Socket이 실제 데이터를 주고 받는 역할을 한다.
6. `close()` : **소켓 종료**
    - 이때, 요청확인에 사용한 Server Socket과 `accept()`로 새롭게 만든 Socket을 모두 종료시켜 주어야한다.
- 서버 측 코드

```java
package com. company; 
 
import java.io.*;  
import java.net.ServerSocket;  
import java.net.Socket;  
  
public class Server {  
  
  public static void main(String[] args) throws IOException {   
      Socket socket ;  
      InputStreamReader inputStreamReader ;  
      OutputStreamWriter outputStreamWriter ;  
      BufferedReader bufferedReader ;  
      BufferedWriter bufferedWriter ;  
      ServerSocket serversocket ;  
  
      serversocket = new ServerSocket(5000);  // ServerSocket 생성
  
      while (true) {  
          try {  
         
              socket = serversocket.accept();  // socket 클라이언트와 연결 수립
      
              inputStreamReader = new InputStreamReader(socket.getInputStream());  // 클라이언트에게 전송
              outputStreamWriter = new OutputStreamWriter(socket.getOutputStream());  // 클라이언트으로부터 수신
              bufferedReader = new BufferedReader(inputStreamReader);  
              bufferedWriter = new BufferedWriter(outputStreamWriter);  

              while (true){  
                  String msgFromClient = bufferedReader.readLine();  
                  System.out.println("Client: " + msgFromClient);   
                  bufferedWriter.write(" MSG Received"); 
                  bufferedWriter.newLine();  
                  bufferedWriter.flush(); 

                  if (msgFromClient.equalsIgnoreCase("BYE"))  
                  break;  
              }  
              socket.close();  // 소켓 종료
              inputStreamReader.close();  
              outputStreamWriter.close();  
              bufferedReader.close();  
              bufferedWriter.close();  

          } catch (IOException e) {  
              e.printStackTrace();  
          }  
        }  
    }  
}
```

## 클라이언트 소켓

> 클라이언트는 연결할 서버의 IP와 포트번호를 알고 있어야 한다.
> 

**클라이언트 소켓 동작 순서**

1. `socket()` : **소켓 생성**
    - 이때 소켓의 타입을 정할 수 있다. (TCP - 어떤 Stream타입일지)
2. `connect()` : **연결 요청**
    - IP주소와 포트번호를 지정하여 서버에 연결요청을 보낸다.
    - 이는 블럭 방식으로 도착하여, 요청 결과가 올때까지 실행된다.
3. `read()`, `write()` : **데이터 송수신**
    - 데이터를 주고 받을 때도 블럭방식으로 동작한다. 결과가 오기 전까지 실행된다.
    - 이때 read()는 계속 기다려야 하므로, 다른 스레드에서 실행된다.
4. `close()` : **소켓 종료**

- 클라이언트 측 코드

```java
package com. company;  
  
import java.io.*;  
import java.net.Socket;  
import java.util.Scanner;  
  
public class client {  

    public static void main(String[] args) {  
        Socket socket = null;  
        InputStreamReader inputStreamReader = null;  
        OutputStreamWriter outputStreamWriter = null;  
        BufferedReader bufferedReader = null;  
        BufferedWriter bufferedWriter = null;  
        
        try {  
            socket = new Socket("localhost", 5000);  // 소켓 생성, 서버의 IP,Port 번호
            inputStreamReader = new InputStreamReader(socket.getInputStream());  
            outputStreamWriter = new OutputStreamWriter(socket.getOutputStream());  
            bufferedReader = new BufferedReader(inputStreamReader);  
            bufferedWriter = new BufferedWriter(outputStreamWriter);  

            Scanner scanner = new Scanner(System.in);  
            while (true){  
                String msgToSend = scanner.nextLine();  
                bufferedWriter.write(msgToSend);  
                bufferedWriter.newLine();  
                bufferedWriter.flush();  
                
                System.out.println("Server: " + bufferedReader.readLine());  //printing the server message
                
                if (msgToSend.equalsIgnoreCase("BYE"))  
                    break;  
            }  
        } catch (IOException e) {  
            e.printStackTrace();  
        } finally {  
             try {  
                  if (socket != null)  
                  socket.close();  // 소켓 종료
                  if (inputStreamReader != null)  
                    inputStreamReader.close();  
                  if (outputStreamWriter != null)  
	                  outputStreamWriter.close();  
                  if (bufferedReader != null)  
	                  bufferedReader.close();  
                  if (bufferedWriter != null)  
	                  bufferedWriter.close();  
             } catch (IOException e) {  
            e.printStackTrace();  
          }  
       }  
    }  
}
```

이러한 소켓 프로그래밍은 1:1 통신 방식이다. 이 방식으로 하나의 서버에 여러 클라이언트가 접속한다면 문제가 생긴다. 서버가 여러 클라이언트를 동시에 처리할 수 없게 된다. 

한 서버 소켓이 무한 루프로 여러 클라이언트 소켓을 받을 수는 있겠지만, 이 방식으로는 교착상태가 발생할 수 있다. 여러 클라이언트와 서버가 통신하는 과정에서 서로 `read()`하려고 대기하는 상황이 생길 수 있기 때문이다. 또한 이 방식으로는 서버 소켓이 매 클라이언트를 받고 `close()` 하기 때문에, 서버가 어떤 로직을 처리하고 클라이언트에게 전달할 수 없다.

따라서 위와 같은 소켓 프로그래밍만으로는 다중 클라이언트와 통신을 하기 어렵게 된다. 이를 해결하기 위해 보조 프로세스를 활용한다. 우선 연결된 프로세스(연결된 포트번호)의 보조 프로세스를 생성한다. 그리고 클라이언트들이 보조 프로세스들끼리 통신하게 한다. TCP/IP 소켓 프로그래밍의 다중 클라이언트 통신은 다음 글에서 더 자세히 다루었다❕

## 정리하면

- TCP/IP 소켓 프로그래밍은 클라이언트와 서버간 1:1 양방향으로 데이터를 송수신한다.
- 이때 클라이언트와 서버가 각각 소켓을 생성하고 종료하는 과정에 대해 알아보았다.
- 클라이언트는 서버측 IP, Port를 통해 서버에 요청 후 데이터를 주고 받는다.
- 서버는 클라이언트를 받을 Port번호를 바인딩 후 대기한다. 요청이 오면, 연결을 성립하고 데이터를 주고 받는다. 이때, Sever socket과 Socket이 사용된다.
- 모든 소켓은 연결이 끝나면 종료해야 한다. 이런 방식으로 매 클라이언트와 서버가 1:1로 통신을 한다.
- 단, 1:1 통신이기에 하나의 서버가 여러 클라이언트를 받을 수 없다. 다중 클라이언트는 보조 프로세스를 활용한다.

**Reference**

- [코드 출처](https://www.section.io/engineering-education/socket-programming-in-java/)
- [https://recipes4dev.tistory.com/153](https://recipes4dev.tistory.com/153)
- [https://velog.io/@emplam27/CS-그림으로-알아보는-네트워크-소켓-프로그래밍과-Handshaking#포트port](https://velog.io/@emplam27/CS-%EA%B7%B8%EB%A6%BC%EC%9C%BC%EB%A1%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EB%8A%94-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EC%86%8C%EC%BC%93-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D%EA%B3%BC-Handshaking#%ED%8F%AC%ED%8A%B8port)
- [https://mingule.tistory.com/60](https://mingule.tistory.com/60)
- [https://do-my-best.tistory.com/entry/멀티-프로세스를-활용한-다중-클라이언트-TCP-통신-좀비-프로세스-삭제](https://do-my-best.tistory.com/entry/%EB%A9%80%ED%8B%B0-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4%EB%A5%BC-%ED%99%9C%EC%9A%A9%ED%95%9C-%EB%8B%A4%EC%A4%91-%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8-TCP-%ED%86%B5%EC%8B%A0-%EC%A2%80%EB%B9%84-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EC%82%AD%EC%A0%9C)
- [https://mainpower4309.tistory.com/25?category=774615](https://mainpower4309.tistory.com/25?category=774615)
- [https://kor-shin.github.io/network/socket-http-differential/](https://kor-shin.github.io/network/socket-http-differential/)
- [https://victorydntmd.tistory.com/135](https://victorydntmd.tistory.com/135)
- [https://thalals.tistory.com/14](https://thalals.tistory.com/14)



[위로가기⬆](#socket-programming-알아보기)

