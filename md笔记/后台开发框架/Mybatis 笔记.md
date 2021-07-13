# 1、MyBatis 简介

## 1.1、是什么

- MyBatis是一款优秀的持久层框架
- 它支持定制化SQL、存储过程以及高级映射
- MyBatis避免了几乎所有的JDBC代码和手动设置参数以及获取结果集
- MyBatis可以使用简单的XML或注解来配置和映射原生类型、接口和Java的POJO(Plain Old Java objects，普通老式Java对象）为数据库中的记录
- MyBatis 本是apache的一个开源项目iBatis，2010年这个项目由apache software foundation迁移到了google code，并且改名为MyBatis
- 2013年11月迁移到Github

## 1.2、如何获取

- maven 仓库：

  ```xml
  <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.3</version>
  </dependency>
  ```

- GitHub：https://github.com/mybatis/mybatis-3

- 中文文档：https://mybatis.org/mybatis-3/zh/index.html

# 2、第一个Mybatis程序

- 基本项目结构：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200929231641.png)

## 2.1、搭建环境

- 新建数据库

- 新建一个 maven 项目

- maven 依赖

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
      <groupId>com.zhuzhu</groupId>
      <artifactId>Mybatis-Study</artifactId>
      <packaging>pom</packaging>
      <version>1.0-SNAPSHOT</version>
      
      <dependencies>
          <!-- mybatis -->
          <dependency>
              <groupId>org.mybatis</groupId>
              <artifactId>mybatis</artifactId>
              <version>3.5.3</version>
          </dependency>
          <!-- mysql -->
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
              <version>5.1.47</version>
          </dependency>
          <!-- junit -->
          <dependency>
              <groupId>junit</groupId>
              <artifactId>junit</artifactId>
              <version>4.13</version>
              <scope>test</scope>
          </dependency>
      </dependencies>
      <build>
          <plugins>
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-compiler-plugin</artifactId>
                  <configuration>
                      <source>13</source>
                      <target>13</target>
                  </configuration>
              </plugin>
          </plugins>
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
  </project>
  ```

  

## 2.2、Mybatis 核心

- 在 resources 目录下新建 mybatis-config.xml 文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="com.mysql.jdbc.Driver"/>
                  <property name="url" value="jdbc:mysql://localhost:3306/zhuzhumall?useSSL=false&amp;serverTimezone=GMT"/>
                  <property name="username" value="root"/>
                  <property name="password" value="123456"/>
              </dataSource>
          </environment>
      </environments>
      <mappers>
          <mapper resource="com/zhuzhu/dao/UserMapper.xml"/>
      </mappers>
  </configuration>
  ```

- 编写工具类：MybatisUtils

  ```java
  // 通过创建 sqlSessionFactory 获取 sqlSession
  public class MybatisUtils {
      private static SqlSessionFactory sqlSessionFactory;
      static {
          try {
              // 1、获取 sqlSessionFactory 对象
              String resource = "mybatis-config.xml";
              InputStream inputStream = Resources.getResourceAsStream(resource);
              sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
      // 2、从 sqlSessionFactory 中获得 SqlSession 的实例
      public static SqlSession getSqlSession() {
          return sqlSessionFactory.openSession();
      }
  }
  ```

## 2.3、编写代码

1. java bean 实体类：User

   ```java
   public class User {
       private Integer id;
       private String username;
       private String password;
       private String email;
   }
   ```

2. dao 层：

   - 接口 UserMapper

     ```java
     public interface UserMapper {
         // 查询所有用户
         List<User> getUserList();
     }
     ```

   - 实现 UserMapper.xml

     ```xml
     <?xml version="1.0" encoding="UTF-8" ?>
     <!DOCTYPE mapper
             PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
             "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
     <mapper namespace="com.zhuzhu.dao.UserMapper">
         <!-- 结果集映射 -->
         <resultMap id="UserMap" type="user">
             <!-- column是数据库的字段，property是实体类中的属性 -->
             <result column="id" property="id"/>
             <result column="username" property="username"/>
             <result column="password" property="password"/>
             <result column="email" property="email"/>
         </resultMap>
         <select id="getUserList" resultMap="UserMap">
             select `id`,`username`,`password`,`email` from zhuzhumall.t_user;
         </select>
     </mapper>
     ```

