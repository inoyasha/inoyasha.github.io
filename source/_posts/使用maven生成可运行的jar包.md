title: 使用maven生成可运行的jar包
tags:
  - maven
categories:
  - 构建工具
author: 郭小黑
date: 2019-01-24 14:43:00
---

#### 引言

maven可以使用 <code>mvn package</code> 命令对项目打包，但是使用 <code>java -jar xxx.jar</code>执行jar文件，会出现**no main manifest attribute, in xxx.jar**的错误。因为是没有配置<code>Main-class</code>

下面我们介绍如果使用maven生成可运行jar包

<!-- more -->

#### jar包能够通过java -xxx.jar执行的条件

- 在jar包中的META-INF/MANIFSET.MF中指定Main-Class。
&nbsp;&nbsp;&nbsp;&nbsp;这样才能确定程序的如果在哪里。
- 需要加载到依赖包。

#### 有两种方法可以生成可执行jar

##### 使用maven-jar-plugin和maven-dependency-plugin插件打包

在pom.xml中配置

```pom
<build>
	<plugins>

		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-jar-plugin</artifactId>
			<version>2.6</version>
			<configuration>
				<archive>
					<manifest>
						<addClasspath>true</addClasspath>
						<classpathPrefix>lib/</classpathPrefix>
						<mainClass>com.xxg.Main</mainClass>
					</manifest>
				</archive>
			</configuration>
		</plugin>
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-dependency-plugin</artifactId>
			<version>2.10</version>
			<executions>
				<execution>
					<id>copy-dependencies</id>
					<phase>package</phase>
					<goals>
						<goal>copy-dependencies</goal>
					</goals>
					<configuration>
						<outputDirectory>${project.build.directory}/lib</outputDirectory>
					</configuration>
				</execution>
			</executions>
		</plugin>

	</plugins>
</build>
```
<code>maven-jar-plugin</code>用于生成<code>META-INF/MANIFEST.MF</code>文件的部分内容<code><mainClass>com.xxg.Main</mainClass></code>指定<code>MANIFEST.MF</code>中的<code>Main-Class</code>，<code>addClasspath节点等于true</code>会在<code>MANIFEST.MF</code>加上<code>Class-Path</code>项并配置依赖包，<classpathPrefix>lib/</classpathPrefix>指定依赖包所在目录。

例如下面是一个通过maven-jar-plugin插件生成的MANIFEST.MF文件片段：

```manifest.mf
    Class-Path: lib/commons-logging-1.2.jar lib/commons-io-2.4.jar  
    Main-Class: com.xxg.Main  
```

只是生成<code>MANIFEST.MF</code>文件还不够，<code>maven-dependency-plugin</code>插件用于将依赖包拷贝到<code>${project.build.directory}/lib</code>指定的位置，即lib目录下。

配置完成后，通过mvn package指令打包，会在target目录下生成jar包，并将依赖包拷贝到target/lib目录下。

##### 使用maven-assembly-plugin插件打包

pom 配置如下

```pom
 <project>
        <build>
            <plugins>
                <!--(start) for package jar with dependencies -->
                <plugin>
                    <artifactId>maven-assembly-plugin</artifactId>
                    <version>3.0.0</version>
                    <configuration>
                        <archive>
                            <manifest>
                                <mainClass>Main</mainClass>
                            </manifest>
                        </archive>
                        <descriptorRefs>
                            <descriptorRef>jar-with-dependencies</descriptorRef>
                        </descriptorRefs>
                    </configuration>
                    <executions>
                        <execution>
                            <id>make-assembly</id> <!-- this is used for inheritance merges -->
                            <phase>package</phase> <!-- bind to the packaging phase -->
                            <goals>
                                <goal>single</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
                <!--(end) for package jar with dependencies -->
            </plugins>
        </build>
    </project>
```

mavan 打包命令

```command
mvn package
```


**ps:** 当打包过程中，如果某一个jar包下载失败，导致不能打包。那么在本地仓库删除这个jar，然后使用<code>mvn test</code>命令重新编译，在test的过程中会重新下载jar包。在eclipse编辑器打包的时候打包的对应的jdk版本是eclipse的默认jdk版本。如果出现maven和jdk版本不统一可以检查是不是jdk版本不对。

参考文章：

[[Maven]Maven构建可执行的jar包(包含依赖jar包)](https://www.cnblogs.com/dzblog/p/6913809.html)
[使用Maven对JAVA程序打包－带主类、带依赖](http://blog.csdn.net/strongyoung88/article/details/54097830)