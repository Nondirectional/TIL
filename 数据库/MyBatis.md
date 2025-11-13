# 初试

先尝试构建一个简单的Mybatis工程来简单了解一下Myabtis。

**1.构建数据库表:**

```mysql
CREATE TABLE user(
	id INT(20) NOT NULL PRIMARY KEY,
    name VARCHAR(30) DEFAULT NULL,
    pwd VARCHAR(30) DEFAULT NULL
)ENGINE = INNODB DEFAULT CHARSET =utf8;

INSERT INTO user(id,name,pwd) VALUES(1,Hari,hh821206);
INSERT INTO user(id,name,pwd) VALUES(2,Mari,hh334981);
```

**2.创建Maven工程**

创建一个普通的Maven工程并引入需要的依赖。

```xml
<!-- pom.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.nond</groupId>
    <artifactId>mybatis-study</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>mybatis-01</module>
    </modules>
    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.23</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
</project>
```

**3.定义pojo对象及其持久层操作接口**

```java
//com.nond.myabtis01.pojo.User
package com.nond.mybatis01.pojo;

public class User {
    private int id ;
    private String name;
    private String pwd;

    public User() { }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}
//com.nond.mybatis01.dao.UserMapper
package com.nond.mybatis01.dao;

import com.nond.mybatis01.pojo.User;

import java.util.List;

public interface UserMapper {
    public List<User> getUsers();
}
```

**4.配置SQL语句映射文件(*mapp.xml)**

与操作接口同一目录下。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace为持久层操作类的类路径-->
<mapper namespace="com.nond.mybatis01.dao.UserMapper">
    <!-- 其中id对应操作接口中具体操作方法名称，resultType对应该方法查询的返回结果类型-->
    <select id="getUsers" resultType="com.nond.mybatis01.pojo.User">
        SELECT * FROM user;
    </select>
</mapper>
```

**5.配置mybaits**

Maven工程resource目录下创建`mybatis-config.xml`:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="mybatis-config.properties"></properties>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/nond/mybatis01/dao/UserMapper.xml"/>
    </mappers>
</configuration>
```

**6.构建获取Session对象的工具类**

```java
public class MybatisUtils {
        static SqlSessionFactory sessionFactory = null;

    static{
        String resource = "mybatis-config.xml";
        InputStream inputStream = null;
        try {
            inputStream = Resources.getResourceAsStream(resource);
            sessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        }catch (IOException e){
            e.printStackTrace();
        }finally {
            try {
                inputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    public static SqlSession getSession(){
        return sessionFactory.openSession();
    }
}
```

**7.测试**

```java
package com.nond.myabtis01.dao;

import com.nond.mybatis01.dao.UserMapper;
import com.nond.mybatis01.pojo.User;
import com.nond.mybatis01.utils.MybatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class UserMapperTest {
    @Test
    public void func01(){
        SqlSession sqlSession = MybatisUtils.getSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        List<User> users = mapper.getUsers();
        for(User u : users){
            System.out.println(u.toString());
        }
        sqlSession.close();
    }
}
```

运行测试以后可能会出现以下错误：

```
java.lang.ExceptionInInitializerError
	at com.nond.myabtis01.dao.UserMapperTest.func01(UserMapperTest.java:14)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:50)
	at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
	at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:47)
	at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
	at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:325)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:78)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:57)
	at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
	at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
	at org.junit.runner.JUnitCore.run(JUnitCore.java:137)
	at com.intellij.junit4.JUnit4IdeaTestRunner.startRunnerWithArgs(JUnit4IdeaTestRunner.java:68)
	at com.intellij.rt.execution.junit.IdeaTestRunner$Repeater.startRunnerWithArgs(IdeaTestRunner.java:47)
	at com.intellij.rt.execution.junit.JUnitStarter.prepareStreamsAndStart(JUnitStarter.java:242)
	at com.intellij.rt.execution.junit.JUnitStarter.main(JUnitStarter.java:70)
Caused by: org.apache.ibatis.exceptions.PersistenceException: 
### Error building SqlSession.
### The error may exist in com/nond/mybatis01/dao/UserMapper.xml
### Cause: org.apache.ibatis.builder.BuilderException: Error parsing SQL Mapper Configuration. Cause: java.io.IOException: Could not find resource com/nond/mybatis01/dao/UserMapper.xml
	at org.apache.ibatis.exceptions.ExceptionFactory.wrapException(ExceptionFactory.java:30)
	at org.apache.ibatis.session.SqlSessionFactoryBuilder.build(SqlSessionFactoryBuilder.java:80)
	at org.apache.ibatis.session.SqlSessionFactoryBuilder.build(SqlSessionFactoryBuilder.java:64)
	at com.nond.mybatis01.utils.MybatisUtils.<clinit>(MybatisUtils.java:19)
	... 23 more
Caused by: org.apache.ibatis.builder.BuilderException: Error parsing SQL Mapper Configuration. Cause: java.io.IOException: Could not find resource com/nond/mybatis01/dao/UserMapper.xml
	at org.apache.ibatis.builder.xml.XMLConfigBuilder.parseConfiguration(XMLConfigBuilder.java:121)
	at org.apache.ibatis.builder.xml.XMLConfigBuilder.parse(XMLConfigBuilder.java:98)
	at org.apache.ibatis.session.SqlSessionFactoryBuilder.build(SqlSessionFactoryBuilder.java:78)
	... 25 more
Caused by: java.io.IOException: Could not find resource com/nond/mybatis01/dao/UserMapper.xml
	at org.apache.ibatis.io.Resources.getResourceAsStream(Resources.java:114)
	at org.apache.ibatis.io.Resources.getResourceAsStream(Resources.java:100)
	at org.apache.ibatis.builder.xml.XMLConfigBuilder.mapperElement(XMLConfigBuilder.java:372)
	at org.apache.ibatis.builder.xml.XMLConfigBuilder.parseConfiguration(XMLConfigBuilder.java:119)
	... 27 more



Process finished with exit code -1
```

错误的重点在**Could not find resource com/nond/mybatis01/dao/UserMapper.xml**，也就是说无法找到UserMapper.xml这个文件。其实就是Maven默认情况下是不会将java目录下所有的xml文件导出的，也就导致了在编译后的target目录下无法找到UserMapper.xml这个文件。

