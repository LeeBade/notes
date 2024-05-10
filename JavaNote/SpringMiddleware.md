# Docker

# Maven

### 概述

Maven-java项目标准的项目结构
  - pom.xml：项目描述文件
  - src
    - main
      - java：Java源码
      - resources：资源文件
    - test
      - java：测试源码
      - resources：测试资源
  - target：.class、jar


maven使用3个变量唯一标识jar包
- groupId：组织/公司名，artifactId：jar包名，version：jar包版本
- 三个向量依次构成在本地仓库的路径

### 依赖关系

$A\to B\to C$，A能否使用C取决于B依赖C的关系的生效时期；
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

### 生命周期

Maven有三个标准生命周期：clean、default/build、site
- clean：删除上一次构建的结果，为下一次构建做好准备
  - pre-clean
  - clean
  - post-clean
- default/build
  - compile：将源程序编译成 .class字节码文件
  - test：运行提前准备好的测试程序
  - package：jar包/war包
  - install：将jar/war存入Maven的本地仓库
  - deploy：jar部署到Nexus私服服务器，通过Maven插件将war部署到Tomcat；
- 每个lifecycle由各phase组成，phase作为maven的接口，需要插件作为实现

| 插件     | 描述                           |
|----------|--------------------------------|
| clean    | 构建之后清理目标文件。删除目标目录。|
| compiler | 编译 Java 源文件。               |
| surefile | 运行 JUnit 单元测试。创建测试报告。|
| jar      | 从当前工程中构建 JAR 文件。        |
| war      | 从当前工程中构建 WAR 文件。        |
| javadoc  | 为工程生成 Javadoc。              |
| antrun   | 从构建过程的任意一个阶段中运行一个 ant 任务的集合。|

- 每个插件可挂钩多个goal



### maven命令

| 命令                 | 作用                                                         |
|----------------------|--------------------------------------------------------------|
| mvn clean            | 清理所有生成的 class 和 jar                                 |
| mvn clean compile    | 先清理，再执行到 compile                                   |
| mvn clean test       | 先清理，再执行到 test，因为执行 test 前必须执行 compile，所以这里不必指定 compile |
| mvn clean package    | 先清理，再执行到 package                                   |
| mvn dependency:list  | 列出所有依赖，包括直接依赖和间接依赖                       |
| mvn -v               | 显示 Maven 的版本信息                                        |
| mvn archetype:generate              | 在该目录生成Maven工程                                        |




### pom.xml

子工程引入依赖时不应指定version版本信息

```XML
<!-- 当前Maven工程的坐标 -->
<groupId>com.atguigu.maven</groupId>
<artifactId>pro01-maven-java</artifactId>
<version>1.0-SNAPSHOT</version>

<!-- 子工程的坐标 -->
<!-- 如果子工程坐标中的groupId和version与父工程一致，那么可以省略 -->
<!-- <groupId>com.atguigu.maven</groupId> -->
<artifactId>pro04-maven-module</artifactId>
<!-- <version>1.0-SNAPSHOT</version> -->

<!-- 当前Maven工程的打包方式，可选值有下面三种： -->
<!-- jar：该配置管理Java工程  -->
<!-- war：该配置管理Web工程 -->
<!-- pom：该配置管理其它配置文件 -->
<packaging>jar</packaging>

<!-- 使用parent标签指定当前工程的父工程 -->
<parent>
  <!-- 父工程的坐标 -->
  <groupId>com.atguigu.maven</groupId>
  <artifactId>pro03-maven-parent</artifactId>
  <version>1.0-SNAPSHOT</version>
</parent>

<modules>  
  <module>son-module</module>
</modules>

<properties>
  <!-- 工程构建过程中读取源码时使用的字符集 -->
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>

 <!-- 该标签标注的jar包将被下载并放入classpath -->
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.12</version>
<!-- scope标签配置依赖的范围 -->
<!-- test：仅测试时使用 -->
<!-- compile：默认的依赖范围，在编译、测试、运行时都有效，因为在源码中引用了其接口、类或资源-->
<!-- runtime：不会被编译，只在运行时被加载，因为源码仅需要其库/框架，例如JDBC驱动 -->
<!-- provided：编译时需要但运行时不需要，运行环境已有相关字节码，避免与运行环境产生冲突，例如Servlet服务器内置Servlet API -->
  <scope>test</scope>
</dependency>
```



