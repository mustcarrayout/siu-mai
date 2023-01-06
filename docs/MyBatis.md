
# MyBatis学习

## 复盘

### 我是怎么学的?

1. 持久层? 那就是和JDBC一类的,和JDBC有什么关系,区别是什么?
2. 结构(架构)是什么?
3. 扩展点在哪?
4. 类似的有哪些?
5. 有什么特点?
6. 自我判断学习得如何了?

## 是什么?

> MyBatis is a first class **persistence framework** with support for **custom SQL**, stored procedures and advanced mappings. MyBatis eliminates almost all of the **JDBC** code and **manual setting of parameters and retrieval of results**. MyBatis can use simple **XML or Annotations** for configuration and map primitives, Map interfaces and Java POJOs (Plain Old Java Objects) to database records.

```xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>x.x.x</version>
</dependency>
```

## 架构

1. SqlSessionFactory|SqlSession|SqlSessionFactoryBuilder
2. XML -> Resources -> define Configuration
3. Mapper -> dynamic proxy(动态代理)
4. 基于JDBC的封装实现的ORM组件
5. SQL的解析、参数的绑定、结果集的反射等;

### SqlSession

```java
public interface SqlSession extends Closeable {
  <T> T selectOne(String statement);
  <T> T selectOne(String statement, Object parameter);

  <E> List<E> selectList(String statement);
  <E> List<E> selectList(String statement, Object parameter);
  // 分页
  <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds);

  // ...

  void commit();
  void commit(boolean force);

  void rollback();
  void rollback(boolean force);
  // 有考虑到使用缓存
  void clearCache();
  // 动态代理
  <T> T getMapper(Class<T> type);
  // 获取当前配置
  Configuration getConfiguration();
  // 获取db链接
  Connection getConnection();

}
```

看代码知道以下几件事情:

1. 是一个接口,应该有默认的实现(DefaultSession);
2. 声明CRUD、function、store procedure等;
3. 事务提交、事务回滚;
4. 可以获取当前的mybatis的配置;
5. 可以获取到即时的db connection;
6. 可以获取到动态代理对象();
7. 有涉及到缓存(缓存什么?查询结果);


```java
public class DefaultSqlSession implements SqlSession {

  private final Configuration configuration;
  // 2
  private final Executor executor;

  private final boolean autoCommit;
  private boolean dirty;
  private List<Cursor<?>> cursorList;

  // 拿到connection才能执行sql
  @Override
  public Connection getConnection() {
    try {
      return executor.getTransaction().getConnection();
    } catch (SQLException e) {
      throw ExceptionFactory.wrapException("Error getting a new connection.  Cause: " + e, e);
    }
  }

  // 真实的操盘手是 executor
  // 问题: 为什么不直接在这里创建db conneciton?
  @Override
  public void clearCache() {
    executor.clearLocalCache();
  }

  // 执行查询
  @Override
  public <E> List<E> selectList(String statement, Object parameter, RowBounds rowBounds) {
    try {
      // 1
      MappedStatement ms = configuration.getMappedStatement(statement);

      return executor.query(ms, wrapCollection(parameter), rowBounds, Executor.NO_RESULT_HANDLER);
    } catch (Exception e) {
      throw ExceptionFactory.wrapException("Error querying database.  Cause: " + e, e);
    } finally {
      ErrorContext.instance().reset();
    }
  }

  // 参数绑定
  private Object wrapCollection(final Object object) {
    return ParamNameResolver.wrapToMapIfCollection(object, null);
  }
}  
```

> sql的操盘手

1. Executor -> BaseExecutor -> SimpleExecutor|BatchExecutor|ReuseExecutor
2. 这里有增删改查,是处理缓存的好入口,缓存之间是要同步的,读写一致;
3. 最终实现的地方在哪?各自的子类.
4. 四大金刚的关系是如何的?

