# Spring当中JDBC的事务管理

在Spring当中关于事务管理的实现类有三种

1. `DataSourceTransatcionManager`

    仅处理一个数据源且使用JDBC存取时使用

2. `JtaTransactionManager`

    Java EE应用服务器上使用

3. `HibernateTransactionManager`

    使用Hibernate框架时使用



在这里使用的是**DataSourceTransactionManager**

## 注解方式



### xml文件配置

#### 配置事务管理器Bean到容器

```xml
<bean name="transactionManager" class="org.springframework.jdbc.datasource.dataSourceTransactionManager">
    <!-- 事务管理器需要数据源bean -->
    <property name="dataSource" ref="dataSource"></property>
</bean>
```



#### 启用事务注解



启用事务主键需要先添加**tx命名空间**

```xml
<!-- transaction-manager属性为上文装配的事务管理器Bean -->
<tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
```

然后在需要开启事务的方法上标注开启事务的注解`@Tranagctional`





#### 事务的传播属性



事务的传播属性描述的是在一个事务方法当中调用其他的事务方法时事务该如何传播



仅介绍常用的两种

* REQUIRED

    下一层的事务方法和上一层事务方法公用一个事务，即下层事务方法有一需要回滚则回滚包括上一层事务方法在内的所有事务

* REQUIRES_NEW

    在事务方法内调用的事务方法将创建一个新的事务，即下层事务方法需要回滚不影响其他事务的完场



#### 给事务方法设置传播属性



设置`@Transactional`注解的**propagation**属性

```java
@Transactional(propagation=Propagation.REQUIRED)
public void func01(){...}
```





#### 设置事务的隔离级别



设置`@Transactional`注解的**isolation**属性



![](.\事务的四种隔离级别.png)



DEFAULT取值为READ_COMMITED







#### 指定捕捉到特定异常时不进行回滚



默认情况下事务对所有的运行时异常进行回滚



设置`@Transactional`注解的**noRollbackFor**属性或**noRollbackForClassName**属性

**noRollbackFor**属性接受的参数为**class**对象数组

**noRollbackForClassName**属性接受的参数为类型名称数组



#### 其他属性



### readOnly属性



如果事务只包含读取操作可以声明事务为只读事务，指定readOlny为true，有益数据库优化



### timeout属性



设置事务超时时间，防止事务长时间占用连接

接受参数为秒数