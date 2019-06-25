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
  	<constructor-arg ref="dataSource" />
  </bean>
  ```

  

