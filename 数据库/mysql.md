

#  MySQL



## Redis和MySQL的区别

Redis是非关系性数据库同时也是缓存数据库（数据保存在缓存中，读取速度快但保存时间有限）

MySQL是持久化存储的关系型数据库（该类型的数据库访问都存在I/O操作相对比缓存数据库效率较低）

两者因为需求的不同一般配合使用（请求现在浏览器缓存中查找，没有再到数据库中查找）



DB(Database)数据库：存储数据的容器

DBMS(Database Management System)数据库管理系统：操作数据库的系统

SQL(Structure Query Language)结构化查询语言：控制数据库操作的命令语言

---

## 常用命令

本地登录

`mysql -u [userName] -p [password]`

云端登录

`mysql -h [hostName] -P hostPort] -u [userName] -p [password]`

查看所有的数据库

`show databases;`

使用数据库

`use [databaseName];`

查看当前使用的数据库

`select database();`

创建数据库

`create database [databaseName];`

删除数据库

`drop database [databaseName];`

库外查看所有表

`show tables from [databaseName];`

库内查看所有表

`show tables;`

查看表结构

`desc [tableName];`

查看数据库服务端版本

​	mysql内

​		`select version();`

​	cmd内	

​		`mysql --version`

​		`mysql -V`

创建表

```sql
create table tableName(
	columnName1 columnType1,
    columnName2 columnType2, 
);
```

注释

```sql
单行注释
	-- Content

多行注释
	/*
		Content
	*/
```

执行外部sql命令文件

`source [filePath];`

---

## DQL

### 基础查询

#### 着重号

在sqlyog使用，cmd中似乎不可用

着重号  **`**  ，在命令中遇到列明和关键字冲突时，列明用着重号包围说明是列名

>   select \`columnName\` from tableName;



#### 简单查询语句

```sql
-- 列出表中所有行的所有列
SELECT * FROM employees;
-- 列出表中所有行的`last_name`列和`salary`列
SELECT `last_name`,`salary` FROM employees;
```



#### 给查询列明指定别名

```sql
-- 方式一
SELECT `first_name` AS  "姓",`last_name` AS "名" FROM employees;
-- 方式二
SELECT `first_name` "姓",`last_name` "名" FROM `employees`;
```



#### 去除查询结果中值重复的项

首先我们需要知道**DISTINCT**关键字只能够放在查询字段的开头，一般而言DISTINCT用来查询不重复记录的条数

```sql
-- 查询单值去重
SELECT DISTINCT `job_id` FROM `employees`;
```

需要注意，如

>   SELECT DISTINCT \`department_id\`,\`job_id\`  FROM employees;

这条查询语句的意义过滤两个字段重复的记录，并不是想象中的查询department_id各不相同的job_id



#### SQL中加号的作用

对与两个数值型字段相加，进行加法运算

相加两方存在字符型，试图将字符型字段转换成数值型再做加法运算

相加两方当中有一方为**NULL**则结果一定为NULL



如果需要进行连接字符串操作，可以使用**CONCAT**函数



#### 连接字符串

使用到`CONCAT(str1,str2,...)`函数，将入参字符串按顺序拼接

```sql
-- 连接姓和名
SELECT CONCAT(`first_name`," ",`last_name`) FROM `employees`;
```

### 模糊查询

首先提到一点，如果模糊查询条件中有单个特殊字符需要转义的可以使用到   \  进行单个特殊字符的转义 

如果在模糊查询条件中所有的特殊字符都需要转义，这时就可以使用**ESCAPE**关键字指定需要转移的字符

如

```sql
SELECT `last_name` FROM `employees` WHERE `last_name` LIKE '$_$' ESCAPE '$';
```



#### LIKE

**LIKE**一般搭配通配符使用

|通配符|说明|
|---|---|
| %| 包含零个或多个字符的任意字符串|
| _ | 任何单个字符。                                         |
| [ ]         | 指定范围 ([a-f]) 或集合([abcdef]) 中的任何单个字符，如`[a-b]` |
| [^]         | 不属于指定范围 ([a-f]) 或集合([abcdef]) 的任何单个字符，如`[^a-b]` |



#### BETWEEN  AND

```sql
-- BETWEEN AND语句等价于第二个查询语句
-- 1. 使用 BETWEEN AND
SELECT salary FROM employees WHERE salary BETWEEN 5000 AND 10000;
-- 2. 使用><= AND
SELECT salary FROM employees WHERE salary>=5000 AND salary<=10000;
```

#### IN

限制条件值在一个数值列表内

如

```sql
SELECT `last_name` FROM employees WHERE `manager_id` IN("108","120");
```



#### IS NULL

普通的=运算符不能够用于判断null值

判断是否为null,可以使用IS NULL关键字

这条语句的作用仅用于判断null值



#### 安全等于

安全等于：<=>

这个等于可以用来判断数值是否相等也可以用来判断数值是否为null



### 排序查询

如果需要对查询结果进行排序，用ORDER BY [columnName]根据列名进行结果排序

```sql
-- ASC升序
SELECT `last_name`,`salary` FROM employees ORDER BY salary ASC;
-- DESC降序
SELECT `last_name`,`salary` FROM employees ORDER BY salary DESC;
-- 先按salary升序排序再按employee_sid降序排序
SELECT `employee_id`,`salary` FROM employees ORDER BY salary ASC,`employee_id` DESC;
-- 可以使用别名
SELECT `employee_id` AS 编号,`salary` AS 工资 FROM employees ORDER BY 工资 DESC;
```



### 分组查询

使用分组函数查询的结果可以进行分组

如，查询每个部门员工的工资总和

```sql
SELECT
	SUM(salary),department_id 
