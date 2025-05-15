@[TOC](目录)

# Spring-mvc

## HelloWorld

### Step1. 依赖包

-   spring-web
-   spring-webmvc
-   spring-core
-   spring-servlet-api

### Step2.配置web.xml

#### 配置DispatcherServlet

手动配置

web.xml下

```xml
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://java.sun.com/xml/ns/javaee"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaeehttp://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
	id="WebApp_ID" version="3.0">
	<servlet>
		<servlet-name>springDispatcherServlet</servlet-name>
		<servlet-class>
			org.springframework.web.servlet.DispatcherServlet
		</servlet-class>
        <!--
		还可以在该Servlet中配置以下标签来指定BeanConfig配置文件的路径
		如果不手动指定,则默认的BeanConfig配置文件为{servlet-name}-servlet.xml
		<init-param>
			<param-name>
				contextConfigLocation
			</param-name>
			<param-value>
				classpath:/dispatcherServlet-servlet.xml
			</param-value>
		</init-param>
		-->
		<load-on-startup>1</load-on-startup>
	</servlet>

	<servlet-mapping>
		<servlet-name>springDispatcherServlet</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>
</web-app>

```

### Step3.BeanConfig配置文件

开启包扫描

装配org.springframework.web.servlet.view.InternalResourceViewResolver

```xml
<context:component-scan base-package="com.nond.springmvc"></context:component-scan>

<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<property name="suffix" value=".jsp"></property>
	<property name="prefix" value="/WEB-INF/pages/"></property>
</bean>
```



### Step4.配置Controller

```java
@Controller
public class HelloWorld {
	
    //说明应答http://localhost:8080/projectName/HelloWorld这个请求
	@RequestMapping("/HelloWorld")
	public String helloWorld() {
		System.out.println("HelloWorld Call");
		return "success";
	}
}

```



## @RequestMapping

在SpringMVC中**RequestMapping**注解的作用是用来声明，当前控制器是用来处理哪些请求的



该注解能够标注在类、方法上，标注在类上提供初步的映射信息，标注在方法上，提供进一步的映射信息



*   method属性 

    映射对应的请求方法

    

*   params属性

    限定请求参数

    如`!param1`，请求不包含param1

    ​	`param1 != value1`，请求的param1参数值不为value1

*   headers属性

    同params属性相类似，用于限定请求的请求头





@RequestMapping的value属性支持的三种匹配符

* *：匹配多个字符
* ？：匹配一个字符
* **：匹配多层路径





## @PathVariable

通过**PathVariable**注解能够将URL中占位符参数绑定到控制器方法的入参当中

如

```java
@RequestMapping(value = "/xxx/{name}")
public String func01(@PathVariable("name")String name){
    System.out.println(name);
    return "success";
}
```

## 发出Put、Delete请求



表单

```xml
<form action="xxx" method="post">
	<input type="hidden" name="_method" value="PUT"/>
    <input type="submit" value="Put"/>
</form>
```



web.xml当中配置filter

```xml
<filter>
	<filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
	<fitler-name>HiddenHttpMethodFilter</fitler-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



## @RequestParam

获取请求参数化为入参



```java
@RequestMapping("/test")
public String func(@RequestParam(value="参数名称",required="布朗值，参数是否为必须" defalutValue="该参数为提供的情况下默认值为null，未提供参数时的默认值"))
```



## @RequestHeader

获取请求头为入参

```java
@RequestMapping("/test")
public String func(@RequestHeader(value="参数名称",required="布朗值，参数是否为必须" defalutValue="该参数为提供的情况下默认值为null，未提供参数时的默认值"))
```



## @CookieValue

获取Cookie值为入参

value属性为Cookie的名称





MVC能够通过请求参数以及控制器的入参自动的创建对象

如参数username,password,address.province,address.city

入参user



还能够一Servlet API作为入参，Spring mvc将会自动的将对应对象传入



支持的ServletAPI 

-   HttpServletRequest 
-   HttpServletResponse 
-   HttpSession 
-   java.security.Principal 
-   Locale 
-   InputStream 
-   OutputStream 
-   Reader 
-   Writer

## 在域对象中设置属性



### requestScope当中设置



#### 使用ModelAndView

使用ModelAndView需要指定视图对象，即已经包含了视图信息在内

```java
@RequestMapping("/testModelAndview")
public ModelAndView testModelAndView(){
    //传入视图名称创建ModelAndView对象
    ModelAndview modelAndView = new ModelAndView("success");
    //插入对象键值对
    modelAndView.addObject("name","Hari");
}

