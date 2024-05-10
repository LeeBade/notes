# Java对I/O的基本支持

InputStream/OutputStream：读写字节流的类

Reader/Writer：自动编码解码的InputStream和OutputStream
- 按指定编码格式互相转换二进制序列和可视字符流


java.io中的四个抽象类及其子类都是同步IO：InputStream、OutputStream、Reader、Writer
- java.nio为异步IO


## File

路径与操作平台
- 绝对路径：以根目录开头的完整路径
- `File.separator`获取平台分隔符，避免移植到不同平台出错
  - 在windows，File.separator将转换为`\\`
- 主流操作系统遵守
  - .->当前目录
  - ..->上级目录
  - 允许路径包括..或.并从左到右进行目录跳转


构造File对象要求关联文件/目录的路径
- 构造路径时使用`File.separator`，避免代码移植出错
  - 传入相对路径时，相对路径前面加上当前目录就是绝对路径：
- 即使该路径对应的目录或文件不存在，也不会发生错误
- 在进行磁盘操作前调用isFile()、isDirectory()

```JAVA

// 假设当前目录是C:\Docs
File f1 = new File("sub\\javac"); // 绝对路径是C:\Docs\sub\javac
File f3 = new File(".\\sub\\javac"); // 绝对路径是C:\Docs\sub\javac
File f3 = new File("..\\sub\\javac"); // 绝对路径是C:\sub\javac

getPath()// 返回构造方法传入的路径
getAbsolutePath()// 返回绝对路径
getCanonicalPath()// 返回规范路径
```

文件操作
```JAVA
createNewFile()
delete()

boolean canRead()// 是否可读；
boolean canWrite()// 是否可写；
boolean canExecute()// 是否可执行；
long length()// 文件字节大小。

createTempFile()
deleteOnExit()
File f = File.createTempFile("tmp-", ".txt"); // 提供临时文件的前缀和后缀
f.deleteOnExit(); // JVM退出时自动删除
```

目录操作
- 若File对象表示存在的目录，list()/listFiles()返回该目录下的文件和目录名
- listFiles接受FilenameFilter对象，用于过滤不想要的文件或目录

```JAVA

boolean mkdir()：创建当前File对象表示的目录；
boolean mkdirs()：创建当前File对象表示的目录，并在必要时将不存在的父目录也创建出来；
boolean delete()：删除当前File对象表示的目录，当前目录必须为空才能删除成功。

```

## Path
java.nio.file.Path：与File相似，适合对目录进行复杂的拼接、遍历等操作

## InputStream


使用try(resource)语法打开资源，要求资源实现java.lang.AutoCloseable接口


```JAVA
public void readFile() throws IOException {
    try (InputStream input = new FileInputStream("src/readme.txt")) {
        int n;
        while ((n = input.read()) != -1) {
            System.out.println(n);
        }
    } // 编译器在此自动写入finally并调用close()
}

```

InputStream
-  read()方法读取输入流的下一个字节，并返回字节表示的int值（0~255），字节流末尾返回-1
-  每次I/O读取多个字节到缓冲区，每次调用从缓冲区取出一个字符
```JAVA
public abstract int read() throws IOException;
```
- 下面两个方法一次读取多个字节，其中byte[]类型的参数作为缓冲区；返回值表示本次I/O实际读取的字节，若返回-1表示本次I/O后没有数据可读
  - `int read(byte[] b)`：读取若干字节并填充到byte[]数组，返回读取的字节数
  - `int read(byte[] b, int off, int len)`：指定byte[]数组的偏移量和最大填充数

## OutputStream

- write方法写入一个字节到输出流
  - 每次只写入int的低8位，高8位将会被忽略
- OutputStream提供flush()方法将缓冲区的内容真正输出到目的地
  - 通常不需要调用flush方法，若在close时缓冲区未写满也会自动调用flush方法
  - 在要求延迟的用户场景下，可能需要写入几个字符就调用一次flush方法，例如编写聊天软件
```JAVA
public abstract void write(int b) throws IOException;

```
使用try(resource)
```JAVA
public void writeFile() throws IOException {
    try (OutputStream output = new FileOutputStream("out/readme.txt")) {
        output.write("Hello".getBytes("UTF-8")); // Hello
    } // 编译器在此自动为我们写入finally并调用close()
}```

在try(resource) { ... }语句中可以同时写出多个资源，用;隔开

// 读取input.txt，写入output.txt:
try (InputStream input = new FileInputStream("input.txt");
     OutputStream output = new FileOutputStream("output.txt"))
{
    input.transferTo(output); 
}
```
## Filter

I/O家族分为两种：提供数据的类，附加额外功能的类；使用装饰器模式进行使用

```JAVA

InputStream file = new FileInputStream("test.gz");

InputStream buffered = new BufferedInputStream(file);

InputStream gzip = new GZIPInputStream(buffered);

```

## classpath

将资源放置在classpath中，避免项目与平台存储产生依赖

## 序列化

<A HREF='#serializable接口'>serializable接口</A>

## Reader

## Writer

## Files

对小文件使用Files操作文件和目录，大文件仍使用字节流或字符流