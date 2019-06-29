# Spring FrameWork 정리

* 클래스를 메모리에 올리는 방법

  1. 기본적인 방법

  ```java
  /*interface TV*/
  /*class LgTV implements TV*/ 
  /*class SamsungTV implements TV*/
  TV user;
  user = new LgTV();
  user = new SamsungTV();
  ```

  2. Spring Bean Configuration File와 BeanFactory를 이용

  ```xml
  <!--applicationContext.xml -->
  <beans>
  	<bean id="lg" class="spring.tv.LgTV"/>
      <bean id="samsung" class="spring.tv.SamsungTV"/>
  </beans>
  ```

  ```java
  String config = "applicationContext.xml";
  BeanFactory factory = new XmlBeanFactory(new ClassPathResource(config));
  TV user = (TV) factory.getBean("lg");
  /*bean의 id값을 통해 class를 Object로 반환*/
  ```

  3. ***Spring Bean Configuration File와 ApplicationContext를 이용***

  ```java
  String[] config = {"applicationContext.xml"};/*복수개의 config사용 가능*/
  ApplicationContext context = new ClassPathXmlApplicationContext(config);
  TV user = (TV) context.getBean("lg");
  TV user2 = context.getBean("lg", TV.class);/*캐스팅 명시를 피할 수 있음*/
  ```

  4. Spring Bean Configuration 추가 정보

  ```xml
  <bean id="lg" class="spring.tv.Lgtv"
        lazy-init="true"   		/*false이면 getBean으로 호출하기 전에 이미 생성됨 */
        scope="prototype"			/*singleton이면 하나의 객체만 생성하고 그 객체를 계속 이용*/
        init-method="init_func"	/*클래스 내부에 정의된 init, destroy메소드를 지정*/
        destroy-method="destroy_func"/>
  
  <bean id="now" class="java.util.Calendar"
        factory-method="getInstance"/> 
  <!--Calendar클래스는 생성자가 없음. 
  객체를 생성하려면 Calendar.getInstance()메소드를 사용해야 함. java에서는 나머지와 같음
  /*ex.java...*/
  Calendar cal = (Calendar) context.getBean("now");-->
  
  <bean id="test" class="Test">
  	<consturctor-arg ref="now"/> <!--construcotr-arg는 생성자 매개변수-->
      <constructor-arg val="abc"/> <!--ref는 해당 xml파일에서 id를 통해 참조-->
      <property name="num" val="123"/> <!--val은 값을 직접 대입-->
  </bean>								<!--property는 멤버변수, name으로 변수명을 구분함-->
  ```

  5. userservice 실전 예제

  ```xml
  <!--DAO 로딩-->
  <bean id="jdbc" class="spring.biz.user.dao.UserDAO_JDBC"/>
  <bean id="spring" class="spring.biz.user.dao.UserDAO_Spring"/>
  <bean id="mybatis" class="spring.biz.user.dao.UserDAO_Mybatis"/>
  
  <!--UserServiceImpl은 그냥 생성하면 dao가 null이므로 dao를 생성해 줌-->
  <bean id="service" class="spring.biz.user.service.UserServiceImpl">
  	<constructor-arg ref="jdbc"/>
  </bean>
  
  <!--user객체 생성하고 속성값 할당하는 기본적인 방법-->
  <bean id="user" class="spring.biz.user.vo.UserVO">
  	<property name="userid" val="admin"/>
      <property name="userpw" val="1234"/>
  </bean>
  <!--Namespace:p를 이용하는 방법-->
  <bean id="user" class="spring.biz.user.vo.UserVO"
        p:userid="admin"
        p:userpw="1234"/>
  <!--xml파일에서 Namespaces에서 p를 체크함-->
  <!--<beans xmlns:p="http://www.springframework.org/schema/p"/> 가 추가됨-->
  ```

  6. ***Spring Bean Configuration File와 Annotation을 이용***

  ```xml
  <!--applicationContext.xml-->
  <beans>
  	<context:annotation-config/>
      <context:component-scan base-package="spring.biz"/>
  </beans>
  ```

  ```java
  @Component("jdbc") /*id가 jdbc로 로딩*/
  /*아이디를 지정해주지 않으면 디폴트로 id를 클래스 소문자로 생성 ex)userdao_jdbc*/
  public class UserDAO_JDBC implements UserDAO{
      /*...*/
  }
  /*의존성 주입 어노테이션*/
  /*@Autowired : 주로 변수 위에 설정, 해당 타입의 객체를 찾아서 자동으로 할당
   *@Qualifier : 특정 객체의 이름을 이용하여 의존성 주입
   *@Inject = @Autowired
   *@Resource = @Autowired + @Inject*/
  @Component("userservice")
  public class UserServiceImpl implements UserService{
      @Autowired
      @Qualifier("jdbc") /*또는 Resource(name="jdbc") */
      private UserDAO dao;
      /*...*/
  }
  ```

  

