# 一、什么是 jsp，它有什么用？

- jsp 的全称是 javaserverpages。Java 的服务器页面。
- jsp 的主要作用是代替 Servlet 程序回传 html 页面的数据。
- 因为 Servlet 程序回传 html 页面数据是一件非常繁锁的事情。开发成本和维护成本都极高。

**jsp 如何访问：** 

jsp 页面和 html 页面一样，都是存放在 web 目录下。访问也跟访问 html 页面一样。

比如：在 web 目录下有如下的文件： 

web 目录 

a.html 页面 访问地址是 =======>>>>>> http://ip:port/工程路径/a.html 

b.jsp 页面 访问地址是 =======>>>>>> http://ip:port/工程路径/b.jsp

# 二、jsp 的本质是什么

- **jsp 页面本质上是一个 Servlet 程序。**
- 当我们第一次访问 jsp 页面的时候。Tomcat 服务器会帮我们把 jsp 页面翻译成为一个 java 源文件。并且对它进行编译成为.class 字节码程序。

# 三、jsp 的三种语法 

## 1、jsp 头部的 page 指令

- jsp 的 page 指令可以修改 jsp 页面中一些重要的属性，或者行为。

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200812173138.png)

i. language 属性：表示 jsp 翻译后是什么语言文件。暂时只支持 java。 

ii. contentType 属性：表示 jsp 返回的数据类型是什么。也是源码中 response.setContentType()参数值 

iii. pageEncoding 属性：表示当前 jsp 页面文件本身的字符集。 

iv. import 属性：跟 java 源代码中一样。用于导包，导类。

========================以下两个属性是给 out 输出流使用============================= 

v. autoFlush 属性：设置当 out 输出流缓冲区满了之后，是否自动刷新冲级区。默认值是 true。 

vi.buffer 属性：设置 out 缓冲区的大小。默认是 8kb

==========================================================================

vii. errorPage 属性：设置当 jsp 页面运行时出错，自动跳转去的错误页面路径。

viii. isErrorPage 属性：设置当前 jsp 页面是否是错误信息页面。默认是 false。如果是 true 可以 获取异常信息。

ix.session 属性：设置访问当前 jsp 页面，是否会创建 HttpSession 对象。默认是 true。 

x. extends 属性：设置 jsp 翻译出来的 java 类默认继承谁。

## 2、jsp 中的常用脚本

### （1）声明脚本（极少使用）

声明脚本的格式是： <%! 声明 java 代码 %> 

作用：可以给 jsp 翻译出来的 java 类定义：属性、方法、静态代码块、内部类等。

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200812173839.png)

### （2）表达式脚本（常用）

表达式脚本的格式是：<%=  表达式  %> 

表达式脚本的作用是：在 jsp 页面上输出数据。

表达式脚本的特点： 

1、所有的表达式脚本都会被翻译到_jspService() 方法中 

2、表达式脚本都会被翻译成为 out.print()输出到页面上 

3、由于表达式脚本翻译的内容都在_jspService() 方法中,所以_jspService()方法中的对象都可以直接使用。

4、表达式脚本中的表达式不能以分号结束。

练习： 1. 输出整型 2. 输出浮点型 3. 输出字符串 4. 输出对象

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200812174117.png)

### （3）代码脚本

代码脚本的格式是： <% java 语句 %> 

代码脚本的作用是：可以在 jsp 页面中，编写我们自己需要的功能（写的是 java 语句）。

代码脚本的特点是：

 1、代码脚本翻译之后都在 jspService 方法中 

2、代码脚本由于翻译到 jspService()方法中，所以在 jspService()方法中的现有对象都可以直接使用。

3、还可以由多个代码脚本块组合完成一个完整的 java 语句。 

4、代码脚本还可以和表达式脚本一起组合使用，在 jsp 页面上输出数据

练习： 1. if 语句  2. for 循环语句  3. 翻译后 java 文件中 jspService 方法内的代码都可以写

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200812174337.png)

## 3、jsp 中的三种注释

