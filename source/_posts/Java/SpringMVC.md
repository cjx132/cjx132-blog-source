---
title: "SpringMVC"
date: 2020-10-29T21:19:04+08:00
categories:
  - java
tags:
  - java基础
---

# SpringMVC工作流程

## SpringMVC接口解释

-   **前端控制器DispatcherServlet**
    
    > 作用：接收请求，响应结果，相当于转发器，中央处理器。有了dispatcherServlet减少了其它组件之间的耦合度。  
    > dispatcherServlet是整个流程控制的中心，由它调用其它组件处理用户的请求。
    > 
    > （DispatcherServlet本质也是一个Servlet，控制走Controller的请求；除此之外，需要在spring-mvc中配置静态资源默认servlet：用于处理静态资源请求。controller之外的请求不走DispatcherServlet）
    
-   **处理器映射器HandlerMapping**
    
    > 作用：根据请求的url查找Handler
    
-   **处理器适配器HandlerAdapter**
    
    > 作用：按照特定规则（HandlerAdapter要求的规则）去执行Handler。通过HandlerAdapter对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。
    
-   **处理器Handler(需要工程师开发)**
    
    > **注意：编写Handler时按照HandlerAdapter的要求去做，这样适配器才可以去正确执行Handler**  
    > 平常叫做Controller。Handler 是继DispatcherServlet前端控制器的后端控制器，在DispatcherServlet的控制下Handler对具体的用户请求进行处理。
    
-   **视图解析器View resolver**
    
    > 作用：进行视图解析，根据逻辑视图名解析成真正的视图（view）
    
-   **视图View(需要工程师开发jsp…)**
    
    > View是一个接口，实现类支持不同的View类型（jsp、freemarker、pdf…）
    

## 执行流程

