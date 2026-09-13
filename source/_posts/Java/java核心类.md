---
title: "java核心类"
date: 2020-01-05T20:51:33+08:00
categories:
  - java
tags:
  - java基础
---

# Object类

1.  int hashCode()方法：返回对象的**逻辑地址值**
    
2.  Class getClass()方法返回：**运行时类的字节码文件(.class文件)对象**
    
    > Class 是一个类型，Class类型对应的对象是字节码文件(.class文件)对象
    > 
    > 在一个程序的运行过程中，同一个类所创建的对象所使用的都是同一份字节码文件(字节码文件只会被加载一次)
    > 
    > 同一个类的对象的字节码文件对象是同一个(可以用”==”判断是否是一个类)
    > 
    > `String name=object.getClass().getName()`//返回object的运行时类名(getClass—>Class对象—>Class对象的getName)
    
3.  String toString()方法：返回对象所属类的全名+@+**hashcode的16进制表示**，**(打印一个对象时，其实调的是toString方法**)
    
4.  equals()方法：如果不重写比较的是”==”，是地址值
    
5.  clone()方法：得到的对象地址值不同，**是一个新的对象**
    
    > 一个对象能否clone，要看该对象的类是否实现了一个标记接口clonable
    > 
    > 如果一个接口内部是空的，那么这个接口叫做”标记接口”
    
6.  finalize()方法：当对象被判定为垃圾对象时，由JVM自动调用此方法，用以标记垃圾对象，进入回收队列。
    
    > 垃圾对象：没有有效引用指向此对象时，为垃圾对象
    > 
    > 垃圾回收：由GC销毁垃圾对象，释放数据存储空间
    > 
    > 自动回收机制：JVM的内存耗尽，一次性回收所有垃圾对象
    > 
    > 手动回收机制：使用`System.gc();`通知JVM执行垃圾回收
    

* * *

# 基本类型的封装类

基本数据类型

封装类

byte

Byte

int

Integer

short

Short

long

Long

float

Float

double

Double

char

Character

boolean

Boolean

-   基本类型变量->对象
    
    > Integer obj=new Integer(10)  
    > Integer obj=new Integer(“10”)  
    > Integer obj =Integer.valueOf(“100”)**更好，我们把能创建“新”对象的静态方法称为静态工厂方法。Integer.valueOf()就是静态工厂方法，它尽可能地返回缓存的实例(范围：\[-128,127\])以节省内存。自动装箱使用的就是这种方法**
    > 
    > > ```java
    > > Integer i3=new Integer(100);
    > > Integer i4=new Integer(100);
    > > System.out.println(i3 == i4);//false,两个不同的对象
    > > 
    > > Integer i1=100;
    > > Integer i2=100;
    > > System.out.println(i1 == i2);//true,原因：自动装箱使用valueOf方法(),i1,i2指向缓冲区缓存的实例
    > > 
    > > Integer i5=200;
    > > Integer i6=200;
    > > System.out.println(i5 == i6);//false,原因：超过缓存区大小[-128,127]
    > > ```
    
-   对象->基本类型变量
    
    > int a = obj.intValue();
    
-   自动装箱和自动拆箱只发生在编译阶段，目的是为了少写代码。
    
    > ```java
    > int i=1;
    > //自动装箱
    > Integer it=i;
    > //自动拆箱
    > int i2=it;
    > ```
    
-   装箱和拆箱会**影响代码的执行效率**，因为编译后的class代码是严格区分基本类型和引用类型的。并且，**自动拆箱执行时可能会报NullPointerException**
    
-   **所有的包装类型都是不变类，使用final修饰**，一旦创建了Integer对象，该对象就是不变的，**不能用==比较，因为Integer是引用类型，必须使用equals()比较。**
    
-   进制转换(将Integer类型转换为String类型)
    
    > 转换为10进制：int x2 = Integer.parseInt(“100”, 16); // 256,因为按16进制解析（其他进制字符串转为10进制整数）  
    > 10进制转换为其他进制：（10进制整数转换为其他进制字符串）
    
    ```
    12345Integer.toString(100)// "100",表示为10进制Integer.toString(100, 36)"2s",表示为36进制Integer.toHexString(100)// "64",表示为16进制Integer.toOctalString(100)//"144",表示为8进制Integer.toBinaryString(100)// "1100100",表示为2进制
    ```
    
-   所有的整数和浮点数的包装类型**都继承自Number**，可以非常方便地直接通过包装类型获取各种基本类型
    
    > ```java
    > // 向上转型为Number:
    > Number num = new Integer(999);
    > // 获取byte, int, long, float, double:
    > byte b = num.byteValue();
    > int n = num.intValue();
    > long ln = num.longValue();
    > float f = num.floatValue();
    > double d = num.doubleValue();
    > ```
    
-   在Java中，**没有无符号整型（Unsigned）的基本数据类型，最高位都是符号位。**无符号整型和有符号整型的转换在Java中需要借助包装类型的静态方法完成
    

# 字符串与基本类型的转换

