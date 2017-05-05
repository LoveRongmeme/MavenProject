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

>远程仓库(配置远程仓库pom.xml->repositories->repository)
>>中央仓库(默认的远程仓库)

>>私服:局域网内置的仓库，代理外部所有远程仓库(通过缓存构件来实现)

>>其它公共库

**快照版本**：\*-SNAPSHOT:(供组织内部使用的)不稳定版本，为了当构件更新时保证其他构件对其依赖也是最新的而规定的<br>
**从仓库解析依赖的机制**:根据依赖groupId,artifactId,version...先在本地仓库匹配，有则解析，没有则去远程仓库匹配，有则解析，没有就报错<br>
**镜像**:如果仓库X可以提供仓库Y储存的所有内容，则可以认为X是Y的一个镜像，也就是说任何一个可以从仓库Y中得到的构件，都可以从它的镜像中获得<br>
settings.xml中配置
```
<settings>
...
<mirrors>
    <mirror>
    <id>maven.cn.net</id>
    <name>one of the central mirrors in China</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public/<url>
    <mirrorOf>central</mirrorOf>
    </morror>
<mirrors>
...
</settings>
```
其中mirrorOf表示的目标仓库的镜像，可以用这种方法配置其它远程仓库的镜像；关于镜像的更常用方法是结合私服，由于私服可以代理所有的远程仓库，所以私服是所有远程仓库的镜像，因此可以配置这样的一个镜像:
```
<settings>
...
<mirrors>
    <mirror>
    <id>internal-repository</id>
    <name>Internal Repository Manager</name>
    <url>http://192.168.1.100/maven2/<url>
    <mirrorOf>*</mirrorOf>
    </morror>
<mirrors>
...
</settings>
```
其中mirrorOf的值为\*，表示该配置是所有Maven仓库的镜像；Maven更高级的镜像配置(mirrorOf):<br>
* \* 匹配所有远程仓库
* external:\* 匹配所有远程仓库,使用localhost的除外，使用file:// 协议的也除外,也就是说，匹配所有不在本机上的远程仓库
* repo1,repo2 使用逗号分隔的多个远程仓库
* \*,! repo1 匹配所有除了repo1的远程仓库
值得注意的是，当镜像仓库出了问题时，也不能绕过它而访问被镜像仓库，因而无法下载构件<br>
## **仓库搜索**
几个常用的寻找Maven依赖的网站:
* Sonatype Nexus: http://repository.sonatype.org/
* Jarvana: http://www.jarnava.com/jarvana/
* mvnbrowser: http://www.mvnborwser.com
* MVNrepository: http://mvnrepository.com/
## 8.**生命周期**
maven生命周期是一个抽象概念，具体的每一部分都由相应的插件来完成，(与Java的模板方法很像);生命周期包括清理、初始化、编译、测试、打包、集成测试、验证、部署、站点生成等；<br>
maven有**三套**生命周期，分别是clean,default,site;clean-清理项目，default-构建项目，site-建立项目站点;每个生命周期都由一些阶段，后面的阶段依赖前面的阶段，按一定顺序执行；**每个生命周期相互独立**<br>
* clean-清理项目<br>
    * pre-clean 执行一些清理前需要完成的工作<br>
    * clean 清理上一次构建生成的文件<br>
    * post-clean 执行一些清理后需要完成的工作<br>
* default-构建项目,定义了构建时所需要的所有步骤，以下只对重点阶段分立及解释<br>
    * validate,initialize,generate-sources<br>
    * process-sources 处理项目主资源文件，通常是对src/main/resources目录内的内容进行变量替换，完成后复制到输出的classpath中<br>
    * generate-resources.process-resources<br>
    * compile 编译主代码，通常是把src/main/java中的内容编译到主classpath(编译classpath)中<br>
    * process-classes,generate-test-sources<br>
    * process-test-sources 处理项目测试资源文件，通常是对src/test/resources目录内的内容进行变量替换，完成后复制到输出的测试lasspath中<br>
    * generate-test-resources,process-test-resources<br>
    * test-compile 编译项目测试代码，通常是把src/test/java中的内容编译到测试classpath中<br>
    * process-test-classes<br>
    * test 使用单元测试框架运行测试，测试代码不会被打包和部署<br>
    * prepare-package<br>
    * package 接受编译好的代码，打包成可发布的格式，如jar<br>
    * pre-integration-test,integration-test,post-integration-test,verify,<br>
    * install 将包安装到本地仓库，供其他maven项目使用<br>
    * deploy 将最终的包部署到远程仓库，供其他开发人员和maven项目使用<br>
    
* site-建立和发布项目站点(基于POM的信息)<br>
    * pre-site执行一些在生成项目站点前必须完成的工作<br>
    * site 生成项目站点文档<br>
    * post-site 执行一些在生成项目站点之后需要完成的工作<br>
    * site-deploy 将生成的项目站点发布到服务器上<br>
**命令行与生命周期**：从命令行执行任务最主要的方式就是调用Maven的生命周期阶段，一些例子：
* mvn clean 调用clean生命周期的clean阶段，实际执行的是pre-clean和clean <br>
* mvn test 调用default生命周期的test阶段，实际执行的是validate,initialize等,直到test的所有阶段，这也就解释了为什么在执行测试的时候，项目的代码能够自动得以编译<br>
* maven clean install 调用clean生命周期的clean阶段和default生命周期的install阶段，实际执行的是clean的pre-clean，clean和default的从validate到install的所有阶段，再执行真正的项目构建之前先清理比较好<br>
* maven clean deploy site-depoly 调用clean的clean，default的deploay以及site的site-deploy阶段，实际执行的是clean的pre-clean，clean和default的的所有阶段以及site的所有阶段<br>
**插件目标**:Maven的核心指示定义了生命周期，具体实现交给插件，而为了复用代码，往往一个插件有多个功能，每一个功能都为一个插件目标，表示方式 dependency:analyze(这是maven-dependency-plugin插件其中的一个功能),冒号前面是插件前缀，后面是目标<br>
**插件绑定**:Maven的生命周期与插件绑定，已完成相应的构建任务，实质上是**生命周期的具体阶段与插件目标绑定**<br>








