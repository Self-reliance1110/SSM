# 利用框架编程流程

* 导包
* 写配置
* 测试

# Spring

* Core Container 模块
  * Bean,spring - beans
  * Core,spring - core
  * Context,spring - context
  * SpEl:表达式(Spring expression language)，spring - expression ,
* AOP+Aspect 模块 面向切面和切面模块,spring - aop
* 数据访问模块，Data Access

## 使用监听器启动Spring容器

IOC容器创建和销毁合适的时机，项目启动，IOC创建，项目关闭，IOC销毁

在web.xml文件下进行配置，依赖于spring-web包

```
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
</context-param>
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```



## IOC(Inversion Of control)

### 控制：资源的获取方式

* 主动式：需要什么资源都需要自己创建

  ```
  class BookServlet
  {
  	BookService bs = new BookService();
  }
  ```

* 被动式：需要的资源交给容器创建

  ```
  class BookServlet
  {
  	@Autowired
  	BookService bs;
  }
  ```

* 容器：管理所有的类，容器可以自动探查到哪一个类需要用到另一个类，然后容器帮我们创建一个这样的类并赋值过去。资源获取由主动new变为被动的接收。

* DI(Dependency Injection)：依赖注入

  容器可以自动探查到哪一个类需要用到另一个类，通过反射的方式给对象赋值

### 框架编程流程

* 导包 ：spring- core,beans,context,expression.jar，commons-logging-1.2.jar

  ```
  <!-- https://mvnrepository.com/artifact/org.springframework/spring-beans -->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-beans</artifactId>
      <version>5.2.17.RELEASE</version>
  </dependency>
  <!-- https://mvnrepository.com/artifact/org.springframework/spring-core -->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>5.2.17.RELEASE</version>
  </dependency>
  <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.2.17.RELEASE</version>
  </dependency>
  <!-- https://mvnrepository.com/artifact/org.springframework/spring-expression -->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-expression</artifactId>
      <version>5.2.17.RELEASE</version>
  </dependency>
  
  ```

* 写配置

  编写application.xml.配置文件

  ```
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
  
  
      <bean id="person" class="cn.miligou.bean.Person">
          <property name="age" value="18"/>
          <property name="name" value="zhangsan"></property>
      </bean>
  
  </beans>
  ```

  

* 测试

### Bean实例的获取

#### 通过id获取

#### 通过bean的类型获取

* 如果在ioc容器中这个类型的bean有多个，会报错
* 但是可以在传id是也传入类型，就不用在赋值时转换类型

#### 调用有参构造器

* bean标签下有constructor-arg标签，使用几个标签就是调用几个参数的构造方法

  ```
  <bean id="person" class="cn.miligou.bean.Person" >
      <constructor-arg name="age" value="18"></constructor-arg>
      <constructor-arg name="name" value="zhangsan"/>
  </bean>
  ```

* 可以省略name属性，但是参数顺序必须严格按照构造器参数顺序排列（不推荐）

#### 通过p命名空间

* 在xml中命名空间是用来防止标签重复的

* 首先添加p命名空间

  ```
  xmlns:p="http://www.springframework.org/schema/p"
  ```

* 使用p命名空间给属性赋值

  ```
  <bean id="person01" class="cn.miligou.bean.Person" p:age ="18" p:name = "zhangsan"/>
  
  ```

### 为各种属性赋值

#### 基本类型

* 使用property标签，类型自动转换
* null值，引用类型，不写value默认为null

#### 引用类型

* 引用外部bean

  ```
  <property name="car" ref="car01"></property>
  ```

* 引用内部bean

  ```
  <property name="car" >
      <bean class="cn.miligou.bean.car">
        <property name="carName" value="宝马"></property>
      </bean>
  </property>
  ```

#### List类型

```
<property name="books">
    <list>
        <bean class="cn.miligou.bean.Book">
        <property name="bookName" value="活着/>
        </bean>
        <bean class="cn.miligou.bean.Book">
        <property name="bookName" value="傲慢与偏见"/>
        </bean>
    </list>
</property>
```

#### Map类型

