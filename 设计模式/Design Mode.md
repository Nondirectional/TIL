# 设计模式

## 1. 设计模式的七大原则

### 1.1 单一职责原则

​    单一职责原则在类的层面上来说就是一个类只负责一项职责，这是因为在一个类负责多个职责的时有一项职责出现变更，其变更可能会对另外其他职责造成影响。

​    使用单一职责元的目的是降低类的复杂度，提高类的可读性和可维护性，并降低变更所带来的风险。

​    需要注意的是通常情况下我们都应该遵守单一职责原则，除非在逻辑足够简单时可以在代码级别上违反单一职责原则，在类中的方法足够少时可以在方法级别上违反但一直指责原则。

### 1.2 接口隔离原则

​    接口隔离原则即**类对类的依赖应该建立在最小接口上**。

​    首先我们要知道什么是建立在接口上的类对类的依赖。

![image-20201018232223541](.\建立在接口上的类对类的依赖.png)

```java
// 建立在接口上的类对类的依赖

interface I{
    void m1();
    void m2();
    void m3();
}

class C implements I{
    void m1(){System.out.println("call m1().");}
    void m2(){System.out.println("call m2().");}
    void m3(){System.out.println("call m3().");}
}
class D implements I{
    void m1(){System.out.println("call m1().");}
    void m2(){System.out.println("call m2().");}
    void m3(){System.out.println("call m3().");}
}


public class A{
    void run(Interface o){
        o.m1();
        o.m2();
    }
}
public class B{
    void run(Interface o){
        o.m1();
        o.m3();
    }
}
```

​    那么什么是建立在最小接口上呢？那上面的代码举个例子：类A通过接口I依赖类C但是A只用到了I中的m1和m2方法，如果要满足接口隔离原则那么就要建立在最小接口上就需要对接口I进行进一步的分拆。

​    分拆之后的UML类图如下

![image-20201018232940149](.\分拆后符合接口隔离原则的实例.png)

### 1.3 依赖倒置原则

​    高层模块不应该依赖低层模块，两者都应该依赖抽象。抽象不应该依赖细节，细节应该依赖抽象。依赖倒置原则的核心就是这两句话，具体的中心思想就是面向接口编程。

​    细节总是多变的，以抽象为基础是比以细节为基础稳定的多。使用接口或抽象类制定好规范，然后把细节的展现交给实现类去完成。

​    原先的系统结构是这样的，人通过两个不同的驾驶方法类去驾驶载具，当新增驾驶载具时就需要新增驾驶方法类，并且客户端代码要做出修改。

`person.drive(DriveCar dc);`

![image-20201019142518919](.\不符合依赖倒置原则.png)

​    使用依赖倒置原则进行修改之后则相较于上面的方式稳定，客户端代码不需要做出修改，这样系统的扩展性会更高。

`person.drive(Drive d);`

![image-20201019142320875](.\符合依赖倒置原则.png)

### 1.4 里式替换原则

1. 里氏替换原则通俗的来讲就是：子类可以扩展父类的功能，但不能改变父类原有的功能。

2. 里氏代换原则告诉我们，在软件中将一个基类对象替换成它的子类对象，程序将不会产生任何错误和异常，反过来则不成立，如果一个软件实体使用的是一个子类对象的话，那么它不一定能够使用基类对象。

3. 里氏代换原则是实现开闭原则的重要方式之一，由于使用基类对象的地方都可以使用子类对象，因此在程序中尽量使用基类类型来对对象进行定义，而在运行时再确定其子类类型，用子类对象来替换父类对象。

### 1.5 开闭原则

​    开闭原则的中心思想是：软件中的对象（类，模块，函数等等）应该对于扩展是开放的，但是对于修改是封闭的。其中的扩展是对软件实体的扩展，并且扩展面向的是提供方，封闭面向的是使用方。 

​    当软件需要变化时，尽量通过扩展软件实体的行为来实现变化，而不是通过修改已有的代码来实现变化。

### 1.6 迪米特法则

​    迪米特法则也称最少知道原则，也就是说对依赖的类知道的越少越好，对于被依赖的类尽量将所有逻辑封装在方法内，对外只提供public方法。

​    迪米特法则也可以简单说成：只和直接的朋友进行交流。其中直接的朋友就是出现在成员变量，方法参数，方法返回值的类，陌生的就是指那些以局部变量的方式出现在类的内部的类，

### 1.7 合成复用原则

​    合成复用原则要求在软件复用时，要尽量先使用组合或者聚合等关联关系来实现，其次才考虑使用继承关系来实现。如果要使用继承关系，则必须严格遵循里氏替换原则。合成复用原则同里氏替换原则相辅相成的，两者都是开闭原则的具体实现规范。

