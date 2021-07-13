# 1、Spring

## 1.1、简介

- Spring：春天 ---> 给软件行业带来了春天
- 2002，首次推出了Spring框架的雏形：interface21框架
- Spring 框架即以`interface21`框架为基础，经过重新设计，并不断丰富其内涵，于2004年3月24日，发布了1.0正式版。
- **Rod Johnson**，Spring Framework创始人，著名作者。很难想象Rod Johnson的学历，真的让好多人大吃一惊，他是悉尼大学的博士，然而他的专业不是计算机，而是音乐学。
- spring理念：使现有的技术更加容易使用，本身是一个大杂烩，整合了现有的技术框架。

## 1.2、如何获取

官网：https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/index.html

GitHub：https://github.com/spring-projects/spring-framework

Maven 仓库：

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.9.RELEASE</version>
</dependency>
```

后期需要整合 MyBatis 需要用到的仓库：

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.9.RELEASE</version>
</dependency>
```

## 1.3、优点

- Spring 是一个开源的免费的框架（容器）!
- Spring 是一个轻量级的、非入侵式的框架!
- 控制反转（I0C），面向切面编程（AOP） !
- 支持事务的处理，对框架整合的支持!

**总结一句话：Spring就是一个轻量级的控制反转（I0C）和面向切面编程（AOP）的框架!**

## 1.4、组成

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201002141231.png)

# 2、IOC 理论

## 2.1、基本思想

- **以前：程序是主动创建对象，创建对象的控制权在程序猿手上。**

  一旦用户需求改变，程序员需要手动更改对象的创建（更改源代码）。

- **现在：程序不再具有主动性，而是变成了被动的接受对象。创建对象的控制权在用户手上。**

  用户需求改变，程序员不再需要更改对象的创建了。

- 这种思想，从本质上解决了问题，我们程序猿不用再去管理对象的创建了。系统的耦合性大大降低，可以更加专
  注的在业务的实现上。这就是 IOC 的原型。

## 2.2、IOC 本质

- **控制反转loC（Inversion of Control），是一种设计思想，DI（依赖注入）是实现IoC的一种方法。**也有人认为 DI 只是 loC 的另一种说法。没有IoC的程序中，我们使用面向对象编程，对象的创建与对象间的依赖关系完全硬编码在程序中，对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方，个人认为所谓控制反转就是：获得依赖对象的方式反转了。
- 采用XML方式配置Bean的时候，Bean的定义信息是和实现分离的，而采用注解的方式可以把两者合为一体，Bean的定义信息直接以注解的形式定义在实现类中，从而达到了零配置的目的。
- **控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。**
- **在Spring中实现控制反转的是IoC容器，其实现方法是依赖注入（Dependency Injection，DI）。**



# 3、第一个 Spring

## 3.1、pojo实体类

必须要有：**无参构造器** 和 **set方法**

```java
public class Hello {
    private String string;
}
```

## 3.2、beans.xml

一个 <bean> 对应一个对象的实例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           https://www.springframework.org/schema/beans/spring-beans.xsd">
    <!-- 使用Spring来创建对象，在Spring中这些都称为Bean -->
    <bean id="hello" class="com.zhuzhu.pojo.Hello">
        <property name="string" value="helloSpring"/>
    </bean>
</beans>
```

**核心：**

- id：new 对象之后的变量名
- class：new 对象的类型（全类名）
- name：属性名
- value 或 ref：通过set方法注入给属性的值
  - value：基本数据类型
  - ref：其他类型

```xml
<bean id="hello" class="com.zhuzhu.pojo.Hello">
    <property name="string" value="helloSpring"/>
