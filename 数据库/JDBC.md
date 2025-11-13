# Java操控数据库

## JDBC的原理

链接数据库

* 导入相应数据库的驱动jar包

* Class.forName("xxx");加载驱动类
* 提供链接数据库的url，username，password
* 使用DriverManager类的静态方法getConnection()获得Connection对象

```java
@Test
public void func1() throws SQLException, ClassNotFoundException {
	Class.forName("com.mysql.jdbc.Driver");
	String url = "jdbc:mysql://localhost:3306/exam?useSSL=true&amp;serverTimezone=UTC&amp;useUnicode=true&amp;characterEncoding=UTF8";
	String username = "test";
	String password = "hhh123";
	Connection connection = DriverManager.getConnection(url, username, password);
}
```



# ！！！ 连接url已经要加参数如jdbc:mysql://localhost:3306/blog?useSSL=true&serverTimezone=GMT&useUnicode=true&characterEncoding=UTF8

如果使用的不是UTF8字符集&需要换成`&amp;`



JDBC是什么？

JDBC是Java访问、操作数据库的一种标准接口



`Class.forName("com.mysql.jdbc.Driver");`是干什么的？

```java
com.mysql.jdbc.Driver driver = new com.mysql.jdbc.Driver();
DriverManager.registDriver(driver);
```
这两行语句是为了把mysql的jdbc驱动类加载到驱动管理器当中去，目的是为了在后面能够通过DriverManager获得一个链接数据库的Connection对象

另外`Class.forName("com.mysql.jdbc.Driver")`和上面的两行代码是等价的

因为，我们知道`Class.forName()`方法是将一个类使用类加载器加载到Java虚拟机当中的，还记不记得我们曾经接触的叫做静态代码块的东西，`static{...}`括号内的内容将会在类被加载到虚拟机时执行。

在com.mysql.jdbc.Driver这个类当中就有一个静态代码块，里边的内容就是
```java
static{
    java.sql.DriverManager.registDriver(new Driver());
}
```

所以说两者的行为是等价的

一般为们使用的加载驱动的方法都是使用Class的forName方法

## 对数据库进行增删改

从Connection中获得Statement对象

`Statement sttm = Connection.createStatement();`

使用Statement对象向数据库发送sql更新语句

使用Statement对象的executeUpdate方法

`int modifyLineNum = sttm.executeUpdate("INSERT INTO person(name,age) VALUES('Hari',18);");`

其中返回值为操作语句对多少行语句造成了影响

```java
	Statement sttm = connection.createStatement();
	int modifyLineNum = sttm.executeUpdate("INSERT INTO person(name,age) VALUES('Hari',18);");
	System.out.println(modifyLineNum);
```

## 对数据库进行查询操作

获得Statement对象，用于向数据库发送查询语句

使用Statement发送查询语句，查询结果用ResultSet接受

最后解析ResultSet对象获得查询数据,先移动行指针，然后获取每一列的数据



这里首先引入一个概念

BeforeFirst

| 1    | aaa  | bbb  |
| :--- | ---- | ---- |
| 2    | CCC  | DDD  |

AfterLast



在Result对象当中有一个虚拟指针，他一开始的的位置是在第一行之前即在BeforeFirst的位置，最末尾的位置是在最后一行之后即在AfterLast的位置

虚拟指针最开始的位置就是在AfterFirst，当使用Result.next()方法之后指针将会向下移动一行,即移动到第一行



使用getInt、getString、getObject等等方法获得指定列的数据,方法需要的参数可以选择使用列的编号，或者列的名称

```java
	Statement sttm = connection.createStatement();
	String sqlStatement = "SELECT * FROM person;";
	ResultSet rs = sttm.executeQuery(sqlStatement);
	while(rs.next()) {
		String name = rs.getString("name");
		int age = rs.getInt("age");
		int id = rs.getInt("id");
		System.out.println("ID:"+id+"   "+"Name:"+name+"   "+"Age:"+age);
	}
/*
ID:1   Name:Hari   Age:18
ID:2   Name:Mari   Age:20
*/
```

## 关闭上面使用到的各个对象

这里使用规范化的关闭，为的是防止程序出现异常导致没有执行到后面的关闭方法

