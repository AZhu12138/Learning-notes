# 一、使用 Statement 操作数据库的弊端

- 存在拼串操作，繁琐

- 存在 SQL 注入问题

> SQL 注入：是利用【某些系统没有对用户的数据进行充分的检查】，而在用户输入数据中注入非法的 SQL 语句段或命令，从而利用系统的 SQL 引擎完成恶意行为的做法。

- 对 java 而言：使用 PreparedStatement 即可防止SQL 注入。

# 二、使用 PreparedStatement  操作数据库

## 1、增删改

### ①最原始的繁琐代码

```java
    @Test
    public void testPreparedStatementUpdate() {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        try {
            //1、读取配置文件
            InputStream inputStream = ClassLoader.getSystemClassLoader().getResourceAsStream("jdbc.properties");

            Properties properties = new Properties();
            properties.load(inputStream);

            String user = properties.getProperty("user");
            String password = properties.getProperty("password");
            String url = properties.getProperty("url");
            String driverClass = properties.getProperty("driverClass");

            //2、加载驱动
            Class.forName(driverClass);

            //3、获取连接
            connection = DriverManager.getConnection(url,user,password);

            //4、预编译sql语句，返回PreparedStatement的实例
            String sql = "insert into vendors(vend_name,vend_address,vend_city,vend_state,vend_zip,vend_country)values(?,?,?,?,?,?)";
            preparedStatement = connection.prepareStatement(sql);

            //5、填充占位符
            preparedStatement.setString(1, "HuangDonggua");
            preparedStatement.setString(2, "FoGang");
            preparedStatement.setString(3, "QingYuan");
            preparedStatement.setString(4, "ZZ");
            preparedStatement.setString(5, "939393");
            preparedStatement.setString(6, "CHINA");

            //可用于填充date类型日期的代码：
//          SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
//          Date date = simpleDateFormat.parse("2020-10-28");
//          preparedStatement.setString(7, new Data(date.getDate()));

            //6、执行操作
            preparedStatement.execute();

        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            //7、资源的关闭
            try {
                //preparedStatement 存在才关闭，避免错误。
                if (preparedStatement != null) {
                    preparedStatement.close();
                }
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
            try {
                //connection 存在才关闭，避免错误。
                if (connection != null) {
                    connection.close();
                }
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
```

### ②小改进

- 把通用的“数据库连接”和“资源关闭”分别封装成两个静态方法到 JDBCUtils 工具类中

```java
/**
 * 操作数据库的工具类
 */
public class JDBCUtils {

    /**
     * 获取连接的方法
     * @return Connection
     * @throws Exception
     */
    public static Connection getConnection() throws Exception{
        //1、读取配置文件
        InputStream inputStream = ClassLoader.getSystemClassLoader().getResourceAsStream("jdbc.properties");

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
        return connection;
    }

    /**
     * 关闭 Statement 和 connection 的方法
     * @param Statement
     * @param connection
     */
    public static void closeResoure(Statement Statement, Connection connection){
        try {
            //Statement 存在才关闭，避免错误。
            if (Statement != null) {
                Statement.close();
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
        try {
            //connection 存在才关闭，避免错误。
            if (connection != null) {
                connection.close();
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
    }
}
```

如下使用：

```java
    @Test
    public void testUpdate() {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        try {
            //1、获取数据库连接
            connection = JDBCUtils.getConnection();
            //2、预编译sql语句
            String sql = "update vendors set vend_name = ? where vend_id = ?";
            preparedStatement = connection.prepareStatement(sql);
            //3、填充占位符
            preparedStatement.setObject(1, "HuangShagua");
            preparedStatement.setObject(2, "1007");
            //4、执行sql语句
            preparedStatement.execute();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //5、关闭资源
            JDBCUtils.closeResoure(preparedStatement, connection);
        }
    }
```

### ③通用的“增删改”

- 把上面②的使用方法的通用部分封装成一个方法

```java
    /**
     * 通用的Update修改数据库的方法
     * @param sql
     * @param args
     */
    public void Update(String sql,Object ...args) {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        try {
            //1、获取数据库连接
            connection = JDBCUtils.getConnection();
            //2、预编译sql语句
            preparedStatement = connection.prepareStatement(sql);
            //3、填充占位符
            for (int i = 0; i < args.length; i++) {
                //注意关于i的参数取值
                preparedStatement.setObject(i + 1, args[i]);
            }
            //4、执行sql语句
            preparedStatement.execute();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //5、关闭资源
            JDBCUtils.closeResoure(preparedStatement, connection);
        }
    }
```

