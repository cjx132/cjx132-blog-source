---
title: "Spring"
date: 2020-10-26T16:33:40+08:00
categories:
  - java
tags:
  - java基础
---

# IOC本质

1.  **控制反转loC(Inversion of Control),是一种设计思想，DI(依赖注入)是实现loC的一种方法**。**个人认为所谓控制反转就是：获得依赖对象的方式反转了**。
    
    > -   没有loC的程序中,我们使用面向对象编程。对象的创建与对象间的依赖关系完全硬编码在程序中，对象的创建由程序自己控制。
    > -   控制反转后将对象的创建转移给IOC 容器，IOC 容器在全局维持一个对象实例集合和类名集合，我们在写某个类的时候把这个类依赖的对象注册到容器里，调用这个类的时候再实例化拿出来。
    

2.  采用XML方式配置Bean的时候，Bean的定义信息是和实现分离的，而采用注解的方式可以把两者合为一体，Bean的定义信息直接以注解的形式定义在实现类中，从而达到了零配置的目的。
    
3.  这个就是 IOC 的思想，一个系统通过组织控制和对象的完全分离来实现” 控制反转”。对于依赖注入，这就意味着通过在系统的其他地方控制和实例化依赖对象，从而实现了解耦。
    

-   控制:谁来控制对象的创建，传统应用程序的对象是由程序本身控制创建的,使用Spring后，对象是由Spring来创建的.
-   反转:程序本身不创建对象,而变成被动的接收对象.
-   依赖注入:就是利用set方法来进行注入的.
-   IOC是一种编程思想，由主动的编程变成被动的接收.
-   可以通过newClassPathXmlApplicationContext去浏览下底层源码.  
    OK ,到了现在，我们彻底不用再程序中去改动了，要实现不同的操作，只需要在xml配置文件中进行修改，所谓的loC,一句话搞定:对象由Spring来创建，管理，装配!

# IOC创建对象方式

1.  使用无参构造创建对象，默认
2.  假设要使用有参构造创建对象：需要在bean标签中使用constructor-arg标签

总结：在配置文件加载的时候，容器中管理的全部对象就已经初始化了！（默认创建的某个类的对象只有一个（单例模式），后面可通过配置修改）

# 依赖注入

## 构造器注入

假设要使用有参构造创建对象：需要在bean标签中使用constructor-arg标签

注意：需要在实体类中写有参构造

## Set方式注入【重点】

-   依赖注入：Set注入
    -   依赖：bean对象的创建依赖于容器
    -   注入：bean对象的所有属性，由容器来注入

示例：Student类：使用Set注入

```java
@Data
public class Student {
    private String name;
    private Address address;
    private String []books;
    private List<String> hobbies;
    private Map<String,String> card;
    private Set<String> games;
    private String wife;
    private Properties properties;
}
```
```xml
<bean id="address" class="com.cjx.Address">
        <property name="address" value="12"></property>
    </bean>
    <bean id="student" class="com.cjx.Student">
        <!--       第一种,普通值注入，value-->
        <property name="name" value="cjx"></property>
        
        <!--       第二种,Bean注入，ref-->
        <property name="address" ref="address"></property>
        
        <!--       数组-->
        <property name="books">
            <array>
                <value>红楼梦</value>
                <value>水浒传</value>
                <value>西游记</value>
            </array>
        </property>
        
        <!--        List-->
        <property name="hobbies">
            <list>
                <value>听歌</value>
                <value>敲代码</value>
                <value>看电影</value>
            </list>
        </property>
        
        <!--        Map-->
        <property name="card">
            <map>
                <entry key="身份证" value="121331"></entry>
                <entry key="银行卡" value="1244325"></entry>
            </map>
        </property>
        
        <!--        Set-->
        <property name="games">
            <set>
                <value>abc</value>
                <value>a</value>
                <value>ac</value>
            </set>
        </property>
        
        <!--        null-->
        <property name="wife">
            <null></null>
        </property>
        
        <!--        Properties-->
        <property name="properties">
            <props>
                <prop key="driver">239472</prop>
                <prop key="url">cjx</prop>
                <prop key="username">root</prop>
                <prop key="password">123456</prop>
            </props>
        </property>
    </bean>
```