</bean>
```

## 3.3、测试

```java
@Test
public void helloTest() {
    // 获取Spring的上下文对象
    ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
    // 需要的对象都在Spring的上下文对象中，可以直接从里面取出来使用
    Hello hello = (Hello) context.getBean("hello");
    // 取出来就可以用了
    System.out.println(hello.toString());
}
```

## 3.4、小结

- **控制：**谁来控制对象的创建，传统应用程序的对象是由程序本身控制创建的；使用Spring后，对象是由Spring来创建的。
- **反转：**程序本身不创建对象，而变成被动的接收对象。
- **依赖注入：**就是利用set方法来进行注入的。
- I0C是一种编程思想，由主动的编程变成被动的接收。

# 4、IOC 创建对象的方式

## 4.1、默认：使用无参构造器

如上 “**3、第一个Spring**”

## 4.2、手动：使用有参构造器

1. 方式一：通过下标赋值

   ```xml
   <!-- 方式一：通过下标赋值 -->
   <bean id="user" class="com.zhuzhu.pojo.User">
       <constructor-arg index="0" value="猪头"/>
   </bean>
   ```

2. 方式二：通过类型赋值 【不建议使用】

   ```xml
   <!-- 方式二：通过类型赋值 【不建议使用】 -->
   <bean id="user" class="com.zhuzhu.pojo.User">
       <constructor-arg type="java.lang.String" value="猪屁股"/>
   </bean>
   ```

3. 方式三：通过参数名赋值【建议使用】

   ```xml
   <!-- 方式三：通过参数名赋值 -->
   <bean id="user" class="com.zhuzhu.pojo.User">
       <constructor-arg name="name" value="猪尾巴"/>
   </bean>
   ```

## 4.3、总结

在配置文件 beans.xml 加载的时候，Spring容器管理的对象就已经实例化了（创建了）。

# 5、Spring 配置

## 5.1、别名

可以给变量起一个别名，后面就可以通过别名获取到这个变量

```xml
<alias name="user" alias="user2"/>
```

## 5.2、Bean 的配置

可以通过 name 起一个或多个别名：

```xml
<bean id="user" class="com.zhuzhu.pojo.User" name="user01,u1,u01">
    <constructor-arg name="name" value="猪尾巴"/>
</bean>
```

## 5.3、import

一般用于团队开发使用：**它可以将多个配置文件，导入合并为一个。**

对于重复的内容，Spring会自动合并。

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201002201804.png)

```xml
<import resource="beans.xml"/>
<import resource="beans02.xml"/>
```

# 6、DI 依赖注入

## 6.1、构造器注入

如上：**4、IOC 创建对象的方式**

## 6.2、Set 方式注入【重点】

- 依赖注入：Set 注入
  - 依赖：bean 对象的创建依赖于容器
  - 注入：bean 对象中的所有属性，由容器来注入

**准备基本环境：**

1. 被引用的实体类

   ```java
   public class Address {
       private String address;
   }
   ```

2. 主测试类

   ```java
   public class Student {
       private String name;
       private Address address;
       private String[] books;
       private List<String> hobbies;
       private Map<String, String> card;
       private Set<String> games;
       private String wife;
       
       
       
       
       
       
       
       
       
       
       
       
       
       private Properties info;
   }
   ```

3. beans.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
                              https://www.springframework.org/schema/beans/spring-beans.xsd">
       <bean id="student" class="com.zhuzhu.pojo.Student">
           <property name="name" value="学生猪头"/>
       </bean>
   </beans>
   ```

4. 测试

   ```java
   @Test
   public void studentTest() {
       ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
       Student student = (Student) context.getBean("student");
       System.out.println(student);
   }
   ```

**各种类型的注入使用方法：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="address" class="com.zhuzhu.pojo.Address">
        <property name="address" value="广州"/>
    </bean>

    <bean id="student" class="com.zhuzhu.pojo.Student">
        <!-- 普通值，value -->
        <property name="name" value="学生猪头"/>
        <!-- Bean，ref -->
        <property name="address" ref="address"/>
        <!-- 数组 -->
        <property name="books">
            <array>
                <value>红楼梦</value>
                <value>西游记</value>
                <value>水浒传</value>
                <value>三国演义</value>
            </array>
        </property>
        <!-- List -->
        <property name="hobbies">
            <list>
                <value>吃饭</value>
                <value>睡觉</value>
                <value>打代码</value>
            </list>
        </property>
        <!-- Map -->
        <property name="card">
            <map>
                <entry key="身份证" value="1234567891110000223"/>
                <entry key="银行卡" value="987654321000"/>
            </map>
        </property>
        <!-- Set -->
        <property name="games">
            <set>
                <value>吃鸡</value>
                <value>王者荣耀</value>
            </set>
        </property>
        <!-- null -->
        <property name="wife">
            <null/>
        </property>
        <!-- Properties -->
        <property name="info">
            <props>
                <prop key="studentNum">20209021</prop>
                <prop key="sex">男</prop>
                <prop key="group">2020</prop>
            </props>
        </property>
    </bean>

