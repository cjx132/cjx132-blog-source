---
title: "Spring Boot"
date: 2020-11-01T15:33:07+08:00
categories:
  - java
tags:
  - java基础
---

![](/images/Java/%E5%AD%A6%E4%B9%A0%E8%B7%AF%E7%BA%BF.png)

# Spring Boot是什么

-   约定大于配置
-   SpringBoot其实不是什么新的框架，它默认配置了很多框架的使用方式，就像maven整合了所有的jar包，spring boot整合了所有的框架
-   [SpringBoot官方文档](https://docs.spring.io/spring-boot/docs/2.3.5.RELEASE/reference/htmlsingle/#using-boot-auto-configuration)

# 启动类

## 自动配置原理

**资料参考**

-   关于每个注解的作用：[参考](https://blog.csdn.net/u010502101/article/details/78817581?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.channel_param)
-   **关于整个自动装配及启动流程的详细解释【重点参考】**：[参考](https://blog.csdn.net/qq_28289405/article/details/81302498)
-   **关于整个自动装配源码剖析的脑图【理解调用流程】**：[参考](https://www.processon.com/view/link/5fa161d67d9c081baf11f97c)

**结论**

-   springboot所有自动配置都是在启动的时候扫描并加载。 所以，自动配置真正实现是从classpath中搜寻所有的META-INF/spring.factories配置文件，并将其中对应的org.springframework.boot.autoconfigure包下的配置项，通过反射实例化为对应标注了@Configuration的JavaConfig形式的IOC容器配置类，然后将这些都汇总成为一个实例并加载到IOC容器中。
-   但是不一定生效，要判断条件是否成立，只要导入了对应的start,就有对应的启动器了，有了启动器,我们自动装配就会生效，然后就配置成功：核心注解：@ConditionalOnXXX，如果这里面的条件都满足，才会生效
-   @Import(AutoConfigurationImportSelector.class)，借助AutoConfigurationImportSelector，@EnableAutoConfiguration可以帮助SpringBoot应用将所有符合条件的@Configuration配置都加载到当前SpringBoot创建并使用的IoC容器。

**自动配置幕后英雄：SpringFactoriesLoader详解**

-   SpringFactoriesLoader属于Spring框架私有的一种扩展方案，其主要功能就是从指定的配置文件META-INF/spring.factories加载配置。
-   配合@EnableAutoConfiguration使用的话，它更多是提供一种配置查找的功能支持，即根据@EnableAutoConfiguration的完整类名org.springframework.boot.autoconfigure.EnableAutoConfiguration作为查找的Key,获取对应的一组@Configuration类
-   所以，@EnableAutoConfiguration自动配置的魔法骑士就变成了：**从classpath中搜寻所有的META-INF/spring.factories配置文件，并将其中org.springframework.boot.autoconfigure.EnableutoConfiguration对应的配置项通过反射（Java Refletion）实例化为对应的标注了@Configuration的JavaConfig形式的IoC容器配置类，然后汇总为一个并加载到IoC容器。**

**结论**

1.  Spring Boot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值
2.  将这些值作为自动配置类导入容器，自动配置类就生效 ，帮我们进行自动配置工作
3.  以前我们需要自己配置的东西，自动配置类都帮我们解决了
4.  整个J2EE的整体解决方案和自动配置都在springboot-autoconfigure的jar包中
5.  它将所有需要导入的组件以全类名的方式返回，这些组件就会被添加到容器中
6.  它会给容器中导入非常多的自动配置类(xxxAutoConfiguration) , 就是给容器中导入这个场景需要的所有组件，并配置好这些组件
7.  有了自动配置类，免去了我们手动编写配置注入功能组件等的工作

**精髓**

1.  SpringBoot启动会加载大量的自动配置类
    
2.  我们看我们需要的功能有没有在SpringBoot默认写好的自动配置类当中;
    
3.  我们再来看这个自动配置类中到底配置了哪些组件; (只要我们要用的组件存在在其中，我们就不需要再手动配置了)
    
4.  给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们只需要在yaml配置文件中指定这些属性的值即可实现自定义配置;
    

xxxxAutoConfigurartion:自动配置类;给容器中添加组件（bean）

xxxxProperties:封装配置文件中相关属性;

# yaml

-   yaml文件配合`@ConfigurationProperties(prefix = "spring.mvc")`注解实现WebMvcProperties类中属性的注入（也就是依赖注入）
    
    （WebMvcProperties类可理解为`xxxProperties`类的一个代表）
    
    > 1.  代替了在WebMvcProperties类中使用@Value()注解进行属性注入的方法，实现了解耦
    >     
    > 2.  经过这种方式注入的WebMvcProperties类，经配置交由IOC容器管理
    >     
    > 3.  WebMvcProperties类由IOC容器管理后，也可被其他JavaConfig类使用，读取其中的属性值
    >     
    >     （JavaConfig类相当于beans.xml，用于配置bean）
    >     
    >     （比如：WebMvcAutoConfiguration自动配置类（JavaConfig）读取WebMvcProperties类中的属性，实现JavaConfig中bean的配置）
    >     
    >     > 具体使用方法：
    >     > 
    >     > 1.  JavaConfig类在类头使用`@EnableConfigurationProperties({xxxProperties.class})`注解
    >     > 2.  在JavaConfig类中读取并使用`xxxProperties`类经yaml注入的属性值
    >     > 3.  因此，通过这个JavaConfig类我们便实现了springboot的自定义配置
    >     
    > 4.  这也是SpringBoot自动装配的原理**【重点】**
    >     
    

-   application.yaml配置文件配置位置及优先级（优先级自上而下依次降低）
    
    1.  `file:./config/`
    2.  `file:./config/*/`
    3.  `file:./`
    4.  `classpath:/config/`
    5.  `classpath:/`
-   多环境切换
    
    方法1：利用配置文件优先级实现配置覆盖
    
    方法2：在一个yaml文件中声明。如下：
    
    ```yaml
    server:
      port: 8081
    
    #环境dev激活使用
    spring:
      profiles:
        active: dev
    
    #多个配置之间中间使用---分割
    ---
    server:
      port: 8082
    
    #指定环境为dev
    spring:
      profiles: dev
    ---
    server:
      port: 8083
    
    #指定环境为test
    spring:
      profiles: test
    ```
    

# SpringBoot Web开发

## 静态资源导入

1.  在springboot中，我们可以使用以下方式处理静态资源
    -   webjars `localhost:8080/webjars/`
    -   public，static，resources `localhost:8080/`
2.  优先级：resources>static(默认)>public
3.  templates目录下的内容只能通过controller访问
4.  首页(index.html)直接放在上述文件夹下即可

## 扩展MVC配置

-   SpringBoot在自动配置很多组件的时候，先看容器中有没有用户自己配置的(如果用户自己配置@bean)，如果有就用用户配置的，如果没有就用自动配置的;如果有些组件可以存在多个，比如我们的视图解析器，就将用户配置的和自己默认的组合起来!
    
-   以下代码
    
    1.  实现`WebMvcConfigurer`接口，扩展`WebMvcAutoConfiguration`；
        
        > -   如果添加@EnableWebMvc该注解，则WebMvcAutoConfiguration自动配置类失效
        > -   (原因在于自动配置类上的@ConditionalOnxxx注解不满足某个条件了)
        

2.  定义自己的视图解析器类；
    
3.  把自定义的视图解析器在MyMvcConfiguration中以@bean方式配置交由SpringIOC容器管理。实现MVC的扩展配置
    

```java
//扩展springMVC(WebMvcConfigurer相当于WebMvcAutoConfiguration的一个扩展)
@Configuration
@EnableWebMvc
//如果添加@EnableWebMvc该注解，则WebMvcAutoConfiguration自动配置类失效
//(原因在于自动配置类上的@ConditionalOnxxx注解不满足某个条件了)

//1.实现WebMvcConfigurer接口,扩展WebMvcAutoConfiguration
public class MyMvcConfiguration implements WebMvcConfigurer {

    //3.把自定义的视图解析器交由SpringIOC容器管理
    @Bean
    public MyViewResolver myViewResolver(){
        return new MyViewResolver();
    }

    //2.自定义一个自己的视图解析器：定义一个实现了ViewResolver的类
    public static class MyViewResolver implements ViewResolver{

        @Override
        public View resolveViewName(String s, Locale locale) throws Exception {
            return null;
        }
    }
}
```

-   在SpringBoot中，有非常多的xxxxConfiguration，只要看见这个东西，说明这个JavaConfig类要对xxxxAutoConfiguration自动配置类做扩展或者覆盖掉

## Druid使用

[参考](https://blog.csdn.net/weixin_38187317/article/details/81562571)

## Mybatis整合

1.  导入包
    
    ```xml
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>2.1.3</version>
    </dependency>
    ```
    
2.  在application.yml中配置
    
    ```yml
    spring:
      datasource:
        password: 123456
        url: jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf8
        driver-class-name: com.mysql.cj.jdbc.Driver
        username: root
    
    #整合mybatis
    mybatis:
      type-aliases-package: com.cjx.pojo
      mapper-locations: classpath:mybatis/mapper/*.xml
    ```
    
3.  编写mapper接口和对应的mapper.xml文件
    
4.  service层调用dao层
    
5.  controller调用service层
    

## Swagger配置

1.  pom文件

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
```

2.  配置Swagger

```java
@Configuration
@EnableSwagger2//开启swagger2
public class SwaggerConfig {

    //实现分组
    //A组开发的

    @Bean
    public Docket docket1() {
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("A");
    }

    //cjx组开发的
    //配置了swagger的Docket的Bean实例
    @Bean
    public Docket docket(Environment environment) {
        //设置要显示的Swagger环境
        Profiles profiles = Profiles.of("dev", "test");

        //通过environment.acceptsProfiles判断是否处在自己设定的环境中
        boolean isEnable = environment.acceptsProfiles(profiles);

        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .enable(isEnable)//是否启动swagger,true启动,false关闭
                .groupName("cjx")//实现分组
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.cjx.controller"))
//                .paths(PathSelectors.ant("/cjx/**"))
                .build()
                ;
    }

    //配置swagger信息=apiInfo
    public ApiInfo apiInfo() {
        Contact contact = new Contact("cjx", "12", "12@qq.com");
        return new ApiInfo(
                "cjx",
                "test",
                "v1.0",
                "12",
                contact,
                "sd",
                "dds",
                new ArrayList<>()
        );
    }
}
```

3.  controller中使用

```java
@RestController
public class HelloController {
    @RequestMapping(value = "/hello",method = RequestMethod.GET)
    public String hello(){
        return "hello";
    }

    //只要返回的接口中存在实体类，他就会被扫描到swagger中
    @PostMapping(value = "/user")
    public User user(){
        return new User();
    }

    @ApiOperation("Hello2接口")
    //restful风格可以接收前端传来的非对象参数
    @RequestMapping(value = "/hello2/{name}",method = RequestMethod.GET)
    public String hello2(@ApiParam("用户名")@PathVariable("name") String username){
        return "hello2"+username;
    }

    @PostMapping(value = "/postt")
    public User postt(User user){
        return user;
    }
}
```

## 异步，定时，邮件

1.  异步
    
    使用场景：controller及时返回前端部分信息，业务层耗时长的代码开启异步在后台执行
    
    需要两个注解：
    
    -   主启动类：@EnableAsync//开启异步注解功能
    -   自定义异步方法：@Async//告诉spring这是一个异步的方法
2.  定时
    
    使用场景：实现定时任务
    
    需要两个注解：
    
    -   主启动类：@EnableScheduling//开启定时功能的注解
    -   自定义定时方法：@Scheduled(cron = “0 56 13 \* \* ?”)
    
    ```java
    //在一个特定的时间执行这个方法
    //cron表达式
    //秒 分 时 日 月 周几
    @Scheduled(cron = "0 56 13 * * ?")
    public void hello(){
        System.out.println("hello,你被执行了");
    }
    ```
    
3.  邮件
    
    -   .pom导入包
        
        ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        ```
        
    -   application.properties配置
        
        ```properties
        spring.mail.host=smtp.qq.com
        spring.mail.username=邮箱名
        spring.mail.password=动态密码
        #qq需要开启加密验证
        spring.mail.properties.mail.smtp.ssl.enable=true
        ```
        
    -   测试使用如下
        
        ```java
        @Autowired
        JavaMailSenderImpl javaMailSender;
        @Test
        void contextLoads() {
            //一个简单的邮件
            SimpleMailMessage message = new SimpleMailMessage();
            message.setSubject("申请");
            message.setText("谢谢");
            message.setTo("15366763357@163.com");
            message.setFrom("2805132402@qq.com");
            javaMailSender.send(message);
        }
        
        
        @Test
        void contextLoads2() throws MessagingException {
            //一个复杂的邮件
            MimeMessage message = javaMailSender.createMimeMessage();
        
            //组装
            MimeMessageHelper helper = new MimeMessageHelper(message,true);
        
        
            //正文
            helper.setSubject("申请");
            helper.setText("<p style='color:red'>复杂</p>",true);
        
            //附件
            helper.addAttachment("1.txt",new File("D:\\桌面文件\\任务.txt"));
        
            helper.setTo("15366763357@163.com");
            helper.setFrom("2805132402@qq.com");
            javaMailSender.send(message);
        }
        ```