FROM
	employees 
WHERE 
	department_id IS NOT NULL 
GROUP BY 
	department_id;

/*结果
4400.00	    10
19000.00	20
24900.00	30
6500.00	    40
156400.00	50
28800.00	60
10000.00	70
304500.00	80
58000.00	90
51600.00	100
20300.00	110
*/
```



如果需要对已经分组的查询结果进行条件限制，此时不能使用WHERE关键字，而是使用到HAVING关键字

如在上述案例的要求上再加一个部门工资总和需要超过10000的条件则查询语句变为

```sql
SELECT
	SUM(salary),department_id 
FROM
	employees 
WHERE 
	department_id IS NOT NULL 
GROUP BY 
	department_id
HAVING
	SUM(salary)>=10000;
```



此外，分组查询还可以根据多个字段分组

### 连接查询

综合多表数据进行查询，这里使用到SQL99的连接查询标准

#### 内连接

##### 等值连接

根据表间的关联字段是否相同进行连接查询

如，查询员工名字和部门名称，其中员工名字在employees表、部门名称在departments表，通过department_id字段关联

```mysql
SELECT e.`last_name`,d.`department_name`
FROM employees e
INNER JOIN departments d
ON e.`department_id`=d.`department_id`;
```

##### 非等值连接

非等值连接用到BETWEEN AND或LIKE等等不等值作为连接条件

如，查询员工工资的工资等级，员工工资在employees表，等级在job_grade表，使用job_grade的lowest_sal和highest_sal字段进行连接

```mysql 
SELECT e.salary,g.grade_level
FROM job_grades AS g
INNER JOIN employees AS e
ON e.salary BETWEEN g.`lowest_sal` AND g.`highest_sal`
ORDER BY g.`grade_level` ASC;
```

##### 自连接

根据表中的某一字段连接相同表中另一字段，如employees表中的employee_id-->manager_id-->employee_id

即查询员工的领导，领导同时也是员工这时候使用到自连接



#### 外连接

##### 左、右外连接

以一方为主表，一方为从表，主表的记录被全部查询出来，然后再根据连接条件得到相应的从表记录，如果没有查询到相匹配的从表记录则值为null

以左外连接为例，即以左表为主表

```mysql
SELECT e.last_name,d.department_name
FROM employees e
LEFT OUTER JOIN departments d
ON e.`department_id`=d.`department_id`;
```

同理，右外连接的关键字为RIGHT OUTER JOIN，以右表为主表

#### 交叉连接

即查询两表结果的笛卡尔积

```sql
SELECT e.last_name,d.department_name
FROM employees e
CROSS JOIN departments d;
```

### 子查询

 指在一条select语句中，嵌入了另外一条select语句，那么被嵌入的select语句称之为子查询语句

#### 子查询分类

按子查询出现的位置：

*   SELECT后

    仅支持标量子查询

*   FROM后

    支持表子查询

*   WHERE或HAVING后

    标量子查询

    列子查询

    行子查询

*   EXISTS后（相关子查询）

    表子查询

    

按结果集的行列数不同：

*   标量子查询（结果集只有一行一列）
*   列子查询（结果集只有一列多行）
*   行子查询（结果集只有一行多列）
*   表子查询（结果集为多行多列）



#### WHERE后或HAVING后

##### 标量子查询

结果集只有一行一列，

```mysql
-- 找出工资和112号员工相同的员工的名字

