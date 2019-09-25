

# Activiti学习

## 了解工作流

1、工作流(Workflow)，就是“业务过程的部分或整体在计算机应用环境下的自动化”，它主要解决的是“使在多个参与者之间按照某种预定义的规则传递文档、信息或任务的过程自动进行，从而实现某个预期的业务目标，或者促使此目标的实现。

2、工作流管理系统(Workflow Management System, WfMS)是一个软件系统，它完成工作量的定义和管理，并按照在系统中预先定义好的工作流逻辑进行工作流实例的执行。工作流管理系统不是企业的业务系统，而是为企业的业务系统的运行提供了一个软件的支撑环境。

3、常见的工作流框架有Activity、JBPM、OSWorkFlow、WorkFlow。本系列使用activiti5.13版本。

4、工作流框架底层需要有数据库提供支持，activiti5.13版本，有23张表。JBPM4.4框架有18张表。JBPM底层使用hibernate操作数据库。Activiti框架底层使用的mybatis操作数据库。

## Activiti介绍

Activiti5是由Alfresco软件在2010年5月17日发布的业务流程管理（BPM）框架，它是覆盖了业务流程管理、工作流、服务协作等领域的一个开源的、灵活的、易扩展的可执行流程语言框架。Activiti基于Apache许可的开源BPM平台，创始人Tom Baeyens是JBoss jBPM的项目架构师，它特色是提供了eclipse插件，开发人员可以通过插件直接绘画出业务流程图。 

![img](D:\java\md_pictures\activiti\bpmn.png)

## Activiti入门

首先我们来梳理一下Activiti的开发步骤：

1. 我们要用到一个工作流，首先就要把这个工作流定义出来【也就是工作流的步骤的怎么样的】，Activiti支持以“图”的方式来定义工作流
2. 定义完工作流，就要部署到起来【我们可以联想到Tomcat，我们光下载了Tomcat是没有用的，要把它部署起来】
3. 随后我们就执行该工作流，该工作流就随着我们定义的步骤来一一执行！

### BPMN

业务流程建模与标注（Business Process Model and Notation，BPMN) ，描述流程的基本符号，包括这些图元如何组合成一个业务流程图（Business Process Diagram）

BPMN这个就是我们所谓**把工作流定义出来的流程图**..

### 数据库相关

我们在**执行工作流步骤的时候会涉及到很多数据**【执行该流程的人是谁、所需要的参数是什么、包括想查看之前流程执行的记录等等】，因此我们会需要用到数据库的表来保存数据...

由于我们使用的是Activiti框架，这个框架会**自动帮我们把对应的数据库表创建起来**，它涉及的表有23个，但是常用的并不是很多，因此也不用很慌...

下面就列举一下表的情况

```

Activiti的后台是有数据库的支持，所有的表都以ACT_开头。 第二部分是表示表的用途的两个字母标识。 用途也和服务的API对应。
ACT_RE_*: 'RE'表示repository。 这个前缀的表包含了流程定义和流程静态资源 （图片，规则，等等）。
ACT_RU_*: 'RU'表示runtime。 这些运行时的表，包含流程实例，任务，变量，异步任务，等运行中的数据。 Activiti只在流程实例执行过程中保存这些数据， 在流程结束时就会删除这些记录。 这样运行时表可以一直很小速度很快。
ACT_ID_*: 'ID'表示identity。 这些表包含身份信息，比如用户，组等等。
ACT_HI_*: 'HI'表示history。 这些表包含历史数据，比如历史流程实例， 变量，任务等等。
ACT_GE_*: 通用数据， 用于不同场景下，如存放资源文件。
```

### 搭建配置环境

可参考：<https://segmentfault.com/a/1190000013839729#articleHeader1>

<http://blog.sina.com.cn/s/blog_4b3196670102woix.html>

中文乱码问题：<http://www.cnblogs.com/mymelody/p/6049291.html>

## 开发步骤

想使用Activiti就需要有数据库的支持，虽然Activiti是自动帮我们创建对应的数据库表，但是我们是需要配置数据库的信息的。我们配置数据库的信息，接着拿到Activiti最重要的API------Activiti引擎

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\Activiti引擎.png)

### 得到工作流引擎

Activiti提供**使用代码的方式来配置数据库的信息**