```
<property name="maps">
    <map>
        <entry key="key01" value="value01"></entry>
        <entry key = "key02">
        <bean class="cn.miligou.bean.Book">
        <property name="bookName" value="傲慢与偏见"/>
        </bean>
        </entry>
    </map>
</property>
```

##### util命名空间

util命名空间可以创建一个外部可以引用的集合,相当于

new了一个LinkedHashMap()

```
<util:map id="utilmap">
    <entry key="key01" value="value01"></entry>
    <entry key = "key02">
        <bean class="cn.miligou.bean.Book">
        <property name="bookName" value="傲慢与偏见"/>
        </bean>
    </entry>
</util:map>
```

在bean中只需要引用就可以使用这个map

```
<property name="maps" ref = "utilmap">
    
</property>
```



#### Properties类型

```
<property name="properties">
    <props>
    	<prop key="111">????</prop>
    </props>
</property>
```



#### 级联属性赋值

为属性的属性赋值

```
<property name = "object.object" value = ""/>
```

#### 配置信息的继承

```
<bean id="person03" class="cn.miligou.bean.Person">
    <property name="name" value="zhangsan"/>
    <property name="age" value="18"/>
</bean>
<bean id="person04" class="cn.miligou.bean.Person" parent="person03">
</bean>
```

parent属性，可以继承一个bean的配置信息，如果在bean中重新配置会覆盖该信息

### 静态工厂

* 定义car类

```
public class car {
    private String carName;
    private String color;
    private int price;
}
```

* 定义一个car静态工厂类

  ```
  public class CarStaticFactory {
      public static car getCar(String 			carName,String color,int price)
      {
          car car = new car(carName,color,price);
          System.out.println("CarStaticFactory");
          return car;
      }
  }
  ```

* xml配置

  * factory - method指定用哪个方法创建

  ```
  <bean id="car02" class="cn.miligou.bean.CarStaticFactory" factory-method="getCar">
          <constructor-arg name="carName" value="奔驰"></constructor-arg>
          <constructor-arg name="color" value="red"/>
          <constructor-arg name="price" value="1"/>
  </bean>
  ```

### 实例工厂

* 先配置实例工厂类Bean

  ```
  <bean id="carInstanceFactory" class="cn.miligou.bean.CarInstanceFactory">
  
  </bean>
  ```

  

* 配置car用哪个实例工厂创建

  * factory - bean指定用哪个工厂类
  * factory - method指定用哪个方法创建

  ```
  <bean id="car03" class="cn.miligou.bean.car" factory-bean="carInstanceFactory" factory-method="getCar">
          <constructor-arg name="carName" value="奔驰"></constructor-arg>
          <constructor-arg name="color" value="red"/>
          <constructor-arg name="price" value= "1"/>
  </bean>
  
  ```

### FactoryBean

* 编写一个Factory的实现类

  ```
  public class MyFactoryBeanImpl implements FactoryBean<car> {
      @Override
      public car getObject() throws Exception {
          car car = new car("奥迪","blue",99);
          return car;
      }
  
      @Override
      public Class<?> getObjectType() {
          return car.class;
      }
  
      @Override
      public boolean isSingleton() {
          return false;
      }
  }
  
  ```

  

* 在配置文件中注册该类

  ```
  <bean id="myFactoryBean" class="cn.miligou.bean.MyFactoryBeanImpl">
  
      </bean>
  ```

* getBean获取的是Factory的实现类getObject()返回的对象

### Spring管理连接池

以c3p0为例

* 导包

  ```
  <!-- https://mvnrepository.com/artifact/com.mchange/c3p0 -->
  <dependency>
      <groupId>com.mchange</groupId>
      <artifactId>c3p0</artifactId>
      <version>0.9.5.2</version>
  </dependency>
  <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.17</version>
  </dependency>
  ```

* 在配置文件中配置Bean

  ```
  <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
      <property name="user" value="root"/>
      <property name="password" value="59192584"/>
      <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/nmid?serverTimezone=GMT%2B8&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
      <property name="driverClass" value="com.mysql.cj.jdbc.Driver"/>
  </bean>
  ```

* 获取bean

  ```
  DataSource dataSource = app.getBean(ComboPooledDataSource.class);
  Connection connection = dataSource.getConnection();引入外部属性文件
  ```