## 拓展方式注入

使用p命名空间和c命名空间（p命名空间本质上还是Set注入，c命名空间本质上是构造器注入）

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--    p命名空间注入，可以直接注入属性的值：property-->
    <bean id="user" class="com.cjx.User" p:age="13" p:name="cjx"></bean>
    
    <!--    c命名空间注入，可以构造器注入：construct-arg-->
    <bean id="user2" class="com.cjx.User" c:age="12" c:name="cjx"></bean>
</beans>
```

注意：使用时需要导入如上代码中的xml约束

```xml
xmlns:p="http://www.springframework.org/schema/p"
xmlns:c="http://www.springframework.org/schema/c"
```

## bean作用域

![](/images/Java/bean%E4%BD%9C%E7%94%A8%E5%9F%9F.png)

1.  单例模式（Spring默认机制）
    
2.  原型模式：每次从容器中get的时候，都会产生新对象！
    
    ```xml
    <bean id="user" class="com.cjx.User" p:age="13" p:name="cjx" scope="prototype"></bean>
    ```
    
3.  其余的request、session、application、这些个只能在web开发中使用到
    

# Bean的自动装配

自动装配是Spring满足bean依赖的一种方式。Spring会在上下文中自动寻找，并自动给bean配置属性

在Spring中有三种装配的方式

1.  在xml中显示配置
2.  在Java中显式配置
3.  隐式的自动装配bean【重要】

案例分析：一个人有猫和狗两个宠物

## byName&byType自动装配

```xml
<bean id="cat" class="com.cjx.pojo.Cat"></bean>
<bean id="dog" class="com.cjx.pojo.Dog"></bean>
<!--    byName:会自动在容器上下文中查找，和自己对象set方法后面的值对应的beanid-->
<!--    byType:会自动在容器上下文中查找，和自己对象属性类型相同的bean-->
<bean id="people" class="com.cjx.pojo.People" autowire="byType">
    <property name="name" value="cjx"></property>
</bean>
```

小结：

-   byName的时候：需要保证所有的bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值一致
-   byType的时候：需要保证所有的bean的class唯一，并且这个bean需要和自动注入的属性的类型一致

## 使用注解实现自动装配

使用注解须知：

1.  导入约束：context约束
    
2.  配置注解的支持：`<context:annotation-config/>`
    
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            https://www.springframework.org/schema/context/spring-context.xsd">
    
        <context:annotation-config/>
    
    </beans>
    ```
    

**@Autowired**

直接在属性上使用即可，也可以在set方法上使用

```java
public class People {
    //如果显式定义了Autowired的required属性为false,说明这个对象可以为null,否则不允许为空
    @Autowired(required = false)
    private Dog dog;
    @Autowired
    private Cat cat;
    private String name;
}
```

如果@Autowired自动装配的环境比较复杂，自动装配无法通过一个注解【@Autowired】完成的时候、我们可以使用@Qulifier(value=”xxx”)去配合@Autowired的使用，指定一个唯一的bean对象注入!

@Autowired先byType查找，之后byName，如果还是不能确定就需要用@Qulifier(value=”xxx”)指定

**@Resource注解**

```java
public class People {
    @Resource(name = "dog1")
    private Dog dog;
    @Resource(name = "cat1")
    private Cat cat;
}
```

-   功能与@Autowired类似，也是实现自动装配的注解。
-   区别是：@Resource(name = “dog1”)相当于@Autowired和@Qulifier(value=”xxx”)的组合

# 使用注解开发

前提：在配置文件中必须包含如下内容

```xml
<!--    指定要扫描的包，这个包下的注解就会生效-->
<context:component-scan base-package="com.cjx"/>
<context:annotation-config/>
```