``` java


    @Test
    public void createActivitiEngine(){

/*        *1.通过代码形式创建
         *  - 取得ProcessEngineConfiguration对象
         *  - 设置数据库连接属性
         *  - 设置创建表的策略 （当没有表时，自动创建表）
         *  - 通过ProcessEngineConfiguration对象创建 ProcessEngine 对象*/

         //取得ProcessEngineConfiguration对象
         ProcessEngineConfiguration engineConfiguration=ProcessEngineConfiguration.
         createStandaloneProcessEngineConfiguration();
         //设置数据库连接属性
         engineConfiguration.setJdbcDriver("com.mysql.jdbc.Driver");
         engineConfiguration.setJdbcUrl("jdbc:mysql://localhost:3306/activitiDB?createDatabaseIfNotExist=true"
         + "&useUnicode=true&characterEncoding=utf8");
         engineConfiguration.setJdbcUsername("root");
         engineConfiguration.setJdbcPassword("root");


         // 设置创建表的策略 （当没有表时，自动创建表）
         //          public static final java.lang.String DB_SCHEMA_UPDATE_FALSE = "false";//不会自动创建表，没有表，则抛异常
         //          public static final java.lang.String DB_SCHEMA_UPDATE_CREATE_DROP = "create-drop";//先删除，再创建表
         //          public static final java.lang.String DB_SCHEMA_UPDATE_TRUE = "true";//假如没有表，则自动创建
         engineConfiguration.setDatabaseSchemaUpdate("true");
         //通过ProcessEngineConfiguration对象创建 ProcessEngine 对象
        ProcessEngine processEngine = engineConfiguration.buildProcessEngine();
         System.out.println("流程引擎创建成功!");
        
    }
```

Activiti也可以通过配置文件来配置数据库的信息，加载配置文件从而得到工作流引擎

``` java

        /**2. 通过加载 activiti.cfg.xml 获取 流程引擎 和自动创建数据库及表
         * 
         
        ProcessEngineConfiguration engineConfiguration=
                ProcessEngineConfiguration.createProcessEngineConfigurationFromResource("activiti.cfg.xml");
            //从类加载路径中查找资源  activiti.cfg.xm文件名可以自定义
        ProcessEngine processEngine = engineConfiguration.buildProcessEngine();
        System.out.println("使用配置文件Activiti.cfg.xml获取流程引擎");
        */
```

**activiti.cfg.xml**

``` xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-2.5.xsd
http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd">
 <!-- 配置 ProcessEngineConfiguration  -->
 <bean id="processEngineConfiguration" class="org.activiti.engine.impl.cfg.StandaloneProcessEngineConfiguration">
   <!-- 配置数据库连接 -->
 <property name="jdbcDriver" value="com.mysql.jdbc.Driver"></property>
 <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/activitiDB?createDatabaseIfNotExist=true&amp;useUnicode=true&amp;characterEncoding=utf8"></property>
 <property name="jdbcUsername" value="root"></property>
 <property name="jdbcPassword" value="root"></property>
 
  <!-- 配置创建表策略 :没有表时，自动创建 -->
  <property name="databaseSchemaUpdate" value="true"></property>
 
 </bean>

</beans>
```

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\Activiti配置文件.png)

上面的那种加载配置文件方式，配置文件的名字是可以自定义的，如果我们配置文件的名字默认就是`activiti.cfg.xml`的话，也是放在类路径下，我们就可以使用默认的方式来进行加载了！

``` java


    @Test
    public void createActivitiEngine(){
        /**
         * 3. 通过ProcessEngines 来获取默认的流程引擎
         */
        //  默认会加载类路径下的 activiti.cfg.xml
        ProcessEngine processEngine = ProcessEngines.getDefaultProcessEngine();
        System.out.println("通过ProcessEngines 来获取流程引擎");
    }

```

### 定义工作流

定义工作流就需要我们刚才下载的插件了，我们是使用图形的方式来定义工作流的....

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\定义工作流.png)

**在每个流程中，我们都可以指定对应的处理人是谁，交由谁处理**

### 部署工作流

我们上面已经定义了工作流了，工作流引擎我们也已经拿到了，接下来就是把工作流部署到工作流引擎中了

``` java
    @Test
    public void deploy() {

        //获取仓库服务 ：管理流程定义
        RepositoryService repositoryService = processEngine.getRepositoryService();
        Deployment deploy = repositoryService.createDeployment()//创建一个部署的构建器
                .addClasspathResource("LeaveActiviti.bpmn")//从类路径中添加资源,一次只能添加一个资源
                .name("请求单流程")//设置部署的名称
                .category("办公类别")//设置部署的类别
                .deploy();

        System.out.println("部署的id"+deploy.getId());
        System.out.println("部署的名称"+deploy.getName());
    }

```

