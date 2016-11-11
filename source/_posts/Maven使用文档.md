---
title: Maven使用文档
date: 2016-11-01 15:32:02
tags: [maven, java]
categories: Java
---
#### 项目对象模型

##### 项目依赖

Maven可以管理内部依赖和外部依赖，外部依赖可能是像`Spring` `Hibernate`之类的第三方库，而内部依赖则表示依赖一个项目中的服务类、模型类之类的
<!-- more -->
```xml
<project>
    ...
    <dependencies>
        <dependency>
            <groupId>org.codehaus.Xfire</groupId>
            <artifactId>Xfire-java5</artifactId>
            <version>1.2.5</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.geronimo.specs</groupId>
            <artifactId>geronimo-servlet_2.4_spec</artifactId>
            <version>1.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>com.fuyou</groupId>
            <artifactId>fuiou-encrypt</artifactId>
            <version>1.0.0</version>
            <scope>system</scope>
            <systemPath>${basedir}/libs/fuiou-encrypt.jar</systemPath>
        </dependency>
        ...
    </dependencies>
</project>
```

上面例子中提供了一个外部依赖的配置示例，其中也包含了**依赖范围**的配置：

**`compile`**（编译范围）：这是默认的范围表示这个包在编译过程中依赖，同时也会被打包

**`provided`***（已提供）：这个范围一般只在JDK或者运行的容器已提供相关包时才使用。表示这个包只在编译时使用，将不会被打包

**`runtime`**（运行时）：只在运行时需要，而编译时不需要依赖

**`test`**（测试范围）：在编译和运行时都不需要依赖，只在编译测试代码和运行测试时依赖

**`system`**（系统范围）：在本地路径中提供，需要手动指定一个路径供maven编译时查找，这个路径应该在编译和运行时都是可用的；最好在项目根目录中创建一个`libs`目录存放这样的软件包

