# 一、概述

## 1、JDBC数据库连接池的必要性

- 在使用开发基于数据库的web程序时,传统的模式基本是按以下步骤:

  ① 在主程序(如servlet、beans)中建立数据库连接
  ② 进行sq|操作
  ③ 断开数据库连接

- 这种模式开发,存在的问题:
  ① 普通的JDBC数据库连接使用DriverManager来获取,每次向数据库建立连接的时候都要将Connection
  加载到内存中,再验证用户名和密码(得花费0.05s ~ 1s的时间)。需要数据库连接的时候,就向数据库要求
  一个,执行完成后再断开连接。这样的方式将会消耗大量的资源和时间。**数据库的连接资源并没有得到很**
  **好的重复利用。**若同时有几百人甚至几千人在线,频繁的进行数据库连接操作将占用很多的系统资源，严
  重的甚至会造成服务器的崩溃。

  ② **对于每一-次数据库连接,使用完后都得断开。**否则,如果程序出现异常而未能关闭,将会导致数据库系统
  中的内存泄漏,最终将导致重启数据库。( 回忆:何为Java的内存泄漏? )
  ③ **这种开发不能控制被创建的连接对象数，**系统资源会被毫无顾及的分配出去,如连接过多,也可能导致内
  存泄漏，服务器崩溃。

## 2、数据库连接池技术

为解决传统开发中的数据库连接问题,可以采用数据库连接池技术。

- 基本思想:就是为数据库连接建立一个“缓冲池”。 预先在缓冲池中放入一定数量的连接,当需
  要建立数据库连接时,只需从”缓冲池”中取出一个,使用完毕之后再放回去。
- 数据库连接池负责分配、管理和释放数据库连接,它允许应用程序重复使用一个现有的数据库连接,而不是重
  新建立一个。
- 数据库连接池在初始化时将创建一定数量的数据库连接放到连接池中,这些数据库连接的数量是由最小数据库
  连接数来设定的。无论这些数据库连接是否被使用,连接池都将一直保证至少拥有这么多的连接数量。 连接池
  的最大数据库连接数量限定了这个连接池能占有的最大连接数,当应用程序向连接池请求的连接数超过最大连
  接数量时,这些请求将被加入到等待队列中。
- 工作原理：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200724134954.png)

- 数据库连接池技术的优点
  **1.资源重用**
  由于数据库连接得以重用,避免了频繁创建,释放连接弓|起的大量性能开销。在减少系统消耗的基础上,另一
  方面也增加了系统运行环境的平稳性。

  **2.更快的系统反应速度**
  数据库连接池在初始化过程中,往往已经创建了若干数据库连接置于连接池中备用。此时连接的初始化工作均
  已完成。对于业务请求处理而言,直接利用现有可用连接,避免了数据库连接初始化和释放过程的时间开销，
  从而减少了系统的响应时间

  **3.新的资源分配手段**
  对于多应用共享同一数据库的系统而言,可在应用层通过数据库连接池的配置,实现某一-应用最大可用数据库
  连接数的限制,避免某-应用独占所有的数据库资源

  **4.统一的连接管理,避免数据库连接泄漏**
  在较为完善的数据库连接池实现中，可根据预先的占用超时设定,强制回收被占用连接,从而避免了常规数据
  库连接操作中可能出现的资源泄露

## 3、多种开源的数据库连接池

- JDBC的数据库连接池使用javax.sql.DataSource来表示, DataSource只是一一个接口 ,该接口通常由服务器:
  (Weblogic, WebSphere, Tomcat)提供实现，也有一一些开源组织提供实现 :

  **DBCP** 是Apache提供的数据库连接池。tomcat服务器自带dbcp数据库连接池。**速度相对c3p0较快**,但
  因自身存在BUG , Hibernate3已不再提供支持。

  **C3P0** 是一个开源组织提供的一一个数据库连接池,**速度相对较慢,稳定性还可以。**hibernate官方推荐使用
  Proxool是sourceforge下的一个开源项目数据库连接池,有监控连接池状态的功能,稳定性较c3p0差一点

  **BoneCP ** 是一一个开源组织提供的数据库连接池,**速度快**

  **Druid** 是阿里提供的数据库连接池,据说是**集DBCP、C3P0、Proxool优点于一身**的数据库连接池,但
  是速度不确定是否有BoneCP快

- DataSource 通常被称为数据源,它包含连接池和连接池管理两个部分,习惯上也经常把DataSource称为连
  接池

- DataSource用来取代DriverManager来获取Connection ,获取速度快,同时可以大幅度提高数据库访问速
  度。

# 二、C3P0 的实现

首先，需要导入 c3p0 的两个jar包：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200724161148.png)

## 1、使用配置文件实现

使用了配置文件 c3p0-config.xml

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200724160811.png)

