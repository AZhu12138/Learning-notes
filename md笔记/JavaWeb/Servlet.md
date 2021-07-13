# 一、Servlet 技术 

## 1、什么是 Servlet

- Servlet 是 JavaEE 规范之一。规范就是接口 
- Servlet 就 JavaWeb 三大组件之一。三大组件分别是：Servlet 程序、Filter 过滤器、Listener 监听器。 
- Servlet 是运行在服务器上的一个 java 小程序，它可以接收客户端发送过来的请求，并响应数据给客户端。

## 2、手动实现 Servlet 程序

(1)、编写一个类去实现 Servlet 接口 

(2)、实现 service 方法，处理请求，并响应数据 

(3)、到 web.xml 中去配置 servlet 程序的访问地址

Servlet 程序的示例代码：

```java
public class HelloServlet implements Servlet {
    @Override public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException { 
        System.out.println("Hello Servlet 被访问了"); 
    }
}
```

web.xml 中的配置： 

```xml
<!-- servlet 标 签 给 Tomcat 配 置 Servlet 程 序 -->
<servlet> 
    <!--servlet-name 标 签 Servlet 程 序 起 一 个 别 名 （ 一 般 是 类 名 ） --> 
    <servlet-name>HelloServlet</servlet-name> 
    <!--servlet-class 是 Servlet 程 序 的 全 类 名 --> 
    <servlet-class>com.atguigu.servlet.HelloServlet</servlet-class> 
</servlet> 
<!--servlet-mapping 标 签 给 servlet 程 序 配 置 访 问 地 址 --> 
<servlet-mapping> 
    <!--servlet-name 标签的作用是告诉服务器，我当前配置的地址给哪个 Servlet程序使用 -->
    <servlet-name>HelloServlet</servlet-name> 
    <!--url-pattern 标 签 配 置 访 问 地 址 <br/> 
/ 斜 杠 在 服 务 器 解 析 的 时 候 ， 表 示 地 址 为 ： http://ip:port/ 工 程 路 径<br/>
/hello表 示 地 址 为 ： http://ip:port/ 工 程 路 径 /hello <br/>--> 
    <url-pattern>/hello</url-pattern>
</servlet-mapping> 
```

- 常见的错误 1：url-pattern 中配置的路径没有以斜杠打头。
- 常见错误 2：servlet-name 配置的值不存在：
- 常见错误 3：servlet-class 标签的全类名配置错误：

## 3、url 地址到 Servlet 程序的访问

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200522231957.png)

## 4、Servlet 的生命周期

（1）、执行 Servlet 构造器方法 

（2）、执行 init 初始化方法

第一、二步，是在第一次访问的时候创建 Servlet 程序会调用。

（3）、执行 service 方法 

第三步，每次访问都会调用。

（4）、执行 destroy 销毁方法 

第四步，在 web 工程停止的时候调用。

## 5、GET 和 POST 请求的分发处理

```java 
public class HelloServlet implements Servlet {
    @Override public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException { 
        System.out.println("3 service === Hello Servlet 被访问了"); 
        if ("GET".equals(method)) { 
            doGet(); 
        } else if ("POST".equals(method)) {
            doPost(); 
        }
    }
    /** * 做 get请 求 的 操 作*/ 
    public void doGet(){ 
        System.out.println("get 请求"); 
        System.out.println("get 请求"); 
    } 
    /** * 做 post 请 求 的 操 作 */ 
    public void doPost(){ 
        System.out.println("post 请求"); 
        System.out.println("post 请求"); 
    }
}
```

## 6、通过继承 HttpServlet 实现 Servlet 程序

一般在实际项目开发中，都是使用继承 HttpServlet 类的方式去实现 Servlet 程序。

（1）、编写一个类去继承 HttpServlet 类 

（2）、根据业务需要重写 doGet 或 doPost 方法 

（3）、到 web.xml 中的配置 Servlet 程序的访问地址

Servlet 类的代码：