SELECT last_name
FROM employees
WHERE salary=(
	SELECT salary 
	FROM employees
	WHERE employee_id=112
);
```

##### 列子查询

结果集为一列多行，与子查询连接的运算符有IN、ANY（SOME）、ALL

```mysql
-- 查找IT部中工资比管理部任意一个人工资高的员工的名字

SELECT last_name
FROM employees
WHERE salary>ANY(
	SELECT salary
	FROM employees
	INNER JOIN departments
	ON employees.department_id=departments.department_id
) AND department_id=ANY(
	SELECT department_id
	FROM departments
	WHERE department_name='IT'
);
```

##### 行子查询

略

```mysql
SELECT *
FROM employees
WHERE (salary,employee_id)=(
	SELECT MAX(salary),MIN(employee_id)
	FROM employees
);
```



#### SELECT后

```mysql
-- 部门信息以及统计每个部门的人数
SELECT d.*,(
	SELECT COUNT(*)
	FROM employees e
	WHERE d.department_id=e.department_id
)
FROM departments d;
```

#### FROM后

```mysql
-- 查询每个部门平均工资的工资等级
SELECT avg_department.d_id,g.grade_level
FROM (
	SELECT AVG(salary) avg_salary,department_id d_id
	FROM employees e
	GROUP BY e.department_id
)avg_department 
JOIN job_grades g
ON avg_department.avg_salary BETWEEN g.lowest_sal AND g.highest_sal

```

#### EXISTS中

```mysql
-- 查询有员工的部门名
-- 使用EXISTS函数，如果EXISTS中有结果则返回1，无则返回0
SELECT d.department_name
FROM departments d
WHERE EXISTS(
	SELECT *
	FROM employees e
	WHERE e.department_id=d.department_id
);
```

### 分页查询

使用到mysql的limit方言，作用是对查询结果进行分页，

​	参数start：起始索引，最低为0

​	参数size：结果长度，当前分页的结果条数

案例：查询工资排名11-20的员工信息

```mysql
SELECT *
FROM employees
ORDER BY salary DESC
LIMIT 10,10;
```

### 联合查询

在查询需要得到来自多表的结果时需要用到联合查询，如中国用户表和外国用户表需要合并成一个表时

需要注意，两个表查询的列数需要一致，结果集的列名为第一个查询的列名

并且联合之后结果集会自动去重



```mysql
CREATE TABLE p_cn(
	id INT PRIMARY KEY AUTO_INCREMENT,
	`name` VARCHAR(20)
	);
	
CREATE TABLE p_an(
	id INT PRIMARY KEY AUTO_INCREMENT,
	`an_name` VARCHAR(20)
	);
	
INSERT INTO p_cn(`name`) VALUES('小明');
INSERT INTO p_an(`an_name`) VALUES('hari');

SELECT `name`,id FROM p_cn 
UNION 
SELECT `an_name`,id FROM p_an;
```



---

## DML

### 插入数据

插入数据到表有两种方式

#### 方式一

该方式同时多行插入，支持子查询

```mysql
INSERT INTO person(name,age) values('hari',11);

-- 同时多行插入
INSERT INTO person(name,age) values('hari',11),('mari',11);

-- 对子查询的支持，返回结果为结果集省略values
INSERT INTO person(name,age) 
SELECT 'hari',12;
```



#### 方式二

不支持多行插入也不支持子查询

 ```mysql