要解决这个问题只要在项目的pom.xml文件当中开启相应的资源过滤即可,即在pom.xml文件中的project标签下添加一下标签内容。

```xml
<build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
    </build>
```

添加再进行测试以后得到如下结果：

> User{id=1, name='Hari', pwd='hh821206'}
>         User{id=2, name='Mari', pwd='hh334981'}



最后我们再来回顾一下这一次所构建的项目结构

![image-20210314181317720](.\图1-1.png)

首先是测试开始运行，获取Session对象。在工具类的方法中获取mybatis-config.xml这个文件当中配置的内容，一是连接数据库需要的信息，二是编写了有哪些Sql语句映射文件(这个例子中就有UserMapper.xml)。

获取到了一个Session对象就相当于打开了一个与数据库的会话，通过这个Session对象能够够直接的与数据库进行交互。

此外，之前还配置了SQL语句映射文件，通过Session对象的getMapper对象就能够获取之前配置的Mapper对象，通过在之前Mapper文件当中预设的sql语句来与数据库进行交互。

在java代码当中具体备用的的其实就是下面这三个类。

**SqlSessionFactoryBuilder**

这个类可以被实例化、使用和丢弃，一旦创建了 SqlSessionFactory，就不再需要它了。 因此 SqlSessionFactoryBuilder 实例的最佳作用域是方法作用域（也就是局部方法变量）。 你可以重用 SqlSessionFactoryBuilder 来创建多个 SqlSessionFactory 实例，但最好还是不要一直保留着它，以保证所有的 XML 解析资源可以被释放给更重要的事情。

**SqlSessionFactory**

SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例。 使用 SqlSessionFactory 的最佳实践是在应用运行期间不要重复创建多次，多次重建 SqlSessionFactory 被视为一种代码“坏习惯”。因此 SqlSessionFactory 的最佳作用域是应用作用域。 有很多方法可以做到，最简单的就是使用单例模式或者静态单例模式。

**SqlSession**

每个线程都应该有它自己的 SqlSession 实例。SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域。 绝对不能将 SqlSession 实例的引用放在一个类的静态域，甚至一个类的实例变量也不行。 也绝不能将 SqlSession 实例的引用放在任何类型的托管作用域中，比如 Servlet 框架中的 HttpSession。 如果你现在正在使用一种 Web 框架，考虑将 SqlSession 放在一个和 HTTP 请求相似的作用域中。 换句话说，每次收到 HTTP 请求，就可以打开一个 SqlSession，返回一个响应后，就关闭它。 这个关闭操作很重要，为了确保每次都能执行关闭操作，你应该把这个关闭操作放到 finally 块中。 

# 1. 全局配置文件

##  1.1 properties标签

这个标签的作用是用来引入外部的properties配置文件内容。该标签有两个属性resource和url，其中resourece用于引入类路径下的资源，url用来引入网络路径资源或磁盘路径资源。

```xml
<properties
	resourece="com.nond.learnMybatis.config.mybatis-config.properties"
	url="http://nond.com/config/mybatis-config.properties"
	url="C:/config/mybatis-config.properties"
>
		
<!--
	如果使用了properties标签引入了properties配置文件内容，则可以使用下面这种方式对mybatis进行配置
-->

<dataSource type="POOLED">
	<property name="driver" value="${jdbc.driver}"/>
	<property name="url" value="${jdbc.url}"/>
	<property name="username" value="${jdbc.username}"/>
	<property name="password" value="${jdbc.password}"/>
</dataSource>
	
<!-- 文件:mybatis-config.properties 
	jdbc.driver=xxx.xxx.xxx
	jdbc.url=xxx.xxx
	jdbc.username=root
	jdbc.password=123456
-->
```

## 1.2. settings标签

用于配置Mybatis的各个设置项。这些设置项设置的是Mybatis运行时的行为。

```xml
<settings> <!-- 包含各个配置项 -->
	<setting name="xxx" value="xxx"/> <!-- 具体的配置项 -->
</settings>
```

### 1.2.1 开启自动驼峰命名规则(mapUnderscoreToCamelCase)

这个设置项的取值有true和false，默认情况下的取值是false。如果设置了取值为true，即自动完成从经典数据库命名Column_Name到经典Java属性名columnName的类似映射。	

```xml
<setting name="mapUnderscoreToCamelCase" value="true"/>
```

### 1.2.2 开启延迟加载

有关延迟加载有两个设置项：

1. lazyLodingEnabled

   该设置项默认值为false，当设置为true开启时，某个对象当中如果关联了有其他对象，那么仅当被关联的对象被使用时被关联的对象才会被加载。

2. aggressiveLazyLoding

   该设置项的默认值为true，当被关联对象被使用到时，该对象的所有属性豆浆会被加载，如果设置该设置项值为false，那么只有被关联对象被使用到的值才会被加载。

在使用association标签进行分步查询时，可以通过这两个值来管理延迟加载。

## 1.3. typeAliases标签

这个标签是别名处理器，作用是在给Java类型全类名取一个别名，这样在Mybatis需要使用到全类名的地方可以使用别名来代替全类名，减少输入全类名的工作量。在这个标签内还有typeAlias子标签，子标签具体的指定某个Java类型全类名的别名。

```xml
<!--mybatis-config.xml-->
<typeAliases>
	<typeAlias type="com.nond.learnMybatis.model.Person" alias="people"/>
	<!--
		这样就给Person类取了一个叫people的别名
		如果上面的typeAlias标签只给定了type属性值而没有alias属性，那么Mybatis会默认给这个类型取一个别名（类名小写），也即person
	-->
</typeAliases>

<!--PersonMapper.xml-->
<mapper namespace="com.nond.learnMybatis.model.PersonMapper">
    <select id="selectAllById" resultType="people">
        SELECT * FROM person WHERE id = #{id}
    </select>
</mapper>
```
我们注意到，使用typeAlias标签每一次只能够为一个Java类型取别名，当有大量的类需要取别名的时候又要怎么办呢？

除typeAlias标签以外，在typeAliases标签下还有另外一个子标签package，这个标签的作用就是批量的给某个包下的所有Java类型取别名。使用这种方式给Java类型取得别名都是默认别名，也就是上面说的别名为类名小写。

```xml
<typeAliases>
	<package name="com.nond.learnMybatis.model"/>
</typeAliases>
```

