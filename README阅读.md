MyBatis SQL Mapper Framework for Java
=====================================
2022-3-23 深入数据源和事务，把握持久化框架的两个关键命脉
http://learn.lianglianglee.com/%E4%B8%93%E6%A0%8F/%E6%B7%B1%E5%85%A5%E5%89%96%E6%9E%90%20MyBatis%20%E6%A0%B8%E5%BF%83%E5%8E%9F%E7%90%86-%E5%AE%8C/07%20%20%E6%B7%B1%E5%85%A5%E6%95%B0%E6%8D%AE%E6%BA%90%E5%92%8C%E4%BA%8B%E5%8A%A1%EF%BC%8C%E6%8A%8A%E6%8F%A1%E6%8C%81%E4%B9%85%E5%8C%96%E6%A1%86%E6%9E%B6%E7%9A%84%E4%B8%A4%E4%B8%AA%E5%85%B3%E9%94%AE%E5%91%BD%E8%84%89.md
javax.sql.DataSource 是 Java 语言中用来抽象数据源的接口，其中定义了所有数据源实现的公共行为，
MyBatis 自身提供的数据源实现也要实现该接口。MyBatis 提供了两种类型的数据源实现，分别是 PooledDataSource 和 UnpooledDataSource
（1）PooledConnection
PooledConnection 是 MyBatis 中定义的一个 InvocationHandler 接口实现类，其中封装了真正的 java.sql.Connection 对象以及相关的代理对象，这里的代理对象就是通过上一讲介绍的 JDK 动态代理产生的。
（2）PoolState
PoolState这个类，它负责管理连接池中所有 PooledConnection 对象的状态，维护了两个 ArrayList <PooledConnection> 集合按照 PooledConnection 对象的状态分类存储，
其中 idleConnections 集合用来存储空闲状态的 PooledConnection 对象，
activeConnections 集合用来存储活跃状态的 PooledConnection 对象。
(3) 获取连接 popConnection()

（4）释放连接 pushConnection()

（5）检测连接可用性 
通过 PooledConnection.isValid() 方法实现的，在该方法中会检测三个方面：
valid 字段值为 true；
realConnection 字段值不为空；
执行 PooledDataSource.pingConnection() 方法，返回值为 true。

2.事务
Transaction 接口是 MyBatis 中对数据库事务的抽象，其中定义了提交事务、回滚事务，以及获取事务底层数据库连接的方法。
JdbcTransaction、ManagedTransaction 是 MyBatis 自带的两个 Transaction 接口实现，这里也使用到了工厂方法模式