INSERT INTO person
SET `name`='hari',age=11;
 ```

### 更新数据
#### 单表更新

格式 ：

​		UPDATE 表名 SET 列名=值,..... WHERE 限制条件



#### 多表更新

多表更新使用到表的连接，两表组合成一个表再进行修改

```mysql
UPDATE employees e
LEFT OUTER JOIN departments d
ON e.department_id=d.department_id
SET d.department_name='IT',e.salary=5000`employees`
WHERE e.employee_id=206;
```

### 删除数据

#### 使用DELETE

特点：

*   删除数据可回滚

*   可以针对单行删除

*   可以多表连接删除

*   AUTO_INCREMENT自增值从断点开始

*   删除有返回值

格式：`DELETE FROM 表名 [WHERE 限制条件] `



#### 使用TRUNCATE

特点：

*   删除数据在事务中不可回滚
*   删除仅针对整个表
*   不支持多表连接删除
*   AUTO_INCREMENT自增值从新开始
*   删除无返回值

格式：`TRUNCATE TABLE 表名`

## DDL

### 库操作

#### 创建库

创建库:`CREATE DATABASE 库名`

如果需要避免库已经存在的错误的话可以给语句加上判断语句

`CREATE DATABASE IF NOT EXISTS 库名`

#### 删除库

`DROP DATABASE 库名`

避免库不存在的错误

`DROP DATABASE IF EXISTS 库名`

#### 查看库字符集

`SHOW CREATE DATABASE 库名`

```mysql
SHOW CREATE DATABASE myemployees;

