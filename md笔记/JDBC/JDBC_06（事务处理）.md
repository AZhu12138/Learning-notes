# 一、事务处理？

1. 事务处理的原则:

   保证所有事务都作为一个工作单元来执行，即使出现了故障，都不能改变这种执行方式。当在一个事务中执行多个操作时，要么所有的事务都被提交( commit),那么这些修改就永久地保存下来;要么数据库管理系统将放弃所作的所有修改，整个事务回滚( rollback )到最初状态。

2. 数据一旦提交，就不可回滚

3. 哪些操作会导致数据的自动提交?

   ①DDL操作一旦执行，都会自动提交。
    	set autocommit = false 对DDL操作失效

   ②DML默认情况下，一旦执行，就会自动提交。
   	我们可以通过set autocommit = false的方式取消DML操作的自动提交。

   ③默认在关闭连接时，会自动提交

# 二、代码实现

```java
    /**
     * 考虑事务处理
     * 通用的Update修改数据库的方法
     * @param sql
     * @param args
     */
    public void transactionUpdate(Connection connection,String sql,Object ...args) {
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
```

使用：

```java
    @Test
    public void testTransaction() {
        Connection connection = null;
        try {
            connection = JDBCUtils.getConnection();
            //1、取消数据的自动提交
            connection.setAutoCommit(false);

            String sql01 = "update customers set cust_zip = 12321 where cust_id = ? ";
            transactionUpdate(connection, sql01, 10004);

            //模拟异常的情况
//            System.out.println(10/0);

            String sql02 = "update customers set cust_zip = 78945 where cust_id = ? ";
            transactionUpdate(connection, sql02, 10005);

            System.out.println("修改成功。");

            //2、提交数据
            connection.commit();

        } catch (Exception e) {
            e.printStackTrace();
            System.out.println("修改失败");
            try {
                //3、回滚数据
                connection.rollback();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        } finally {
            try {
                //4、把连接设置为默认状态
                connection.setAutoCommit(true);
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
            JDBCUtils.closeResoure(null, connection);
        }
    }
```

# 三、事务的 ACID 属性

1. 原子性( Atomicity )
   原子性是指事务是一一个不可分割的工作单位,事务中的操作要么都发生,要么都不发生。
2. 一致性 ( Consistency )
   事务必须使数据库从一个一致性状态变换到另外- 个一致性状态。
3. 隔离性( Isolation )
   事务的隔离性是指一个事务的执行不能被其他事务干扰,即一个事务内部的操作及使用的数据对并发的其他事
   务是隔离的,并发执行的各个事务之间不能互相干扰。
4. 持久性( Durability)
   持久性是指一个事务一旦被提交 ，它对数据库中数据的改变就是永久性的,接下来的其他操作和数据库故障不
   应该对其有任何影响

## 2、数据库的并发问题

- 对于同时运行的多个事务,当这些事务访问数据库中相同的数据时,如果没有采取必要的隔离机制,就会导致各
  种并发问题:
  **脏读**:对于两个事务T1, T2, T1读取了已经被T2更新但还没有被提交的字段。之后,若T2回滚, T1读取的
  内容就是临时且无效的。
  **不可重复读**:对于两个事务T1, T2, T1读取了一个字段然后T2更新了该字段。之后，T1再次读取同一个字
  段,值就不同了。
  **幻读**:对于两个事务T1, T2,T1从一一个表中读取了-一个字段,然后T2在该表中插入了一些新的行。之后,如
  果T1再次读取同一个表,就会多出几行。
- 数据库事务的隔离性:数据库系统必须具有隔离并发运行各个事务的能力,使它们不会相互影响,避免各种并发
  问题。
- 一个事务与其他事务隔离的程度称为隔离级别。数据库规定了多种事务隔离级别,不同隔离级别对应不同的干
  扰程度,隔离级别越高,数据-致性就越好，但并发性越弱。

## 3、四种隔离级别

- 数据库提供的4种事务隔离级别:

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20200722155301.png)

- Oracle支持的2种事务隔离级别: READ COMMITED, SERIALIZABLE。Oracle 默认的事务隔离级别为
  READ COMMITED。
- Mysql支持4种事务隔离级别。Mysq| 默认的事务隔离级别为: REPEATABLE READ。

## 4、在MySq|中设置隔离级别

- 每启动-一个mysql程序,就会获得一个单独的数据库连接. 每个数据库连接都有一个全局变量 @@tx_ _isolation,
  表示当前的事务隔离级别。_

- 查看当前的隔离级别:

  ```mysql
  SELECT @@tx_isolation;
  ```

- 设置当前mySQL连接的隔离级别:

  ```mysql
  set transaction isolation level read committed;
  ```

- 设置数据库系统的全局的隔离级别:

  ```mysql
  set global transaction isolation level read committed;
  ```

- 补充操作:
  **创建mysq|数据库用户:**

  ```mysql
  create user tom identified by 'abc123';
  ```

  **授予权限**

  ```mysql
  #授予通过网络方 式登录的tom用户，对所有库所有表的全部权限，密码设为abc123.
  grant all privileges on *.* to tom@'%' identified by 'abc123';
  
  #给tom用户使用本地命令行方式,授予atgui gudb这个库下的所有表的插删改查的权限。
  grant select, insert, delete,update on atguigudb.* to tom@localhost identified by 'abc123';
  ```

  


- 每启动一个mysql程序,就会获得一个单独的数据库连接. 每个数据库连接都有一个全局变量 @@tx_ _isolation,
  表示当前的事务隔离级别。_

- 查看当前的隔离级别:

  ```mysql
  SELECT @@tx_ isolation;
  ```

- 设置当前mySQL连接的隔离级别:

  ```mysql
  set transaction isolation level read committed;
  ```

- 设置数据库系统的全局的隔离级别:

  ```mysql
  set global transaction isolation level read committed;
  ```

- 补充操作:
  **创建mysq|数据库用户:**

  ```mysql
  create user tom identified by 'abc123';
  ```

  **授予权限**

  ```mysql
  #授予通过网络方 式登录的t om用户，对所有库所有表的全部权限，密码设为abc123.
  grant all privileges on *.* to tom@'%' identified by 'abc123';
  
  #给tom用户使用本地命令行方式,授予atgui gudb这个库下的所有表的插删改查的权限。
  grant select, insert, delete,update on atguigudb.* to tom@localhost identified by 'abc123';
  ```

  