# Git
```BASH
# 在当前目录生成.git文件夹,用于跟踪管理对该仓库每个文件的修改删除
git init
```
- 更改当前目录为Git管理的仓库
  - Git追踪对文本文件每一次改动，并记录改动的内容
  - 只能记录二进制文件大小的改动，Microsoft的Word格式是二进制格式
  - Microsoft记事本在保存UTF-8编码文件时，在每个文件开头添加了0xefbbbf字符

- 将文件添加到仓库
  - 多次add提交到暂存区
  - 一次commit生成一次快照
```BASH
# 提交到暂存区
git add readme.md
# 将暂存区文件推送到本地仓库，并使用-m附带提交说明
git commit -m "wrote a readme file"

[master (root-commit) eaadf4e]# SHA-1 哈希值唯一标识本次提交
"wrote a readme file"#本次提交的提交消息
1 file changed, 2 insertions(+)# 本次提交更改一个文件，插入两行新内容
create mode 100644 readme.txt#Git 分配了文件 "readme.txt" 的文件模式为 100644
```

## git status

```bash
git status
# modified:   readme.md
    # 该文件被修改
# no changes added to commit
    # 暂存区没有文件
# 上述消息表明readme.txt已经被修改，但是未被add到暂存区


# Untracked指示从来没有被commit的文件
```

## git diff
```bash
# 查看对readme.md做了哪些修改
git diff readme.md

diff --git a/readme.txt b/readme.txt# 比较readme.md文件和readme.md文件,指示文件名是否修改
index 46d49bf..9247db6 100644# 旧版本索引 新版本索引 该文件的文件模式
--- a/readme.txt# 标识旧版本的文件路径
+++ b/readme.txt# 标识新版本的文件路径
@@ -1,2 +1,2 @@#被修改行的索引
    #-1,2代表旧文件从第一行开始的两行内容
    #+1,2代表新文件从第一行开始的两行内容
-Git is a version control system.#-代表被修改的旧版本内容
+Git is a distributed version control system.
 Git is free software#加号代表新版本中的内容

 nothing to commit, working tree clean#指示没有别修改的内容,暂存区么有文件等待提交
```

```bash
# 比较Head所指快照与readme.txt之间的内容变化
git diff HEAD -- readme.txt
```

## 快照/命令时间轴
- Commit时间轴
  - 通常情况下，在只有master分支的情况下，master指针指向最新的快照，head通过指向master指针指向最新的快照；
  - 当回退到master分支的历史版本时，head指针会指向历史快照，而master分支仍不动，持续指向最新快照，此时处于分离头指针状态；
  - 在分离头指针状态下commit会创建匿名分支，使用匿名的头指针指向该分支的最新快照；避免分离头指针的状态
    - 如果使用头指针表示一个分支的最新快照，令head指针切换分支后，将非常难以再次切换到该分支
    - 但是可以通过git log查看分支的哈希值，通过哈希值合并切换分支；
  - 记录每次Commit的id、commit message、snapshot、committer、commit time
  - Head指针：指向当前工作区所使用的版本
  - 每份快照是完整的文件，因此回退速度很快；
```bash
#列出Head指针及Head指针之后的Commit信息；
git log
# reset操作
# hard指示变更工作区和暂存区内容
# HEAD^：指示Head指针指向下1个节点
# HEAD^^指示Head指针指向下2个节点
# HEAD~100指示Head指针指向下100个节点
# --hard 46d49bf命令hard指向46d49bf快照
git reset --hard HEAD^

```

- Command时间轴
```bash
git reflog

# Head指针移动，并且指示快照id
e475afc HEAD@{1}: reset: moving to HEAD^1094adb (HEAD->master) HEAD@{2}: commit: append GPL
# 在Commit时间轴上增加快照的操作，该快照目前索引3；
e475afc HEAD@{3}: commit: add distributed
# 在Commit时间轴上增加快照的操作，该快照目前索引4；
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```

- 通过Command时间轴可查询回退版本操作的两个快照的id


## 区域

- 仓库分为工作区和版本库(隐藏文件夹.git)
- .git文件夹中的index文件夹是暂存区
- 将指定目录更改为仓库时，Git会自动创建第一个分支Master，以及指向master的指针head


## git checkout

```bash
# 第一种情况，修改前未add，将回退到与Head一致的情况
# 第二种情况，修改前已add，将回退到暂存区中的情况
git checkout -- readme.txt
```

```BASH
# 不影响在工作目录中的实际文件，只是将其从暂存区中移除，使其不包含在下一次的提交中
git reset HEAD readme.txt
```