</beans>
```



## 6.3、拓展方式注入

可以使用以下两种命名空间方式注入：

- p 命名空间：底层对应于Set方式注入（实体类需要有**Set方法**和**无参构造器**）
- c 命名空间：底层对应于构造器注入（实体类需要有**有参构造器**）

**注意：使用时需要导入各自的约束**

```xml
xmlns:p="http://www.springframework.org/schema/p"
xmlns:c="http://www.springframework.org/schema/c"
```

使用：

1. beans.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:p="http://www.springframework.org/schema/p"
          xmlns:c="http://www.springframework.org/schema/c"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
                              https://www.springframework.org/schema/beans/spring-beans.xsd">
       <bean id="user01" class="com.zhuzhu.pojo.User" p:name="用户一" p:age="21"/>
       <bean id="user02" class="com.zhuzhu.pojo.User" c:name="用户二" c:age="22"/>
   </beans>
   ```

2. 测试：

   ```java
   @Test
   public void userTest() {
       ApplicationContext context = new ClassPathXmlApplicationContext("userbeans.xml");
       User user01 = context.getBean("user01", User.class);
       User user02 = context.getBean("user02", User.class);
       System.out.println(user01);
       System.out.println(user02);
   }
   ```

## 6.4、Bean 的作用域

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201003212837.png)

1. **单例模式：**（Spring默认机制）

   取出来的都是同一个

   ```xml
   <bean id="user01" class="com.zhuzhu.pojo.User" p:name="用户一" p:age="21" scope="singleton"/>
   ```

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201003213420.png)

2. **原型模式：**

   取出来的都是各自不同的。

   每次 get一个，都会创建一个新的对象实例。

   ```xml
   <bean id="user01" class="com.zhuzhu.pojo.User" p:name="用户一" p:age="21" scope="prototype"/>
   ```

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201003213452.png)

3. 其它的，都需要在web开发中用到。

# 7、Bean 的自动装配

在Spring中有三种装配的方式

1. 在xml中显式地配置
2. 在java中显式地配置
3. **隐式地自动装配 bean【重点】**
   - 自动装配是Spring满足 bean 依赖一种方式
   - Spring会在上下文中自动寻找，并自动给bean装配属性

## 7.1、基本测试环境

一个人拥有一只狗和一只猫

```java
public class Person {
    private String name;
    private Dog dog;
    private Cat cat;
}
```

beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="dog" class="com.zhuzhu.pojo.Dog"/>
    <bean id="cat" class="com.zhuzhu.pojo.Cat"/>

    <bean id="person" class="com.zhuzhu.pojo.Person">
        <property name="name" value="一个人"/>
        <property name="dog" ref="dog"/>
        <property name="cat" ref="cat"/>
    </bean>

</beans>
```

## 7.2、ByName 自动装配

会自动在容器上下文中查找，和自己对象的 **属性set方法后面的值** 相同的 **bean 的 id**

```xml
<bean id="dog" class="com.zhuzhu.pojo.Dog"/>
<bean id="cat" class="com.zhuzhu.pojo.Cat"/>

<bean id="person" class="com.zhuzhu.pojo.Person" autowire="byName">
    <property name="name" value="一个人"/>
</bean>
```

## 7.3、ByType 自动装配

会自动在容器上下文中查找，和自己对象的 **属性类型** 相同的 **bean 的 class**

```xml
<bean id="dog" class="com.zhuzhu.pojo.Dog"/>
<bean id="cat123" class="com.zhuzhu.pojo.Cat"/>

<bean id="person" class="com.zhuzhu.pojo.Person" autowire="byType">
    <property name="name" value="一个人"/>
</bean>
```

**小结：**

- byname的时候，需要保证所有bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值一致
- bytype的时候，需要保证所有bean的class唯一，并且这个bean需要和自动注入的属性的类型一致

## 7.4、使用注解实现自动装配

使用注解的须知：

1. 导入约束：

   ```xml
   xmlns:context="http://www.springframework.org/schema/context"
   xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           https://www.springframework.org/schema/context/spring-context.xsd"
   ```

2. 配置注解的支持：

   ```xml
   <context:annotation-config/>
   ```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```



### @Autowired

可以在属性上或者set方法上使用@Autowired注解，

```java
public class Person {
    @Autowired
    private Dog dog;
    @Autowired
    private Cat cat;
    private String name;
}
```

### @Qualifier

和 @Autowired 配合使用，

在 @Autowired 通过**类型**找不到的时候，使用 @Qualifier 设置名字，再通过**名字**查找，装配。

```java
public class Person {
    @Autowired
    @Qualifier(value = "dog12")
    private Dog dog;
    @Autowired
    @Qualifier(value = "cat12")
    private Cat cat;
    private String name;
}
```

### @Resource

也是一个可以实现自动装配的注解，使用方法与@Autowired类似，但是 jdk11之后不再支持了。