/*
Database	Create Database
myemployees	CREATE DATABASE `myemployees` /*!40100 DEFAULT CHARACTER SET gbk */ /*!80016 DEFAULT ENCRYPTION='N' */
*/
```

#### 修改表字符集

`ALTER DATEBASE 库名 CHARACTER SET 字符集`

#### 修改数据库名

当前版本语句修改库名已经不可行了，会对库造成影响

可行的方法是直接修改库本地文件系统下的文件夹名

### 表操作

#### 创建表

语法：

```mysql
CREATE TABLE 表名(
	列名 列类型(长度) 约束,
    列名 列类型(长度) 约束,
    ...
    列名 列类型(长度) 约束
)
```

可添加IF NOT EXISTS判断



#### 修改表

语法：

```mysql
ALTER TABLE 表名 MODIFY|CHANGE|ADD|DROP COLUMN 列名 [列类型 约束]
```



##### 修改列类型、约束（MODIFY）

语法：`ALTER TABLE 表名 MODIFY COLUMN  列名 新类型 新约束` 

如果不提供相应的列级约束则原本的约束将会清空

##### 修改列名、列类型、约束（CHANGE）

语法：`ALTER TABLE 表名 CHANGE COLUMN 旧列名 新列名 列类型 约束`

如果通过CHANGEG仅修改列名还要提供原本的列类型和约束，如果不提供列类型，语句将无法执行，如果不提供原本的约束，原本的约束将丢失

也即是说通过这种方法修改，需要提供列名、列类型、约束着三个信息



##### 添加列

语法：`ALTER TABLE 表名 ADD COLUMN 列名 列类型 约束`



##### 删除列

语法：`ALTER TABLE 表名 DROP COLUMN 列名`



##### 表重命名

`ALTER TABLE 旧表名 RENAME TO 新表名`



##### 删除表

语法：`DORP TABLE 表名`

可添加IF EXISTS判断



##### 复制表

可以对表进行多种赋值操作

*   仅复制表结构
*   仅复制表的部分结构（子查询）
*   复制表的结构以及记录（子查询）
*   复制表的部分结构以及部分记录（子查询）

###### 仅复制表结构

语法：

```mysql
CREATE TABLE 新表名 LIKE 旧表名;
```



###### 仅复制表的部分结构

语法：

```mysql
CREATE TABLE 新表名
SELECT 列名1，列名2
FROM 旧表名
WHERE 矛盾表达式(如1=0)
```



###### 复制表的结构以及记录

语法：如上

```mysql
CREATE TABLE 新表名
SELECT * 
FROM 旧表名
```



###### 复制表的部分结构以及部分列记录

略，参考仅复制表的部分结构，将矛盾表达式更换成想要的限制条件即可

### 数据类型

#### 整型

TINYINT、SMALLINT、MEDIUMINT、INT(INTEGER)、BIGINT分别对应1、2、3、4、8字节的整形

给类型添加UNSIGNED可声明无符号整形



#### 小数

*   FLOAT 4字节
*   DOUBLE 8字节
*   DECIMAL  10字节

三个类型的声明

*   FLOAT(M,D)
*   DOUBLE(M,D)
*   DECIMAL(M,D)

M表示整数位和小数位总位数的上限

D表示小数位位数的上限

在FLOAT、DOUBLE的声明中(M,D)如果省略，在插入数据时会自动根据数值的大小变化

在DECIMAL的声明中，如果省略了(M,D)将会有一个默认值(10,0)



#### 字符与二进制

定长和可变长的区别是，

​	对于容量为10定长字符串，存入长度为2的数据，大小依旧为10

​	对于容量为10的可变长字符串，存入长度为2的数据，大小为2

*   char定长字符串

*   varchar可变长字符串

*   text大文本

    *   tinytext:255byte

    -   text:64kb
    -   mediumtext:16m
    -   longtext:4gb

*   binary定长二进制数组

*   varbinary可变长二进制数组

*   blob

    -   tinyblob:255byte
-   blob:64kb
    -   mediumblob:16m
    -   longblob:4gb 
    
    

#### 日期类型

-   date：仅日期
-   time：仅时间
-   datetime：日期+时间，占8字节
-   timestamp：日期加时间，与本地时区相关，占4字节
-   year：仅年

### 约束

#### 六大约束

-   PRIMARY KEY：主键约束（不可重复，不可为空）
-   UNIQUE：唯一约束（不可重复，可为空，虽然说可以为空值，但是NULL值也是不可重复的，即null值也只能存在一个）
-   NOT NULL：非空约束（不可为空）
-   DEFAULT：默认值约束（给列设置默认值）
-   CHECK：MySQL不支持
-   FOREIGN KEY：外键约束（说明该列引用自另一个表，一般引用的列是主键或唯一键标注的）



#### 列级约束

列级约束指放在在列声明字段后的约束
PRIMARY KEY、NOT NULL、UNIQUE、DEFAULT都是列级约束

FOREIGN KEY也可以像列级约束一样声明，但是MySQL不支持FOREIGN KEY使用列级约束的方式声明，语法不报错但不生效
案例演示列级约束的声明
    

```mysql
CREATE TABLE person(
    id INT PRIMARY KEY,
    `name` VARCHAR(20) NOT NULL,
    telephon_number VARCHAR(11) UNIQUE,
    gender VARCHAR(1) DEFAULT '男',
    -- 外键  语法支持但无效
    job varchar(20) REFERENCES person_job(id)
);
```


​    
#### 表级约束

表级约束是在表声明的末尾给字段添加的约束
PRIMARY KEY、FOREIGN KEY、UNIQUE都是表级约束
语法：[CONSTRAINT 自定义键名] 约束名(约束字段名称)
外键：[CONSTRAINT 自定义键名] FOREIGN（主表字段名称） REFERENCES 从表名称（从表字段名称）

表级约束使用案例
    

```mysql
	CREATE TABLE person(
    	id INT,
    	`name` VARCHAR(20),
   	 	telephone_number VARCHAR(11),
    	-- 列级约束 DEFAULT
   		gender VARCHAR(1) DEFAULT '男',
    	job INT,
    	CONSTRAINT pk PRIMARY KEY(id),
    	CONSTRAINT uk UNIQUE(telephone_number),
    	CONSTRAINT fk FOREIGN KEY(job) REFERENCES t_job(id)
    );