相对应的数据库表就会插入数据、涉及到的数据库表后面会详细说明。现在我们只要了解到，**我们工作流引擎执行操作会有数据库表记录**

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\Activiti数据表记录.png)

### 执行工作流

指定工作流就是我们定义时**工作流程表的id**

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\执行工作流.png)

``` java


    @Test
    public void startProcess(){

        //指定执行我们刚才部署的工作流程
        String processDefiKey="leaveBill";
        //取运行时服务
        RuntimeService runtimeService = processEngine.getRuntimeService();
        //取得流程实例
        ProcessInstance pi = runtimeService.startProcessInstanceByKey(processDefiKey);//通过流程定义的key 来执行流程
        System.out.println("流程实例id:"+pi.getId());//流程实例id
        System.out.println("流程定义id:"+pi.getProcessDefinitionId());//输出程定义的id
    }
```

### 根据代理人查询当前任务的信息

在开始工作流之后，随后工作流就回到去申请请假的流程，申请请假的处理人是钟福成，可以查询出对应的信息：

``` java
    //查询任务
    @Test
    public void queryTask(){
        //任务的办理人
        String assignee="钟福成";
        //取得任务服务
        TaskService taskService = processEngine.getTaskService();
        //创建一个任务查询对象
        TaskQuery taskQuery = taskService.createTaskQuery();
        //办理人的任务列表
        List<Task> list = taskQuery.taskAssignee(assignee)//指定办理人
                .list();
        //遍历任务列表
        if(list!=null&&list.size()>0){
            for(Task task:list){
                System.out.println("任务的办理人："+task.getAssignee());
                System.out.println("任务的id："+task.getId());
                System.out.println("任务的名称："+task.getName());
            }
        }
    }

```

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\查询当前任务的信息,png)

### 处理任务

我们现在处理流程去到“申请请假”中，处理人是钟福成...**接着就是钟福成去处理任务，根据任务的id使得流程继续往下走**

任务的id刚才我们已经查询出来了【上面】，我们如果是在web端操作数据的话，那么只要传递过去就行了！

``` java
    //完成任务
    @Test
    public void compileTask(){
        String taskId="304";
        //taskId：任务id
        processEngine.getTaskService().complete(taskId);
        System.out.println("当前任务执行完毕");
    }
```

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\处理任务1.png)

当我们处理完该任务的时候，就到了批准【班主任】任务了，我们查询一下是不是如我们想象的效果：

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\处理任务2.png)

我们按照定义的工作流程图一步一步往下走，最终把流程走完

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\工作流程.png)

## 流程定义细讲

管理流程定义主要涉及到以下的4张表：

``` sql
-- 流程部署相关的表
SELECT * FROM act_ge_bytearray # 通用字节资源表

SELECT * FROM act_ge_property # 通用属性表，可以生成部署id

SELECT * FROM act_re_deployment  #部署表

SELECT * FROM act_re_procdef    # 流程定义表
```

### PNG资源

在Eclipse中Activiti插件会自动生成一个BPMN与之对应的PNG图片，是需要通过加载PNG图片的

``` java
    @Test
    public void deploy() {

        //获取仓库服务 ：管理流程定义
        RepositoryService repositoryService = processEngine.getRepositoryService();
        Deployment deploy = repositoryService.createDeployment()//创建一个部署的构建器
                .addClasspathResource("LeaveActiviti.bpmn")//从类路径中添加资源,一次只能添加一个资源
                .name("请求单流程")//设置部署的名称
                .category("办公类别")//设置部署的类别
                .deploy();

        System.out.println("部署的id"+deploy.getId());
        System.out.println("部署的名称"+deploy.getName());
    }
```

而我们的Intellij idea插件不会自动生成PNG图片，但是我们在加载BPMN文件的时候，好像会自动插入PNG图片，数据库是有对应的记录的【我们是没有手动加载PNG图片资源的】

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\加载png图片.png)

我们查看一下

