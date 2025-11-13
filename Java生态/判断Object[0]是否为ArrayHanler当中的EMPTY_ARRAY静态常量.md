# 判断Object[0]是否为ArrayHanler当中的EMPTY_ARRAY静态常量

在使用DBUtils查询数据库时，如
```java
QueryRunner qr = new QueryRunner();
String sql = "SELECT u_username,u_password,u_regist_date FROM t_user WHERE u_username=?";
ArrayHandler ah = new ArrayHandler();
os = qr.query(JDBCUtils.getConnection(), sql, ah, username);
```
此时想要判断query方法查询得到的数组是否为空，就不能够简单的使用`os==null`来判断

在ArrayHandler内部有
```java
private static final Object[] EMPTY_ARRAY = new Object[0];
public Object[] handle(ResultSet rs) throws SQLException {
        return rs.next() ? this.convert.toArray(rs) : EMPTY_ARRAY;
    }
```
说明要判断查询没有得到结果需要使用到**EMPTY_ARRAY**这个私有的静态常量来判断

 但**EMPTY_ARRAY**是私有的，并且没有getter方法，这个时候就要使用到java的反射了

 ```java
 @Test
 public void func3() throws  SQLException,PropertyVetoException, NoSuchFieldException, SecurityException, IllegalArgumentException, IllegalAccessException {
 	QueryRunner qr = new QueryRunner();
 	String sql = "SELECT u_username,u_password,u_regist_date FROM t_user WHERE u_username=?";

 	ArrayHandler ah = new ArrayHandler();

  //xxx在数据库当中是不存在的,query必定会返回一个EMPTY_ARRAY
 	Object[] os = qr.query(JDBCUtils.getConnection(), sql, ah, "xxx");

  //私有的字段不能够使用getField方法获得Field对象，这里需要用到getDeclaredField方法
 	Field f = ArrayHandler.class.getDeclaredField("EMPTY_ARRAY");

  //允许访问成员,记得在使用完后关闭权限
 	f.setAccessible(true);

  //得到ArrayHandler中的private static final Object[] EMPTY_ARRAY = new Object[0];
 	Object empty = f.get(ArrayHandler.class);

  //判断查询结果是否为空
 	System.out.println(os.equals(empty));

  //关闭访问成员的权限
 	f.setAccessible(false);

 }
 ```
