# MyBatis

>> http://www.mybatis.org/mybatis-3/zh/java-api.html

## 一、关键类

接口|实现类|功能
---|---|---
SqlSessionFactory|DefaultSqlSessionFactory|获取SqlSession
SqlSession|DefaultSqlSession|执行语句、提交或回滚事务和获取映射器
|MappedStatement|
|BoundSql|sql和sql参数
Executor|BaseExecutor|执行sql语句执行对象
StatementHandler|SimpleStatementHandler|
ResultSetHandler|DefaultResultSetHandler|处理ResultSet

### 1. SqlSessionFactory

SqlSessionFactory 有六个方法创建 SqlSession 实例。通常来说，当你选择这些方法时你需要考虑以下几点：

- 事务处理：我需要在 session 使用事务或者使用自动提交功能（auto-commit）吗？（通常意味着很多数据库和/或 JDBC 驱动没有事务）
- 连接：我需要依赖 MyBatis 获得来自数据源的配置吗？还是使用自己提供的配置？
- 执行语句：我需要 MyBatis 复用预处理语句和/或批量更新语句（包括插入和删除）吗？

### 2. SqlSession

在这里你会看到所有执行语句、提交或回滚事务和获取映射器实例的方法。

## 二、spring boot整合mybatis