* AOP

  1. applicationContext.xml 을 이용
  
  ```xml
  <!--applicationContext.xml-->
  <bean id="advice" class="aop.Advice"/>
  <context:component-scan base-package="spring.biz.user"/>
  <!--aop와 aop의 대상이 될 클래스 모두 bean에 올라가야 함-->
  <aop:config>
  	<aop:pointcut id="mp"
                    expression=
                    "execution(* spring.biz.user.service.*Service.login(..))"/>
      <!--pointcut의 대상이 될 메소드들 expression에 지정-->
      <aop:aspect ref="advice">
      	<aop:before pointcut-ref="mp" method="log"/>
          <!--Advice.log()로 mp를 pointcut으로 삼음, (before)-->
          <aop:after-returning pointcut-ref="mp" 
                               method="after_Return" returning="data"/>
          <aop:after-throwing pointcut-ref="mp"
                              method="after_Throw" returning="e"/>
        <aop:around pointcut-ref="mp" method="around"/>
      </aop:aspect>                                                           
  </aop:config>
  ```
  
  ```java
  /*Advice.java*/
  public class Advice{
      public void log(JoinPoint jp){
          /*jp.getArgs(), jp.toShortString()...*/
      }
      public void after_Return(Object data){
          /*data...*/
      }
      public void after_Throw(Exception e){
          /*e...*/
      }
      public Object around(ProceedingJoinPoint pp){
          Object obj;
          /*...obj = pp.proceed();..*/
    	return obj;
      }
  }
  ```
  
  ```java
  /*AOPTets.java*/
  public class AOPTest {
  	public static void main(String[] args) {
  		String[] config = {"applicationContext.xml"};
  		ApplicationContext context = new ClassPathXmlApplicationContext(config);
  		
  		UserService service = (UserService) context.getBean("userservice");
          /*반드시 context.getBean을 이용해야 함.*/
          /*주의: new UserService()를 사용하면 안됨*/
  		service.login("admin", "a1234");
  	}
  }
  ```
  
  2. ***Annotation을 이용***
  
  ```java
  /*AnnotAdvice.java*/
  @Component
  @Aspect
  public class AnnotAdvice {
  	@Pointcut("execution(* test.CutClass.cutOut(..))")
  	public void pointCut() {}
  	
  	@Before("pointCut()")
  	public void log(JoinPoint jp) {
  		System.out.println("====start=====");
  		System.out.println(jp.toShortString());
  		System.out.println("====end=====");
  	}
      
      @AfterReturning(pointcut = "pointCut()", returning = "data")
  	public void after_Throwing(JoinPoint jp, Object data) {
  		System.out.println("====after start=====");
  		System.out.println(jp.toShortString());
  		System.out.println(data.toString() );
  		System.out.println("====after end=====");
  	}
  }
  ```
  
  ```xml
  <!--application.xml-->
  <context:component-scan base-package="test, aop" />
  <aop:aspectj-autoproxy />
  ```
  



* applicationContext에서 Namespace이용하려면

```xml
<!--pom.xml-->
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>4.3.14.RELEASE</version>
</dependency>
```



* oracle DB에 접근하기 위한 기본적인 세팅

