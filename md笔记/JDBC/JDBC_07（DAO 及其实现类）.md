# 一、父类 BaseDAO

1. 作为父类，不能被实例化
2. 作用：封装了针对数据库表的通用的操作
3. 实现了以下 4 个方法：

①通用的Update修改数据库的方法：

**public void update(Connection connection, String sql, Object ...args)**

②对不同数据表通用的查询单条记录的方法：返回单条记录：

**public <T> T getInstance(Connection connection, Class<T> tClass, String sql, Object... args)**

③对不同数据表通用的查询多条记录的方法：返回包含 多条记录 的一个集合：

**public <T> List<T> getForList(Connection connection,Class<T> tClass, String sql, Object... args)**

④用于查询特殊值的通用方法：

**public <E> E getValue(Connection connection, String sql, Object... args)**

```java
/**
 * 封装了针对数据库表的通用的操作
 */
public abstract class BaseDAO {

    /**
     * 考虑事务处理
     * 通用的Update修改数据库的方法
     * @param sql
     * @param args
     */
    public void update(Connection connection, String sql, Object ...args) {
        PreparedStatement preparedStatement = null;
        try {
            //1、预编译sql语句
            preparedStatement = connection.prepareStatement(sql);
            //2、填充占位符
            for (int i = 0; i < args.length; i++) {
                //注意关于i的参数取值
                preparedStatement.setObject(i + 1, args[i]);
            }
            //3、执行sql语句
            preparedStatement.execute();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //4、关闭资源
            JDBCUtils.closeResoure(preparedStatement, null);
        }
    }

    /**
     * 考虑事务处理
     * 对不同数据表通用的查询单条记录的方法：返回单条记录
     * @param connection
     * @param tClass
     * @param sql
     * @param args
     * @param <T>
     * @return
     */
    public <T> T getInstance(Connection connection, Class<T> tClass, String sql, Object... args) {
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
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
            JDBCUtils.closeResoure(preparedStatement, null, resultSet);
        }
        return null;
    }

    /**
     * 考虑事务处理
     * 对不同数据表通用的查询多条记录的方法：返回包含 多条记录 的一个集合
     * @param connection
     * @param tClass
     * @param sql
     * @param args
     * @param <T>
     * @return
     */
    public <T> List<T> getForList(Connection connection,Class<T> tClass, String sql, Object... args) {
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
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
            JDBCUtils.closeResoure(preparedStatement, null, resultSet);
        }
        return null;
    }

    //用于查询特殊值的通用方法
    public <E> E getValue(Connection connection, String sql, Object... args) {
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;
        try {
            //预编译sql语句
            preparedStatement = connection.prepareStatement(sql);
            for (int i = 0; i < args.length; i++) {
                preparedStatement.setObject(i + 1, args[i]);
            }

            resultSet = preparedStatement.executeQuery();
            if (resultSet.next()) {
                return (E) resultSet.getObject(1);
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            JDBCUtils.closeResoure(preparedStatement, null, resultSet);
        }
        return null;
    }

}
```

# 二、接口 CustomersDAO

1. 作用：用于**规范** Customers 表的常用操作
2. 声明多个对于 Customers 表的常用方法（可根据**需求**自行声明）

```java
public interface CustomersDAO {

    /**
     * 针对给定的Customers对象，插入到数据库表中
     * @param connection
     * @param customers
     */
    void insert(Connection connection, Customers customers);

    /**
     * 针对给定的id，删除表中的一条记录
     * @param connection
     * @param id
     */
    void deleteById(Connection connection, int id);

    /**
     * 针对内存中的对象，修改数据库表中的对象
     * @param connection
     * @param customers
     */
    void update(Connection connection, Customers customers);

    /**
     * 针对给定的id，查询对应的Customers对象
     * @param connection
     * @param id
     * @return
     */
    Customers getCustomersById(Connection connection, int id);

    /**
     * 查询表中的所有记录
     * @param connection
     * @return
     */
    List<Customers> getAll(Connection connection);

    /**
     * 返回数据表的条目数
     * @param connection
     * @return
     */
    long getCount(Connection connection);

}
```

# 三、实现类 CustomersDAOImpl

继承了父类 BaseDAO，实现了接口 CustomersDAO

实现了 CustomersDAO 声明的方法