## 2.4、测试

```java
@Test
public void getUserList() {
    // 1、获取 sqlSession 对象
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    // 2、执行查询语句
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    List<User> userList = userMapper.getUserList();
    for (User user : userList) {
        System.out.println(user);
    }
    // 3、关闭 sqlSession
    sqlSession.close();
}
```

# 3、CRUD

## 3.1、namespace

```xml
<mapper namespace="com.zhuzhu.dao.UserMapper">
</mapper>
```

## 3.2、select

1. 接口

   ```java
   // 根据id查询单个用户
   User getUserById(int id);
   ```

2. mapper.xml 实现

   ```xml
   <select id="getUserById" parameterType="int" resultType="user">
       select `id`,`username`,`password`,`email` from zhuzhumall.t_user where id=#{id};
   </select>
   ```

3. 测试

   ```java
   @Test
   public void getUserById() {
       // 获取 sqlSession 对象
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
       User user = userMapper.getUserById(3);
       System.out.println(user);
       // 关闭 sqlSession
       sqlSession.close();
   }
   ```

## 3.3、insert

1. 接口

   ```java
   // 插入一个用户
   int addUser(User user);
   ```

2. mapper.xml 实现

   ```xml
   <insert id="addUser" parameterType="user">
       insert into zhuzhumall.t_user (username,password,email) values (#{username},#{password},#{email});
   </insert>
   ```

3. 测试

   ```java
   @Test
   public void addUser() {
       // 获取 sqlSession 对象
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
       int i = userMapper.addUser(new User(0, "猪头3", "456123", "zhutou2@qq.com"));
       System.out.println(i);
       // 提交事务
       sqlSession.commit();
       // 关闭 sqlSession
       sqlSession.close();
   }
   ```

## 3.4、update

1. 接口

   ```java
   // 修改用户信息
   int updateUser(User user);
   ```

2. mapper.xml 实现

   ```xml
   <update id="updateUser" parameterType="user">
       update t_user set username=#{username}, password=#{password}, email=#{email} where id=#{id};
   </update>
   ```

3. 测试

   ```java
   @Test
   public void updateUser() {
       // 获取 sqlSession 对象
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
       int i = userMapper.updateUser(new User(8, "猪头", "123456789", "zhutou@qq.com"));
       System.out.println(i);
       // 提交事务
       sqlSession.commit();
       // 关闭 sqlSession
       sqlSession.close();
   }
   ```

## 3.5、delete

1. 接口

   ```java
   // 根据id删除一个用户
   int deleteUserById(int id);
   ```

2. mapper.xml 实现

   ```xml
   <delete id="deleteUserById" parameterType="int">
       delete from t_user where id=#{id};
   </delete>
   ```

3. 测试

   ```java
   @Test
   public void deleteUserById() {
       // 获取 sqlSession 对象
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
       int i = userMapper.deleteUserById(6);
       System.out.println(i);
       // 提交事务
       sqlSession.commit();
       // 关闭 sqlSession
       sqlSession.close();
   }
   ```

## 3.6、注意错误

- 标签不要匹配错
- resource绑定mapper，需要使用路径!
- 程序配置文件必须符合规范!
- NullPointerException，没有注册到资源!
- 输出的xml文件中存在中文乱码问题!
- maven资源没有导出问题!

## 3.7、万能 Map

假设，我们的实体类，或者数据库中的表，字段或者参数过多，我们应当考虑使用Map!

**万能 Map 属于非规范使用，但是在实际公司项目中会有用到。**

```java
// 传入万能的 Map
int addUser(Map<String,Object> map);
```

```xml
<insert id="addUser" parameterType="map">
    insert into t_user (id,name) values(#{userId},#{userName})
</insert>
```

- Map传递参数，直接在sql中取出key即可!【parameterType="map"】
- 对象传递参数，直接在sql中取对象的属性即可!【parameterType="Object"】
- 只有一个基本类型参数的情况下，可以直接在sql中取到!
- 多个参数用Map，或者注解!

## 3.8、小拓展

模糊查询怎么写？

1. Java 代码执行的时候，传递通配符 % %

   ```java
   List<User> userList = mapper.getUserLike("%李%");
   ```

