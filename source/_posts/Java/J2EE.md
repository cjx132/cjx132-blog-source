---
title: "J2EE"
date: 2020-10-17T00:01:55+08:00
categories:
  - java
tags:
  - java基础
---

# Http

HTTP：运行在TCP之上，默认端口80

Https：安全的，默认端口443

## 两个时代

-   http1.0:
    -   HTTP/1.0:客户端可以与web服务器连接后，只能获得一个Web资源，断开连接
-   http2.0:
    -   HTTP/1.1:客户端可以与web服务器连接后，可以获得多个Web资源。

## 请求方式

Get，Post，HEAD，DELETE，PUT，TRACT…

-   get:请求能够携带的参数比较少，大小有限制，会在浏览器的URL地址栏显示数据内容，不安全，但高效
-   post:请求能够携带的参数没有限制，不会在浏览器的URL地址栏显示数据内容，安全，但不高效

## 响应状态码

200：请求响应成功 200

3xx：请求重定向

4xx：找不到资源 404

-   资源不存在

5xx：服务器代码错误 500 502：网关错误

**常见面试题：**

当你的浏览器中地址栏输入地址并回车的一瞬间到页面能够展示出来，经历了什么？

# Maven

## Maven资源导出问题

在pom.xml文件添加如下内容：

```xml
<!--    build中配置resources，来防止我们资源导出失败的问题-->
  <build>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>false</filtering>
      </resource>
      <resource>
        <directory>src/main/java</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>false</filtering>
      </resource>
    </resources>
  </build>
```

# Servlet

一个web应用的web.xml文件：首先要按照对应的tomcat服务器中的样例网站文件的web.xml复制头信息；之后配置自己的servlet等。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1"
         metadata-complete="false">

  <!--  web.xml是配置我们的web应用的核心配置-->
    
</web-app>
```

## Servlet调用流程

![Servlet调用流程图](https://stepimagewm.how2j.cn/7461.png)

## Servlet生命周期

![生命周期](https://stepimagewm.how2j.cn/1593.png)

-   实例化：当用户通过浏览器输入一个路径，这个路径对应的servlet被调用的时候，该Servlet就会被实例化；构造方法 **只会执行一次**，所以Servlet是**单实例的**
-   初始化：init(ServletConfig) 方法；无论访问了多少次Serlvet，init初始化 **只会执行一次**
-   提供服务：在service()中编写我们的**业务代码**
-   销毁：调用destroy()；销毁时机：1.web应用重新启动；2.关闭tomcat
-   被回收：当该Servlet被销毁后，就满足垃圾回收的条件了。 当下一次垃圾回收GC来临的时候，就有可能被回收。

## ServletContext

Web容器在启动时，会为每个web程序都创建一个对应的ServletContext对象，它代表了当前的web应用

通过this对象即可获取：`ServletContext servletContext = this.getServletContext();`

### 共享数据

我在这个Servlet中保存的数据，可以在另外一个Servlet中拿到

Servlet1保存数据：`servletContext.setAttribute("username",name);`

Servlet2获取Servlet1保存的数据：

`String string=(String)servletContext.getAttribute("username");`

### 获取初始化参数

web.xml文件

```xml
<context-param>
  <param-name>url</param-name>
  <param-value>jdbc:mysql://localhost:3306/mybatis</param-value>