```



#### 联合主键

多列字段联合为一个主键

如在上述的person表中将id主键修改为一个id和telephone_number联合的主键

`CONSTRAINT uni_pk PRIMARY KEY(id,telephone_number)` 

联合主键的其一在表中值有重复不算做重复，联合主键的两者的值都有重复时才算作重复



#### 给已创建的表添加约束

列级约束使用ALTER TABLE MODIFY的方法

已创建的表使用ALTER TABLE ADD的方法添加，即`ALTER TABLE 表名 ADD COLMUN [CONSTRAINT 自定义键名] FOREIGN（主表字段名称） REFERENCES 从表名称（从表字段名称）`



#### 删除约束

删除非空约束和默认值参考修改表



删除UNIQUE:

​	`ALTER TABLE 表名 DROP INDEX 唯一约束名或约束所在列名`

删除PRIMARY KEY

​	`ALTER TABLE 表名 DROP PRIMARY KEY`

删除FOREIGN KEY

​	`ALTER TABLE 表名 DROP FOREIGN KEY 外键约束名或外键约束所在列名`

### 标识列（自增列)

标识列使用在主键或者唯一键上（一般多用在主键上），让列的值自增长

关键字：AUTO_INCREMENT（标识位置参考标识列级约束）

标识列的类型只能为数值型（整数+浮点数）



默认的，标识列在第一次插入记录时省略标识列的值，默认为1，再次插入时再加1

如果在标识类的一次插入记录时给定一个数值，那么该值就是标识标识列的起始值，再次插入时再加1



如果需要规定标识列自增的步长，可以使用下面这条语句

`SET auto_increment_increment=步长`

---

## 事务

[事务.md](.\事务.md)

---

## 视图

视图就是一个由一个或多个真实表导出的虚拟表，对视图的记录进行修改（仅支持原始表有的字段）也会影响到原始表的数据，但是在一下情况下不能够对视图进行增、删、改操作

![](.\视图不能进行增删改操作的情况.png)

如

```mysql
UPDATE view_01 SET last_name='King',department_name='unknow' WHERE last_name='K_ing';
```

这个跨越两表的操作不允许的





一般的，也可以将视图用来当做SQL语句复用的工具

#### 创建视图

简单创建视图的语法：CREATE VIEW 视图名 AS (得到结果集的查询语句)

```mysql
CREATE OR REPLACE VIEW view_01
AS 

SELECT last_name,salary,department_name,d.department
FROM employees e
INNER JOIN departments d 
ON e.department_id=d.department_id
WHERE salary>10000;
```



#### 视图的使用

对于视图的使用，只要把视图当做一个表来使用就可以了

但是需要注意在上面提到的几个不能使用增、删、改操作的情况



#### 查看视图结构

跟表一样使用DESC关键字查看



#### 删除视图

`DROP VIEW 视图名`



#### 修改视图

只需要把创建视图的语句`CREATE VIEW 视图名 AS`改成`CREATE OR REPLACE VIEW 视图名 AS`即即可

语义为如果视图不存在则创建视图，若视图存在则替换原视图为新的视图

---

## 变量

### 系统变量

系统变量是由服务器提供的作用于mysql全部连接或部分连接的变量，如autocommit

系统变量根据作用域可以分为

-   全局变量(GLOBAL)

    作用于所有的会话，在某一个会话中修改也会影响到其他会话，但修改在mysql服务重启后变回默认值

-   局部变量(SESSION)

    仅作用于当前会话

#### 系统变量的查看及修改

```mysql
-- 查看所有全局变量
SHOW GLOBAL VARIABLES;
-- 查看部分全局变量，使用模糊查询进行限制
SHOW GLOBAL VARIABLES LIKE '%char';
-- 查看某个全局变量的值
SELECT @@global.autocommit;
-- 修改某个全局变量的值
	-- 1.
	SET @@global.autocommit=0;
	-- 2.
	SET GLOBAL autocommit=1;

-- 查看所有的局部变量
SHOW VARIABLES;
SHOW SESSION VARIABLES;
-- 查看部分局部变量，使用模糊查询进行限制
SHOW VARIABLES LIKE '%char%';
SHOW SESSION VARIABLES LIKE '%char%';
-- 查看某个局部变量的值
SELECT @@autocommit;
SELECT @@session.autocommit;
-- 修改某个全局变量的值
	-- 1.
	SET @@autocommit=0;
	SET @@session.autocommit=0;
	-- 2.
	SET SESSION autocommit=1;