2. 在 sql 拼接中使用通配符

   ```xml
   select * from t_user where name like "%"#{value}"%"
   ```

# 4、配置解析

## 4.1、核心配置文件

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200930234843.png)

- mybatis-config.xml

- MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。 配置文档的顶层结构如下：

- configuration（配置）

  - properties（属性）

  - settings（设置）
  - typeAliases（类型别名）
  - typeHandlers（类型处理器）
  - objectFactory（对象工厂）
  - plugins（插件）
  - environments（环境配置）
    - environment（环境变量）
      - transactionManager（事务管理器）
      - dataSource（数据源）
  - databaseIdProvider（数据库厂商标识）
  - mappers（映射器）

## 4.2、环境

- MyBatis可以配置成适应多种环境
- **不过要记住：尽管可以配置多个环境，但每个SqlSessionFactory实例只能选择一种环境。**
- 学会使用配置多套运行环境！
- Mybatis默认的事务管理器就是JDBC，连接池：POOLED

## 4.3、属性

我们可以通过 properties 属性来实现引用配置文件

这些属性都是可外部配置且可动态替换的，既可以在典型的 Java 属性文件中配置，亦可通过 properties 元素的子元素来传递。【db.properties】

1. 新建一个 db.properties 文件

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200930235846.png)

   写入：

   ```properties
   driver=com.mysql.jdbc.Driver
   username=root
   password=123456
   url=jdbc:mysql://localhost:3306/zhuzhumall?useSSL=false&amp;serverTimezone=GMT&amp;characterEncoding=UTF-8
   ```

2. 在 mybatis-config.xml 中写入：

   ```xml
   <!-- 配置引入外部文件 -->
   <properties resource="db.properties"/>
   ```

   ```xml
   <property name="driver" value="${driver}"/>
   <property name="url" value="${url}"/>
   <property name="username" value="${username}"/>
   <property name="password" value="${password}"/>
   ```

- 可以直接引入外部文件
- 可以在其中增加一些属性配置
- **如果两个文件有同一个字段，优先使用外部配置文件的**

## 4.4、类型别名

- 类型别名是为Java类型设置一个短的名字。
- 存在的意义仅在于用来减少类完全限定名的冗余。

1. 可以给单独一个类起别名

   ```xml
   <typeAliases>
       <typeAlias type="com.zhuzhu.pojo.User" alias="User"/>
   </typeAliases>
   ```

2. 也可以指定一个包名，MyBatis会在包名下面搜索需要的Java Bean，比如:

   它的默认**别名就是这个类的类名，首字母小写!**

   ```xml
   <typeAliases>
       <package name="com.zhuzhu.pojo"/>
   </typeAliases>
   ```

- 实体类比较少，用第一种方式

- 实体类比较多，用第二种方式

- 第一种可以自定义别名，第二种则不能，如果非要自定义，需要在实体类上使用注解

  ```java
  @Alias("user")
  public class User {}
  ```

## 4.5、设置

 这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。 

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201001002046.png)

## 4.6、其他配置

- [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)
- [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)
- [plugins（插件）](https://mybatis.org/mybatis-3/zh/configuration.html#plugins)

## 4.7、映射器

MapperRegistry：需要注册绑定每一个 Mapper 文件

1. 方式一：为每一个 Mapper.xml 单独注册【推荐使用】

   ```xml
   <mappers>
       <mapper resource="com/zhuzhu/dao/OrderMapper.xml"/>
   </mappers>
   ```

   好处：Mapper.xml 可以放在项目的任意目录下

2. 方式二：使用 class

   ```xml
   <mappers>
       <mapper class="com.zhuzhu.dao.OrderMapper"/>
   </mappers>
   ```

   注意：

   - 接口 Mapper 和 Mapper.xml 文件必须**同名**
   - 接口 Mapper 和 Mapper.xml 文件必须**在同一个包下**

3. 方式三：使用扫描包

   ```xml
   <mappers>
       <package name="com.zhuzhu.dao"/>
   </mappers>
   ```

   注意：

   - 接口 Mapper 和 Mapper.xml 文件必须**同名**
   - 接口 Mapper 和 Mapper.xml 文件必须**在同一个包下**

## 4.8、生命周期和作用域

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201001004413.png)

生命周期，和作用域，是至关重要的，因为错误的使用会导致非常严重的并发问题。

