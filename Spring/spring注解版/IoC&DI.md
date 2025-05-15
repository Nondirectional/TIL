@[TOC](目录)

# IOC&DI

## 组件注册



### 简单配置

配置Configuration类（主配置类)，等价于在xml方式当中配置xml配置文件

主配置类**@Configuration**注解标注



配置Bean

**@Bean**标注返回bean的方法，可以传入参数指定bean的id

如果不指定则方法名就是bena的id



**ApplicationContext**使用实现类**AnnotationConfigApplicationContext**

构造参数为配置类class对象，获取bean 依旧使用getBean方法





MainConfig.java

```java
@Configuration
public class MainConfig{
    @Bean("person")				//参数为指定bean的name
    public Person getPerson(){
        return new Person("Hari",12);
    }
}
```

MainTest.java

```java
public class MainTest{
    public void main(String args[]){
        ApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfig.class);
        Person p = (Person)ctx.getBean("person");
    }
}
```



### 组件扫描



组件注解标注，包扫描配置到容器



在配置类当中加入**@ComponentScan**注解，

属性value接受参数为字符串数组，扫描的包数组



#### 组件过滤

属性excludeFilters指定排除扫描类的过滤规则

属性includeFilters指定包含扫描类的过滤规则

​		过滤规则属性接受参数为**@Filter**数组



![](..\spring-xml\include exclude-filter过滤表达式.png)



常用两种为**annotation**和**assinable**

**includeFilters**有默认过滤规则即**自动扫描带有 @Component、@Repository、@Service 和 @Controller 的类**

如果需要完全自定义过滤规则，需要先把默认过滤规则关闭，即设置**@ComponentScan**注解的**useDefaultFilters**为**false**






以includeFilters为用例

```java
@ComponentScan(value="com.nond",
               useDefaultFilters=false,
             includeFilters={
                  @Filter(type = FilterType.ANNOTATION,classes = Service.class)  //扫描所有@Service标注的类
              	}
              )
public class MainConfig{
    ...
}
```



@Filter标签内Type为过滤类型，使用**FilterType**当中的静态常量，

annotation、assinable、custom：根据类过滤，第二个属性使用classes

aspectj、regex：根据表达式过滤，第二个属性使用pattern



组件标注照旧使用@Component、@Repository、@Service 和 @Controller



如果需要配置多种扫描策略

还可以使用@ComponentScans注解，该注解接受@ComponentScan[]数组，配置多种扫描策略





#### CUSTOM方式过滤



即使用自定义的过滤规则进行过滤

`@Filter`注解当中type使用``FilterType.CUSTOM`静态常量，classes传入自定义的匹配规则类即可





创建自定义匹配规则类

实现**TypeFilter**接口

实现接口的`public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory)`方法



通过参数**metaReader**可以获得当前扫描中的类的注解信息数据、类信息数据、类资源路径信息数据，分别使用`getAnnotationMetadata()`、`getClassMetadata()`、`getResource()`方法获取



通过**metadataReaderFactory**参数的`getMetadataReader()`方法还能够获取到其他类的`MetadataReader`



其中返回值为是否匹配成功



实例

```java
/*
自定义匹配规则类
规则为装配类名当中包含My的类到容器当中
*/

public class CustomFilter implements TypeFilter {

	public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory)
			throws IOException {
		return metadataReader.getClassMetadata().getClassName().contains("My");
	}

}



//主配置类注解
@ComponentScan(
		useDefaultFilters = false,
		includeFilters = {@Filter(type = FilterType.CUSTOM,classes = CustomFilter.class)}
		)


/**
*定义有类MyComputer,YourComputer
*/

//测试代码
@Test
public void test() {
	ApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfig.class);
	String[] beanDefinitionNames = ctx.getBeanDefinitionNames();
	for(String str:beanDefinitionNames)
	{
		System.out.println(str);
	}
}