#### 引入外部属性文件

* 编辑属性文件

* 在Spring配置文件中添加context命名空间

* 使用context命名空间引入外部文件

  ```
  <context:property-placeholder location="classpath:jdbc.properties">
  ```

* 使用${属性名}来获取属性文件里的值

  



##### 常见问题

* java连接数据库出现的Access denied for user 'é??'@'localhost' (using password: YES)问题

  username是Spring中的一个关键字

在java中有通过配置文件来获取用户名和密码以及driverClass，url进行连接的方法 而在xml的el表达式中${username}实际上是系统用户名而不是数据库连接用户名，也就是计算机的用户名而在java编译读取中，它会通username读取系统用户名而导致连接数据库出错



### XML自动装配

在bean标签的子标签有property，property标签是手动赋值

在bean标签的属性有atuowire属性

* autowire:default	不自动装配
* autowire:no	不自动装配
* autowire:byName    通过属性名查找是否存在以属性名命名的bean，没找到为null
* autowire:byType    通过类型查找是否存在相同类型的bean，不过这个类型的bean有多个会报错，没找到为null
* autowire:constructor     通过构造器

### SpEL，#{}

* 运算表达式
* 引用bean的属性值
* 引用其他bean
* 调用静态方法，#{T(全类名).静态方法(参数1，参数2...)}
* 调用非静态方法，#{对象id.非静态方法()}

### Spring注解

* Controller：控制器
* Service：业务逻辑
* Repository：数据库操作
* Component：其他

#### @Autowired(依赖注入)

原理：

* 先按照类型到ioc容器中寻找组件

  * 找到一个，直接赋值
  * 没找到，编译异常
  * 找到多个，以变量名作为id继续匹配
    * 匹配上，赋值
    * 没有匹配上，报错

* @Qualifier(""),指定一个id，不要让spring使用变量名作为id去匹配组件

* required属性，设置为flase，当没有匹配到组件，赋值为null

* @Resource,@Inject也有自动装配的功能

  **@Autowired是spring定义的，最强大；@Resource是java定义的**

  **二者区别，@Resource扩展性更强，@Autowired离开spring就没办法用了**

#### 注解使用步骤

* 给需要的组件添加注解

* 扫描需要加入的组件，依赖于context命名空间

* 一定要导入spring  - aop 包，

* ```
  <context:component-scan base-package="cn"/>
  ```

  将base-package包下及子包下的所有添加了注解的类加入到容器中

* getBean默认id为类名首字母小写

### Spring单元测试

* 导入spring-test包

* ```
  @ContextConfiguration(locations = "classpath:applicationContext.xml")
  @RunWith(SpringJUnit4ClassRunner.class)
  public class test2 {
      
  }
  ```



### IOC细节

* 容器中对象的创建时机：在容器创建时创建
* bean中的property属性名不是由类属性名决定，而是由getter，setter方法名决定

## AOP

#### 动态代理

##### 动态代理的实现

java.lang.reflect.Proxy 类的newProxyInstance()方法

- | `newProxyInstance(ClassLoader loader, Class<?>[] interfaces,  InvocationHandler h)` | Returns a proxy instance for the specified  interfaces that dispatches method invocations to the specified invocation  handler.  译: 返回指定接口的代理实例，该代理实例将方法调用调度到指定的调用处理程序。 |
  | ------------------------------------------------------------ | ------------------------------------------------------------ |
  |                                                              |                                                              |

* ClassLoader 类加载器，代理类.getClass().getLoader()

* interfaces 接口,代理类.getClass().getInterfaces()

* InvocationHandler 方法执行器

  ```
  InvocationHandler invocationHandler = new InvocationHandler() {
      @Override
      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable 	{
  
      Object result = method.invoke(需要代理的类对象，final属性, args);
      return result;
      }
  };
  ```

  

##### 动态代理缺点

如果目标对象没有实现接口，就无法创建代理对象

#### AOP术语

![](C:\Users\李烘\Desktop\电子书\AOP.png)

#### 基于注解的SpringAOP功能

* 导包，spring-aspects(基础),{cglib,aopalliance,aspect.weaver}(加强，没有实现接口也能创建动态代理对象)