//在视图当中获取对象,success.jsp
${requestScope.name}
```



#### 使用Map

```java
@RequestMapping("/testMap")
public String testMap(Map<String, Object> models) {
	models.put("name", "Hari");
	return "success";
}
```

使用map在域中设置属性时，map中除了控制器方法放入的属性还有springmvc放入的在 request域中的对象

### SessionScope当中设置

#### 使用SessionAttibutes

使用@SessionAttributes标注类 

@SessionAttributes有两个属性

value:指定插入到session当中的键

type:指定插入到session当中的数据类型



```java
@SessionAttributes(value={"user"})
public class TestSessionAttribute

@RequestMapping("/testSessionAttribute")
public String testSessionAttibute(Map<String,Object> map) {
	User u = new User();
	u.setUsername("hari");
	u.setPassword("hhh123");
	map.put("user", u);
	return "success";
}
```



## @ModelAttribute



### 使用情景

  	接受部分Bean属性，保留敏感信息，确敏感信息的安全。

​	如，修改用户名和email，保留原本的登录密码





### @ModelAttribute标注后控制器目标方法的运行流程

注意:@ModelAttribute注解标记的方法，在控制器的每一个方法执行前都会调用一次



表单

```html
<form action="/spring-mvc/testModelAttribute" method="post">
oldName:
<input type="text" name="oldName"/>

<br/>
newName:
<input type="text" name="username"/>

</br>

<input type="submit"/>
</form>
```



控制器

```java
@ModelAttribute
public String getUser(@RequestParam(value="oldName",required=false,defaultValue="")String 

name,Map<String,Object> m){
	if(!"".equalss(oldName)){
		m.put("user",UserDao.queryUser(oldName));
		System.out.println("Query by old username...");
	}
}

