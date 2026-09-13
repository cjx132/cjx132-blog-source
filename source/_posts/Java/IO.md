---
title: "IO"
date: 2020-09-23T18:04:17+08:00
categories:
  - java
tags:
  - java基础
---

# 流

## 概念

内存与存储设备之间传输数据的通道

## 分类

按方向：输入流，输出流；

按单位：字节流，字符流；

> 1.  字节流：以字节为单位，可以读写所有数据
> 2.  字符流：以字符为单位，只能读写文本数据

按功能：节点流，过滤流

> 1.  节点流：具有实际传输数据的读写功能
> 2.  过滤流：在节点流的基础之上增强功能

## 字节流

字节流的父类（抽象类）

```java
//InputStream 字节输入流
public int read(){}
public int read(byte[] b){}
public int read(byte[] b, int off, int len){}

// OutputStream 字节输出流
public void write(int n){}
public void write(byte[] b){}
public void write(byte[] b, int off, int len){}
```

### 文件字节流

文件输入流

```java
public static void main(String[] args) throws Exception{
  // 1 创建FileInputStream 并指定文件路径
  FileInputStream fis = new FileInputStream("d:\\abc.txt");
  // 2 读取文件
  // fis.read();
  // 2.1单字节读取
  int data = 0;
  while((data = fis.read()) != -1){
    System.out.print((char)data);
  }
  
  // 2.2 一次读取多个字节
  byte[] buf = new byte[1024]; // 大小为3的缓存区
  int count = 0;
  while((count = fis.read(buf)) != -1){
    sout(new String(buf, 0, count));//这里必须要这样写：因为最后一次读取有可能不到1024个字符
  }
  
  // 3 关闭
  fis.close();
}
```

文件输出流

```java
public static void main(String[] args) throws IOException {
  // 1 创建文件字节输出流
  FileOutputStream fos = new FileOutputStream("路径", true);// true表示不覆盖 接着写 
  // 2 写入文件
  fos.write(97);
  fos.write('a');
  String string = "hello world";
  fos.write(string.getByte());
  // 3 关闭
  fos.close();
}
```

### 图片复制案例

```java
// 1 创建流
// 1.1 文件字节输入流
FileInputStream fis = new FileInputStream("路径");
// 1.2 文件字节输出流
FileInputStream fos = new FileOutpuStream("路径");
// 2 边读边写
byte[] buf = new byte[1024];
int count = 0;
while((count = fis.read(buf)) != -1){
  fos.write(buf, 0, count);//这里必须要这样写：因为最后一次读取有可能不到1024个字符
}
// 3 关闭
fis.close();
fos.close();
```

### 字节缓冲流

缓冲流：BufferedInputStream/ BufferedOutputStream

-   提高IO效率，减少访问磁盘次数(缓冲流中存在8k的缓冲区)
-   数据存储在缓冲区中，flush是将缓冲区的内容写入文件中，也可以直接close

```java
// 使用字节缓冲流 读取 文件
psvm(String[] args) throws Exception{
  // 1 创建BufferedInputStream
  FileInputStream fis = new FileInputStream("路径");
  BufferedInputStream bis = new BufferedInputStream(fis);
  // 2 读取
  int data = 0;
  while((data = bis.read()) != -1){
    sout((char)data);
  }
  // 用自己创建的缓冲流
  byte[] buf = new byte[1024];
  int count = 0;
  while((count = bis.read(buf)) != -1){
    sout(new String(buf, 0, count));
  }
  
  // 3 关闭
  bis.close();
}
```
```java
// 使用字节缓冲流 写入 文件
psvm(String[] args) throws Exception{
  // 1 创建BufferedInputStream
  FileOutputStream fos = new FileOutputStream("路径");
  BufferedOutputStream bis = new BufferedOutputStream(fos);
  // 2 写入文件
  for(int i = 0; i < 10; i ++){
    bos.write("hello".getBytes());// 写入8k缓冲区
    bos.flush(); // 刷新到硬盘
  }
  // 3 关闭(内部调用flush方法)
  bos.close();
}
```

## 对象流

