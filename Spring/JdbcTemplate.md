# JdbcTemplate

[参考文章](https://my.oschina.net/u/218421/blog/38598)：https://my.oschina.net/u/218421/blog/38598

## 配置JdbcTemplatet

配置JdbcTemplate有很多种方法

* 使用JdbcTemplate空参构造器创建，`setDataSource()`方法注入DataSource
* 或者使用有参构造器传入DataSource
* Spring当中配置到IoC容器

### Spring当中配置jdbcTemplate

`db.properties`

```properties
jdbc.user=root
jdbc.password=hhh123
jdbc.driverClass=com.mysql.cj.jdbc.Driver
jdbc.jdbcUrl=jdbc:mysql://localhost:3306/spring_jdbc?useSSL=true&serverTimezone=GMT&useUnicode=true&characterEncoding=UTF8

jdbc.initialPoolSize=5
jdbc.maxPoolSize=20
```



`ApplicationContext.xml`

```xml
<!--引入db.properties-->
<context:property-placeholder location="classpath:db.properties"/>

<bean name="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	<property name="user" value="${jdbc.user}"></property>
	<property name="password" value="${jdbc.password}"></property>
	<property name="driverClass" value="${jdbc.driverClass}"></property>
	<property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
	<property name="initialPoolSize" value="${jdbc.initialPoolSize}"></property>
	<property name="maxPoolSize" value="${jdbc.maxPoolSize}"></property>
</bean>
```



配置jdbcTemple bean

`ApplicatonContext.xml`

```xml
<bean id="jdbcTemplate" 
		class="org.springframework.jdbc.core.JdbcTemplate">
		<property name="dataSource" ref="dataSource"></property>
</bean>
```



## 数据库操作

假设有数据库表

department:

| Field         | Type        | Null | Key  | Default | Extra          |
| ------------- | ----------- | ---- | ---- | ------- | -------------- |
| id            | int(11)     | NO   | PRI  | NULL    | auto_increment |
| name          | varchar(20) | YES  |      | NULL    |                |
| department_id | int(11)     | YES  | MUL  | NULL    |                |

employee:
| Field           | Type        | Null | Key  | Default | Extra          |
| --------------- | ----------- | ---- | ---- | ------- | -------------- |
| id              | int(11)     | NO   | PRI  | NULL    | auto_increment |
| department_name | varchar(20) | YES  |      | NULL    |                |



### 更新操作

更新操作统一使用`update(String sql,Object args...)`

相应的更新批处理使用`batcdUpdate(String sql, List<T> args)`



#### 删除数据

```java
@Test
public void func01() throws SQLException{
	String sql = "delete from department where department_name=?;";
	jdbcTemplate.update(sql,"销售部");
}
```

#### 插入数据

```java
@Test
public void fun01() throws SQLException{
    String sql = "insert into department(department_name) values(?)";
    jdbcTemplate.update(sql,"事业部");
}
```

#### 修改数据

```java
@Test
public void fun01() throws SQLException{
    String sql = "update department set department_name=? where department_name =?;
    jdbcTemplate.update(sql,"销售部","事业部");
}
```



#### 批处理

```java
public void fun01() throws SQLException{
		String sql = "insert into employee(name,department_id) values(?,?)";
	   List<Object[]> argList = new LinkedList<Object[]>();
	   argList.add(new Object[] {"Mari",1});
	   argList.add(new Object[] {"Lari",1});
	   argList.add(new Object[] {"Kari",1});
	   argList.add(new Object[] {"Rari",1});
	   argList.add(new Object[] {"Wari",1});
	   argList.add(new Object[] {"Dari",1});
	   jdbcTemplate.batchUpdate(sql, argList);
	}
```



### 查询操作

对于简单的查询操作，如提供sql语句和参数然后返回一个结果的查询，可以使用一下一组JdbcTemplate提供的模板方法


> public int queryForInt(String sql)
> 
> public int queryForInt(String sql, Object[] args)
> 
> public int queryForInt(String sql, Object[] args)
> 
> public long queryForLong(String sql)
> 
> public long queryForLong(String sql, Object[] args)
> 
> public long queryForLong(String sql, Object[] args, int[] argTypes)
> 
> public Object queryForObject(String sql, Class requiredType)
> 
> public Object queryForObject(String sql, Object[] args, int[] argTypes, Class requiredType)
> 
> public Object queryForObject(String sql, Object[] args, int[] argTypes, RowMapper rowMapper)
> 
> public Object queryForObject(String sql, Object[] args, Class requiredType)
> 
> public Object queryForObject(String sql, Object[] args, RowMapper rowMapper)
> 
> public Object queryForObject(String sql, RowMapper rowMapper)

对于其中**ForInt**,**ForLong**查询不作演示

#### 查询结果多行



##### 查询返回有映射关系的对象实体




使用`JdbcTemplate.queryForObject(String sql, RowMapper<Class<T>> rowMapper, Object... args)`

其中RowMapper为数据库表与bean的映射

即列名称与bean对象属性名称映射，可以给查询列指定别名进行映射

如

​	列-->employee：id,name,department_id

​	类-->Employee：id,username,departmentId

查询语句使用别名`select id,name username,derpartment_id departmentId from employee where=?`



创建**RowMapper**对象使用**BeanPropertyRowMapper**实现类

例`RowMapper<Employee> rowMapper = new BeanPropertyRowMapper(Employee.clas);`



使用**queryForObject**方法的查询结果为表对应的列，但是JdbcTemplate无法为级联属性创建对象

即无法直接创建外键关联列所在表的对象



用例

```java
@Test
public void fun01() throws SQLException{
	String sql = "select id,name username,department_id departmentId from employee where id=?";
	RowMapper<Employee> rowMapper = new BeanPropertyRowMapper(Employee.class);
	Employee e1 = jdbcTemplate.queryForObject(sql, rowMapper,1);
	System.out.println(e1);
}
```



如果查询到的结果是多行的，那么可以使用以下方法较为便捷的把多行结果的对象实体存储到**List**对象当中

使用``JdbcTemplate.query(String sql, RowMapper<Class<T>> rowMapper, Object... args)`



用例

```java
@Test
public void fun01() throws SQLException{
	String sql = "select id,name username,department_id departmentId from employee where department_id=?";
	RowMapper<Employee> rowMapper = new BeanPropertyRowMapper<Employee>(Employee.class);
	List<Employee> employees = jdbcTemplate.query(sql, rowMapper, 1);
	for(Employee e:employees) {
		System.out.println(e);
	}
}
```



##### 查询结果多行但列数不足以装配实体对象

有时候查询返回的结果为多行，但是列数不足以构建一个有映射关系的实体对象时，我们可以`queryForList()`方法



> public List queryForList(String sql)
> 
> public List queryForList(String sql, Object[] args)
> 
> public List queryForList(String sql, Object[] args, int[] argTypes)

使用这种方法返回的结果类型是`List<Map<String,Object>>`

其中**List**对象中的成员**Map**的key为列名称、value为查询的结果



用例：

```java
@Test
public void fun01() throws SQLException{
	String sql = "select id,name username,department_id departmentId from employee where department_id=?";
	List<Map<String, Object>> resultList = null;
	resultList = jdbcTemplate.queryForList(sql, 1);
	int i=1;
	for(Map<String, Object> m:resultList) {
		System.out.println("Row:"+i++);
		for(Entry e:m.entrySet()) {
			System.out.println("Key:"+e.getKey()+"  Value:"+e.getValue());
		}
		System.out.println();
	}
}
```



#### 自定义结果集处理器查询

可以定制一下三种接口对结果集处理进行定制,其中ResultSetExtractor功能性最强

* org.springframework.jdbc.core.ResultSetExtractor

    重写`public Object extractData(ResultSet rs)`方法

* org.springframework.jdbc.core.RowCallbackHandler

    重写`public void processRow(ResultSet rs)`方法

* org.springframework.jdbc.core.RowMapper

    重写`public Object mapRow(ResultSet rs, int rowNum)`方法

    

实现处理方法后的接口传入到

* `query(String sql,ResultSetExtractor interface,Object args...)`

* `query(String sql,RowCallbackHandler interface,Object args...)`

* `query(String sql,RowMapper interface,Object args...)`