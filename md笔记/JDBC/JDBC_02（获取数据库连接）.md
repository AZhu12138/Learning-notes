# 连接的方式一

```java
    @Test
    public void testConnection_01() throws SQLException {

        Driver driver = new com.mysql.jdbc.Driver();

        //url = 协议://ip地址:端口号/数据库
        String url = "jdbc:mysql://localhost:3306/mysupermarket";
        //将用户名和密码封装在Properties中
        Properties info = new Properties();
        info.setProperty("user","root");
        info.setProperty("password","azhu251264");

        Connection connect = driver.connect(url, info);

        System.out.println(connect);
    }
```



# 连接的方式二（对方式一的迭代）

```java
    @Test
    public void testConnection_02() throws Exception {
        //1、获取Driver实现类对象（反射）
        Class aClass = Class.forName("com.mysql.jdbc.Driver");
        Driver driver = (Driver) aClass.newInstance();

        //2、提供要连接的数据库
        String url = "jdbc:mysql://localhost:3306/mysupermarket";

        //3、提供连接需要的用户名和密码
        Properties info = new Properties();
        info.setProperty("user","root");
        info.setProperty("password","azhu251264");

        //4、获取连接
        Connection connect = driver.connect(url, info);

        System.out.println(connect);
    }
```

# 连接的方式三

```java
    @Test
    public void testConnection_03() throws Exception{
        //1、获取Driver实现类对象（反射）
        Class aClass = Class.forName("com.mysql.jdbc.Driver");
        Driver driver = (Driver) aClass.newInstance();

        //2、提供需要的3个基本信息
        String url = "jdbc:mysql://localhost:3306/mysupermarket";
        String user = "root";
        String password = "azhu251264";

        //3、注册驱动
        DriverManager.registerDriver(driver);
        //4、获取连接
        Connection connection = DriverManager.getConnection(url, user, password);

        System.out.println(connection);
    }

```

# 连接的方式四（方式三的优化）

```java
@Test
    public void testConnection_04() throws Exception{
        //1、提供需要的3个基本信息
        String url = "jdbc:mysql://localhost:3306/mysupermarket";
        String user = "root";
        String password = "azhu251264";

        //2、加载Driver
        Class.forName("com.mysql.jdbc.Driver");

        /*
        * 省略了驱动的注册，
        * 因为在mysql的Driver实现类中声明的
        * static{
        *     try{
        *         java.aql.DriverManager.registerDriver(new driver);
        *     }
        *     catch{
        *         、、、、、、
        *     }
        * }
        * 语句块自动注册了驱动。
        * */

        //3、获取连接
        Connection connection = DriverManager.getConnection(url, user, password);

        System.out.println(connection);
    }
```

# 连接的方式五（最终版）

- 使用配置文件读取获取连接需要的4个基本信息：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200717181721.png)

配置文件代码如下：

```
user=root
password=azhu251264
url=jdbc:mysql://localhost:3306/mysupermarket
driverClass=com.mysql.jdbc.Driver
```

```java
    @Test
    public void testConnection_05() throws Exception{
        //1、读取配置文件
        InputStream inputStream = ConnectionTest_01.class.getClassLoader().
            getResourceAsStream("jdbc.properties");

        Properties properties = new Properties();
        properties.load(inputStream);

        String user = properties.getProperty("user");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
        String driverClass = properties.getProperty("driverClass");

        //2、加载驱动
        Class.forName(driverClass);

        //3、获取连接
        Connection connection = DriverManager.getConnection(url,user,password);

        System.out.println(connection);

    }
```