-   增强了缓冲区功能
-   增强了读写8种基本数据类型和字符串的功能
-   增强了读写对象的功能
    -   `readObject()` 从流中读取一个对象，称为反序列化（这里所说的流有BuffedInputStream中的缓冲区的味道）
    -   `writeObject(Object obj)` 向流中写入一个对象，称为序列化（这里所说的流有BuffedOutputStream中的缓冲区的味道）

### 序列化

```java
// 使用objectoutputStream实现序列化
psvm(String[] args){
  // 1. 创建对象流
  FileOutputStream fos = new FileOutputStream("d:\\st.bin");
  ObjectOutputSream oos = new objectOutputSream(fos);
  // 2. 序列化（写入操作）
  Student zhangsan = new Student("zs", 20);
  oos.WriteObject(zhangsan);
    
  oos.flush();//从流中刷新写入文件
  // 3. 关闭
  oos.close();
  sout("序列化完毕");
}
```

### 反序列化

```java
// 使用ObjectInputSteam实现反序列化（读取重构对象）
psvm(String[] args){
  // 1. 创建对象流
  FileInputStream fis = new FileInputStream("d:\\stu.bin");
  ObjectInputStream ois = new ObjectInputStream(fis);
  // 2. 读取文件（反序列化）
  Student s = (Student)ois.readObject();
  // 3. 关闭
  ois.close();
  sout("执行完毕");
  sout(s.toString());  
}
```

### 注意事项

1.  某个类要想序列化必须实现Serializable接口
2.  序列化类中对象属性要求实现Serializable接口
3.  序列化版本号ID，保证序列化的类和反序列化的类是同一个类
4.  使用transient修饰属性，这个属性就不能序列化
5.  静态属性不能被序列化
6.  序列化多个对象，可以借助集合来实现

* * *

## 字符流

字符流的父类（抽象类）

`reader` 字符输入流

-   `public int read(){}`
-   `public int read(char[] c){}`
-   `public int read(char[] b, int off, int len){}`

`Writer` 字符输出流

-   `public void write(int n){}`
-   `public void write(String str){}`
-   `public void write(char[] c){}`

```java
// 1. 创建FileReader 文件字符输入流
FileReader fr = new FileReader("..");
// 2. 读取
// 2.1 单个字符读取
int data = 0;
while((data = fr.read()) != -1){
  sout((char)data);// 读取一个字符
}
char[] buf = new char[2];// 字符缓冲区读取
int count = 0;
while((count = fr.read(buf) != -1)){
  sout(new String(buf, 0, count));
}
// 3. 关闭
fr.close();
```
```java
// 1. 创建FileWriter对象
FileWriter fw = new FileWriter("..");
// 2. 写入
for(int i = 0; i < 10; i ++){
  fw.write("写入的内容");
  fw.flush();
}
// 3. 关闭
fw.close();
sout("执行完毕");
```

### 文本文件复制

不能复制图片或二进制文件，使用字节流可以复制任意文件

```java
psvm(String[] args) throws Exception{
  // 1. 创建
  FileReader fr = new FileReader("...");
  FileWriter fw = new FileWriter("...");
  // 2. 读写
  int data = 0;
  while((data = fr.read()) != -1){
    fw.write(data);
    fw.flush();
  }
  // 3. 关闭
  fw.close();
  fr.close();
}
```

### 字符缓冲流

BufferedReader / BufferedWriter

高效读写、支持输入换行符、可一次写一行读一行

```java
psvm(String[] args) throws Exception{
  // 创建缓冲流
  FileReader fr = new FileReader("..");
	BufferedReader br = new BufferedReader(fr);
  // 读取
  // 1. 第一种方式
  char[] buf = new char[1024];
  int count = 0;
  while((count = br.read(buf)) != -1){
    sout(new String(buf, 0, count));
  }
  // 2. 第二种方式 一行一行读取
  String line = null;
  while((line = br.readLine()) != null){
    sout(line);
  }
  
	// 关闭
  br.close();
}
```
```java
psvm(String[] args){
  // 1. 创建BufferedWriter对象
  FileWriter fw = new FileWriter("..");
  BufferedWriter bw = new BufferedWriter(fw);
  // 2. 写入
  for(int i = 0; i < 10; i ++){
    bw.write("写入的内容");
    vw.newLine(); // 写入一个换行符
    bw.flush();
  }
  // 3. 关闭
  bw.close(); // 此时会自动关闭fw
}
```