-   bean
    
    @Component：组件，放在类上，说明这个类被Spring管理了，就是Bean!
    
-   属性如何注入： @Value(“cjx”)
    
    ```java
    @Component
    public class User {
        //相当于<property name="name" value="cjx"></property>
        @Value("cjx")
        public String name;
    
        public void setName(String name) {
            this.name = name;
        }
        
    }
    ```
    
-   衍生的注解
    
    @Component有几个衍生注解，我们在web开发中，会按照mvc三层架构分层
    
    -   dao 【@Repository】
    -   service 【@Service】
    -   controller 【@Controller】（该注解标注的类中的方法，如果返回值为字符串，默认去找对应的jsp页面）
    
    这四个注解功能都是一样的，都是代表将某个类注册到Spring中，装配Bean
    

-   自动装配
    
    见上一章节：Bean的自动装配
    
-   作用域
    
    ```java
    @Component
    @Scope("prototype")
    public class User {
        //相当于<property name="name" value="cjx"></property>
        @Value("cjx")
        public String name;
    
    }
    ```
    

# AOP

## 什么是AOP

AOP (Aspect Oriented Programming)意为:面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP 是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

![](/images/Java/aop.png)

## AOP在Spring中的作用

提供声明式事务；允许用户自定义切面

-   横切关注点:跨越应用程序多个模块的方法或功能。即是，与我们业务逻辑无关的，但是我们需要关注的部分，就是横切关注点。如日志,安全,缓存,事务等等…
-   切面(ASPECT) :横切关注点被模块化的特殊对象。即，它是一 个类。
-   通知(Advice) :切面必须要完成的工作。即，它是类中的一一个方法。
-   目标(Target) :被通知对象。
-   代理(Proxy) :向目标对象应用通知之后创建的对象。
-   切入点(PointCut) :切面通知执行的“地点”的定义。
-   连接点UointPoint) :与切入点匹配的执行点。

![](/images/Java/aop1.png)

SpringAOP中，通过Advice定义横切逻辑，Spring中支持5种类型的Advice:

![](/images/Java/aop3.png)

即AOP在不改变原有代码的情况下，去增加新的功能

## 使用Java的方式配置Spring

完全不使用Spring的xml配置，全权交给Java来做

案例

实体类

```java
//这里这个注解的意思，就是说明这个类被Spring接管了，注册到了容器中
@Component
public class User {
    private String name;

    public String getName() {
        return name;
    }

    @Value("cjx")//属性注入值
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

配置文件

```java
//这个也会Spring容器托管，注册到容器中，因为他本来就是一个@Component
// @Configuration代表这是一个配置类，就和我们之前看的beans.xml
@Configuration
@ComponentScan("com.cjx.pojo")
public class MyConfig {

    //注册一个bean,就相当于我们之前写的一个bean标签
    //这个方法的名字，就相当于bean标签中的id属性
    //这个方法的返回值，就相当于bean标签中的class属性
    @Bean
    public User user(){
        return new User();//就是返回要注入到bean的对象
    }
}
```

测试类

```java
public class MyTest {
    public static void main(String[] args) {
        //如果完全使用了配置类方式去做，我们就只能通过AnnotationConfigApplicationContext上下文来获取容器，通过配置类的class对象加载
        ApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
        User user = context.getBean("user", User.class);
        System.out.println(user.getName());

    }
}
```

## 使用Spring实现Aop

导入相关jar包

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.6</version>
</dependency>
```

### 方法一：使用Spring的API接口【主要SpringAPI接口实现】

1.  定义Log类：在方法前执行

```java
@Component
public class Log implements MethodBeforeAdvice {
    //method:要执行的目标对象的方法
    //args:参数
    //target:目标对象
    @Override
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName() + "的" + method.getName() + "被执行了");
    }
}
```