@RequestMapping("/testModelAttribute")
public String testModelAttribute(@RequestParam(value="oldName",User user){
	UserDao.UpdateByUserName(oldName,user);
	System.out.println("Update..."+user);
}
```




页面发出请求，@ModelAttribute标注的方法先拦截到ServletRequest对象

@ModelAttribute注解标注的getUser方法根据请求参数oldName查询到User对象并且通过Map插入到请求

对象当中，键名为"user"

此时请求对象当中有

> ​	"oldName": "oldname"
> ​	"username":"newname"
> ​	"user":["username":"oldname","password":"password"]



请求对象的目标方法为@RequestMapping("testModelAttibute")标注的方法

springmvc将请求对象中的属性，按照属性名赋值给请求对象当中的"user"对象


此时请求对象
>	​	"oldName": "oldname"
>​	"username":"newname"
>​	"user":["username":"newname","password":"password"]

最后，springmvc再把键名为"user"的对象传给入参User user



​	注意:默认情况下,控制器目标方法的入参类型为User,入参对象接受对象的键名就为"user"，即

入参数据类型的首字母小写。
	如果，需要指定接口对象的键名，给入参对象使用@ModelAttribute标注,value值指定接收对象

的键名
```java
public String testModelAttribute(@RequestParam(value="oldName",@ModelAtrribute
("user") User user){
		...
	}
```



### 确定POJO类型入参的过程

1. 确定一个key
    1. 入参有无**@ModelAttribute**标注，有则**key**为注解**@ModelAttribute**指定的名称
    2. 无则为入参类型的首字母小写
2. **ImplicitModel**当中查找与key相对相应的对象
    1. 有则取出对象作为入参传入
    2. 无且控制器类有**@SessionAttributes**标注有类型或名称与key对应的对象则在Session域中取出传入，没有找到则抛出异常
    3. 如果没有**@SessionAttributes**注解，则根据反射创建对象



## 视图



### 视图解析的流程

首先需要知道无论控制器目标方法返回的是String,ModelAndView,ModelMap，springmvc都会把他们转换成ModelAndView在ModelAndView对象当中包含了Model和View



常见View的实现如下

![常用的视图实现类](./常用的视图实现类.png)



然后ModelAndView移交给视图解析器进行解析

常用的视图解析器实现如下



![常用的视图解析器实现类](./常用的视图解析器实现类.png)







### 直接转发不经过handler

```xml 
<!--如果单纯只配置view-controller,原本经过handler的转发将会出现异常-->
<mvc:view-controller path="/targetPath" view-name="success"/>
<!--如果需要原本经过handler的转发成功，需要配置annotation-driven-->
<mvc:annotation-driven></mvc:annotation-driven>
```



### 自定义视图

自定义一个视图，输出当前请求的所有请求参数



创建CustomView

```java
public class CustomView implements View {
	//视图的数据类型
	public String getContentType() {
		return "text/html";
	}
    
	//解析视图
	public void render(Map<String, ?> model, HttpServletRequest request, HttpServletResponse response)
			throws Exception {
		Writer writer = response.getWriter();
		if(model.isEmpty()) 
			writer.append("Model map empty.");
		else 
			for(Entry<String, ?> e:model.entrySet()) 
				writer.append("Model key:"+e.getKey()+"|Model value:"+e.getValue()+"<br/>");
	}

}
```





配置一个BeanNameViewResolver到上下文

```xml
<bean class="org.springframework.web.servlet.view.BeanNameViewResolver">
    <!--指定视图解析器的优先级-->
	<property name="order" value="100"></property>
</bean>
```

BeanNameViewResolver视图解析器是根据bean的名称来

访问到自己的视图

```java
	@RequestMapping("testCustomView")
	public Object testCustomView(HttpServletRequest request,Map<String,Object> m) {
		Enumeration<String> parameterNames = request.getParameterNames();
		while(parameterNames.hasMoreElements()) {
			String name = parameterNames.nextElement();
			m.put(name, request.getParameter(name));
		}
		return new CustomView();
	}
```

## 数据绑定



### 数据绑定流程

页面发出请求->ServletRequest对象

ServletRequest对象、控制器目标方法的入参对象以及入参对象的名称(默认没有@ModelAttribute指定的情况下为对象类名的首字母小写)交给WebDataBinderFactory
WebDataBinderFactory创建**DataBinder**对象

DataBinder根据上下文当中的

* **ConversionService**:进行数据转换，数据格式化
* **Validator**：进行数据合法性校验

然后根据处理结果生成BindingData对象

最后在BindingResult中抽取出入参对象和错误对象赋值给入参对象

### 自定义数据类型转换器

Spring支持三种数据转换器接口，如下

![](./Spring支持的转换器接口.png)

实现自定义类型转换器类

```java
@Component("CustomConverter")
public class CustomConverter implements Converter<String, Person> {
	
    //接受一行字符串，解析字符串为String属性name和age
	public Person convert(String source) {
		String[] strs = source.split(":");
		Person person = new Person();
		person.setName(strs[0]);
		person.setAge(Integer.parseInt(strs[1]));
		return person;
	}
}

```

要使用到自定义的类型转换器，除了实现相应接口以外，还需要

1.  配置一个ConversionServiceFactoryBean到容器中

    ```xml
    <bean class="org.springframework.context.support.ConversionServiceFactoryBean" name="ConversionService">
        <!--属性接收一个 Set,Set的元素为类型转换器Bean-->
    	<property name="converters">
    		<set>
    			<ref bean="CustomConverter"></ref>
    		</set>
    	</property>
    </bean>
    ```

    

2.  在BeanConfig中配置annotation-driven标签，指定ConversionService

    ```xml
    <mvc:annotation-driven conversion-service="ConversionService"></mvc:annotation-driven>
    ```
    
    ### 数据格式化
    
    

## 静态资源请求

​	将DispatcherServlet映射路径配置为/，spring mvc将会捕捉web容器当中所有的请求（包括静态资源请求），在这种情况下静态资源的请求如果没有对应的Controller进行处理的话，项目将会出现异常。Spring mvc针对这种情况给出了下面这种解决方案

​	在BeanConfig中配置`<mvc:default-servlet-handler/>`标签

配置该标签后，会在Spring mvc上下文中注册一个**DefaultServletHttpRequestHandler**组件，该组件会检查DispatcherServlet拦截的请求，如果是静态资源请求则交于web应用服务器进行处理，否则继续给DispatcherServlet处理





## mvc:annotation-driven标签的作用

首先有两个应用情景:

​	配置了`<mvc:view-controller path="/sucesss" view-name="success"></mvc:view-controller>`标签或`<mvc:default-servlet-handler/>`标签

配置了前者之后，Spring mvc无法判断页面的请求是不经过Handler直接转发还是通过Handler处理，导致了@RequestMapping映射的路径无法正确访问。

配置了后者之后，Spring mvc无法判断页面的请求是静态资源请求还是mvc的注解，又导致了@RequestMapping映射路径无法正确访问

但是加上了mvc:annotation-driven标签之后，前面的问题就解决了，为什么？





使用了mvc:annotation-driven后，Spring mvc默认会帮我们注册默认处理请求，参数和返回值的类，其中最主要的两个类，它们分别为HandlerMapping的实现类和HandlerAdapter的实现类

-   DefaultAnnotationHandlerMapping 

    实现类RequestMappingHandlerMapping，它会处理@RequestMapping 注解，并将其注册到请求映射表中。

    

-   AnnotationMethodHandlerAdapter 

    实现类RequestMappingHandlerAdapter，则是处理请求的适配器，确定调用哪个类的哪个方法，并且构造方法参数，返回值



当配置了mvc:annotation-driven后，Spring就知道了我们启用注解驱动。然后Spring通过context:component-scan标签的配置，会自动为我们将扫描到的@Component，@Controller，@Service，@Repository等注解标记的组件注册到工厂中，来处理我们的请求



除此之外，配置了mvc:annotation-driven后还提供一下支持

-   支持ConversionService实例对表单参数进行数据转换
-   支持NumberFormat注解、DateTimeFormat注解完成数据的格式化
-   支持使用Vaild注解对JavaBean进行JSR 303数据校验
-   支持使用RequestBody、ResonponseBody注解



## @InitBinder



InitBinder注解标注的方法可以对WebDataBinder对象进行初始化，其中WebDataBinder对象用于完成表单请求参数到JavaBean的属性绑定

通过WebDataBinder对象可以对数据绑定 的过程进行控制

InitBinder标注方法返回值为**void**

入参一般为**WebDataBinder**

```java
@InitBinder
public void InitBinder(WebDataBinder binder){
    ...
}
```

## 数据格式化

​	在一些情况下，传入的数据需要进行格式化，比如说金额、日期等。传递的日期格式为yyyy-MM-dd或者yyyy-MM-dd hh:ss:mm，这些是需要格式化的，对于金额也是如此，比如1万元人民币，在正式场合往往要写作￥10 000.00，这些都要求把字符串按照一定的格式转换为日期或者金额



为了满足这些场景的需求，Spring提供了Fomatter接口、DateTimeFormat注解和NumberFormat注解

后面两个接口可能无法满足需求，可以自定义Formatter



[自定义DateTimeFormatter](https://blog.csdn.net/xinluke/article/details/53672480)

[数据格式化](https://www.cnblogs.com/sonng/archive/2017/04/03/6661273.html#_label0)



## JSR-303数据校验

​	JSR-303 是JAVA EE 6 中的一项子规范，叫做Bean Validation，Hibernate Validator 是 Bean Validation 的参考实现 . Hibernate Validator 提供了 JSR 303 规范中所有内置 constraint 的实现，除此之外还有一些附加的 constraint。

JSR-303标准

![](.\JSR-303.png)

Hibernate Vaildator附加

![](.\HibernateVaildator.png)



Hibernate Vaildator的依赖

```xml
<!-- https://mvnrepository.com/artifact/org.hibernate/hibernate-validator -->
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>5.4.3.Final</version>
</dependency>

```

​	除了需要导入JSR-303的实现依赖以外，还需要在Spring上下文当中装配一个**LocalValidatorFactoryBean**，不过，只要在BeanConfig中配置了 mvc:annotation-driven 标签，Spring就会自动的为我们装配这个FactoryBean

​	实际使用时，只要给需要校验的类属性标注上需要的约束注解，并且给相应的控制器方法入参标注@Valid注解即可





#### 错误信息以及数据回显



首先来说数据校验过程中出现的错误对象，从数据绑定流程我们可以知道数据校验产生的错误也会传入到**BindingResult**对象当中，需要用到错误信息，添加BindingResult到控制器方法入参即可

需要注意的是，如果需要入参**BindingResult**，BindingResult只能够紧挨在@RequestBody @Valid @ModelAttribute 注解标注的实体后面

即`public String xxx(@Valid String name,BindingResult bindingResult){...}`，否则会出现错误



每个被数据校验的字段如果出现了相应的错误，这个错误信息会绑定到BindingResult对象当中，错误信息可以使用校验注解的message属性来指定

如

```java
@NotEmpty(message="内容为空")
private String content;
```

然后可以通过`List<ObjectError>BindingResult.getAllErrors()`方法获取到所有的错误对象

ObjectError.getDefaultMessage()获得错误信息







##### 数据回显

​	对于简单数据类型（如String，Integer，Float）需要回显到表单，则需要手动的把数据传入到request域中

对于POJO类型入参Spring会自动的将POJO对象传入到Request域中





## 文件上传

要使用Spring mvc提供的文件上传功能需要把MultipartResolver  bean配置到BeanConfig中

在MultipartResolver当中还能配置默认编码，最大上传文件大小等配置信息

```xml
<bean name="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
	<property name="defaultEncoding" value="utf-8"></property>
	<property name="maxUploadSize" value="1024000"/>
</bean>
```



还需要使用到commos-fileupload

```xml
	<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
	<dependency>
		<groupId>commons-fileupload</groupId>
		<artifactId>commons-fileupload</artifactId>
		<version>1.3.1</version>
	</dependency>
```
上传表单

```html
<form action="xxx" enctype="multipart/form-data" method="post">
    <input type="file" name="file"/>
    <input type="submit"/>
</form>
```

在Controller方法接收上传文件对象

```java
@RequestMapping("testFileUpload")
public String testFileUpload(@RequestParam("file")MultipartFile file){
    System.out.println(file.getOriginalName());
    System.out.println(file.getSize());
    return "success";
}
```

## 拦截器



### 自定义拦截器

自定义的拦截器类

```java
public class MyHandlerInterceptor implements HandlerInterceptor {
	/**
	 *	目标方法执行前调用
	 *	返回值决定了是否执行后续的拦截器方法和目标方法
	 *	handler对象为拦截器所拦截的方法
	 */
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {
		
		return true;
	}
	/** 
	 * 	目标方法执行后调用，但是在视图渲染之前
	 * modelAndView对象为渲染前的视图对象，程序处理后返回的ModelAndView对象，亦可为空
	 */
	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
			ModelAndView modelAndView) throws Exception {
		// TODO Auto-generated method stub

	}
	/**
	 * 	目标方法执行后，且在视图渲染之后执行
	 */
	public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
			throws Exception {
		// TODO Auto-generated method stub

	}

}
```

配置到BeanConfig中

```xml
<mvc:interceptors>
	<bean class="com.xxx.xxx.MyHandlerInterceptor"/>
</mvc:interceptors>
```

### 配置拦截器的拦截路径

在`mvc:interceptors`标签内配置子标签`mvc:interceptor`，在子标签内通过`mvc:mapping`或`mvc:exclude-mapping`子标签配置当前`mvc:interceptor`标签下所有的拦截器bean拦截与不拦截的路径

```xml
<mvc:interceptors>
    <mvc:interceptor>
        <!--拦截器拦截的路径-->
    	<mvc:mapping path="/xxx"/>
        <!--
		<mvc:exclude-mapping path="/xxx"/>
		拦截器不拦截的路径
		-->
        <bean class="xxxHandlerInterceptor"></bean>
    </mvc:interceptor>
</mvc:interceptors>
```





### 拦截器的执行顺序

先后配置有拦截器

*   **HandlerInterceptorOne**
*   **HandlerInterceptorTwo**



执行顺序为

1.  HandlerInterceptorOne#preHandle
2.  HanderInterceptorTwo#preHandle
3.  目标方法
4.  HanderInterceptorTwo#postHandle
5.  HanderInterceptorOne#postHandle
6.  渲染视图
7.  HandlerInterceptorTwo#afterCompletion
8.  HandlerInterceptorOne#afterCompletion

## 异常处理



### 拦截异常

在Controller中设置**ExceptionHandler**注解标注的控制器方法处理异常，接受参数为异常类对象列表，即目标异常类

```java
@ExceptionHandler({ArithmeticException.class})
public ModelAndView exceptionHandler(Exception ex) {
	ModelAndView mv = new ModelAndView("error");
	mv.addObject("exception", ex);
	return mv;
}
```
注意，在拦截异常方法中不能使用Map参数，如果需要传参到请求域可以返回ModelAndView对象



拦截的异常类有优先级，有限到具体的异常



### @ResponseStatus注解 

该注解标注在抛出异常的方法或自定义异常类上

接受两个参数code与reason，分别为HttpStatus状态码和异常信息



Spring mvc还有默认的异常处理类会对一些特殊的异常进行自动处理



### SimpleMappingExceptionResolver



 如果希望对所有的异常进行统一的处理可以使用这个解析器，它会将异常映射到视图



配置SimpleMappingExceptionResolver



```xml

<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
	<!--传到映射视图的异常对象参数,value值为该参数的访问名-->
    <property name="exceptionAttribute" value="ex"/>
    
    <property name="exceptionMappings">
    	<props>
            <!--key为处理的异常的全类名，value值为映射的视图-->
        	<prop key="com.nond.springmvc.exceptions.MyException">fileSizeOutOfBound</prop>
        </props>
    </property>
</bean>
```





## Spring mvc运行流程

请求发出到服务器，Spring的DispatcherServlet查找Spring mvc中是否存在相应的映射



若不存在，如果配置了mvc:default-servlet-handler则寻找目标静态资源，如果未配置则控制台输出(No mapping found for HTTP request with URI [xx/xx] in DispatcherServlet)且转到404页面



若存在则请求映射到HandlerMapping，获取HandlerExecutionChain(在HandlerExecutionChain当中有处理器对象以及拦截器对象)，然后获取HandlerAdapter对象(在其中有数据处理，视图解析等工作)，接下来就是拦截器的运行流程