</context-param>
```

Servlet3获取初始化参数：

```java
String url = servletContext.getInitParameter("url");//jdbc:mysql://localhost:3306/mybatis
```

### 请求转发

```java
servletContext.getRequestDispatcher("/get").forward(req, resp);//转发到/get路径对应的Servlet
```

### 读取资源文件

Properties

-   在java目录下新建properties
-   在resources目录下新建properties

发现：都被打包到了同一个路径下：classes，我们俗称这个路径为classpath：

思路：需要一个文件流：**同时在pom.xml文件添加资源导出相关配置**

```properties
username=root
password=123456
```
```java
public class Servlet05 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        InputStream inputStream = this.getServletContext().getResourceAsStream("/WEB-INF/classes/com/cjx/servlet/aa.properties");
        Properties properties = new Properties();
        properties.load(inputStream);
        String username = properties.getProperty("username");
        String password = properties.getProperty("password");
        resp.getWriter().println(username+":"+password);
    }
}
```

## HttpServletResponse

### 重定向

一个web资源收到客户端请求后，他会通知客户端去访问另外一个web资源，这个过程叫重定向`resp.sendRedirect("绝对路由");`重定向编码为302。**重定向的方法参数：路由需要虚拟路径**

## HttpServletRequest

### 请求转发

`req.getRequestDispatcher("/get").forward(req,resp);`请求转发编码为307。**请求转发的方法参数：路由不需要虚拟路径**

## 重定向（客户端跳转）和请求转发（服务端跳转）的区别：

![服务端跳转与客户端跳转图示](https://stepimagewm.how2j.cn/1602.png)

# cookie、session

## 会话

浏览器打开到关闭，这段过程叫做一次会话

一个网站怎么证明你来过？

客户端 服务端

1.  服务端给客户端一个信件，客户端下次访问服务端带上信件就可以了；这个信件就是cookie
2.  服务器登记你来过了，下次你来的时候我来匹配你；session

## 保存会话的两种技术

**cookie**

-   客户端技术（服务端通过响应发给客户端cookie，客户端通过请求带上cookie）

**session**

-   服务器技术，利用这个技术可以保存用户的会话信息，我们可以把信息和数据放在session中

常见：网站登录之后，你下次不用登录，第二次访问直接就上去了

## Cookie

1.  从请求中拿到cookie信息
2.  服务器响应给客户端cookie

```java
//1.从请求中拿到cookie信息
Cookie[] cookies = req.getCookies();//获得cookie
cookie.getName()//获得cookie中的key
cookie.getValue()//获得cookie中的value
    
//2.服务器新建一个cookie并响应给客户端保存
new Cookie("lastLoginTime", System.currentTimeMillis()+"");//新建一个cookie
cookie.setMaxAge(24*60*60);//设置cookie的有效期
resp.addCookie(cookie);//响应给客户端一个cookie
```

**cookie:一般会保存在本地的用户目录下appdata;**

细节:

-   一个cookie只能保存一个信息
-   一个web站点可以给浏览器发送多个cookie，最多存放20个cookie
-   cookie大小有限制4kb
-   300个cookie浏览器上限

删除cookie:

-   不设置有效期，关闭浏览器，自动失效
-   设置有效期时间为0

乱码解决方案：

```java
//编码
Cookie cookie = new Cookie("name", URLEncoder.encode("常家鑫","utf-8"));
//解码
out.write(URLDecoder.decode(cookie.getValue(),"utf-8"));
```

## Session(重点)

什么是Session:

-   服务器会给**每一个用户（浏览器）**创建一个session对象
-   一个session独占一个浏览器，只要浏览器没有关闭，这个session就存在
-   用户登录之后，整个网站它都可以访问！–>保存用户的信息，保存购物车的信息

Session和Cookie的区别：

-   Cookie是把用户的数据写给用户的浏览器，浏览器保存（可以保存多个）
-   Session把用户的数据写到用户独占的Session中，服务器端保存（保存重要的信息，减少服务器资源的浪费）
-   Session对象由服务器创建

使用场景：

-   保存一个登录用户的信息
-   购物车信息
-   在整个网站中经常会使用的数据，我们将它保存在Session中

使用Session：

```java
HttpSession session = req.getSession();//1.从请求中获得Session对象（由服务器自己创建，不需自己创建）
session.setAttribute("name",new People("常家鑫",22));//2.将信息保存在session中

String id = session.getId();//3.读取session中的信息
if(session.isNew()){
    resp.getWriter().write("session创建成功,ID:"+id);
}else {
    resp.getWriter().write("session已经在服务器中存在了,ID:"+id);
}

//session创建的时候做了以下事情（猜想）：在客户端保存了一个键值对为JSESSIONID:id的cookie
//Cookie jsessionid = new Cookie("JSESSIONID", id);
//resp.addCookie(jsessionid);
```
```java
HttpSession session = req.getSession();
session.removeAttribute("name");//4.在另一个servlet中删除保存在session中的信息
session.invalidate();//5.注销session（注销之后会立即产生一个新的session）
```

会话自动过期：web.xml配置

```xml
<!--  设置session失效时间-->
  <session-config>
<!--    1分钟之后失效-->
    <session-timeout>
      1
    </session-timeout>

  </session-config>