2.  xml中配置Bean；配置aop

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--    注解配置Bean:指定要扫描的包，这个包下的注解就会生效-->
    <context:component-scan base-package="com.cjx"/>
    <context:annotation-config/>

    <!--    方式一：使用原生Spring API接口-->
    <!--    配置aop:需要导入aop的约束-->
    <aop:config>
        <!--        切入点：expression:表达式，execution(要执行的位置！)-->
        <aop:pointcut id="pointcut" expression="execution(* com.cjx.service.UserServiceImpl.*(..))"/>

        <!--        执行环绕增加-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
    </aop:config>
</beans>
```

### 方法二：自定义实现AOP【主要是切面定义】

1.  自定义切面类
    
    ```java
    @Component
    public class DiyPointCut {
        public void before(){
            System.out.println("方法执行前");
        }
        public void after(){
            System.out.println("方法执行后");
        }
    }
    ```
    
2.  xml中配置Bean；配置aop（在aop中自定义切面）
    
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:aop="http://www.springframework.org/schema/aop"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            https://www.springframework.org/schema/context/spring-context.xsd
            http://www.springframework.org/schema/aop
            https://www.springframework.org/schema/aop/spring-aop.xsd">
    
        <!--    注解配置Bean:指定要扫描的包，这个包下的注解就会生效-->
        <context:component-scan base-package="com.cjx"/>
        <context:annotation-config/>
    
            <aop:config>
        <!--        自定义切面，ref要引用的类-->
                <aop:aspect ref="diyPointCut">
        <!--            切入点-->
                    <aop:pointcut id="point" expression="execution(* com.cjx.service.UserServiceImpl.*(..))"/>
        <!--            通知-->
                    <aop:before method="before" pointcut-ref="point"/>
                    <aop:after method="after" pointcut-ref="point"/>
                </aop:aspect>
            </aop:config>
    </beans>
    ```
    

### 方法三：使用注解实现

1.  使用注解自定义切面类
    
    ```java
    @Component
    @Aspect//标注这个类是一个切面
    public class AnnotationPointCut {
        @Before("execution(* com.cjx.service.UserServiceImpl.*(..))")
        public void before(){
            System.out.println("方法执行前");
        }
        @After("execution(* com.cjx.service.UserServiceImpl.*(..))")
        public void after(){
            System.out.println("方法执行后");
        }
        //在环绕增强中，我们可以给定一个参数，代表我们要获取处理切入的点
        @Around("execution(* com.cjx.service.UserServiceImpl.*(..))")
        public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
            System.out.println("环绕前");
            //获得签名
            System.out.println(proceedingJoinPoint.getSignature());
            //执行方法
            Object proceed = proceedingJoinPoint.proceed();
            System.out.println("环绕后");
            System.out.println(proceed);
        }
    }
    ```
    