```xml
<!--pom.xml-->
<repositories>
    <!--oracle 드라이버를 maven repository에서 찾을 수 없으므로 직접 지정해줌-->
	<repository>
		<id>codelds</id>
		<url>https://code.lds.org/nexus/content/groups/main-repo</url>
	</repository>
</repositories>

<dependencies>
	<dependency>
		<groupId>com.oracle</groupId>
		<artifactId>ojdbc6</artifactId>
		<version>11.2.0.3</version>
	</dependency>
</dependencies>
```



* dao - spring 사용법

  1. dataSource를 메모리에 올린다.

  ```xml
  <!--pom.xml-->
  <!-- https://mvnrepository.com/artifact/commons-dbcp/commons-dbcp -->
  <dependency>
  	<groupId>commons-dbcp</groupId>
  	<artifactId>commons-dbcp</artifactId>
  	<version>1.4</version>
  </dependency>
  ```

  ```xml
  <!--applicationContext.xml-->
  <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
  	p:driverClassName="oracle.jdbc.OracleDriver"
  	p:url="jdbc:oracle:thin:@127.0.0.1:1521:xe"
  	p:username="scott"
  	p:password="TIGER"/>
  ```

  

  2. template를 메모리에 올린다.

  ```xml
  <!--pom.xml-->
  <!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
  <dependency>
  	<groupId>org.springframework</groupId>
  	<artifactId>spring-jdbc</artifactId>
  	<version>4.3.14.RELEASE</version>
  </dependency>
  ```

  ```xml
  <!--applicationContext.xml-->
  <bean id="template"
  	class="org.springframework.jdbc.core.JdbcTemplate">
  	<constructor-arg ref="dataSource" /><!--bean에 올려둔 dataSource를 참조함-->
  </bean>
  ```
  
  
  
  3. Java에 template를 Autowired
  
  ```java
  /*UserDAO_Spring.java*/
  @Repository("spring")
  public class UserDAO_Spring implements UserDAO{
  	 @Autowired
  	 private JdbcTemplate template;
  	 /*dao contents...*/
  }
  ```
  
  
  
  5. RowMapper 클래스 구현
  
  ```java
  /*UserRowMapper.java*/
  class UserRowMapper implements RowMapper<UserVO>{
  	@Override
  	public UserVO mapRow(ResultSet rs, int rowNum) throws SQLException {
          UserVO user = new UserVO();
          /*UserVO의 멤버변수는 id, pw뿐이라고 하자*/
          user.setId(rs.getString("id"));
          user.setPw(rs.getString("pw"));
  		return user;
  	}
  }
  ```
  
  
  
  4. SQL명령어 실행문 구현
  
  ```java
  /*UserDAO_Spring.java*/
  @Repository("spring")
  public class UserDAO_Spring implements UserDAO{
  	@Autowired
  	private JdbcTemplate template;
      
      public UserVO getUser(String id){
          String sql = "select * from userinfo where id=?";
          UserVO user = null;
          try{
              user = template.queryForObject(sql,
                                            new Object[] {id},
                                            new UserRowMapper());
          } catch(Exception e){}
          return user;
      }
      
  	public int addUser(UserVO vo){
          String sql = "insert into userinfo (id, pw) values(?, ?)";
          return template.update(sql, vo.getId(), vo.getPw());
      }
      
      public List<UserVO> getUserList(){
          String sql = "select * from userinfo";
          return template.query(sql, new UserRowMapper());
      }
      
      public int updateUser(UserVO vo){
          String sql = "update userinfo set id=?, pw=?";
          return template.update(sql, vo.getId(), vo.getPw());
      }
  }
  ```
  
  
  
