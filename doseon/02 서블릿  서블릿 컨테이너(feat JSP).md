# 서블릿이란
서블릿이란 자바로 만들어진 프로그램을 서버에서 실행하기 위해 만들어졌다.

서블릿은 클라이언트 요청에 대해 비즈니스 로직을 수행하여 클라이언트에 적절한 응답을 반환한다.

서블릿 컨테이너에 의해 실행된다.

서블릿 자체는 자바 코드로 구현되지만, 서블릿 컨테이너에 해당 클래스가 서블릿임을 알려야하고 어떤 URL접근에 실행해야 하는 지 등록과정이 필요하다(ex. web.xml).

지금은 별도의 web.xml 작성없이, 자바 애너테이션을 이용한 방법이 사용되고 있다.(@WebServlet("/매핑주소")

## 서블릿 클래스 구조

서블릿은 `javax.servlet.Servlet`인터페이스를 구현한 추상클래스인 `HttpServelt` 클래스를 상속받아 구현하는 형태이다. 

```java
public class HelloWorldServlet extends HttpServlet {
    public doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ...
    }
    
    public doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
    ...
}
```

## 서블릿 생명 주기

![image](https://github.com/user-attachments/assets/30fb28c8-7a81-4a10-a619-64daaf4500ea)

- init()
    - 초기 설정 및 리소스 준비단계
    - 서블릿이 처음 로드되고 초기화할 때 한번 호출된다.
- service()
    - 클라이언트 요청을 처리하기 위해 service()를 호출한다.
    - service()에서는 요청 메소드에 따라 doGet(), doPost()를 호출한다.
- doGet()
    - get요청을 처리합니다
- doPost()
    - post요청을 처리합니다.

## 서블릿 컨테이너

- 서블릿의 생성과 소멸을 담당
- 클라이언트의 요청을 서블릿으로 전송하고, 서블릿의 응답을 클라이언트로 전송
- 멀티스레드환경을 제공하여 동시요청을 처리
- 요청 인증, 세션 추적 등을 제공
- ex) Apache Tomcat, Jetty, WildFly(JBoss) 등등

### 웹서버 vs 서블릿 컨테이너

웹서버는 정적인 데이터를 클라이언트에 제공하는 것이 목적(ex. Nginx, Apache Http Server 등등)

서블리 컨테이너는 비즈니스로직을 수행하고 동적인 데이터를 클라이언트에 제공하는 것이 목적

## JSP(Java Server Page)

JSP는 서블릿의 화면처리에 대한 어려움으로 등장했습니다. 서블릿은 동적인 HTML을 생성하기 위해서는 자바코드의 문자열 형태로 HTML을 작성해야 했습니다. 예를 들면 아래와 같은 코드가 있습니다.

```java
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html");
        resp.setCharacterEncoding("UTF-8");

        // HTML 출력
        resp.getWriter().println("<!DOCTYPE html>");
        resp.getWriter().println("<html>");
        resp.getWriter().println("<head>");
        resp.getWriter().println("<title>서블릿 화면 처리</title>");
        resp.getWriter().println("</head>");
        resp.getWriter().println("<body>");
        resp.getWriter().println("<h1>안녕하세요, 서블릿입니다!</h1>");
        resp.getWriter().println("<p>현재 서버 시간: " + new java.util.Date() + "</p>");
        resp.getWriter().println("</body>");
        resp.getWriter().println("</html>");
    }
}
```

JSP는 HTML코드 중심으로 작성됩니다. 그리고 자바 코드는 필요할 때만 추가로 삽입합니다. 예를들면, 아래와 같은 코드가 있습니다.

```java
<!DOCTYPE html>
<html>
<head>
    <title>JSP 화면 처리</title>
</head>
<body>
    <h1>안녕하세요, JSP입니다!</h1>
    <p>현재 서버 시간: <%= new java.util.Date() %></p>
</body>
</html>

```

가독성과 유지보수성에서 서블릿보다 뛰어나며, 디자이너와의 협업에서도 디자이너가 자바코드를 깊게 이해하지않아도 된다는 장점이 생겼습니다.
