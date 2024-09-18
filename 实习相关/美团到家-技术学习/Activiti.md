## 一、Activiti介绍

### 1.1 介绍

`Activiti `是一个工作流引擎， `activiti `可以将业务系统中复杂的业务流程抽取出来，使用专门的建模语言（`BPMN2.0`）进行定义，业务系统按照预先定义的流程进行执行，业务系统的业务流程由 `activiti `进行管理，减少业务系统由于流程变更进行系统升级改造的工作量，从而提高系统的健壮性，同时也减少了系统开发维护成本。

- `BPM`：`Business Process Management`，业务流程管理。 
- `BPM`系统：业务流程管理的系统。 
- `BPMN`：`Business Process Model And Notation`，业务流程模型和符号 是由 `BPMI`（`BusinessProcess Management Initiative`）开发的一套标准的业务流程建模符号，使用 `BPMN` 提供的符号可以创建业务流程。 

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202409090023227.jpg" style="zoom:60%;" />

> - `startEvent`、`endEvent`表示开始、结束事件 
>
> - `UserTask`、`ScriptTask`、`ServiceTask`表示各种任务，比如审批使用的就是`UserTask `
>
> - `xxxGateWay`表示各种网管，在定义流程时可以指定条件节点 



### 1.2 activiti使用流程

1. 定义流程：使用`Activiti`的建模工具定义业务流程`.bpmn`文件。 
2. 部署流程定义：使用`Activiti`提供的API把流程定义内容存储起来，在`Acitivti`执行过程可以查询定义的内容。`Activiti`是通过数据库来存储业务流程的。 
3. 启动流程实例：流程实例也叫`Processlnstance`。启动一个流程实例表示开始一次业务流程的运作。例如员工提交请假申请后，就可以开启一个流程实例，从而推动后续的审批等操作。 
4. 用户查询待办任务（task）：因为系统的业务流程都交给了activiti管理，通过activiti就可以查询当前流程执行到哪个步骤。当前用户需要办理哪些任务也就同样可以由activiti帮我们管理，开发人员不需要自己编写sql语句进行查询了。 
5. 用户办理任务：用户查询到自己的待办任务后，就可以办理某个业务，如果这个业务办理完成还需要其他用户办理，就可以由`activiti`帮我们把工作流程往后面的步骤推动。
6. 流程结束：当任务办理完成没有下一个任务节点后，这个流程实例就执行完成了。 



# 二、activiti库表

## 1、常用service

- `RepositoryService`：`activiti`的仓库服务类。所谓的仓库指流程定义文档的两个文件：bpmn文件和流程图片 
- `RuntimeService`：`activiti`的流程执行服务类。可以从这个服务类中获取很多关于流程执行相关的信息。 
- `TaskService`：`activiti`的任务服务类。可以从这个类中获取任务的信息。 
- `HistoryService`：`activiti`的查询历史信息的类。在一个流程执行完成后，这个对象为我们提供查询历史信息 



## 2、库表

### 2.1 ACT_RE_*

`'RE'`表示`repository`。 这些表包含了流程定义和流程静态资源 （图片，规则，等等）。

- `act_re_deployment `部署信息表 
- `act_re_model` 流程设计模型部署表 
- `act_re_procdef` 流程定义数据表 

### 2.2 ACT_RU_*:_

`_'RU'`表示runtime。 这些表，包含流程实例，任务，变量，异步任务，等运行中的数据。 `Activiti`只在流程实例执行过程中保存这些数据， 在流程结束时就会删除这些记录。 这样运行时表可以一直很小速度很快。 

- `act_ru_execution `运行时流程执行实例表 
- `_act_ru_identitylink `运行时流程人员表，主要存储任务节点与参与者的相关信息 
- `_act_ru_task` 运行时任务节点表 
- `act_ru_variable` 运行时流程变量数据表 
- `_act_ru_job` 工作数据表 
- `_act_ru_event_subscr `事件描述表 

### 2.3 ACT_ID_*:

`'ID'`表示`identity`。 这些表包含身份信息，比如用户，组等。 

- `act_id_group `用户组信息表 
- `act_id_info `用户扩展信息表 
- `act_id_membership` 用户与用户组对应信息表 
- `act_id_user` 用户信息表 

### 2.4 ACT_HI_*:

`'HI'`表示`history`。 这些表包含历史数据，比如历史流程实例， 变量，任务等等。 

- `act_hi_actinst `历史节点表 
- `act_hi_attachment` 历史附件表 
- `act_hi_comment `历史意见表 
- `act_hi_identitylink` 历史流程人员表 
- `act_hi_detail `历史详情表，提供历史变量的查询 
- `act_hi_procinst` 历史流程实例表 
- `act_hi_taskinst` 历史任务实例表 
- `act_hi_varinst `历史变量表 

### 2.5 ACT_GE_*:

通用数据， 用于不同场景下，如存放资源文件。 