``` java

    //查看bpmn 资源图片
    @Test
    public void viewImage() throws Exception{
        String deploymentId="201";
        String imageName=null;
        //取得某个部署的资源的名称  deploymentId
        List<String> resourceNames = processEngine.getRepositoryService().getDeploymentResourceNames(deploymentId);
        // buybill.bpmn  buybill.png
        if(resourceNames!=null&&resourceNames.size()>0){
            for(String temp :resourceNames){
                if(temp.indexOf(".png")>0){
                    imageName=temp;
                }
            }
        }

        /**
         * 读取资源
         * deploymentId:部署的id
         * resourceName：资源的文件名
         */
        InputStream resourceAsStream = processEngine.getRepositoryService()
                .getResourceAsStream(deploymentId, imageName);

        //把文件输入流写入到文件中
        File file=new File("d:/"+imageName);
        FileUtils.copyInputStreamToFile(resourceAsStream, file);
    }
```

可惜的是，**查看出来的图片的中文数据会乱码**...

这一部分存在一定争议，可以查看下面连接的具体部属方法

<https://blog.csdn.net/cs_hnu_scw/article/details/79059965>

### 查看流程定义

我们当时候查询流程定义是通过我们设置流程图的id来查看的....其实我们可以通过其他的属性来查询...并且可以查询出更加详细的数据

``` java

    //查看流程定义
    @Test
    public void queryProcessDefination(){
        String processDefiKey="buyBill";//流程定义key
        //获取流程定义列表
        List<ProcessDefinition> list = processEngine.getRepositoryService().createProcessDefinitionQuery()
        //查询 ，好比where
//        .processDefinitionId(proDefiId) //流程定义id
         // 流程定义id  ： buyBill:2:704   组成 ： proDefikey（流程定义key）+version(版本)+自动生成id
        .processDefinitionKey(processDefiKey)//流程定义key 由bpmn 的 process 的  id属性决定
//        .processDefinitionName(name)//流程定义名称  由bpmn 的 process 的  name属性决定
//        .processDefinitionVersion(version)//流程定义的版本
        .latestVersion()//最新版本
        
        //排序
        .orderByProcessDefinitionVersion().desc()//按版本的降序排序
        
        //结果
//        .count()//统计结果
//        .listPage(arg0, arg1)//分页查询
        .list();
        
        
        //遍历结果
        if(list!=null&&list.size()>0){
            for(ProcessDefinition temp:list){
                System.out.print("流程定义的id: "+temp.getId());
                System.out.print("流程定义的key: "+temp.getKey());
                System.out.print("流程定义的版本: "+temp.getVersion());
                System.out.print("流程定义部署的id: "+temp.getDeploymentId());
                System.out.println("流程定义的名称: "+temp.getName());
            }
        }
    }
```

### 来自ZIP的职员

我们还可以加载的是ZIP类型的资源数据

``` java

    //部署流程定义,资源来自zip格式
    @Test
    public void deployProcessDefiByZip(){
        InputStream in=getClass().getClassLoader().getResourceAsStream("BuyBill.zip");
        Deployment deploy = processEngine.getRepositoryService()
                .createDeployment()
                .name("采购流程")
                .addZipInputStream(new ZipInputStream(in))
                .deploy();
        
        System.out.println("部署名称:"+deploy.getName());
        System.out.println("部署id:"+deploy.getId());
    }
```

### 删除流程定义

``` java
    //删除流程定义
    @Test
    public void deleteProcessDefi(){
        //通过部署id来删除流程定义
        String deploymentId="101";
        processEngine.getRepositoryService().deleteDeployment(deploymentId);
    }
```

再次查询的时候，已经没有101这个流程定义的数据了。

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\删除流程.png)

## 流程实例与任务执行细讲

流程实例与任务执行的常用表有以下几个：

``` java
-- 流程实例与任务

SELECT * FROM act_ru_execution  # 流程执行对象信息
SELECT * FROM act_ru_task   # 正在运行的任务表

SELECT * FROM act_hi_procinst # 历史流程实例表
SELECT * FROM act_hi_taskinst  # 历史流程任务表
```

这里就简单简述一下流程实例与流程对象的区别：

- **如果是单例流程，执行对象ID就是流程实例ID**
- **如果一个流程有分支和聚合，那么执行对象ID和流程实例ID就不相同**
- **一个流程中，流程实例只有1个，执行对象可以存在多个。**

### 开始流程

``` java

    @Test
    public void startProcess(){
        String processDefiKey="leaveActiviti";//bpmn 的 process id属性
        ProcessInstance pi = processEngine.getRuntimeService()
                .startProcessInstanceByKey(processDefiKey);

        System.out.println("流程执行对象的id："+pi.getId());//Execution 对象
        System.out.println("流程实例的id："+pi.getProcessInstanceId());//ProcessInstance 对象
        System.out.println("流程定义的id："+pi.getProcessDefinitionId());//默认执行的是最新版本的流程定义
    }
```

