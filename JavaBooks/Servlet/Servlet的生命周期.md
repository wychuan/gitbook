# Servlet的生命周期

## 1. 带着问题去学习
* `Servlet`对象的生命周期？
* `Servlet`对象什么时候创建，
* `Servlet`是否线程安全？处理每次请求的是同一个线程吗？
* `Servlet`对象什么时候销毁？
* 处理每次请求的`Servlet`对象是同一个对象吗？
* 

## 2. Servlet的生命周期

==`Servlet`对象的生命周期？==

一个`Servlet`是在web容器中运行的小java程序，通常接受并处理客户端发送的http请求，`Servlet`从创建到销毁的整个生命周期主要有三个方法：

- `init()`
- `service()`
- `destroy()`

每个`Servlet`都会实现这三个方法，并在特定的时间运行某个方法。


>This interface defines methods to initialize a servlet,to service requests, and to remove a servlet from the server.These are known as life-cycle methods and are called in the following sequence:
>* The servlet is constructed, then initialized with the init method.
>* Any calls from clients to the service method are handled.
>* The servlet is taken out of service, then destroyed with the destroy method, then garbage collected and finalized.

### 2.1 `init()`

==`Servlet`对象什么时候创建?==

`servlet`容器在`servlet`生命周期的初始化阶段调用`init`方法：
>1. 默认情况下，`Servlet`对象在第一次请求到来时创建。
>2. 可以通过web.xml文件配置Servlet 对象的创建时间，`<load-on-startup>数字</load-on-startup>`，表示服务器启动时创建，并依照数字大小按顺序创建，小数字优先加载，在`<Servlet></Servlet>`标签中使用，一般只有重要的`Servlet`才会使用这个设置。

1. **在`servlet`构造完成之后，在调用`service()`之前调用`init()`方法；**
2. **`init`方法在`servlet`的整个生命周期中仅会调用一次；**
3. **`init`方法调用成功后，`servlet`才可以接收请求。**
4. `init()`方法接收一个`ServletConfig`参数，此参数包含了`servlet`配置和初始化参数（`web.xml`文件中配置的name-value参数)
```xml
<init-param>
    <param-name>foo</param-name>
    <param-value>bar</param-value>
</init-param>
```
`Servlet`接口中`init`方法定义如下：

```java
/**
 * Called by the servlet container to indicate to a servlet that the 
 * servlet is being placed into service.
 *
 * <p>The servlet container calls the <code>init</code>
 * method exactly once after instantiating the servlet.
 * The <code>init</code> method must complete successfully
 * before the servlet can receive any requests.
 *
 * <p>The servlet container cannot place the servlet into service
 * if the <code>init</code> method
 * <ol>
 * <li>Throws a <code>ServletException</code>
 * <li>Does not return within a time period defined by the Web server
 * </ol>
 *
 *
 * @param config			a <code>ServletConfig</code> object 
 *					containing the servlet's
 * 					configuration and initialization parameters
 *
 *
 */
public void init(ServletConfig config) throws ServletException;
```

### 2.2 `service()`
在`servlet`初始化完成之后，web容器会调用`service`方法来处理客户端请求；

==`Servlet`是否线程安全？处理每次请求的是同一个线程吗？==

>* `servlets`通常运行在可以并发处理多个请求的多线程`servlet`容器中，所以在实现一个`servlet`的时候，一定要考虑线程安全，尤其是访问一些类似文件的共享资源、变量等；
>* `servlet`的`service`方法是多线程运行，但`Servlet`对象可能是同一个。

```java
@Override
public void init() throws ServletException {
    logger.info("Servlet:{} init method invoked.", this.getClass());
}

@Override
protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    logger.info("Servlet:{} service method invoked.", this.getClass());
    super.service(req, resp);
}

```
日志：
```text
21:50:39.314 [http-nio-8080-exec-6] INFO samples.servlet.HelloWorldServlet - Servlet:class samples.servlet.HelloWorldServlet service method invoked.
21:50:43.332 [http-nio-8080-exec-7] INFO samples.servlet.HelloWorldServlet - Servlet:class samples.servlet.HelloWorldServlet service method invoked.
21:50:44.244 [http-nio-8080-exec-8] INFO samples.servlet.HelloWorldServlet - Servlet:class samples.servlet.HelloWorldServlet service method invoked.
21:50:44.527 [http-nio-8080-exec-9] INFO samples.servlet.HelloWorldServlet - Servlet:class samples.servlet.HelloWorldServlet service method invoked.
21:50:44.728 [http-nio-8080-exec-10] INFO samples.servlet.HelloWorldServlet - Servlet:class samples.servlet.HelloWorldServlet service method invoked.
```
==处理每次请求的Servlet对象是同一个对象吗？==

刷新同一个页面，从上面日志可以看到：
1. 每次日志打印都是不同的线程,所以servlet是多线程处理的；
2. 没有发现`init`方法的日志，所以使用的是同一个`servlet`对象；

`Servlet`接口的`service()`方法的定义：

```java
/**
 * Called by the servlet container to allow the servlet to respond(回答) to 
 * a request.
 *
 * <p>This method is only called after the servlet's <code>init()</code>
 * method has completed successfully.
 * 
 * <p>  The status code of the response always should be set for a servlet 
 * that throws or sends an error.
 *
 * 
 * <p>Servlets typically run inside multithreaded servlet containers
 * that can handle multiple requests concurrently. Developers must 
 * be aware to synchronize access to any shared resources such as files,
 * network connections, and as well as the servlet's class and instance 
 * variables. 
 *
 * @param req 	the <code>ServletRequest</code> object that contains
 *			the client's request
 *
 * @param res 	the <code>ServletResponse</code> object that contains
 *			the servlet's response
 *
 * @exception ServletException 	if an exception occurs that interferes
 *					with the servlet's normal operation 
 *
 * @exception IOException 		if an input or output exception occurs
 *
 */

public void service(ServletRequest req, ServletResponse res)
throws ServletException, IOException;
```
在`HttpServlet`中有默认对`http`请求处理的实现，一般情况下，我们不需要重写新方法。