```java
	Connection cnnt = null;
	Statement sttm = null;
	ResultSet rs = null;
	String url = "jdbc:mysql://localhost:3306/my_db";
	String username = "test";
	String password = "hhh123";
	String sql = "SELECT * FROM person;";
	try {
		Class.forName("com.mysql.jdbc.Driver");
		
		cnnt = DriverManager.getConnection(url, username, password);
		sttm = cnnt.createStatement();
		rs = sttm.executeQuery(sql);
		while(rs.next()) {
			String name = rs.getString("name");
			int age = rs.getInt("age");
			int id = rs.getInt("id");
			System.out.println("ID:"+id+"   "+"Name:"+name+"   "+"Age:"+age);
		}
	} catch (ClassNotFoundException e) {
		e.printStackTrace();
	} catch (SQLException e) {
		e.printStackTrace();
	}finally {
		if(cnnt!=null)
			try {
				cnnt.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}finally {
				if(sttm!=null)
					try {
						sttm.close();
					} catch (SQLException e) {
						e.printStackTrace();
					}finally {
						if(rs!=null)
							try {
								rs.close();
							} catch (SQLException e) {
								e.printStackTrace();
							}
					}
			}
		}
```



##  结果集元数据

结果集元数据能够获得

* 结果集的列数
* 结果集指定列的列名

获得结果集:`rs.getMetaData()`,这个方法的返回值为`ResultSetMetaData`

获得结果集列数:`int getColumnCount()`

获得指定列的列名:`Stirng getColumnName(int columnIndex)`



## 结果集的三种特性

### 滚动

​	不可滚动则说明结果集的虚拟指针只能有向下移动一行的操作

​	可滚动则说明结果集的虚拟指针可以使用其他的移动虚拟指针的方法移动指针 

### 敏感

​	这种属性分为两种，一种为迟钝，一种为敏感，如果结果集是敏感的，那么在数据库的数据发生变化后，结果集当中的数据也会发生相应的改变，迟钝则不会发生改变

​	但是遗憾的是，所有的数据库驱动程序都没有实现结果集敏感这个特性，即使我们在创建Statement对象的时候设置了获得结果集应该是敏感的，但是实际获得的结果集也是非敏感的

### 更新

​	默认的状态下创建的结果集不可更新的

​	可更新的意思是如果结果集当中的数据发生了改变那么数据库当中的数据也会发生相应的改变

​	不可更新则反之

## 可滚动可更新结果集 

结果集不可滚动也就是说结果集当中的虚拟指针是只能够使用next()方法进行滚动，而不能使用其他方法进行滚动的

默认情况下创建的结果集都是可滚动的,并且结果集也是不可更新的

对于可更新这个特性我们普遍不会去使用，若果需要对数据库的数据进行修改使用Statement的executeUpdate方法就可以了，不需要使用到结果集可更新这个特性

如果想要获得能够不可滚动可更新的结果集，就要在创建Statement对象的时候在获得Statment的方法当中加入相应的参数

`Connection.createStatment(int resultSetType,int resultSetConcurrency)`

resultSetType设置为ResultSet.TYPE_FORWARD_ONLY

resultSetConcurrency设置为ResultSet.CONCUR_UPDATABLE

如果需要对结果集的数据进行更新类似于使用get方法的方式使用结果集的update方法即可

### resultSetType参数:

ResultSet.TYPE_FORWARD_ONLY：不滚动结果集；

ResultSet.TYPE_SCROLL_INSENSITIVE：滚动结果集，但结果集数据不会再跟随数据库而变化；

 ResultSet.TYPE_SCROLL_SENSITIVE：滚动结果集，但结果集数据会再跟随数据库而变化；（理想状态下存在，但是驱动没有实现这个特性）

### resultSetConcurrency参数:

ResultSet.CONCUR_READ_ONLY：结果集是只读的，不能通过修改结果集而反向影响数据库；

ResultSet.CONCUR_UPDATABLE：结果集是可更新的，对结果集的更新可以反向影响数据库



## 预处理

使用PrepareStatement能够有效的提高语句的执行效率，以及代码的可读性，同时还能够防止SQL注入