```

### 自定义变量

只能在BEGIN    END中使用

 语法：DECLARE 变量名 变量类型 [DEFAULT 初始值]

### 用户变量

 用户变量的作用域为当前会话

#### 声明与赋值

所有的能够用于声明并初始化变量的语法格式都可以用来修改变量值

```mysql
-- 声明并初始化用户变量
SET @num=1;
SET @num:=1;
SELECT @num:=1;
-- 这种方式进行声明并并初始化select返回的结果只能为一行一列
SELECT COUNT(*) INTO @count FROM employees;
-- 更新用户变量值
SET @num=2;
SET @num:=2;
SELECT @num:=2;
-- 这种方法进行复制select返回的结果只能为一行一列
SELECT COUNT(*) INTO @count FROM employees WHERE salary>10000;
SELECT @count;
```

#### 

### 局部变量

 局部变量的作用域在BEGIN     END两个语句之间，也只能够生命在两者之间

声明语法：DECLARE 局部变量名 类型 [DEFAULT 初始化值]

#### 赋值与更新

```mysql
SET 变量名=值;
SET 变量名:=值;
SELECT @变量名:=值;

SELECT xxx INTO 变量名
FROM XXXX;

-- 查看变量值
SELECT 变量名;
```





---

## 存储过程

存储过程其实就是变成语言中的方法，作用复用使用频率高的语句的集合，提高语句的复用性



#### 修改结束符

要创建一个存储过程，第一步需要做的就是修改结束符。默认的结束符为  `;`  

但是在存储方程的方法体内必定是有多个结束符`;`的，如果没有修改结束符，那么在创建存储过程的过程中方法体内的语句被提交查询了，修改结束符就是为了完成声明存储过程

```mysql
DELIMITER 结束符
```

DELIMITER修改结束符的有效域为当前会话

#### 创建使用过程并调用案例

存储过程的传入参数除了要指定变量名和变量类型外还需要说明变量模式

模式如下

-   IN

    变量作为入参

-   OUT

    变量作为返回值

-   INOUT

    变量即为入参也为返回值



案例1：查询工资大于某值的员工数

```mysql
-- 修改结束符
DELIMITER $

-- 声明存储过程
CREATE PROCEDURE test01(IN salary DOUBLE,OUT COUNT INT)
BEGIN
SELECT COUNT(*) INTO COUNT FROM employees e WHERE e.salary>salary;
END$

DECLARE COUNT INT;

-- 调用
CALL test01(5000.0,COUNT)$

SELECT COUNT;
```

案例2：传入某值，将值变为原来的n倍

```mysql
-- 修改结束符
DELIMITER $

SET @num:=11;

-- 声明存储过程
CREATE PROCEDURE test02(INOUT num INT,IN n INT)
BEGIN
SET num:=num*n;
END $

-- 调用
CALL test02(@num,11)$

SELECT @num;
```

#### 删除存储过程

DROP PROCEDURE 存储过程名

#### 查看存储过程

SHOW CREATE PROCEDURE 存储过程名



---

## 自定义函数

自定义函数的使用跟存储过程类似，首先修改结束符，随后定义函数

需要注意的是mysql的bin_log在开启时，我们自定义的函数需要在定义说明函数是否为确定性的或者是否修改数据，有一下三个参数

1.  DETERMINISTIC 确定的
2.  NO SQL 没有SQl语句，当然也不会修改数据
3.  READS SQL DATA 只是读取数据，当然也不会修改数据 

参考： https://blog.csdn.net/ty_soft/article/details/6940190 



##### 创建函数实例

```mysql
DELIMITER $

CREATE FUNCTION func01(a INT,b INT) RETURNS INT DETERMINISTIC
BEGIN 
DECLARE result INT;
SET result:=a+b;
RETURN result;s
END $

SELECT func01(1,2)$
```



##### 查看函数

SHOW CREATE FUNCTION 函数名；

##### 删除函数

DROP FUNCTION 函数名；

---

## 流程控制

###  if...elseif...else...

在begin    end中使用

使用案例

```mysql
CREATE FUNCTION func02(i INT) RETURNS BOOLEAN NO SQL
BEGIN 
DECLARE result BOOLEAN;
IF(i=0) THEN SET result:=FALSE;
ELSEIF(i=1) THEN SET result:=TRUE;
END IF;
RETURN result;
END $
```

### 循环结构

mysql 中有三种循环结构，分别为

-   while
-   loop
-   repeat

使用语法如下

###### while

```mysql
[名称:]WHILE 循环条件 DO
循环体....
END WHILE [名称];

