# 一、方式一

普通地、直接地、循环执行 sql语句

```java
    /**
     * 方式一
     * 插入20000数据需要：61374 毫秒
     */
    @Test
    public void testInsert01() {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        try {
            long startTime = System.currentTimeMillis();

            connection = JDBCUtils.getConnection();
            String sql = "insert into goods(name)values(?)";
            preparedStatement = connection.prepareStatement(sql);
            //循环执行sql语句
            for (int i = 1; i <= 20000; i++) {
                preparedStatement.setObject(1, "name_" + i);
                preparedStatement.execute();
            }

            long endTime = System.currentTimeMillis();
            System.out.println("花费的时间为：" + (endTime - startTime) + " 毫秒");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.closeResoure(preparedStatement, connection);
        }
    }
```

# 二、方式二（批处理）

使用批处理

```java
    /**
     * 方式二（批处理）
     * 1、使用：addBatch，executeBatch，clearBatch
     * 2、MySql默认关闭批处理，需要在配置文件jdbc.properties中的url后面加上：
     *   ?rewriteBatchedStatements=true
     * 3、要使用支持批处理的MySql驱动
     *
     * 插入20000数据需要：2063 毫秒
     * 插入1000000数据需要：15528 毫秒
     */
    @Test
    public void testInsert02() {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        try {
            long startTime = System.currentTimeMillis();

            connection = JDBCUtils.getConnection();
            String sql = "insert into goods(name)values(?)";
            preparedStatement = connection.prepareStatement(sql);

            for (int i = 1; i <= 1000000; i++) {
                preparedStatement.setObject(1, "name_" + i);

                //1、攒sql
                preparedStatement.addBatch();
                if (i % 500 == 0) {
                    //2、执行batch
                    preparedStatement.executeBatch();
                    //3、清空batch
                    preparedStatement.clearBatch();
                }
            }

            long endTime = System.currentTimeMillis();
            System.out.println("花费的时间为：" + (endTime - startTime) + " 毫秒");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.closeResoure(preparedStatement, connection);
        }
    }
```

# 三、方式三（控制数据的提交）

```java
    /**
     * 方式三（控制数据的提交）
     * 插入1000000数据需要：8107 毫秒
     */
    @Test
    public void testInsert03() {
        Connection connection = null;
        PreparedStatement preparedStatement = null;
        try {
            long startTime = System.currentTimeMillis();

            connection = JDBCUtils.getConnection();

            //设置不允许自动提交
            connection.setAutoCommit(false);

            String sql = "insert into goods(name)values(?)";
            preparedStatement = connection.prepareStatement(sql);

            for (int i = 1; i <= 1000000; i++) {
                preparedStatement.setObject(1, "name_" + i);

                //1、攒sql
                preparedStatement.addBatch();
                if (i % 500 == 0) {
                    //2、执行batch
                    preparedStatement.executeBatch();
                    //3、清空batch
                    preparedStatement.clearBatch();
                }
            }

            //统一现在提交
            connection.commit();

            long endTime = System.currentTimeMillis();
            System.out.println("花费的时间为：" + (endTime - startTime) + " 毫秒");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.closeResoure(preparedStatement, connection);
        }
    }
```

