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
* mvn- compile编译主代码
* mvn- test 测试
* mvn- package 打包主代码到target中
* mvn- install 安装jar到本地仓库
* mvn- clean 清理---删除target

如果项目中使用了别的项目的包，则需要在pom.xml中建立依赖，同时编译器还会在本地仓库中查找，如果找到了则会将目标jar包加入到项目的classPath中，如果没有找到，就会去网上的maven中央仓库查找并下载到本地仓库供其使用，若中央仓库也没有则会报错。(tips:按住shift然后右键你需要的路径可以直接进入相应的命令窗口<br>
坐标(groupId、artifactId、version这种类型):用来唯一标识构件
* groupId 定义当前Maven项目属于哪个组||公司  一般反写公司名+项目名
* artifactId 定义项目中模块的唯一标识 一般项目名-模块名
* version 版本