```

###### loop

```mysql
[名称:]LOOP
循环体...
END LOOP [名称];
```



###### repeat

```mysql
[名称:]REPEAT
循环体
UNTIL 结束条件
END REPEAT [名称];
```

除此之外还有两个循环控制语句

-   leave：类似于break，用于跳出所在的循环
-   iterate：类似于continue，用于结束本次循环，继续下一次



以while 为使用案例

```mysql
CREATE PROCEDURE pd01(IN i INT)
BEGIN
DECLARE j INT DEFAULT 0;
wh:WHILE j<=i DO
INSERT INTO number(num) VALUES(j);
SET j:=j+1;
END WHILE wh;
END $
UNTIL
```

---

## 字符函数

#### length

返回传入字符串的自付出，mysql的utf8字符集中每个中文大小为3

#### concat

拼接字符串，上文已经提到

#### upper

将传入字符串转换大写

#### lower

将传入字符串装换成小写

#### substr

需要注意sql中索引不是从0开始而是从1开始

截取字符串，这里提到该函数的两个重载

```sql
/*
重载一，substr(str,index)
从index开始往后截取字符串至字符串结尾
*/
SELECT SUBSTR('abcde',3);
/*
重载二，substr(str,index,length)
从index开始往后截取长度为length的字符串
*/
SELECT SUBSTR("abcde",3,2);
```

#### instr

即Java中的String.indexOf(str)

在sql中为INSTR(str,subStr)

```sql
SELECT INSTR('abcde','cde');
```

#### trim

TRIM([{BOTH | LEADING | TRAILING}] [remstr] FROM str)  

对字符首尾的字符进行过滤 ，如‘    abc     ’，trim就可以吧首尾的空格给过滤掉

BOTH、LEADING、TRAILING为可选项，分别对应对前后、前、后字符进行过滤

remstr为需要过滤的字符，不指定时默认为空格

```sql
SELECT TRIM(BOTH ' ' FROM '     abc      ');
```

#### lpad

对不满足指定长度的字符串进行左填充

LPAD(str,len,padstr)

padstr为填充字符

如果str的长度也超过了len，str也将会被截取到长度等于len为止

#### rpad

对不满足指定长度的字符串进行左填充

RPAD(str,len,padstr)

padstr为填充字符

如果str的长度也超过了len，str也将会被截取到长度等于len为止

#### replace

REPLACE(str,tarstr,repstr)

替换字符串的某一段

## 数学函数



#### round

对数值进行四舍五入，在未提供bit时默认四舍五入到个位，提供了bit时四舍五入到bit位小数

round(value,[bit])

#### ceil

CEIL(value)，该函数返回一个大于value值的最小整数

#### floor

FLOOR(value)，该函数返回一个小于value值的最大整数

#### truncate

truncate(value,bit)，按bit截断小数部分，即截断到bit位小数

## 日期函数

#### now

返回当前系统日期+时间

#### curdate

返回当前系统日期

#### curtime

返回当前系统时间

#### str_to_date

STR_TO_DATE(str,format)

根据提供的格式将字符串转换成data类型值

```sql
/*	
	%y  两位年份
	%Y  四位年份
	%m  月，小于十月则十位用零填充
	%c  月
	%d  日
	%H  24小时制小时
	%h  12小时制小时
	%i  分钟
	%s  秒
*/

SELECT STR_TO_DATE('2019-09-19,18:00:00','%Y-%m-%d,%H:%i:%s');
```

#### date_format

DATE_FORMAT(date,format)

将日期按模板返回字符串

```sql
SELECT DATE_FORMAT(CURDATE(),"%y-%c-%d");

-- 19-10-13
```



## 流程控制函数

#### if

IF(exp,T_return,F_return)

#### case

类似于swich，但用法不同，在sql中还有两种格式

```sql
-- 判断case后时候于when后相同
SELECT CASE 1
WHEN 1 THEN 1
WHEN 0 THEN 0
ELSE -1
END;

-- 判断when后表达式是否成立
SELECT CASE
WHEN 1=2 THEN 1
WHEN 1=0 THEN 0
ELSE -1
END;
```

## 分组函数

分组函数默认情况都是忽略null值的

#### sum

对查询字段求和

#### avg

对查询字段求平均数

#### min

对查询字段求最小值

#### max

对查询字段求最大值

#### count

对查询字段计算出现次数





上述的五个函数都能够搭配DISTINCT使用进行去重后计算



查询表中记录数

一般使用COUNT(*)