```java
public class Configuration {
  // 常见 Executor
  public Executor newExecutor(Transaction transaction, ExecutorType executorType) {
    executorType = executorType == null ? defaultExecutorType : executorType;
    executorType = executorType == null ? ExecutorType.SIMPLE : executorType;
    Executor executor;
    if (ExecutorType.BATCH == executorType) {
      executor = new BatchExecutor(this, transaction);
    } else if (ExecutorType.REUSE == executorType) {
      executor = new ReuseExecutor(this, transaction);
    } else {
      executor = new SimpleExecutor(this, transaction);
    }
    if (cacheEnabled) {
      executor = new CachingExecutor(executor);
    }
    executor = (Executor) interceptorChain.pluginAll(executor);
    return executor;
  }
}
```


```java
public interface Executor {
  // 结果集
  ResultHandler NO_RESULT_HANDLER = null;
  // 
  int update(MappedStatement ms, Object parameter) throws SQLException;
  // 缓存
  <E> List<E> query(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, CacheKey cacheKey, BoundSql boundSql) throws SQLException;

  void commit(boolean required) throws SQLException;
  void rollback(boolean required) throws SQLException;
  CacheKey createCacheKey(MappedStatement ms, Object parameterObject, RowBounds rowBounds, BoundSql boundSql);

  boolean isCached(MappedStatement ms, CacheKey key);
  // mybatis的事务,不是jdbc的.
  Transaction getTransaction();
}
// Executor => final Executor executor = configuration.newExecutor(tx, execType);
// 默认 => executor = new SimpleExecutor(this, transaction);
```

```java

public class SimpleExecutor extends BaseExecutor {
  @Override
  public <E> List<E> doQuery(MappedStatement ms, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) throws SQLException {
    Statement stmt = null;
    try {
      Configuration configuration = ms.getConfiguration();
      // StatementHandler 搞什么的?
      // 问题: db的connection在这里对象中,从参数来看并没有传过去,真正执行的地方在哪?
      StatementHandler handler = configuration.newStatementHandler(wrapper, ms, parameter, rowBounds, resultHandler, boundSql);
      stmt = prepareStatement(handler, ms.getStatementLog());
      // 问题:为什么使用handler.的调用? 链?
      return handler.query(stmt, resultHandler);
    } finally {
      closeStatement(stmt);
    }
  }

  // 拿到db connection,交给Statement,Statement有执行能力.
  private Statement prepareStatement(StatementHandler handler, Log statementLog) throws SQLException {
    Statement stmt;
    Connection connection = getConnection(statementLog);
    stmt = handler.prepare(connection, transaction.getTimeout());
    handler.parameterize(stmt);
    return stmt;
  }

}
```

```java
public interface StatementHandler {

  Statement prepare(Connection connection, Integer transactionTimeout)
      throws SQLException;

  void parameterize(Statement statement)
      throws SQLException;

  void batch(Statement statement)
      throws SQLException;

  int update(Statement statement)
      throws SQLException;

  <E> List<E> query(Statement statement, ResultHandler resultHandler)
      throws SQLException;

  <E> Cursor<E> queryCursor(Statement statement)
      throws SQLException;

  BoundSql getBoundSql();

  ParameterHandler getParameterHandler();
}

public class SimpleStatementHandler extends BaseStatementHandler {
  public SimpleStatementHandler(Executor executor, MappedStatement mappedStatement, Object parameter, RowBounds rowBounds, ResultHandler resultHandler, BoundSql boundSql) {
    super(executor, mappedStatement, parameter, rowBounds, resultHandler, boundSql);
  }

  @Override
  public <E> List<E> query(Statement statement, ResultHandler resultHandler) throws SQLException {
    String sql = boundSql.getSql();
    statement.execute(sql);
    return resultSetHandler.handleResultSets(statement);
  }
  
}

```

```java
public interface ResultSetHandler {

  <E> List<E> handleResultSets(Statement stmt) throws SQLException;

  <E> Cursor<E> handleCursorResultSets(Statement stmt) throws SQLException;

  void handleOutputParameters(CallableStatement cs) throws SQLException;

}

```





### 扩展点

#### 分页
