---
title: activiti 对 mybatis 的使用解析
date: 2019-08-09 14:24:02
tags:
- activiti 
- mybatis 
---

## 要点

* 每个基于 MyBatis 的应用都是以一个 **SqlSessionFactory** 的实例为核心的
* activit 的配置核心是 **ProcessEngineConfigurationImpl**


## 创建 mybatis SqlSessionFactory

org.activiti.engine.impl.cfg.ProcessEngineConfigurationImpl#initSqlSessionFactory

读取 xml 配置文件

```java 
inputStream = getMyBatisXmlConfigurationSteam();
Configuration configuration = initMybatisConfiguration(environment, reader, properties);
sqlSessionFactory = new DefaultSqlSessionFactory(configuration);
```





## 用于应用查询的 mapping 文件

文件路径: Activiti\activiti-engine\src\main\resources\org\activiti\db\mapping



## activiti 的 createxxxQuery

比如  runtimeService 的 createExecutionQuery,
返回 ExecutionQuery 的 派生类对象 ExecutionQueryImpl 对象

query 实现了 execute 接口,

executeList 方法

> List<?> executions = commandContext.getExecutionEntityManager().findExecutionsByQueryCriteria(this, page);


ExecutionEntityManager 负责调用mybatis 的接口

> getDbSqlSession().selectList("selectExecutionsByQueryCriteria", executionQuery, page);


DbSqlSession 包装了对 mybatis sqlSession 的调用

List loadedObjects = sqlSession.selectList(statement, parameter);