如果用到了package标签为一个包下的类批量起别名，有可能会出现有同名类冲突。其扫描是扫描该路径下的额所有类，即包括子包下的类。如果存在同名类，就需要使用到@Alias注解对该类另外取一个别名消除冲突。

```java
@Alias("person1")
public class Person{
    ...
}
```



## 1.4. environments标签

environments标签用来配置Mybatis运行使用哪一个环境，比如说 生产环境、测试环境和开发环境。其中default属性就是用来选择某一个环境的，其取值为environment的ID。

```xml
<environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
      	</environment>
</environments>
```

而子标签environment就用来具体的配置环境，属性id设置该环境的id。在environment标签下还有transactionManager标签和dataSource标签。

transactionManager标签用于配置该环境使用哪种事务管理器, 使用type属性指定。

- JDBC：使用JDBC事务管理器。
- MANAGED：这种机制mybatis自身不会去实现事务管理，而是让程序的容器（JBOSS,WebLogic）来实现对事务的管理。
- 自定义事务管理器：如果需要自定义事务管理器可以创建一个类实现TransactionFactory接口，然后type属性设置为该类的全类名。



dataSource标签用于配置该环境使用的数据源，type属性设置数据源类型，子标签property设置数据源的配置信息。

其中数据源类型有UNPOOLED、POOLED、JNDI，其中UNPOOLED为不使用连接池技术，POOLED为使用Mybatis自带的连接池技术、JNDI为使用JNDI。除此之外还可以使用自定义的连接池，自定义类实现DataSourceFactory接口后，type属性设置为该类全类名即可。

## 1.5. mappers标签

mappers标签配合其子标签mapper用于注册SQL映射。其中mapper子标签有三个属性resource、url和class都是用来指定SQL映射文件路径的。

```xml
<mappers>
    <!-- 1. resource属性：引用类路径下的sql映射文件 -->
    <mapper resource="mappers/PersonMapper.xml"></mapper>
    <!-- 2. url属性：引用网络路径或磁盘路径下的sql映射文件 -->
    <mapper url="/var/PersonMapper.xml"></mapper>
    <!-- 3. class属性：引用（注册）接口，可以有两种用法
			1) 有sql映射文件，映射文件名必须和接口同名，并且要与接口放在同一目录下。
			2) 没有sql映射文件，所有sql映射都用注解的形式写在接口上。
	-->
    <mapper class="com.nond.learnMybatis.dao.PersonMapper"></mapper>
</mappers>
```



# 2.映射文件

## 2.1. 实现增删改查

要用Mybatis实现对表记录的增删改查有三个个前提，首先是要正确编写SQL映射，其次就是编写相对应的映射接口，最后注册SQL映射，完成这三项工作之后就可以在Java代码中使用到Mybatis对进行表记录操作。

SQL映射：

```xml
<!--PersonMapper.xml-->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC  "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybaits.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.nond.learnMybatis.model.PersonMapper">
    <!-- 
    <select id="select" resultType="com.nond.learnMybatis.model.Person">
        SELECT * FROM person WHERE id = #{id}
    </select>

    <insert id="insert" parameterType="com.nond.learnMybatis.model.Person">
        INSERT INTO person
        VALUES(#{id},#{firstName},#{lastName},#{age})
    </insert>

    <update id="update">
        UPDATE person
        SET first_name=#{firstName},last_name=#{lastName},age=#{age}
        WHERE id = #{id}
    </update>

    <delete id="delete">
        DELETE FROM person
        WHERE id=#{id}
    </delete>
</mapper>
```

接口：

```java
//PersonMapper.java
public interface PersonMapper {
    public Person select(int id);
    public void insert(Person person);
    public void update(Person person);
    public void delete(int id);
}
```

使用：

```java
//Test01.java - func01()
public void func01() throws IOException {
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    	//openSession有两种打开回话的方式
    	//    sessionFactory.openSession()为默认打开方式，关闭自动提交功能
    	//    sessionFactory.openSession(true)为开启自动提交功能
        try(SqlSession session = sessionFactory.openSession()){
            PersonMapper mapper = session.getMapper(PersonMapper.class);
            System.out.println(mapper.select(1));
            
            mapper.insert(new Person(2,"Smith","Mari",18));
            System.out.println(mapper.select(1));
            
            mapper.update(new Person(2,"Smith","Mari",19));
            System.out.println(mapper.select(2));

            mapper.delete(2);
            System.out.println(mapper.select(2));
            
            session.commit();
        }catch (Exception e){
            e.printStackTrace();
        }
    }
```

## 2.2. insert获取自增主键的值

在数据库表使用自增主键时，往往不需要给定id予对象，但是插入操作之后可能有需要用到这个表记录的id。如果需要该如何获取？

标签的useGenerateKeys属性设置为true（对于支持自动生成记录主键的数据库，如：MySQL，SQL Server，此时设置useGeneratedKeys参数值为true，在执行添加记录之后可以获取到数据库自动生成的主键ID），keyProperty设置为接收自增主键值的属性名。需要注意一点，传入的参数需要是一个javabean其属性需要包含keyProperty指定的属性用来接收返回的主键值。

```xml
<!--PersonMapper.xml-->
<insert id="insert" parameterType="com.nond.learnMybatis.model.Person"
    useGeneratedKeys="true" keyProperty="id">
    INSERT INTO person(first_name,last_name,age)
    VALUES(#{firstName},#{lastName},#{age})
</insert>
```

执行完操作以后，自增主键的值就写入到了参数对象的相应属性当中。

```java
//Test01.java - func01()
String resource = "mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
try(SqlSession session = sessionFactory.openSession()){
    PersonMapper mapper = session.getMapper(PersonMapper.class);
    Person p = new Person("Smith","Kali",20);
    mapper.insert(p);
    System.out.println(p.getId());
            
    session.commit();
    }catch (Exception e){
    e.printStackTrace();
}
```



在使用时遇到了一个问题：