```java
public class CustomersDAOImpl extends BaseDAO implements CustomersDAO {

    @Override
    public void insert(Connection connection, Customers customers) {
        String sql = "insert into customers(cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country,cust_contact,cust_email)value(?,?,?,?,?,?,?,?)";
        update(connection, sql, customers.getCustName(), customers.getCustAddress(), customers.getCustCity(), customers.getCustState(), customers.getCustZip(), customers.getCustCountry(), customers.getCustContact(), customers.getCustEmail());
    }

    @Override
    public void deleteById(Connection connection, int id) {
        String sql = "delete from customers where cust_id = ?";
        update(connection, sql, id);
    }

    @Override
    public void update(Connection connection, Customers customers) {
        String sql = "update customers set cust_name=?,cust_address=?,cust_city=?,cust_state=?,cust_zip=?,cust_country=?,cust_contact=?,cust_email=? where cust_id = ?";
        update(connection, sql, customers.getCustName(), customers.getCustAddress(), customers.getCustCity(), customers.getCustState(), customers.getCustZip(), customers.getCustCountry(), customers.getCustContact(), customers.getCustEmail(), customers.getCustId());
    }

    @Override
    public Customers getCustomersById(Connection connection, int id) {
        String sql = "select cust_id custId,cust_name custName,cust_address custAddress,cust_city custCity,cust_state custState,cust_zip custZip,cust_country custCountry,cust_contact custContact,cust_email custEmail from customers where cust_id = ?";
        Customers customers = getInstance(connection, Customers.class, sql, id);
        return customers;
    }

    @Override
    public List<Customers> getAll(Connection connection) {
        String sql = "select cust_id custId,cust_name custName,cust_address custAddress,cust_city custCity,cust_state custState,cust_zip custZip,cust_country custCountry,cust_contact custContact,cust_email custEmail from customers";
        List<Customers> customersList = getForList(connection, Customers.class, sql);
        return customersList;
    }

    @Override
    public long getCount(Connection connection) {
        String sql = "select count(*) from customers";
        Object value = getValue(connection, sql);
        return (long) value;
    }

}
```

# 四、测试类 CustomersDAOImplTest

用于测试 **CustomersDAOImpl**

```java
public class CustomersDAOImplTest {

    private CustomersDAOImpl dao = new CustomersDAOImpl();

    @Test
    public void insert() {
        Connection connection = null;
        try {
            connection = JDBCUtils.getConnection();
            Customers customers = new Customers(1, "huangzhuzhu", "532 street", "qingyuan", "AB", "987456", "China", "ZhuZHu", "huangzhuzhu@163.com)");
            dao.insert(connection, customers);
            System.out.println("插入成功");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.closeResoure(null,connection);
        }
    }

    @Test
    public void deleteById() {
        Connection connection = null;
        try {
            connection = JDBCUtils.getConnection();

            dao.deleteById(connection, 10008);

            System.out.println("删除成功");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.closeResoure(null,connection);
        }
    }

    @Test
    public void update() {
        Connection connection = null;
        try {
            connection = JDBCUtils.getConnection();

            Customers customers = new Customers(10009, "huangzhuzhu", "532 street", "qingyuan", "AB", "98745", "China", "ZhuZHu", "huangzhuzhu@163.com)");
            dao.update(connection,customers);

            System.out.println("修改成功");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.closeResoure(null,connection);
        }
    }

    @Test
    public void getCustomersById() {
        Connection connection = null;
        try {
            connection = JDBCUtils.getConnection();

            Customers customers = dao.getCustomersById(connection, 10009);
            System.out.println(customers);

            System.out.println("查询成功");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.closeResoure(null,connection);
        }
    }

    @Test
    public void getAll() {
        Connection connection = null;
        try {
            connection = JDBCUtils.getConnection();

            List<Customers> customersList = dao.getAll(connection);
            customersList.forEach(System.out::println);

            System.out.println("查询成功");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.closeResoure(null,connection);
        }
    }

    @Test
    public void getCount() {
        Connection connection = null;
        try {
            connection = JDBCUtils.getConnection();

            long count = dao.getCount(connection);
            System.out.println(count);

            System.out.println("获取成功");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.closeResoure(null,connection);
        }
    }

}
```

# 五、包结构

如下：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200723204738.png)

**注：**

**1、BaseDAO 封装的是对数据库的不同表 通用的方法，只需要一个。**

**2、CustomersDAO、CustomersDAOImpl、CustomersDAOImplTest 三个需要成组存在，每组对应一个数据库表**