> 上面的每一种依赖返回在处理依赖传递时有不同的行为，详细参考[官方文档](http://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#Dependency_Scope)

**可选依赖**  
如果项目的某一个模块或者功能需要依赖一个包，而这个功能又是可选的，不影响主要功能的使用；那么我们可以将这个依赖包设置为可选的，这样如果用户不需要这个功能则不必提供这个依赖配置，如果用户需要使用这个功能就需要手动的显式的提供这个依赖包的`artifact`:

```xml
<project>
  ...
  <dependencies>
    <!-- declare the dependency to be set as optional -->
    <dependency>
      <groupId>sample.ProjectA</groupId>
      <artifactId>Project-A</artifactId>
      <version>1.0</version>
      <scope>compile</scope>
      <optional>true</optional> <!-- value will be true or false only -->
    </dependency>
  </dependencies>
</project>
```

**依赖版本界限**  
可以在`<version></version>`中指定一个版本范围来表示一个依赖范围，格式是：`(3.8,)`表示大于3.8版本，`[3.8,]`表示大于等于3.8版本，`[3.8,4.2)`表示大于3.8但是小于等于4.2版本

**传递性依赖**  
如果我们在一个项目中依赖的包自己也依赖其他的包，这就是传递性依赖，默认情况下`maven`会帮我们处理好这种依赖关系和依赖包的导入；但是也有一些特殊情况时的处理规则需要注意：

*   如果项目中依赖的两个不同的包同时又依赖一个相同包的不同版本（可能是间接或者直接），则在`maven 2.0`版本之后项目会使用依赖层次较少的那个版本；如果两个版本的层次相同，则从`maven 2.0.9`开始，最先声明依赖的那个会被使用；当然，显式的声明这两个版本中的一个会覆盖上面两个规则

**排除依赖**  
如果某一个软件包依赖另外一个特定的包，而我们又想使用这个软件包的另外的实现，可以使用如下形式排除依赖并使用另外一个包：
```xml
<project>
    ...
    <dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate</artifactId>
        <version>3.2.5.ga</version>
        <exclusions>
            <exclusion>
                <groupId>javax.transaction</groupId>
                <artifactId>jta</artificatId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.apache.geronimo.specs</groupId>
        <artifactId>geronimo-jta_1.1_spec</artifactId>
        <version>1.1</version>
    </dependency>
</project>
```

**依赖管理**  
如果你的项目是一个比较大的项目，当你想要统一项目中的各个模块所使用的依赖包的版本号时，可以定义一个父`pom.xml`文件在其中定义`<dependencyManagement>`元素，然后在各个子模块的`pom.xml`中使用这个父pom文件并且在指定依赖包时不需要指定版本号

```xml
<project>
    ...
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>5.1.2</version>
            </dependency>
            ...
        </dependencies>
    </dependencyManagement>
</project>
```

```xml
<project>
    ...
    <parent>
        <groupId>org.sonatype.mavenbook</groupId>
        <artifactId>a-parent</artifactId>
        <version>1.0.0</version>
    </parent>
    <artifactId>project-a</artifactId>
    
    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        ...
    </dependencies>
</project>
```

如果在子模块的`pom.xml`中没有定义版本号，则会使用父`pom.xml`中版本号，否则会覆盖父`pom.xml`中版本号

**`依赖归类`**  

依赖归类可以让我们创建一个打包方式为`pom`的项目，这个项目中仅仅定义了一组依赖：

```xml
<project>
    <groupId>org.sonatype.mavenbook</groupId>
    <artifactId>persistent-deps</artifactId>
    <version>1.0</version>
    <packaging>pom</packaging>
    
    <dependencies>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate</artifactId>
            <version>${hibernateVersion}</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-annotations</artifactId>
            <version>${hibernateAnnotationsVersion}</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysqlVersion}</version>
        </dependency>
    </dependencies>
    
    <properties>
        <mysqlVersion>(5.1,)</mysqlVersion>
        <hibernateVersion>3.2.5.ga</hibernateVersion>
        <hibernateAnnotationsVersion>3.3.0.ga</hibernateAnnotationsVersion>
    </properties>
</project>
```
如果你在一个名为`persistent-deps`的目录下创建了这个`pom.xml`文件，那么你只需要运行`mvn install`命令将这个项目安装到本地仓库，然后就可以在其他的项目中使用这个依赖，要注意添加依赖的类型为`pom`：

```xml
<project>
    ...
    
    <dependencies>
        <dependency>
            <groupId>org.sonatype.mavenbook</groupId>
            <artifactId>persistent-deps</artifactId>
            <version>1.0</version>
            <type>pom</type>
        </dependency>
        ...
    </dependencies>
</project>
```

**`导入依赖`**  
todo

#### 构建生命周期

Maven中有三种标准的生命周期：`clean` `default` `site`；每一个生命周期都会包括一些特定的阶段，我们可以将插件的某一个目标绑定在生命周期中的某一个阶段；使用`mvn`命令执行时可以执行某一个插件的某一个目标，也可以执行整个生命周期

除了在某一个阶段绑定插件的目标之外，我们还可以自定义某一个插件的行为，详细的插件的目标和自定义配置可以查阅官方文档的相应[插件文档](https://maven.apache.org/plugins/index.html)

**`clean`**  

运行`mvn clean`将调用这个生命周期，这个生命周期包括三个阶段：`pre-clean` `clean` `post-clean`；默认Clean插件的clean目标（`clean:clean`）被绑定在这个生命的`clean`阶段

下面的例子中在`clean`生命周期的`pre-clean`阶段绑定`autrun:run`：

```xml
<project>
    ...
    <build>
        <plugins>...</plugins>
        <artifactId>maven-autrun-plugin</artifactId>
        <executions>
            <execution>
                <id>file-exists</id>
                <phase>pre-clean</phase>
                <goals>
                    <goal>run</goal>
                </goals>
                <configuration>
                    <tasks>
                        <taskdef resource="net/sf/antcontrib/antcontrib.properties" />
                        <available file="/usr/local/hudson/hudson-home/jobs/maven-guide-zh-to-production" property="file.exists" value="true" />
                        <if>
                            <not>
                                <isset property="file.exists" />
                            </not>
                            <then>
                                <echo> No book.jar to delete</echo>
                            </then>
                            <else>
                                <echo> Deleting book.jar</echo>
                            </else>
                        <if>
                    </tasks>
                </configuration>
            </execution>
        </executions>
    </build>
</project>
```

**`default`**  

默认的生命周期包括如下阶段：

*   `validate`: 验证项目是否正确，以及为了完整构建必要的信息是否可用
*   `initialize`：初始化构建状态，比如设置属性创建目录
*   `generate-sources`: 生成所有需要包含在编译过程中的源代码
*   `process-sources`: 处理源代码，比如过滤一些值
*   `generate-resources`: 生成所有需要包含在打包过程中的资源文件
*   `process-resources`: 复制并处理资源文件至目标目录，准备打包
*   `compile`: 编译项目源代码
*   `process-clesses`: 处理编译生成的文件，例如对Java类进行字节码增强
*   `generate-test-sources`: 生成所有包含在测试编译过程中的测试源码
*   `process-test-sources`: 处理测试源码，比如过滤一些值
*   `generate-test-resources`: 生成测试需要的资源文件 
*   `process-test-resources`: 复制并处理测试资源文件至测试目标目录
*   `test-compile`: 编译测试源码至测试目标目录
*   `test`: 使用合适的单元测试框架运行测试；测试代码不应该被打包
*   `prepare-package`: 在打包之前执行一些必要操作
*   `package`: 将编译好的代码打包成可分发的格式，比如`war` `ear` `jar`
*   `pre-integration-test`: 执行一些在集成测试运行之前必要的动作
*   `integration-test`: 如果有必要的话，将包发布至集成测试环境
*   `post-integration-test`: 执行一些在集成测试之后需要的操作，比如清理集成测试环境
*   `verify`: 执行所有检查，验证包是有效的
*   `install`: 安装包至本地仓库
*   `deploy`: 安装包至远程仓库

**`site`**  

用来为项目生成项目文档和报告，有四个阶段：`pre-site` `site` `post-site` `site-deploy`

默认绑定到站点的生命周期目标是：`site:site`绑定到`site`、`site:deploy`绑定到`site-deplopy`

##### 打包相关的生命周期阶段绑定

maven会根据打包类型的不同（`<packaging>`元素指定的值）默认将一些不同的目标绑定到这种打包类型所默认执行的生命周期阶段中

**`jar类型`**  

`jar`是默认的打包类型

默认生命周期阶段 | 默认绑定的目标
---|---
`process-resources` | `resources:resources`
`compile` | `compiler:compile`
`process-test-resources` | `resources:testResources`
`test-compile`| `compiler:testCompile`
`test` | `surefire:test`
`package` | `jar:jar`
`install` | `install:install`
`deploy` | `deploy:deploy`

**`pom类型`**  

`pom`是最简单的打包类型，生成的构建只包含一个`pom`文件，没有要编译和打包的源文件和资源文件

默认生命周期阶段 | 默认绑定的目标
---|---
`package` | `site:attach-descriptor`
`install` | `install:install`
`deploy` | `deploy:deploy`

**`war类型`**  

`war`类型和`jar` `ejb`类似，区别是`package`生命周期阶段绑定的是`war:war`目标；另外这个插件需要一个`web.xml`配置文件在项目的`sr/main/webapp/WEB-INF/`目录下

默认生命周期阶段 | 默认绑定的目标
---|---
`process-resources` | `resources:resources`
`compile` | `compiler:compile`
`process-test-resources` | `resources:testResources`
`test-compile`| `compiler:testCompile`
`test` | `surefire:test`
`package` | `war:war`
`install` | `install:install`
`deploy` | `deploy:deploy`

其他的还有`ear` `ejb` `maven plugin`打包类型查阅sonatype的maven权威指南

##### 通用生命周期阶段的目标

**`Process Resources`**  

一般会将`resources:resources`目标绑定至`process-resouces`生命周期阶段。这个阶段的工作是将`src/main/resources`目录中的文件复制到`target/classes`目录中；并且可能应用一些过滤，让你可以替换资源文件中的一些符号；和`profile`结合起来就可以生成针对不同的环境的构建

默认情况下资源文件放置在`src/main/resouces`目录中，这是在超级`pom`中定义的，我们可以在自定义`pom.xml`文件中覆盖这个设置，并且添加更多的资源目录：

```xml
<build>
...
    <resources>
        <resource>
            <directory>src/main/resources</directory>
        </resource>
        <resource>
            <directory>src/main/xml</directory>
        </resource>
        <resource>
            <directory>src/main/images</directory>
        </resource>
    </resources>
...
</build>
```

当然也可以在`<resource>`元素中使用`<filter>`和`<excludes>` `<includes>`来排除和包含某些特定的文件

**`Compile`**  

一般会将`compiler:compile`目标绑定至`compile`阶段；这个目标的默认配置是编译所哟源码并且复制到指定的构建输出目录，默认是`target/classes`；指定源文件的位置和编译后输出的位置都在超级`pom`中指定，我们可以覆盖这些值来自定义位置；`compiler`插件的配置可以参考[官方文档](http://maven.apache.org/plugins/maven-compiler-plugin/)

**`Process Test Resources`**  
**`Test compile`**  
这两个阶段的操作和上面两个基本一致

**`Test`**  

默认会将`surefire:test`目标绑定至`test`阶段；`SureFire`插件是maven的单元测试插件，默认行为是寻找测试源码目录下所有`*Test`命名的类，并且使用`junit`运行他们，插件的详细信息参考官方文档

如果想要跳过测试可以在执行`mvn`命令时使用如下参数：`-Dmaven.test.skip=true`

**`install`**  

默认会将`install:install`目标绑定在`install`生命周期阶段；`Install`插件是将项目的主要构件安装在本地仓库

**`deploy`**  

默认将`deploy:deploy`目标绑定至`deploy`生命周期阶段，`Deploy`插件是将项目的主要构件发布到远程maven仓库，当你执行一次发布的时候通常需要更新远程仓库；远程仓库的配置一般在maven的`settings.xml`文件中

#### Profiles

maven中有三种类型的`profile`：每项目的(`pom.xml`)、每用户的(~/.m2/settings.xml)、全局的(`${maven.home}/conf/settings.xml`)

`profile`的触发方式：

*   `命令行显式触发`：`mvn groupId:artifactId:goal -P profile-id-1,profile-id-2`，使用这种方式时，在`settings.xml`中配置的触发依然有效
*   `配置文件配置`：在`<settings>`元素中使用`<activeProfiles>`元素配置
*   `根据profile中配置的触发条件`：在`<profile>`中配置的`<activation>`元素的子元素可以指定在某些条件下触发（比如：jdk版本、操作系统类型、环境变量设置等）详细信息参考[官方文档](http://maven.apache.org/guides/introduction/introduction-to-profiles.html)
*   可以在命令行使用`!`操作符来禁用一个`profile`配置：`mvn groupId:artifactId:goal -P !profile-id-1,!profile-id-2`

##### 可以在profile中使用的配置项

**`在外部配置文件中`**  

在外部文件中（`settings.xml`）定义的profile可以影响全局的设定，所以通常只能使用三个配置元素：`<repositories>` `<pluginRepositories>` `<properties>`

Profile中定义的`<properties>`元素可以指定一系列的`key-value`对（变量）；在后面的配置中可以使用`${profile.provided.path}`的形式来引用自定义的变量（不要使用这个功能）

**`在项目的pom.xml`**  

这个配置文件只影响当前项目及子模块所以可以有更多的配置元素：

```xml
<repositories>
<pluginRepositories>
<dependencies>
<plugins>
<properties> (not actually available in the main POM, but used behind the scenes)
<modules>
<reporting>
<dependencyManagement>
<distributionManagement>
<build>

//build中的子元素
<defaultGoal>
<resources>
<testResources>
<finalName>
```
> 在实践中不建议（甚至不允许）更改`<profile>`元素之外的设定，因为这些更改在打包并部署时不会被发布

##### 使用profile要注意的事项

**`不要引用profile中定义的变量`**：外部的`profile`中定义的变量可能在你未激活这个`profile`时不可用，外部配置文件中在全局中定义的变量，在你将项目放在其他的环境中编译时也可能不可用

##### 查看当前构建中启用的profile

可以使用maven的`help`插件来展示当前构建中激活的`profile`：

    mvn help:active-profiles

还可以提供一个查询参数`-D`，这样会在profile中匹配`<property>`定义的元素

    mvn help:active-profiles -Denv=dev
    
> 在`settings.xml`中定义的`profile`优先于在`pom.xml`中的


#### 标准项目目录结构

目录名 | 描述
---|---
`src/main/java` | 应用/库源代码
`src/main/resources` | 应用/库资源文件
`src/main/resources-filtered` | 被过滤的应用/库资源文件
`src/main/filters` | 资源过滤文件
`src/main/webapp` | `Web`应用源码
`src/test/java` | 测试源码
`src/test/resources` | 测试资源文件
`src/test/resources-filtered` | 被过滤的测试资源文件
`src/test/filters` | 测试资源过滤文件
`src/it` | 集成测试
`src/assembly` | 装配描述符
`src/site` | 站点
`LICENSE.txt` | 项目的`license`
`NOTICE.txt` | 注意事项、属性及项目的依赖
`README.txt` | 项目的`README`

除了上面这些目录文件之外，在项目根目录还应该有一个`pom.xml`文件用来描述这个`maven`项目

另外项目的编译输出目录一般指定在项目根目录下面的`target/`目录中

项目根目录中还可能有一些版本库管理的元素据比如：`.git` `.gitignore` `.svn`之类的目录和文件

#### Repositories的配置

默认情况下不需要做任何的配置，`settings.xml`中的`<localRepository>`元素指定了本地仓库的位置；超级`pom`中的`<repositories>`元素也指定远程中央仓库的位置（当请求的包在本地仓库中没有时，maven会自动到远程仓库下载）；你可以通过在`settings.xml`中配置`<mirror>`元素来设定一个中央仓库的镜像，这样maven只会连接这个镜像而不会连接中央仓库（当然你可以在项目中配置而覆盖这个全局的配置）：

```xml
<settings>
  ...
  <mirrors>
    <mirror>
        <id>UK</id>
        <name>UK Central</name>
        <url>http://uk.maven.org/maven2</url>
        <mirrorOf>central</mirrorOf>
    </mirror>
  </mirrors>
      ...
</settings>
```

对于特定的仓库比如`central`只能配置一个`<mirror>`；但是一个`<mirror>`可以被多个仓库使用：

```xml
<settings>
  ...
    <mirrors>
        <mirror>
            <id>internal-repository</id>
            <name>Maven Repository Manager running on repo.mycompany.com</name>
            <url>http://repo.mycompany.com/proxy</url>
            <mirrorOf>*</mirrorOf>
        </mirror>
    </mirrors>
      ...
</settings>
```

##### 使用nexus私服

配置单个项目使用nexus私服（也包括其他的远程仓库服务器）需要在项目的`pom`文件中添加如下配置：

```xml
<repositories>  
    <repository>  
        <id>nexus</id>  
        <name>nexus</name>  
        <url>http://192.168.1.103:8081/nexus/content/groups/public/</url>  
        <releases>  
            <enabled>true</enabled>  
        </releases>  
        <snapshots>  
            <enabled>true</enabled>  
        </snapshots>  
    </repository>  
</repositories>  

//指定插件仓库

<pluginRepositories>
    <pluginRepository>  
        <id>nexus</id>
        <name>nexus</name>
        <url>http://192.168.1.103:8081/nexus/content/groups/public/</url>  
        <releases>  
        <enabled>true</enabled>  
        </releases>  
        <snapshots>  
            <enabled>true</enabled>  
        </snapshots>  
    </pluginRepository>  
</pluginRepositories> 
```
> 如果你指定的远程服务器需要验证，可以使用这里指定的`<id>`然后在`settings.xml`中进行验证相关的配置

如果要所有使用这个maven编译的项目都使用这个私服则需要在maven的`settings.xml`中配置：

```xml
<profiles>
    <profile>
        <id>central-repository</id>
        <repositories>
            <repository>
                <id>central</id>
                <name>central</name>
                <url>http://192.168.1.103:8081/nexus/content/groups/public</url>
                <layout>default</layout>
                <releases>  
                    <enabled>true</enabled>  
                </releases>  
                <snapshots>  
                    <enabled>true</enabled>  
                </snapshots>
            </repository>
        </repositories>
    </profile>
</profiles>

<activeProfiles>
    <activeProfile>central-repository</activeProfile>
</activeProfiles>
```

如果要将编译好的项目发布到私服仓库则需要在`pom.xml`做如下配置：

```xml
<distributionManagement>  
    <repository>  
        <id>user-release</id>  
        <name>User Project Release</name>  
        <url>http://192.168.1.103:8081/nexus/content/repositories/releases/</url>  
    </repository>  
  
    <snapshotRepository>  
        <id>user-snapshots</id>  
        <name>User Project SNAPSHOTS</name>  
        <url>http://192.168.1.103:8081/nexus/content/repositories/snapshots/</url>  
    </snapshotRepository>  
</distributionManagement>
```

因为在nexus中有发布权限控制所以还要在`settings.xml`中指定连接到nexus服务器的账号或者密码（也可以使用证书的方式）：

```xml
<servers>
    <server>
        <id>user-release</id>
        <username>deployment</username>
        <password>deployment123</password>
    </server>
    
    <server>
        <id>user-snapshots</id>
        <username>deployment</username>
        <password>deployment123</password>
    </server>
<servers>
```
> 这里的id要和pom.xml中配置的或者上面连接发布服务器配置的id一致


#### 配置文件参考

**`settings.xml配置`**

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.1.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.1.0 http://maven.apache.org/xsd/settings-1.1.0.xsd">
    <localRepository/>
    <interactiveMode/>
    <usePluginRegistry/>
    <offline/>

    <proxies>
        <proxy>
            <active/>
            <protocol/>
            <username/>
            <password/>
            <port/>
            <host/>
            <nonProxyHosts/>
            <id/>
        </proxy>
    </proxies>

    <servers>
        <server>
            <username/>
            <password/>
            <privateKey/>
            <passphrase/>
            <filePermissions/>
            <directoryPermissions/>
            <configuration/>
            <id/>
        </server>
    </servers>

    <mirrors>
        <mirror>
            <mirrorOf/>
            <name/>
            <url/>
            <layout/>
            <mirrorOfLayouts/>
            <id/>
        </mirror>
    </mirrors>

    <profiles>
        <profile>
            <activation>
                <activeByDefault/>
                <jdk/>
                <os>
                    <name/>
                    <family/>
                    <arch/>
                    <version/>
                </os>
                <property>
                    <name/>
                    <value/>
                </property>
                <file>
                    <missing/>
                    <exists/>
                </file>
            </activation>
            <properties>
                <key>value</key>
            </properties>
    
            <repositories>
                <repository>
                    <releases>
                        <enabled/>
                        <updatePolicy/>
                        <checksumPolicy/>
                    </releases>
                    <snapshots>
                        <enabled/>
                        <updatePolicy/>
                        <checksumPolicy/>
                    </snapshots>
                    <id/>
                    <name/>
                    <url/>
                    <layout/>
                </repository>
            </repositories>
            <pluginRepositories>
                <pluginRepository>
                    <releases>
                        <enabled/>
                        <updatePolicy/>
                        <checksumPolicy/>
                    </releases>
                    <snapshots>
                        <enabled/>
                        <updatePolicy/>
                        <checksumPolicy/>
                    </snapshots>
                    <id/>
                    <name/>
                    <url/>
                    <layout/>
                </pluginRepository>
            </pluginRepositories>
            <id/>
        </profile>
    </profiles>

    <activeProfiles/>
    <pluginGroups/>
</settings>
```

**`pom.xml配置`**

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion/>

    <parent>
        <groupId/>
        <artifactId/>
        <version/>
        <relativePath/>
    </parent>

    <groupId/>
    <artifactId/>
    <version/>
    <packaging/>

    <name/>
    <description/>
    <url/>
    <inceptionYear/>
    <organization>
        <name/>
        <url/>
    </organization>
    <licenses>
        <license>
            <name/>
            <url/>
            <distribution/>
            <comments/>
        </license>
    </licenses>

    <developers>
        <developer>
            <id/>
            <name/>
            <email/>
            <url/>
            <organization/>
            <organizationUrl/>
            <roles/>
            <timezone/>
            <properties>
                <key>value</key>
            </properties>
        </developer>
    </developers>
    <contributors>
        <contributor>
            <name/>
            <email/>
            <url/>
            <organization/>
            <organizationUrl/>
            <roles/>
            <timezone/>
            <properties>
                <key>value</key>
            </properties>
        </contributor>
    </contributors>

    <mailingLists>
        <mailingList>
            <name/>
            <subscribe/>
            <unsubscribe/>
            <post/>
            <archive/>
            <otherArchives/>
        </mailingList>
    </mailingLists>

    <prerequisites>
        <maven/>
    </prerequisites>

    <modules/>

    <scm>
        <connection/>
        <developerConnection/>
        <tag/>
        <url/>
    </scm>
    <issueManagement>
        <system/>
        <url/>
    </issueManagement>
    <ciManagement>
        <system/>
        <url/>
        <notifiers>
            <notifier>
                <type/>
                <sendOnError/>
                <sendOnFailure/>
                <sendOnSuccess/>
                <sendOnWarning/>
                <address/>
                <configuration>
                    <key>value</key>
                </configuration>
            </notifier>
        </notifiers>
    </ciManagement>

    <distributionManagement>
        <repository>
            <uniqueVersion/>
            <releases>
                <enabled/>
                <updatePolicy/>
                <checksumPolicy/>
            </releases>
            <snapshots>
                <enabled/>
                <updatePolicy/>
                <checksumPolicy/>
            </snapshots>
            <id/>
            <name/>
            <url/>
            <layout/>
        </repository>
        <snapshotRepository>
            <uniqueVersion/>
            <releases>
                <enabled/>
                <updatePolicy/>
                <checksumPolicy/>
            </releases>
            <snapshots>
                <enabled/>
                <updatePolicy/>
                <checksumPolicy/>
            </snapshots>
            <id/>
            <name/>
            <url/>
            <layout/>
        </snapshotRepository>
        <site>
            <id/>
            <name/>
            <url/>
        </site>
        <downloadUrl/>
        <relocation>
            <groupId/>
            <artifactId/>
            <version/>
            <message/>
        </relocation>
        <status/>
    </distributionManagement>

    <properties>
        <key>value</key>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId/>
                <artifactId/>
                <version/>
                <type/>
                <classifier/>
                <scope/>
                <systemPath/>
                <exclusions>
                    <exclusion>
                        <artifactId/>
                        <groupId/>
                    </exclusion>
                </exclusions>
                <optional/>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId/>
            <artifactId/>
            <version/>
            <type/>
            <classifier/>
            <scope/>
            <systemPath/>
            <exclusions>
                <exclusion>
                    <artifactId/>
                    <groupId/>
                </exclusion>
            </exclusions>
            <optional/>
        </dependency>
    </dependencies>

    <repositories>
        <repository>
            <releases>
                <enabled/>
                <updatePolicy/>
                <checksumPolicy/>
            </releases>
            <snapshots>
                <enabled/>
                <updatePolicy/>
                <checksumPolicy/>
            </snapshots>
            <id/>
            <name/>
            <url/>
            <layout/>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <releases>
                <enabled/>
                <updatePolicy/>
                <checksumPolicy/>
            </releases>
            <snapshots>
                <enabled/>
                <updatePolicy/>
                <checksumPolicy/>
            </snapshots>
            <id/>
            <name/>
            <url/>
            <layout/>
        </pluginRepository>
    </pluginRepositories>

    <build>
        <sourceDirectory/>
        <scriptSourceDirectory/>
        <testSourceDirectory/>
        <outputDirectory/>
        <testOutputDirectory/>
        <extensions>
            <extension>
                <groupId/>
                <artifactId/>
                <version/>
            </extension>
        </extensions>
        <defaultGoal/>
        <resources>
            <resource>
                <targetPath/>
                <filtering/>
                <directory/>
                <includes/>
                <excludes/>
            </resource>
        </resources>
        <testResources>
            <testResource>
                <targetPath/>
                <filtering/>
                <directory/>
                <includes/>
                <excludes/>
            </testResource>
        </testResources>
        <directory/>
        <finalName/>
        <filters/>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId/>
                    <artifactId/>
                    <version/>
                    <extensions/>
                    <executions>
                        <execution>
                            <id/>
                            <phase/>
                            <goals/>
                            <inherited/>
                            <configuration/>
                        </execution>
                    </executions>
                    <dependencies>
                        <dependency>
                            <groupId/>
                            <artifactId/>
                            <version/>
                            <type/>
                            <classifier/>
                            <scope/>
                            <systemPath/>
                            <exclusions>
                                <exclusion>
                                    <artifactId/>
                                    <groupId/>
                                </exclusion>
                            </exclusions>
                            <optional/>
                        </dependency>
                    </dependencies>
                    <goals/>
                    <inherited/>
                    <configuration/>
                </plugin>
            </plugins>
        </pluginManagement>
        <plugins>
            <plugin>
                <groupId/>
                <artifactId/>
                <version/>
                <extensions/>
                <executions>
                    <execution>
                        <id/>
                        <phase/>
                        <goals/>
                        <inherited/>
                        <configuration/>
                    </execution>
                </executions>
                <dependencies>
                    <dependency>
                        <groupId/>
                        <artifactId/>
                        <version/>
                        <type/>
                        <classifier/>
                        <scope/>
                        <systemPath/>
                        <exclusions>
                            <exclusion>
                                <artifactId/>
                                <groupId/>
                            </exclusion>
                        </exclusions>
                        <optional/>
                    </dependency>
                </dependencies>
                <goals/>
                <inherited/>
                <configuration/>
            </plugin>
        </plugins>
    </build>

    <reports/>
    <reporting>
        <excludeDefaults/>
        <outputDirectory/>
        <plugins>
            <plugin>
                <groupId/>
                <artifactId/>
                <version/>
                <reportSets>
                    <reportSet>
                        <id/>
                        <reports/>
                        <inherited/>
                        <configuration/>
                    </reportSet>
                </reportSets>
                <inherited/>
                <configuration/>
            </plugin>
        </plugins>
    </reporting>

    <profiles>
        <profile>
            <id/>
            <activation>
                <activeByDefault/>
                <jdk/>
                <os>
                    <name/>
                    <family/>
                    <arch/>
                    <version/>
                </os>
                <property>
                    <name/>
                    <value/>
                </property>
                <file>
                    <missing/>
                    <exists/>
                </file>
            </activation>
            <build>
                <defaultGoal/>
                <resources>
                    <resource>
                        <targetPath/>
                        <filtering/>
                        <directory/>
                        <includes/>
                        <excludes/>
                    </resource>
                </resources>
                <testResources>
                    <testResource>
                        <targetPath/>
                        <filtering/>
                        <directory/>
                        <includes/>
                        <excludes/>
                    </testResource>
                </testResources>
                <directory/>
                <finalName/>
                <filters/>
                <pluginManagement>
                    <plugins>
                        <plugin>
                            <groupId/>
                            <artifactId/>
                            <version/>
                            <extensions/>
                            <executions>
                                <execution>
                                    <id/>
                                    <phase/>
                                    <goals/>
                                    <inherited/>
                                    <configuration/>
                                </execution>
                            </executions>
                            <dependencies>
                                <dependency>
                                    <groupId/>
                                    <artifactId/>
                                    <version/>
                                    <type/>
                                    <classifier/>
                                    <scope/>
                                    <systemPath/>
                                    <exclusions>
                                        <exclusion>
                                            <artifactId/>
                                            <groupId/>
                                        </exclusion>
                                    </exclusions>
                                    <optional/>
                                </dependency>
                            </dependencies>
                            <goals/>
                            <inherited/>
                            <configuration/>
                        </plugin>
                    </plugins>
                </pluginManagement>
                <plugins>
                    <plugin>
                        <groupId/>
                        <artifactId/>
                        <version/>
                        <extensions/>
                        <executions>
                            <execution>
                                <id/>
                                <phase/>
                                <goals/>
                                <inherited/>
                                <configuration/>
                            </execution>
                        </executions>
                        <dependencies>
                            <dependency>
                                <groupId/>
                                <artifactId/>
                                <version/>
                                <type/>
                                <classifier/>
                                <scope/>
                                <systemPath/>
                                <exclusions>
                                    <exclusion>
                                        <artifactId/>
                                        <groupId/>
                                    </exclusion>
                                </exclusions>
                                <optional/>
                            </dependency>
                        </dependencies>
                        <goals/>
                        <inherited/>
                        <configuration/>
                    </plugin>
                </plugins>
            </build>

            <modules/>

            <distributionManagement>
                <repository>
                    <uniqueVersion/>
                    <releases>
                        <enabled/>
                        <updatePolicy/>
                        <checksumPolicy/>
                    </releases>
                    <snapshots>
                        <enabled/>
                        <updatePolicy/>
                        <checksumPolicy/>
                    </snapshots>
                    <id/>
                    <name/>
                    <url/>
                    <layout/>
                </repository>
                <snapshotRepository>
                    <uniqueVersion/>
                    <releases>
                        <enabled/>
                        <updatePolicy/>
                        <checksumPolicy/>
                    </releases>
                    <snapshots>
                        <enabled/>
                        <updatePolicy/>
                        <checksumPolicy/>
                    </snapshots>
                    <id/>
                    <name/>
                    <url/>
                    <layout/>
                </snapshotRepository>
                <site>
                    <id/>
                    <name/>
                    <url/>
                </site>
                <downloadUrl/>
                <relocation>
                    <groupId/>
                    <artifactId/>
                    <version/>
                    <message/>
                </relocation>
                <status/>
            </distributionManagement>

            <properties>
                <key>value</key>
            </properties>

            <dependencyManagement>
                <dependencies>
                    <dependency>
                        <groupId/>
                        <artifactId/>
                        <version/>
                        <type/>
                        <classifier/>
                        <scope/>
                        <systemPath/>
                        <exclusions>
                            <exclusion>
                                <artifactId/>
                                <groupId/>
                            </exclusion>
                        </exclusions>
                        <optional/>
                    </dependency>
                </dependencies>
            </dependencyManagement>
            <dependencies>
                <dependency>
                    <groupId/>
                    <artifactId/>
                    <version/>
                    <type/>
                    <classifier/>
                    <scope/>
                    <systemPath/>
                    <exclusions>
                        <exclusion>
                            <artifactId/>
                            <groupId/>
                        </exclusion>
                    </exclusions>
                    <optional/>
                </dependency>
            </dependencies>

            <repositories>
                <repository>
                    <releases>
                        <enabled/>
                        <updatePolicy/>
                        <checksumPolicy/>
                    </releases>
                    <snapshots>
                        <enabled/>
                        <updatePolicy/>
                        <checksumPolicy/>
                    </snapshots>
                    <id/>
                    <name/>
                    <url/>
                    <layout/>
                </repository>
            </repositories>
            <pluginRepositories>
                <pluginRepository>
                    <releases>
                        <enabled/>
                        <updatePolicy/>
                        <checksumPolicy/>
                    </releases>
                    <snapshots>
                        <enabled/>
                        <updatePolicy/>
                        <checksumPolicy/>
                    </snapshots>
                    <id/>
                    <name/>
                    <url/>
                    <layout/>
                </pluginRepository>
            </pluginRepositories>

            <reports/>
            <reporting>
                <excludeDefaults/>
                <outputDirectory/>
                <plugins>
                    <plugin>
                        <groupId/>
                        <artifactId/>
                        <version/>
                        <reportSets>
                            <reportSet>
                                <id/>
                                <reports/>
                                <inherited/>
                                <configuration/>
                            </reportSet>
                        </reportSets>
                        <inherited/>
                        <configuration/>
                    </plugin>
                </plugins>
            </reporting>
        </profile>
    </profiles>
</project>
```