### @Nullable

被这个注解标记了的字段，这个字段就可以为 null

```java
public class Person {
    @Nullable
    private String name;
    @Autowired
    @Qualifier(value = "dog12")
    private Dog dog;
    @Autowired
    @Qualifier(value = "cat12")
    private Cat cat;
}
```

### 小结

@Autowired：先通过 byType 自动装配，不行再通过 byName 自动装配。

@Resource：先通过 byName 自动装配，不行再通过 byType 自动装配。

# 8、使用注解开发

在 Spring4 之后，要使用注解开发，需要导入 AOP 的包：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201004150713.png)

还要记得：

- 导入约束

- 配置注解的支持

- 扫描包，使包中的类的注解生效

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
                             https://www.springframework.org/schema/beans/spring-beans.xsd
                             http://www.springframework.org/schema/context
                             https://www.springframework.org/schema/context/spring-context.xsd">
  
      <!-- 指定要扫描的包，这个包下的注解就会生效 -->
      <context:component-scan base-package="com.zhuzhu"/>
      <context:annotation-config/>
  
  </beans>
  ```

## 8.1、Bean

@Component：这个注解放在实体类上，那么这个类就被Spring管理了。

## 8.2、属性注入

```java
@Component
@Scope("prototype")
public class User {
    @Value("猪头")//方式一
    private String name;
    
    @Value("猪头")//方式二
    public void setName(String name) {
        this.name = name;
    }
}
```

## 8.3、衍生的注解

@Component 有几个衍生的注解，对应于mvc三层架构，实质上这4个注解的功能是一样的。

- dao：【@Repository】
- service：【@Service】
- controller：【@Controller】

## 8.4、自动装配

- @Autowired
- @Qualifier
- @Resource

## 8.5、作用域

@Scope("prototype")：放在实体类上，就可以设置这个Bean的作用域

## 8.6、小结

xml 与注解：

- xml：更加万能，适用于任何场合，维护也简单方便。
- 注解：不是自己的类用不了，维护相对复杂。

两者的最佳结合：

- xml 用来管理 bean
- 注解只负责完成 属性的注入

# 9、使用 java 来配置 Spring

现在可以完全不使用 Spring 的 xml 配置，全部都使用 java 来完成。

JavaConfig 是 Spring 的一个子项目，在 Spring 4 之后，它成为了一个核心功能。

1. 实体类

   ```java
   @Component
   public class User {
       private String name;
       @Value("猪屁股")
       public void setName(String name) {
           this.name = name;
       }
       @Override
       public String toString() {
           return "User{" +
                   "name='" + name + '\'' +
                   '}';
       }
   }
   ```

2. 配置类

   ```java
   // @Configuration代表这是一个配置类，相当于Spring的一个xml文件。
   // 底层也是使用了@Component注册到容器中。
   @Configuration
   // 扫描包，使注解生效
   @ComponentScan("com.zhuzhu")
   // 导入其他配置类，自动合并为一个配置类
   @Import(AppConfig02.class)
   public class AppConfig {
   
       // 一个方法：相当于一个<bean>
       // 方法的名字：相当于<bean>中的id属性
       // 方法的返回类型：相当于<bean>中的class属性
       @Bean
       public User getUser() {
           // 返回的就是要注入到bean的对象
           return new User();
       }
   }
   ```

   ```java
   @Configuration
   public class AppConfig02 {
   }
   ```

3. 测试类

   ```java
   public class MyTest {
       @Test
       public void test01() {
           //使用了java的配置类之后，只能通过 AnnotationConfig 上下文来获取容器，通过配置类的class对象加载
           ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
           User user = context.getBean("getUser", User.class);
           System.out.println(user);
       }
   }
   ```

这种纯 Java 的配置，在 SpringBoot 中随处可见。

# 10、代理模式

**代理模式是SpringAOP的底层，面试必问：【SpringAOP 和 SpringMVC】**

代理模式的分类：

- 静态代理
- 动态代理

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201005134831.png)

## 10.1、静态代理

角色分析：

- 抽象角色：一般会使用接口或者抽象类来解决
- 真实角色：被代理的角色
- 代理角色：代理真实角色，代理真实角色之后，一般还会做一些附属操作
- 客户：访问代理对象的人

代码实现：

1. 抽象角色

   ```java
   public interface Rent {
       public void rent();
   }
   ```

2. 真实角色

   ```java
   public class Host implements Rent {
       @Override
       public void rent() {
           System.out.println("房东出租房子了！");
       }
   }
   ```

3. 代理角色

   ```java
   public class Proxy implements Rent {
       private Host host;
       public Proxy() {
       }
       public Proxy(Host host) {
           this.host = host;
       }
       // 租房
       @Override
       public void rent() {
           seeHouse();
           host.rent();
           lease();
       }
       public void seeHouse() {
           System.out.println("中介带你看房子");
       }
       public void lease() {
           System.out.println("中介和你签合同");
       }
   }
   ```

4. 客户

   ```java
   public class Client {
       public static void main(String[] args) {
           Proxy proxy = new Proxy(new Host());
           proxy.rent();
       }
   
   }
   ```

优点：

- 可以使真实角色的操作更加纯粹，不用去关注一些公共业务
- 公共业务交给代理角色，实现了业务的分工
- 公共业务发生拓展的时候，方便集中管理

缺点：

- 一个真实角色就会产生一个代理角色：代码量会翻倍，导致开发效率降低



## 10.2、动态代理

- 动态代理和静态代理的角色是一样的
- 动态代理的代理类是动态生成的，静态代理的代理类是直接写好的
- 动态代理分为两类：基于接口的动态代理，基于类的动态代理
  - 基于接口：JDK 动态代理【现在使用】
  - 基于类：cglib
  - java 字节码实现：javassist

需要了解两个类：

- Proxy（代理）
- InvocationHandler（调用处理程序）

代码实现：

在静态代理的基础上，丢弃原本的代理类，新增一个**动态代理类**：ProxyInvocationHandler

1、**ProxyInvocationHandler**：用来动态生成**我们需要的代理类**

```java
// 用这个类，来自动生成代理类
public class ProxyInvocationHandler implements InvocationHandler {
    // 被代理的接口
    private Rent rent;
    public void setRent(Rent rent) {
        this.rent = rent;
    }