### 查看正在运行的任务

``` java

//查询正在运行任务 
    @Test
    public void queryTask(){
        //取得任务服务
        TaskService taskService = processEngine.getTaskService();
        //创建一个任务查询对象
        TaskQuery taskQuery = taskService.createTaskQuery();
        //办理人的任务列表
        List<Task> list = taskQuery.list();
        //遍历任务列表
        if(list!=null&&list.size()>0){
            for(Task task:list){
                System.out.println("任务的办理人："+task.getAssignee());
                System.out.println("任务的id："+task.getId());
                System.out.println("任务的名称："+task.getName());
                
            }
        }
        
    }
```

**查询SELECT \* FROM act_ru_task 表**

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\查询正在运行的任务.png)

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\查询正在运行的任务2.png)

### 获取流程实例的状态

有的时候，我们需要判断它是在该流程，还是该流程已经结束了。我们可以根据获取出来的对象是否为空来进行判断

``` java

    //获取流程实例的状态
    @Test
    public void getProcessInstanceState(){
        String processInstanceId="605";
        ProcessInstance pi = processEngine.getRuntimeService()
                .createProcessInstanceQuery()
                .processInstanceId(processInstanceId)
                .singleResult();//返回的数据要么是单行，要么是空 ，其他情况报错
        //判断流程实例的状态
        if(pi!=null){
            System.out.println("该流程实例"+processInstanceId+"正在运行...  "+"当前活动的任务:"+pi.getActivityId());
        }else{
            System.out.println("当前的流程实例"+processInstanceId+" 已经结束！");
        }

    }

```

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\获取流程实例的状态.png)

### 查看历史流程实例的信息

``` java
    //查看历史执行流程实例信息
    @Test
    public void queryHistoryProcInst(){
        List<HistoricProcessInstance> list = processEngine.getHistoryService()
        .createHistoricProcessInstanceQuery()
        .list();
        if(list!=null&&list.size()>0){
            for(HistoricProcessInstance temp:list){
                System.out.println("历史流程实例id:"+temp.getId());
                System.out.println("历史流程定义的id:"+temp.getProcessDefinitionId());
                System.out.println("历史流程实例开始时间--结束时间:"+temp.getStartTime()+"-->"+temp.getEndTime());
            }
        }
    }
```

查询表：

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\查询历史信息1.png)

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\查询历史信息2.png)

### 查看历史实例执行任务信息

``` java
    @Test
    public void queryHistoryTask(){
        String processInstanceId="605";
        List<HistoricTaskInstance> list = processEngine.getHistoryService()
                .createHistoricTaskInstanceQuery()
                .processInstanceId(processInstanceId)
                .list();
        if(list!=null&&list.size()>0){
            for(HistoricTaskInstance temp:list){
                System.out.print("历史流程实例任务id:"+temp.getId());
                System.out.print("历史流程定义的id:"+temp.getProcessDefinitionId());
                System.out.print("历史流程实例任务名称:"+temp.getName());
                System.out.println("历史流程实例任务处理人:"+temp.getAssignee());
            }
        }
    }
```

给予对应的实例id就可以查询出执行到哪个任务了...

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\查询历史实例id)

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\查询历史实例.png)

### 执行任务

根据任务的id，就可以把该任务执行了。

``` java
    @Test
    public void compileTask(){
        String taskId="608";
        //taskId：任务id
        processEngine.getTaskService().complete(taskId);
        System.out.println("当前任务执行完毕");
    }
```

## 流程变量细讲

流程变量涉及到的数据库表：

``` sql
act_ru_variable：正在执行的流程变量表
act_hi_varinst：流程变量历史表
```

流程变量在工作流中扮演着一个非常重要的角色。例如：请假流程中有请假天数、请假原因等一些参数都为流程变量的范围。**流程变量的作用域范围是只对应一个流程实例**。也就是说各个流程实例的流程变量是不相互影响的。流程实例结束完成以后流程变量还保存在数据库中（存放到流程变量的历史表中）。

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\流程遍历的作用域只对应一个流程实例.png)

### 设置流程变量

​	我们有两种服务可以设置流程变量，TaskService【任务服务】和RuntimeService【运行时服务】

场景：

- **在流程开始的时候设置流程变量**
- **在完成某个任务的时候设置流程变量**

