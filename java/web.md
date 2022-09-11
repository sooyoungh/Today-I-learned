# 자바와 웹프로그래밍

## **JSP(Java Server Page)**

- **html 코드 안에 java 언어**가 있는 확장자가 `.jsp`인 파일
```java
// Java program to show servlet example
// Importing required Java libraries
import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;
  
// Extend HttpServlet class
public class AdvanceJavaConcepts extends HttpServlet 
{ 
   private String output;
    
   // Initializing servlet 
   public void init() throws ServletException 
   {
      output = "Advance Java Concepts";
   }
  
   // Requesting and printing the output
   public void doGet(HttpServletRequest req, 
                    HttpServletResponse resp)
      throws ServletException, IOException 
      {
         resp.setContentType("text/html");
         PrintWriter out = resp.getWriter();
         out.println(output);
      }
}
```

## **Servlet**

- java 코드 안에 html 코드가 있는 확장자가 `.java`인 파일
- `HttpServlet`를 상속받은 자바 클래스
```html
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title> Hello World - JSP tutorial </title>
</head>
<body>
    <%= "Hello World!" %>
</body>
</html>
```


## MVC

- **Model** : 비즈니스 영역의 로직 처리
- **View** : 화면단 출력 처리
- **Controller** : Model과 View를 컨트롤, 흐름 제어 담당

모델과 뷰를 나누어 각각의 역할에 집중할 수 있도록 분리하고, 어플리케이션 흐름 제어는 컨트롤러가 담당한다.

> MVC1은 JSP가 비즈니스로직과 화면 출력을 담당한다. 로직과 뷰 코드가 혼합되어 유지보수가 복잡해지기에 MVC2가 등장하였다.
> 

## MVC2 동작 과정

Model, View, Controller를 분리하여 유지보수가 편리하다.

![image](https://user-images.githubusercontent.com/77563814/189526939-57855c64-3df8-442d-8746-146b096dff3e.png)


## Filter
![image](https://user-images.githubusercontent.com/77563814/189526948-a1780ad1-784b-480b-9044-75a7fbe4b72d.png)

- **필터란?**
    - **JSP/서블릿을 실행하기 전에** 요청이 올바른지 검증 혹은 접근 권한 체크를 처리할 수 있다.
    - HTTP 요청/응답을 변경할 수 있는 자바 클래스
    - 클라이언트와 자원 사이에 위치하고 있다.
    - 필터 체인으로 여러 필터를 사용할 수 있다.