    // 生成得到代理类
    public Object getProxy() {
        return Proxy.newProxyInstance(this.getClass().getClassLoader(), rent.getClass().getInterfaces(), this);
    }

    // 处理代理实例，并返回结果。
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // 动态代理的本质：就是使用反射机制实现。
        Object invoke = method.invoke(rent, args);
        return invoke;
    }
}
```

2、**Client**：通过 ProxyInvocationHandler 动态获得代理类的实例（代理对象）（代理角色）

- 通过 setRent(host) ：把我们需要的接口对象传递给 ProxyInvocationHandler，让他生成对应的代理类
- 通过 getProxy() 可以获得我们需要的代理对象
- proxy.rent() 会通过 ProxyInvocationHandler 里面的 invoke 方法执行我们需要的代理接口的方法。

```java
public class Client {
    public static void main(String[] args) {
        // 真实角色
        Host host = new Host();

        ProxyInvocationHandler proxyInvocationHandler = new ProxyInvocationHandler();
        // 通过调用程序处理角色，来处理我们要调用的接口对象
        proxyInvocationHandler.setRent(host);
        // 动态得到了代理对象
        Rent proxy = (Rent) proxyInvocationHandler.getProxy();

        proxy.rent();
    }
}
```

3、**因此**：

如果我们需要给生成的代理类增加一些业务方法，只需要在我们的 **动态代理类ProxyInvocationHandler** 中增加即可：

```java
// 用这个类，来自动生成代理类
public class ProxyInvocationHandler implements InvocationHandler {
    ......
    // 处理代理实例，并返回结果。
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        seeHouse();
        // 动态代理的本质：就是使用反射机制实现。
        Object invoke = method.invoke(rent, args);
        lease();
        return invoke;
    }
    public void seeHouse() {
        System.out.println("中介带你看房子了");
    }
    public void lease() {
        System.out.println("中介和你签合同了");
    }
}
```

4、**通用的动态代理类 ProxyInvocationHandler**

可以直接把 ProxyInvocationHandler 写成一个通用的工具类：

```java
// 用这个类，来自动生成代理类
public class ProxyInvocationHandler implements InvocationHandler {
    // 被代理的接口
    private Object target;
    public void setTarget(Object target) {
        this.target = target;
    }