如下，使用起来如此简单：

```java
    @Test
    public void testUpdate_02() {
        String sql = "update vendors set vend_name = ? where vend_id = ?";
        Update(sql, "HuangZhuzhu", 1003);
    }
```

## 2、查

### ①ORM 编程思想

- （object relational mapping）

> 一个数据表对应一个java类
>
> 表中的一条记录对应java类的一个对象
>
> 表中的一个字段对应java类的一个属性

**所以 **创建一个与 数据表 对应的 类，将查询的 结果的 数据 封装在 对应的类的对象 中。

```java
/**
 * ORM编程思想（object relational mapping）
 * 一个数据表对应一个java类
 * 表中的一条记录对应java类的一个对象
 * 表中的一个字段对应java类的一个属性
 */
public class Customers {

    private int custId;
    private String custName;
    private String custAddress;
    private String custCity;
    private String custState;
    private String custZip;
    private String custCountry;
    private String custContact;
    private String custEmail;

    public Customers() {
        super();
    }
    public Customers(int id, String name, String address, String city, String state, String zip, String country, String contact, String email) {
        super();
        this.custId = id;
        this.custName = name;
        this.custAddress = address;
        this.custCity = city;
        this.custState = state;
        this.custZip = zip;
        this.custCountry = country;
        this.custContact = contact;
        this.custEmail = email;
    }
    
    //以下省略了 Getter,Setter,toString 方法
    、、、、、、
}
```

### ②对单个 Customers 表通用

```java
    /**
     * 通用的查询Customers表的方法
     * @param sql
     * @param args
     * @return
     */
    public Customers queryForCustomers(String sql,Object ...args) {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
            connection = JDBCUtils.getConnection();
            preparedStatement = connection.prepareStatement(sql);
            for (int i = 0; i < args.length; i++) {
                preparedStatement.setObject(i + 1, args[i]);
            }

            //执行sql语句，返回结果集
            resultSet = preparedStatement.executeQuery();
            //获取结果集的元数据
            ResultSetMetaData metaData = resultSet.getMetaData();
            //从元数据中获取列数
            int columnCount = metaData.getColumnCount();

            //处理结果集
            if (resultSet.next()) {
                Customers customers = new Customers();
                //处理结果集的一行数据的每一列
                for (int i = 0; i < columnCount; i++) {
                    //获取列值（通过结果集）
                    Object columnValue = resultSet.getObject(i + 1);
                    //获取列的别名（通过结果集的元数据）（没有别名时，则自动获取列的列名）
                    String columnLabel = metaData.getColumnLabel(i + 1);

                    //通过反射：给customers对象指定的columnName属性赋值为columnValue
                    Field declaredField = Customers.class.getDeclaredField(columnLabel);
                    declaredField.setAccessible(true);
                    declaredField.set(customers, columnValue);
                }
                return customers;
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //关闭资源
            JDBCUtils.closeResoure(preparedStatement, connection, resultSet);
        }
        return null;
    }
```

如下使用：

```java
    @Test
    public void testQueryForCustomers() {
        //数据表的列名 与 java类的属性名 不一致时，
        //查询的sql语句中 需要给 列名 取定 别名。
        String sql = "SELECT cust_name custName,cust_email custEmail,cust_address custAddress,cust_country custCountry FROM customers WHERE cust_id = ?";
        Customers customers = queryForCustomers(sql, 10002);
        System.out.println(customers);
    }
```

### ③对不同的表通用（单条记录）