[为什么PrepareStament能够防止SQL注入？](<https://blog.csdn.net/liu1324457514/article/details/54427565>)

### 打开MySQL的预处理

首先，在MySQL当中预处理是默认关闭的，如果要使用到预处理需要在链接数据库的时候在url里设置相应参数

url:`jdbc:mysql://localhost:3306/xxx?useServerPrepStmts=true&useServerPrepStmts=true`

### 使用PrepareStatement

------

`INSERT INTO person(name,age) VALUES(?,?)`，在这条语句当中?代表未知的参数，需要后期使用setxxx()方法来设置这个参数的值,以的到目标的SQL语句

而且?是只能够用来设置数值的，如果用来指定列名等等操作是没有办法满足的

如`SELECT ?,? FROM ?`这一条语句,我们使用setString方法给三个？设置值

prepareStatement.setString(1,"name");

prepareStatement.setString(2,"age");

prepareStatement.setString(3,"person");

最终的到的语句是`SELECT 'name','age' FROM 'person';`

而我们期望的语句`SELECT name,age FROM person;`是无法得到的

------

对PreparedStatement的使用步骤

首先需要创建预处理的SQL语句

`String sql = "SELECT name FROM person WHERE age=?;";`

获得PreparedStatement对象,指定预设SQL语句

`PreparedStatement psttm = cnnt.prepareStatement(sql);`注意是prepareStatement不是preparedStatement

设置PreparedStatement对象的未知参数

`psttm.setInt(18);`

执行查询语句,并获得结果集

`ResultSet rs = psttm.executeQuery();`

## 大数据

MySQL数据库一般是不允许大数据传输的，如果需要传输大数据需要在my.ini文件当中设置最大允许的传输文件大小

`max_allowed_packet=10485760`其中数字的单位是字节数 10485760等于10MB 

### 对长文本的操作

　对于MySQL中的Text类型，可调用如下方法设置

```java
PreparedStatement.setCharacterStream(index, reader, length);//注意length长度须设置，并且设置为int型
```

　　对MySQL中的Text类型，可调用如下方法获取

```java
reader = resultSet. getCharacterStream(String columnLabel);
String s = resultSet.getString(String columnLabel);
```

#### 存储大文本

1. 获得需要存储的大文本的Reader流
2. 使用setCharacterStream方法，指定Reader流、文件大小
3. 执行更新语句

```java
		Connection cnnt = null;
		PreparedStatement psttm = null;
		String url = "jdbc:mysql://localhost:3306/my_db";
		String username = "test";
		String password = "hhh123";
		String sql = "INSERT INTO textfile(filename,data) VALUES(?,?);";
		Class.forName("com.mysql.jdbc.Driver");
		
		cnnt = DriverManager.getConnection(url,username,password);
		psttm = cnnt.prepareStatement(sql);
		File f = new File("C:\\Users\\Administrator\\Desktop\\Java操控数据库.md");
		FileReader reader = new FileReader(f);
		psttm.setString(1, f.getName());
		psttm.setCharacterStream(2, reader,f.length());
		psttm.executeUpdate();
```

#### 获得大文本

使用到的MySQL表

```mysql
CREATE TABLE `textfile` (
	`filename` VARCHAR(50) NULL DEFAULT NULL,
	`data` TEXT NULL
)
```



得到数据的结果集之后使用下面两个方法

##### 使用ResultSet的getString方法

```java
		Connection cnnt = null;
		PreparedStatement psttm = null;
		ResultSet rs = null;
		String url = "jdbc:mysql://localhost:3306/my_db";
		String username = "test";
		String password = "hhh123";
		String sql = "SELECT data FROM textfile WHERE filename=?;";
		Class.forName("com.mysql.jdbc.Driver");
		
		
		cnnt = DriverManager.getConnection(url,username,password);
		psttm = cnnt.prepareStatement(sql);
		psttm.setString(1, "Java操控数据库.md");
		rs = psttm.executeQuery();
		rs.next();
		String s = rs.getString("data");
		System.out.println(s);
```



##### 使用文件流的方法

```java
		Connection cnnt = null;
		PreparedStatement psttm = null;
		ResultSet rs = null;
		String url = "jdbc:mysql://localhost:3306/my_db";
		String username = "test";
		String password = "hhh123";
		String sql = "SELECT data FROM textfile WHERE filename=?;";
		Class.forName("com.mysql.jdbc.Driver");
		
		
		cnnt = DriverManager.getConnection(url,username,password);
		psttm = cnnt.prepareStatement(sql);
		psttm.setString(1, "Java操控数据库.md");
		rs = psttm.executeQuery();
		rs.next();
		Reader reader = rs.getCharacterStream("data");
		char[] charBuff = new char[1024];
		while(reader.ready()) {
			reader.read(charBuff);
			System.out.print(new String(charBuff).toString());
		}
```

### 对大的二进制文件的操作

使用到的MySQL表

```mysql
CREATE TABLE `binaryfile` (
	`filename` VARCHAR(50) NULL DEFAULT NULL,
	`data` MEDIUMBLOB NULL
)
```



对于MySQL中的BLOB类型，可调用如下方法设置：

```java
PreparedStatement. setBinaryStream(i, inputStream, length);
```

　　对MySQL中的BLOB类型，可调用如下方法获取：

```java
//1.
InputStream in  = resultSet.getBinaryStream(String columnLabel);
//2.
InputStream in  = resultSet.getBlob(String columnLabel).getBinaryStream(); 
```

#### 存储

```java
		Connection cnnt = null;
		PreparedStatement psttm = null;
		ResultSet rs = null;
		String url = "jdbc:mysql://localhost:3306/my_db";
		String username = "test";
		String password = "hhh123";
		String sql = "INSERT INTO binaryfile(filename,data) VALUES(?,?);";
		Class.forName("com.mysql.jdbc.Driver");
		
		File file = new File("C:\\Users\\Administrator\\Pictures\\noName\\mmexport1499416995567.jpg");
		FileInputStream fis = new FileInputStream(file);
		cnnt = DriverManager.getConnection(url,username,password);
		psttm = cnnt.prepareStatement(sql);
		psttm.setString(1, file.getName());
		psttm.setBinaryStream(2, fis, file.length());
		psttm.executeUpdate();
```



#### 获取

##### 第一种方法

```java
		Connection cnnt = null;
		PreparedStatement psttm = null;
		ResultSet rs = null;
		String url = "jdbc:mysql://localhost:3306/my_db";
		String username = "test";
		String password = "hhh123";
		String sql = "SELECT data FROM binaryfile WHERE filename=?;";
		Class.forName("com.mysql.jdbc.Driver");
		
		File file = new File("C:/Users/Administrator/Desktop/newfile.jpg");
		if(!file.exists())file.createNewFile();
		FileInputStream fis = new FileInputStream(file);
		cnnt = DriverManager.getConnection(url,username,password);
		psttm = cnnt.prepareStatement(sql);
		psttm.setString(1, "mmexport1499416995567.jpg");
		rs = psttm.executeQuery();
		rs.next();
		InputStream is = rs.getBinaryStream("data");
		FileOutputStream fos = new FileOutputStream(file);
		byte[] byteBuf = new byte[1024];
		while(is.read(byteBuf)>0) {
			fos.write(byteBuf);
		}
```

第二种方法

```java
//略
```

## 批处理

在数据库操作当中如果每次语句的执行都通过一次请求进行时非常占用网络带宽的，而批处理就是把多次的语句合成到一批当中，一次向SQL的请求执行一批的SQL语句

MySQL当中批处理也是默认关闭的，如果需要用到批处理就需要在链接url当中加入下列参数

`rewriteBatchedStatements=true`

批处理的执行结果是一个int[]，这个int数组的每一项分别为每一条语句执行后影响数据的行数

使用批处理目的是执行DML SQL语句的，而不是DQL SQL语句，是无法得到结果集的

### 在Statement中

使用到Statement的`addBatch(String sql)`方法，该方法的行为大概可以形容为往当前批次当中添加一条语句

当准备批处理SQL语句时，调用`executeBatch()`方法执行批处理

### 在PreparedStatement中

与Statement类似，

1. 设置好PreparedStatement对象的位置参数
2. 调用PreparedStatement对象的addBatch()方法
3. 需要执行批处理时使用PreparedStatement的executeBatch()方法

## 对于MySQL当中的时间数据类型的转换

java.sql.Date,java.sql.Time,java.sql.TimeStemp--->java.util.Date

sql包当中的三个时间类都是java.util.Date类的子类，如果想要转换，只需要向上转型即可

java.util.Date--->java.sql.Date,java.sql.Time,java.sql.TimeStemp

只需要把util.Date类转换成long型的毫秒值

然后把毫秒值交给sql时间类的构造器就可以完成转换