​    合成复用原则是通过将已有的对象纳入新对象中，作为新对象的成员对象来实现的，新对象可以调用已有对象的功能，从而达到复用。

### 1.8 七大原则小结

​    首先我们的系统是要根据抽象架构的，在系统发生变化是通过扩展实体的方式来实现变化，扩展面对提供方修改对使用方封闭。

​    以抽象为基础使用接口或抽象类制定好规范，使用实现类来扩展细节。在接口上要满足接口隔离原则，类对类的依赖应该建立在最小接口上，如果使用的接口有冗余应该讲冗余的部分拆分组合。

​    类的结构上应该满足单一职责原则即一个类只负责一项职责，以及里式替换原则子类可以扩展父类的功能，但不能改变父类原有的功能，如果子类对父类功能的过度改变，在软件中将一个基类对象替换成它的子类对象，程序将不会产生任何错误和异常，反过来则不成立，这样的情况是我们不愿意看到的。

​    然后是开发使用合成复用原则提高开发效率。

## 2. 创建性设计模式

​    创建性设计模式是把实例化过程进行抽象化，以使一个系统可以从创建、组合和表示对象的过程中独立出来。一个类的创建性模式可使用继承改变被实例化的类，而一个对象创建性模式将实例化委托给另一个对象。

### 2.1 单例模式

​    单例模式是设计模式中最简单的形式之一。这一模式的目的是使得类的一个对象成为系统中的唯一实例。要实现这一点，可以从客户端对其进行实例化开始。因此需要用一种只允许生成对象类的唯一实例的机制，“阻止”所有想要生成对象的访问。

​    单例模式使用的场景：

1. 需要频繁创建和销毁的对象。
2. 创建耗时多或占用资源多但又频繁使用到的对象。

#### 2.1.1 饿汉式

​    使用饿汉式加载的特点是在类加载的时候Instance就已经被创建并且这种创建方式是没有进程同步问题的，但是饿汉式方式带来的问题是类加载的情况是不定的，譬如说`Singleton.class`这行语句也会造成类的加载，如果程序自始至终都没有用到Instance并且加载了这个类就会造成内存空间的浪费。

```java
class Singleton{
    private Singleton(){}

    private final static Singleton instance = new Singleton();

    public static Singleton getInstance(){
        return instance;
    }
}
```

#### 2.1.2 懒汉式

​    懒汉式加载的特点是在需要使用到Instance的时候才创建实例对象，但是在多线程工作环境下就会有线程同步问题，可能会早成实例对象被创建多个造成内存空间的浪费。

```java
class Singleton{
    private Singleton(){}

    private static Singleton instance;

    public static Singleton getInstance(){
        if(instance == null)
            instance = new Singleton();
        return instance
    }
}
```

#### 2.1.3 懒汉式(同步方法)

​    这种方法避免了线程同步问题的发生，但是每次调用`getInstance()`方法都会进行同步在效率上有浪费。

```java
class Singleton{
    private Singleton(){}

    private static Singleton instance;

    public static synchronized Singleton getInstance(){
        if(instance == null)
            instance = new Singleton();
        return instance
    }
}
```

#### 2.1.4 懒汉式(同步代码块)

​    需要注意这种方法实现单例模式是错误的，这种方式并不能解决线程同步问题。假设有两条线程，两者都进入到了同步代码块，这个时候两条线程都判断`instance`对象是空的，在第一条线程进入到同步代码块创建了对象之后，第二条线程会再次创建实例对象。

```java
class Singleton{
    private Singleton(){}

    private static Singleton instance;

    public static Singleton getInstance(){
        if(instance == null)
            synchronized(Singleton.class){
                instance = new Singleton();
            }
        return instance;
    }
}
```

#### 2.1.5 双重检查

​    这里需要先解释一下**volatile**关键字：当把变量声明为volatile类型后，编译器与运行时都会注意到这个变量是共享的，因此不会将该变量上的操作与其他内存操作一起重排序（指CPU采用了允许将多条指令不按程序规定的顺序分开发送给各相应电路单元处理）。volatile变量不会被缓存在寄存器或者对其他处理器不可见的地方，因此在读取volatile类型的变量时总会返回最新写入的值。

​    使用了同步代码块，在同步代码块内再检查一次`instance`对象是否为空并且对`instance`对象使用了**volatile**关键字确保创建的实例对象实时的写入到主内存当中。

```java
class Singleton{
    private Singleton(){}

    private volatile static Singleton instance;

    public static Singleton getInstance(){
        if(instance == null)
            synchronized(Singleton.class){
                if(instance == null)
                    instance = new Singleton();
            }
        return instance;
    }
}
```

#### 2.1.6 静态内部类