* dao - Mybatis 사용법 (beans)

  1.  dataSource를 beans에 올린다. (Spring과 동일)

  ```xml
  <!--pom.xml-->
  <!--spring을 사용할 때는 template 때문에 넣어줬지만 어쨋거나 mybatis에서도 필요하다-->
  <dependency>
  	<groupId>org.springframework</groupId>
  	<artifactId>spring-jdbc</artifactId>
  	<version>4.3.14.RELEASE</version>
  </dependency>
  ```

  

  2.  config.xml 파일을 생성한다.

  ```xml
  <!--mybatis_config.xml-->
  <!DOCTYPE configuration
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
      <typeAliases>
      	<typeAlias type="spring.biz.user.vo.UserVO" alias="uservo"/>
          <!--user_mapper.xml 에서 사용-->
      </typeAliases>
      
      <mappers>
      	<mapper resource="mapper/user_mapper.xml"/>
          <!--user_mapper.xml 파일을 만들어 sql명령어를 처리-->
      </mappers>
  </configuration>
  ```

  

  3.  mapper.xml 파일을 생성한다.

  ```xml
  <!--user_mapper.xml-->
  <!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="user">
      <select id="login" parameterType="uservo" resultType="uservo">
          <!--uservo는 mybatis_config.xml에서 정의되어 있음-->
      	select * from userinfo where id=#{id} and pw=#{pw}
          <!--uservo의 멤버변수와 이름이 같으면 자동으로 get메소드가 파싱-->
      </select>
  	<select id="getuser" resultType="uservo">
      	select * from userinfo where id=#{id}
          <!--파싱할 변수가 하나일 경우에는 parameterType을 지정하지 않아도 됨-->
      </select>
      <insert id="adduser" parameterType="uservo">
      	insert into userinfo(id, pw) values(#{id}, #{pw})
      </insert>
      <select id="list" resultType="uservo">
      	select * from userinfo
      </select>
      <update id="update" parameterType="uservo">
      	update userinfo set id=#{id}, pw=#{pw}
      </update>
  </mapper>
  ```

  

  4.  sqlSessionFractoryBean와 sqlSession을 beans에 올린다.

  ```xml
  <!--pom.xml-->
  <dependency>
  	<groupId>org.mybatis</groupId>
  	<artifactId>mybatis-spring</artifactId>
  	<version>1.3.2</version>
  </dependency>
  ```

  ```xml
  <!--applicationContext.xml-->
  <bean id="sqlSessionFactoryBean"
        class="org.mybatis.spring.SqlSessionFactoryBean"
        p:dataSource-ref="dataSource"
        p:configLocation="classpath:/mybatis_config.xml"/>
  <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
  	<constructor-arg index="0" ref="sqlSessionFactoryBean"/>
  </bean>
  ```

  

  6.  Java구현

  ```xml
  <!--pom.xml-->
  <!--java에서 SqlSession클래스를 import하기 위함-->
  <dependency>
  	<groupId>org.mybatis</groupId>
  	<artifactId>mybatis</artifactId>
  	<version>3.4.6</version>
  </dependency>
  ```

  ```java
  /*DAO_Mybatis.java*/
  @Repository("mybatis")
  public class UserDAO_MyBatis implements UserDAO{
  
      @Autowired
  	SqlSession sqlSession;
      
      public UserVO login(String id, String pw){
          UserVO vo = new UserVO();
          vo.setId(id);
          vo.setPw(pw);
          return sqlSession.selectOne("user.login", vo);
      }
      
      public UserVO getUser(String id){
          return sqlSession.selectOne("user.getuser", id);
      }
      
      public int addUser(UserVO vo){
          return sqlSession.insert("user.add", vo);
      }
     
  	public List<UserVO> getUserList(){
          return sqlSession.selectList("user.list");
      }
      
      public int update(UserVO vo){
          return sqlSession.update("user.update", vo);
      }
  }
  ```

  

* ***dao - Mybatis 사용하기(annotation)***

1. dataSource를 beans에 올린다.
2. sqlSessionFactoryBean을 beans에 올린다.
3. sqlSession을 beans에 올린다.

```xml
<!--pom.xml-->
<repositories>
	<repository>
		<id>codelds</id>
		<url>https://code.lds.org/nexus/content/groups/main-repo</url>
	</repository>
</repositories>

<dependencies>
    <dependency>
		<groupId>com.oracle</groupId>
		<artifactId>ojdbc6</artifactId>
		<version>11.2.0.3</version>
	</dependency>
    
    <dependency>
		<groupId>org.mybatis</groupId>
		<artifactId>mybatis</artifactId>
		<version>3.4.6</version>
	</dependency>
    
    <dependency>
		<groupId>org.mybatis</groupId>
		<artifactId>mybatis-spring</artifactId>
		<version>1.3.2</version>
	</dependency>
    
    <dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-jdbc</artifactId>
		<version>4.3.14.RELEASE</version>
	</dependency>
</dependencies>
```