## 字符串准换成基本类型

> **int a=Integer.parseInt(“ff”,16)//按16进制转换**

## 基本类型准换成字符串

> String s1 = “” + 23;  
> **String s2 = Integer.toString(100);**  
> String s3 = String.valueOf(66);

* * *

# 字符串类

## String类

-   **不可变特性(字符串内容不可变)**
    
-   字符串字面值存储在字符串池中，可以共享。
    
    > ```java
    > String s1="abc";//产生一个对象，常量池中存储
    > String s2="abc";
    > String s3=new String("abc");//产生两个对象，堆、池各存一个
    > String s4 = new String("abc");
    > System.out.println(s1 == s2);
    > System.out.println(s3 == s4);
    > /*以下为详细分析：
    > 1. ==比较的是地址
    > 2. String是引用变量
    > 3. String 源码中被final修饰，存储于常量池中
    > 4. 所以s1与s2均指向字符串"abc"的地址
    > */
    > ```
    
-   “+”运算符
    
    > “ab”+10+20 结果:”ab1020”  
    > 10+20+”ab” 结果:”30ab”
    
-   拼接字符串
    
    > String\[\] arr = {“A”, “B”, “C”};  
    > String s = String.join(“, “, arr); // “A, B, C”
    
-   与char\[\]的互换
    
    > char\[\] cs = “Hello”.toCharArray(); // String -> char\[\]  
    > String s = new String(cs); // char\[\] -> String
    
-   字符串的比较必须使用a.equals(b)
    
-   **a.compareTo(b)：**
    
    > 从a和b的第一个字符开始比较：
    > 
    > 1.  相等继续往后走
    > 2.  不等则这个位置的两个字符做减法
    > 3.  如果此时字符串a的位置为空，则返回a和b字符串长度的差值
    

## StringBuffer类

-   可变，最终StringBuffer对象调用本身的toString()方法转换成一个String对象打印输出，线程安全，效率低。
    
    > ```java
    > StringBuffer sb=new StringBuffer();
    > sb.append("ada");
    > System.out.println(sb.toString());
    > 
    > sb.insert(0,"dsd");
    > System.out.println(sb.toString());
    > 
    > sb.replace(0,1,"c");
    > System.out.println(sb.toString());
    > 
    > sb.delete(0,1);
    > System.out.println(sb.toString());
    > ```
    

## StringBuilder类

-   可变，通过实例.append(“…”)方法链式操作（返回的是this），最终StringBuilder对象调用本身的toString()方法转换成一个String对象打印输出，非线程安全，效率高。
-   使用方法与StringBuffer类似

## StringJoiner类(拼接字符串,可以指定开头和结尾)

-   源码使用StringBuilder，最终通过调用本身的toString()方法转换成一个String对象，非线程安全，效率高。

> ```java
> public class Main {
>     public static void main(String[] args) {
>         String[] names = {"Bob", "Alice", "Grace"};
>         var sj = new StringJoiner(", ", "Hello ", "!");
>         for (String name : names) {
>             sj.add(name);
>         }
>         System.out.println(sj.toString());//Hello Bob, Alice, Grace！
>     }
> }
> ```

* * *

# enum

-   enum常量本身带有类型信息，如Weekday.SUN类型是Weekday
    
-   enum是引用类型，但是可以用==比较，因为enum类型的每个常量在JVM中只有一个唯一实例
    
-   特点
    
    > 定义的enum类型总是继承自java.lang.Enum，且无法被继承  
    > 只能定义出enum的实例，而无法通过new操作符创建enum的实例  
    > 定义的每个实例都是引用类型的唯一实例  
    > 可以将enum类型用于switch语句
    
-   可通过name()获取常量定义的字符串
    
-   可以为enum编写构造方法、字段和方法，enum的构造方法要声明为private，字段强烈建议声明为final
    

* * *

# BigInteger，BigDecimal

-   BigInteger，BigDecimal**是不变类，并且继承自Number**
    
    > ```java
    > BigDecimal bigDecimal = new BigDecimal("1.0");//必须要用字符串，不然还会出现精度问题
    > BigDecimal bigDecimal1 = new BigDecimal("3");
    > 
    > BigDecimal result=bigDecimal.subtract(bigDecimal1);//加
    > System.out.println(result);
    > 
    > BigDecimal result1=bigDecimal.add(bigDecimal1);//减
    > System.out.println(result1);
    > 
    > BigDecimal result2=bigDecimal.multiply(bigDecimal1);//乘
    > System.out.println(result2);
    > //对于除不尽的：需要声明保留几位小数以及四舍五入等近似方法
    > BigDecimal result3=bigDecimal.divide(bigDecimal1,2,BigDecimal.ROUND_HALF_UP);//除
    > System.out.println(result3);
    > ```
    
-   将BigInteger转换成基本类型时可使用longValueExact()等方法保证结果准确。
    
-   比较BigDecimal的值是否相等，**必须使用compareTo()而不能使用equals()**
    
    > 结果为0代表相等
