# 1、SpringMVC简介

SpringMVC 的特点：

1. 轻量级，简单易学
2. 高效，基于请求响应的MVC框架心
3. 与Spring兼容性好，无缝结合
4. 约定优于配置
5. 功能强大：RESTful、数据验证、格式化、本地化、主题等
6. 简洁灵活

# 2、SpringMVC 执行原理

## 2.1、表层执行流程：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201013221856.png)

## 2.2、底层执行流程：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201013215909.png)

# 3、第一个 SpringMVC

## 3.1、新建一个项目（模块），添加web支持

注意：需要把 jar 包导入到 Tomcat 服务器中，否则会报【404】

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201012222057.png)

## 3.2、完善配置（Maven可能存在资源过滤问题）

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```

## 3.3、pom.xml 引入相关依赖

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.2.9.RELEASE</version>
        </dependency>
        <!-- junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
    </dependencies>
```

## 3.4、配置 web.xml

注意点：

- 注意web.xml版本问题，要最新版!
- 注册 DispatcherServlet
- 关联 SpringMVC 的配置文件
- 启动级别为1
- 映射路径为 /【不要用 /*，会404】

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--1、注册DispatcherServlet-->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--关联一个springmvc的配置文件：【servlet-name】-servlet.xml-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!--启动级别：1-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <!--/：匹配所有请求（不包括.jsp） -->
    <!--/*：匹配所有请求（包括.jsp） -->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

## 3.5、添加 SpringMVC 配置文件（springmvc-servlet.xml）

- 让IOC的注解生效
- 静态资源过滤：HTML、JS、css、图片、视频....
- MVC的注解驱动
- 配置视图解析器

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       https://www.springframework.org/schema/beans/spring-beans.xsd 
       http://www.springframework.org/schema/context 
       https://www.springframework.org/schema/context/spring-context.xsd 
       http://www.springframework.org/schema/mvc 
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--自动扫描包-->
    <context:component-scan base-package="com.zhuzhu.controller"/>
    <mvc:default-servlet-handler/>
    <mvc:annotation-driven/>

    <!--视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```

## 3.6、创建 Controller

```java
@Controller
@RequestMapping("/hello")  //父 访问路径
public class HelloController {

    @RequestMapping("/h1")  //访问路径
    public String hello(Model model) {
        // 封装数据
        model.addAttribute("msg", "Hello!SpringMVC Annotetion!");
        // 会被视图解析器处理
        return "test";
    }

}
```

## 3.7、创建视图层（test.jsp）

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
${msg}
</body>
</html>
```

## 3.8、测试运行

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201013231031.png)

## 3.9、小结

使用 SpringMVC 必须配置的三大件：

- 处理器映射器、处理器适配器、视图解析器

通常，我们只需要手动配置**视图解析器**，而处理器映射器和处理器适配器只需要**开启注解驱动**即
可，而省去了大段的xml配置。

# 4、@Controller

# 5、@RequestMapping

# 6、RestFul 风格

- Restful 就是一个资源定位及资源操作的风格。不是标准也不是协议，只是一种风格。
- 基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。

url的参数的传递问题

- 原来的风格：http://ip:端口号/工程名/add?a=1&b=2
- RestFul 风格：http://ip:端口号/工程名/add/a/b

## 6.1、方式一

```java
@RequestMapping(value = "/add/{a}/{b}", method = RequestMethod.POST)
public String addtest(@PathVariable int a, @PathVariable int b, Model model) {
    int result = a + b;
    model.addAttribute("msg", "结果01为：" + result);
    return "test";
}
```

## 6.2、方式二【推荐】

```java
@GetMapping("/add/{a}/{b}")
public String addtest02(@PathVariable int a, @PathVariable int b, Model model) {
    int result = a + b;
    model.addAttribute("msg", "结果02为：" + result);
    return "test";
}
```

# 7、结果跳转方式

## 7.1、使用SpringMVC实现（不使用视图解析器）

```java
return "redirect:/index.jsp";
```

```java
return "forward:/index.jsp";
```

## 7.2、使用视图解析器【推荐】

```xml
<!--视图解析器-->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
    <!--前缀-->
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <!--后缀-->
    <property name="suffix" value=".jsp"/>
</bean>
```

# 8、数据处理

## 8.1、提交的域名城和处理方法的参数名一致

```java
// http://localhost:8080/spring-04/user?name=xxx
@GetMapping("/user")
public String getUser(String name) {
    System.out.println(name);
    return "test";
}
```

## 8.2、提交的域名城和处理方法的参数名不一致

```java
// http://localhost:8080/spring-04/user?username=xxx
@GetMapping("/user")
public String getUser(@RequestParam("username") String name) {
    System.out.println(name);
    return "test";
}
```

## 8.3、提交了一个对象

**这种方式的前提是：要求请求提交的参数名必须和对象的属性名一致。**

```java
// http://localhost:8080/spring-04/user?userid=123&username=xxx
@GetMapping("/user")
public String getUser(User user) {
    System.out.println(user);
    return "test";
}
```

# 9、乱码问题解决

## 9.1、以前的解决方式

- 在java代码中对res和rsop进行字符编码的设置
- 自定义filter过滤器对字符集进行设置

## 9.2、使用SpringMVC的乱码过滤【推荐】

```xml
<!-- 配置Springmvc的乱码过滤 -->
<filter>
    <filter-name>encoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

# 10、JSON

## 10.1、简介

- JSON（JavaScript Object Notation，JS对象标记）是一种轻量级的数据交换格式，目前使用特别广泛。
- 采用完全独立于编程语言的**文本格式**来存储和表示数据。
- 简洁和清晰的层次结构使得JSON成为理想的数据交换语言。
- 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。

## 10.2、Controller 返回 json 数据

使用json的解析工具：

- Jackson
- Fastjson

## 10.3、使用 Jackson

需要导包：

```xml
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.10.3</version>
</dependency>
```

需要在 springmvc-servlet.xml 中配置解决 json 的乱码问题：

```xml
<!-- 解决JSON乱码问题的配置-->
<mvc:annotation-driven>
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <constructor-arg value="UTF-8"/>
        </bean>
        <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
            <property name="objectMapper">
                <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                    <property name="failOnEmptyBeans" value="false"/>
                </bean>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

Controller：

```java
//@Controller
@RestController
public class UserController {

    @RequestMapping("/j1")
    //@ResponseBody
    public String sendJson01() {
        ObjectMapper mapper = new ObjectMapper();
        User user = new User("猪头", 18, "男");
        String str = null;
        try {
            str = mapper.writeValueAsString(user);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        return str;
    }

    @RequestMapping("/j2")
    public String sendJson02() throws JsonProcessingException {
        ObjectMapper mapper = new ObjectMapper();
        User user1 = new User("猪头1", 18, "男");
        User user2 = new User("猪头2", 18, "男");
        User user3 = new User("猪头3", 18, "男");
        User user4 = new User("猪头4", 18, "男");
        List<User> userList = new ArrayList<>();
        userList.add(user1);
        userList.add(user2);
        userList.add(user3);
        userList.add(user4);
        return mapper.writeValueAsString(userList);
    }

}
```

## 10.4、使用 Fastjson

也需要导包

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.73</version>
</dependency>
```



