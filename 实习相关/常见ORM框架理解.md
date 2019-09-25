# 常见ORM框架理解

JAVA编程免不了和数据库打交道，那么如何高效便捷地操作数据库，也是一个需要而对的问题，原生的基于JDBC的方式当然是非常低效，而且要写一大堆无用的模板代码，不值得选取。好在我们不需要重复的造轮子，目前已经有很多优秀的ORM框架可供使用了，常见的比如Hibernate, Mybatis,TopLink等，JAVA EE3.0规范中提出的JPA，同样也是十分的好用，所以有时面试官也会问一下些和这个相关的面试题，下面整理一些个人遇到，或者网上经常看到的一些面试题 。

## 一、什么是JPA，为什么要有JPA？

​	JPA的全称是Java Persistence API， 即JAVA 持久化API，是EJB 3.0 的专家推出，作为 JSR-220的一部分，简单点来说可以理解为是一个JAVA的标准规范，这个规范为对JAVA对象的持久化制定了一些标准的接口，也可以说，JPA是一个标准的ORM（对象关系映射）规范。 

​	那么为什么要有JPA呢?时下ORM框架很多，不同的ORM框架相互之间并不兼容。SUN也意识到了，对于JAVA对象的持久化是一个重要的内容，提出这个规范，一方面是为了简化EJB中对于对象持久化的操作，另一方面，也希望通过制定统一规范，达到一统ORM标准的目的。 

​	JPA虽然是为EJB服务，但并不对JAVA对象有特别的要求，它可以是一个普通的POJO，仅此而已。该规范主要包括三部分内容，即ORM映射元数据，标准接口API，以及查询语言。 

​	要注意的是JPA只是一个接口规范，而不是实现，具体实现由各供应商来完成，目前的话，Hibernate，TopLink,OpenJPA都很好地实现了JPA接口。 

​	要基于JPA操作数据库，需要有一个persistence.xml配置文件，具体的几个关键类是EntityManagerFactory, EntityManager, 通过EntityManager的实例，我们可以对JAVA对象进行操作，如persist, merge, remove, createQuery等。



## 二、Hibernate

​	Hibernate可能是最常用的ORM框架了，整体表现上来看还是比较优秀。不得不提一下他的创始人：Gavin King，据说他是因为无法所爱EJB1.1的臃肿的模式代码而要寻求改变，而在写Hibernate之前，做的第一件事，居然是先去买了一本SQL基础的书。这也说明，要成功，一是要想法，二是，马上做手去做。

​	要进行Hibernate开发，我觉得有几个点是需要了解的，一是配置文件，默认的hibernate.cfg.xml，和对象对应的配置文件,如User.hbm.xml。另外和JPA类似，Hibernate中也有几个重要的对象，如Configuration, SessionFactory, Session, Transaction， Query, Criteria. 只要知道它们如何使用，建立一个DEMO工程便是一件很简单的事了。下面说一个几个常见的问题：

**2.1 创建SESSION有哪两种方式，它们有什么区别？**

​	Hibernate里的Session对象主要负责对对象进行CRUD操作，创建Session有两种方式，第一种是openSession，另外一种是getCurrentSession. 

​	第一种方式下，总是会新建一个会话，当会话结束后，需要手动进行关闭以释放资源，而getCurrentSession则会和当前线程关联，如果当前线程没有，则新建一个，如果有，则取当前的，这个的特点是不需要手动关闭，当事务提交后，就自动关闭了，而且再调用其关闭接口反而会报错。不过这种方式需要我们在配置文件中设置下面这一项

```xml
<property name="current_session_context_class">thread</property>
```

​	其底层应该是基于ThreadLocal实现的。通常情况下，我们使用这一种即可。

**2.2 LOAD和GET有什么区别？**

load和get都是从数据库中查询指定主键的记录。参考网上的资料，整理如下： 
相同点：都会先查找session缓存，若缓存中有，则直接返回。 
不同点：load认为数据库中一定是有这条记录的，所以若不存在，则先会创建一个代理对象，当真正使用这个对象的数据时，才会再去查询二级缓存和数据库，这就是所谓的延迟加载。若查不到，则返回ObjectNotFoundException。而对于get来说，则会直接依次查找二级缓存和数据库，查不到的话，则直接返回null.

**2.3 Hibernate中的主键类型主要有哪几种？**

对于自增型的数据库，使用identity，如MYSQL，对于序列化的主键，使用Sequence，如Oracle。如果不清楚，可以用native.

**2.4 Hibernate中的缓存机制？**

主要分为两类缓存，即所谓的一级缓存和二级缓存。一级缓存是session级别的，也是强制使用的，我们无需过多的关心也无法干预其使用。当我们用Session对Java对象进行crud操作时会用到，比如添加，是先将对象放到 一级缓存中，这个时候信息并未入库，而是在缓存中，当调用了commit之后再入库的。 

二级缓存位于sessionFactory的级别，也就是说，同一sessionFactory下，二级缓存中的对象，是可以被所有的session共享的，但这个缓存基于外部缓存组件，需要手动配置和开启。常见的有EHCache。

由于被session共享，所以就存在并发的问题，所以，二级缓存，只适合存储那些不经常修改，非重要数据，偶尔或几乎不会产生并发的数据。

查询的时候，会先查缓存，再查数据库，更新的时候，也会先更新缓存，再更新数据库。

Hibernate中还有一类缓存叫做查询缓存，只有查询的时候才起作用，这一类一般用途不是太大。



## 三、 Mybatis

​	mybatis的前身是ibatis，源于 Apache的一个开源项目。同样的，这是一个ORM框架，不过这个框架最大的特点有三点，一是面向接口编程，二是可以在配置文件中定义SQL语句，三是支持动态SQL,这是其独特的一面。 

**3.1 Mybatis中#和$的区别？**

​	这两个都可以作为占位符，不过#{}里的占位符，mybatis会加以处理，类似于普通SQL中的？，这样的话可以避免SQL注入的问题。 而 $ 则是原样输出，无法避免SQL注入的问题，通常情况下，我们优先使用#，但如果有的时候，我们就是要传一个字段名进去，那么使用$，比如 order by \${id}

**3.2 Mybatis和Hibernate比较？** 

相同点就不说了，说一下主要区别： 

1. mybatis手写SQL，而hibernate提供映射机制，开发人员无需关心 

2. mybatis控制更细粒度，但可移植性差，hibernate开发DAO很简单，可移植性好 

3. mybatis容易上手学习，hibernate难度稍大 
4. hibernate提供了面向对象的SQL语句HQL，也能使用普通的SQL，但mybatis只能使用普通的SQL。