- **使用TaskService设置服务**
- **使用RuntimeService设置服务**

作用：

- **传递业务参数**
- **动态指定代理人【我们快速入门的例子是固定在流程定义图上写上代理人的】**
- **指定连接【决定流程往哪边走】**

### 流程变量支持类型

**如果我们使用JavaBean来作为流程的变量，那么我们需要将JavaBean实现Serializable接口**。

**Javabean类型设置获取流程变量，除了需要这个javabean实现了Serializable接口外，还要求流程变量对象的属性不能发生变化，否则抛出异常。解决方案，固定序列化ID**

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\流程变量的支持类型.png)


### setVariable和setVariableLocal的区别

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\setVariable和setVariableLocal的区别.png)

### 举例

``` java

//模拟流程变量设置
    @Test
    public void  getAndSetProcessVariable(){
        //有两种服务可以设置流程变量
//        TaskService taskService = processEngine.getTaskService();
//        RuntimeService runtimeService = processEngine.getRuntimeService();
        
        /**1.通过 runtimeService 来设置流程变量
         * executionId: 执行对象
         * variableName：变量名
         * values：变量值
         */
//        runtimeService.setVariable(executionId, variableName, values);
//        runtimeService.setVariableLocal(executionId, variableName, values);
        //设置本执行对象的变量 ，该变量的作用域只在当前的execution对象
//        runtimeService.setVariables(executionId, variables); 
          //可以设置多个变量  放在 Map<key,value>  Map<String,Object>
        
        /**2. 通过TaskService来设置流程变量
         * taskId：任务id
         */
//        taskService.setVariable(taskId, variableName, values);
//        taskService.setVariableLocal(taskId, variableName, values);
////        设置本执行对象的变量 ，该变量的作用域只在当前的execution对象
//        taskService.setVariables(taskId, variables); //设置的是Map<key,values>
        
        /**3. 当流程开始执行的时候，设置变量参数
         * processDefiKey: 流程定义的key
         * variables： 设置多个变量  Map<key,values>
         */
//        processEngine.getRuntimeService()
//        .startProcessInstanceByKey(processDefiKey, variables)
        
        /**4. 当任务完成时候，可以设置流程变量
         * taskId:任务id
         * variables： 设置多个变量  Map<key,values>
         */
//        processEngine.getTaskService().complete(taskId, variables);
        
        
        /** 5. 通过RuntimeService取变量值
         * exxcutionId： 执行对象
         * 
         */
//        runtimeService.getVariable(executionId, variableName);//取变量
//        runtimeService.getVariableLocal(executionId, variableName);//取本执行对象的某个变量
//        runtimeService.getVariables(variablesName);//取当前执行对象的所有变量
        /** 6. 通过TaskService取变量值
         * TaskId： 执行对象
         * 
         */
//        taskService.getVariable(taskId, variableName);//取变量
//        taskService.getVariableLocal(taskId, variableName);//取本执行对象的某个变量
//        taskService.getVariables(taskId);//取当前执行对象的所有变量
    }
```

``` java

    //设置流程变量值
    @Test
    public void setVariable(){
        String taskId="1804";//任务id
        //采用TaskService来设置流程变量
        
        //1. 第一次设置流程变量
//        TaskService taskService = processEngine.getTaskService();
//        taskService.setVariable(taskId, "cost", 1000);//设置单一的变量，作用域在整个流程实例
//        taskService.setVariable(taskId, "申请时间", new Date());
//        taskService.setVariableLocal(taskId, "申请人", "何某某");//该变量只有在本任务中是有效的
        
        
        //2. 在不同的任务中设置变量
//        TaskService taskService = processEngine.getTaskService();
//        taskService.setVariable(taskId, "cost", 5000);//设置单一的变量，作用域在整个流程实例
//        taskService.setVariable(taskId, "申请时间", new Date());
//        taskService.setVariableLocal(taskId, "申请人", "李某某");//该变量只有在本任务中是有效的
        
        /**
         * 3. 变量支持的类型
         * - 简单的类型 ：String 、boolean、Integer、double、date 
         * - 自定义对象bean  
         */
        TaskService taskService = processEngine.getTaskService();
        //传递的一个自定义bean对象
        AppayBillBean appayBillBean=new AppayBillBean();
        appayBillBean.setId(1);
        appayBillBean.setCost(300);
        appayBillBean.setDate(new Date());
        appayBillBean.setAppayPerson("何某某");
        taskService.setVariable(taskId, "appayBillBean", appayBillBean);
        
        
        System.out.println("设置成功！");

    }
```

