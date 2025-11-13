# Spring

## BeanFactory是什么？

`BeanFactory`就是Spring的核心容器。

`ApplicationContext`继承自`BeanFactory`，既实际上`ApplicationContext`也是一个`BeanFactory`。

## BeanFacotyr能够用来干嘛？

表面上来看BeanFactory只能够用来获取Bean，但是实际上控制反转、依赖注入以及Bean生命周期中的各种功能都是通过BeanFactory的实现类来提供的。

`DefaultListableBeanFactory`的类图:

![DefaultListableBeanFactory类图](https://nondirectional.oss-cn-heyuan.aliyuncs.com/picgo/1705501275770.png)

关注父类`DefaultSingletonBeanRegistry`的成员变量`singletonObjects`，单例Bean都存在于这个Map下。   

## ApplicationContext除了是一个BeanFactory它还有哪些功能？

![ApplicationContext类图](https://nondirectional.oss-cn-heyuan.aliyuncs.com/picgo/1705501280041.png)

主要集中在四个父接口

- `MessageSource`：处理国际化资源

- `ResourcePatternResolver`：使用通配符匹配资源（类路径、文件路径下的资源）

- `EnvironmentCapable`：发布事件对象（观察者模式同步调用，）

- `ApplicationEventPublisher`：处理系统环境信息 