* 写配置

  * 将目标类和切面类加入到ioc容器中

  * 告诉Spring哪个是切面类@Aspect

  * 告诉Spring，切面类的方法什么时候在哪里运行

    * @Before,使用用例如下

      ```
      @Before("execution(public int cn.miligou.bean.CalculatorImpl.add(int,int))")
      ```

    * @After

    * @AfterThrowing

    * @AfterReturning

    * @Around

* 开启基于注解的AOP功能

  * 在spring配置文件添加aop命名空间，使用标签

    ```
    <aop:aspectj-autoproxy/>
    ```

* 测试

  * 要从ioc容器中拿到对象

  * ```
    @Autowired
    private Calculator calculator;
    ```

  * **用接口类型去接收对象**

#### AOP细节

* AOP的底层就是动态代理，IOC保存的组件是代理对象，所以要**用接口类型去接收对象**

* 切入点表达式写法

  execution(访问权限名 返回类型 方法全类名(参数列表))

  通配符：

  * ‘*’ ：
    * 匹配一个或多个字符
    * 匹配任意一个参数
  * ‘..’ ：
    * 匹配任意多个参数
    * 匹配任意多个路径

* **JoinPoin**t获取目标方法的信息，**是JoinPoint，不是Joinpoint**

  在通知方法的参数列表加入JoinPoint就可以调用其方法获取信息

* 方法返回值，在AfterReturning中有returning属性设置参数名，然后在通知方法的参数列表添加该参数

  抛出异常有属性throwing，其他操作差不多

* Spring对通知方法的约束，唯一要求的就是参数列表不能乱写

* 抽取可重用的切入点表达式

  * 随便声明一个空实现的返回void的方法

  * 给方法上面标注@Pointcut注解

  * 注解里写上切点表达式

  * 调用的时候如下

    ```
    @Pointcut("execution(public int *.*(..))")
    public void method(){}
    
    @Before(value = "method()")
    public ...
    ```

* @Around 环绕通知，Spring最强大通知方法，依赖的是ProceedingJoinPoint类,将四种通知合为一体

  就是相当于一个动态代理

  ```
  @Around("execution(public int cn.miligou.bean.CalculatorImpl.*(int,int))")
      public static Object myAround(ProceedingJoinPoint proceedingJoinPoint)
      {
          Object[] args = proceedingJoinPoint.getArgs();//被代理对象方法参数列表
  
          Object proceed =null;//被代理对象方法返回值
          try {
              System.out.println("前置方法");
              proceed = proceedingJoinPoint.proceed(args);//执行目标方法，相当于method.invoke(obj,args)
              System.out.println("Return方法");
          } catch (Throwable throwable) {
              System.out.println("异常方法");
              throwable.printStackTrace();
          }finally {
              System.out.println("后置方法");
      }
      return proceed;//返回被代理对象方法返回值
  }
  ```

  

#### 基于配置的AOP功能

* 将目标类和切面类加入到IOC容器中

* 告诉Spring哪个是切面类

* 告诉切面类方法在何时何地执行

  ```
  <bean id="calculator" class="cn.miligou.bean.CalculatorImpl"/>
  <bean id="logUtils" class="cn.miligou.bean.LogUtils"/>
      <aop:aspectj-autoproxy/>
          <aop:config>
          <aop:aspect ref="logUtils">
          <aop:before method="Before" pointcut="execution(public int cn.miligou.bean.CalculatorImpl.*(int,int))"/>
      </aop:aspect>
  </aop:config>
  ```

  相比配置，注解要更方便快捷，但对于通知方法需要调用外部依赖，配置更加完善

  **重要的切面写配置，不重要的写注解**

## JdbcTemplate

* 导入spring-orm，jdbc，tx包

* 配置数据源

* 配置JdbcTemplate到IOC容器

  ```
  <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
      <property name="user" value="root"/>
      <property name="password" value="59192584"/>
      <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/nmid?serverTimezone=GMT%2B8&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
      <property name="driverClass" value="com.mysql.jdbc.Driver"/>
  </bean>
  <bean class="org.springframework.jdbc.core.JdbcTemplate">
      <constructor-arg name="dataSource" ref="dataSource">
      </constructor-arg>
  </bean>
  ```

  