``` java
   
    //查询流程变量
    @Test
    public void getVariable(){
        String taskId="1804";//任务id
//        TaskService taskService = processEngine.getTaskService();
//        Integer cost=(Integer) taskService.getVariable(taskId, "cost");//取变量
//        Date date=(Date) taskService.getVariable(taskId, "申请时间");//取本任务中的变量
////        Date date=(Date) taskService.getVariableLocal(taskId, "申请时间");//取本任务中的变量
//        String appayPerson=(String) taskService.getVariableLocal(taskId, "申请人");//取本任务中的变量
////        String appayPerson=(String) taskService.getVariable(taskId, "申请人");//取本任务中的变量
//        
//        System.out.println("金额:"+cost);
//        System.out.println("申请时间:"+date);
//        System.out.println("申请人:"+appayPerson);
        
        
        //读取实现序列化的对象变量数据
        TaskService taskService = processEngine.getTaskService();
        AppayBillBean appayBillBean=(AppayBillBean) taskService.getVariable(taskId, "appayBillBean");
        System.out.println(appayBillBean.getCost());
        System.out.println(appayBillBean.getAppayPerson());
        
    }

```

``` java

public class AppayBillBean  implements Serializable{
    private Integer id;
    private Integer cost;//金额
    private String appayPerson;//申请人
    private Date date;//申请日期
    public Integer getId() {
        return id;
    }
    public void setId(Integer id) {
        this.id = id;
    }
    public Integer getCost() {
        return cost;
    }
    public void setCost(Integer cost) {
        this.cost = cost;
    }
    public String getAppayPerson() {
        return appayPerson;
    }
    public void setAppayPerson(String appayPerson) {
        this.appayPerson = appayPerson;
    }
    public Date getDate() {
        return date;
    }
    public void setDate(Date date) {
        this.date = date;
    }
    

}
```

## 连线

上面我们已将学过了流程变量了，可以在【任务服务、运行时服务、流程开始、完成某个任务时设置流程变量】，而我们的**连接就是流程变量的实际应用了**....

### 定义流程图

我们并不是所有的流程都是按一条的路径来走的，我们有的时候会根据条件来走不同的路。当然了，最终该流程是会一步步走完....

例子：

**重要的信息交由老板来处理，不重要的信息交由经理来处理**

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\连线设置变量.png)

**表达式的结果必须是布尔型**

- `#{variable=='value'}`
- ${variable==value}

### 测试

我在任务完成时设置流程变量为不重要，那么跳到下一个流程时就是经理来进行处理

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\连线设置.png)

当我设置为重要的时候，那么就是交由老板来处理

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\连线设置2.png)

## 排他网关

上面我们使用连线的时候用了两个条件 : 要么条件是“重要”，要么条件是“不重要”....如果有另一种情况呢？？？就是用户把条件输入错了，写成“不知道重不重要”，那么我们的流程怎么走？？？岂不是奔溃了？？？

因此，我们要有一条默认的路来走，就**是当该变量不符合任何的条件时，我们也有一条默认的路**

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\排他网关.png)

值得注意的是：**如果是在Eclipse中使用插件的BPMN流程图，如果使用了排他网关，那么在Idea下是解析不了的**...

解决：

**我们只要重新定义BPMN流程图的排他网关就行了，idea中的Activiti插件是不用制定默认流程的，只要我们不设置条件，那就是默认的连接线**

### 测试

