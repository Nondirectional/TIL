# Servlet

## 运行过程

1. Web浏览器向Web容器发出请求

2. Web容器向创建请求头(包含内容)和响应头对象(空)

3. 发送给Servlet对象,调用service方法，读取请求头中的信息，写入信息到响应头对象返回给Web容器

4. Web容器响应Web浏览器

## 类的基本结构

init方法

对Servlet对象的初始化，从Servlet创建到销毁只执行一次，即对象生成后

service方法

在接受到外部对象请求时的服务操作

destory方法

servlet对象销毁时执行的相应操作

## Servlet的抽象类和实现类

GenericServlet(抽象类)

仅包含了三个基础抽象方法

HttpServlet(实现类)

已经包含了处理相应请求的抽象方法,只需要复写即可,诸如doPost(),doGet()方法

## 将Servlet类配置到应用程序中

在web.xml文件中插入web-app标签内插入`<servlet>`标签与`<servlet-marpping>`标签

```xml
<servlet>
	<servlet-name>IndexController</servlet-name>
    <servlet-class>com.nond.controller.IndexController</servlet-class>
</servlet>

<servlet-mapping>
	<servlet-name>IndexController</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

### servlet标签

`<servlet-name>`

Servlet类的注册名称，需要`和<servlet-mapping>`标签当中的相应标签相同，以保证对应

`<servlet-class>`

值为所注册的Servlet类的完整类名，即如在包中需要把包路径写完整

`<servlet>`元素用于注册Servlet，它包含有两个主要的子元素：`<servlet-name>`和`<servlet-class>`，分别用于设置Servlet的注册名称和Servlet的完整类名。 

###  servlet-mapping标签

一个`<servlet-mapping>`元素用于映射一个已注册的Servlet的一个对外访问路径，它包含有两个子元素：`<servlet-name>`和`<url-pattern>`，分别用于指定Servlet的注册名称和Servlet的对外访问路径

`<servlet-name>`

即与`<servlet>`标签当中的`<servlet-name>`标签的值想对应

`<url-pattern>`
 这个标签当中的内容就是对外的访问路径，其根目录为当前应用程序目录即/servlet/xxx,在浏览器当中访问http://localhost:8080/ServletDemo01/servlet/xxx

多个路径能够指向同一个Servlet对象

并且url-pattern标签体当中能够使用通配符

路径为/时，表示为缺省路径，如果容器找不到其他相应的路径时都将访问缺省路径对象的Servlet对象,如404页

## ServletConfig(Servlet对象的配置信息对象)

### 配置配置对象

在web.xml文件当中，在相应的Servlet标签之中插入init-param标签

在init-param标签当中插入param-name标签，为配置项的名称

在init-param标签当中插入param-value,为配置项的内容 

这里需要注意,上述配置的初始化参数是在相应Servlet对象生成的时候,这些参数才会载入到ServletConfig对象当中

### 获得ServletConfig对象

```java
public class AServlet extends HttpServlet{
    @override
    protected void doGet(HttpServletRequest request,HttpServletResposne response){
        ServletConfig servletConfig = this.getServletConfig();
    }
}
```



### 利用ServletConfig对象获取初始化参数

使用getInitParameter方法或者 getInitParameterNames方法获得遍历对象进行遍历



## ServletContext

在每个应用程序启动的时候，容器都会为它创建一个ServletContext对象

在一个应用程序当中使用ServletContext共享数据，可以说，ServletContext的作用域就是在一个应用程序当中

### 在应用程序当中设置初始化参数 

在web.xml当中`<web-app>`内插入`<context-param>`

```xml
<web-app>
	<context-param>
    	<param-name>name</param-name>
        <param-value>value</param-value>
    </context-param>
</web-app>
```

`<param-name>`初始化参数的名称

`<param-value>`初始化参数的值


### 利用Context对象获取在web.xml文件当中配置的初始化参数 

使用ServletContext对象的`getInitParameter()`方法和`getInitParameterNames()`方法获取

### 利用ServletContext实现请求转发

1.获得ServletContext对象

2.根据ServletContext对象的getRequestDispatcher()方法获得RequestDispatcher对象

3.把request,response作为参数传递到`RequestDispatcher的forward(request,response)`方法中

```java
protected void doGet(HttpServletRequest request,HttpServletResponse resposne){
    request.getRequestDispatcher("/WEB-INF/pages/index.jsp").forward(request,resposne);
    //request.getRequestDispatcher("/WEB-INF/pages/index.jsp").include(request,resposne);
}
```




### 利用ServletContext获得项目资源文件流

`context.getResourceAsStream(path)`

### 利用装载Servlet类的装载器来加载资源文件流

使用这种方法加载文件只适用于小文件，用在大文件是极易造成jvm内存溢出

并且装载的文件只局限于java资源文件夹当中的资源

1.获得类装载器对象

 ClassLoader loader = this.getClass().getClassLoder();

2.使用装载器的方法获得文件流

 InputStream is = loader.getResourceAsStream(path);


## HttpServletResponse

### Response的OutputStream输出中文的两种方式

#### 1.使用OutputStream流设置响应头编码

```java
String str = "党的光芒照大地!";