i. html 注释：html 注释会被翻译到 java 源代码中。在_jspService 方法里，以 out.writer 输出到客户端

ii. java注释：java 注释会被翻译到 java 源代码中。

iii. jsp 注释：jsp 注释可以注掉 jsp 页面中所有代码。

# 四、jsp 九大内置对象

- jsp 中的内置对象，是指 Tomcat 在翻译 jsp 页面成为 Servlet 源代码后，内部提供的九大对象，叫内置对象。

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200812174635.png)

# 五、jsp 四大域对象

四个域对象分别是： 

- pageContext (PageContextImpl 类)    当前 jsp 页面范围内有效 
- request (HttpServletRequest 类)         一次请求内有效 
- session (HttpSession 类)                     一个会话范围内有效（打开浏览器访问服务器，直到关闭浏览器） 
- application (ServletContext 类)            整个 web 工程范围内都有效（只要 web 工程不停止，数据都在）

域对象是可以像 Map 一样存取数据的对象。四个域对象功能一样。不同的是它们对数据的存取范围。

虽然四个域对象都可以存取数据。在使用上它们是有优先顺序的。

四个域在使用的时候，优先顺序分别是，他们从小到大的范围的顺序：

pageContext ====>>> request ====>>> session ====>>> application

# 六、out 和 response 输出的区别

- response 中表示响应，我们经常用于设置返回给客户端的内容（输出）

- out 也是给用户做输出使用的。

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200812175207.png)

由于 jsp 翻译之后，底层源代码都是使用 out 来进行输出，所以一般情况下。我们在 jsp 页面中统一使用 out 来进行输出。避 免打乱页面输出内容的顺序。

out.write() 输出字符串没有问题 

out.print() 输出任意数据都没有问题（都转换成为字符串后调用的 write 输出）

**深入源码，浅出结论：在 jsp 页面中，可以统一使用 out.print()来进行输出** 

# 七、jsp 的常用标签

##  1、jsp 静态包含

静态包含的特点：

1 、 静态包含不会翻译被包含的 jsp 页面 。

2 、 静态包含其实是把被包含的 jsp 页面的代码拷贝到包含的位置执行输出

```jsp
<%@ include file="/include/footer.jsp"%>

```

## 2、jsp 动态包含

```jsp
<jsp:include page="/include/footer.jsp"> 
    <jsp:param name="username" value="bbj"/> 
    <jsp:param name="password" value="root"/> 
</jsp:include>
```

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200812175906.png)

## 3、jsp 请求转发

```jsp
<jsp:forward page="/scope2.jsp"></jsp:forward>
```

# 八、什么是 Listener 监听器？

- Listener 监听器它是 JavaWeb 的三大组件之一。

  JavaWeb 的三大组件分别是：Servlet 程序、Filter 过滤器、Listener监 听器。

- Listener 它是 JavaEE 的规范，就是接口 

- 监听器的作用是，监听某种事物的变化。然后通过回调函数，反馈给客户（程序）去做一些相应的处理。

## 1、ServletContextListener 监听器

ServletContextListener 它可以监听 ServletContext 对象的创建和销毁。

ServletContext 对象在 web 工程启动的时候创建，在 web 工程停止的时候销毁。

监听到创建和销毁之后都会分别调用 ServletContextListener 监听器的方法反馈。

两个方法分别是：

```java
publicinterfaceServletContextListenerextendsEventListener{ 
/**
在 ServletContext
对 象 创 建 之 后 马 上 调 用 ， 做 初 始 化
*/ 
    publicvoidcontextInitialized(ServletContextEventsce);
    
/**
在 ServletContext
对 象 销 毁 之 后 调 用
*/ 
    publicvoidcontextDestroyed(ServletContextEventsce);
    
}
```

## 2、如何使用

如何使用 ServletContextListener 监听器监听 ServletContext 对象。 

使用步骤如下： 

1、编写一个类去实现 ServletContextListener 

2、实现其两个回调方法 

3、到 web.xml 中去配置监听器

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200812180555.png)