**sqISessionFactoryBuilder：**

- 一旦创建了SqlSessionFactory，就不再需要它了
- 局部变量

**sqlSessionFactory:**

- 说白了就是可以想象为：数据库连接池
- SqlSessionFactory一旦被创建就应该在应用的运行期间一直存在，**没有任何理由丢弃它或重新创建另一个实例。**
- 因此SqlSessionFactory的最佳作用域是应用作用域。
- 最简单的就是使用**单例模式**或者静态单例模式。

**sqlSession：**

- 连接到连接池的一个请求
- SqlSession的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域。
- **用完之后需要赶紧关闭，否则资源被占用**

# 5、属性名与字段名不一致

解决 **Java实体类的属性名**与**数据库的字段名**不一致的问题

1. 方式一：在 sql 语句中**起别名**

   ```xml
   <select id="getUserList" resultType="com.zhuzhu.pojo.User">
       select `id` userId,`username` userName,`password` userPassword from t_user;
   </select>
   ```

2. 方式二：使用**结果集映射（resultMap ）**

   ```xml
   <select id="getUserList" resultMap="UserMap">
       select `id`,`username`,`password` from t_user;
   </select>
   ```

   ```xml
   <!-- 结果集映射 -->
   <resultMap id="UserMap" type="user">
       <!-- column是数据库的字段，property是实体类中的属性 -->
       <result column="id" property="userId"/>
       <result column="username" property="userName"/>
       <result column="password" property="userPassword"/>
   </resultMap>
   ```

- ResultMap 元素是MyBatis中最重要最强大的元素
- ResultMap 的设计思想是：对于简单的语句根本不需要配置显式的结果映射，而对于复杂一点的语句只需要描述它们的关系就行了。
- ResultMap最优秀的地方在于：虽然你已经对它相当了解了，但是根本就不需要显式地用到他们。

# 6、日志

## 6.1、日志工厂

如果一个数据库操作，出现了异常，我们需要排错。日志就是最好的助手!

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201001145909.png)

- SLF4J
- **LOG4J【掌握】**
- LOG4J2
- JDK_LOGGING
- COMMONS_LOGGING
- **STDOUT_LOGGING【掌握】**（标准日志）
- NO_LOGGING

使用方式：

在 mybatis-config.xml 核心配置文件中 设置：

```xml
<settings>
    <setting name="" value=""/>
</settings>
```

## 6.2、LOG4J

1. 是什么？

   - Log4j 是Apache的一个开源项目，通过使用 Log4j，我们可以控制日志信息输送的目的地是控制台、文件、GUI组件
   - 我们也可以控制每一条日志的输出格式
   - 通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。
   - 通过一个配置文件来灵活地进行配置，而不需要修改应用的代码。

2. 怎么用？

   1. 导包：

      ```xml
      <dependency>
          <groupId>log4j</groupId>
          <artifactId>log4j</artifactId>
          <version>1.2.17</version>
      </dependency>
      ```

   2. 新建 log4j.properties 文件

      ```properties
      #将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
      log4j.rootLogger=DEBUG,console,file
      
      #控制台输出的相关设置
      log4j.appender.console = org.apache.log4j.ConsoleAppender
      log4j.appender.console.Target = System.out
      log4j.appender.console.Threshold=DEBUG
      log4j.appender.console.layout = org.apache.log4j.PatternLayout
      log4j.appender.console.layout.ConversionPattern=[%c]-%m%n
      
      #文件输出的相关设置
      log4j.appender.file = org.apache.log4j.RollingFileAppender
      log4j.appender.file.File=./log/zhuzhu.log
      log4j.appender.file.MaxFileSize=10mb
      log4j.appender.file.Threshold=DEBUG
      log4j.appender.file.layout=org.apache.log4j.PatternLayout
      log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n
      
      #日志输出级别
      log4j.logger.org.mybatis=DEBUG
      log4j.logger.java.sql=DEBUG
      log4j.logger.java.sql.Statement=DEBUG
      log4j.logger.java.sql.ResultSet=DEBUG
      log4j.logger.java.sql.P
      reparedStatement=DEBUG
      ```

   3. 在核心配置文件中设置

      ```xml
      <settings>
          <!-- 配置日志为 log4j 的实现-->
          <setting name="logImpl" value="LOG4J"/>
      </settings>
      ```