/*输出结果
org.springframework.context.annotation.internalConfigurationAnnotationProcessor
org.springframework.context.annotation.internalAutowiredAnnotationProcessor
org.springframework.context.annotation.internalRequiredAnnotationProcessor
org.springframework.context.annotation.internalCommonAnnotationProcessor
org.springframework.context.event.internalEventListenerProcessor
org.springframework.context.event.internalEventListenerFactory
mainConfig
myComputer
*/
```





### Bean作用域



#### Bean常用有四种作用域



* `ConfigurableBeanFactory.SCOPE_PROTOTYPE`多实例

* `ConfigurableBeanFactory.SCOPE_SINGLETON`单实例
* `WebApplicationContext.SCOPE_REQUEST`请求
* `WebApplicationContext.SCOPE_SESSION`会话



#### 配置Bean的作用域



`@Scope`注解，在方法或类使用

```java
@Scope(value = ConfigurableBeanFactory.SCOPE_SINGLETON)
public class YourComputer {...}






@Scope(value = ConfigurableBeanFactory.SCOPE_SINGLETON)
@Bean
public YourComputer yourComputer(){
    return new YourComputer();
}
```







### 懒加载



单实例bean默认在容器创建时创建

多实例bean在需要取用bean时才创建



针对单实例bean，可以使用@Lazy注解标注使之懒加载，即取用bean时创建







### 条件注入



满足条件时才把bean注入到容器当中

使用**@Conditional**注解标注方法或类，根据定制的Condition条件进行判断

该注解接受一个Condition接口数组





实现Conndition接口



复写`public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata)`方法



通过方法的两个参数能够获取，运行环境信息和注解信息

match方法的返回值为 boolean，决定条件是否成立

实例

```java
//MyCondition.java
public class MyCondition implements Condition {
	public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
		return false;
	}
}


//MyComputer.java
@Component
@Conditional(value = {MyCondition.class})
public class MyComputer {...}



//MainTest.java
ApplicationContext ctx = new AnnotationConfigApplicationContext(MainConfig.class);
String[] beanDefinitionNames = ctx.getBeanDefinitionNames();
for(String str:beanDefinitionNames)
{
	System.out.println(str);
}



Output:

org.springframework.context.annotation.internalConfigurationAnnotationProcessor
org.springframework.context.annotation.internalAutowiredAnnotationProcessor
org.springframework.context.annotation.internalRequiredAnnotationProcessor
org.springframework.context.annotation.internalCommonAnnotationProcessor
org.springframework.context.event.internalEventListenerProcessor
org.springframework.context.event.internalEventListenerFactory
mainConfig

```





### 使用@Import导入bean



@Import注解的作用是将多个配置类整合到一个主配置类当中，避免所有的配置写在一个配置类



Spring4.2之前，只支持使用**@Import**注解导入配置类，后面版本支持将普通类导入并将其声明成一个bean



#### 多配置类整合

`@Import(value = {config1.class,condig2.class})`

同时使用这种方式还能导入无参构造器创建的对象





#### 使用ImportSelector接口导入

实现public String[] selectImports(AnnotationMetadata importingClassMetadata)方法

返回值为全类名列表

**@Import**注解通过全类名列表导入这些类的无参构造器创建的bean 

```java
MainConfig.java
@Import(value = {MyImportSelector.class})
@Configuration
public class MainConfig{
    ...
}

MyImportSelector.java
public class MyImportSelector implements ImportSelector {

	public String[] selectImports(AnnotationMetadata importingClassMetadata) {
		return new String[] {"com.nond.importselector.Person"};
	}

}

Person.java
public class Person{...}

```





### 使用ImportBeanDefinitionRegistrar接口

使用这种接口导入对Bean有更多的操作权限



实现**ImportBeanDefinitionRegistrar**接口

实现`public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry)`方法



创建BeanDefinition使用实现类RootBeanDefinition

调用registry.registerBeanDefinition("beanID"，BeanDefinition)



其中使用importingClassMetaData能够获取到使用Import注解标注类的类信息

```java
MyImportBeanDefinitionRegistar.java

