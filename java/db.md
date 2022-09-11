# 자바와 DB - JDBC, 커넥션풀

**목차**
1. [JDBC란](#jdbc란)
    - [JDBC가 생긴 이유?](#jdbc가-생긴-이유)
2. [JDBC 동작 과정](#jdbc-동작-과정)
3. [Statement와 Prestatement](#statement와-prestatement)
    - [Statement보다 Prestatement을 사용하는 이유?](#statement보다-prestatement을-사용하는-이유)
4. [DB connection pool](#db-connection-pool)
    - [DB connection pool 사용하는 이유?](#db-connection-pool-사용하는-이유)



## JDBC란

<aside>
  
    🌟 자바에서 DB에 독립적이게 DB 작업(접속/CRUD)을 할 수 있는 자바 인터페이스이다.

DBMS(Oracle, MySQL 등)의 종류에 상관없이 하나의 JDBC API를 이용해서 데이터베이스 작업을 처리할 수 있다. → DB별 JDBC 드라이버를 통해

</aside>

> ### JDBC가 생긴 이유?
> 
> 이전에는 데이터베이스의 종류마다 각각의 SQL문을 사용하였다. DB의 종류에 따라 SQL문(데이터베이스 방언)의 작성 방법이 달라서 구현이 불편했다. 따라서 DBMS 종류에 독립적이게 동일한 CRUD를 구현할 수 있도록 JDBC를 만들었다. (마치 JVM 처럼)

## JDBC 동작 과정

간단하게는 아래의 과정을 거친다.

`DB와 연결 → DB에 SQL 전송 → (DB에서 처리) → DB에서 결과 전송 → 연결 종료`

```java
String driverPath = "net.sourceforge.jtds.jdbc.Driver"; 
String address = "jdbc:jtds:sqlserver://IP/DB";
String userName = "user";
String password = "password";
String query = "SELECT ... where id = ?";
try {
 Class.forName(driverPath); // 1. JDBC 드라이버 로딩
 Connection connection = DriverManager.getConnection(address, userName, password); // 2. 커넥션 생성
 PreparedStatement ps = con.prepareStatement(query); // 3. 쿼리문 실행
 ps.setString(1, id);
 ResultSet rs = get.executeQuery(); // 4. 결과
 while (rs.next()) {
		// 결과로 로직 처리
	}
} catch (Exception e) { }
} finally {
 rs.close(); // 6. 결과 종료
 ps.close(); // 7. 쿼리문 종료
	connection.close() // 8. 커넥션 종료
}
```

1. DB 서버 접속을 위해 JDBC 드라이버를 로드한다.
2. DB Connection 객체를 생성한다**(매 연결 시 Connection 객체를 생성해야함)**.
3. 쿼리를 수행하기 위한 PreparedStatement 객체를 생성한다.
4. executeQuery를 수행하고 ResultSet로 반환받는다.
5. 그 결과로 ResultSet 객체를 받아서 데이터를 처리한다.
6. ResultSet를 종료한다.
7. 실행한 쿼리문 Statement를 종료한다.
8. JDBC Connection 종료한다.

## Statement와 Prestatement

- Statement이란
    - 정적 쿼리문으로, 쿼리에 값이 미리 입력되어야 한다.
    - `executeQuery()`/`executeUpdate()`를 실행하는 시점에 컴파일되어 실행한다.
        
        ```java
        String query = "INSERT INTO persons(id, name) VALUES(" + personEntity.getId() + ", '"+ personEntity.getName() + "')";
        
        Statement statement = connection.createStatement();
        statement.executeUpdate(query);
        ```
        

- Prestatement이란
    - 동적 쿼리문으로, 쿼리에 값이 런타임에 결정되어도 된다.
    - SQL쿼리의 틀을 미리 컴파일 해놓고, `placeholder(?)`값이 나중에 지정되면 그떄 실행한다.
    - 동일한 SQL문을 특정 파라미터만 바꿔서 여러 번 실행시켜야할 때 효율적이다.
        
        ```java
        String query = "INSERT INTO persons(id, name) VALUES( ?, ?)";
        
        PreparedStatement preparedStatement = connection.prepareStatement(query);
        preparedStatement.setInt(1, personEntity.getId());
        preparedStatement.setString(2, personEntity.getName());
        preparedStatement.executeUpdate();
        ```
        

> ### Statement보다 Prestatement을 사용하는 이유?
> 
> 1. 특수 기호를 쉽게 처리할 수 있다.
> 
>     Statement는 ‘는 두번 써주어야하는 등 특수 기호 처리가 복잡하였다. Prestatement는 알아서 파싱해주므로 쉽게 처리할 수 있다.
> 
> 2. Prestatement는 캐시를 사용한다.
>   
>     반복 수행하는 쿼리일 경우 효율적이다. Prestatement는 처음 컴파일 후 캐시에 보관한다. 값을 지정하여 실행 시 재사용한다. DB에 부하가 적고 성능이 좋다.

## DB connection pool

DB connection pool이란 데이터베이스에 접근하기 위한 Connection들을 모아두는 공간을 의미한다.

<aside>
  
    🌟 DB connection pool을 사용하면,

**웹서버가 DB서버에 접근할 때** DB connection pool의 미리 생성되어 있는 **DB Connection 객체를 가져다 쓰고 반환한다.**

</aside>

> ### DB connection pool 사용하는 이유?
> 
> 매 연결마다 Connection 객체를 생성하고 소멸시키는 비용/시간을 줄일 수 있다. 또한, DB에 접근하는 Connection의 수를 제한하여, 메모리와 DB에 걸리는 부하를 조정할 수 있다.

참고) 웹 서버에서 DB서버에 최초로 연결되어 Connection 객체를 생성할 때가 가장 오래 걸린다.

**Reference**

- [https://www.baeldung.com/java-statement-preparedstatement](https://www.baeldung.com/java-statement-preparedstatement)


[위로가기⬆](#자바와-db---jdbc-커넥션풀)