```
org.apache.ibatis.binding.BindingException: Mapper method 'com.nond.mybatis02.mapper.DepartmentMapper.insertDepartment' has an unsupported return type: class com.nond.mybatis02.pojo.Department

	at org.apache.ibatis.binding.MapperMethod.rowCountResult(MapperMethod.java:118)
	at org.apache.ibatis.binding.MapperMethod.execute(MapperMethod.java:62)
	at org.apache.ibatis.binding.MapperProxy.invoke(MapperProxy.java:57)
	at com.sun.proxy.$Proxy4.insertDepartment(Unknown Source)
	at com.nond.mybatis02.mapper.DepartmentMapperTest.insertDepartment(DepartmentMapperTest.java:14)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:50)
	at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
	at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:47)
	at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
	at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:325)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:78)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:57)
	at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
	at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
	at org.junit.runner.JUnitCore.run(JUnitCore.java:137)
	at com.intellij.junit4.JUnit4IdeaTestRunner.startRunnerWithArgs(JUnit4IdeaTestRunner.java:68)
	at com.intellij.rt.execution.junit.IdeaTestRunner$Repeater.startRunnerWithArgs(IdeaTestRunner.java:47)
	at com.intellij.rt.execution.junit.JUnitStarter.prepareStreamsAndStart(JUnitStarter.java:242)
	at com.intellij.rt.execution.junit.JUnitStarter.main(JUnitStarter.java:70)

```

这是因为mapper文件中的update,delete,insert语句是不需要设置返回类型的，他们都是默认返回一个int,而我这次在使用时mapper接口中定义的方法是`public Department insertDepartment(String name)`，然而真确的使用应该是这样的：

```java
//DepartmentMapper.java
public int insertDepartment(String name);

//DepartmentMapperTest.java
@Test
public void insertDepartment(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        DepartmentMapper mapper = sqlSession.getMapper(DepartmentMapper.class);
        Department department = new Department("开发");
        int i = mapper.insertDepartment(department);
        System.out.println(department);

        sqlSession.commit();
        sqlSession.close();
}
```



但是有些数据库是不支持自动生成记录主键的，比如说Oracle就需要先获取一个自增序号之后再将该值给到insert语句进行插入，对于这种情况使用上面所说的方式就不能够拿到这个主键的值了。拿Oracle的插入举个例子，xml映射的编写如下：

```xml
<!--PersonMapper.xml-->
<insert id="insert" parameterType="com.nond.learnMybatis.model.Person"> 
          <selectKey resultType="Integer" keyProperty="id" order="BEFORE"> 
              select SEQ_USER_ID.nextval as id from dual 
          </selectKey> 
          INSERT INTO person 
          (id,fist_name,last_name,age) 
          VALUES
          (#{id},#{first_name},#{last_name},#{age}) 
</insert>
```

其中selectKey的order属性BEFORE的意思是在insert语句执行之前先进行查询。

使用selectKey标签也同样和useGrenerateKeys属性设置为true获取到自增主键的值，但是需要注意的是order属性的值需要设置为AFTER，即在insert语句执行之后进行查询。

```xml
<!--PersonMapper.xml-->
<insert id="insert" parameterType="com.nond.learnMybatis.model.Person">
    INSERT INTO person(first_name,last_name,age)
    VALUES(#{firstName},#{lastName},#{age})
    <selectKey keyProperty="id" resultType="Integer" order="AFTER">
        SELECT LAST_INSERT_ID() AS ID
    </selectKey>
</insert>
```

## 2.3 参数处理

### 2.3.1 多参数的处理机制

我们之前尝试的多是接口方法只接收一个参数，这种情况映射中每次获取参数获取到的都是那一个参数。如有接口：`public Person selectPersonById(int id);`，那么在映射中无论是用#{id}、#{xx}、#{n}来获取参数，获取到的都是传入参数id。举个例子：调用之前定义的select并将映射中的SQL语句改为

```xml
<!--PersonMapper.xml-->
<select id="select" resultType="com.nond.learnMybatis.model.Person">
        SELECT * FROM person WHERE id = #{abcd}
</select>
```

然后再次运行

```java
//Test01.java - func01()
try(SqlSession session = sessionFactory.openSession()){
    PersonMapper mapper = session.getMapper(PersonMapper.class);
    System.out.println(mapper.select(1));
}catch (Exception e){
    e.printStackTrace();
}

//输出：Person{id=1, firstName='Davis', lastName='Hari', age=18}
```

如果此时新增了一个接口方法`public Person selectById_firstName(int id,String firstName);`

映射的SQL语句为：

```xml
<!--PersonMapper.xml-->
<select id="selectById_firstName" resultType="com.nond.learnMybatis.model.Person">
    SELECT * FROM person
    WHERE id=#{id} AND first_name=#{firstName}
</select>
```

然后尝试调用这个方法进行查询，结果却报错了`Parameter 'id' not found. Available parameters are [arg1, arg0, param1, param2]`，这是因为在有多参数时Mybatis会将参数封装在一个Map中，如果没有指定各个参数的key的话，Mybatis会默认的将其key设置为param1、param2........或arg0、arg1.......，要指定各个参数的key就需要在映射接口中使用`@Param`注解标注参数来指定其key。

```java
//PersonMapper.java
public Person selectById_firstName(@Param("id") int id,@Param("firstName") String firstName);
```

正确设置以后就能够在映射的SQL语句中使用#{id}、#{firstName}获取传入参数。

但其实用这种方式传入参数相对而言比较麻烦，如果有POJO可以直接将POJO传入，其次还可以直接传入一个Map对象省去在接口方法中标注的麻烦。

### 2.3.2 多参数处理的源码分析

现有方法`public int insertUser(@Param("id")int id,@Param("name")String name,String pwd);`