    // 生成得到代理类
    public Object getProxy() {
        return Proxy.newProxyInstance(this.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
    }

    // 处理代理实例，并返回结果。
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // 动态代理的本质：就是使用反射机制实现。
        Object invoke = method.invoke(target, args);
        return invoke;
    }
}
```

优点：

除了拥有静态代理的优点：

- 可以使真实角色的操作更加纯粹，不用去关注一些公共业务
- 公共业务交给代理角色，实现了业务的分工
- 公共业务发生拓展的时候，方便集中管理

还有：

- 一个动态代理类代理的是一个接口，一般就是对应的一类业务
- 一个动态代理类可以代理多个类，只要是实现了同一个接口即可

# 11、AOP

## 11.1、AOP 是什么？

- AOP（Aspect Oriented Programming）意为：**面向切面编程**，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。
- AOP 是 OOP 的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。
- 利用 AOP 可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201005163156.png)

## 11.2、AOP 在 Spring 中的作用

**提供声明式事务；允许用户自定义切面。**

- 横切关注点：跨越应用程序多个模块的方法或功能。

  即是：与我们业务逻辑无关的，但是我们需要关注的部分，就是横切关注点。如日志，安全，缓存，事务等等······

- 切面（ASPECT）：横切关注点被模块化的特殊对象。即，它是一个类。

- 通知（Advice）：切面必须要完成的工作。即，它是类中的一个方法。

- 目标（Target）：被通知对象【真实角色】

- **代理（Proxy）：向目标对象应用 通知 之后创建的对象。**【代理角色】

- 切入点（PointCut）：切面通知 执行的“地点”的定义。

- 连接点（JointPoint）：与切入点匹配的执行点。

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201005165757.png)

## 11.3、使用 Spring 实现 AOP

### 基本环境：

【重点】使用AOP织入，需要导入依赖：

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.5</version>
</dependency>
```

UserService：

```java
public interface UserService {
    public void add();
    public void delete();
    public void update();
    public void select();
}
```

UserServiceImpl：

```java
public class UserServiceImpl implements UserService {
    @Override
    public void add() {
        System.out.println("增加用户的方法");
    }
    @Override
    public void delete() {
        System.out.println("删除用户的方法");
    }
    @Override
    public void update() {
        System.out.println("修改用户的方法");
    }
    @Override
    public void select() {
        System.out.println("查询用户的方法");
    }
}
```

applicationcontext.xml：

```xml
<bean id="userService" class="com.zhuzhu.service.UserServiceImpl"/>
```

测试：（后面的测试都不需要改变）

```java
@Test
public void test01() {
    ApplicationContext context = new ClassPathXmlApplicationContext("applicationcontext.xml");
    UserService userService = context.getBean("userService", UserService.class);
    userService.add();
    userService.select();
}
```

### 方式一：使用 Spring 的API接口实现

Log：

```java
public class Log implements MethodBeforeAdvice {
    @Override
    public void before(Method method, Object[] objects, Object o) throws Throwable {
        System.out.println(o.getClass().getName()+"的"+method.getName()+"方法，执行了！");
    }
}
```

AfterLog：

```java
public class AfterLog implements AfterReturningAdvice {
    // o ：返回值类型
    @Override
    public void afterReturning(Object o, Method method, Object[] objects, Object o1) 
        throws Throwable {
        System.out.println("执行了：" + method.getName() + "方法，返回结果为：" + o);
    }
}
```

applicationcontext.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean id="log" class="com.zhuzhu.log.Log"/>
    <bean id="afterLog" class="com.zhuzhu.log.AfterLog"/>
    <bean id="userService" class="com.zhuzhu.service.UserServiceImpl"/>

    <!-- 方式一：使用原生的Spring API接口 -->
    <!-- 注意：需要导入AOP的约束 -->
    <aop:config>
        <!-- 切入点：expression表达式，execution(要执行的位置) -->
        <aop:pointcut id="pointcut" 
                      expression="execution(* com.zhuzhu.service.UserServiceImpl.*(..))"/>
        <!-- 执行环绕增加 -->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
    </aop:config>

</beans>
```

### 方式二：使用自定义的类实现

DiyPointcut：

```java
public class DiyPointcut {
    public void before() {
        System.out.println("====方法执行前====");
    }
    public void after() {
        System.out.println("====方法执行后====");
    }
}
```

applicationcontext.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 方式二：使用自定义的类实现 -->
    <bean id="diyPointcut" class="com.zhuzhu.diy.DiyPointcut"/>
    <aop:config>
        <!-- 自定义切面，ref：要引用的类 -->
        <aop:aspect ref="diyPointcut">
            <!-- 切入点 -->
            <aop:pointcut id="point" 
                          expression="execution(* com.zhuzhu.service.UserServiceImpl.*(..))"/>
            <!-- 通知 -->
            <aop:before method="before" pointcut-ref="point"/>
            <aop:after method="after" pointcut-ref="point"/>
        </aop:aspect>
    </aop:config>

</beans>
```

