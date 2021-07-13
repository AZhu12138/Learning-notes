# 一、Apache-DBUtils 简介

- commons-dbutils 是Apache组织提供的一个开源JDBC工具类库,它是对JDBC的简单封装,学习成本极低,并且使用dbutils能极大简化 jdbc 编码的工作量,同时也不会影响程序的性能。
- API介绍:
  org.apache.commons.dbutils.QueryRunner
  org.apache.commons.dbutils.ResultSetHandler
  工具类: org.apache.commons.dbutils.DbUtils

# 二、使用

需要导入 DBUtils 的一个jar包

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200726155437.png)

## 1、改（增删 类似）

```java
    @Test
    public void testUpdate() {
        Connection connection = null;
        try {
            connection = JDBCUtils.getConnectionFromDruid();
            String sql = "update customers set cust_email=? where cust_id = ?";

            QueryRunner runner = new QueryRunner();
            int updateCount = runner.update(connection, sql, "mh@qq.com", 10002);

            System.out.println("修改了" + updateCount + "条记录。");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.closeResoure(null, connection);
        }
    }
```

## 2、查（一条）

```java
    @Test
    /**
     * BeanHandler：是ResultSetHandler接口的实现类，用于封装表中的一条记录
     */
    public void testQuery01() {
        Connection connection = null;
        try {
            connection = JDBCUtils.getConnectionFromDruid();
            String sql = "select cust_id custId,cust_name custName,cust_email custEmail from customers where cust_id = ?";

            BeanHandler<Customers> beanHandler = new BeanHandler<>(Customers.class);

            QueryRunner runner = new QueryRunner();
            Customers customers = runner.query(connection, sql, beanHandler, 10005);
            System.out.println(customers);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.closeResoure(null, connection);
        }
    }
```

## 3、查（多条）

```java
    @Test
    /**
     * BeanListHandler：是ResultSetHandler接口的实现类，用于封装表中的多条记录构成的的集合
     */
    public void testQuery02() {
        Connection connection = null;
        try {
            connection = JDBCUtils.getConnectionFromDruid();
            String sql = "select cust_id custId,cust_name custName,cust_email custEmail from customers where cust_id < ?";

            BeanListHandler<Customers> handler = new BeanListHandler<>(Customers.class);
            QueryRunner runner = new QueryRunner();

            List<Customers> customersList = runner.query(connection, sql, handler, 10005);
            customersList.forEach(System.out::println);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.closeResoure(null, connection);
        }
    }
```

## 4、查（特殊值）

```java
    @Test
    /**
     * ScalarHandler：用于查询特殊值
     */
    public void testQuery03() {
        Connection connection = null;
        try {
            connection = JDBCUtils.getConnectionFromDruid();
            String sql = "select count(*) from customers";

            ScalarHandler handler = new ScalarHandler();
            QueryRunner runner = new QueryRunner();

            Long count = (Long) runner.query(connection, sql, handler);
            System.out.println(count);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.closeResoure(null, connection);
        }
    }
```

## 5、在 BaseDAO 中使用

以下为 update 方法：

还有其他的方法没有写出，可自行写入。

```java
    /**
     * 直接使用DBUtils的update方法
     * @param connection
     * @param sql
     * @param args
     */
    public void updateByDBUtils(Connection connection, String sql, Object ...args) {
        QueryRunner runner = new QueryRunner();
        try {
            runner.update(connection, sql, args);
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
    }
```

# 三、使用DBUtils关闭资源

可直接在类 JDBCUtils 中添加以下代码：

```java
    /**
     * 使用了DBUtils里面的方法
     * 关闭 Statement 和 connection 的方法
     * @param Statement
     * @param connection
     */
    public static void closeResoureByDBUtils(Statement Statement, Connection connection) {
        DbUtils.closeQuietly(Statement);
        DbUtils.closeQuietly(connection);
    }

    /**
     * 使用了DBUtils里面的方法
     * 关闭 Statement 和 connection 和 resultSet 的方法
     * @param Statement
     * @param connection
     * @param resultSet
     */
    public static void closeResoureByDBUtils(Statement Statement, Connection connection, ResultSet resultSet) {
        DbUtils.closeQuietly(Statement);
        DbUtils.closeQuietly(connection);
        DbUtils.closeQuietly(resultSet);
    }
```

