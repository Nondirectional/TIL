# Mongo DB

## 基本概念

MongoDB 是由C++语言编写的，是一个基于分布式文件存储的开源数据库系统。

在高负载的情况下，添加更多的节点，可以保证服务器性能。

MongoDB 旨在为WEB应用提供可扩展的高性能数据存储解决方案。

MongoDB 将数据存储为一个文档，数据结构由键值(key=>value)对组成。MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组。![](https://nondirectional.oss-cn-heyuan.aliyuncs.com/picgo/image-20210518170555440.png)

下面通过对比来看一下SQL和MongoDB的区别：

| SQL术语/概念    | MongoDB术语/概念 | 解释/说明                   |
| ----------- | ------------ | ----------------------- |
| database    | database     | 数据库                     |
| table       | collection   | 数据库表/集合                 |
| row         | document     | 数据记录行/文档                |
| column      | field        | 数据字段/域                  |
| index       | index        | 索引                      |
| table joins |              | 表连接,MongoDB不支持          |
| primary key | primary key  | 主键,MongoDB自动将_id字段设置为主键 |

<img title="" src="https://nondirectional.oss-cn-heyuan.aliyuncs.com/picgo/01.png" alt="" style="zoom:150%;">

## 常用指令

```sql
#Help查看命令提示 
db.help();

#切换/创建数据库
#如果数据库不存在，则创建数据库，否则切换到指定数据库
#创建的数据库需要在向文档插入记录以后才会被show dbs命令查询到
use test

#查询所有数据库 
show dbs;

#删除当前使用数据库 
db.dropDatabase();

#查看当前使用的数据库 
db.getName();

#显示当前db状态 
db.stats();

# 当前db版本 
db.version();

# 查看当前db的链接机器地址 
db.getMongo〇;
```

## 集合

集合就是 MongoDB 文档组，类似于 RDBMS （关系数据库管理系统：Relational Database Management System)中的表格。

集合存在于数据库中，集合没有固定的结构，这意味着你在对集合可以插入不同格式和类型的数据，但通常情况下我们插入集合的数据都会有一定的关联性。

常用命令：

> 创建一个集合：
> 
> （table)  db.createCollection(  "collName");  
> 
>  得到指定名称的集合（table )：
> 
>   db.getCollection("user");  

## 文档

文档是一组键值(key-value)对(即BSON)。MongoDB 的文档不需要设置相同的字段，并且相同的字段不需要相同的数据类型，这与关系型数据库有很大的区别，也是 MongoDB 非常突出的特点。

下表列出了 RDBMS 与 MongoDB 对应的术语：

| RDBMS | MongoDB                      |
| ----- | ---------------------------- |
| 数据库   | 数据库                          |
| 表格    | 集合                           |
| 行     | 文档                           |
| 列     | 字段                           |
| 表联合   | 嵌入文档                         |
| 主键    | 主键 (MongoDB 提供了  key 为 _id ) |

**需要注意的是：**

1、文档中的键/值对是有序的。

2、文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)。

3、MongoDB区分类型和大小写。

4、MongoDB的文档不能有重复的键。

5、文档的键是字符串。除了少数例外情况，键可以使用任意UTF-8字符。

## 数据类型

下表为MongoDB中常用的几种数据类型：

| 数据类型               | 描述                                                          |
| ------------------ | ----------------------------------------------------------- |
| String             | 字符串。存储数据常用的数据类型。在 MongoDB 中，UTF-8 编码的字符串才是合法的。              |
| Integer            | 整型数值。用于存储数值。根据你所采用的服务器，可分为 32 位或 64 位。                      |
| Boolean            | 布尔值。用于存储布尔值（真/假）。                                           |
| Double             | 双精度浮点值。用于存储浮点值。                                             |
| Min/Max keys       | 将一个值与 BSON（二进制的  JSON）元素的最低值和最高值相对比。                        |
| Arrays             | 用于将数组或列表或多个值存储为一个键。                                         |
| Timestamp          | 时间戳。记录文档修改或添加的具体时间。                                         |
| Object             | 用于内嵌文档。                                                     |
| Null               | 用于创建空值。                                                     |
| Symbol             | 符号。该数据类型基本上等同于字符串类型，但不同的是，它一般用于采用特殊符号类型的语言。                 |
| Date               | 日期时间。用 UNIX 时间格式来存储当前日期或时间。你可以指定自己的日期时间：创建 Date 对象，传入年月日信息。 |
| Object ID          | 对象 ID。用于创建文档的  ID。                                          |
| Binary Data        | 二进制数据。用于存储二进制数据。                                            |
| Code               | 代码类型。用于在文档中存储 JavaScript 代码。                                |
| Regular expression | 正则表达式类型。用于存储正则表达式。                                          |

