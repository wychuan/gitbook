# 编写你的第一个Servlet

## 0. 带着问题来学习。
1. 什么是`Servlet`?定义？
2. 什么是`Servlet`容器？

## 1. 使用maven构造web项目
新建maven webapp项目，项目目录结构如下：

```
src
    main
        java
            samples.servlet
                HelloWorldServlet.java
        webapp
            WEB-INF
                web.xml
            index.jsp
pom.xml
```

## 2. pom 文件中添加`javax.servlet-api`依赖
```xml
<dependencies>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
    </dependency>
</dependencies>
```

## 3. 编写Servlet类`HelloWorldServlet.java`
`HelloWorldServlet`类是我们编写的第一个`Servlet`，它的功能非常简单，输出一句Hello World.

```java
public class HelloWorldServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8");
        PrintWriter out = resp.getWriter();

        try {
            out.println("<html>");
            out.println("<head>");
            out.println("<title>HelloWorld servlet</title");
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
我们可以看到，实现一个`Servlet`的过程：
1. 所有的`Servlet`都必须继承`javax.servlet.GenericServlet`，或`javax.servlet.http.HttpServlet`；
2. 重写基类的`doGet`、`doPost`等方法，当一个`get`请求或`post`请求到来时，会被映射到相应的方法。如发起一个`get`请求，会执行`doGet`方法；
3. `HttpServletRequest`和`HttpServletResponse`两个对象是`doGet`、`doPost`等方法的默认参数。这两个对象后面再具体研究；

## 4. 配置web.xml
上述我们写了自己的第一个`HelloWorldServlet`类，它继承自`HttpServlet`类。要把此类运行起来，我们需要在`web.xml`文件中配置`servlet`：

```xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
    <display-name>Archetype Created Web Application</display-name>
    <servlet>
        <servlet-name>helloWorldServlet</servlet-name>
        <servlet-class>samples.servlet.HelloWorldServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>helloWorldServlet</servlet-name>
        <url-pattern>/helloworld</url-pattern>
    </servlet-mapping>

    <welcome-file-list>
        <welcome-file>/helloworld</welcome-file>
    </welcome-file-list>
</web-app>
```

web.xml中的`<servlet></servlet>`用来声明一个`servlet`的数据，它的主要元素有：
- `<servlet-name></sevlet-name>`: 指定servlet的名称
- `<servlet-class></servlet-class>`:指定servlet的类名称
- `<jsp-file></jsp-file>` 指定web站台中的某个JSP网页的完整路径
- `<init-param></init-param>` 用来定义参数，可有多个`init-param`。在servlet类中通过`getInitParamenter(String name)`方法访问初始化参数
- `<load-on-startup></load-on-startup>`指定当Web应用启动时，装载Servlet的次序。当值为正数或零时：`Servlet`容器先加载数值小的servlet，再依次加载其他数值大的servlet。当值为负或未定义：`Servlet`容器将在Web客户首次访问这个servlet时加载它。

`<servlet-mapping></servlet-mapping>` 用来定义servlet所对应的URL，包含两个子元素：
- `<servlet-name></servlet-name>` 指定servlet的名称
- `<url-pattern></url-pattern>` 指定servlet所对应的URL

```xml
<!-- 基本配置-->
<servlet>
    <servlet-name>snoop</servlet-name>
    <servlet-class>SnoopServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>snoop</servlet-name>
    <url-pattern>/snoop</url-pattern>
</servlet-mapping>
<!-- 高级配置-->
<servlet>
    <servlet-name>snoop</servlet-name>
    <servlet-class>SnoopServlet</servlet-class>
    <init-param>
        <param-name>foo</param-name>
        <param-value>bar</param-value>
    </init-param>
    <run-as>
        <description>Security role for anonymous access</description>
        <role-name>tomcat</role-name>
    </run-as>
</servlet>
<servlet-mapping>
    <servlet-name>snoop</servlet-name>
    <url-pattern>/snoop</url-pattern>
</servlet-mapping>
```
## 5. 运行
运行上面的web程序，在浏览器中输入`http://localhost:8080/helloworld`，可以看到页面上显示的内容：Hello World.

## 6. 下一节

这节是最简单的入门课，通过这次学习，我们对`Servlet`有了初步了解，懂得了如何实现一个最简单的`Servlet`小程序。

下面一步，我们研究一下`Servlet`的生命周期。

## 7. 总结
* 什么是`Servlet`?定义？
    
    一个Servlet是在web容器中运行的小java程序，通常接受并处理客户端发送的http请求
* 