```java
//调用insertUser(3,"小李","hh3344455")

//MapperProxy.java  
@Override
  public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    try {
        //如果执行方法的类是常规的Object类或方法为默认方法则直接放行
      if (Object.class.equals(method.getDeclaringClass())) {
        return method.invoke(this, args);
      } else if (method.isDefault()) {
        return invokeDefaultMethod(proxy, method, args);
      }
    } catch (Throwable t) {
      throw ExceptionUtil.unwrapThrowable(t);
    }
      //转到这里执行
    final MapperMethod mapperMethod = cachedMapperMethod(method);
    return mapperMethod.execute(sqlSession, args);
  }

//MapperMethod.java
public Object execute(SqlSession sqlSession, Object[] args) {
    Object result;
    //该类的构造方法中已经解析出需要执行的语句类型，根据需要执行的语句类型来选择具体执行方法
    //但是都需要进行参数解析:-> Object param = method.convertArgsToSqlCommandParam(args);
    switch (command.getType()) {
      case INSERT: {
        //进入参数解析 go:line 73
        Object param = method.convertArgsToSqlCommandParam(args);
        result = rowCountResult(sqlSession.insert(command.getName(), param));
        break;
      }
      case UPDATE: {
        Object param = method.convertArgsToSqlCommandParam(args);
        result = rowCountResult(sqlSession.update(command.getName(), param));
        break;
      }
      case DELETE: {
        Object param = method.convertArgsToSqlCommandParam(args);
        result = rowCountResult(sqlSession.delete(command.getName(), param));
        break;
      }
      case SELECT:
        if (method.returnsVoid() && method.hasResultHandler()) {
          executeWithResultHandler(sqlSession, args);
          result = null;
        } else if (method.returnsMany()) {
          result = executeForMany(sqlSession, args);
        } else if (method.returnsMap()) {
          result = executeForMap(sqlSession, args);
        } else if (method.returnsCursor()) {
          result = executeForCursor(sqlSession, args);
        } else {
          Object param = method.convertArgsToSqlCommandParam(args);
          result = sqlSession.selectOne(command.getName(), param);
          if (method.returnsOptional()
              && (result == null || !method.getReturnType().equals(result.getClass()))) {
            result = Optional.ofNullable(result);
          }
        }
        break;
      case FLUSH:
        result = sqlSession.flushStatements();
        break;
      default:
        throw new BindingException("Unknown execution method for: " + command.getName());
    }
    if (result == null && method.getReturnType().isPrimitive() && !method.returnsVoid()) {
      throw new BindingException("Mapper method '" + command.getName()
          + " attempted to return null from a method with a primitive return type (" + method.getReturnType() + ").");
    }
    return result;
  }

public Object convertArgsToSqlCommandParam(Object[] args) {
    //进入参数解析 go:line 78
	return paramNameResolver.getNamedParams(args);
}

//ParamNameResolver.java
  //解析传入的各个参数的参数名  go:line 91
  public ParamNameResolver(Configuration config, Method method) {
    final Class<?>[] paramTypes = method.getParameterTypes();
    final Annotation[][] paramAnnotations = method.getParameterAnnotations();
    final SortedMap<Integer, String> map = new TreeMap<>();
    int paramCount = paramAnnotations.length;
    // get names from @Param annotations
    for (int paramIndex = 0; paramIndex < paramCount; paramIndex++) {
      if (isSpecialParameter(paramTypes[paramIndex])) {
        // skip special parameters
        continue;
      }
      String name = null;
      for (Annotation annotation : paramAnnotations[paramIndex]) {
        //如果有标签标注则参数名为标签的value值
        if (annotation instanceof Param) {
          hasParamAnnotation = true;
          name = ((Param) annotation).value();
          break;
        }
      }
      //如果没有标签标注则参数名为0,1,2,3....
      if (name == null) {
        // @Param was not specified.
        if (config.isUseActualParamName()) {
          name = getActualParamName(method, paramIndex);
        }
        if (name == null) {
          // use the parameter index as the name ("0", "1", ...)
          // gcode issue #71
          name = String.valueOf(map.size());
        }
      }
      map.put(paramIndex, name);
    }
    names = Collections.unmodifiableSortedMap(map);
    //go:line 118
  }

  public Object getNamedParams(Object[] args) {
    final int paramCount = names.size();
    //若参数为零则返回null
    if (args == null || paramCount == 0) {
      return null;
    //若无Param标签标注且参数仅有一个,则直接将这个参数返回
    } else if (!hasParamAnnotation && paramCount == 1) {
      return args[names.firstKey()];
    } else {
      final Map<String, Object> param = new ParamMap<>();
      int i = 0;
      for (Map.Entry<Integer, String> entry : names.entrySet()) {
        //有被@Param标签标注的则用names entry的value作为param map的key，names entry的key做为param map的value的取值依据
        param.put(entry.getValue(), args[entry.getKey()]);
        // add generic param names (param1, param2, ...)
        //所有的参数还将给与一个默认参数名加入到param map中,其key为"Param"前缀+String.valueOf(i +++ 1);
        final String genericParamName = GENERIC_NAME_PREFIX + String.valueOf(i + 1);
        // ensure not to overwrite parameter named with @Param
        if (!names.containsValue(genericParamName)) {
          param.put(genericParamName, args[entry.getKey()]);
        }
        i++;
      }
      return param;
    }
  }
```

这一系列操作以后 param map如下

![image-20210316010420446](.\图3-1.png)

### 2.3.3 ${}取值与#{}取值的区别

使用前者对参数进行取值会直接将参数设置到sql语句上，而后者则是用预编译的形式将参数设置到sql语句上

${}： `INSERT INTO user(id,name,pwd) VALUES(${id},${name},${pwd});`==>`INSERT INTO user(id,name,pwd) VALUES(3,"小李","hh334455");`

#{}：`INSERT INTO user(id,name,pwd) VALUES(#{id},#{name},#{pwd});`==>`INSERT INTO user(id,name,pwd) VALUES(?,?,?);`

一般情况下都是使用#{}来进行取值，但是在某些情况下是无法在预编译的情况下进行取值的，比如说按年份分表拆分则需要使用到${}来进行取值

`SELECT * FROM ${year}_salary WHERE xxx;`



## 2.4 Select

### 2.4.1 查询结果封装为List

查询结果有多个时返回List，而这时select标签的resultType属性需要填写的结果类型需要填写List中元素的类型。

假设有查询`public List<User> getUsers();`其查询user表中的所有记录。

sql映射如下：

```xml
<select id="getUsers" resultType="com.nond.mybatis01.pojo.User">
    SELECT * FROM user;
</select>
```

### 2.4.2 查询结果封装为map

第一种情况是查询一条记录，字段名为key、字段值为value封装为一个map。

`public Map<String,Object> getUserById_toMap(int id);`

```xml
<select id="getUserById_toMap" resultType="map">
    SELECT * FROM mybatis.user WHERE id=#{id}
</select>
```

第二种情况是查询多条记录，将记录的某个字段值作为key，整个记录作为key封装为一个map。

```xml
<select id="getUsers_toMap" resultType="com.nond.mybatis01.pojo.User">
    SELECT * FROM user;
</select>
```