## 基本操作

### 增删改查

#### 增

插入语法：

```sql
db.collection(集合名).insertone (
    {key1:value1, key2:value2}
);

db.collection(集合名).insertMany(
    {key1:value1, key2:value2},
    {key1:value1, key2:value2}
);
```

运行实例：

![](https://nondirectional.oss-cn-heyuan.aliyuncs.com/picgo/image-20210518172838422.png)

成功插入以后会返回一个ObjectId，Mongo DB默认创建_id行作为该集合的主键，并且其值自动生成，值为字符串类型。

#### 删

```sql
# 根据id删除行
db.collection.remove(id);

# 删除所有行
db.collection.remove({});
```

#### 改

语法格式：

```sql
db.collection.update(    
    <query>, 
    <update>, 
    {       
        upsert: <boolean>,   
        multi: <boolean>,  
        writeConcern: <document>
    }
)
```

参数说明：

- **query** : update的查询条件，类似sql update查询内where后面的。
- **update** : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
- **upsert** : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
- **multi** : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
- **writeConcern** :可选，抛出异常的级别。

实例：

```sql
db.Perosn.update(
    {name:"Hari"},
    {$set:{name:"Harry"}},
    {
        upsert:false,
        multi:false
    }
)
```

#### 查

1、WHERE

```sql
# select * from User where name =  'zhangsan'  
> db.User.find({name:"zhangsan"})  
```

2、FIELDS

```sql
# select name, age from User where age  = 21  
> db.User.find({age:21},  {'name':1, 'age':1})  
```

3、SORT

在 MongoDB 中使用 sort() 方法对数据进行排序，sort() 方法可以通过参数指定排序的字段，并使用 1 和 -1 来指定排序的方式，其中 1 为升序排列，而 -1 是用于降序排列。

```sql
# select * from User order by age  
> db.User.find().sort({age:1})  
```

4、SUCE

在 MongoDB 中使用 limit()方法来读取指定数量的数据，skip()方法来跳过指定数量的数据

```sql
# select * from User skip 2 limit 3  
> db.User.find().skip(0).limit(3)  
```

5、IN

```sql
# select * from User where age in (21,  26, 32)  
> db.User.find({age:{$in:[21,26,32]}})  
```

6、COUNT

```sql
 # select count(*) from User where age  >20  
 >  db.User.find({age:{$gt:20}}).count()  
```

7、0R

```sql
# select * from User where age = 21 or  age = 28  
>  db.User.find({$or:[{age:21}, {age:28}]})  
```

#### 聚合表达式

| **表达式**   | **描述**                  | **实例**                                                                                   |
| --------- | ----------------------- | ---------------------------------------------------------------------------------------- |
| $sum      | 计算总和。                   | `db.mycol.aggregate([{$group  : {_id : "$by_user", num_tutorial : {$sum : "$likes"}}}])` |
| $avg      | 计算平均值                   | `db.mycol.aggregate([{$group  : {_id : "$by_user", num_tutorial : {$avg : "$likes"}}}])` |
| $min      | 获取集合中所有文档对应值得最小值。       | `db.mycol.aggregate([{$group  : {_id : "$by_user", num_tutorial : {$min : "$likes"}}}])` |
| $max      | 获取集合中所有文档对应值得最大值。       | `db.mycol.aggregate([{$group  : {_id : "$by_user", num_tutorial : {$max : "$likes"}}}])` |
| $push     | 在结果文档中插入值到一个数组中。        | `db.mycol.aggregate([{$group  : {_id : "$by_user", url : {$push: "$url"}}}])`            |
| $addToSet | 在结果文档中插入值到一个数组中，但不创建副本。 | `db.mycol.aggregate([{$group  : {_id : "$by_user", url : {$addToSet : "$url"}}}])`       |
| $first    | 根据资源文档的排序获取第一个文档数据。     | `db.mycol.aggregate([{$group  : {_id : "$by_user", first_url : {$first : "$url"}}}])`    |
| $last     | 根据资源文档的排序获取最后一个文档数据     | `db.mycol.aggregate([{$group  : {_id : "$by_user", last_url : {$last : "$url"}}}])`      |

### 索引

索引通常能够极大的提高查询的效率，如果没有索引，MongoDB在读取数据时必须扫描集合中的每个文件并选取那些符合查询条件的记录。

这种扫描全集合的查询效率是非常低的，特别在处理大量的数据时，查询可以要花费几十秒甚至几分钟，这对网站的性能是非常致命的。

索引是特殊的数据结构，索引存储在一个易于遍历读取的数据集合中，索引是对数据库表中一列或多列的值进行排序的一种结构。

  `db.User.createIndex({"name":1})  `

语法中 name值为你要创建的索引字段，1 为指定按升序创建索引，如果你想按降序来创建索引指定为 -1 即可

## Spring Boot中集成MongoDB

添加依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

相关配置：

```properties
# MongoDB配置
# host 地址
spring.data.mongodb.host=localhost
# 默认数据库端口 27017
spring.data.mongodb.port=27017
# 连接数据库名
spring.data.mongodb.database=test
```

## 基于MongoTemplate操作MongoDB

```java
@Autowired
MongoTemplate mongoTemplate;

//插入文档
@Test
public void insertPerson() {
    Person person = new Person();
    person.setName("Zoey");
    person.setAge(20);
    person.setSex(false);
    mongoTemplate.insert(person);
}
//查询所有
@Test
public void findAll(){
    List<Person> all = mongoTemplate.findAll(Person.class);
    for(Person p:all)
        System.out.println(p);
}

//根据id查询
@Test
public void findById(){
    Person person = mongoTemplate.findById("60a380f26f7c0a6d5eb7fb78", Person.class);
    System.out.println(person);
}

//条件查询
@Test
public void findByCondition(){
    Query query = new Query(Criteria.where("age").gt(18).and("sex").is(true));
    List<Person> personList = mongoTemplate.find(query, Person.class);
    for(Person p:personList)
        System.out.println(p);
}

//模糊查询
@Test
public void findUsersLikeName() {
    String name = "i";
    String regex = String.format("%s%s%s", "^.*", name, ".*$");
    Pattern pattern = Pattern.compile(regex, Pattern.CASE_INSENSITIVE);
    Query query = new Query(Criteria.where("name").regex(pattern));
    List<Person> personList = mongoTemplate.find(query, Person.class);
    for(Person p:personList)
        System.out.println(p);
}

//分页查询
@Test
public void findPersonPage(){
    int current = 1;
    int size = 2;
    Query query = new Query();
    // 每页2个
    Pageable pageable = PageRequest.of(current-1, size); // get 2 profiles on a page
    query.with(pageable);
    // 排序
    query.with(Sort.by(
            Sort.Order.asc("name")
    ));
    // 查询总数
    int count = (int) mongoTemplate.count(query, Person.class, "Person");
    List<Person> personList = mongoTemplate.find(query, Person.class, "Person");
    for (Person p : personList)
        System.out.println(p);
    System.out.println("Page:"+current+"/"+count);
}

//修改
@Test
public void updatePerson(){
    Update update = new Update();
    update.set("name","Harry");
    UpdateResult updateResult = mongoTemplate.updateFirst(new Query(Criteria.where("name").is("Hari")), update, Person.class);
    System.out.println(updateResult);
}

//删除
@Test
public void removePerson(){
    DeleteResult deleteResult = mongoTemplate.remove(new Query(Criteria.where("name").is("Li Si")),Person.class);
    System.out.println(deleteResult);
}
```