## PrintWriter

封装了`print() / println()` 方法 支持写入后换行

支持数据原样打印

**无论是字节还是字符均可使用该类中的方法**

```java
psvm(String[] args){
  // 1 创建打印流
  PrintWriter pw = new PrintWriter("..");
  // 2 打印
  pw.println(12);
  pw.println(true);
  pw.println(3.14);
  pw.println('a');
    
  pw.flush();
  // 3 关闭
  pw.close();//会自动调用flush方法
}
```

## 转换流

桥转换流 `InputStreamReader / OutputStreamWriter`

`InputStreamReader`将硬盘中的字节流转换为字符流进入内存中，**将字节流转换为字符流**；可设置解码方式

`OutputStreamWriter`将内存中的字符流转换为字节流存储于硬盘中，**将字符流转换为字节流**；可设置编码方式

```java
psvm(String[] args) throws Exception{
  // 1 创建InputStreamReader对象
  FileInputStream fis = new FisInputStream("..");
  InputStreamReader isr = new InputStreamReader(fis, "utf-8");
  // 2 读取文件
  int data = 0;
  while((data = isr.read()) != -1){
    sout((char)data);
  }
  // 3 关闭
  isr.close();
}
```
```java
psvm(String[] args) throws Exception{
  // 1 创建OutputStreamReader对象
  FileOutputStream fos = new FisOutputStream("..");
  OutputStreamWRITER osw = new OutputStreamReader(fos, "utf-8");
  // 2 写入
  for(int i = 0; i < 10; i ++){
    osw.write("写入内容");
    osw.flush();
  }
  // 3 关闭
  osw.close();
}
```

# File类

概念：代表物理盘符中的一个文件或者文件夹

## 基本方法与属性

```java
public class FileLearn {
    public static void main(String[] args) throws Exception {
//        separater();
        fileOpe();
//        directoryOpe();
    }
    public static void separater(){
        System.out.println("路径分隔符" + File.pathSeparator);
        System.out.println("名称分隔符" + File.separator);
    }

    public static void fileOpe() throws IOException, InterruptedException {
        File file = new File("d:\\file.txt");
        System.out.println(file);

        if(!file.exists()){//创建文件之前先判断
            boolean newFile = file.createNewFile();//创建文件
            System.out.println("创建结果" + newFile);
        }

// 2. 删除文件
        // 2.1 直接删除
//        file.delete(); // 成功true
        // 2.2 使用jvm退出时删除
//        file.deleteOnExit();

        // 3. 获取文件信息
        System.out.println("获取绝对路径" + file.getAbsolutePath());
        System.out.println("获取路径" + file.getPath());//File对象的字符串目录
        System.out.println("获取文件名称" + file.getName());
        System.out.println("获取父目录" + file.getParent());
        System.out.println("获取文件长度" + file.length());//字节数
        System.out.println("文件创建时间" + new Date(file.lastModified()).toLocaleString());

        // 4. 判断
        System.out.println("是否可写" + file.canWrite());
        System.out.println("是否是文件" + file.isFile());
        System.out.println("是否隐藏" + file.isHidden());
    }

    // 文件夹操作
    public static void directoryOpe() throws Exception{
        // 1. 创建文件夹
        File dir = new File("d:\\aaa\\bbb\\ccc");
        System.out.println(dir.toString());
        if(!dir.exists()){//创建文件夹之前先判断
            //dir.mkdir(); // 只能创建单级目录
            System.out.println("创建结果:"+dir.mkdirs());//可创建多级目录，并返回创建结果
        }

        // 2. 删除文件夹（只能删除最底层空目录）
        // 2.1 直接删除
//        System.out.println("删除结果:"+dir.delete());
        // 2.2 使用jvm删除
//        dir.deleteOnExit();

        // 3. 获取文件夹信息
        System.out.println("获取绝对路径" + dir.getAbsolutePath());
        System.out.println("获取路径" + dir.getPath());//File对象的目录
        System.out.println("获取文件夹名称" + dir.getName());
        System.out.println("获取父目录" + dir.getParent());
        System.out.println("文件夹创建时间" + new Date(dir.lastModified()).toLocaleString());

        // 4. 判断
        System.out.println("是否是文件夹" + dir.isDirectory());//判断是否是目录，与是否是文件不一样
        System.out.println("是否隐藏" + dir.isHidden());

        System.out.println("----------");
        // 5. 遍历文件夹
        File dir2 = new File("d:\\图片");
        String[] files = dir2.list();//返回的是字符串对象
        for(String string : files){
            System.out.println(string);
        }

        // FileFilter接口的使用

        File[] files2 = dir2.listFiles(new FileFilter(){//使用匿名内部类的方法实现接口，与TreeMap的比较器类似

            @Override
            public boolean accept(File pathname){
                if(pathname.getName().endsWith(".jpg")){//获取jpg文件
                    return true;
                }
                return false;
            }
        });
        System.out.println("-----------------");
        for(File file : files2){
            System.out.println(file.getName());
        }
    }
```