public class MyImportBeanDefinitionRegistar implements ImportBeanDefinitionRegistrar {

	public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
		RootBeanDefinition rootBeanDefinition = new RootBeanDefinition(Person.class);
		registry.registerBeanDefinition("person", rootBeanDefinition);
	}
}
```







### FactoryBean



FactoryBean的作用是隐藏实例化一些复杂Bean的细节



FactoryBean跟普通Bean不同，其返回的对象不是指定类的一个实例，而是该FactoryBean的getObject方法所返回的对象。创建出来的对象是否属于单例由isSingleton中的返回决定。



实现FactoryBean接口

```java
public class PersonFactoryBean implements FactoryBean{

	public Object getObject() throws Exception {
		return new Person("Hari",16);
	}

	public Class getObjectType() {
		return Person.class;
	}

	/**
	 * 该方法的返回值决定使用FactoryBean创建的Bean是否是单例的
	 * true：创建的Bean为单例
	 * false:创建的Bean为多例
	 */
	public boolean isSingleton() {
		return false;
	}
	
}
```

配置到Config类

```java
	@Bean
	public PersonFactoryBean person() {
		return new PersonFactoryBean();
	}
```





如果需要获得FactoryBean本身，只需要在获取Bean的时候在id前加&即可

`ctx.getBean("&myFactoryBean")`



## 生命周期



### 初始化后与销毁前方法



给Bean指定初始化(Bean创建并且赋值完成后)和销毁(Bean销毁之前)方法

1. 在类中创建初始化和销毁方法并在**@Bean** 注解当中使用init-method属性和destory-method属性指定初始化和销毁方法

    ```java
    Person.java
    public class Person {
    	public Person() {
    		System.out.println("Construct method call...");
    	}
    	
    	public void init() {
    		System.out.println("init method call...");
    	}
    	
    	public void destory() {
    		System.out.println("destory method call...");
    	}
    }
    
    MainConfig.java
    @Configuration
    public class MainConfig {
    
    	@Bean(initMethod = "init",destroyMethod = "destory")
    	public Person person() {
    		return new Person();
    	}
    }
    
    Output:
    
    	Construct method call...
    	init method call...
    ```

    

2. Bean类实现**DisposableBean**,**InitializingBean**接口，分别对应销毁和初始化

    ```java
    Person.java
    @Component
    public class Person implements InitializingBean,DisposableBean{
    	public Person() {
    		System.out.println("Construct method call...");
    	}
    	
    	
    	public void destroy() throws Exception {
    		System.out.println("destroy() call...");
    	}
    
    	public void afterPropertiesSet() throws Exception {
    		System.out.println("afterPropertiesSet() call...");
    	}
    }
    
    MainConfig.java
    @Configuration
    @ComponentScan("com.nond.lifecycle")
    public class MainConfig {...}
    
    
    Output:
    	Construct method call...
    	afterPropertiesSet() call...
    ```

    

3. JSR250标准，使用**@PostConstruct**,**@PreDestory**注解标注bean的初始化销毁方法(需要导入JSR250依赖)

    ```java
    Person.java
    
    @Component
    public class Person{
    	public Person() {
    		System.out.println("Construct method call...");
    	}
    	
    	@PreDestroy
    	public void destroy(){
    		System.out.println("destroy() call...");
    	}
    	
    	@PostConstruct
    	public void init(){
    		System.out.println("init() call...");
    	}
    }
    
    Output:
    	Construct method call...
    	init() call...
    
    ```

    





### BeanPostProcessor处理器

**BeanPostProcessor**处理器在bean初始化方法调用前后进行处理

该处理器装配到容器当中会后，面向所有的容器当中的bean，即对每个装配到容器中的bean都进行处理



#### 创建及使用

实现PostBeanProcessor接口

实现其两个方法,两个方法的返回值都是处理后的bean

`public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException`初始化方法前

`public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException`初始化方法后



然后将其装配到容器当中



#### Bean生命周期中有关于BeanPostProcessor的流程



1. bean创建

2. bean赋值

3. postProcessBeforeInitialization()

4. bean初始化

5. postProcessAfterInitialization()



如果在postProcessBeforeInitialization返回的bean是null,流程终止，直接跳出返回null



在Spring底层当中大量的使用到了BeanProcessor，如在初始化前后，bean 属性注入，组件注入等



## 属性赋值

使用@Value标签



该标签的作用是用于给属性赋值

标签能够传入基本数据类型

能够使用Spel表达式`#{...}`