```xml
<!--applicationContext.xml-->
<bean id="dataSource" 
    class="org.apache.commons.dbcp.BasicDataSource"
	p:driverClassName="oracle.jdbc.OracleDriver"
	p:url="jdbc:oracle:thin:@127.0.0.1:1521:xe" p:username="scott"
	p:password="TIGER" />

<bean id="sqlSessionFactoryBean"
	class="org.mybatis.spring.SqlSessionFactoryBean"
	p:dataSource-ref="dataSource"
	p:configLocation="classpath:/mybatis_config.xml" />

<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
	<constructor-arg index="0" ref="sqlSessionFactoryBean" />
</bean>
```



4. config.xml 파일을 생성한다.

```xml
<!--mybatis_config.xml-->
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <mappers>
    	<mapper class="mappers.UserMapper"/>
        <!--UserMapper.java 파일을 만들어 sql명령어를 처리-->
    </mappers>
</configuration>
```



3. Mapper.java 파일을 생성한다.

```java
/*UserMapper.java*/
public interface UserMapper{
    @Select("select * from userinfo where id=#{id}")
    UserVO getUser(String id);
    
    @Select("select * from userinfo where id='${id}' and pw='${pw}'")
    /*map안의 key값 id, pw에 각각 접근한다.*/
    /*ex) if map contains {"id", "abc"} then ${id} = abc*/
    UserVO login(HashMap<String, String> map);
    
    @Select("select * from userinfo")
    List<UserVO> getUserList();
    
    @Insert("insert into userinfo(id, pw) values(#{id}, #{pw})")
    int addUser(UserVO vo);
    
    @Update("update userinfo set id=#{id}, pw=#{pw}")
    int updateUser(UserVO vo);
    
    @Delete("delete from userinfo where id=#{id}")
    int deleteUser(String id);
    
    /*sql쿼리문을 동적으로 생성하고 싶을 때 사용*/
    /*type에는 자신이 작성한 java클래스명을 입력*/
    /*method에는 해당 class에서 실행할 method명을 입력*/
    /*type.method의 return 값이 sql문이 된다.*/
    @SelectProvider(type = dynamic.DynamicSql.class, method = "dynamicSearch")
    UserVO searchUser(@Param("id")String id, @Param("pw")String pw);
    /*동적 sql생성 메소드에 parameter을 전달하고 싶을 때, map형태로 전달*/
    /*@Param(key) value*/
}
```



4. dao_Java 구현

```java
/*UserDAO_Mybatis.java*/
public class UserDAO_Mybatis implements UserDAO{
    
    /*SqlSession을 바로 @Autowired하지 않는다.*/
    private SqlSession sqlSession = null;
	private UserMapper ui = null;
	
    /*set메소드에서 SqlSession에 접근하여 UserMapper객체까지 생성한다*/
	@Autowired
	public void setSqlSession(SqlSession sqlSession) {
		this.sqlSession = sqlSession;
		ui = sqlSession.getMapper(UserMapper.class);
	}
    
    public UserVO getUser(String id){
        return ui.getUser(id);
    }
    
    public UserVO login(String id, String pw) {
		HashMap<String, String> map = new HashMap<String, String>();
		map.put("id", id);
		map.put("pw", pw);
		return ui.login(map);
	}
    
    public List<UserVO> getUserList() {
		return ui.getUserList();
	}
    
    public int addUser(UserVO user) {
		return ui.addUser(user); 
	}
    
    public int updateUser(UserVO user) {
		return ui.updateUser(user);
	}
    
    public int deleteUser(String id) {
		return ui.deleteeUser(id);
	}
    
    public UserVO searchUser(String id, String pw){
        return ui.searchUser(id, pw);
    }
    
}
```



5. DynamicSql.java 구현

```java
public class DynamicSql {
	public static String dynamicSearch(Map<String, Object> params) {
		String sql = "";
		String id = (String) params.get("id");
		String pw = (String) params.get("pw");
		sql = "select * from userinfo where id='"+id+"' and pw='"+pw+"'";
		return sql;
	}
}
```

