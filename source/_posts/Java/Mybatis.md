---
title: "Mybatis"
date: 2020-10-24T12:04:39+08:00
categories:
  - java
tags:
  - java基础
---

# 入门

[mybatis中文文档](https://mybatis.org/mybatis-3/zh/index.html)

步骤1：编写utils类MybatisUtils用于获取sqlSession对象

```java
//sqlSessionFactory-->sqlSession
public class MybatisUtils {
    //提升作用域，静态代码块和方法均能访问到
    private static SqlSessionFactory sqlSessionFactory;
    static {
        try {
            //使用Mybatis第一步：获取sqlSessionFactory对象
            String resource="mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

//    既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
//    SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。
    public static SqlSession getSqlSession(){
//        参数为true自动提交事务
        return sqlSessionFactory.openSession(true);
    }
}
```

步骤2：编写mybatis-config.xml核心配置文件

（注意其中的数据库中文乱码问题解决）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
<!--                解决中文乱码问题-->
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
<!--    每一个Mapper.XML都需要在Mybatis核心配置文件中注册！-->
    <mappers>
        <mapper resource="com/cjx/dao/UserMapper.xml"/>
    </mappers>
</configuration>
```

步骤3：编写实体类，与数据库表的字段相对应

步骤4：编写接口

```java
public interface UserDao {
    List<User>getUserList();
}
```

步骤5：编写Mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace=绑定一个对应的Dao/Mapper接口-->
<mapper namespace="com.cjx.dao.UserDao">
    <select id="getUserList" resultType="com.cjx.pojo.User">
        select *from mybatis.user
    </select>
</mapper>
```

步骤6：编写测试类测试(**增删改需要提交事务**)

```java
public class UserDaoTest {
    @Test
    public void test(){
//        第一步：获取SqlSession对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();
//        方式1：getMapper,执行SQL
        UserDao mapper = sqlSession.getMapper(UserDao.class);
        List<User> userList = mapper.getUserList();
        for (User user : userList) {
            System.out.println(user);
        }
//        关闭SqlSession
        sqlSession.close();
    }
}
```

步骤7：注意maven设置中配置文件的导出问题

## Mybatis中的三大类

![](/images/Java/%E4%B8%89%E5%A4%A7%E7%B1%BB.png)

# 万能Map

假设，我们的实体类，或者数据库中的表，字段或者参数过多，我们应当考虑使用Map!

map传递参数，直接在sql中取出key即可！【parameterType=”map”】

只有一个基本类型参数的情况下，可以直接在sql中取到！【可以不写参数类型】；多个参数用Map，或者注解

# 模糊查询

1.  Java代码执行的时候，传递通配符%%
    
    ```java
    List<User> list = mapper.getUserLike("%李%");
    ```
    
2.  在sql拼接中使用通配符！（易导致sql注入，不安全）
    
    ```xml
    select *from mybatis.user where name like "%"#{value}"%"
    ```
    

# 配置解析

## 环境配置（environments）

MyBatis 可以配置成适应多种环境

**不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

学会使用配置多套运行环境！

Mybatis默认的事务管理器是JDBC，连接池：POOLED

## 属性（properties）

我们可以通过properties属性来实现引用配置文件

这些属性可以在外部进行配置，并可以进行动态替换。你既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置。【db.properties】

编写一个配置文件db.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=UTF-8
username=root
password=123456
```

在核心配置文件中引入

```xml
<properties resource="db.properties">
    <property name="username" value="root"/>
    <property name="pwd" value="123456"/>
</properties>
```

-   可以直接引入外部文件
-   可以在其中增加一些属性配置
-   如果两个文件有同一个字段，优先使用外部配置文件的！

## 类型别名（typeAliases）

-   类型别名可为 Java 类型设置一个缩写名字
-   存在的意义在于降低冗余的全限定类名书写

### 方法1：

```xml
<!--    可以给实体类起别名-->
<typeAliases>
    <typeAlias type="com.cjx.pojo.User" alias="User"></typeAlias>
</typeAliases>
```

### 方法2：

也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean。比如：

扫描实体类的包，它的默认别名就为这个类的类名，首字母小写！

```xml
<!--    可以给实体类起别名-->
<typeAliases>
    <package name="com.cjx.pojo"/>
</typeAliases>
```

-   在实体类比较少的时候，使用第一种方式。
-   如果实体类十分多，建议使用第二种
-   第一种可以自定义各种别名；第二种则不行，第二种如果非要自定义别名，需要在实体类上增加注解

```java
@Alias("user")
public class User {}
```

## 映射器（mappers）

MapperRegistry:注册绑定我们的Mapper文件：

### 方式1：【推荐使用】

```xml
<mappers>
    <mapper resource="com/cjx/dao/UserMapper.xml"/>-->
</mappers>
```

### 方式2：

```xml
<mappers>
    <mapper class="com.cjx.dao.UserMapper"></mapper>-->
</mappers>
```

注意点：

-   接口和他的Mapper配置文件必须同名！
-   接口和他的Mapper配置文件必须在同一个包下！

### 方式3：使用扫描包进行注入绑定

```xml
<mappers>
    <package name="com.cjx.dao"/>
</mappers>
```

注意点：

-   接口和他的Mapper配置文件必须同名！
-   接口和他的Mapper配置文件必须在同一个包下！

# 生命周期和作用域

不同作用域和生命周期类别是至关重要的，因为错误的使用会导致非常严重的**并发问题**

## SqlSessionFactoryBuilder

-   一旦创建了 SqlSessionFactory，就不再需要它了
-   局部变量

## SqlSessionFactory

-   可以想象为：数据库连接池
-   SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，**没有任何理由丢弃它或重新创建另一个实例。**
-   因此 SqlSessionFactory 的最佳作用域是应用作用域
-   最简单的就是使用**单例模式**或者静态单例模式。

## SqlSession

-   可以想象为：连接到连接池的一个请求
-   SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域。
-   用完之后需要赶紧关闭，否则资源被占用！

# 解决属性名和字段名不一致的问题

## resultMap（结果集映射）

```xml
<!--    结果集映射-->
<resultMap id="UserMap" type="User">
<!--        column数据库中的字段，property实体类中的属性-->
    <result column="id" property="id"/>
    <result column="name" property="name"/>
    <result column="pwd" property="password"/>
</resultMap>
<select id="getUserById" resultMap="UserMap">
    select *from mybatis.user where id=#{id}
</select>
```

# 日志工厂

在核心配置文件mybatis-config.xml中配置日志工厂

## STDOUT\_LOGGING

标准日志工厂：STDOUT\_LOGGING

```xml
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

# 分页实现

1.  使用SQL语句中的limit startindex pagesize实现
2.  分页插件：PageHelper

# Mybatis执行流程

[源码剖析参考链接](https://www.iteye.com/category/223150)

## SqlSessionFactory获取步骤

1.  读取Ibatis的主配置文件，并将文件读成文件流形式(InputStream)。
2.  从主配置文件流中读取文件的各个节点信息并存放到Configuration对象中。读取mappers节点的引用文件，并将这些文件的各个节点信息存放到Configuration对象。
3.  传入Configuration对象进去,创建DefaultSqlSessionFactory实例出来。 DefaultSqlSessionFactory是SqlSessionFactory的默认实现。

## SqlSession获取步骤

1.  获取前面我们加载配置文件获得的Configuration对象的环境信息(Environment对象)，并根据Environment对象获取配置的数据源。
2.  传入Environment对象获取事务工厂（TransactionFactory对象），之后传入事务信息（是否自动提交、事务级别）以及数据源获取一个新的事务（Transaction对象）。
3.  传入Transaction对象以及执行器类型（ExecutorType对象）获取一个新的执行器（Executor对象）。
4.  传入执行器（Executor对象）、配置信息（Configuration对象）以及事务信息（是否自动提交）获取一个执行会话对象（DefaultSqlSession），DefaultSqlSession是SqlSession的默认实现。

## 流程图

![](/images/Java/mybatis%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

# 相关注解

## @Param(“id”)

-   基本类型的参数或者String类型，需要加上
-   引用类型不需要加
-   如果只有一个基本类型的话，可以忽略，但是建议加上
-   我们在SQL中引用的就是我们这里的@Param(“id”)中设定的属性名

## #{} ${}区别

[参考链接1](https://segmentfault.com/a/1190000004617028)

[参考链接2](http://www.mybatis.cn/archives/70.html)

角度1：参数替换时机

1.  mybatis在处理#{}时，在预编译之前会将sql中的#{}替换为?号，预编译之后调用PreparedStatement的set方法来赋值。
2.  mybatis在处理${}时，就是把${}替换成变量的值。
3.  #{} 的**参数替换**是发生在 DBMS中（**预编译之后**） ，而 ${}的参数替换则发生在动态解析过程中（**编译之前**）
4.  使用#{}可以有效的防止SQL注入，提高系统安全性。原因在于：预编译机制。**预编译完成之后，SQL的结构已经固定，即便用户输入非法参数，也不会对SQL的结构产生影响，从而避免了潜在的安全风险。**
5.  预编译是提前对SQL语句进行预编译，而其后注入的参数将不会再进行SQL编译。我们知道，SQL注入是发生在编译的过程中，因为恶意注入了某些特殊字符，最后被编译成了恶意的执行操作。而预编译机制则可以很好的防止SQL注入。

角度2：#{}中的变量替换时，会把变量的两边加上””，形成字符串。${}中的变量替换时，会直接替换，变量两边不会有””。

1.  因此${}存在以下使用场景：
    
    > 1.  表名作为变量时：select \* from ${tableName} where name = #{name}
    > 2.  ORDER BY ${columnName}
    

# 多对一

例如：学生–>老师。站在学生的角度即为多对一，是关联

## 联表查询

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.cjx.dao.StudentMapper">
    
    <!--    思路1：联表查询-->
    <select id="getStudent2" resultMap="StudentTeacher2">
        select s.id sid,s.name sname, t.name tname,t.id tid
        from student s,teacher t
        where s.tid=t.id;
	</select>
    
    <resultMap id="StudentTeacher2" type="Student">
        <result property="id" column="sid"></result>
        <result property="name" column="sname"></result>
        <!--        复杂的属性我们需要单独处理   对象：association   集合：collection-->
        <association property="teacher" javaType="Teacher">
            <result property="id" column="tid"></result>
            <result property="name" column="tname"></result>
        </association>
    </resultMap>
```

## 子查询

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.cjx.dao.StudentMapper">
    
    <!--    思路2：子查询-->
    <select id="getStudent" resultMap="StudentTeacher">
        select * from mybatis.student
    </select>
    
    <resultMap id="StudentTeacher" type="Student">
        <result property="id" column="id"></result>
        <result property="name" column="name"></result>
        <!--        复杂的属性我们需要单独处理   对象：association   集合：collection-->
        <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"></association>
    </resultMap>
    
    <select id="getTeacher" resultType="Teacher">
        select *from mybatis.teacher where id=#{id}
    </select>
</mapper>
```

# 一对多

例如：老师–>学生。站在老师的角度即为一对多

联表查询

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.cjx.dao.TeacherMapper">
    
    <resultMap id="TeacherStudent" type="Teacher">
        <result property="id" column="tid"></result>
        <result property="name" column="tname"></result>
        <!--        复杂的属性我们需要单独处理   对象：association   集合：collection-->
<!--        javaType=""指定属性的类型-->
<!--        集合中的泛型信息，我们使用ofType获取-->
        <collection property="students" ofType="Student">
            <result property="id" column="sid"></result>
            <result property="name" column="sname"></result>
            <result property="tid" column="tid"></result>
        </collection>
    </resultMap>
    
    <select id="getTeacher" resultMap="TeacherStudent">
        select t.id tid,t.name tname ,s.name sname,s.id sid
        from student s,teacher t
        where s.tid=t.id
        and t.id=#{tid}
    </select>
</mapper>
```

## 小结：

1.  关联-association，即一个JavaBean中含有其他类的对象【多对一】
2.  集合-collection【一对多】
3.  javaType & ofType
    1.  javaType用来指定实体类中属性的类型
    2.  ofType用来指定映射到List或者集合中的pojo类型，泛型中的约束类型
4.  注意一对多和多对一中，属性名和字段的问题

## 面试高频（必问）：

-   Mysql引擎
-   InnoDB底层原理
-   索引
-   索引优化

# 动态SQL

**什么是动态SQL：动态SQL就是根据不同的条件生成不同的SQL语句**

## if

```xml
<select id="queryBlogIf" parameterType="map" resultType="Blog">
    select *from mybatis.blog
    <where>
        <if test="author!=null">
            author=#{author}
        </if>
        <if test="title!=null">
            and title=#{title}
        </if>
    </where>
</select>
```

-   加入where标签可：1. 在SQL中添加关键字where。2. 去除多余的and关键字（可以多写但是不能少写），保证SQL语法正确
-   if标签中的test语句中的变量是外部传入的变量

## choose (when, otherwise)

```xml
<select id="queryBlogChoose" parameterType="map" resultType="Blog">
    select *from mybatis.blog
    <where>
    <choose>
        <when test="author!=null">
            author=#{author}
        </when>
        <when test="title!=null">
            and title=#{title}
        </when>
        <otherwise>
            and views=#{views}
        </otherwise>
    </choose>
    </where>
</select>
```

-   只会选择所有when标签中的一个（与if的区别），如果when标签都不满足，选择otherwise标签
-   类似于switch语句

## set

update操作时，使用set标签：1. 在SQL中添加关键字set。2. 去除多余的’,’（可以多写但是不能少写），保证SQL语法正确

```xml
<update id="updateBlog" parameterType="map" >
    update mybatis.blog
    <set>
        <if test="title!=null">
            title=#{title},
        </if>
        <if test="author!=null">
            author=#{author},
        </if>
    </set>
    where id=#{id}
</update>
```

**所谓的动态SQL，本质还是SQL语句，只是我们可以在SQL层面，去执行一个逻辑代码**

## sql

有的时候，我们可能会将些一功能的部分抽取出来， 方便复用!

1.  使用sql标签抽取公共的部分
2.  在需要使用的地方使用include标签引用即可

## foreach

用于对集合进行遍历，通常是在构造IN条件语句的时候

```xml
<select id="queryBlogForeach" parameterType="map" resultType="Blog">
    select *from mybatis.blog
    <where>
        <foreach collection="ids" item="id" open=" (" close=")" separator=" or">
            id=#{id}
        </foreach>
    </where>
</select>
```

-   collection：外部传入的命名称为ids的集合
-   item：集合中每个元素的值用id（可自定义为其他名字，之后在foreach标签内使用）来表示
-   open：foreach所拼接的SQL的前缀
-   close：foreach所拼接的SQL的后缀
-   separator：分隔符
-   index：集合中元素的索引，（可自定义为其他名字，之后在foreach标签内使用）

**动态SQL就是在拼接SQL语句，我们只要保证SQL的正确性，按照SQL的格式，去排列组合就可以了**

建议：

-   先在Mysql中写出完整的SQL，再对应的去修改成为我们的动态SQL实现通用即可

# Mybatis缓存

-   MyBatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大的提升查询效率。
-   MyBatis系统中默认定义了两级缓存: 一级缓存和二级缓存
    -   默认情况下，只有一级缓存开启。 (SqlSession级别的缓存， 也称为本地缓存)
    -   二级缓存需要手动开启和配置，他是基于namespace级别的缓存。
    -   为了提高扩展性，MyBatis定义了缓存接口Cache。我们可以通过实现Cache接口来自定义二级缓存

## 一级缓存

-   一级缓存也叫本地缓存（sqlSession开启与关闭之间）:
    -   与数据库同一次会话期间查询到的数据会放在本地缓存中。
    -   以后如果需要获取相同的数据，直接从缓存中拿， 没必须再去查询数据库;
-   一级缓存失效的情况
    1.  查询不同的东西
    2.  增删改操作，可能会改变原来的数据，所以必定会刷新缓存!
    3.  查询不同的Mapper.xml
    4.  手动清理缓存!|

小结: 一级缓存默认是开启的，只在一一次SqISession中有效， 也就是拿到连接到关闭连接这个区间段! 一级缓存就是一个map

## 二级缓存

-   二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存
-   基于namespace级别的缓存，一个名称空间，对应一个二 级缓存;
-   工作机制
    -   一个会话查询一条数据,这个数据就会被放在当前会话的一级缓存中;
    -   如果当前会话关闭了,这个会话对应的一级缓存就没了;但是我们想要的是，会话关闭了，一级缓存中的数据被保存到二级缓存中;
    -   新的会话查询信息，就可以从二级缓存中获取内容;
    -   不同的mapper查出的数据会放在自己对应的缓存(map) 中;

**步骤**：

1.  在mybatis-config.xml中开启全局缓存
    
    ```xml
    <settings>
        <setting name="cacheEnabled" value="true"/>
    </settings>
    ```
    
2.  在要使用二级缓存的Mapper中开启
    
    ```xml
    <!--    在当前Mapper.xml中使用二级缓存-->
        <cache/>
    ```
    
    也可以自定义参数
    
    ```xml
    <cache
      eviction="FIFO"
      flushInterval="60000"
      size="512"
      readOnly="true"/>
    ```
    
    未设置 readOnly=”true”时：
    
    1.  pojo对象需要实现序列化接口，否则报错；
    2.  第二次查询时，不需连接数据库，读取到的值是之前值的拷贝（二者不是同一个对象）。

**小结**:

-   只要开启了二级缓存,在同一个Mapper下就有效
-   所有的数据都会先放在一级缓存中;
-   只有当会话提交，或者关闭的时候，才会提交到二级缓存中!

## 缓存顺序

1.  先看二级缓存中有没有
2.  再看一级缓存中有没有
3.  查询数据库
