# JVM

## JRE
JRE=JVM、Java标准库、Java运行时的环境支持文件、执行Java程序的工具、jar包
- JVM：Java虚拟机Java Virtual Machine：将Java字节码编译成操作系统机器代码并执行Java应用程序
- jar包：字节码文件，可以直接运行
  - 第三方jar包不应再次编译
## .class

- classpath：一组目录的集合，格式和系统相关；指示jvm在指定目录集合中搜索.class文件
  - 不应该在系统环境变量中设置classpath，而是应该在运行java命令时附带classpath参数
  - IDE自动传入的cp参数是当前工程的bin目录和引入的jar包
  - 约定既不在系统环境变量设置classpath，也不在java命令传入参数，而是在工程根目录运行java命令
```bash

# .代表当前目录
# 分号代表目录的分隔符
# 目录格式与分隔符的格式由操作系统决定
java -classpath .;C:\work\project1\bin;C:\shared abc.xyz.Hello

# 可以将classpath简写为cp
java -cp .;C:\work\project1\bin;C:\shared abc.xyz.Hello

# 按约定不设置系统环境变量，并且没有传入cp参数时，默认classpath是当前命令的目录
java abc.xyz.Hello

```