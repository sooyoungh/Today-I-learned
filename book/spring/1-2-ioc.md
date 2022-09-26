---
description: IoC의 개념과 스프링의 IoC 알아보기
---

# 1장-2 스프링의 IoC



지난 글에서 Dao를 다음 코드 수준까지 리팩토링하였다.

```java
// 클라이언트 코드(UserDaoTest)
// 여기서 구현체를 결정한다.
ConnectionMaker e = new EConnectionMaker();
UserDao dao1 = new UserDao(e);
```

하지만 이 클라이언트단(UserDaoTest)에서 UserDao의 기능만 테스트해야하는데, **ConnectionMaker 객체 생성과 해당 객체를 UserDao에 주입하는 역할까지 담당**하고 있다. 즉, 클라이언트가 관심사가 아닌 일까지 하고 있다. 따라서 역할과 책임의 분리가 필요하다. <mark style="background-color:red;">클라이언트단에서는 UserDao 객체를 사용하는 부분만 남기고, 객체를 생성하는 부분은 분리하려 한다.</mark>



#### 클라이언트 코드에서 객체 생성부를 분리해보자

> 이를 위해 객체 생성을 담당하는 팩토리 클래스를 새로 만들어야 한다.

{% hint style="info" %}
**팩토리 클래스**

객체를 **생성**하여 반환하는 클래스이다. **객체 생성부와** **객체 사용부를 분리**하기 위해 만든다.
{% endhint %}

``

객체 생성을 담당하는 `DaoFactory` 클래스를 만들었다.

```java
// IoC 적용
public class DaoFactory { // 구현 클래스 생성

	private DSimpleConnectionMaker getConnectionMaker() {
        return new DSimpleConnectionMaker();
    }

	public UserDao userDao() {
        return new UserDao(getConnectionMaker());
    }

}

// 클라이언트 코드
UserDao dao = new DaoFactory().userDao();
```

위의 코드에서는 팩토리 클래스를 통해 객체 생성을 위임했다. 비교해보면,



이전에는

`UserDao dao1 = new UserDao( new EConnectionMaker() );`

UserDao가 사용할 객체를 스스로 생성했다. 그리고 주입할 다른 객체(`EConnectionMaker`)도 직접 결정했다.



리팩토링한 코드에서는

`UserDao dao = new DaoFactory().userDao();`

UserDao가 스스로 객체를 생성하지 않고, 내부 구현을 알지 못한다. 스스로 객제 생성하고 주입받을 객체를 결정하지 않았다. 외부에서 지시를 받았다. 이러한 방식을 제어의 역전(IoC)이라고 한다. 즉, 수행부와 구현부가 분리되어 있다.





### 1. **제어의 역전(IOC)**

#### **일반적인 프로그램의 흐름**

`main()` 메소드에서부터 시작하여, 사용할 오브젝트를 결정하여 생성하고, 생성한 객체의 메소드를 호출한다. 그리고 메소드 안에서 다음 메소드를 호출하는 식의 흐름으로 프로그램이 진행된다.

****

#### **제어의 역전**

일반적인 프로그램의 흐름과 달리 **제어권을 자신이 아닌 외부에 위임한다.**

* 객체 스스로가 생성하지 않고,
* 내부 로직 혹은 사용될 지점을 알 수 없다.

즉, **프로그램의 수행**과 **구체적인 구현**을 분리하였다.

이를 통해 <mark style="background-color:red;">구현이 변경되어도 수행하는 코드(클라이언트단)는 바뀌지 않는다. 모듈간 의존성이 낮아져 변경/확장이 쉽다.</mark>

****

****

### **2. 제어의 역전(IOC)** 사용 이유 및 예시

{% hint style="warning" %}
**제어의 역전을 사용하는 이유?**\
**객체의 생성부와 구현부를 분리한다. 구현부가 변경되더라도 생성부는 일관된 방식으로 동작하게 한다. 이로 인해 변경과 확장에 열려있다.**
{% endhint %}

#### **제어의 역전 사용 예시**

* **팩토리** (위의 예시)
  * 클라이언트단에서 객체 스스로가 생성하였으나,
  * IoC 적용 시 팩토리 클래스를 통해 팩토리 객체가 대신 생성해주었다.
* **서블릿**
  * 서블릿은 개발자가 직접 제어할 수 없고, 컨트롤러를 통해 사용자의 요청에 따라 로직이 실행된다(페이지 반환 등).
* **프레임 워크** (ex. 스프링)
  * 프레임 워크는 이미 짜여진 구조에서 개발자가 원하는 로직을 구현한다. 해당 로직은 개발자가 실행하지 못하고, 프레임 워크에서 지정한 타이밍에 실행된다.





### 3. 스프링의 IoC



위에서 만들어준 Factory 클래스가 스프링에서는 `BeanFactory`에 해당한다. `BeanFactory`에서 객체를 생성한다. 이 객체를 Bean이라 하고, 클라이언트단에서 Bean을 주입하여 사용한다. `ApplicationContext`는 `BeanFactory`에서 일부 기능을 확장한 팩토리 클래스(제어의 역전이 적용된 클래스)이다. `ApplicationContext`, `BeanFactory`를 `Container`라고도 한다.



* `BeanFactor` 설정 정보

아래와 같이 설정하면 자동으로 스프링 컨테이너에 Bean을 생성하여 올린다.

```java
@Configuration
public class DaoFactory {

    @Bean 
    public UserDao userDao() {
        return new UserDao(getConnectionMaker());
    }

    @Bean
    public DSimpleConnectionMaker getConnectionMaker() {
        return new DSimpleConnectionMaker();
    }
}
```

****

* **클라이언트 코드에서 `Bean` 객체 주입하여 사용**

```java
public class UserDaoTest {
    public static void main(String[] args) throws SQLException, ClassNotFoundException {
        ApplicationContext applicationContext
                = new AnnotationConfigApplicationContext(DaoFactory.class);

        UserDao userDao = applicationContext.getBean("userDao", UserDao.class);

        User user = new User();
        user.setId("2");
        user.setName("제이크2");
        user.setPassword("jakejake");
        userDao.add(user); // 클라이언트단에서는 객체 생성X, 객체 사용

        User user2 = userDao.get(user.getId()); 
    }
}
```

이렇게 클라이언트단(**객체 사용 부분**)에서 **객체 구현부**, 즉 구체적인 로직을 알 필요가 없다. 클라이언트에서는 구현체가 바뀌더라도 일관된 방식으로 객체를 사용할 수 있다. 따라서 변경/확장이 쉽다.





