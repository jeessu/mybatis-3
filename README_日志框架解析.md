MyBatis SQL Mapper Framework for Java
=====================================
2022-4-26
mybatis-日志框架
切入类 org.apache.ibatis.logging.LogFactory
测试类 org.apache.ibatis.logging.LogFactoryTest
一、适配器模式的运用 
1).工厂类中静态块static{} 尝试获取日志类的实现
static {
    tryImplementation(LogFactory::useSlf4jLogging);
    tryImplementation(LogFactory::useCommonsLogging);
    tryImplementation(LogFactory::useLog4J2Logging);
    tryImplementation(LogFactory::useLog4JLogging);
    tryImplementation(LogFactory::useJdkLogging);
    tryImplementation(LogFactory::useNoLogging);
  }
2).所有的实现类（如useSlf4jLogging，useLog4J2Logging等）都经过适配 实现Log接口，最终实例化对应日志类
private static Constructor<? extends Log> logConstructor;

二、代理模式 （需要对java动态代理有一定的了解）

切入类   org.apache.ibatis.logging.jdbc.BaseJdbcLogger
        org.apache.ibatis.logging.jdbc.ConnectionLogger
        org.apache.ibatis.logging.jdbc.PreparedStatementLogger.invoke

1）重点：从BaseJdbcLogger继承类中可以看到，BaseJdbcLogger 的子类同时会实现 InvocationHandler 接口。
2）代理说明：
- 在ConnectionLogger实现中，其底层维护了一个Connection对象的引用，在ConnectionLogger.newInstance() 方法中会使用 JDK 动态代理的方式为这个 Connection 对象创建相应的代理对象。
- invoke() 方法是代理对象的核心方法，在该方法中，ConnectionLogger 会为 prepareStatement()、prepareCall()、createStatement() 三个方法添加代理逻辑。

org.apache.ibatis.logging.jdbc.ConnectionLogger.invoke 方法中查看代理模式实现

3）实例化方法
public static Connection newInstance(Connection conn, Log statementLog, int queryStack) {
    InvocationHandler handler = new ConnectionLogger(conn, statementLog, queryStack);
    ClassLoader cl = Connection.class.getClassLoader();
    return (Connection) Proxy.newProxyInstance(cl, new Class[]{Connection.class}, handler);
  }
  
4）代理对象获取 ConnectionLogger.newInstance(connection, statementLog, queryStack);