具体哪个字段作为map的key在接口中使用**MapKey**注解标注。

```java
@MapKey("id")
public Map<Integer,User> getUsers_toMap();
```

### 2.4.3 resultMap属性

该属性的作用是指定一个自定义的映射规则。原先使用resultType一般是直接列明对应属性名映射或驼峰命名法映射，而resultMap属性可以指定一个使用resultMap标签制定的映射规则。

`public List<User> getUsers2()`

```xml
<resultMap id="employeeMap" type="com.nond.mybatis01.pojo.User">
	<id column="id" property="id"/><!-- id标签用于主键的映射 -->
    <rersult column="name" property="name"/><!-- reslut标签用于其他列的映射 -->
    <result column="pwd" property="pwd"/><!-- column为sql中的列名，property为Java中的属性名 -->
</resultMap>

<select id="getUsers2" resultMap="userMap">
	SELECT * FROM user
</select>
```

除此之外resultMap还能够运用在**多表联查**的场景当中。我们现在的pojo和相应的数据库表发生了变化：

```java
/*
CREATE TABLE user(
	id INT PRIMARY KEY,
	name VARCHAR(30),
	pwd VARCHAR(30),
	d_id INT,
	CONSTRAINT fk_user_dept FOREGIN KEY (d_id) REFERENCES department(id)
)
*/
public class User {
    private int id ;
    private String name;
    private String pwd;
    private Deparment department;
}

/*
CREATE TABLE department(
	id INT PRIMARY KEY,
	d_name VARCHAR(30)
)
*/
public class Department{
    private int id;
    private String departmentName;
}
```

User多了一个Department属性，并且它还有id和departmentName这两个属性，而user表是通过department表的id来进行关联的。这时候只需要一个多表联查语句，将所有的属性都查询出来，在通过result标签将他们一一映射即可。通过这样的方式不单单只可以查询并映射user表的所有字段，还能够将department表的所有字段也查询并映射。

```xml
    <resultMap id="userMap" type="com.nond.mybatis01.pojo.User">
        <id column="id" property="id"/>
        <result column="name" property="name"/>
        <result column="pwd" property="pwd"/>
        <!-- 通过级联属性的方式将列映射至具体的属性 -->
        <result column="d_id" property="department.id"/>
        <result column="d_name" property="department.departmentName"/>
    </resultMap>

    <select id="getUsers3" resultMap="userMap">
        SELECT u.id id,u.name name,u.d_id d_id,d.d_name d_name 
        FROM user u,department d 
        WHERE d.id = u.d_id
    </select>
```

除了使用result标签来实现之外还有一个association标签也可以完成同样的目标。其指定关联的java属性，然后将查询到有关该属性列的值赋予该属性的属性。它们所需要的的sql语句还是一样的，只不过在映射规则的编写方式上有不同而已。

```xml
<resultMap id="userMap2" type="com.nond.mybatis01.pojo.User">
    <id column="id" property="id"/>
    <result column="name" property="name"/>
    <result column="pwd" property="pwd"/>
    <!--
	<result column="d_id" property="department.id"/>
    <result column="d_name" property="department.departmentName"/>
	变为下面这种形式
	-->
    <association property="department" javaType="com.nond.mybatis01.pojo.Deparment">
        <id column="d_id" property="id"/>
        <result column="d_name" property="departmentName"/>
    </association>
</resultMap>
```



**分步查询**

association标签除了能够通过多表联查的形式来实现多表关联的映射以外，还能够进行分步查询来完成。

一般来说，department表也会有其自己的sql映射，比如说selectDepartmentById。先查询出user表的某些记录所有字段，然后通过user中的d_id查询department表某些记录的所有字段，这样同样也查询到了所有需要的字段。

现在departmentMapper中有：

```xml
<!-- namespace:com.nond.mybatis01.dao.DepartmentMapper-->
<select id="getDepartmentById" resultType="com.nond.mybatis01.pojo.Department">
	SELECT * FROM department WHERE id=#{id}
</select>
```

上面多表联查的映射就可以改为：

```xml
<resultMap id="getUsers" type="com.nond.mybatis01.pojo.User">
	<id column="id" property="id"/>
    <result column="name" property="name"/>
    <result column="pwd" property="pwd"/>
    <association property="department"
                 select="com.nond.mybatis01.dao.DepartmentMapper.getDepartmentById"
                 column="d_id"/>
</resultMap>
```

其中select指向分步查询的第二条语句（目标sql语句映射所在的命名空间+sql语句映射id ）,column为第二部查询要用到的字段。



**关联属性为集合属性**

现在的Department发生了变化，对象还有一个集合属性存储当前部门下的所有员工对象。这个时候单单使用级联属性的那套方法就不能够解决这个问题了。

在resultMap标签下还有一个collection标签，它的作用就是用来处理关联属性的为集合类型的映射的。

```xml
<select id="getDepartmentById" resultMap="departmentMap">
    SELECT d.id id,d.d_name d_name,u.id u_id,u.name u_name,u.pwd u_pwd
    FROM mybatis.user u,mybatis.department d
    WHERE u.d_id = d.id AND d.id=#{id}
</select>
    
<resultMap id="departmentMap" type="com.nond.mybatis01.pojo.Department">
    <id column="id" property="id"/>
    <result column="d_name" property="departmentName"/>
    <!-- ofType:指定集合中对象的类型 -->
    <collection property="users" ofType="com.nond.mybatis01.pojo.User">
        <id column="u_id" property="id"/>
        <result column="u_name" property="name"/>
        <result column="u_pwd" property="pwd"/>
    </collection>
</resultMap>
```



同样的这也可以进行分步查询，详细看下面示例：

```xml
<!-- DepartmentMapper.xml -->
    <select id="getDepartmentById" resultMap="departmentMap2">
        SELECT *
        FROM department
        WHERE id=#{id}
    </select>
    <resultMap id="departmentMap2" type="com.nond.mybatis01.pojo.Department">
        <id column="id" property="id"/>
        <result column="d_name" property="departmentName"/>
        <collection property="users"
                    select="com.nond.mybatis01.dao.UserMapper.getUsersByDepartmentId"
                    column="id"/>
    </resultMap>

<!-- UserMapper.xml -->
    <select id="getUsersByDepartmentId" resultType="com.nond.mybatis01.pojo.User">
        SELECT * FROM mybatis.user WHERE d_id=#{d_id}
    </select>
```