[参考链接](https://juejin.im/post/6844903735303503880)

Spring MVC比较粗浅的执行过程如下图：

![](/images/Java/springMVC%E6%B5%81%E7%A8%8B.png)

Spring MVC详细执行过程如下图：

![](/images/Java/springMVC%E6%B5%81%E7%A8%8B1.png)

1.  用户发送请求至前端控制器DispatcherServlet。
2.  DispatcherServlet收到请求调用HandlerMapping处理器映射器。
3.  处理器映射器找到具体的处理器(可以根据xml配置、注解进行查找)，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。
4.  DispatcherServlet调用HandlerAdapter处理器适配器。
5.  HandlerAdapter经过适配调用具体的处理器(Controller，也叫后端控制器)。
6.  Controller执行完成返回ModelAndView给HandlerAdapter。
7.  HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。
8.  DispatcherServlet将ModelAndView传给ViewReslover视图解析器。
9.  ViewReslover解析后返回具体View，这个view不是完整的，**仅仅是一个页面（视图）名字**，且没有后缀名。
10.  DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）。
11.  DispatcherServlet响应用户。

## 代码编写流程与执行流程一致

[具体配置可参考链接](https://juejin.im/post/6844903735303503880#heading-3)：这里仅提供思路

1.  配置web.xml中的DispatcherServlet
2.  创建springmvc配置文件并配置映射器
3.  配置适配器
4.  创建并实现处理器（Handle也叫Controller）
5.  Spring配置文件中配置处理器
6.  配置视图解析器
7.  页面接收返回参数

# RestFul 风格

资源操作方式：使用POST、DELETE、PUT、GET，使用不同方式对资源进行操作。分别对应 添加、 删除、修改、查询。

与传统风格之间的区别:

1.  url形式上

> 传统风格：[http://localhost:8080/add?a=1&b=2](http://localhost:8080/add?a=1&b=2)
> 
> RestFul风格：[http://localhost:8080/add/1/2](http://localhost:8080/add/1/2)

2.  使用传统的风格：一个url对应一个操作
3.  **使用RESTful操作资源** ：url相同的情况下，可以通过不同的方式（POST、DELETE、PUT、GET）实现不同的效果，实现url的复用

代码示例：

```java
@Controller
public class RestFulController {

//    传统风格：http://localhost:8080/add?a=1&b=1
//    RestFul风格：http://localhost:8080/add/1/1

    //    @PathVariable注解将变量与url映射
    @GetMapping("/add/{a}/{b}")
    public String test(@PathVariable int a, @PathVariable int b, Model model) {
        int res = a + b;
        model.addAttribute("msg", res + "test,get");
        //默认就是转发,返回的是view,走视图解析器
        return "hello";
    }

    //    同一url的POST方法
    @RequestMapping(value = "/add/{a}/{b}", method = RequestMethod.POST)
    public String test1(@PathVariable int a, @PathVariable int b, Model model) {
        int res = a + b;
        model.addAttribute("msg", res + "test1,post");
        //重定向,返回的不是view,不走视图解析器
        return "redirect:/index.jsp";
    }
}
```

补充：

@GetMapping 是一个组合注解：它所扮演的是 @RequestMapping(method =RequestMethod.GET) 的一个快捷方式。

类似的有：

-   @GetMapping
-   @PostMapping
-   @PutMapping
-   @DeleteMapping
-   @PatchMapping

# 重定向和转发

WEB-INF目录下的内容服务器端可以访问到，通过url不能访问到。

> 因此，在服务端转发可以访问WEB-INF目录下的内容，重定向则不能访问

### **`forward:`**

-   默认的方式，但是也是可以使用`return "forward:login"`
-   返回的一定是一个`view`,经过视图解析器之后会转发到指定的视图

### **`redirect:`**

-   重定向 ： `return "redirect:login.do"`
-   返回的是一个Controller方法的路径，而不是一个view，这个不会经过视图解析器，而是直接跳转

# 接收请求参数

1.  **提交的域名称和处理方法的参数名一致**
    
    直接在方法的参数中接收即可
    
2.  **提交的域名称和处理方法的参数名不一致**
    
    使用注解`@RequestParam("username") String name`表示前端传递的参数名必须为username,同时与name相对照，建议名称是否一致，均加上这个注解
    
3.  **提交的是一个对象**
    
    要求提交的表单域和对象的属性名一致 , 参数使用对象即可
    

示例如下：

```java
@Controller
@RequestMapping("/user")
public class UserController {
    @GetMapping("/t1")
//    http://localhost:8080/user/t1?username=cjx
    public String test1(@RequestParam("username") String name, Model model) {
        //1.接收前端参数
        System.out.println("接收到的参数为：" + name);
//        2.将返回的结果传递给前端，model
        model.addAttribute("msg", name);
//        3.视图跳转
        return "hello";
    }

    //    前端接收的是一个对象：id,name,age
    /*
    1.接收前端传递的参数，判断参数的名字，假设名字直接在方法上，可以直接使用
    2.假设传递的是一个对象User,匹配User对象中的字段名；如果名字一致则OK,否则，匹配不到
     */
    @GetMapping("/t2")
    public String test2(User user) {
        System.out.println(user);
        return "hello";
    }
}
```

# 乱码问题解决

## 在web.xml中配置如下内容

```xml
<!--    使用springMVC自带的filter-->
    <filter>
        <filter-name>encoding</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

补充：在`<url-pattern>/*</url-pattern>`中

> 1.  /\*匹配jsp页面
> 2.  /不匹配jsp页面

相关注解

@Controller：标注在类上，类中所有返回值为String的方法走视图解析器

@RestController：标注在类上，类中所有方法不走视图解析器，直接返回字符串

@ResponseBody：不会走视图解析器，直接返回字符串

## JackSon乱码问题解决

在springmvc.xml配置如下内容

```xml
<mvc:annotation-driven>
    <mvc:message-converters register-defaults="true">
        <!-- 启动Spring MVC的注解功能，完成请求和注解POJO的映射 注解请求映射
        默认是ISO-88859-1，避免乱码这里设置为UTF-8 -->
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <property name="supportedMediaTypes" value="text/html;charset=UTF-8" />
        </bean>
        <!-- 启动JSON格式的配置,自动将格式转换成JSON格式，不需要其他类 -->
        <bean id="jacksonMessageConverter" class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
            <property name="supportedMediaTypes" value="application/json;charset=UTF-8" />
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```

# 拦截器

SpringMVC的处理器拦截器类似于Servlet开发中的过滤器Filter,用于对处理器进行预处理和后处理。开发者可以自己定义一些拦截器来实现特定的功能。

**过滤器与拦截器的区别：**拦截器是AOP思想的具体应用。

**过滤器**

-   servlet规范中的一部分，任何java web工程都可以使用
-   在url-pattern中配置了/\*之后，可以对所有要访问的资源进行拦截

**拦截器**

-   拦截器是SpringMVC框架自己的，只有使用了SpringMVC框架的工程才能使用
-   拦截器只会拦截访问的控制器方法， 如果访问的是jsp/html/css/image/js是不会进行拦截的

## 自定义拦截器步骤

1.  编写拦截器实现HandlerInterceptor接口的全部方法
    
    ```java
    public class MyInterceptor implements HandlerInterceptor {
        //在请求处理的方法之前执行
        //如果返回true执行下一个拦截器
        //如果返回false就不执行下一个拦截器
        public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
            System.out.println("------------处理前------------");
            return true;
        }
    
        //在请求处理方法执行之后执行
        public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
            System.out.println("------------处理后------------");
        }
    
        //在dispatcherServlet处理后执行,做清理工作.
        public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
            System.out.println("------------清理------------");
        }
    }
    ```
    
2.  在spring-mvc中配置拦截器
    
    ```xml
    <!--关于拦截器的配置-->
    <mvc:interceptors>
       <mvc:interceptor>
           <!--/** 包括路径及其子路径-->
           <!--/admin/* 拦截的是/admin/add等等这种 , /admin/add/user不会被拦截-->
           <!--/admin/** 拦截的是/admin/下的所有-->
           <mvc:mapping path="/**"/>
           <!--bean配置的就是拦截器-->
           <bean class="com.cjx.interceptor.MyInterceptor"/>
       </mvc:interceptor>
    </mvc:interceptors>
    ```
    

# 实战案例

1.  ssm框架整合：[参考链接](https://mp.weixin.qq.com/s?__biz=Mzg2NTAzMTExNg==&mid=2247484004&idx=1&sn=cef9d881d0a8d7db7e8ddc6a380a9a76&scene=19#wechat_redirect)
2.  文件上传下载：[参考链接](https://mp.weixin.qq.com/s?__biz=Mzg2NTAzMTExNg==&mid=2247484026&idx=1&sn=eba24b51963e8c3293d023cbcf3318dc&scene=19#wechat_redirect)
3.  登录验证：[参考链接](https://mp.weixin.qq.com/s?__biz=Mzg2NTAzMTExNg==&mid=2247484026&idx=1&sn=eba24b51963e8c3293d023cbcf3318dc&scene=19#wechat_redirect)
