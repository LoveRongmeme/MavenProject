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
* groupId 定义当前Maven项目属于哪个组||公司  一般反写公司名+项目名
* artifactId 定义项目中模块的唯一标识 一般项目名-模块名
* version 版本<br>
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







