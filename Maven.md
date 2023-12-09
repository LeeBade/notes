# JRE

- JRE包括以下组件
  - JVM：Java虚拟机Java Virtual Machine：将Java字节码编译成操作系统机器代码并执行Java应用程序
  - Java标准库；
  - Java运行时环境支持文件，如：运行时配置文件、字体文件、本地化资源文件等
  - 执行工具：用于启动Java应用程序的执行工具
    - java命令：运行Java应用程序
    - javac：编译Java源代码为字节码文件和jar
- jar包：字节码文件，可以直接运行
  - 不包括所依赖的Java标准库，包括所有业务逻辑代码；
- 对于引入的第三方jar包，不应该再次编译，否则会引入很奇怪的东西

# Maven

## 概述

- Maven:专门为 Java 项目提供构建和依赖管理支持的工具
  - jar：引入第三方开发的jar归档文件，直接调用其中的类；
  - jar包依赖其它jar包形成依赖关系，工程引入依赖增加时，依赖关系可能冲突
- 构建过程
  - 清理：删除上一次构建的结果，为下一次构建做好准备
  - 编译：Java 源程序编译成 *.class 字节码文件
  - 测试：运行提前准备好的测试程序
  - 报告：针对刚才测试的结果生成一个全面的信息
  - 打包：jar包/war包
  - 安装：将jar/war存入Maven的本地仓库
  - 部署：jar部署到Nexus私服服务器，通过Maven插件将war部署到Tomcat；
- 依赖管理
  - 子工程继承父工程的依赖
  - 工程依赖类别：
    - 本地Maven仓库的jar包(可以由用户自己将jar包上传到本地仓库)
    - <a href=#框架与库>第三方库或第三方框架的jar包</a>
    - <a href=#maven插件>Maven插件的jar包</a>
  - 自动导入工程直接依赖的jar包所依赖的jar包，并解决依赖关系之间的冲突；对本地没有的jar包将从中央仓库下载；

## 配置Maven核心程序

-  Maven 的核心配置文件：conf/settings.xml
```xml
<!-- 确定默认的本地仓库位置 -->
<localRepository> the path of local repository maven</localRepository>

<mirror>
  <id>maven-default-http-blocker</id>
  <mirrorOf>external:http:*</mirrorOf>
  <name>Pseudo repository to mirror external repositories initially using HTTP.</name>
  <url>http://0.0.0.0/</url>
  <blocked>true</blocked>
</mirror>

<!-- 例如阿里云的镜像 -->
<mirror>
  <id>nexus-aliyun</id>
  <mirrorOf>central</mirrorOf>
  <name>Nexus aliyun</name>
  <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>

<!-- 配置默认JDK版本 -->

<profile>
  <id>jdk-1.8</id>
  <activation>
  <activeByDefault>true</activeByDefault>
  <jdk>1.8</jdk>
  </activation>
  <properties>
  <maven.compiler.source>1.8</maven.compiler.source>
  <maven.compiler.target>1.8</maven.compiler.target>
  <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
  </properties>
</profile>
```
- Java开发，需要JDK
## 仓库

- 本地仓库
- 远程仓库
  - 局域网：自己搭建的Maven私服，例如使用Nexus技术
  - Internet
    - 中央仓库
    - 镜像仓库
## 定位

- 三维向量定位唯一定位jar包(groupld,artifactld,version)
- groupld：工程名，按java规范：公司名/组织域名逆序
  - 例如；org.springframework
- artifactld：工程下的模块名
  - org.springframework由多个模块组成，例如spring-webmvc
- version：
  - SNAPSHOT：快照版本，迭代中的不稳定版本
  - RELEASE：正式版本

```XML
<!-- 该三维向量在本地仓库的路径为：\javax\servlet\servlet-api\2.5\servlet-api-2.5.jar -->
  <groupId>javax.servlet</groupId>
  <artifactId>servlet-api</artifactId>
  <version>2.5</version>

```
## 命令行命令

```BASH
# 列出所有依赖，包括直接依赖和间接依赖
mvn dependency:list
# version
mvn -v 
```



## 依赖生效范围与pom.xml
```bash
# 在该目录生成Maven工程
mvn archetype:generate
```

```XML
<!-- 当前Maven工程的坐标 -->
<groupId>com.atguigu.maven</groupId>
<artifactId>pro01-maven-java</artifactId>
<version>1.0-SNAPSHOT</version>
  
<!-- 当前Maven工程的打包方式，可选值有下面三种： -->
<!-- jar：表示这个工程是一个Java工程  -->
<!-- war：表示这个工程是一个Web工程 -->
<!-- pom：表示这个工程是“管理其他工程”的工程 -->
<packaging>jar</packaging>

<properties>
  <!-- 工程构建过程中读取源码时使用的字符集 -->
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>

 <!-- 使用dependency配置一个具体的依赖 -->
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.12</version>
<!-- scope标签配置依赖的范围 -->
<!-- test，指定该依赖只有在测试(生命周期的一部分)时期会被包含在项目中 -->
<!-- compile：默认的依赖范围，在编译、测试、运行时都有效 -->
<!-- runtime：只在运行时需要的库/框架，例如JDBC驱动 -->
<!-- provided：服务器自带，比如servlet-api，避免与服务器产生冲突 -->
  <scope>test</scope>
</dependency>
```

# POM

- POM：Project Object Model，项目对象模型
  - DOM：Document Object Model，文档对象模型
  - 二者类似，具体体现模型化思想

## Maven目录

- 约定>配置，配置>编码
- src
  - main：主体程序
    - java：源码
      - com：package目录
      - resources：配置文件
  - test：测试程序目录
    - java：Java源码
      - com：package目录