分步查询可以这样理解：先通过getDepartmentById查询到部门信息，然后再将查询到的部门id传给getUsersByDepartmentId作进一步查询得到部门下所有员工信息。



再再思考一个问题，如果分步查询的时候第二步查询需要用到的查询条件不止一个的时候该怎么传递列过去？

原先使用到collection和association的column属性都只给了一个列名，而要传递多列只要把列写成map的形式就可以了。假设现在查询的是员工年龄平均年龄为24岁的部门，并且属性包含所有在该部门的24岁员工对象集合，那么第二步查询就需要把部门id和年龄传递过去。

```xml
<!-- DepartmentMapper.xml -->
<select id="getDepartmentById&uAge" resultMap="departmentMap2">
        SELECT *
        FROM department
        WHERE id=#{id} avg_age=#{avgAge}
</select>
    <resultMap id="departmentMap2" type="com.nond.mybatis01.pojo.Department">
        <id column="id" property="id"/>
        <result column="d_name" property="departmentName"/>
        <collection property="users"
                    select="com.nond.mybatis01.dao.UserMapper.getUsersByDepartmentId"
                    column="{d_id=id,age=avg_age}"/>
    </resultMap>

<!-- UserMapper.xml -->
<select id="getUsersByDepartment&Age" resultType="com.nond.mybatis01.pojo.User">
    SELECT * FROM mybatis.user WHERE d_id=#{d_id} AND age=#{age}
</select>
```





通过查询结果选择封装行为。

查询用户，如果用户部门为1，则将name列映射到属性pwd上、pwd列映射到属性name上,如果部门为2，则仅将name列映射属性pwd上。

```xml
<resultMap id="userMap3" type="com.nond.mybatis01.pojo.User">
    <id property="id" column="id"/>
    <discriminator javaType="int" column="d_id">
        <!-- case中注意要填写resultType -->
        <case value="1" resultType="com.nond.mybatis01.pojo.User">
            <result column="pwd" property="name"/>
            <result column="name" property="pwd"/>
        </case>
        <case value="2" resultType="com.nond.mybatis01.pojo.User">
            <result column="name" property="pwd"/>
        </case>
    </discriminator>
</resultMap>
```

如果某些列没有相应的映射规则并且它是Mybatis的默认映射规则能够完成映射的列，那么Mybatis会自动的为这些列完成映射，否则不进行映射。比如说在上面代码中的case2命中时，name列占用了pwd属性，那么Mybatis就不会自动的为pwd列完成映射因为pwd属性已经被其他属性映射了。



## 2.5 动态sql

使用动态sql可以根据条件改变实际的sql语句。比如说根据id，age，gender这三列值来查询gender表，但不是三个列的值都需要，只要传入了就使用。原先在JDBC中只能够预先写好各种sql语句，再根据实际传入了哪几个参数选择一个sql语句进行查询。在Mybatis中用动态sql只需要写一条映射即可。先看第一个示例：

```xml
	<select id="getEmployeesByCondition" resultType="com.nond.mybatis02.pojo.Employee">
        SELECT * FROM employee
        WHERE
        <if test="id!=null">
            id=#{id}
        </if>
        <if test="age!=null">
            AND age=#{age}
        </if>
        <if test="gender==0 or gender==1">
            AND gender=#{gender}
        </if>
    </select>
```

test属性中的内容就是条件语句。但是这样做是由缺陷的，if语句的任务只是根据条件是否成立来决定是否将标签体内的追加到语句当中。当传入的id为null时最后生成的语句是这样的`SELECT * FROM employee WHERE AND age=xxx AND gender=xxx`，或者说所有的可选参数都没有传入，语句就变成了`SELECT * FROM employee WHERE`，很明显这样的语句是非法的。

为了避免这样的情况出现，可以额外在if标签外套一个`<where>`标签替代语句中的WHERE，并且会自动的去掉在语句开头多余的AND 或 OR（语句末尾的无法自动去除）。`<where>`标签加入以后第一个示例的代码就变成了这样：

```xml
    <select id="getEmployeesByCondition" resultType="com.nond.mybatis02.pojo.Employee">
        SELECT * FROM employee
        <where>
            <if test="id!=null">
                id=#{id}
            </if>
            <if test="age!=null">
                AND age=#{age}
            </if>
            <if test="gender==0 or gender==1">
                AND gender=#{gender}
            </if>
        </where>
    </select>
```

现在，即使id没有传入也能够正常的工作。但是上面也提到了，放在语句后面多余的AND或OR还是不会自动取出的。如果非要放在后面的话就可以使用`<trim>`标签。

```xml
<!--
<trim prefix="给整个标签体拼接的内容加一个前缀" prefixOverrides="去掉标签体前面多出来的内容"
      suffix="给整个标签体拼接的内容加一个后缀" suffixOverrides="去掉标签体末尾多出来的内容">
</trim>
-->

<select id="getEmployeesByCondition" resultType="com.nond.mybatis02.pojo.Employee">
    SELECT * FROM employee
    <trim prefix="WHERE" prefixOverrides="AND" suffixOverrides="AND">
        <if test="id!=null">
            id=#{id} AND
        </if>
        <if test="age!=null">
            age=#{age} AND
        </if>
        <if test="gender==0 or gender==1">
            gender=#{gender}
        </if>
    </trim>
</select>
```





choose

类似于Java中的Swich语句，在多个选项当中选择一个执行。

```xml
<select id="getEmployeeByChoose" resultType="com.nond.stu01.pojo.Employee">
    SELECT * FROM employee
    <where>
        <choose>
            <when test="id!=null">
                id=#{id}
            </when>

            <when test="lastName!=null">
                last_name like concat("%",#{lastName},"%")
            </when>

            <otherwise>
                gender=0
            </otherwise>
        </choose>
    </where>
</select>
```

在上面实例当中先判断id!=null是否成立，否则判断lastName!=null是否成立，当所有的选项都未命中时选择otherwise条件。

这里稍微提一点，在Mybatis映射中怎么使用模糊查询。以%%为例，使用concat函数将模糊查询符号与#{}进行拼接即可。





**foreach**