​    在`Singleton`类加载的时候其静态内部类`SingletonLoader`并不会一同加载，在使用到`getInstance()`方法的时候`SingletonLoader`才会被加载并且创建实例化`instance`对象。这样静态加载不存在线程同步问题。

```java
class Singleton{
    private Singleton(){}

    private static class SingletonLoader{
        static Singleton instance = new Singleton();
    }

    public static Singleton getInstance(){
        return SingletonLoader.instance;
    }
}
```

#### 2.1.7 枚举

​        因为枚举的特性是没有构造方法，其只会有有限个已经定义的实例如下文代码中就只有一个INSTANCE实例。同时避免了上述的两种问题。

```java
enum Singleton{
    INSTANCE;
}
```

### 2.2 工厂模式

​    工厂，顾名思义就是生产产品，该模式用于封装和管理对象的创建，是一种创建型模式。

#### 2.2.1 简单工厂模式

​    这种工厂模式对对象的创建和管理相对比较简单，只是对各个产品的创建做了简单的封装。通过传递类型来向工厂指定生产哪种对象。

![工厂模式的 UML 图](.\简单工厂图示.png)

#### 2.2.2 工厂方法模式

​    和简单工厂模式中工厂负责生产所有产品相比，工厂方法模式将生成具体产品的任务分发给具体的产品工厂。

![image-20201020013622838](.\工厂方法图示.png)

#### 2.2.3 抽象工厂模式

​    上面两种工厂方法都是针对一种类型产品的，如果需要生产多种类型产品是就没有办法进行扩展。这个时候就使用到抽象工厂模式。抽象工厂模式是围绕一个超级工厂创建其他工厂。该超级工厂又称为其他工厂的工厂。其他工厂生产该工厂族的产品。

​    抽象工厂可以对工厂族进行扩展，但前提是超级工厂需要有调用其他工厂生产的接口。

![抽象工厂模式的 UML 图](.\抽象工厂类图示意.png)

### 2.3 原型模式

​    原型模式是用于创建重复的对象，同时又能保证性能。这种模式是实现了一个原型接口（比如说Java中的Clone），该接口用于创建当前对象的克隆。当直接创建对象的代价比较大时，则采用这种模式。例如，一个对象需要在一个高代价的数据库操作之后被创建。我们可以缓存该对象，在下一个请求时返回它的克隆，在需要的时候更新数据库，以此来减少数据库调用。

![image-20201020025535789](.\原型模式UML示意.png)

#### 2.3.1 基本实现

```java
public class Main{
    public static void main(String args[]){
        Sheep sheep = new Sheep("Hari");
        Sheep clone_sheep = sheep.clone();
        System.out.println("prototype sheep hashcode:"+sheep.hashCode());
        System.out.println("clone sheep hashcode:"+clone_sheep.hashCode());
    }
}

class Sheep implements Cloneable{
    public String name;

    Sheep(String name){
        this.name = name;
    }

    @Override
    public Sheep clone(){
         Sheep clone = null;
      try {
         clone = (Sheep)super.clone();
      } catch (CloneNotSupportedException e) {
         e.printStackTrace();
      }
      return clone;
    }
}
```

#### 2.3.2 浅拷贝和深拷贝

​    通过基本实现当中代码（即使用`super.clone()`）方式创建克隆对象进行的浅拷贝。那么什么是浅拷贝呢？

​    Object类的clone方法只会拷贝对象中的基本的数据类型（基本类型byte,char,short,int,long,float,double，boolean），对于数组、容器对象、引用对象等都不会进行拷贝将原型当中的上述成员的引用复制过来。使用这种方式进行拷贝称为浅拷贝。如果要实现深拷贝，必须将原型模式中的数组、容器对象、引用对象等另行拷贝。

​    实现深拷贝有两种方案：

1. 重写clone方法实现深拷贝
2. 通过对象序列化实现深拷贝

​    使用重写clone方法实现深拷贝这里就不举例说明了，具体要说的是序列化实现深拷贝，在便捷性这方面来说序列化肯定是优于前者的。

```java
import java.io.*;

public class Main{
    public static void main(String args[]){
        Sheep sheep = new Sheep("Hari",new Sheep("Mari"));
        Sheep clone_sheep = sheep.deepClone();
        System.out.println("prototype sheep friend hashcode:"+sheep.friend.hashCode());
        System.out.println("clone sheep friend hashcode:"+clone_sheep.friend.hashCode());
    }
}


//以序列化方式进行克隆的目标类实现Serializable接口
class Sheep implements Serializable{
    public String name;
    public Sheep friend;

    Sheep(String name){
        this.name = name;
    }

    Sheep(String name,Sheep friend){
        this.name = name;
        this.friend = friend;
    }

    public Sheep deepClone(){
        ByteArrayOutputStream bos = null;
        ObjectOutputStream oos = null;
        ByteArrayInputStream bis = null;
        ObjectInputStream ois = null;
        Sheep clone = null;
        try{
            //对象序列化,将对象转化为字节数组写到字节数组流中
            bos = new ByteArrayOutputStream();
            oos = new ObjectOutputStream(bos);
            oos.writeObject(this);

          //对象反序列化，将字节数组流当中的字节数组读出转化为对象
            bis = new ByteArrayInputStream(bos.toByteArray());
            ois = new ObjectInputStream(bis);
            clone = (Sheep)ois.readObject();
        }catch(Exception e){
            System.out.println(e.toString());
        }finally{
            try{
                bos.close();
                oos.close();
                bis.close();
                ois.close();
            }catch(Exception e){
                System.out.println(e.toString());
            }
        }
        return clone;
    }
}
```