byte[] data = str.getBytes("UTF-8");

response.setHeader("content-type","text/html;charset=UTF-8");

response.getOutputStream().write(data);
```

#### 2.使用PrintWriter流并设置页面编码，同事设置相应体编码

```java
String str = "党的光芒照大地！";

response.setCharacterEncoding("UTF-8");

PrintWriter pw = response.getWriter();

pw.println("<meta http-eqiv='content-type' content='text/html;charset=UTF-8'>");

pw.println(str);
```



#### 载文件

#####  1.配置响应头

`response.setHeader("content-disposition","attachment;filename="+fileName);`

##### 2.将需要下载的文件的数据写入到response的OutputStream

如果需要给需要下载的文件设置中文文件名的话1中的内容变为

`response.setHeader("content-disposition",attachment;filename="+URLEncoder.encode(fileName,"UTF-8"));`

## 生成验证码图片
```java
//1.创建一个图片对象 （BufferedImage）

BufferedImage img = new BufferedImage(80,20,BufferedImage.TYPE_INT_RGB);

//2.获得图片对象的画笔对象

Graphics2D g = (Graphics2D)img.getGraphics();

//3.设置画笔颜色

g.setColor(Color.WHITE);

//4.填充图片背景

g.fillRect(0,0,80,20);

//5.设置字体样式

g.setFont(new Font(null,Font.BOLD,20);

//三个参数分别是字体，字体样式，字体大小

//6.写字符串到图片

g.drawString("LLLP",0,0);

//7.将图片写入到浏览器

ImageIO.write(img.'jpg',response.getOutputStream());
```

## web工程中URL地址的推荐用法

推荐使用/开头

假如需要访问localhost/Project01/ServletA

1.如果URL地址是交给服务器使用的，或者是在容器当中使用的,/是基于当前应用程序的,即/ServletA

2.如果URL地址是交给浏览器使用的，那么/是基于webapps目录的，即/Project01/ServletA

## 两种会话

### Cookie

Cookie主要是在用户本地，浏览器把数据保存到用户本地，这些数据的生命周期是可以设置的，同时结束会话之后也是可以保证存在的

#### Cookie使用的简单流程

1. 获得当前会话对象与当前服务器保存的所有Cookie 

   Cookie[] cookies = request.getCookies();

2. 遍历Cookie列表是否已经存在有相应的Cookie

3. 获得Cookie的名称和值

   cookie.getName()

   cookie.getValue(value)

4. 创建新的Cookie

   Cookie cookie = new Cookie();

5. 设置Cookie名称和值

   cookie.setName(name);

   cookie.setValue(value);

6. 设置Cookie的生命周期 

   cookie.setMaxAge(time);

7. 设置Cookie的作用域

   cookie.setPath("/Project01")

   当用户访问Project01目录下的资源的时候才是带Cookie的

8. 写入Cookie

   respsonse.addCookie(cookie);

### Session

#### 和Cookie的区别

Cookie是服务器写给客户端当中的，是在客户端你的

Session是服务器写到服务器当中的

#### 实现原理

服务器端把SesssionId用Cookie写入到客户端当中

服务器端保存相应数据到相应SessionId对应的Session中

当客户端再次访问时,带着它的SessionId给到服务器端，服务器端就知道了这个的Session是哪一个

#### 在客户端禁用Cookie时的解决方案

https://www.cnblogs.com/xdp-gacl/p/3855702.html

#### 销毁

##### 自动销毁设置

默认的自动销毁时30分钟

在web.xml当中插入
```xml

<session-config>

<session-timeout>分钟</sesssion-timeout>

</sesssion-config>
```

##### 手动销毁

`Session.invalidate();`

会话的概念是用户在访问服务器的这个过程，如打开浏览器访问浏览器

Cookie和Session就是为了在用户和服务器会话的过程中为了保护与用户交互数据的技术