```java
public class HelloServlet2 extends HttpServlet { 
    /** 
    * doGet （ ） 在 get 请 求 的 时 候 调 用 
    * @param req 
    * @param resp 
    * @throws ServletException 
    * @throws IOException 
    */ @Override protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException { 
        System.out.println("HelloServlet2 的 doGet 方法"); 
    } 
    /** 
    * doPost （ ） 在 post 请 求 的 时 候 调 用 
    * @param req 
    * @param resp 
    * @throws ServletException 
    * @throws IOException 
    */ @Override protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException { 
        System.out.println("HelloServlet2 的 doPost 方法"); 
    } 
}
```

web.xml 中的配置：

```xml
<servlet> 
    <servlet-name>HelloServlet2</servlet-name> 
    <servlet-class>com.atguigu.servlet.HelloServlet2</servlet-class> 
</servlet> 
<servlet-mapping> 
    <servlet-name>HelloServlet2</servlet-name> 
    <url-pattern>/hello2</url-pattern> 
</servlet-mapping>
```

## 7、Servlet 类的继承体系

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200523151542.png)

# 二、ServletConfig 类

- ServletConfig 类从类名上来看，就知道是 Servlet 程序的配置信息类。
- Servlet 程序和 ServletConfig 对象都是由 Tomcat 负责创建，我们负责使用。 
- Servlet 程序默认是第一次访问的时候创建，
- ServletConfig 是每个 Servlet 程序创建时，就创建一个对应的 ServletConfig对象。

## 1、ServletConfig 类的三大作用

- 可以获取 Servlet 程序的别名 servlet-name 的值 
- 获取初始化参数 init-param 
- 获取 ServletContext 对象

# 三、ServletContext 类 

## 1、什么是 ServletContext?

（1）、ServletContext 是一个接口，它表示 Servlet 上下文对象 

（2）、一个 web 工程，只有一个 ServletContext 对象实例。 

（3）、ServletContext 对象是一个域对象。 

（4）、ServletContext 是在 web 工程部署启动的时候创建。在 web 工程停止的时候销毁。

- 什么是域对象? 域对象，是可以像 Map 一样存取数据的对象，叫域对象。 

- 这里的域指的是存取数据的操作范围，整个 web 工程。


​                   存数据               取数据                 删除数据 

Map             put()                   get()                    remove() 

域对象       setAttribute()     getAttribute()        removeAttribute();

## 2、ServletContext 类的四个作用

（1）、获取 web.xml 中配置的上下文参数 context-param 

（2）、获取当前的工程路径，格式:/工程路径 

（3）、获取工程部署后在服务器硬盘上的绝对路径 

（4）、像 Map 一样存取数据

ServletContext 演示代码：

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException { 
    // 1 、 获 取 web.xml 中 配 置 的 上 下 文 参 数 
    String username = context.getInitParameter("username"); 
    System.out.println("context-param 参数 username 的值是:" + username); 
    System.out.println("context-param 参数 password 的值是:" + context.getInitParameter("password"));
    // 2 、 获 取 当 前 的 工 程 路 径 ， 格 式 : / 工 程 路 径
    System.out.println( "当前工程路径:" + context.getContextPath() ); 
    // 3 、 获 取 工 程 部 署 后 在 服 务 器 硬 盘 上 的 绝 对 路 径 
    /** 
    * /斜杠被服务器解析地址为 :http://ip:port/工程名/映射到IDEA代码的 web目录
    */ 
    System.out.println("工程部署的路径是:" + context.getRealPath("/")); 
    System.out.println("工程下 css 目录的绝对路径是:" + context.getRealPath("/css"));
    System.out.println("工程下 imgs 目录 1.jpg 的绝对路径是:" + context.getRealPath("/imgs/1.jpg"));
}
```

ServletContext 像 Map 一样存取数据：

ContextServlet1 代码：

```java
public class ContextServlet1 extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException { 
        // 获 取 ServletContext 对 象 
        ServletContext context = getServletContext(); System.out.println(context);
        System.out.println("保存之前: Context1 获取 key1 的值是:"+ context.getAttribute("key1"));
        context.setAttribute("key1", "value1");
        System.out.println("Context1 中获取域数据 key1 的值是:"+ context.getAttribute("key1"));
    }
}
```

ContextServlet2 代码：

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException { 
    ServletContext context = getServletContext();
    System.out.println(context); 
    System.out.println("Context2 中获取域数据 key1 的值是:"+ context.getAttribute("key1"));
}
```