### 2.4 生成器模式

​    生成器模式的作用是把一个复杂对象的构建和它的表示分离，是的同样的构建过程可以创建不同的类。

![建造者模式的结构图](.\生成器模式结构图.png)

​    从结构图可以看出，生成器模式当中有四个角色

    1. 抽象建造者：为创建一个Product对象的各个部件指定抽象接口，通常还有一个返回复杂产品的方法。
     2. 具体建造者：构建复杂产品的各个部件的具体建造者。
     3. 产品：包含多个组成部件的复杂对象，组成部件由具体建造者创建。
     4. 指挥者：调用建造者的部件构造和部件装配方法完成复杂对象的构建。

## 3. 结构性设计模式

### 3.1 适配器模式

​    适配器模式的目的是将一个类的结构转换成用户希望的另外一个接口。适配器模式使得原本由于接口不兼容而不能一起工作的那些类能够一起工作。

​    那我们生活中的电源适配器作为例子，我们日常生活中的标准电压是220V，但是给电脑供电或者是给手机供电的工作电压都不同于标准电压，这个时候就需要电源适配器将标准电压转换成给设备供电需要的工作电压。

​    其中需要转换的源接口就是标准电压，目标接口就是设备的供电电压，适配器的工作就是讲标准电压转换成设备的供电电压。

![image-20201020222553636](.\适配器举例.png)

​    根据实现方式不同，在java环境下适配器模式分为三种：

1. 类适配器模式
2. 对象适配器模式
3. 接口适配器模式

#### 3.1.1 类适配器模式

​    类适配器模式的结构如下：

![](.\类适配器模式.png)

​    适配器通过继承220V类，实现5V接口的转换方法向Phone提供5V电压。

```java
public class Main{
    public static void main(String args[]){
        Phone p = new Phone();
        p.charging(new VoltageAdapter().provideWorkVoltage());
    }
}

class StandardVoltage{
    private int voltage = 220;

    public int provideStandardVoltage(){
        return voltage;
    }
}

interface WorkVoltage{
    public int provideWorkVoltage();
}

class VoltageAdapter extends StandardVoltage implements WorkVoltage{
    @Override
    public int provideWorkVoltage(){
        int workVoltage = provideStandardVoltage()/44;
        return workVoltage;
    }
}

class Phone{
    public void charging(int voltage){
        System.out.println("Charging in "+voltage+"V...");
    }
}
```

​    对于Java、C#等不支持多重继承的语言，一次最多只能适配一个适配者类，而且目标抽象类只能为接口，不能为类，其使用有一定的局限性，不能将一个适配者类和他的子类同时适配到目标接口。

#### 3.1.2 对象适配器模式

​    对象适配器模式和类适配器模式不同点就是把类适配器模式当中的适配器和源类的继承关系变为持有关系。

![image-20201020225359015](.\对象适配器模式.png)

```java
public class Main{
    public static void main(String args[]){
        Phone p = new Phone();
        p.charging(new VoltageAdapter().provideWorkVoltage());
    }
}

class StandardVoltage{
    private int voltage = 220;

    public int provideStandardVoltage(){
        return voltage;
    }
}

interface workVoltage{
    public int provideWorkVoltage();
}

class VoltageAdapter implements workVoltage{
    private StandardVoltage src = new StandardVoltage();

    @Override
    public int provideWorkVoltage(){
        int workVoltage = src.provideStandardVoltage()/44;
        return workVoltage;
    }
}

class Phone{
    public void charging(int voltage){
        System.out.println("Charging in "+voltage+"V...");
    }
}
```

#### 3.1.3 接口适配器模式

​    假设适配器接口有提供5V、10V、20V这三种方法，如果要使用到适配器的任意一种方法都需要把三个方法都实现了。那么有没有办法实现一个接口方法就能够使用这个适配器？

​    接口适配器模式就能解决这个问题，

![image-20201020230927122](.\接口适配器模式.png)