## 递归遍历/删除文件夹

```java
public static void main(String[] args) {
//        listDir(new File("d:\\图片 - 副本"));
        delDir(new File("d:\\图片 - 副本"));
    }
    //案例1：递归遍历文件夹
    public static void listDir(File dir) {
        File []files=dir.listFiles();
        System.out.println(dir.getAbsolutePath());
        if(files!=null&&files.length>0){
            for (File file : files) {
                if(file.isDirectory()){
                    listDir(file);//递归
                }else{
                    System.out.println(file.getAbsolutePath());
                }
            }
        }
    }

    public static void delDir(File dir){
        File []files=dir.listFiles();
        if(files!=null&&files.length>0){
            for (File file : files) {
                if(file.isDirectory()){
                    delDir(file);//递归
                }else {
                    //删除文件
                    System.out.println(file.getAbsolutePath() + "删除：" + file.delete());
                }
            }
        }
        System.out.println(dir.getAbsolutePath() + "删除：" + dir.delete());
    }
```

# Properties

Properties:属性集合

特点:

```
1. 存储属性名和属性值
 2. 属性名和属性值都是字符串类型
 3. 没有泛型
 4. 和流有关
```
```java
public static void main(String[] args) throws IOException {
        //1.创建集合
        Properties properties = new Properties();
        
    
        //2.添加数据
        properties.setProperty("username","zhangsan");
        properties.setProperty("age","20");
        System.out.println(properties);
        
    
        //3.遍历
        //3.1---keySet---
        for (Object o : properties.keySet()) {
            System.out.println(o +"="+ properties.getProperty((String) o));
        }
        //3.2---entrySet---
        for (Map.Entry<Object, Object> objectObjectEntry : properties.entrySet()) {
            System.out.println(objectObjectEntry.getKey()+"=" + objectObjectEntry.getValue() );
        }
        //3.3---stringPropertyNames---得到属性名集合
        for (String stringPropertyName : properties.stringPropertyNames()) {
            System.out.println(stringPropertyName+"="+properties.getProperty(stringPropertyName));
        }

    
        //和流有关的方法
        //----------list方法---------------
        PrintWriter printWriter = new PrintWriter("d:\\print.txt");
        properties.list(printWriter);//将所有属性名和属性值打印到指定文件中
        printWriter.close();

        //-----------2store方法------------
        FileOutputStream fileOutputStream = new FileOutputStream("d:\\print.properties");
        properties.store(fileOutputStream,"注释");//将所有属性名和属性值保存到指定文件中
        fileOutputStream.close();

        //----------3load方法
        Properties properties1 = new Properties();
        FileInputStream fileInputStream = new FileInputStream("d:\\print.properties");
        properties1.load(fileInputStream);//将指定文件中存储的属性名和属性值加载到新的Properties对象中
        fileInputStream.close();
        System.out.println(properties1);
    }
```