## git rm

- 在本地删除文件后将与版本库不一致，使用git status检查状态

```bash
git status

# 以下提示信息代表，与当前快照相比，删除了test.txt，但是未提交到版本库
deleted:    test.txt
git status
```

- 删除后从版本库恢复文件
```
git checkout -- test.txt
```
- 删除后将删除操作add到暂存区，再commit到版本库生成快照；
```bash
# 将删除操作添加到暂存区
git rm test.txt
# 推送到版本库形成快照
git commit -m "remove operation"
```

# 远程仓库与Github

- 在Git仓库之间传输通过SSH加密

```bash

# 第一步：为本系统用户创建与邮箱关联的SSH密钥
ssh-keygen -t rsa -C "ryyfv504026@163.com"

# 第二步：在Github中添加添加公钥，即id_rsa.pub中的内容，允许该系统用户向Github仓库推送
# 当SSH建立时，系统用户将使用id_rsa中的密钥验证身份，不应将密钥告诉他人，只能将公钥分享

# 第三步：关联本地仓库和Github仓库
# 对本地仓库而言，该远程仓库的名字是origin
# 当创建Github仓库时，会创建隐藏的.git文件
# @github.com:LeeBade指明用户
# /learngit.git指明将本地仓库与learngit仓库关联
git remote add origin git@github.com:LeeBade/Infinite.git

# 第四步：将本地仓库中的内容推送到learngit仓库
# git push: 将本地的提交推送到关联的远程仓库origin
# -u: 该标志表示 "upstream"，建立本地分支与远程分支之间的追踪关系，在推送和拉取时不再需要指定远程和分支
# origin: 指定远程仓库的名称
# master: 被推送的本地分支的名称
# 在指定仓库中将出现新的master分支
git push -u origin master

# 使用以下命令将本地仓库推送到origin
git push origin master
```

- 从任意远程库克隆
```bash
git clone git@github.com:username/repositoryname.git
```

git clone git@github.com:suyuhuan/php-bbs
gh repo clone suyuhuan/php-bbs



- 也可以使用https协议进行传输，但是每次推送都需要口令，且传输速度慢；

# Git分支与指针

- 创建仓库时，将创建Master和Head指针
  - Master将指向索引为0的头节点(存疑)
  - Head指针将指向Master指针
- 分支：链表
  - 每个节点代表commit的快照
  - 每个节点：commit时间、唯一的哈希值、执行commit的用户...
  - 每个快照是完整的文件集合
- Branch指针：与分支同名，总是指向该分支的最新快照
  - Master指针属于Branch指针的一种，与其它Branch指针等地位
- Head指针
  - 代表当前工作目录所参照的快照
  - 通常Head指针指向Branch指针，然后指向最新快照
  - 当回退版本时，Head将沿该分支回溯至指定版本，处于头指针分离状态；
- commit操作
  - 当Head指针指向Branch指针时，将创建新节点并移动Branch指针，但是Head指针不会移动
  - 当Head指针处于头指针分离状态时，将创建匿名Branch和匿名Branch指针，然后让Head指针指向Branch指针，并创建新节点并移动Branch指针；
- 创建分支操作
  - 创建一个Branch指针，指向Head指针所指的快照、或Head指针所指Branch指针所指快照
- 切换分支
  - 将Head指针指向Branch指针；

## 操作

- `git checkout`：如果没有该分支就创建该分支，但是不会切换到该分支；如果该分支已存在，则切换到该分支
```BASH
# 创建分支，-b创建后切换到dev分支
git checkout -b dev

# 查看当前分支
git branch

# 将Master指向dev所指快照，但是不会自动删除dev指针/dev分支
git merge dev

# 删除分支
git branch -d dev
```

- switch
  - git checkout -- <file>回退对工作目录中文件的操作
  - 容易混淆，因此提供`git switch`
```bash
# 创建并切换到dev分支
git switch -c dev

# 切换到master分支
git switch master
```

## 解决冲突

- `git merge`时，处于`Fast forward`模式
  - 指定Branch指针所指的快照与Head所指Branch指针所指的快照不存在差别，因此不需要创建新的快照，并且丢失之前的分支信息
  - 创建新的快照，进行合并
  - 执行失败，在同样的行存在冲突
- `git merge --no-ff`使用普通模式合并
## 分支策略

- 仅在master分支发布新版本
- 在dev分支进行开发