```

# JavaBean

就是一个普通类（实体bean），JavaBean特定的写法：

-   必须要有一个无参构造
-   属性必须私有化
-   必有有对应的set/get 方法

一般用来和数据库字段做映射：ORM（对象关系映射）

-   表–>类
-   字段–>属性
-   行记录–>对象

# MVC设计模式

通过控制器(C)，让不同的视图(V)，显示不同的数据(M)

![MVC架构图](/images/Java/mvc.png)

Model

-   业务处理：业务逻辑（Service)
-   数据持久层：CRUD (Dao)

View

-   展示数据
-   提供链接发起Servlet请求

Controller(Servlet)

-   接收用户的请求：(req:请求参数、Session信息…)
-   交给业务层处理对应的代码
-   控制视图的跳转

# 经典三层架构

![实体类对应图](/images/Java/%E5%AE%9E%E4%BD%93%E7%B1%BB%E5%AF%B9%E5%BA%94%E5%9B%BE.png)

![三层架构](/images/Java/%E4%B8%89%E5%B1%82%E6%9E%B6%E6%9E%84.png)

# Filter（重要）

Filter：过滤器，用来过滤网站的数据；**可在进入Servlet之前与离开Servlet之后进行附加的操作**

`filterChain.doFilter(servletRequest,servletResponse);`本行代码相当于从过滤器中进入之前的Servlet

上述代码行之前的代码是在进入Servlet之前进行操作，上述代码行之后的代码是在离开Servlet之后进行操作

## 开发步骤与servlet基本类似：

1.  实现Filter接口，重写其中的全部方法
    
    ```java
    //字符乱码过滤器
    public class Filter1 implements Filter {
    @Override
    //初始化：web服务器启动，就已经初始化了，随时准备过滤对象出现！
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("过滤器初始化");
    }
    
    @Override
    //chain:链
    /*
    1.过滤器中的所有代码在过滤特定请求的时候都会执行
    2.必须调用filterChain.doFilter(servletRequest,servletResponse);方法让请求继续往下走,去执行servlet中的代码
     */
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        servletRequest.setCharacterEncoding("utf-8");
        servletResponse.setCharacterEncoding("utf-8");
        servletResponse.setContentType("text/html;charset:utf-8");
        System.out.println("过滤器执行前");
        //让我们的请求继续走，去执行servlet中的代码，如果不写，程序到这里就被拦截停止！
        filterChain.doFilter(servletRequest,servletResponse);
        System.out.println("过滤器执行后");
    }
    
    @Override
    //销毁：web服务器关闭的时候，过滤器会销毁
    public void destroy() {
        System.out.println("过滤器销毁");
    }
    }
    ```
    
2.  在web.xml中配置Filter（和servlet配置方法一样）
    
    ```xml
    <filter>
        <filter-name>f1</filter-name>
        <filter-class>com.cjx.filter.Filter1</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>f1</filter-name>
    <!--        只要是/servlet的任何请求，都会经过这个过滤器-->
        <url-pattern>/servlet/*</url-pattern>
    </filter-mapping>
    ```
    

## 常见应用

-   处理中文乱码（见上述代码）
    
-   登录验证：用户登录之后才能进入主页，用户注销之后不能进入主页了！
    
    1.  用户登录之后，向session中放入用户的数据
        
    2.  进入主页的时候要判断用户是否已经登录
        
        ```java
        @Override
        public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        
            Object attribute = ((HttpServletRequest) servletRequest).getSession().getAttribute(Constant.USERSESSIONID);
            if(attribute==null){
                ((HttpServletResponse)servletResponse).sendRedirect("/error.jsp");
            }
            filterChain.doFilter(servletRequest,servletResponse);
        }
        ```
        

# 监听器

1.  实现一个监听器的接口
    
    ```java
    //统计网站在线人数，统计session
    public class Listener1 implements HttpSessionListener {
        @Override
        //一旦创建session就会触发一次这个事件
        public void sessionCreated(HttpSessionEvent httpSessionEvent) {
            System.out.println(httpSessionEvent.getSession().getId());
            ServletContext servletContext = httpSessionEvent.getSession().getServletContext();
            Object count = servletContext.getAttribute("OnlineCount");
            if (count == null) {
                count = new Integer(1);
            } else {
                count=(Integer)((Integer)count).intValue()+1;
            }
            servletContext.setAttribute("OnlineCount", count);
    
        }
    
        @Override
        //一旦销毁session就会触发一次这个事件
        public void sessionDestroyed(HttpSessionEvent httpSessionEvent) {
            ServletContext servletContext = httpSessionEvent.getSession().getServletContext();
            Object count = servletContext.getAttribute("OnlineCount");
            if (count == null) {
                count = new Integer(0);
            } else {
                count=(Integer)((Integer)count).intValue()-1;
            }
            servletContext.setAttribute("OnlineCount", count);
        }
    }
    ```
    

2.  web.xml中注册监听器
    
    ```xml
    <listener>
        <listener-class>com.cjx.listener.Listener1</listener-class>
    </listener>
    ```
    

# JDBC

1.  maven导入数据库驱动
    
2.  JDBC固定步骤
    
    ```java
    //配置信息
    //useUnicode=true&characterEncoding=utf-8 解决中文乱码
    String url="jdbc:mysql://localhost:3306/jdbc?useUnicode=true&characterEncoding=utf-8";
    String username="root";
    String password="123456";
    
    //1.加载驱动
    Class.forName("com.mysql.jdbc.Driver");
    //2.连接数据库，代表数据库
    Connection connection = DriverManager.getConnection(url, username, password);
    //3.向数据库发送SQL的对象Statement:CRUD
    Statement statement = connection.createStatement();
    //4.编写SQL
    String sql="select * from users";
    //受影响的行数，增删改都使用executeUpdate即可
    //int i = statement.executeUpdate(sql);
    //5.执行SQL,返回一个ResultSet；结果集
    ResultSet resultSet = statement.executeQuery(sql);
    while(resultSet.next()){
        System.out.println("id=" + resultSet.getObject("id"));
        System.out.println("name=" + resultSet.getObject("name"));
        System.out.println("password=" + resultSet.getObject("password"));
        System.out.println("email=" + resultSet.getObject("email"));
        System.out.println("birthday=" + resultSet.getObject("birthday"));
    }
    //6.关闭连接，释放资源 （一定要做）
    resultSet.close();
    statement.close();
    connection.close();
    ```
    

**预编译SQL**

```java
//配置信息
//useUnicode=true&characterEncoding=utf-8 解决中文乱码
String url="jdbc:mysql://localhost:3306/jdbc?useUnicode=true&characterEncoding=utf-8";
String username="root";
String password="123456";

//1.加载驱动
Class.forName("com.mysql.jdbc.Driver");
//2.连接数据库，代表数据库
Connection connection = DriverManager.getConnection(url, username, password);
//3.编写SQL
String sql="insert into users (id, name, password, email, birthday) VALUES (?,?,?,?,?)";
//4.预编译
PreparedStatement preparedStatement = connection.prepareStatement(sql);
preparedStatement.setInt(1,1);//给第一个？的值赋值为1
preparedStatement.setString(2,"王五");
preparedStatement.setString(3,"123456");
preparedStatement.setString(4,"ab@qq.com");
preparedStatement.setDate(5,new Date(new java.util.Date().getTime()));

//5.执行SQL,返回一个ResultSet；结果集
int i = preparedStatement.executeUpdate();
if (i>0) {
    System.out.println("插入成功");
}

//6.关闭连接，释放资源 （一定要做）
preparedStatement.close();
connection.close();
```

## 事务

要么都成功，要么都失败

ACID原则：保证数据的安全（原子性；一致性；隔离性：多个业务可能操作同一资源，防止数据损坏；持久性：事务一旦提交，无论系统发生什么问题，结果都不会再被影响，被持久化地写到存储器中）

```plain
开启事务
事务提交commit()
事务回滚rollback()
关闭事务
```

SQL语句如下：

```sql
start transaction;#开启事务
update account set money=money-100 where name='A';
update account set money=money+100 where name='B';
rollback ;#回滚
commit ;#提交事务
```

Java测试如下：

```java
//配置信息
//useUnicode=true&characterEncoding=utf-8 解决中文乱码
String url="jdbc:mysql://localhost:3306/jdbc?useUnicode=true&characterEncoding=utf-8";
String username="root";
String password="123456";
Connection connection= null;

try {
    //1.加载驱动
    Class.forName("com.mysql.jdbc.Driver");
    connection = null;
    //2.连接数据库，代表数据库
    connection = DriverManager.getConnection(url, username, password);
    //3.通知数据库开启事务，false代表开启
    connection.setAutoCommit(false);

    String sql="update account set money=money-100 where name='A';";
    connection.prepareStatement(sql).executeUpdate();
    //制造错误
    int i=1/0;
    String sql1="update account set money=money+100 where name='B';";
    connection.prepareStatement(sql1).executeUpdate();

    connection.commit();//以上SQL都执行成功了，就提交事务
    System.out.println("SUCCESS");
} catch (Exception e) {
    try {
        e.printStackTrace();
        //如果出现异常通知数据库回滚事务
        connection.rollback();
    } catch (SQLException ex) {
        ex.printStackTrace();
    }
}finally {
    //6.关闭连接，释放资源 （一定要做）
    try {
        connection.close();
    } catch (SQLException e) {
        e.printStackTrace();
    }
}
```
