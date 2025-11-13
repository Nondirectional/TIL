# Object transport
>在Java当中除了基本数据类型是按值传递的外，其他都是按引用传递的，如String传递的就是句柄

> 在很多情况下传递句柄是缺乏安全性的，在取得句柄之后就意味着能够对数据进行操作了，

> 如果我们不想要这样的情况发生，该怎么做呢？

## 1.克隆对象
> 创建一个和原对象数据相同的对象，这样对这个对象操作便不会因想到原对象了

克隆对象有两种途径

 (1) clone()方法.

 (2) 创建副本构建器

## 2.创建只读类
把类设计成能够读取，而不能改变

---
## 复写clone()方法:

> 想要具有克隆能力，类必须实现Cloneable接口，这个接口是空的，但是可以看成是一个标志是否具有克隆能力的标记

>浅度克隆，既不能保证某些非基本数据类型能够正常克隆

 1) 浅度克隆

> 访问修饰符改为public，把对象数据复制到新的克隆对象，方法体内`return (typeName)super.clone();`
 另外,克隆方法法都抛出`ClonenNotSupportedException`，需要对此进行捕捉，但是只要类实现了克隆能力标记接口，这个异常就永远不会被抛出


 2) 深度克隆
> 在浅度克隆的基础上把那些无法确保能否正确克隆的成员对象手动克隆，坑更新到克隆对象

例:
```
public Person clone(){
  Person p = (Person)super.clone();
  p.setPet(this.p.clone());
  reutrn p;
}
```

---
## 副本构建器
> 其实和克隆方法是非常的相似的，具体实现就是给方法提供模板，根据这个末班创建一个相同的对象后返回（用构造方法）

例:
```
Person(Person p){
  this.name = p.getName();
  this.age = p.getAge();
  this.pet = new Pet(p.getPet);
}

Pet(Pet p){
  this.name = p.getName();
}
```