​    接口适配器模式用一个抽象类对各个接口方法进行空实现，方法的实现交个各个实现类（可以使用匿名内部类的方式创建并实现接口方法）。从接口图可以看到，抽象适配器和对象适配器模式的适配器一样持有源类对象。

```java
public class Main{
    public static void main(String args[]){
        Phone p = new Phone();
        p.charging(new AbstractAdapter(){
            @Override
            public int provide10Voltage(){
                int voltage = getSrc().provideStandardVoltage()/22;
                return voltage;
            }
        }.provide10Voltage());
    }
}

class StandardVoltage{
    private int voltage = 220;

    public int provideStandardVoltage(){
        return this.voltage;
    }
}

interface workVoltage{
    public int provide5Voltage();
    public int provide10Voltage();
    public int provide20Voltage();
}

abstract class AbstractAdapter implements workVoltage{
    private StandardVoltage src = new StandardVoltage();

    public StandardVoltage getSrc(){
        return this.src;
    }

    @Override
    public int provide5Voltage(){return 0;}
    @Override
    public int provide10Voltage(){return 0;}
    @Override
    public int provide20Voltage(){return 0;}
}

class Phone{
    public void charging(int voltage){
        System.out.println("Charging in "+voltage+"V...");
    }
}
```

### 3.2 桥接模式

​    桥接模式：将抽象部分与它的实现部分分离，使它们都可以独立地变化。

​    桥接模式结构图：

![img](.\桥接模式结构图.png)

​    举个例子：假设对水彩笔的大小和颜色建模，按大小分为大号笔和小号笔，按颜色分为红绿蓝三种颜色。一般情况下它的结构是这样的：

![image-20201021020449766](.\桥接模式-普通结构.png)

​    如果在这种多层继承结构下要对水彩笔的大小种类或是颜色种类进行添加势必会引起实现类的大量增加，造成类结构臃肿。在这种情况下是否能够让设计系统下的类更少，并且让其扩展更为方便？

​    对于水彩笔我们可以将它分拆成两个维度：大小、颜色。然后将继承关系转换成关联关系：

![image-20201021021657561](.\桥接模式-桥接结构.png)

​    转换成这种结构之后，我们要对水彩笔的抽象部分（大小）或是实现部分（颜色）进行扩展变得更加灵活。

### 3.3 装饰者模式