- `service`方法定义了所有能够处理的请求类型。
- `service`根据不同的请求类型，分发到相应的`doXXX`方法进行处理；
- 在实现一个`Servlet`方法时，必须实现处理不同请求类型的方法，如`doGet`、`doPost`等，如果没有实现，那么父类的方法将会被调用，父类一般会返回请求一个错误信息；

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp)
        throws ServletException, IOException {
    String protocol = req.getProtocol();
    String msg = lStrings.getString("http.method_get_not_supported");
    if (protocol.endsWith("1.1")) {
        resp.sendError(HttpServletResponse.SC_METHOD_NOT_ALLOWED, msg);
    } else {
        resp.sendError(HttpServletResponse.SC_BAD_REQUEST, msg);
    }
}

/**
 * Receives standard HTTP requests from the public
 * <code>service</code> method and dispatches
 * them to the <code>do</code><i>XXX</i> methods defined in 
 * this class. This method is an HTTP-specific version of the 
 * {@link javax.servlet.Servlet#service} method. There's no
 * need to override this method.
 *
 */
protected void service(HttpServletRequest req, HttpServletResponse resp)
    throws ServletException, IOException {
    String method = req.getMethod();

    if (method.equals(METHOD_GET)) {
        long lastModified = getLastModified(req);
        if (lastModified == -1) {
            // servlet doesn't support if-modified-since, no reason
            // to go through further expensive logic
            doGet(req, resp);
        } else {
            long ifModifiedSince = req.getDateHeader(HEADER_IFMODSINCE);
            if (ifModifiedSince < lastModified) {
                // If the servlet mod time is later, call doGet()
                // Round down to the nearest second for a proper compare
                // A ifModifiedSince of -1 will always be less
                maybeSetLastModified(resp, lastModified);
                doGet(req, resp);
            } else {
                resp.setStatus(HttpServletResponse.SC_NOT_MODIFIED);
            }
        }

    } else if (method.equals(METHOD_HEAD)) {
        long lastModified = getLastModified(req);
        maybeSetLastModified(resp, lastModified);
        doHead(req, resp);

    } else if (method.equals(METHOD_POST)) {
        doPost(req, resp);
        
    } else if (method.equals(METHOD_PUT)) {
        doPut(req, resp);
        
    } else if (method.equals(METHOD_DELETE)) {
        doDelete(req, resp);
        
    } else if (method.equals(METHOD_OPTIONS)) {
        doOptions(req,resp);
        
    } else if (method.equals(METHOD_TRACE)) {
        doTrace(req,resp);
        
    } else {
        //
        // Note that this means NO servlet supports whatever
        // method was requested, anywhere on this server.
        //

        String errMsg = lStrings.getString("http.method_not_implemented");
        Object[] errArgs = new Object[1];
        errArgs[0] = method;
        errMsg = MessageFormat.format(errMsg, errArgs);
        
        resp.sendError(HttpServletResponse.SC_NOT_IMPLEMENTED, errMsg);
    }
}
```

### 2.3 `destroy()`
==Servlet对象什么时候销毁？==

`Servlet`对象在长时间没有被调用或者是服务器关闭时，会调用`destroy()`方法来销毁`Servlet` 对象。如果想要在`servlet`生命周期结束时释放一些资源，如文件或网络资源等，可以调用这个方法。

**`destroy`方法和`init`方法一样，在`servlet`的整个生命周期内只能调用一次；**

在`Servlet`接口中`destroy`方法的定义：

```java
/**
 *
 * Called by the servlet container to indicate to a servlet that the
 * servlet is being taken out of service.  This method is
 * only called once all threads within the servlet's
 * <code>service</code> method have exited or after a timeout
 * period has passed. After the servlet container calls this 
 * method, it will not call the <code>service</code> method again
 * on this servlet.
 *
 * <p>This method gives the servlet an opportunity 
 * to clean up any resources that are being held (for example, memory,
 * file handles, threads) and make sure that any persistent state is
 * synchronized with the servlet's current state in memory.
 *
 */

public void destroy();
```

# 总结

此文主要讲了一个`servlet`在web容器中从创建到销毁的整个生命周期，总结如下：

- `servlet`生命周期主要有三个阶段，分别是初始化(会调用`init`方法)、处理请求（调用`service`方法)、销毁(调用`destroy`方法)；
- `servlet`的`init`方法和`destroy`方法只会调用一次；
- 在实现`servlet`的`doXXX`方法的时候，一定要注意线程安全，因为`servlet`容器一般都会并行处理多个请求；
- `servlet`的`service`会根据请求类型(method)分别调用不同的`doXXX`方法，如请求类型为`GET`则会调用`doGet`方法，我们通常不需要重写`service`方法，但需要实现`doXXX`方法；

# 遗留问题

- ==`init`方法接受一个`ServletConfig`参数，此类型是什么东东？==
- ==`service`方法接收了`HttpServletRequest`和`HttpServletResponse`两个参数，这两个参数的用法研究一下；==
- ==我们上面一直说的`servlet`容器或叫web容器，会调用`Servlet`生命周期中的这三个方法，那么`servlet`容器(如tomcat)究竟是如何实现的？==


