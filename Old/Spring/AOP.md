 

# AOP（面向切面编程）



## 概念

​	把多个业务流程的公共部分抽取成一个独立的切面，进行统一管理，在合适的时机横向的把切面切入到业务流程的指定位置当中。



举例说明


在一些项目当中，有很多的业务流程都要用户登录之后才能使用。

​	使用OOP想要解决就是把登录验证模块插入到每一个业务流程当中，并且每一个业务流程需要的登录验证还可能是不统一的。

​	使用AOP只需要把登录验证模块单独抽取出来统一管理，在需要用到的位置声明需要用到登录验证模块，把登录验证的切面插入到这一切点当中。



## AOP术语

* 切面：横切关注点(跨越应用程序多个模块的功能)被模块化的特殊对象，即把公共功能独立出来的特殊对象
* 通知：切面需要完成的工作，即想要实现的功能，切面里的每一个方法都称作为一个通知
* 连接点：程序执行的某个位置，可以认为是Spring允许使用通知的地方，如方法执行前、执行后、抛出异常
* 切点：AOP 通过切点定位到特定的连接点。类比连接点相当于数据库中的记录，切点相当于查询条件。切点和连接点不是一对一的关系，一个切点匹配多个连接点
* 代理：向目标对象通知后生成的对象







## 基于注解



### 使用条件

Spring本身也有自己的AOP，但是普遍都在使用AspectJ，反正基本上都在用。



* 导入Spring-AOP及AspectJ依赖jar包

    略

* xml配置文件当中引入aop命名空间

* 在`ApplicationContext.xml`当中加入`<aop:aspcetj-autoproxy>`自动代理

* 每一个切面都是一个IoC容器当中的bean, 都需要用``@Component`等注解标注装配到容器当中

    

    声明切面

    ```java
    @Aspect
    @Component
    public class TestAspect{
        //
    }
    ```

    



### 通知

​	上文当中已经提到了通知的含义,需要知道所有的通知都是在切面对象当中的方法



​	在除环绕通知外的所有通知当中都可以指定一个**JoinPoint**对象用来获得目标方法的信息(非必须)，而环绕通知必须包含**ProceedingJoinPoin**t参数,如果不包含这个参数将无法调用目标方法，Object为返回值



#### 前置通知

前置通知使用``@Before`注解，`@Before`中的参数为切点表达式，**可以使用`*`占位符**

```java
@Before("execution(* com.nond.demo01.Calc.*(int,int))")
public void beforeAdvice(JoinPoint point) {
	System.out.println("Method:"+point.getSignature().getName()+" begin,With:"+Arrays.asList(point.getArgs()));
}
```



#### 后置通知

​	需要注意后置通知是无论代码是否抛出异常都会执行后置通知的代码，也即后置通知是在连接点完成的会后执行的，但是在我实际的测试当中，后置通知的打印语句是先于返回通知和异常通知执行的

​	前置通知可以获取到方法的入参值，但是后置通知不能获取到该方法执行完后的结果，需要在返回通知里面去获取结果值

```java
@After("execution(* com.nond.demo01.Calc.*(int,int))")
public void afterAdvice(JoinPoint point) {
	System.out.println("Method:"+point.getSignature().getName()+" end,With:"+Arrays.asList(point.getArgs()));
}
```



#### 返回通知

在返回通知中，注解需要指定两个参数

* **value**：切点表达式

* **returning**：目标方法的返回值名称，指定该名称之后可以在通知方法的参数列表当中设置相同名称的参数。该参数的值就是目标方法的返回值



```java
@AfterReturning(value="execution(* com.nond.demo01.Calc.*(int,int))",returning="result")
public void returningAdvice(JoinPoint point,Object result) {
	System.out.println("Method:"+point.getSignature().getName()+" returning,With:"+Arrays.asList(point.getArgs())+",Result="+result);
}
```



#### 异常通知

在异常通知中，注解需要指定两个参数

- **value**：切点表达式

- **throwing**：在目标方法当中捕捉到的异常对象，指定该名称之后可以在通知方法的参数列表当中设置相同名称的参数。该参数就是捕捉到的异常对象



```java
@AfterThrowing(value="execution(* com.nond.demo01.Calc.*(int,int))",throwing="e")
public void throwingAdvice(JoinPoint point,Throwable e) {
	System.out.println("Method:"+point.getSignature().getName()+" returning,With:"+Arrays.asList(point.getArgs())+",Throwing="+t);
}
```



#### 环绕通知

* 环绕通知用`@Around`注解标注，注解内的参数为切点表达式

* catch捕捉`Throwable`,即`point.proceed()`抛出`java.lang.Throwable`

* 环绕通知就是一套完整的动态代理，在环绕通知内能够加入前置通知、后置通知等所有的通知



```java
@Around("execution(...)")
public Object arroundAdvice(ProceedingJoinPoint point)throws Throwable{
	Object result = null;
	try{
		System.out.println("前置通知");
		result = point.proceed();
		System.out.println("返回通知，返回值:"+result);
	}catch(Throwable e){
		System.out.println("异常通知，异常:"+e);
		throw e;
	}
	System.out.println("后置通知");
	return result;
}
```



### 切面优先级

​	当有多个切面同时作用于同一个连接点时，如果没有指定切面的优先级会出现优先级混乱的情况，即哪一个切面优先是不确定的

​	可以使用两种方法给切面指定优先级

1. 实现**Ordered**接口，其中`getOrder()`方法返回值越小则切片的优先级越高
2. 使用`@Ordered`，指定优先级，参数越小则切片优先级越高



### 切点表达式复用

​	在一个切面当中多个通知共同使用一个切点时，可以吧切点表达式单独抽取出来复用

```java
@Aspect
@Component
public class TestAspect{
    @PointCut("execution(* com.nond.demo01.Calc.*(int,int)))
    public publicPointCut(){}
    
    @Before("publicPointCut()")
    public beforeAdvice(JoinPoint point){
        ...
    }
              
    @After("publicPointCut()")
    public afterAdvice(JoinPoint point){
        ...
    }
}
```





## 基于XML

使用XML配置AOP的思想与使用注解配合基本相同

使用注解方式需要配置的内容在xml方式都要配置



### 装配切面bean

```xml
<!--切面bean-->
<bean name="loggerAspect" class="com.nond.demo01.LoggerAspect"</bean>
<!--目标对象-->
<bean name="calc" class="com.nond.demo01.Calc"></bean>
```



### 配置`<aop:cofig>`

在`<aop:config>`内配置切点、连接点和通知

```xml
<aop:config>
    <!--配置切点，指定切点表达式和id-->
	<aop:pointcut expression="execution(* com.nond.demo01.Calc.*(int,int))" id="pointcut"></aop:pointcut>
    
    <!--配置连接点及通知
	其中使用<aop-aspect>的order属性能够指定相应切面的优先级
	-->
    <aop:aspect ref="loggerAspect" order="1">
    	<aop:before method="beforeAdvice" pointcut-ref="pointcut"/>
		<aop:after method="afterAdvice" pointcut-ref="pointcut"/>
		<aop:after-returning method="returningAdvice" returning="result" pointcut-ref="pointcut"/>
		<aop:after-throwing method="throwingAdvice" throwing="t" pointcut-ref="pointcut"/>
    </aop:aspect>
</aop:config>
```



