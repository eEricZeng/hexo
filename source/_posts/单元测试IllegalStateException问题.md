---
title: 单元测试IllegalStateException问题
categories: java
tags:
  - java
  - 单元测试
  - junit
  - h2
  - IllegalStateException
date: 2019-09-07 22:25:44
---


该文是解决IllegalState Failed to load ApplicationContext异常中的一种。

## 背景介绍

-   spring-web-4.3.6.RELEASE
-   Junit v4.12
-   h2 v1.4.192
-   Run As单元测试类，全部测试用例可以通过
-   mvn test部分测试类下面所有的测试用例都报错

mvn test报错信息如下：

```log
testMethod(io.github.eericzeng.StudentTest) Time elapsed: 0 sec  <<< ERROR!
java.lang.IllegalStateException: Failed to load ApplicationContext
        at org.h2.message.DbException.getJdbcSQLException(DbException.java:345)
        at org.h2.message.DbException.get(DbException.java:179)
        at org.h2.message.DbException.get(DbException.java:155)
        at org.h2.command.ddl.CreateTable.update(CreateTable.java:115)
        at org.h2.command.CommandContainer.update(CommandContainer.java:98)
        at org.h2.command.Command.executeUpdate(Command.java:258)
        at org.h2.jdbc.JdbcStatement.executeInternal(JdbcStatement.java:184)
        at org.h2.jdbc.JdbcStatement.execute(JdbcStatement.java:158)
        at org.springframework.jdbc.datasource.init.ScriptUtils.executeSqlScript(ScriptUtils.java:471)
        at org.springframework.jdbc.datasource.init.ResourceDatabasePopulator.populate(ResourceDatabasePopulator.java:238)
        at org.springframework.jdbc.datasource.init.CompositeDatabasePopulator.populate(CompositeDatabasePopulator.java:87)
        at org.springframework.jdbc.datasource.init.DatabasePopulatorUtils.execute(DatabasePopulatorUtils.java:48)
        at org.springframework.jdbc.datasource.init.DataSourceInitializer.execute(DataSourceInitializer.java:108)
        at org.springframework.jdbc.datasource.init.DataSourceInitializer.afterPropertiesSet(DataSourceInitializer.java:93)
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java
:1687)
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:16
24)
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:555)

        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:483)
        at org.springframework.beans.factory.support.AbstractBeanFactory$1.getObject(AbstractBeanFactory.java:306)
        at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:230)
        at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:302)
        at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:197)
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:761)
        at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:866)
        at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:542)
        at org.springframework.test.context.web.AbstractGenericWebContextLoader.loadContext(AbstractGenericWebContextLoader.java:134)
        at org.springframework.test.context.web.AbstractGenericWebContextLoader.loadContext(AbstractGenericWebContextLoader.java:61)
        at org.springframework.test.context.support.AbstractDelegatingSmartContextLoader.delegateLoading(AbstractDelegatingSmartContextLoader.jav
a:108)
        at org.springframework.test.context.support.AbstractDelegatingSmartContextLoader.loadContext(AbstractDelegatingSmartContextLoader.java:25
1)
        at org.springframework.test.context.cache.DefaultCacheAwareContextLoaderDelegate.loadContextInternal(DefaultCacheAwareContextLoaderDelega
te.java:98)
        at org.springframework.test.context.cache.DefaultCacheAwareContextLoaderDelegate.loadContext(DefaultCacheAwareContextLoaderDelegate.java:
116)
        at org.springframework.test.context.support.DefaultTestContext.getApplicationContext(DefaultTestContext.java:83)
        at org.springframework.test.context.web.ServletTestExecutionListener.setUpRequestContextIfNecessary(ServletTestExecutionListener.java:189
)
        at org.springframework.test.context.web.ServletTestExecutionListener.prepareTestInstance(ServletTestExecutionListener.java:131)
        at org.springframework.test.context.TestContextManager.prepareTestInstance(TestContextManager.java:230)
        at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.createTest(SpringJUnit4ClassRunner.java:228)
        at org.springframework.test.context.junit4.SpringJUnit4ClassRunner$1.runReflectiveCall(SpringJUnit4ClassRunner.java:287)
        at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
        at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.methodBlock(SpringJUnit4ClassRunner.java:289)
        at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.runChild(SpringJUnit4ClassRunner.java:247)
        at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.runChild(SpringJUnit4ClassRunner.java:94)
        at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
        at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
        at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
        at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
        at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
        at org.junit.internal.runners.statements.RunBefores.evaluate(RunBefores.java:26)
        at org.springframework.test.context.junit4.statements.RunBeforeTestClassCallbacks.evaluate(RunBeforeTestClassCallbacks.java:61)
        at org.springframework.test.context.junit4.statements.RunAfterTestClassCallbacks.evaluate(RunAfterTestClassCallbacks.java:70)
        at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
        at org.springframework.test.context.junit4.SpringJUnit4ClassRunner.run(SpringJUnit4ClassRunner.java:191)
        at org.apache.maven.surefire.junit4.JUnit4Provider.execute(JUnit4Provider.java:264)
        at org.apache.maven.surefire.junit4.JUnit4Provider.executeTestSet(JUnit4Provider.java:153)
        at org.apache.maven.surefire.junit4.JUnit4Provider.invoke(JUnit4Provider.java:124)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.maven.surefire.util.ReflectionUtils.invokeMethodWithArray2(ReflectionUtils.java:208)
        at org.apache.maven.surefire.booter.ProviderFactory$ProviderProxy.invoke(ProviderFactory.java:158)
        at org.apache.maven.surefire.booter.ProviderFactory.invokeProvider(ProviderFactory.java:86)
        at org.apache.maven.surefire.booter.ForkedBooter.runSuitesInProcess(ForkedBooter.java:153)
        at org.apache.maven.surefire.booter.ForkedBooter.main(ForkedBooter.java:95)

Results :

Tests in error:
   ? IllegalState Failed to load ApplicationContext
```

## 异常排查

由异常信息getJdbcSQLException/ApplicationContext推断出配置信息有问题，而且是关于数据库的异常，getJdbcSQLException是h2抛出的，说明h2在初始化就存在问题。配置单元测试log4j日志，log4j日志关键信息如下：

```log
Caused by: org.h2.jdbc.JdbcSQLException: Table "student" already exists; SQL statement:
```

执行SQL创建数据表失败，说明建表语句被执行了多次。检查SQL脚本发现，脚本中只有创建数据表的语句，并没有DROP语句，所以每次执行测试用例时都会失败。

## 解决办法

根据本次异常排查情况，在CREATE TABLE前添加如下SQL语句：

```sql
DROP TABLE IF EXISTS 'student';
```