2.  在xml中配置Bean；并开启aop注解支持并配置代理实现方式
    
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:aop="http://www.springframework.org/schema/aop"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            https://www.springframework.org/schema/context/spring-context.xsd
            http://www.springframework.org/schema/aop
            https://www.springframework.org/schema/aop/spring-aop.xsd">
    
        <!--    指定要扫描的包，这个包下的注解就会生效-->
        <context:component-scan base-package="com.cjx"/>
        <context:annotation-config/>
        
        <!--    开启注解支持      JDK(默认  proxy-target-class="false")  cglib(proxy-target-class="true")-->
        <aop:aspectj-autoproxy proxy-target-class="true"/>
    </beans>
    ```
    

# 整合Mybatis

[mybatis-spring官网](http://mybatis.org/spring/zh/index.html)

1.  编写数据源配置
    
2.  sqlSessionFactory
    
3.  sqlSessionTemplate
    
    spring-dao.xml配置文件如下：
    
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:aop="http://www.springframework.org/schema/aop"
           xmlns:tx="http://www.springframework.org/schema/tx"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            https://www.springframework.org/schema/context/spring-context.xsd
            http://www.springframework.org/schema/tx
            https://www.springframework.org/schema/tx/spring-tx.xsd
            http://www.springframework.org/schema/aop
            https://www.springframework.org/schema/aop/spring-aop.xsd">
    
        <!--    指定要扫描的包，这个包下的注解就会生效-->
        <context:component-scan base-package="com.cjx"/>
        <context:annotation-config/>
    
        <!--    开启注解支持      JDK(默认  proxy-target-class="false")  cglib(proxy-target-class="true")-->
        <aop:aspectj-autoproxy proxy-target-class="false"/>
    
        <!--    1.使用Spring的数据源代替Mybatis数据源-->
        <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
            <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=UTF-8"/>
            <property name="username" value="root"/>
            <property name="password" value="123456"/>
        </bean>
    
        <!--    2.sqlSessionFactory-->
        <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
            <property name="dataSource" ref="dataSource"/>
            <!--        必须绑定Mybatis配置文件-->
            <property name="configLocation" value="classpath:mybatis-config.xml"/>
            <property name="mapperLocations" value="classpath:com/cjx/mapper/*.xml"/>
        </bean>
    
        <!--    3.SqlSessionTemplate就是我们使用的sqlSession-->
        <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
            <!--        只能使用构造器注入，因为没有set方法-->
            <constructor-arg index="0" ref="sqlSessionFactory"></constructor-arg>
        </bean>
    
        <!--    5.将自己写的实现类，注入到Spring中-->
        <bean id="userMapper" class="com.cjx.mapper.UserMapperImpl">
            <property name="sqlSession" ref="sqlSession"/>
        </bean>
        <bean id="userMapper2" class="com.cjx.mapper.UserMapperImpl2">
            <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
        </bean>
    ```
    
4.  需要给接口加实现类
    
    第一种方式：
    
    ```java
    public class UserMapperImpl implements UserMapper{
        //我们的所有操作，都使用sqlSession来执行，在原来，现在都使用SqlSessionTemplate
        private SqlSessionTemplate sqlSession;
    
        public void setSqlSession(SqlSessionTemplate sqlSession) {
            this.sqlSession = sqlSession;
        }
    
        @Override
        public List<User> selectUser() {
            UserMapper mapper = sqlSession.getMapper(UserMapper.class);
            List<User> list = mapper.selectUser();
            return list;
        }
    }
    ```
    
    第二种方式：需要注意spring-dao.xml对应的配置
    
    ```java
    public class UserMapperImpl2 extends SqlSessionDaoSupport implements UserMapper {
    
        @Override
        public List<User> selectUser() {
            SqlSession sqlSession = getSqlSession();
            UserMapper mapper = sqlSession.getMapper(UserMapper.class);
            List<User> list = mapper.selectUser();
            return list;
        }
    }
    ```
    
5.  将自己写的实现类，注入到Spring中
    
6.  测试使用即可
    
    ```java
    @Test
    public void testB() {
        ApplicationContext context = new ClassPathXmlApplicationContext("spring-dao.xml");
        UserMapper userMapper = (UserMapper) context.getBean("userMapper2");
        System.out.println(userMapper.selectUser());
    }
    ```
    

# spring中的事务管理

声明式事务：AOP

在spring配置文件中增加如下配置即可

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx
        https://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--    1.使用Spring的数据源代替Mybatis数据源-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=UTF-8"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </bean>

    <!--    2.配置声明式事务-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <constructor-arg ref="dataSource"/>
    </bean>

    
    <!--    结合aop实现事的织入-->
    
    <!--    3.配置事务通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <!--        给哪些方法配置事务-->
        <!--        配置事务的传播特性 propagation=  -->
        <tx:attributes>
            <tx:method name="add" propagation="REQUIRED"/>
            <tx:method name="delete" propagation="REQUIRED"/>
            <tx:method name="update" propagation="REQUIRED"/>
            <tx:method name="query" read-only="true"/>
            <tx:method name="*" propagation="REQUIRED"/>
        </tx:attributes>
    </tx:advice>

    <!--    4.配置事务切入-->
    <aop:config>
        <aop:pointcut id="txPointCut" expression="execution(* com.cjx.mapper.*.*(..))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
    </aop:config>
