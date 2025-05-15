[TOC]

# Java注解

## 自定义注解的创建

```java
public @interface 注解名称{
    //注解内支持的数据类型除了8大基础类型外
    //还支持Enum,String,Class,Annotation,上述类型的一维数组
    int age();  //每个属性名称后都要有一个();
    String name() default;
    A a() default @A(name="Hari",age=1);  //使用default还能够为注解指定默认值
    
}

@interface A{
    int age();
    String name();
}
```

## 元注解

所谓元注解就是注解注解的注解，就是用来修饰注解的注解

### @Ducumented

这个元注解的作用是目标注解将会被添加到文档当中

### @Target

Target注解的作用是限定注解的作用目标

| 属性值                      | 功能                                       |
| --------------------------- | ------------------------------------------ |
| ElementType.ANNOTATION_TYPE | 可以给一个注解进行注解                     |
| ElementType.METHOD          | 可以给一个方法进行注解                     |
| ElementType.FIELD           | 可以给一个属性进行注解                     |
| ElementType.CONSTRUCTOR     | 可以给构造方法进行注解                     |
| ElementType.LOCAL_VARIABLE  | 可以给局部变量进行注解                     |
| ElementType.PACKAGE         | 可以给一个包进行注解                       |
| ElementType.PARAMETER       | 可以给一个方法内的参数进行注解             |
| ElementType.TYPE            | 可以给一个类型进行注解，比如类、接口、枚举 |

