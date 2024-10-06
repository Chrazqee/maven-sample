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