- target：jar包、war包
- 约定
  - 主体程序目录中是被测试的程序，即运行时类
  - 测试程序目录使用测试框架，在测试(生命周期之一)时运行

## 工程继承

- 在父工程中统一管理项目依赖关系，并创建子工程，子工程中的pom.xml将继承父工程的pom.xml的配置允许子工程配置自己的依赖关系
- 父工程不应有业务代码，其pom.xml文件中的打包方式应为
```XML
<!-- 指示该工程为父工程，其子工程应继承该父工程的依赖关系 -->
<packaging>pom</packaging>
```
- 在父工程中创建子工程
```BASH
mvn archetype:generate
```
- 创建完成后可以在父工程的pom.xml文件中找到子工程的信息
```XML
<modules>  
  <module>son-module</module>
</modules>
```
- 创建完成后在子工程中可以找到父工程的信息
```XML
<!-- 使用parent标签指定当前工程的父工程 -->
<parent>
  <!-- 父工程的坐标 -->
  <groupId>com.atguigu.maven</groupId>
  <artifactId>pro03-maven-parent</artifactId>
  <version>1.0-SNAPSHOT</version>
</parent>

<!-- 子工程的坐标 -->
<!-- 如果子工程坐标中的groupId和version与父工程一致，那么可以省略 -->
<!-- <groupId>com.atguigu.maven</groupId> -->
<artifactId>pro04-maven-module</artifactId>
<!-- <version>1.0-SNAPSHOT</version> -->
```
- 子工程引入依赖时不应指定version版本信息
- 在父工程中自定义属性
  - 一处修改，处处生效：属性中定义变量，然后在dependency中使用该变量
```XML
<!-- 通过自定义属性，统一指定Spring的版本 -->
<properties>
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  <!-- 自定义标签，维护Spring版本数据 -->
  <atguigu.spring.version>4.3.6.RELEASE</atguigu.spring.version>
</properties>

<dependencies>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <!-- 在此处使用<properties>标签中的atguigu.spring.version变量 -->
    <!-- 在xml文件中通过${}的形式引用变量 -->
    <version>${atguigu.spring.version}</version>
  </dependency>
</dependencies>
```

## 工程聚合

- 将多个工程聚合在同一个总工程下，在总工程的pom.xml目录下使用`mvn install`
- 依赖循环$A\to B\to C\to A$
# 生命周期

## 构建jar

- 在pom.xml所在目录执行命令

```bash
# 1.清理
# 删除 target 目录
mvn clean

# 2.编译
# 编译主程序，编译结果目录target/classes
# mvn compile，编译结果目录target/test—classes
# 编译测试程序
mvn test-compile

# 3.测试
# 测试报告存放在target/surefire-reports
# 执行测试将自动编译
mvn test

# 4.打包，jar包将存放在target目录
mvn package

# 5.安装，存入本地仓库
# pom.xml更名为XXX.pom
mvn install
```

## 构建war

- 构建Web工程需要不同于java工程的archetype模板
- project
- pom.xml
- src
  - main
    - java：源码
      - com：Servlet类所在目录
    - webapp
      - index.jsp
      - WEB-INF
        - web.xml

- 运行命令依据archetype模板创建Web工程

```BASH
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes-DarchetypeArtifactId=maven-archetype-webapp-DarchetypeVersion=1.4
```

- 在pom.xml文件中是
```xml
<packaging>war</packaging>
```
- 在java目录下创建软件包并创建Servlet类
```Java
public class HelloServlet extends HttpServlet{
  protected void doGet(
          HttpServletRequest request,
          HttpServletResponse response)
          throws ServletException, IOException {
    response.getWriter().write("hello maven web");
  }
  
}
```
- 在web.xml中注册Servlet
```XML
<servlet>
  <!-- 指示tomcat将指定全路径的类初始化为指定名字的对象 -->
  <servlet-name>helloServlet</servlet-name>
  <servlet-class>com.TiTian.HelloServlet</servlet-class>
</servlet>
<servlet-mapping>
  <!-- 对目标url是classpath+指定url的请求转发给该对象 -->
  <servlet-name>helloServlet</servlet-name>
  <url-pattern>/helloServlet</url-pattern>
</servlet-mapping>
```

- mvn package并复制到tomcat/webapps目录下，重启tomcat，访问8080端口的指定路径

## jar与war

- java工程将导出jar供其它war工程或java工程使用
- Web工程所导出的war包用于生产环境
- 编译后WEB-INF/lib 目录将存放war所依赖的jar包
## 依赖

- $A\to B\to C$，A能否使用C取决于B依赖C的关系的生效时期；
  - $B\to C$：compile，允许传递
  - $B\to C$：test or provided，不允许传递
- $A\to B\to C$，当$B\to C$是compile时，使用以下方式破坏$A\to C$的依赖关系
```xml
<dependency>
  <groupId>A</groupId>
  <artifactId>A</artifactId>
  <version>A</version>
  <!-- 使用excludes标签配置依赖的排除  -->
  <exclusions>
    <!-- 在exclude标签中配置一个具体的排除 -->
    <exclusion>
      <!-- 指定坐标（不需要写version） -->
      <groupId>C/D/E...</groupId>
      <artifactId>C/D/E...</artifactId>
    </exclusion>
  </exclusions>
</dependency>

```
# Others

## 框架与库
- Invertion of control: your code calls a library but a framework calls your code, 
## maven插件

<A href="https://www.runoob.com/maven/maven-plugins.html">maven插件</A>
- Maven 的核心程序仅仅负责宏观调度，不做具体工作。具体工作都是由 Maven 插件完成的。例如：编译就是由 maven-compiler-plugin-3.1.jar 插件来执行的。





# EOF