3. **简单使用**

   1. 日志对象，参数为当前类的class

      ```java
      static Logger logger = Logger.getLogger(UserDaoTest.class);
      ```

   2. 日志级别

      ```java
      logger.info("info:进入了testLog4j");
      logger.debug("debug:进入了testLog4j");
      logger.error("error:进入了testLog4j");
      ```

# 7、分页

## 7.1、使用 Limit（sql层面实现）

1. 接口 Mapper

   ```java
   // 分页查询用户
   List<User> getUserByLimit(Map<String, Integer> map);
   ```

2. Mapper.xml 实现

   ```xml
   <select id="getUserByLimit" parameterType="map" resultType="com.zhuzhu.pojo.User">
       select `id`,`username`,`password`,`email` from t_user limit #{startIndex},#{pageSize};
   </select>
   ```

3. 测试

   ```java
   @Test
   public void getUserByLimit() {
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
       HashMap<String, Integer> map = new HashMap<>();
       map.put("startIndex", 4);
       map.put("pageSize", 3);
       List<User> userList = mapper.getUserByLimit(map);
       for (User user : userList) {
           System.out.println(user);
       }
       sqlSession.close();
   }
   ```

## 7.2、使用 RowBounds（java层面实现）

较少使用，略。

## 7.3、使用分页插件

**了解即可！**

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201001153114.png)

# 8、使用注解开发

## 8.1、使用方法

直接在 Mapper 接口 中实现

```java
@Select("select * from t_order where order_id = #{OrderId}")
Order getOrderById(@Param("OrderId") String string);
```

- 本质：反射机制实现
- 底层：动态代理
- **对于复杂的sql语句比较难实现，所以比较少使用，还是使用 Mapper.xml 实现比较多！**

## 8.2、参数的 @Param() 注解

- **基本类型的参数或者String类型，需要加上**
- 引用类型不需要加
- 如果只有一个基本类型的话，可以忽略，但是**建议大家都加上**!
- **我们在SQL中引用的就是我们这里的 @Param() 中设定的属性名!**

# 9、Lombok

## 9.1、是什么？

- Lombok 是一个Java库，它会自动插入编辑器和构建工具中。
- Lombok 提供了一组有用的注释，用来消除Java类中的大量样板代码。
- 仅五个字符(@Data)就可以替换数百行代码从而产生干净，简洁且易于维护的Java类 。

## 9.2、怎么用？

1. 在 IDEA 中安装 Lombok 插件

2. 导包

   ```xml
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
       <version>1.18.12</version>
   </dependency>
   ```

