## Maven简介
> Maven 是 Apache 下的项目管理工具，它由纯Java语言开发，可以帮助我们更方便的管理和构建Java项目。
>

## Maven功能

Maven 能够帮助开发者完成以下工作：

- 构建
- 文档生成
- 报告
- 依赖
- SCMs
- 发布
- 分发
- 邮件列表

## Maven环境配置

​		[Maven环境配置](https://github.com/MrSayyes/Grocery-store/blob/master/环境配置/Maven环境配置.md)

## Maven的核心概念

1. 约定的目录结构
2. POM
3. 坐标
4. 依赖
5. 仓库
6. 生命周期/插件/目标
7. 继承
8. 聚合

## Maven学习过程

自动化构建工具：make---ant---maven---gradle

1、eclipse的maven设置：核心程序路径，settings.xml配置文件路径

2、maven java-project新增，使用简单模板创建，如果不勾选则按需要选择模板类型

3、settings.xml设置jdk版本，否则maven创建工程默认1.5

```xml
<profiles>
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
</profiles>
```

4、建一个类，在pom.xml右击Run As执行maven指令

5、web-project新增，选择war包

6、调整project facets里面的web动态设置，先去掉，然后再勾选有个配置提示进行处理即可

7、新增jsp会报错，通过eclipse添加阿帕奇处理可以，但是这里通过maven管理provided范围依赖添加servlet-api

```xml
<dependencies>
	<dependency>
		<groupId>javax.servlet</groupId>
		<artifactId>servlet-api</artifactId>
		<version>2.5</version>
		<scope>provided</scope>
	</dependency>
</dependencies>
```

8、jsp文件使用jsp代码，需要依赖jsp-api

```xml
<dependency>
	<groupId>javax.servlet.jsp</groupId>
	<artifactId>jsp-api</artifactId>
	<version>2.1.3-b06</version>
	<scope>provided</scope>
</dependency>
```

9、依赖传递：如工程A依赖B，B依赖C，在C中加了包依赖，那么A和B可以被传递使用；前提是compile范围才可以传递

10、依赖排除设置，在依赖包的dependency下设置

```xml
<exclusions>
	<exclusion>
		<groupId>组id</groupId>
		<artifactId>包id</artifactId>
	</exclusion>
</exclusions>
```

11、依赖的原则

- 多组传递，路径长度不同，路径最短优先
- 多组传递，路径长度相同，声明优先

12、统一管理依赖的版本

- properties自定义标签，使用${com.sayyes.version}来赋值，这个不局限在版本设置

```xml
<properties>
	<com.sayyes.version>1.0</com.sayyes.version>
</properties>
```

13、继承

- 现状：不可传递的依赖，例如生命周期为test，provided

- 需求：统一管理各个模块工程中对该依赖的版本

- 解决思路：将依赖包统一提取到“父”工程中，在子工程中声明依赖包不指定版本号，以父工程设定的版本号为准。

- 操作步骤：

  1、创建packaging为pom的maven工程；

  2、在子工程中声明对父工程的引用，不设置版本号

  ```xml
  <parent>
  	<groupId>com.sayyes.parent</groupId>
  	<artifactId>Parent</artifactId>
  	<version>0.0.1-SNAPSHOT</version>
  	<!-- 以当前文件为基准的父工程的pom.xml文件的相对路径 -->
  	<relativePath>../根据情况设置/pom.xml</relativePath>
  </parent>
  ```

  3、如果有重复的依赖，可以删除子工程

  4、在父工程pom.xml配置依赖包的管理

  ```
  <dependencyManagement>
  	<dependencies>
  		<dependency>
  			...设置统一依赖...
  		</dependency>
  	</dependencies>
  </dependencyManagement>
  ```

  5、在子工程中删除版本号的部分
  
  6、配置继承后，执行安装命令时先安装父工程

14、聚合

- 作用：一键安装各个模块工程

- 配置方式：在一个“总的聚合工程”中配置各个参与聚合的模块

  1、可以单独建聚合工程

  2、可以设置在parent中，顺序无关（依赖自动识别）

  ```xml
  <!-- 配置聚合 -->
  <modules>
  	<module>../子工程的相对路径</module>
  	<module>../子工程的相对路径</module>
  </modules>
  ```

  3、单独执行配置该配置的工程执行maven安装即可把配置的模块一起安装

15、工程自动部署

——cmd里面执行mvn deploy（eclipse只能起不能停）

——这里随着工具的完善，已经不需要这样的自动部署，直接eclipse常规操作部署即可

```xml
	<build>
		<finalName>WebProject</finalName>
		<plugins>
			<plugin>
				<!-- cargo是一家专门从事“启动servlet容器"的组织 -->
				<groupId>org.codehaus.cargo</groupId>
				<artifactId>cargo-maven2-plugin</artifactId>
				<version>1.2.3</version>
				<!-- 针对插件进行的配置 -->
				<configuration>
					<!-- 配置当前系统中容器的位置 -->
					<container>
						<containerId>tomcat9x</containerId>
						<home>F:\software\tomcat\apache-tomcat-9.0.22</home>
					</container>
					<configuration>
						<type>existing</type>
						<home>F:\software\tomcat\apache-tomcat-9.0.22</home>
						<!-- 如果tomcat端口默认8080不需要配置 -->
						<properties>
							<cargo.servlet.port>8989</cargo.servlet.port>
						</properties>
					</configuration>
				</configuration>
				<!-- 配置插件在什么情况下执行 -->
				<executions>
					<execution>
						<id>cargo-run</id>
						<!-- 声明周期的阶段 -->
						<phase>install</phase>
						<goals>
							<!-- 插件的目标 -->
							<goal>run</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>
```

16、Maven查找依赖信息的网站

Maven酷站：https://mvnrepository.com/

如查找Spring-core，然后查找出来点进去，可以在页面找到Maven的设置信息

