​    [装饰者模式](https://commonwealths/of-fanruice/p/11565679.html)就先转一篇博客吧，并且mark一本书**《Head First 设计模式》**。博客就是根据这本书写的，确实很好理解。

### 3.4 组合模式

​    组合模式：有时又叫作部分-整体模式，它是一种将对象组合成树状的层次结构的模式，用来表示“部分-整体”的关系，使用户对单个对象和组合对象具有一致的访问性。    

​    还记不记得讲uml类图的组合关系时的举的例子：窗口和窗口内的按钮、文字框等组件之间的关系就是组成关系，即窗口关闭时按钮、文字框等组件也将消失。

​    组合模式就是类似窗口和组件组合关系的一种模式。其结构图如下：

![安全式的组合模式的结构图](.\组合模式结构图.png)

​    其中树叶构件为不可再扩展的组件，树枝构件为能够容纳其他组件的组件。对结构的实现如下:

```java
import java.util.*;

public class Main{
    public static void main(String args[]){
        Composite top = new Composite("top");
        top.add(new Leef("Leef1 parent->top"));
        Composite c1 = new Composite("Composite1 parent-top");
        c1.add(new Leef("Leef2 parent-Composite1"));
        c1.add(new Leef("Leef3 parent-Composite1"));
        top.add(c1);

        top.show();
    }
}

interface Comment{
    public void show();
    public String getName();
}

class Composite implements Comment{
    private String name;

    public String getName(){
        return this.name;
    }

    Composite(String name){
        this.name = name;
    }


    ArrayList<Comment> childen = new ArrayList<Comment>();

    public void add(Comment child){
        this.childen.add(child);
    }

    public void remove(Comment child){
        this.childen.remove(child);
    }

    public Comment getComment(int i){
        return this.childen.get(i);
    }

    public void show(){
        System.out.println(this.getName());
        for(Comment c:childen){
            c.show();
        }
    }
}

class Leef implements Comment{
    private String name;

    public String getName(){
        return this.name;
    }

    Leef(String name){
        this.name = name;
    }

    public void show(){
        System.out.println(name+"...");
    }
}
```

### 3.5 外观模式

​    外观模式：为子系统中的一组接口提供一个一致的界面，此模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。

​    其结构如下：

![image-20201021035334712](.\外观模式结构图.png)

​    我们拿基金来举个例子，基金是交由基金经理管理的股票集合。拿外观模式来类比基金，Client就是购买基金的人，Facade就是基金经理，而Facade下的各个子系统就是基金管理的各个股票。有了基金后，就变成众多用户只和基金打交道，关心基金的上涨和下跌，而实际上的操作确是基金经理与股票和其它投资产品打交道。

### 3.6 享元模式

​    享元模式：运用共享技术有效地支持大量细粒度的对象。结构图如下：

![img](.\享元模式结构图.png)

​    **内部状态**指对象共享出来的信息，存储在享元对象内部并且不会随环境的改变而改变；**外部状态**指对象得以依赖的一个标记，是随环境改变而改变的、不可共享的状态，外部状态作为参数传入方法中，改变方法的行为，但是并不改变对象的内部状态。。

​    Flyweight是抽象享元角色。它是产品的抽象类，同时定义出对象的外部状态和内部状态（外部状态及内部状态相关内容见后方）的接口或实现；ConcreteFlyweight是具体享元角色，是具体的产品类，实现抽象角色定义的业务；UnsharedConcreteFlyweight是不可共享的享元角色，一般不会出现在享元工厂中；FlyweightFactory是享元工厂，它用于构造一个池容器，同时提供从池中获得对象的方法。

​    举个例子：假设有一个围棋棋盘，用户可以在棋盘上落黑子和白子。如果每在棋盘上落一个子都创建一个创建一个棋子对象，这样需要创建的棋子是非常多的。我们分析一下，发现棋子有颜色以及在棋盘上的位置这几个属性。无论用户怎样下棋棋子永远都只有黑色和白色两种颜色，就此我们可以确定这两个属性是内部状态（对象共享的信息），但是棋子的落点往往都是不同的，所以落点这个属性是外部属性（对象不可共享的属性，是对象依赖的标记）。

```java
//抽象享元角色类
public interface Flyweight {
    //一个示意性方法，参数state是外部状态
    public void operation(String state);
}

//具体享元角色类
//具体享元角色类ConcreteFlyweight有一个内部状态，在本例中一个Character类型的intrinsicState属性代表，它的值应当在享元对象
//被创建时赋予。所有的内部状态在对象创建之后，就不会再改变了。如果一个享元对象有外部状态的话，所有的外部状态都必须存储在客户端，
//在使用享元对象时，再由客户端传入享元对象。这里只有一个外部状态，operation()方法的参数state就是由外部传入的外蕴状态。
public class ConcreteFlyweight implements Flyweight {
    private Character intrinsicState = null;
    /**
     * 构造函数，内蕴部态作为参数传入
     * @param state
     */
    public ConcreteFlyweight(Character state){
        this.intrinsicState = state;
    }


    /**
     * 外部状态作为参数传入方法中，改变方法的行为，
     * 但是并不改变对象的内部状态。
     */
    @Override
    public void operation(String state) {
        // TODO Auto-generated method stub
        System.out.println("Intrinsic State = " + this.intrinsicState);
        System.out.println("Extrinsic State = " + state);
    }

}


//享元工厂角色类
//享元工厂角色类，必须指出的是，客户端不可以直接将具体享元类实例化，而必须通过一个工厂对象，利用一个factory()方法得到享元对象。
//一般而言，享元工厂对象在整个系统中只有一个，因此也可以使用单例模式。

//当客户端需要单纯享元对象的时候，需要调用享元工厂的factory()方法，并传入所需的单纯享元对象的内部状态，由工厂方法产生所需要的
//享元对象。
public class FlyweightFactory {
    private Map<Character,Flyweight> files = new HashMap<Character,Flyweight>();

    public Flyweight factory(Character state){
        //先从缓存中查找对象
        Flyweight fly = files.get(state);
        if(fly == null){
            //如果对象不存在则创建一个新的Flyweight对象
            fly = new ConcreteFlyweight(state);
            //把这个新的Flyweight对象添加到缓存中
            files.put(state, fly);
        }
        return fly;
    }
}


//客户端类
public class Client {

    public static void main(String[] args) {
        // TODO Auto-generated method stub
        FlyweightFactory factory = new FlyweightFactory();
        Flyweight fly = factory.factory(new Character('a'));
        fly.operation("First Call");

        fly = factory.factory(new Character('b'));
        fly.operation("Second Call");

        fly = factory.factory(new Character('a'));
        fly.operation("Third Call");
    }

}
```

### 3.7 代理模式

​     代理模式：代理模式给某一个对象提供一个代理对象，并由代理对象控制对原对象的引用。

​    通俗的来讲代理模式就是我们生活中常见的中介。假如有人需要出售一套房产，但是出售一套房产往往需要对房源进行推广寻找买家包括带有意向的客户看房等等操作，如果不想要进行这些操作我们就可以把房产委托给中介出售让我们卖房能够忽略上面这一系列的过程，而只要签订卖房合同和收钱就可以了。

​    先给出代理模式的结构：

![img](.\代理模式结构图.jpg)

​    其中Client是买房的客户，而RealSubject是卖房的客户，Proxy是中介，而Subject是卖房客户和中介都实现的接口。Proxy其实并不是真正意义上的卖房，卖房的核心（房产产权移交）其实还是交由RealSubject来进行，Proxy真正做的是对卖房客户卖房行为的一系列包装(进行推广寻找买家包括带有意向的客户看房等等操作)。下面用代码来举例说明。

```java
public class Main{
    public static void main(String args[]){
        HouseOwner ho = new HouseOwner();
        SellProxy sp = new SellProxy(ho);
        sp.sell();
    }
}

interface SellHouse{
    public void sell();
}

class HouseOwner implements SellHouse{
    public void sell(){
        System.out.println("房主移交了产权...");
    }
}
class SellProxy{
    private SellHouse houseOwner;
    SellProxy(SellHouse houseOwner){
        this.houseOwner = houseOwner;
    }

    public void sell(){
        System.out.println("中介进行了房源推广找来了有意向买房的客户...");
        this.houseOwner.sell();
        System.out.println("房主收到了房款并向中介支付了房款2%的中介费...");
    }
}
/*Output:
*    中介进行了房源推广找来了有意向买房的客户...
*    房主移交了产权...
*    房主收到了房款并向中介支付了房款2%的中介费...
*/
```

​    根据代理方式的不同由把代理模式分为两种：

1. 静态代理
2. 动态代理

#### 3.7.1 静态代理

​    静态代理是由程序员创建或特定工具自动生成源代码，在对其编译。在程序员运行之前，代理类.class文件就已经被创建了。

​    上面用到介绍代理模式原理用到的程序使用的就是静态代理。

​    静态代理也有利有弊：

​    优点：可以做到在符合开闭原则的情况下对目标对象进行功能扩展。

​    缺点：我们得为每一个服务都得创建代理类，工作量太大，不易管理。同时接口一旦发生改变，代理类也得相应修改。

#### 3.7.2 动态代理

​    动态代理是在程序运行时通过反射机制动态创建的。在动态代理中我们不再需要再手动的创建代理类，我们只需要编写一个动态处理器就可以了。真正的代理对象由JDK再运行时为我们动态的来创建。

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class Main{
    public static void main(String args[]){
        SellHouse ho = new HouseOwner();
        SellHouse sp = (SellHouse) Proxy.newProxyInstance(
            SellHouse.class.getClassLoader(), 
            new Class[]{SellHouse.class}, 
            new SellProxy(ho));
        sp.sell();
    }
}

interface SellHouse{
    public void sell();
}

class HouseOwner implements SellHouse{
    public void sell(){
        System.out.println("房主移交了产权...");
    }
}

class SellProxy implements InvocationHandler{
    private Object houseOwner;

    SellProxy(Object houseOwner){
        this.houseOwner = houseOwner;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("中介进行了房源推广找来了有意向买房的客户...");
        Object result = method.invoke(houseOwner,args);
        System.out.println("房主收到了房款并向中介支付了房款2%的中介费...");
        return result;
    }
}
```

## 4 行为性设计模式

### 4.1 责任链模式

​    责任链模式中包含包含一些命令对象和处理对象。处理对象会通过指针形成链式结构（责任链），命名对象经过责任链上的处理对象一个个处理得到最终的处理结果。责任链模式的结构如下：

![责任链模式的结构图](.\责任链模式结构.png)

​    责任链是一条由具体处理者对象链接在一起的对象集合，当客户对象提交请求给一个开始对象这个请求将会一一的传递给所有的处理对象。举个例子实现说明：

​    假设某个公司职员要请求请假请假天数在三天以下小组长同意即可，请假天数在3-7天需要小组长和部门经理同意，请假天数在7天以上需要小组长、部门经理以及总经理同意。可以知道请假天数在3-7天或7天以上都要交小组长同意以及上级审批。对这个问题建模就是一个责任链模式的实现。

```java
public class Main{
    public static void main(String args[]){

    }
}
```

### 4.2 模板模式

​    定义一个操作中算法的骨架，而将一些步骤延迟到子类中，模板方法使得子类可以不改变算法的结构即可重定义该算法的某些特定步骤。简单来说就是办某些事有固定的几个步骤，但是因为具体的事件不同导致某几个步骤要具体怎么做是不确定的。模板模式就是把固定的步骤流程抽象到抽象类中，具体步骤操作细节交给实现类实现。

​    举个例子，我们去银行办理业务，固定的流程是：取号->办业务->给服务评分，其中取号和给服务评分这两个步骤是固定的可以直接在父类总实现。整个流程也是固定的，而办业务这个步骤因为有不同的业务，是不固定的。

​    结构图如下：

![img](.\模板模式结构图.png)

### 4.3 命令模式

​    命令模式：将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化，对请求排队或记录请求日志。以及支持可撤销的操作。命令模式结构图如下：

<img src=".\命令模式结构图.png" alt="img" style="zoom:150%;" />

​    拿万能遥控器举个例子：有一个万能遥控器，能够对家里的所有的电器进行遥控。对万能遥控器进行建模：

​    遥控器：担任Invoker的角色，是命令的发起者。

​    遥控命令：担任ConcreteCommand的角色，调用Receiver执行操作完成命令。

​    电器：担任Receiver的角色，是命令的具体执行者，是最后完成各项操作的人。

命令对象可以把行动及参数封装起来，于是这些行动可以被：

- 重复多次
- 取消
- 恢复
- 排队

### 4.4 访问者模式

​    访问者模式：将作用于某种数据结构中的各元素的操作分离出来封装成独立的类，使其在不改变数据结构的前提下可以添加作用于这些元素的新的操作，为数据结构中的每个元素提供多种访问方式。它将对数据的操作与数据结构进行分离，是行为类模式中最复杂的一种模式。

​    访问者（Visitor）模式是一种对象行为型模式，其主要优点如下：

1. 扩展性好。能够在不修改对象结构中的元素的情况下，为对象结构中的元素添加新的功能。
2. 复用性好。可以通过访问者来定义整个对象结构通用的功能，从而提高系统的复用程度。
3. 灵活性好。访问者模式将数据结构与作用于结构上的操作解耦，使得操作集合可相对自由地演化而不影响系统的数据结构。
4. 符合单一职责原则。访问者模式把相关的行为封装在一起，构成一个访问者，使每一个访问者的功能都比较单一。

访问者（Visitor）模式的主要缺点如下：

1. 增加新的元素类很困难。在访问者模式中，每增加一个新的元素类，都要在每一个具体访问者类中增加相应的具体操作，这违背了“开闭原则”。
2. 破坏封装。访问者模式中具体元素对访问者公布细节，这破坏了对象的封装性。
3. 违反了依赖倒置原则。访问者模式依赖了具体类，而没有依赖抽象类。

其结构图如下。

![访问者（Visitor）模式的结构图](.\访问者模式结构.png)

​    我们来举个例子理解这个模式。小明是公司的一名销售员，假设他有两个属性本月考勤和本月销售额，在公司里人事部门关注他的考勤问题，他的部门主管关注他的销售额问题。如果要考虑类的单一职责问题，销售员这个数据结构主要负责的他的销售职责，考勤问题交给人事部关系，销售额问题交给老板关心。这两个信息人事部和老板都用访问者的身份来访问。

​    对这个问题的实现如下：

```java
public class Main{
    public static void main(String args[]){
        Salesperson s1 = new Salesperson("XiaoMing",26,100000);
        Salesperson s2 = new Salesperson("XiaohHuang",24,150000);
        Salesperson s3 = new Salesperson("XiaohHua",26,80000);
        PersonnelDepartment pd = new PersonnelDepartment();
        Boss b = new Boss();

        Sales s = new Sales();
        s.add(s1);
        s.add(s2);
        s.add(s3);
        s.accept(pd);
        s.accept(b);
        /*
        output:
        人事部查看了销售员XiaoMing的考勤天数:26天
        人事部查看了销售员XiaohHuang的考勤天数:24天
        人事部查看了销售员XiaohHua的考勤天数:26天
        老板查看了销售员XiaoMing的销售额:100000元
        老板查看了销售员XiaohHuang的销售额:150000元
        老板查看了销售员XiaohHua的销售额:80000元
        */
    }
}

class Sales{
    ArrayList<Salesperson> sales = new ArrayList<Salesperson>();

    public void add(Salesperson s){
        sales.add(s);
    }

    public accept(Manger m){
        for(Salesperson s:sales){
            s.accept(m);
        }
    }
}
interface Employee{
    public void accept(Manage m);
}

class Salesperson implements employee{
    String name;
    int attendanceDays;
    int salesValue; 

    public Salesperson(String name,int attendanceDays,int salesValue){
        this.name = name;
        this.attendanceDays = attendanceDays;
        this.salesValue = salesValue;
    }
    public void accept(Manage m){
        m.visitor(this);
    }
}

interface Manager{
    public void visit(Salesperson s);
}

class Boss implements Manger{
    public void visit(Salesperson s){
        System.out.println("老板查看了销售员"+s.name+"的销售额:"+s.salesValue+"元");
    }
}

class PersonnelDepartment implements Manager{
    public void visit(Salesperson s){
        System.out.println("人事部查看了销售员"+s.name+"的考勤天数:"+s.attendanceDays+"天");
    }
}
```