### 方式三：使用注解实现

AnnotationPointcut：

```java
@Aspect
public class AnnotationPointcut {
    @Before("execution(* com.zhuzhu.service.UserServiceImpl.*(..))")
    public void before() {
        System.out.println("----方法执行前----");
    }
    @After("execution(* com.zhuzhu.service.UserServiceImpl.*(..))")
    public void after() {
        System.out.println("----方法执行后----");
    }
}
```

applicationcontext.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 方式三：使用注解实现 -->
    <bean id="annotationPointcut" class="com.zhuzhu.diy.AnnotationPointcut"/>
    <!-- 开启注解支持 -->
    <aop:aspectj-autoproxy/>

</beans>
```

# 12、整合 MyBatis

需要的jar包：

- junit
- mybatis
- mysql
- spring
- aop
- mybatis-spring

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.9.RELEASE</version>
    </dependency>
    <!-- Spring操作数据库，还需要一个spring-jdbc -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
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
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.5</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.47</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.3</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>2.0.3</version>
    </dependency>
```

## 12.1、回顾 MyBatis

1. 编写实体类
2. 编写核心配置文件
3. 编写接口
4. 编写接口的实现
5. 测试

## 12.2、MyBatis-Spring

**目的**：封装 SqlSession，使 service层调用 dao层时，可以直接调用其核心方法，而不需要关心 SqlSession 的创建。

### 方式一：

1. 编写数据源配置

2. sqlSessionFactory

3. sqlSessionTemplate

   以上 1、2、3 可以写在一个配置文件中

   spring-dao.xml

   ```xml
   <?xml version="1.0" encoding="GBK"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/aop
           https://www.springframework.org/schema/aop/spring-aop.xsd">
   
       <!-- DataSourse：使用Spring的数据源代替Mybatis的配置 -->
       <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
           <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
           <property name="url"
                     value="jdbc:mysql://localhost:3306/zhuzhumall?useSSL=false&amp;serverTimezone=GMT&amp;characterEncoding=UTF-8"/>
           <property name="username" value="root"/>
           <property name="password" value="azhu251264"/>
       </bean>
   
       <!-- sqlSessionFactory -->
       <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
           <property name="dataSource" ref="dataSource"/>
           <!-- 绑定MyBatis配置文件 -->
           <property name="configLocation" value="classpath:mybatis-config.xml"/>
           <property name="mapperLocations" value="classpath:com/zhuzhu/dao/*.xml"/>
       </bean>
   
       <!-- sqlSessionTemplate：就是我们要使用的sqlSession -->
       <bean id="sqlSessionTemplate" class="org.mybatis.spring.SqlSessionTemplate">
           <!-- 只能使用构造器注入sqlSessionFactory，因为它没有set方法。 -->
           <constructor-arg index="0" ref="sqlSessionFactory"/>
       </bean>
   
   </beans>
   ```

   以上完成之后，mybatis-config.xml 可以精简为：

   ```xml
   <?xml version="1.0" encoding="GBK" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
   
       <!-- 配置别名，直接引入包名，会自动扫描 -->
       <typeAliases>
           <package name="com.zhuzhu.pojo"/>
       </typeAliases>
   
   </configuration>
   ```

4. **给Mapper接口加实现类【重点】**

   UserMapperImpl

   **目的**：并不是要去实现 UserMapper，而是为了封装 SqlSession，使 service层调用 dao层时，可以直接调用UserMapperImpl，而不需要关心 SqlSession 的创建。

   ```java
   public class UserMapperImpl implements UserMapper {
       // 以前都使用sqlSession来执行，现在使用sqlSessionTemplate
       private SqlSessionTemplate sqlSessionTemplate;
       
       public void setSqlSessionTemplate(SqlSessionTemplate sqlSessionTemplate) {
           this.sqlSessionTemplate = sqlSessionTemplate;
       }
       
       @Override
       public List<User> selectUserList() {
           UserMapper mapper = sqlSessionTemplate.getMapper(UserMapper.class);
           return mapper.selectUserList();
       }
   }
   ```

5. 将实现类注入到Spring容器中

   applicationContext.xml

   ```xml
   <?xml version="1.0" encoding="GBK"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/aop
           https://www.springframework.org/schema/aop/spring-aop.xsd">
   
       <import resource="spring-dao.xml"/>
       <bean id="userMapperImpl" class="com.zhuzhu.dao.UserMapperImpl">
           <property name="sqlSessionTemplate" ref="sqlSessionTemplate"/>
       </bean>
   </beans>
   ```