3. 在实体类上使用注解

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class Order {
       private String orderId;
       private Date createTime;
       private BigDecimal price;
       private Integer userId;
   }
   ```

# 10、多对一

- 多个学生，对应一个老师
- 对于学生这边而言，**关联**：多个学生，关联一个老师【多对一】
- 对于老师而言，**集合**：一个老师，有很多学生【一对多】

## 10.1、按查询过程嵌套处理

1. Mapper 接口

   ```java
   // 查询所有订单项及对应的订单信息
   // 方式一：按查询过程嵌套处理
   List<OrderItem> getOrderItemAndOrder01();
   ```

2. Mapper.xml 实现

   ```xml
   <!-- 方式一：按查询过程嵌套处理 -->
   <select id="getOrderItemAndOrder01" resultMap="OrderItemAndOrder">
       select id, name, count, price, total_price, order_id
       from zhuzhumall.t_order_item;
   </select>
   <resultMap id="OrderItemAndOrder" type="OrderItem">
       <result property="id" column="id"/>
       <result property="name" column="name"/>
       <result property="price" column="price"/>
       <result property="totalPrice" column="total_price"/>
       <association property="order" column="order_id" javaType="Order" select="getOrder"/>
   </resultMap>
   
   <select id="getOrder" resultMap="orders">
       select order_id, create_time, price, status, user_id
       from zhuzhumall.t_order
       where order_id = #{OrderId};
   </select>
   <resultMap id="orders" type="Order">
       <result property="orderId" column="order_id"/>
       <result property="createTime" column="create_time"/>
       <result property="price" column="price"/>
       <result property="status" column="status"/>
       <result property="userId" column="user_id"/>
   </resultMap>
   ```

3. 测试

   ```java
   @Test
   public void getOrderItemAndOrder01Test() {
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       OrderItemMapper mapper = sqlSession.getMapper(OrderItemMapper.class);
       List<OrderItem> orderItemList = mapper.getOrderItemAndOrder01();
       for (OrderItem orderItem : orderItemList) {
           System.out.println(orderItem);
       }
       sqlSession.close();
   }
   ```

## 10.2、按查询结果嵌套处理

1. Mapper 接口

   ```java
   // 方式二：按查询结果嵌套处理
   List<OrderItem> getOrderItemAndOrder02();
   ```

2. Mapper.xml 实现

   ```xml
   <!-- 方式二：按查询结果嵌套处理 -->
   <select id="getOrderItemAndOrder02" resultMap="OrderItemAndOrder02">
       select oi.id, oi.name, os.order_id, os.create_time
       from t_order_item oi, t_order os
       where oi.order_id = os.order_id;
   </select>
   <resultMap id="OrderItemAndOrder02" type="OrderItem">
       <result property="id" column="oi.id"/>
       <result property="name" column="oi.name"/>
       <association property="order" javaType="Order">
           <result property="orderId" column="os.order_id"/>
           <result property="createTime" column="os.create_time"/>
       </association>
   </resultMap>
   ```

3. 测试

   ```java
   @Test
   public void getOrderItemAndOrder02Test() {
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       OrderItemMapper mapper = sqlSession.getMapper(OrderItemMapper.class);
       List<OrderItem> orderItemList = mapper.getOrderItemAndOrder02();
       for (OrderItem orderItem : orderItemList) {
           System.out.println(orderItem);
       }
       sqlSession.close();
   }
   ```

# 11、一对多

## 11.1、按查询过程嵌套处理

1. Mapper 接口

   ```java
   // 通过订单号查询订单及对应的订单项
   // 方式一、通过查询过程嵌套处理
   Order getOrderAndOrderItemByOrderId02(@Param("orderId") String orderId);
   ```

2. Mapper.xml 实现

   ```xml
   <!-- 方式一、通过查询过程嵌套 -->
   <select id="getOrderAndOrderItemByOrderId02" resultMap="getOrderAndOrderItem02">
       select * from t_order where order_id = #{orderId};
   </select>
   <resultMap id="getOrderAndOrderItem02" type="Order">
       <result property="orderId" column="order_id"/>
       <result property="createTime" column="create_time"/>
       <result property="price" column="price"/>
       <result property="status" column="status"/>
       <result property="userId" column="user_id"/>
       <collection property="orderItemList" javaType="ArrayList" ofType="OrderItem"
                   select="getOrderItemByOrderId" column="order_id"/>
   </resultMap>
   
   <select id="getOrderItemByOrderId" resultMap="getOrderItem">
       select * from t_order_item where order_id = #{orderId}
   </select>
   <resultMap id="getOrderItem" type="OrderItem">
       <result property="id" column="id"/>
       <result property="name" column="name"/>
       <result property="count" column="count"/>
       <result property="price" column="price"/>
       <result property="totalPrice" column="total_price"/>
       <result property="orderId" column="order_id"/>
   </resultMap>
   ```

3. 测试

   ```java
   @Test
   public void getOrderAndOrderItemByOrderId02Test() {
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       OrderMapper mapper = sqlSession.getMapper(OrderMapper.class);
       Order order = mapper.getOrderAndOrderItemByOrderId02("15977336442342");
       System.out.println(order);
       sqlSession.close();
   }
   ```

## 12.2、按查询结果嵌套处理

1. Mapper 接口

   ```java
   // 方式二、通过查询结果嵌套处理
   Order getOrderAndOrderItemByOrderId01(@Param("orderId") String orderId);
   ```

2. Mapper.xml 实现

   ```xml
   <!-- 方式二、通过查询结果嵌套 -->
   <select id="getOrderAndOrderItemByOrderId01" resultMap="getOrderAndOrderItem01">
       select *
       from t_order , t_order_item
       where t_order.order_id = #{orderId} and t_order.order_id = t_order_item.order_id
   </select>
   <resultMap id="getOrderAndOrderItem01" type="Order">
       <result property="orderId" column="order_id"/>
       <result property="createTime" column="create_time"/>
       <!-- 由于与下一个Map的price同名，所以会把下一个price覆盖掉 -->
       <result property="price" column="price"/>
       <result property="status" column="status"/>
       <result property="userId" column="user_id"/>
       <collection property="orderItemList" ofType="OrderItem">
           <result property="id" column="id"/>
           <result property="name" column="name"/>
           <result property="count" column="count"/>
           <!-- 由于与上一个Map的price同名，所以此price会被覆盖掉 -->
           <result property="price" column="price"/>
           <result property="totalPrice" column="total_price"/>
           <result property="orderId" column="order_id"/>
       </collection>
   </resultMap>
   ```

3. 测试

   ```java
   @Test
   public void getOrderAndOrderItemByOrderId01Test() {
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       OrderMapper mapper = sqlSession.getMapper(OrderMapper.class);
       Order order = mapper.getOrderAndOrderItemByOrderId01("15977336442342");
       System.out.println(order);
       sqlSession.close();
   }
   ```

## 12.3、小结

1. 关联：association【多对一】
2. 集合：collection【一对多】
3. javaType & ofType
   - JavaType 用来指定实体类中属性的类型
   - ofType 用来指定映射到List或者集合中的pojo类型，泛型中的约束类型

**注意点:**

- 保证SQL的可读性，尽量保证通俗易懂
- 注意一对多和多对一中，属性名和字段的问题
- 如果问题不好排查错误，可以使用日志，建议使用Log4j

**面试高频**

- Mysql引擎
- InnoDB底层原理
- 索引
- 索引优化!

# 12、动态 SQL

**什么是动态SQL？动态SQL就是指根据不同的条件生成不同的SQL语句**

**所谓的动态SQL，本质还是SQL语句，只是我们可以在SQL层面，去执行一个逻辑代码**

常用标签：

- if
- choose (when, otherwise)
- trim (where, set)
- foreach

## 12.1、if

1. Mapper 接口

   ```java
   // 动态sql：根据传入的参数，查询对应的书本信息
   List<Book> getBookByIf(Map map);
   ```

2. Mapper.xml 实现

   ```xml
   <select id="getBookByIf" parameterType="map" resultType="Book">
       select * from t_book
       <where>
           <if test="name != null">
               and name = #{name}
           </if>
           <if test="author != null">
               and author = #{author}
           </if>
       </where>
   </select>
   ```

3. 测试

   ```java
   @Test
   public void getBookByIfTest() {
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       BookMapper mapper = sqlSession.getMapper(BookMapper.class);
       HashMap map = new HashMap();
       //map.put("name", "鸟哥的Linux私房菜");
       map.put("author", "曼昆");
       List<Book> bookList = mapper.getBookByIf(map);
       for (Book book : bookList) {
           System.out.println(book);
       }
       sqlSession.close();
   }
   ```

## 12.2、choose (when, otherwise)

Mapper.xml 

```xml
<select id="findActiveBookLike"
        resultType="Book">
    SELECT * FROM BLOG WHERE state = ‘ACTIVE’
    <choose>
        <when test="title != null">
            AND title like #{title}
        </when>
        <when test="author != null and author.name != null">
            AND author_name like #{author.name}
        </when>
        <otherwise>
            AND featured = 1
        </otherwise>
    </choose>
