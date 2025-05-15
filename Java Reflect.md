# Java的反射

## Class对象

Class对象包含了类型的所有信息，我们要创建的所有实例都需要通过Class对象，这是因为我们创建实例需要的信息都在Class对象当中

在运行期，我们想要创建某个类的对象的时候首先就要检查那个类的.class文件有没有加载到JVM虚拟机当中，如果虚拟机当中没有那个类的Class对象，那么虚拟机就会在路径下寻找跟类同名的.class文件，加载到虚拟机当中，如果Class对象进入到了我内存当中，就会把所有这个类型的对象创建出来

可以说，所有得对象都是其对应Class对象那个的实例

##  对于`Class.forName(className)`的理解

上面对Class对象的描述当中说了，如果要使用到类的时候虚拟机就会把所需要的Class类对应的.class对象加载到虚拟机当中

而`Class.forName()`就是一种手动将类加载到虚拟机当中的方法

`Class.forName()`方法返回一个Class对象

##  使用Class对象创建实例

### 使用无参构造方法创建实例

使用Class对象创建实例用到了`Class.newInstance()`方法,使用这个方法的时候需要注意，如果需要创建的类没有无参构造方法的时候使用`Class.newInstance()`方法将会抛出一个异常

```java
Person p = Person.class.newInstance();
```

### 附带参数的构造方法创建实例

1. 使用Class类的`getDeclaredConstructor()`方法获得构造方法对象
2. 调用构造方法对象的`newInstance()`方法并提供相应的参数

```java
Person p = Person.class.getDeclaredConstructor(String.class, int.class).newInstance("Hari",16);
```



## 使用反射的方式调用对象的方法 

1. 获得方法的Method对象
2. 调用Method对象的`invoke()`方法

```java
Person p = Person.class.getDeclaredConstructor(String.class, int.class).newInstance("Hari",16);
Method setName = Person.class.getDeclaredMethod("setName",String.class);
setName.invoke(p,"Mari");
```

## 使用反射的方式修改字段

通过这种方式甚至能够访问private访问修饰符修饰的字段

1. 获得Field对象
2. 如果需要访问的是private字段，先调用Field对象的`setAccessable(boolean flag)`方法设置字段为可被访问
3. 最后调用Field的`set(Object obj,Object arg);`

```java
Person p = Person.class.getDeclaredConstructor(String.class, int.class).newInstance("Hari",16);
Field f = Person.class.getDeclaredField("name");
f.setAccessable(true);
f.set(p,"Mari");
```


## 使用Class对象获取所有的Class的成员信息

Class当中的成员主要有Constructor（构造方法）、Method（成员方法）、Field（字段）、Annotate（注解）

分别可以使用

1. `xxx.class.getDeclaredConstructors();`
2. `xxx.class.getDeclaredMethods();`
3. `xxx.class.getDeclaredFields();`
4. `xxx.class.getDeclaredAnnotates();`

来获取类的全部成员信息

还有一种方法`xxx.class.getXXXs()`使用这种方法获取的信息是所有public成员的，不包括其他private,protected修饰的成员

## ClassLoader（类加载器）

虚拟机装载.class文件到虚拟机的时候就会使用到ClassLoader

ClassLoader可以分为三种，
1. Bootstrap ClassLoader（使用C++编写）
2. Extension ClassLoader
3. App ClassLoader

其中Bootstrap ClassLoader是由本地本地代码实现的，它的任务就是把Java核心的API加载到虚拟机当中，其中就包括了Extension ClassLoader

Extension ClassLoader称为扩展类加载器，负责加载Java的扩展类库，默认加载JAVA_HOME/jre/lib/ext/目下的所有jar包

App ClassLoader称为系统类加载器，负责加载应用程序classpath目录下的所有jar和class文件

## 通过反射获取泛型信息

假如说有有一泛型类A<T>，它有非常多的如B,C,D等子类，如果我们拿到了B类的向上转型为A类的对象，我们该如何确认B的泛型类型呢？

`A a = new B();` 也就是说，我们只有a这个对象的时候该如何确认它的泛型类型呢？

假定有

```java
abstract class A<T>{}
class B extends A<String>{}
class C extends A<Integer>{}

public class Main{
    public static void main(String args[]){
        A a = new B();
    }
}
```

1. `a.getClass().getGenericSuperclass()`

   ​	通过这个方法返回对象的的toString方法就能个可以知道当前的对象以及泛型类型的信息，但是这个方法返回的对象只有toString方法，只能够粗略的得到一个字符串。

2. 使用上述方法获得对象的子接口的getActualTypeArguments方法得到一个包含所有泛型类型的Type数组

   ```java
   ParameterizedType parameterizedType = (ParameterizedType)a.getClass().getGenericSuperclass();
   Type[] types = parameterizedType.getActualTypeArguments();
   for(Type t:types){
       System.out.println(t);
   }
   ```

   



##  动态代理

动态代理涉及到一个InvocationHandler接口和Proxy对象

简单来说，动态代理就是讲一个对象包装成一个新的对象

创建一个Proxy对象，`Proxy.newProxyInstance(ClassLoader,Interfaces,InvocationHandler)`

需要三个参数，ClassLoader为原对象的类加载器,Interfaces为原对象的所有接口对象，InvocationHandler为实现了InvocationHandler接口的类的对象

ClassLoader为加载原对象类的类加载器

Interfaces为代理对象需要实现的接口

InvocationHandler就是动态代理当中的代理，用来指派方法调用的调用处理程序

### 动态代理的流程

如果我们调用了Proxy的方法,就会调用InvocationHandler的invoke方法，同时把Proxy对象、Proxy所执行的方法的Method实例、所执行方法的参数列表传给InvocationHandler的invoke方法

在执行invoke方法体的内容后，返回Method方法调用后的返回值

```java
import java.lang.reflect.*;

//这里就是原对象的接口，只有接口当中的方法才会被InvocationHandler监听
interface Human{
    public void say();
}

class Person implements Human{
    public void say(){
        System.out.println("Hello World!");
    }
}

class PersonProxyInvocationHandler implements InvocationHandler{
    private Object target_object = null;
    
    PersonProxyInvocationHandler(Object obj){
        this.target_object = obj;
    }
    
    public Human getProxy(){
        return (Human)Proxy.newProxyInstance(target_object.getClass().getClassLoader(),target_object.getClass().getInterfaces(),this);
    }
    
    @Override
    public Object invoke(Object porxy,Method method,Object[] args){
        //传入的三个参数
        //proxy代理对象
        //method监听到的方法
        //args方法的参数列表
        System.out.println("invoke before");
		Object obj = null;
		try{
			obj = method.invoke(target_object,args);
		}catch(Exception e){
			System.out.println(e.toString());
		}
        System.out.println("invoke late");
        return obj;
    }
}

public class Main{
    public static void main(String args[]){
		try{
			Person real_person = new Person();
			PersonProxyInvocationHandler personProxyInvocationHandler = new PersonProxyInvocationHandler(real_person);
			Human person_proxy = personProxyInvocationHandler.getProxy();
			person_proxy.say();
		}catch(Exception e){
			System.out.println(e.toString());
		}
    }
}
```



需要注意，在动态代理的invoke方法内部，一般不使用参数当中的proxy对象

如果使用proxy对象调用方法，调用方法又会触发invoke方法，进入到无限循环当中