6. 测试使用

   可以直接调用 UserMapperImpl，而不需要关心 SqlSession 的创建。

   ```java
   @Test
   public void selectUserListTest() {
       ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
       UserMapperImpl userMapperImpl = context.getBean("userMapperImpl", UserMapperImpl.class);
       List<User> userList = userMapperImpl.selectUserList();
       for (User user : userList) {
           System.out.println(user);
       }
   }
   ```

### 方式二：（对方式一的简化）

1. 编写数据源配置

2. sqlSessionFactory

3. sqlSessionTemplate（这里不需要了）

   以上 1、2 可以写在一个配置文件中

   spring-dao.xml

   ```xml
   <!-- DataSourse：使用Spring的数据源代替Mybatis的配置 -->
       <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
           <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
           <property name="url"
                     value="jdbc:mysql://localhost:3306/zhuzhumall?useSSL=false&amp;serverTimezone=GMT&amp;characterEncoding=UTF-8"/>
           <property name="username" value="root"/>
           <property name="password" value="azhu251264"/>
       </bean>
   
       <!-- sqlSessionFactory -->
       <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
           <property name="dataSource" ref="dataSource"/>
           <!-- 绑定MyBatis配置文件 -->
           <property name="configLocation" value="classpath:mybatis-config.xml"/>
           <property name="mapperLocations" value="classpath:com/zhuzhu/dao/*.xml"/>
       </bean>
   ```

4. **给Mapper接口加实现类【重点】**

   用继承 SqlSessionDaoSupport 的方法去封装 sqlSession，只需要给父类注入 sqlSessionFactory 即可。

   ```java
   public class UserMapperImpl2 extends SqlSessionDaoSupport implements UserMapper {
       @Override
       public List<User> selectUserList() {
           SqlSession sqlSession = getSqlSession();
           UserMapper mapper = sqlSession.getMapper(UserMapper.class);
           return mapper.selectUserList();
       }
   }
   ```

5. 将实现类注入到Spring容器中

   给父类注入 sqlSessionFactory 

   ```xml
   <import resource="spring-dao.xml"/>
   
   <bean id="userMapperImpl2" class="com.zhuzhu.dao.UserMapperImpl2">
       <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
   </bean>
   ```

6. 测试使用

   ```java
   @Test
   public void selectUserListTest2() {
       ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
       UserMapperImpl2 userMapperImpl2 = context.getBean("userMapperImpl2", UserMapperImpl2.class);
       List<User> userList = userMapperImpl2.selectUserList();
       for (User user : userList) {
           System.out.println(user);
       }
   }
   ```

# 13、声明式事务

## 13.1、回顾事务

- 把一组业务当成一个业务来做：要么都成功，要么都失败
- 事务在项目开发中，十分的重要，涉及到数据的一致性问题，不能马虎
- 确保完整性和一致性

事务 ACID 原则：

- 原子性
- 一致性
- 隔离性
  - 多个业务可能操作同一个资源，防止数据损坏
- 持久性
  - 事务一旦提交，无论系统发生什么问题，结果都不会再被影响，被持久化的写到存储器中

## 13.2、Spring 中的事务

- 声明式事务：AOP 实现【推荐使用】
- 编程式事务：在原有的代码上进行更改【不推荐使用】

声明式事务实现：

spring-dao.xml

```xml
<!-- 开启 Spring 的事务处理功能 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <constructor-arg ref="dataSource" />
</bean>

<!-- 结合AOP实现事务的织入 -->
<!-- 配置事务通知 -->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <!-- 给不同或者所有的的方法配置事务 -->
    <!-- 可以配置事务的传播特性，propagation -->
    <tx:attributes>
        <tx:method name="add" propagation="REQUIRED"/>
        <tx:method name="delete" propagation="REQUIRED"/>
        <tx:method name="update" propagation="REQUIRED"/>
        <tx:method name="query" read-only="true"/>
        <tx:method name="*" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>

<!-- 配置事务切入 -->
<aop:config>
    <aop:pointcut id="txPointCut" expression="execution(* com.zhuzhu.dao.*.*(..))"/>
    <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
</aop:config>
```

注意：需要导入 tx 事务的约束：

```xml
xmlns:tx="http://www.springframework.org/schema/tx"
xsi:schemaLocation="http://www.springframework.org/schema/tx
https://www.springframework.org/schema/tx/spring-tx.xsd"
```