还能获取到资源文件当中的信息(运行环境的环境变量)`${...}`





### 获取properties文件属性

**@PropertySource**注解在MainConfig当中说明引入的资源文件

```java
@PropertySource("classpath:/db.properties")
```



然后在@Value标签使用`${...}`



或者用运行环境获取环境变量方式获取

```java
Environment environment = ctx.getEnvironment();
environment.getProperty("person.name");
```









## 自动装配



### @Autowired

使用@Autowired标注的属性，自动在容器当中寻找类型相匹配的bean装配



如果在容器当中，类型匹配的bean有多个，默认情况下注入的是和属性名相同的bean



还可以使用@Primary注解标注bean，说明在有多个类型匹配的bean情况下，首选使用使用该bean注入



还可以使用@Qualifier注解标注需要自动装配的bean，说明一定要注入名为**@Qualifier**注解value 值的bean



如果在容器当中没有找到bean则不需要注入，则可以使用@autowired注解的required属性，false为该属性在没有找到能够注入的bean时不注入，如果没有指定这个注解的属性，在没有找到bean的情况下会报错



@Autowired注解不止能够标注在属性上

还能够标注在构造方法上setter方法上，以及标注在方法参数上



#### 注意点1

如果被标注需要装配到容器当中的类，该类只有一个有参构造器且其中参数需要容器当中取用

则在这种情况下可以不使用**@Autowired**注解标注也能够自动装配



#### 注意点2

在主配置类当中用**@Bean**标注方法装配的bean

参数需要自动装配，这种情况下也可以不使用**@Autowired**的标注也会自动装配





除了Autowired注解以外还有@Resource(JSR250),@Inject(JSR330)

即对JCP规范的支持



### @Resource注解

​	通过设置name,type两个属性能够确定寻找bean的范围

​	name属性，寻找容器当中ID和name属性值匹配的bean，如果不存在，报错

​	type,属性，寻找容器当中类型相匹配的bean，如果不存在或者存在多个，报错



### @Inject注解

​	和autowired相比缺少了required=false功能，需要导入javax.inject包









### 自动装配Spring底层的bean



需要装配Spring底层bean的类实现xxxxxAware接口

实现接口的方法，通过其中的参数就能拿到底层的bean



通过这种方式可以拿到字符串解析器，容器等对象

这种实现接口自动装配的方法也是通过PostBeanProcessor方式实现的



给bean装配容器对象实例

```java
@Component
public class Person implements ApplicationContextAware{
	ApplicationContext ctx;

	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
		this.ctx = applicationContext;
	}
}
```





## @Profile的使用



在生产过程中有多种程序的运行环境，如开发环境，测试环境，生产环境

在多种生产环境之下需要暴露的bean也不尽相同，比如各种环境下的数据源

使用profile能够给bean指定暴露在哪种运行环境下



四种环境

* default默认
* dev开发
* test测试
* prod生产



没有使用@Profile标注的bean，在任何情况下都能够使用



### 改变运行环境



#### 方法1



设置运行参数-Dspring.profiles.active=xxx





#### 方法2



通过applicationContext设置运行环境

通过无参构造器创建一个空的applicationContext

applicationContext.getEnvironment().setProfiles(args...)能够同时设置多个运行环境

applicationContext.register(mainconfig.class)把主配置类注册进去

applicationContext.refresh()刷新applicationContext



