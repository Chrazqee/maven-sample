# First maven project
## 安装 maven
- 下载 Maven：`wget https://dlcdn.apache.org/maven/maven-3/3.9.9/binaries/apache-maven-3.9.9-bin.tar.gz` 
- 解压 Maven: `tar -zxvf apache-maven-3.9.9-bin.tar.gz`
    - `mv apache-maven-3.9.9 /usr/local/` 不是必须的
- 配置 Maven: 
    - `sudo vim ~/.bashrc`
    - 添加 MAVEN_HOME 和 PATH
        - `export MAVEN_HOME=/usr/local/apache-maven-3.9.9`
        - `export PATH=$MAVEN_HOME/bin:$PATH`
    - 修改仓库源 和 本地仓库 目录
        - 打开文件 `/usr/local/apache-maven-3.9.9/conf/setting.xml`
        - 添加:
            ```xml
            <localRepository>/home/chrazqee/maven_local_repo</localRepository>

            <mirror>
                <id>alimaven</id>
                <name>阿里云公共仓库</name>
                <url>https://maven.aliyun.com/repository/public</url>
                <mirrorOf>*</mirrorOf>
            </mirror>

            <profiles>
            <profile>
                <id>jdk-18</id>
                <activation>
                    <activeByDefault>true</activeByDefault>
                    <jdk>18</jdk>
                </activation>
                <properties>
                    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
                    <maven.compiler.source>18</maven.compiler.source>
                    <maven.compiler.target>18</maven.compiler.target>
                </properties>
            </profile>
            ```
    - 配完成之后, 检查 maven 是否安装成功
        - `mvn -v`
## 使用 Maven 构建一个项目
```bash
mvn archetype:generate -DgroupId=com.chrazqee -DartifactId=maven-sample -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.5 -DinteractiveMode=false
```
命令的解释：
```text 
mvn archetype:generate 
-DgroupId=com.chrazqee  # 组织或者公司名称
-DartifactId=maven-sample  # 项目名称
-DarchetypeArtifactId=maven-archetype-quickstart  # 模板名称
-DarchetypeVersion=1.5  # 模板版本
-DinteractiveMode=false 
```
## 得到如下项目目录
```
.
└── maven-sample
    ├── pom.xml
    ├── src
    │   ├── main
    │   │   └── java
    │   │       └── com
    │   │           └── chrazqee
    │   │               └── App.java
    │   └── test
    │       └── java
    │           └── com
    │               └── chrazqee
    │                   └── AppTest.java
    └── target
        ├── classes
        │   └── com
        │       └── chrazqee
        │           └── App.class
        └── test-classes
            └── com
                └── chrazqee
                    └── AppTest.class
```
## 编译项目，使用 `mvn compile`
- `cd maven-sample`
- `mvn compile`

## 打包，用于生成jar包
- `mvn package`

## 执行项目
- `java -cp target/maven-sample-1.0-SNAPSHOT.jar com.chrazqee.App`
    ```text
    java -cp # -cp -> classpath
    target/maven-sample-1.0-SNAPSHOT.jar  # jar包的路径
    com.chrazqee.App  # main方法所在的类
    ```
# jar 包的说明
```xml
  <!-- groupId + artifactId + version 唯一标识一个 jar 包 -->
  <groupId>com.chrazqee</groupId>  <!-- 组织的唯一标识 -->
  <artifactId>maven-sample</artifactId>  <!-- 项目的唯一标识 -->
  <version>1.0-SNAPSHOT</version>  <!-- 项目的版本号; 区分不同版本的 jar 包; 分为 snapshot 快照版 和 release 正式版 -->
```
在仓库中, 可以根据这些信息找到确定的 jar 包;
查找开源 jar 包: [`mvn repository`](https://mvnrepository.com/) 这个网站查找.

- 当我们在 `pom.xml` 中添加一个依赖的时候, 本地仓库可能没有下载这个依赖, 那么,我们只需要 编译 一下即可完成下载

# 依赖范围: scope
- compile: 编译运行都需要, 默认; 可以省略不写
- provided: 编译时需要, 但是运行时不需要; 如 `Lombok` jar包(简化 getter setter 构造函数等, 编译完成, 运行不需要)
- runtime: 运行时需要, 编译时不需要; 如 jdbc 驱动的 jar包
- test: 测试需要,不会被打包到最终的jar包, 正式的业务代码中不需要;
- system: `<scope>system</scope>` 手动指定 jar 包的位置: `<systemPath>${basedir}/lib/junit-3.8.1.jar</systemPath>`
    - 不推荐, 建议是将 jar 包传到私服仓库中, 在 pom.xml 文件中直接引用下载
- import: 在 dependencyManagement 中使用; 导入其他 pom 文件中的依赖, 但是不会实际引入依赖, 管理依赖的版本号

# 依赖冲突
- 最短路径优先
- 先声明优先: 配置顺序靠前的
- 手动解决
    - `exclusions` 标签排除不需要的依赖; 标签指定 `groupId` 和 `artifactId` 即可, 不需要指定版本号
    - `optional` 标签标记一个依赖是可选的; `<optional>true</optional>`

# 父子工程
- 新建父工程: 将 `<packaging>jar</packaging>` 改成 `<packaging>pom</packaging>`; 并删除 `src` 目录
    - 聚合
        ```xml
        <modules>
            <module>child-a</module>
            <module>child-b</module>
            <module>child-c</module>
        </modules>
        ```
- 新建子工程: 新建完成后,会出现一个标签,内容如下:
    ```xml
    <parent>
        <groupId>com.chrazqee</groupId>
        <artifactId>maven-parent</artifactId>  <!-- maven-parent 是最外部的父工程的工程名 -->
        <version>1.0-SNAPSHOT</version>
    </parent>

    <artifactId>maven-parent</artifactId>
    <packaging>jar</packaging>
    ```

