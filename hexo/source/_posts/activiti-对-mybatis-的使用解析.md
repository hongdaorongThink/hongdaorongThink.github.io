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


## getxxxEntityManager 是 session的子类

xxxEntityManager 将请求转到session, activiti 默认创建了需要的 xxxEntityManager

commandContext.getExecutionEntityManager()




## historicTaskInstanceQueryImpl

查询列表,
org.activiti.engine.impl.HistoricTaskInstanceQueryImpl#executeList


	if (includeTaskLocalVariables || includeProcessVariables) {
	  tasks = commandContext
		  .getHistoricTaskInstanceEntityManager()
		  .findHistoricTaskInstancesAndVariablesByQueryCriteria(this);
	} else {
	  tasks = commandContext
		  .getHistoricTaskInstanceEntityManager()
		  .findHistoricTaskInstancesByQueryCriteria(this);
	}


org.activiti.engine.impl.persistence.entity.HistoricTaskInstanceEntityManager#findHistoricTaskInstancesByQueryCriteria

	getDbSqlSession().selectList("selectHistoricTaskInstancesByQueryCriteria", historicTaskInstanceQuery);
	
org.activiti.engine.impl.db.DbSqlSession#selectList(java.lang.String, org.activiti.engine.impl.db.ListQueryParameterObject)

	selectListWithRawParameter(statement, parameter, parameter.getFirstResult(), parameter.getMaxResults());
	statement = dbSqlSessionFactory.mapStatement(statement);    
	List loadedObjects = sqlSession.selectList(statement, parameter);
	
org.activiti.engine.impl.db.DbSqlSessionFactory#mapStatement
	
org.apache.ibatis.session.SqlSession#selectList(java.lang.String, java.lang.Object)


	
	
	
## activiti 是如何初始化 mybatis 的

观察 :
DbSqlSession,
DbSqlSessionFactory,




自动配置 

// 可以配置的 activiti 参数
org.activiti.spring.boot.ActivitiProperties

	private List<String> customMybatisMappers;  // 用户自己的 mybatis mapper
	private List<String> customMybatisXMLMappers;  // 用户自己的 mybatis xml mapper 

org.activiti.spring.boot.AbstractProcessEngineAutoConfiguration#processEngine

org.activiti.spring.boot.AbstractProcessEngineConfiguration#springProcessEngineBean
	ProcessEngineFactoryBean processEngineFactoryBean = new ProcessEngineFactoryBean();
    processEngineFactoryBean.setProcessEngineConfiguration(configuration);
	
org.activiti.spring.ProcessEngineFactoryBean#getObject
	this.processEngine = processEngineConfiguration.buildProcessEngine();

org.activiti.engine.impl.cfg.ProcessEngineConfigurationImpl#init
		
org.activiti.engine.impl.cfg.ProcessEngineConfigurationImpl#initDataSource
		
    protected void init() {
		initConfigurators();
		configuratorsBeforeInit();
		initProcessDiagramGenerator();
		initHistoryLevel();
		initExpressionManager();
		initDataSource();
		initVariableTypes();
		initBeans();
		initFormEngines();
		initFormTypes();
		initScriptingEngines();
		initClock();
		initBusinessCalendarManager();
		initCommandContextFactory();
		initTransactionContextFactory();
		initCommandExecutors();
		initServices();
		initIdGenerator();
		initDeployers();
		initJobHandlers();
		initJobExecutor();
		initAsyncExecutor();
		initTransactionFactory();
		initSqlSessionFactory();
		initSessionFactories();
		initJpa();
		initDelegateInterceptor();
		initEventHandlers();
		initFailedJobCommandFactory();
		initEventDispatcher();
		initProcessValidator();
		initDatabaseEventLogging();
		configuratorsAfterInit();
	  }

org.activiti.engine.impl.cfg.ProcessEngineConfigurationImpl#initSqlSessionFactory
	
	inputStream = getMyBatisXmlConfigurationSteam();
	Environment environment = new Environment("default", transactionFactory, dataSource);
	Reader reader = new InputStreamReader(inputStream);
	
	Configuration configuration = initMybatisConfiguration(environment, reader, properties);
	sqlSessionFactory = new DefaultSqlSessionFactory(configuration);
	
	
	
	
org.activiti.engine.impl.cfg.ProcessEngineConfigurationImpl#initSessionFactories
		


org.activiti.engine.impl.cfg.ProcessEngineConfigurationImpl#initCustomMybatisMappers	

org.activiti.engine.impl.cmd.ExecuteCustomSqlCmd#execute
	Mapper mapper = commandContext.getDbSqlSession().getSqlSession().getMapper(mapperClass);
	return customSqlExecution.execute(mapper);
	
	
	
## activiti-springboot使用自定义mybatis-mapper

[1]: https://segmentfault.com/a/1190000005661603

在application.properties新增自定义配置

	spring.activiti.customMybatisXMLMappers=com/codecraft/dao/ProcInstMapper.xml

	spring.activiti.customMybatisMappers=com.codecraft.dao.ProcInstMapper
	
使用方式

```java 
public List<ProcInstVo> getAllProcInsts(){

	return managementService.executeCustomSql(new AbstractCustomSqlExecution<ProcInstMapper,List<ProcInstVo>>(ProcInstMapper.class) {

		@Override

		public List<ProcInstVo> execute(ProcInstMapper procInstMapper) {

			return procInstMapper.selectAllProcInsts();

		}

	});

}
```	


```java 
ProcessEngineConfiguration cfg = ProcessEngineConfiguration.createProcessEngineConfigurationFromResource("activiti.cfg.xml");
ProcessEngine processEngine = cfg.buildProcessEngine();
ManagementService managementService = processEngine.getManagementService();

// 获取指定表的数据
TablePage tablePage = managementService.createTablePageQuery()
                .tableName(managementService.getTableName(ProcessDefinitionEntity.class))
                .listPage(0,100);
List<Map<String,Object>> rowsList = tablePage.getRows();
for (Map<String,Object> row : rowsList){
     logger.info("row={}",row);
}
```