## 声明式事务

Spring的事务依赖于[PlatformTransactionManager]接口，如果是使用jdbc数据源，就使用其实例

DataSourceTransactionManager

* 配置事务管理器并让其控制事务,导入面向切面的依赖jar包

  ```
  <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
          <property name="dataSource" ref="dataSource"></property>
  </bean>
  ```

* 开启基于注解的事务控制功能，依赖于tx名称空间

  ```
  <tx:annotation-driven transaction-manager="transactionManager"/>
  ```

### 事务的属性

* Isolation isolation() default Isolation.DEFAULT;事物的隔离级别
* Propagation propagation() default Propagation.REQUIRED;事物的传播行为
* Class<? extends Throwable>[] noRollbackFor() default {};哪些异常事务可以不回滚
* String[] noRollbackForClassName() default {};String全类名
* Class<? extends Throwable>[] rollbackFor() default {};哪些异常事务需要回滚
* String[] rollbackForClassName() default {};String全类名
* boolean readOnly() default false;设置事务的只读属性
* int timeout() default -1;超出指定时长后，主动终止，回滚
  * 以秒为单位

### 事务的异常的处理

* 运行时异常：默认回滚
* 编译时异常：要么try-catch，要么throw，默认不回滚

### 数据库事务并发问题
假设现在有两个事务: TransactionO1 和TransactionO2并发执行。。

* 脏读
  * Transaction01将某条记录的AGE值从20修改为30。
  * Transaction02读取了Transaction01 更新后的值: 30。 
  * Transaction01回滚，AGE 值恢复到了20。
  * Transaction02读取到的30就是-一个无效的值。
* 不可重复读
  * Transaction01读取了AGE值为20。
  * Transaction02将AGE值修改为30。
  * Transaction01再次读取AGE值为30，和第一次读取不一-致。
* 幻读
  * Transaction01读取了STUDENT表中的一部分数据。
  * TransactionO2向STUDENT表中插入了新的行。
  * Transaction01读取了STUDENT表时，多出了一些行。

### 隔离级别

* 读未提交: READ UNCOMMITTED。
  允许Transaction01读取Transaction02未提交的修改。。
* 读己提交: READ COMMITTED。
  要求Transaction01只能读取Transaction02已提交的修改。。
* 可重复读: REPEATABLE READ。
  确保Transaction01可以多次从一个字段中读取到相同的值，即Transaction01
  执行期间禁止其它事务对这个字段进行更新。。
* 串行化:SERIALIZABLE
  确保Transaction01可以多次从一个表中读取到相同的行，在Transaction01执
  行期间，禁止其它事务对这个表进行添加、更新、删除操作。可以避免任何并发问题，
  但性能十分低下。。（不用）

### 事务的传播行为(事物的传播和事物的行为)

当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。例如:方
法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行。
事务的传播行为可以由传播属性指定。Spring 定义了7种类传播行为。。
传播属性 

* REQUIRED 

  如果有事务在运行，当前的方法就在这个事务内运行，否则，就启
  动一个新的事务，并在自己的事务内运行

* REQUIRED_ NEW
  当前的方法必须启动新事务，并在它自己的事务内运行.如果有事
  务正在运行，应该将它挂起

* SUPPORTS
  如果有事务在运行，当前的方法就在这个事务内运行.否则它可以不
  运行在事务中.

* NOT_ SUPPORTE

  当前的方法不应该运行在事务中.如果有运行的事务，将它挂起

* MANDATORY
  当前的方法必须运行在事务内部，如果没有正在运行的事务，就抛
  出异常

* NEVER
  当前的方法不应该运行在事务中。如果有运行的事务，就抛出异常

* NESTED
  如果有事务在运行，当前的方法就应该在这个事务的嵌套事务内运
  行.否则，就启动一个新的事务，并在它自己的事务内运行.

### 细节

有事务的业务逻辑，ioc容器中保存的是该业务逻辑的代理对象

### 基于xml的事务控制

依赖于tx和aop命名空间

。。。。。。。。。。。。。。。



## 



## SpringIOC源码

思路：从helloworld开始，调试每一个方法作用