</beans>
```

编程式事务：需要在代码中，进行事务的管理

为什么需要事务?

-   如果不配置事务，可能存在数据提交不一致的情况下:
-   如果我们不在Spring中去配置声明式事务，我们就需要在代码中手动配置事务
-   事务在项目的开发中十分重要，设计到数据的一致性和完整性问题，不容马虎

# 面试相关

## spring ioc加载过程

![image-20210422174852875](https://cjx132.github.io/picture-bed/img/image-20210422174852875.png)

## BeanFactory和FactoryBean的区别

BeanFactory用于生产Bean

FactoryBean是一个特殊的Bean，实现这一接口的类需要重写getObject方法，最终调用getObject方法返回Bean

## Bean生命周期

class–>BeanDefinition–>BeanFactory组建完成–>BeanFactoryPostProcessor–>new User()–>填充属性–>Aware(回调)–>初始化–>aop–>单例池(Map<BeanName,对象>)

![image-20210326131832044](https://cjx132.github.io/picture-bed/img/image-20210326131832044.png)

## 三级缓存

### 为什么需要二级缓存？

二级缓存只要是为了分离成熟Bean和纯净Bean(未注入属性)的存放， 防止多线程中在Bean还未创建完成时读取到的Bean时不完整的。所以也是为了保证我们getBean是完整最终的Bean，不会出现不完整的情况。

### 为什么需要三级缓存？

1.  Bean生命周期中何时进行动态代理：两种情况：没有循环依赖的时候在初始化之后进行动态代理；有循环依赖的时候在实例化之后进行动态代理。原因：如果有循环依赖，那么在实例化之后已经注入了属性，因此必须要提前进行AOP
2.  使用三级缓存解决AOP的问题以及进行解耦

### 解决循环依赖的步骤

1.  A 创建过程中需要 B，于是 A 将自己的ObjectFactory放到三级缓里面 ，去实例化 B；
2.  B 实例化的时候发现需要 A，于是 B 先查一级缓存，没有，再查二级缓存，还是没有，再查三级缓存，找到了！ 然后把三级缓存里面的这个 A 放到二级缓存里面，并删除三级缓存里面的 A， B 顺利初始化完毕，将自己放到一级缓存里面（此时B里面的A依然是创建中状态）；
3.  然后回来接着创建 A，此时 B 已经创建结束，直接从一级缓存里面拿到 B ，然后完成创建，并将自己放到一级缓存里面。

![image-20210326131558435](https://cjx132.github.io/picture-bed/img/image-20210326131558435.png)

### 不能解决构造函数的循环依赖

构造函数在实例化时就会执行，无法缓存对象

### 不能解决多例下的循环依赖：

多例根本没有单例池

## AOP

### 介绍下AspectJ和AOP和关系

Spring只用到了AspectJ的切点表达式以及相关的概念。为什么不用：需要额外的aj文件的编译器。

### Spring AOP中aspect、advise、pointcut、advisor分别有什么意义?

### 介绍AOP有几种实现方式

1.  接口
2.  注解
3.  xml
4.  AspectJ编译

### 简单介绍Spring-AOP的底层原理

### AOP的流程

1.  解析切面
    
    ![image-20210423150049793](https://cjx132.github.io/picture-bed/img/image-20210423150049793.png)
    
2.  创建动态代理
    
    ![image-20210423150154601](https://cjx132.github.io/picture-bed/img/image-20210423150154601.png)
    
3.  调用
    
    **AOP通过责任链的方式调用**：责任链需要实现统一的抽象，通过循环或者递归的方式进行责任链的调用
    
    ![image-20210423150256628](https://cjx132.github.io/picture-bed/img/image-20210423150256628.png)