```java
    /**
     * 对不同数据表通用的查询单条记录的方法：返回单条记录
     * @param tClass
     * @param sql
     * @param args
     * @param <T>
     * @return
     */
    public <T> T getInstance(Class<T> tClass, String sql, Object... args) {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
            connection = JDBCUtils.getConnection();
            preparedStatement = connection.prepareStatement(sql);
            for (int i = 0; i < args.length; i++) {
                preparedStatement.setObject(i + 1, args[i]);
            }

            //执行sql语句，返回结果集
            resultSet = preparedStatement.executeQuery();
            //获取结果集的元数据
            ResultSetMetaData metaData = resultSet.getMetaData();
            //从元数据中获取列数
            int columnCount = metaData.getColumnCount();

            //处理结果集
            if (resultSet.next()) {
                T t = tClass.newInstance();
                //处理结果集的一行数据的每一列
                for (int i = 0; i < columnCount; i++) {
                    //获取列值（通过结果集）
                    Object columnValue = resultSet.getObject(i + 1);
                    //获取列的别名（通过结果集的元数据）（没有别名时，则自动获取列的列名）
                    String columnLabel = metaData.getColumnLabel(i + 1);

                    //通过反射：给 t 对象指定的columnName属性赋值为columnValue
                    Field declaredField = tClass.getDeclaredField(columnLabel);
                    declaredField.setAccessible(true);
                    declaredField.set(t, columnValue);
                }
                return t;
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //关闭资源
            JDBCUtils.closeResoure(preparedStatement, connection, resultSet);
        }
        return null;
    }
```

使用：

```java
    @Test
    public void testGetInstance() {
        String sql = "SELECT cust_name custName,cust_email custEmail,cust_address custAddress,cust_country custCountry FROM customers WHERE cust_id = ?";
        Customers customers = getInstance(Customers.class, sql, 10002);
        System.out.println(customers);
    }
```

### ④对不同的表通用（多条记录）

```java
    /**
     * 对不同数据表通用的查询多条记录的方法：返回多条记录
     * @param tClass
     * @param sql
     * @param args
     * @param <T>
     * @return
     */
    public <T> List<T> getForList(Class<T> tClass, String sql, Object... args) {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
            connection = JDBCUtils.getConnection();
            preparedStatement = connection.prepareStatement(sql);
            for (int i = 0; i < args.length; i++) {
                preparedStatement.setObject(i + 1, args[i]);
            }

            //执行sql语句，返回结果集
            resultSet = preparedStatement.executeQuery();
            //获取结果集的元数据
            ResultSetMetaData metaData = resultSet.getMetaData();
            //从元数据中获取列数
            int columnCount = metaData.getColumnCount();

            //创建存放多条记录的集合
            ArrayList<T> tArrayList = new ArrayList<>();
            //处理结果集
            while (resultSet.next()) {
                T t = tClass.newInstance();
                //处理结果集的一行数据的每一列
                for (int i = 0; i < columnCount; i++) {
                    //获取列值（通过结果集）
                    Object columnValue = resultSet.getObject(i + 1);
                    //获取列的别名（通过结果集的元数据）（没有别名时，则自动获取列的列名）
                    String columnLabel = metaData.getColumnLabel(i + 1);

                    //通过反射：给 t 对象指定的columnName属性赋值为columnValue
                    Field declaredField = tClass.getDeclaredField(columnLabel);
                    declaredField.setAccessible(true);
                    declaredField.set(t, columnValue);
                }
                tArrayList.add(t);
            }
            return tArrayList;
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //关闭资源
            JDBCUtils.closeResoure(preparedStatement, connection, resultSet);
        }
        return null;
    }
```

使用：

```java
    @Test
    public void testGetForList() {
        String sql = "SELECT cust_name custName,cust_email custEmail,cust_address custAddress,cust_country custCountry FROM customers WHERE cust_id > ?";
        List<Customers> customersList = getForList(Customers.class, sql, 10002);
        customersList.forEach(System.out::println);
    }
```

### ⑤查询操作的基本流程图:

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200719202815.png)

# 三、Java 与 MySQL 数据类型转换

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200719141032.png)

# 四、PreparedStatement 和 Statement 的区别

**1、代码的 可读性 和 可维护性。**

**2、PreparedStatement 能最大可能提高性能:**

- DBServer会对预编译语句提供性能优化。因为预编译语句有可能被重复调用,所以语句在被DBServer的
  编译器编译后的执行代码被缓存下来。那么下次调用时只要是相同的预编译语句就不需要编译。只要将参
  数直接传入编译过的语句执行代码中就会得到执行。
- 在statement语句中,即使是相同操作但因为数据内容不一样，所以整个语句本身不能匹配，没有缓存语句的
  意义.事实是没有数据库会对普通语句编译后的执行代码缓存。这样每执行-次都要对传入的语句编译一
  次。
- (语法检查,语义检查,翻译成_二进制命令,缓存)

**3、PreparedStatement 可以防止SQL注入**