```xml
    <select id="getEmployeesByIdIn" resultType="com.nond.stu01.pojo.Employee">
        <!-- 原语句：
		SELECT * FROM employee WHERE id IN (... , ... , ...)
        使用foreach如下
		-->
        SELECT * FROM employee WHERE id IN
        <foreach collection="ids" item="id" open="(" close=")" separator=",">
            #{id}
        </foreach>
        <!-- 
		collection:指定foreach使用中使用的集合名称
		item:集合元素名称
		open:foreach语句最终在语句开头填充符号
		close:foreach语句最终在语句结尾填充符号
		separator:分割符号
		-->
    </select>
```

内置参数

**_parameter**

单个参数：_parameter就代表这个参数

多个参数：参数会被封装成一个map，_parameter就代表这个map

```xml
<select id="xxx" resultType="employee">
	SELECT * FROM employee
    <where>
    	<choose>
            <!-- 如果传入的整个对象不为null -->
        	<case test="_parameter!=null">
            	id=#{id}
            </case>
            <otherwise>
            	gender=0
            </otherwise>
        </choose>
    </where>
</select>
```



**_databaseId**

_databaseId:如果配置了databaseIdProvider标签

_databaseId 就是代表当前数据库的别名Oracle

```xml
<select id="xxx" resultType="employee">
	<if test="_databaseId=mysql">
    	SELECT * FROM mysql_employee
    </if>
    
    <if test="_databaseId=oracel">
    	SELECT * FROM oracel_employee
    </if>
</select>
```



bind标签

可以把OGIN表达式的值赋值给一个变量。

```xml
<select id="xxx" resultType="employee">
	<bind name="_lastName" value="%狗%"/>
    SELECT * FROM employee WHERE last_name LIKE #{_lastName}
</select>
```







sql通过sql标签还能够定义重用的sql语句片段

```xml
<sql id="xxxx">
	a,b,c,d
</sql>


<!-- 在实际语句内部使用include标签来把该片段插入 -->
<select id="xxxxxx">
	INSERT INTO abcd(
    	<include refid="xxxx"></include>
    ) VALUES(1,2,3,4)
</select>
```

另外在sql标签内也能够使用其他的动态sql标签

除此之外，还能够通过include标签来想sql标签内定义的sql语句传递变量



``` xml
<sql id="xxx">
	a,b,c,${x}
</sql>

<include refid="xxx">
	<property name="x" value="g"
</include>
```







Cache

Mybatis的Cache分为一级缓存和二级缓存，默认情况下一级缓存式自动开启的。首先，一级缓存的作用域是在一个Session内的，也就是说在一次会话当中查询到的数据会被缓存，但是仅限于这次会话当中。在应用运行过程中，我们有可能在一次数据库会话中，执行多次查询条件完全相同的SQL，MyBatis 提供了一级缓存的方案优化这部分场景，如果是相同的SQL语句，会优先命中一级缓存，避免直接对数据库进行查询，提高性能。



二级缓存又是怎么样的？

二级缓存的作用域是在每个对象的mapper中的，但是它不仅仅局限于一次会话。**当某个缓存从一个Session当中被清出时如果开启了二级缓存则该条缓存才会进入到二级缓存当中。**

Mybatis默认情况下只开启了一级缓存，如果需要开启二级缓存只需要在映射文件当中加入`<cache></cache>`标签即可。在那个Mapper中加入了`<cache>`，哪个Mapper就会开启二级缓存。通过这种方式开启的二级缓存的细节是默认配置的：

- 映射语句文件中的所有 select 语句的结果将会被缓存。
- 映射语句文件中的所有 insert、update 和 delete 语句会刷新缓存。
- 缓存会使用最近最少使用算法（LRU, Least Recently Used）算法来清除不需要的缓存。
- 缓存不会定时进行刷新（也就是说，没有刷新间隔）。
- 缓存会保存列表或对象（无论查询方法返回哪种）的 1024 个引用。
- 缓存会被视为读/写缓存，这意味着获取到的对象并不是共享的，可以安全地被调用者修改，而不干扰其他调用者或线程所做的潜在修改。

当然这些细节可以通过`<cache>`标签的属性来进行设置。

```xml
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```

>eviction属性：指定缓存的清除策略，可以选择的几种策略为
>
>- `LRU` – 最近最少使用：移除最长时间不被使用的对象。
>- `FIFO` – 先进先出：按对象进入缓存的顺序来移除它们。
>- `SOFT` – 软引用：基于垃圾回收器状态和软引用规则移除对象。
>- `WEAK` – 弱引用：更积极地基于垃圾收集器状态和弱引用规则移除对象。
>
>其默认的清除规则为LRU。
>
>flushInteval属性：其设定缓存的刷新间隔，若设置为0则缓存不会刷新。
>
>size属性：其指定最大可以保存的缓存条数。
>
>readOnly属性：这个属性有true和false两个可选项，若设置属性为true则Mybatis认为这条缓存是只读的，其在使用期间不会遭到修改所以Mybatis将直接将该缓存的引用交予我们，这个选项相对比较不安全但是其效率高。如果设置为false，则Mybatis会通过序列化&反序列化技术克隆一个缓存对象来交给我们，安全但是效率较低。

开启二级缓存需要注意三点：

- `<setting name="cacheEnable" value="true"></setting>`开启全局二级缓存
- mapper文件当中配置`<cache>`标签使用缓存
- 二级缓存的目标对象实现序列化接口



和缓存有关的各个属性和配置

>
>
>1. `<setting name="cacheEnabled" value="true"></cache>`如果配置为true开启二级缓存，设置为 false关闭二级缓存，其取值不会对一级缓存造成影响。默认值为true。
>2. select标签的useCache属性，默认设置为ture对当前语句查询开启二级缓存，设置false关闭二级缓存，不对一级缓存有影响。
>3. 每个增删改标签都有flushCache属性，其默认取值为ture，即每次增删改操作都会清空缓存。取值为false时不清空缓存。select标签也有这个属性，但是默认值为false，如果设置为true则每一次查询都会清空缓存，即不会有缓存存在。
>4. sqlSession.clearCache()方法只会清空当前Session当中的一级缓存。
>5. `<setting name="localCacheScope"></setting>`可选项SESSION|STATEMENT，默认为SESSION，配置为STATEMENT可以禁用一级缓存。
>
>