</select>
```

## 12.3、trim 自定义(where, set)

1. 自定义 where

   ```xml
   <trim prefix="WHERE" prefixOverrides="AND |OR ">
     ...
   </trim>
   ```

2. 自定义 set

   ```xml
   <trim prefix="SET" suffixOverrides=",">
     ...
   </trim>
   ```

## 12.4、SQL 片段

有的时候，我们可能会将一些功能的部分抽取出来，方便复用!

1. 使用SQL标签抽取公共的部分

   ```xml
   <sql id="if-title-author">
       <if test="title!=null">
           title = #{title}
       </if>
       <if test="author!=null">
           author = #{author}
       </if>
   </sql>
   ```

2. 在需要使用的地方使用Include标签引用即可

   ```xml
   <select id="queryBookByIf" parameterType="map" resu1tType="Book">
       select * from t_book
       <where>
           <include refid="if-title-author"></include>
       </where>
   </select>
   ```

## 12.5、foreach

1. Mapper 接口

   ```java
   // 根据传入的map中的数组查询特定范围的书本信息
   List<Book> getBookByForeach(Map map);
   ```

2. Mapper.xml 实现

   ```xml
   <select id="getBookByForeach" parameterType="map" resultType="Book">
       select * from t_book
       <where>
           <foreach collection="idList" item="id" open="(" separator="or" close=")">
               id = #{id}
           </foreach>
       </where>
   </select>
   ```

3. 测试

   ```java
   @Test
   public void getBookByForeachTest() {
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       BookMapper mapper = sqlSession.getMapper(BookMapper.class);
       HashMap map = new HashMap();
       ArrayList<Integer> list = new ArrayList<>();
       list.add(30);
       list.add(31);
       list.add(32);
       map.put("idList", list);
       List<Book> bookList = mapper.getBookByForeach(map);
       for (Book book : bookList) {
           System.out.println(book);
       }
       sqlSession.close();
   }
   ```

**注意事项：**

- 最好基于单表来定义SQL片段!
- 不要存在where标签

# 13、缓存（了解）

## 13.1、简介

1. 什么是缓存【Cache】?
   - 存在内存中的临时数据。
   - 将用户经常查询的数据放在缓存（内存）中，用户去查询数据就不用从磁盘上（关系型数据库数据文件）查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题。
2. 为什么使用缓存？
   - 减少和数据库的交互次数，减少系统开销，提高系统效率。
3. 什么样的数据能使用缓存?
   - 经常查询并且不经常改变的数据。

## 13.2、Mybatis 缓存

- MyBatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。
- MyBatis系统中默认定义了两级缓存：**一级缓存**和**二级缓存**
  - 默认情况下，只有一级缓存开启。（SqlSession级别的缓存，也称为本地缓存）
  - 二级缓存需要手动开启和配置，（namespace级别的缓存）
  - 为了提高扩展性，MyBatis定义了缓存接口Cache。我们可以通过实现Cache接口来自定义二级缓存

## 13.3、一级缓存

- 一级缓存也叫本地缓存：sqlSession
  - 与数据库同一次会话期间查询到的数据会放在本地缓存中
  - 以后如果需要获取相同的数据，直接从缓存中拿，没必须再去查询数据库

- 缓存失效的情况：
  - 查询不同的东西
  - 增删改操作，可能会改变原来的数据，所以必定会刷新缓存!

## 13.4、二级缓存

- 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存
- 基于namespace级别的缓存，一个名称空间，对应一个二级缓存;
- 工作机制
  - 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中
  - 如果当前会话关闭了，这个会话对应的一级缓存就没了，一级缓存中的数据就会被保存到二级缓存中
  - 新的会话查询信息，就可以从二级缓存中获取内容
  - 不同的mapper查出的数据会放在自己对应的缓存（map）中;

**使用步骤：**

1. 开启全局缓存

   ```xml
   <!--显式地开启全局缓存-->
   <setting name="cacheEnabled" value="true"/>
   ```

2. 在要使用二级缓存的 Mapper 中开启

   ```xml
   <!--在当前的Mapper.xml中开启-->
   <cache/>
   ```

   也可以自定义参数

   ```xml
   <!--在当前的Mapper.xml中开启-->
   <cache
          eviction="FIFO"
          flushInterval="60000"
          size="512"
          readOnly="true"/>
   ```

   这个更高级的配置创建了一个 FIFO 缓存，每隔 60 秒刷新，最多可以存储结果对象或列表的 512 个引用，而且返回的对象被认为是只读的，因此对它们进行修改可能会在不同线程中的调用者产生冲突。 

3. 注意：

   **需要将实体类序列化，否则会报错！**

   ```java
   public class User implements Serializable {
       ...
   }
   ```

**小结：**

- 只要开启了二级缓存，在同一个Mapper下就有效
- 所有的数据都会先放在一级缓存中
- 只有当会话提交，或者关闭的时候，才会提交到二级缓冲中

## 13.5、缓存原理

查询顺序：

1. 先看二级缓存有没有
2. 再看一级缓存有没有
3. 查询数据库

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20201001164924.png)

