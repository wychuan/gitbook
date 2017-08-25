# @WebServlet

除了可以在`web.xml`配置文件中配置`Servlet`之外，也可以使用`@WebServlet`注解来开发`Servlet`。

## 1. 使用maven构建web项目
新建maven webapp项目，项目目录结构如下：

```
src
    main
        java
            samples.servlet
            AnnotationHelloWorldServlet.java
        webapp
            WEB-INF
                web.xml
            index.jsp
pom.xml
```

## 2.pom文件添加servlet依赖
```xml
<dependencies>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
    </dependency>
</dependencies>
```

## 3. 编写Servlet类`AnnotationHelloWorldServlet`

```java
@WebServlet(name="helloWorldServlet", urlPatterns={"/helloworld"})
public class AnnotationHelloWorldServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");
        PrintWriter out = resp.getWriter();

        try {
            out.println("<html>");
            out.println("<head>");
            out.println("<title>AnnotationHelloWorldServlet</title");
            out.println("</head>");
            out.println("<body>");
            out.println("<h2>Hello World.</h2>");
            out.println("</body>");
            out.println("</html>");
        } finally {
            out.close();
        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }

    @Override
    public String getServletInfo() {
        return "HelloWorldServlet";
    }
}
```
我们可以看到，使用注解实现一个`Servlet`的过程：
1. 所有的`Servlet`都必须继承`javax.servlet.GenericServlet`，或`javax.servlet.http.HttpServlet`；
2. 重写基类的`doGet`、`doPost`等方法，当一个`get`请求或`post`请求到来时，会被映射到相应的方法。如发起一个`get`请求，会执行`doGet`方法；
3. `HttpServletRequest`和`HttpServletResponse`两个对象是`doGet`、`doPost`等方法的默认参数。这两个对象后面再具体研究；
4. 除了上述和使用web.xml配置Servlet的过程之外，在此Servlet类上添加注解`@WebServlet(name="helloWorldServlet", urlPatterns={"/helloworld"})`

## 5. 运行
运行上面的web程序，在浏览器中输入`http://localhost:8080/helloworld`，可以看到页面上显示的内容：Hello World.

## 6. 疑问

1. 注解的详细用法？
2. `Servlet`窗口是如何通过注解加载`Servlet`的？