# 四、HttpServletRequest 类 

## 1、有什么作用

每次只要有请求进入 Tomcat 服务器，Tomcat 服务器就会把请求过来的 HTTP 协议信息解析好封装到 Request 对象中。 然后传递到 service 方法（doGet 和 doPost）中给我们使用。

我们可以通过 HttpServletRequest 对象，获取到所有请求的 信息。

## 2、常用方法

- getRequestURI() 获取请求的资源路径 
- getRequestURL() 获取请求的统一资源定位符（绝对路径） 
- getRemoteHost() 获取客户端的 ip 地址 
- getHeader() 获取请求头 
- getParameter() 获取请求的参数 
- getParameterValues() 获取请求的参数（多个值的时候使用） 
- getMethod() 获取请求的方式 GET 或 POST 
- setAttribute(key, value); 设置域数据 
- getAttribute(key); 获取域数据 
- getRequestDispatcher() 获取请求转发对象

## 3、如何获取请求参数

```java
@Override protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException { 
    // 获 取 请 求 参 数 
    String username = req.getParameter("username"); 
    String password = req.getParameter("password"); 
    String[] hobby = req.getParameterValues("hobby");
    System.out.println("用户名：" + username); 
    System.out.println("密码：" + password); 
    System.out.println("兴趣爱好：" + Arrays.asList(hobby));
}
```

## 4、POST 请求的中文乱码解决

```java
@Override protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException { 
    // 设 置 请 求 体 的 字 符 集 为 UTF-8 ， 从 而 解 决 post 请 求 的 中 文 乱 码 问 题
    req.setCharacterEncoding("UTF-8"); System.out.println("-------------doPost------------"); 
    // 获 取 请 求 参 数 
    String username = req.getParameter("username"); 
    String password = req.getParameter("password"); 
    String[] hobby = req.getParameterValues("hobby");
    System.out.println("用户名：" + username); 
    System.out.println("密码：" + password); 
    System.out.println("兴趣爱好：" + Arrays.asList(hobby));
}
```

## 5、请求的转发

什么是请求的转发? 请求转发是指，服务器收到请求后，从一次资源跳转到另一个资源的操作叫请求转发。

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200528143803.png)

## 6、base 标签

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200528150945.png)

## 7、Web 中的相对路径和绝对路径

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200820223953.png)

## 8、web 中 / 斜杠的不同意义

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200820224040.png)

# 五、HttpServletResponse 类 

## 1、HttpServletResponse 类的作用

HttpServletResponse 类和 HttpServletRequest 类一样。每次请求进来，Tomcat 服务器都会创建一个 Response 对象传 递给 Servlet 程序去使用。HttpServletRequest 表示请求过来的信息，HttpServletResponse 表示所有响应的信息， 我们如果需要设置返回给客户端的信息，都可以通过 HttpServletResponse 对象来进行设置

## 2、两个输出流的说明。

- 字节流 getOutputStream(); 常用于下载（传递二进制数据） 
- 字符流 getWriter(); 常用于回传字符串（常用）
- 两个流同时只能使用一个。 使用了字节流，就不能再使用字符流，反之亦然，否则就会报错。

## 3、如何往客户端回传数据

```java
public class ResponseIOServlet extends HttpServlet { 
    @Override protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException { 
        // 要 求 ： 往 客 户 端 回 传 字 符 串 数 据 。 
        PrintWriter writer = resp.getWriter(); 
        writer.write("response's content!!!"); 
    } 
}
```

## 4、响应的乱码解决

```java
//它 会 同 时 设 置 服 务 器 和 客 户 端 都 使 用UTF-8字符集，还设置了响应头
//此 方 法 一 定 要 在 获 取 流 对 象 之 前 调 用 才 有 效
resp.setContentType("text/html; charset=UTF-8");
```

## 5、请求重定向

请求重定向，是指客户端给服务器发请求，然后服务器告诉客户端说。我给你一些地址。你去新地址访问。叫请求重定向（因为之前的地址可能已经被废弃）。

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200529225516.png)

```java
resp.sendRedirect("http://localhost:8080");
```