- `act_ge_bytearray `二进制数据表 
- `act_ge_property` 属性数据表存储整个流程引擎级别的数据,初始化表结构时，会默认插入三条记录 





## 三、activiti使用

### 1、环境配置

#### 1.1 配置activity.cfg.xml

`activiti`底层使用了25张表来描述整个流程引擎。在`activity.cfg.xml`中配置数据源后，将`processEngineConfiguration`注册为bean，同时配置数据源，开启自动创建表的配置后，当我们创建`ProcessEngine`时，就会自动帮我们创建数据库表

```xml
<?xml version="1.0" encoding="UTF-8"?> 
<beans xmlns="http://www.springframework.org/schema/beans" 
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
       xmlns:context="http://www.springframework.org/schema/context" 
       xmlns:tx="http://www.springframework.org/schema/tx" 
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
                           http://www.springframework.org/schema/beans/spring-beans.xsd 				     http://www.springframework.org/schema/contex http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd"> 
    
    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"> 
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/> 
        <property name="url" value="jdbc:mysql://localhost:3306/activiti"/> 
        <property name="username" value="root"/> 
        <property name="password" value="123456"/> 
    </bean> 
    <bean id="processEngineConfiguration" class="org.activiti.engine.impl.cfg.StandaloneProcessEngineConfiguration"> 
        <property name="dataSource" ref="dataSource"></property> 
        <!--是否自动生成表结构--> 
        <property name="databaseSchemaUpdate" value="true"/> 
    </bean> 
</beans>
```



#### 1.2 创建ProcessEngine

创建`ProcessEngine`后，`activiti`会自动检测表结构，如果没有会帮我们创建，创建完之后会生成25张表

<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202409090024815.jpg" style="zoom:50%;" />

- `ACT_RE_*`: 'RE'表示 repository。这些表包含了流程定义和流程静态资源 （图片，规则，等等）。 _
- `ACT_RU_*`: 'RU'表示 runtime。这些表，包含流程实例，任务，变量，异步任务，等运行中的数据。Activiti 只在流程实例执行过程中保存这些数据， 在流程结束时就会删除这些记录。这样运行时表可以一直很小速度很快。 *
- `ACT_HI_*`: 'HI'表示 history。这些表包含历史数据，比如历史流程实例， 变量，任务等等。 
- `ACT_GE_*`: 'GE'表示 general。通用数据， 用于不同场景下 

```java
public class ActivitiInit { 
    /** 
    * 方式一 
    */ 
    @Test 
    public void GenActivitiTables() { 
        // 1.创建ProcessEngineConfiguration对象。第一个参数：配置文件名称；第二个参数：processEngineConfiguration的bean的id 
        ProcessEngineConfiguration processEngineConfiguration = ProcessEngineConfiguration.createProcessEngineConfigurationFromResource( "activiti.cfg.xml", "processEngineConfiguration" ); 
        // 2.创建ProcessEngine对象 
        ProcessEngine processEngine = processEngineConfiguration.buildProcessEngine(); 
        // 3.输出processEngine对象 
        System.out.println( processEngine ); 
    } 
    /** 
    * 方式二 
    */ 
    @Test 
    public void GenActivitiTables2() { 
        // 条件：1.activiti配置文件名称：activiti.cfg.xml 2.bean的id="processEngineConfiguration" 
        ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine(); 
        System.out.println( processEngine ); 
    } 
}
```



### 2、流程定义

流程定义是按照 bpmn2.0 标准去描述 业务流程，通过可视化的方式配置一个流程，最终会生成一个bpmn文件，该文件展示的是可视化的流程信息，但实际是一个xml文件，通过xml的形式描述了定义的流程，以及如何可视化展示的。


<img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202409090025545.jpg" style="zoom:30%;" /><img src="https://cdn.jsdelivr.net/gh/xrj123123/Images/202409090025984.jpg" style="zoom:30%;" />

左侧是bpmn文件展示的可视化样式，右侧是bpmn文件内容。bpmn文件就是通过一个xml文件定义了各种节点，以及节点之间的联系



### 3、流程部署

将定义的流程，即`bpmn`文件部署到 `activiti` 数据库中，通过调用` activiti `的 api 将流程定义的 `bpmn` 和 `png` 两个文件添加部署到` activiti `中，即将这些数据插入到`activiti`的数据库中

- `act_re_deployment`：部署信息表，存放流程定义的名称和部署时间，每部署一次增加一条记录 
- `act_re_procdef`：流程定义的信息，存放流程定义的属性信息，部署每个新的流程定义都会在这张表中增加一条记录。当流程定义的key相同的情况下，使用的是版本升级 
- `act_ge_bytearray`：存储流程定义相关的部署信息。即流程定义文档的存放位置。每部署一次就会增加两条记录，一条是关于`bpmn`规则文件的，一条是图片的 