``` java

public class ExclusiveGetWay {
    private ProcessEngine processEngine = ProcessEngines
            .getDefaultProcessEngine();

    // 部署流程定义，资源来在bpmn格式
    @Test
    public void deployProcessDefi() {
        Deployment deploy = processEngine.getRepositoryService()
                .createDeployment().name("排他网关流程")
                .addClasspathResource("ExclusiveGateway.bpmn")
                .deploy();

        System.out.println("部署名称:" + deploy.getName());
        System.out.println("部署id:" + deploy.getId());
    }

    // 执行流程，开始跑流程
    @Test
    public void startProcess() {
        String processDefiKey = "bankBill";// bpmn 的 process id属性
        ProcessInstance pi = processEngine.getRuntimeService()
                .startProcessInstanceByKey(processDefiKey);

        System.out.println("流程执行对象的id：" + pi.getId());// Execution 对象
        System.out.println("流程实例的id：" + pi.getProcessInstanceId());// ProcessInstance
                                                                    // 对象
        System.out.println("流程定义的id：" + pi.getProcessDefinitionId());// 默认执行的是最新版本的流程定义
    }

    // 查询正在运行任务
    @Test
    public void queryTask() {
        // 取得任务服务
        TaskService taskService = processEngine.getTaskService();
        // 创建一个任务查询对象
        TaskQuery taskQuery = taskService.createTaskQuery();
        // 办理人的任务列表
        List<Task> list = taskQuery.list();
        // 遍历任务列表
        if (list != null && list.size() > 0) {
            for (Task task : list) {
                System.out.println("任务的办理人：" + task.getAssignee());
                System.out.println("任务的id：" + task.getId());
                System.out.println("任务的名称：" + task.getName());
            }
        }

    }

    // 完成任务
    @Test
    public void compileTask() {
        String taskId = "2404";
        Map<String,Object> params=new HashMap<String, Object>();
        params.put("visitor", 6);
        // taskId：任务id
        processEngine.getTaskService().complete(taskId, params);
//        processEngine.getTaskService().complete(taskId);
        System.out.println("当前任务执行完毕");
    }
    
}
```

我们指定的值并不是VIP也不是后台，那么就会自动去普通窗口中处理

![è¿éåå¾çæè¿°](D:\java\md_pictures\activiti\排他网关测试.png)
  

## 总结

- 如果一个业务需要多方面角色进行处理的话，那么我们最好就是用工作流框架。**因为如果其中一个环节的需求发生了变化的话，我们要是没有用到工作流。那就需要修改很多的代码。十分麻烦。**
- Activiti工作流框架快速入门:
  - **定义工作流，使用插件来把我们的流程图画出来。这个流程图就是我们定义的工作流。**
  - **工作流引擎是工作流的核心，能够让我们定义出来的工作流部署起来。**
  - **由于我们使用工作流的时候是有很多数据产生的，因此Activiti是将数据保存到数据库表中的。这些数据库表由Actitviti创建，由Activiti维护。**
  - **部署完的工作流是需要手动去执行该工作流的。**
  - **根据由谁处理当前任务，我们就可以查询出具体的任务信息。**
  - **根据任务的id，我们就可以执行任务了。**
- 流程定义涉及到了四张数据库表
  - 我们可以通过API把我们的流程定义图读取出来
  - **可以根据查询最新版本的流程定义**
  - **删除流程定义**
  - 部署流程定义的时候也可以是ZIP文件
- 流程在运行中，涉及到两个对象，四张数据库表:
  - **流程实例**
  - **流程任务**
  - **流程实例可以有多个，流程对象只能有一个。**
    - 如果流程没有分支的话，那么流程实例就等于流程对象
  - 基于这么两个对象，我们就可以做很多事情了
    - **获取流程实例和任务的历史信息**
    - **判断流程实例是否为空来判断流程是否结束了**
    - **查看正在运行服务的详细信息**
    - **通过流程实例来开启流程**
- 流程变量:它涉及到了两张表。**流程变量实际上就是我们的条件。**
  - **流程变量的作用域只在流程实例中。**
  - **我们可以在流程开始的时候设置流程变量，在任务完成的时候设置流程变量。**
  - **运行时服务和流程任务都可以设置流程变量。**
- **通过连线我们可以在其中设置条件，根据不同的条件流程走不同的分支**
- **如果没有设置默认的条件，当条件不吻合的时候，那么流程就走不下去了，因此需要排他网关来设置一条默认的路径。**



如果一个业务需要多方面角色进行处理的话，那么我们最好就是用工作流框架。**因为如果其中一个环节的需求发生了变化的话，我们要是没有用到工作流。那就需要修改很多的代码。十分麻烦。**

Activiti工作流框架快速入门:

- **定义工作流，使用插件来把我们的流程图画出来。这个流程图就是我们定义的工作流。**
- **工作流引擎是工作流的核心，能够让我们定义出来的工作流部署起来。**
- **由于我们使用工作流的时候是有很多数据产生的，因此Activiti是将数据保存到数据库表中的。这些数据库表由Actitviti创建，由Activiti维护。**
- **部署完的工作流是需要手动去执行该工作流的。**
- **根据由谁处理当前任务，我们就可以查询出具体的任务信息。**
- **根据任务的id，我们就可以执行任务了。**

流程定义涉及到了四张数据库表

