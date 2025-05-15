# NamedParameterJdbcTemplate



**NamedParameterJdbcTemplate**是在**JdbcTemplate**的基础上实现的前者拥有后者的绝大多数方法

同时**NamedParameterJdbcTemplate**另外有使用别名给sql语句添加参数的功能



如

```java
@Test
public void func01() throws SQLException{
	NamedParameterJdbcTemplate namedJdbcTemplate = new NamedParameterJdbcTemplate(ctx.getBean(ComboPooledDataSource.class));
	Map map = new HashMap();
	map.put("id1", 1);
	map.put("id2", 3);
	String sql = "select * from employee where id=:id1 or id=:id2";
	List<Map> queryForList = namedJdbcTemplate.queryForList(sql, map);
	for(Map<String,Object> m:queryForList) {
		for(Entry e:m.entrySet()) {
			System.out.println("Key:"+e.getKey()+"   "+e.getValue());
		}
	}
}
```



另外还有**BeanPropertySqlParameterSource**这个对象接受一个JavaBean，通过JavaBean对象属性来决定命名参数的值。 



例如创建了一个Bean。 

```java
public class User {  
    private int id;  
    private String userName;
    private String password; 
    //省略getter和setter       
}
```

现在验证一个登录信息是否正确： 

```java
public boolean login(String userName,String password){
    NamedParameterJdbcTemplate namedParameterJdbcTemplate = null;  
    namedParameterJdbcTemplate = new NamedParameterJdbcTemplate(getJdbcTemplate());  
    User user = new User();  
    model.setUserName("jialeens");  
    model.setPassword("hehe");  
    String sql = "SELECT COUNT(1) FROM USER WHERE USERNAME=:userName AND PASSWORD=:password";  
    SqlParameterSource paramSource = new BeanPropertySqlParameterSource(user);  
    int size = namedParameterJdbcTemplate.queryForInt(sql, paramSource);  
    return size==1;
}
```