```JAVA
public class ActivitiDeployment { 
    @Test 
    public void activitiDeploymentTest() { 
        ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine(); 
        RepositoryService repositoryService = processEngine.getRepositoryService(); 
        
        Deployment deployment = repositoryService.createDeployment() 
                                        .addClasspathResource( "diagram/holiday.bpmn" ) 
                                        .addClasspathResource( "diagram/holiday.png" ) 
                                        .name"请假申请单流程" ) 
                                        .deploy(); 
        System.out.println( deployment.getName() ); 
        System.out.println( deployment.getId() ); 
    } 
}
```



### 4、启动流程

启动流程就是创建一个流程实例，即`processInstance`。`processInstance`中包含了各种task节点。涉及的库表如下

- `act_hi_actinst`：已完成的活动信息 
- `act_hi_identitylink`：参与者信息 
- `act_hi_procinst`：流程实例 
- `act_hi_taskinst`：任务实例 
- `act_ru_execution`：执行表 
- `act_ru_identitylink`：参与者信息 
- `act_ru_task`：任务 

```JAVA
public class ActivitiStartInstance { 
    public static void main(String[] args) { 
        ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine(); 
        RuntimeService runtimeService = processEngine.getRuntimeService(); 
        
        // 创建流程实例，需要传入对应流程的DefinitionKey 
        ProcessInstance processInstance = runtimeService.startProcessInstanceByKey( "holiday" ); 
        System.out.println( "流程部署ID" + processInstance.getDeploymentId() ); 
        System.out.println( "流程定义ID" + processInstance.getProcessDefinitionId() ); 
        System.out.println( "流程实例ID" + processInstance.getId() ); 
        System.out.println( "活动ID" + processInstance.getActivityId() ); 
    } 
}
```



### 5、流程操作

#### 5.1 流程定义查询

通过`DefinitionKey`查询流程信息

```JAVA
public class QueryProceccDefinition { 
    @Test 
    public void queryProceccDefinition() { 
        // 流程定义key 
        String processDefinitionKey = "holiday"; 
        ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine(); 
        RepositoryService repositoryService = processEngine.getRepositoryService(); 
        
        ProcessDefinitionQuery processDefinitionQuery = repositoryService.createProcessDefinitionQuery(); 
        // 查询结果 
        List<ProcessDefinition> list = processDefinitionQuery 
                                            .processDefinitionKey( processDefinitionKey ) 
                                            .orderByProcessDefinitionVersion().desc().list(); 
        
        for (ProcessDefinition processDefinition : list) { 
            System.out.println( "------------------------" ); 
            System.out.println( " 流 程 部 署 id ： " + processDefinition.getDeploymentId() ); 
            System.out.println( "流程定义id： " + processDefinition.getId() ); 
            System.out.println( "流程定义名称： " + processDefinition.getName() ); 
            System.out.println( "流程定义key： " + processDefinition.getKey() ); 
            System.out.println( "流程定义版本： " + processDefinition.getVersion() ); 
        } 
    } 
}
```



#### 5.2 流程删除

```JAVA
/** 
* 删除指定流程id的流程 
*/ 
public void deleteDeployment() { 
    // 流程部署id 
    String deploymentId = "8801"; 
    ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine(); 
    RepositoryService repositoryService = processEngine.getRepositoryService(); 
    
    //删除流程， 如果该流程已有流程实例启动则删除时出错 
    repositoryService.deleteDeployment(deploymentId ); 
    //设置true 级联删除流程定义，即使该流程有流程实例启动也可以删除 
    repositoryService.deleteDeployment(deploymentId, true); 
}
```



#### 5.3 任务查询

查询流程实例中对应的任务节点信息

```JAVA
List<Task> taskList = processEngine.getTaskService() 
    .createTaskQuery() //创建任务查询对象 
    .taskAssignee("小明") // 指定办理人 
    // .taskCandidateUser(candidateUser) //组任务的办理人查询 
    // .processDefinitionId(processDefinitionId) //使用流程定义ID查询 
    // .processInstanceId(processInstanceId) //使用流程实例ID查询 
    .orderByTaskCreateTime().asc() //使用创建时间的升序排列 
    // .singleResult() //返回惟一结果集 
    // .count() //返回结果集的数量 
    // .listPage(firstResult, maxResults); //分页查询 
    .list(); //返回列表 
for (Task task:taskList){ 
    System.out.println("任务ID:"+task.getId()); 
    System.out.println("任务名称:"+task.getName()); 
    System.out.println("任务的创建时间:"+task.getCreateTime()); 
    System.out.println("任务的办理人:"+task.getAssignee()); 
    System.out.println("流程实例ID："+task.getProcessInstanceId()); 
    System.out.println("执行对象ID:"+task.getExecutionId()); 
    System.out.println("流程定义ID:"+task.getProcessDefinitionId()); 
}
```



#### 5.4 完成任务

通过taskId指定任务，任务完成后，流程引擎会自动流程至下一个节点

```JAVA
public void completeMyPersonalTask(){
  	processEngine.getTaskService().  //正在执行任务相关的Service
    	complete("104");  //根据taskid完成任务
}
```

