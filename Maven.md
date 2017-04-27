## 1.**了解 Maven**<br>
* Maven可以帮我们自动化构建过程，从清理、编译、测试到生成报告，再到打包和部署；抽象构建过程，提供构建任务实现； 跨平台，对外提供一致接口；<br>
* Maven同时还是依赖管理工具和项目管理工具<br>
* Maven是声明式的，项目构建过程和过程各个阶段所需的任务都由插件完成，而大部分插件都是现成的，开发者只需声明项目
的基本元素，Maven就执行内置的、完整的构建过程。<br>
## 2.**Maven的安装与配置** 
这个网上很多教程，随便找个看看就是了;很有用的一点是将M2_HOME/conf/settings.xml复制到~/m2./settings.xml,控制范围为当前用户<br>
## 3.**Maven的数据结构**
src
>main
>>java
>>>package

>test
>>java
>>>package

>resource
## 4.**Maven的常用构建命令**
* mvn- v 查看Maven版本
* mvn- compile编译主代码到target/classes
* mvn- test 测试
* mvn- package 打包主代码到target中 命名规则:artifactId-version.jar
* mvn- install 安装jar到本地仓库
* mvn- clean 清理---删除target

如果项目中使用了别的项目的包，则需要在pom.xml中建立依赖，同时编译器还会在本地仓库中查找，如果找到了则会将目标jar包加入到项目的classPath中，如果没有找到，就会去网上的maven中央仓库查找并下载到本地仓库供其使用，若中央仓库也没有则会报错。(tips:按住shift右键你需要的路径可以直接进入命令窗口<br>
坐标(groupId、artifactId、version这种类型):用来唯一标识构件
* groupId 定义当前Maven项目属于哪个组||公司  也就是所属的实际项目  一般反写公司名+项目名 可以参考org.springframework
* artifactId 定义实际项目中的Maven项目(模块)的唯一标识 一般项目名-模块名
* version Maven项目当前所处的版本<br>
* packaging 定义Maven项目的打包方式
* classifier 帮助定义一些由插件生成的附属构件 不能直接定义项目的classifier，因为附属构件不是项目直接默认生成的，而是由附加的插件生成

这5个元素,groupId,artifactId,version是必需的，packaging是可选的(默认为.jar),classifier<br>
项目的构件名一般命名规则 artifactId-version[-classifier].packaging   [-classifier]表示可选<br>
中央镜像仓库:[Center]( https://repo.maven.apache.org/maven2 "repository")<br>
国内镜像仓库网址:[ALi]( http://maven.aliyun.com/nexus/content/groups/public/ "repositoryInAli")<br>
可以在settings.xml中修改<br>   
``` 
<mirror>
    <id>maven.net.cn</id>
    <mirrorOf>central</mirrorOf>
    <name>central mirror in china</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
</mirror>
```
本地仓库的默认位置也可以在settings.xml中修改 localRepository;在Eclipse中使用maven---一些基本配置上网查0.0<br>
## 5.**自动创建目录骨架**
使用archetype插件可以自动创建符合maven规定的目录框架,有两种方式:
* mvn archetype:generate 按照提示进行
* mvn archetype:generate –DgroupId=组织名 
-DartifactId=项目名-模块名
-Dversion=版本号
-Dpackage=包名<br>
这两种都是针对3.0及以上，若是2.0方法有不同，具体网上查<br>
## 6.**依赖**
1).**基本配置**
```
<project>
...
<dependencies>
    <dependency>
      <groupId>...</groupId>
      <artifactId>...</artifactId>
      <version>...</version>
      <type>...</type>
      <scope>...</scope>
      <optional>...</optional>
     <exclusions>
      <exclusion>
      ...
      </exclusion>
     ...
     </exclusions>
    </dependency>
   ...
</dependencies>
...
</project>
```
* **groupId、artifactId、version**：依赖的基本坐标
* **type**:依赖的类型，对应于项目的packaging，一般不用声明，默认是jar
* **scope**:依赖的范围
* **optional**:标记依赖是否可选
* **exclusions**:用来排除传递性依赖<br>

2).**依赖范围**<br>
关于依赖范围，首先得明确Maven在编译项目主代码、编译和执行测试、实际运行的时候用的是不同的classpath，依赖范围就是用来控制依赖与这三种classpath（编译classpath、测试classpath、运行classpath）的关系，Maven有以下几种依赖范围:
* **compile**:编译依赖范围，**默认的**，对于三种classpath都有效，典型代表spring-core，在编译、测试、运行都需要使用该依赖
* **test**:测试依赖范围，只对测试classpath起作用，典型代表JUnit,在编译测试代码及运行测试的时候需要
* **provided**:已提供依赖范围，对编译classpath和测试classpath有效，典型代表是servlet-api，编译和测试时需要，在运行时，由于容器已经提供，故不需要
* **runtime**:运行时依赖，对于测试classpath和运行classpath有效，典型代表是JDBC驱动实现，项目主代码的编译只需要JDK提供的JDBC接口，只有在执行测试和运行项目时才需要实现上述JDBC接口的具体JDBC驱动
* **system**:系统依赖范围，对编译classpath和测试classpath有效，使用时必须通过systemPath元素显示指定依赖文件路径，此类依赖不通过Maven仓库解析，且往往与本机系统绑定，可能造成构建的不可移植，慎用！systemPath元素可以引用系统环境变量，如:
```
<dependency>
      <groupId>javax.sql</groupId>
      <artifactId>jdbc-stdext</artifactId>
      <version>2.0</version>
      <scope>system</scope>
      <systemPath>${JAVA_HOME}/lib/rt.jar</systemPath>
</dependency>
```
* **import**:导入依赖范围，该依赖范围不会对三种classpath产生实际影响<br>

3).**传递性依赖**<br>
Maven在使用依赖时会解析依赖的POM，会将必要的间接依赖通过传递性依赖引入到当前项目中来；依赖范围会对传递性依赖产生影响;<br>
比如，假设A依赖B,B依赖C，那么我们说A对于B是第一直接依赖，B对于C是第二直接依赖，A对于C是传递性依赖；<br>
规律:当第二直接依赖范围是compile时，传递性依赖范围与第一直接依赖范围相同；当第二直接依赖范围是test时，依赖不会传递；当第二直接依赖范围是provided时，只传递第一直接依赖范围是provided的依赖，传递性依赖范围为provided；当第二直接依赖范围是runtime时，传递性依赖范围与第一直接依赖范围一致，但provided除外，此时传递性依赖范围为runtime;<br>
4).**依赖调解**<br>
当对同一个依赖有不同版本时，两个原则:1.就近原则，依赖传递的次数少者优先；2.第一声明者优先原则，当传递次数相等时，谁先声明就解析使用谁<br>
5).**可选依赖(optional)**<br>
可选依赖不会被传递，可选依赖使用场景:项目实现了很多不同特性，比如两个，且这些特性相互排斥(只能存在一个)。例如项目B是一个持久层隔离工具包，它支持多种数据库，构建这个项目的时候需要多种数据库的驱动程序，但是使用的时候只能依赖一种数据库；但是，其实可选依赖是不应该被使用的，在JAVA面向对象设计原则中，有个职能单一性原则，一个类应该只实现一种功能，而不应该糅合太多的功能；<br>
6).**最佳实践**<br>
* 排除依赖:A依赖B，B依赖C，但是A想用C的另外一个版本，那么可以用exclusions来排除C依赖，同时自己再显示添加对应版本的C依赖，值得注意的是，在使用exclusion时，只需要groupId和artifactId,不需要version，因为在Maven解析后的依赖中，只可能出现一个版本的依赖(依赖调解)
* 归类依赖:对于同一个项目的不同模块，版本应该保持一致，比如在使用Spring Framework时，有很多spring依赖，但是版本一致，因此可以使用属性properties
```
<properties>
<springframework.version>2.5.6</springframework>
</properties>
```
在申明依赖时，可以用${springframework.version}来使用
* 优化依赖:在Maven解析了所有的直接依赖和传递依赖之后，确保一个构件只有唯一的版本存在，称为已解析依赖，有如下三种命令来优化(主要删除一些没用依赖):
    * mvn dependency:list查看所有的已解析依赖<br>
    * mvn dependency:tree查看依赖数，即依赖的层数(第几层依赖)<br>
    * mvn dependency:analyze 只会分析编译主代码和编译测试代码是所需的依赖，而不会分析执行测试和运行项目时所需的依赖<br>
以上三种依赖依情况谨慎使用
## 7.**仓库**
仓库，一般来说，一个用户只有一个本地仓库，但是可以配置访问多个远程仓库
>本地仓库

>远程仓库
>>中央仓库

>>私服:局域网内置的仓库，代理外部所有远程仓库

>>其它公共库