配置文件的内容：

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<c3p0-config>
    <named-config name="helloc3p0">
        <!-- 提供获取连接的4个基本信息 -->
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/mysupermarket</property>
        <property name="user">root</property>
        <property name="password">azhu251264</property>

        <!-- 进行数据库连接池管理的基本信息 -->
        <!-- 当数据库连接池中的连接数不够时，c3p0一次性向数据库服务器申请的连接数 -->
        <property name="acquireIncrement ">5</property>
        <!-- c3p0数据库连接池中初始化时的连接数 -->
        <property name="initialPoolsize">10</property>
        <!-- c3p0数据库连接池维护的最少连接数 -->
        <property name="minPoolSize">10</property>
        <!-- c3p0数据库连接池维护的最多的连接数 -->
        <property name="maxPoolsize">100</property>
        <!-- c3p0数据库连接池最多维护的Statement的个数 -->
        <property name="maxStatements">50</property>
        <!-- 每个连接中可以最多使用的Statement的个数 -->
        <property name="maxStatementsPerConnection">2</property>

    </named-config>
</c3p0-config>
```

测试使用：

```java
    @Test
    /**
     * 使用了xml配置文件
     */
    public void testC3p0Connection() {

        try {
            ComboPooledDataSource cpds = new ComboPooledDataSource("helloc3p0");
            Connection connection = cpds.getConnection();
            System.out.println(connection);
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }

    }
```

## 2、在 JDBCUtils 中实现

在工具类 JDBCUtils 中添加以下代码：

```java
    //保证一个项目当中只有一个连接池
    private static ComboPooledDataSource cpds = new ComboPooledDataSource("helloc3p0");

    /**
     * 从连接池中
     * 获取连接的方法
     * @return Connection
     * @throws Exception
     */
    public static Connection getConnectionFromPool() throws Exception{
        Connection connection = cpds.getConnection();
        return connection;
    }
```

# 三、DBCP 的实现

需要导入DBCP的三个jar包

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200725112353.png)

**注意：**

**如果 commons-logging-1.2.jar 没有导入的话，**

**会出现 “java.lang.NoClassDefFoundError: org/apache/commons/logging/LogFactory” 的错误**

## 1、使用配置文件实现

使用了配置文件 dbcp.properties

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200725112821.png)

配置文件的内容：

```properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql:///mysupermarket
username=root
password=azhu251264
```

测试使用：

```java
    @Test
    public void testDBCPConnection() {
        try {
            //读取配置文件
            Properties properties = new Properties();
            FileInputStream inputStream = new FileInputStream(new File("src/dbcp.properties"));
            //加载配置文件
            properties.load(inputStream);
            //通过配置文件来创建DBCP数据库连接池对象
            BasicDataSource source = BasicDataSourceFactory.createDataSource(properties);

            Connection connection = source.getConnection();

            System.out.println(connection);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

## 2、在 JDBCUtils 中实现

在工具类 JDBCUtils 中添加以下代码：

```java
    //保证一个项目当中只有一个DBCP连接池
    private static DataSource DBCPSource;
    //静态代码块，随着类的加载，只会执行一次。
    static {
        try {
            //读取配置文件
            Properties properties = new Properties();
            FileInputStream inputStream = new FileInputStream(new File("src/dbcp.properties"));
            //加载配置文件
            properties.load(inputStream);
            //通过配置文件来创建DBCP数据库连接池对象
            DBCPSource = BasicDataSourceFactory.createDataSource(properties);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    /**
     * 从DBCP连接池中
     * 获取连接的方法
     * @return Connection
     * @throws Exception
     */
    public static Connection getConnectionFromDBCP() throws Exception {
        Connection connection = DBCPSource.getConnection();
        return connection;
    }
```

# 四、Druid （德鲁伊）的实现

需要导入 Druid 的一个jar包

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200725151608.png)

## 1、使用配置文件实现

使用了配置文件 dbcp.properties

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200725151819.png)

配置文件的内容：

```properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql:///mysupermarket
username=root
password=azhu251264
```

测试使用：

```java
    @Test
    public void testDruidConnection() {
        try {
            //读取配置文件
            Properties properties = new Properties();
            InputStream inputStream = ClassLoader.getSystemClassLoader().getResourceAsStream("druid.properties");
            //加载配置文件
            properties.load(inputStream);
            //通过配置文件来创建Druid数据库连接池对象
            DataSource source = DruidDataSourceFactory.createDataSource(properties);
            Connection connection = source.getConnection();
            
            System.out.println(connection);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```



## 2、在 JDBCUtils 中实现

在工具类 JDBCUtils 中添加以下代码：

```java
    //保证一个项目当中只有一个Druid连接池
    private static DataSource DruidSource;
    //静态代码块，随着类的加载，只会执行一次。
    static {
        try {
            //读取配置文件
            Properties properties = new Properties();
            InputStream inputStream = ClassLoader.getSystemClassLoader().getResourceAsStream("druid.properties");
            //加载配置文件
            properties.load(inputStream);
            //通过配置文件来创建Druid数据库连接池对象
            DruidSource = DruidDataSourceFactory.createDataSource(properties);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    /**
     * 从Druid连接池中
     * 获取连接的方法
     * @return Connection
     * @throws Exception
     */
    public static Connection getConnectionFromDruid() throws Exception{
        Connection connection = DruidSource.getConnection();
        return connection;
    }
```

