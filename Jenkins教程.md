# Jenkins教程

## 1. 简介

Jenkins是一个开源的、提供友好操作界面的持续集成(CI)工具，起源于Hudson（Hudson是商用的），主要用于持续自动构建、测试、部署我们的项目。Jenkins用Java语言编写，可在Tomcat等流行的servlet容器中运行，也可独立运行。通常与版本管理工具(SCM)、构建工具结合使用。常用的版本控制工具有SVN、GIT，构建工具有Maven、Ant、Gradle。

**什么是CI：**

CI(Continuous integration，中文意思是持续集成)是一种软件开发时间。持续集成强调开发人员提交了新代码之后，立刻进行构建、（单元）测试。根据测试结果，我们可以确定新代码和原有代码能否正确地集成在一起。

![image-20200319132226471](https://tva1.sinaimg.cn/large/00831rSTgy1gcz63npy9nj315o0ick0u.jpg)

**什么是CD：**

CD(Continuous Delivery， 中文意思持续交付)是在持续集成的基础上，将集成后的代码部署到更贴近真实运行环境(类生产环境)中。比如，我们完成单元测试后，可以把代码部署到连接数据库的Staging环境中更多的测试。如果代码没有问题，可以继续手动部署到生产环境。

![image-20200319132338585](https://tva1.sinaimg.cn/large/00831rSTgy1gcz64vczetj30yi0pj4b4.jpg)

> 参考：https://www.jianshu.com/p/5f671aca2b5a

## 2. 下载

官方网址：https://jenkins.io/zh/download/

jenkins分为长期支持版本（LTS）和每周更新版，并且有对应不同平台的安装包下载，还提供了一个可直接运行的war包，内部集成了jetty容器。这里直接下载LTS版本的war包运行即可。

![image-20200319135123472](https://tva1.sinaimg.cn/large/00831rSTgy1gcz6xqp5mwj313j0q0q79.jpg)

## 3. 启动

使用java命令运行war文件，并指定http端口：

~~~shell
java -jar jenkins.war --httpPort=8088
~~~

也可以使用后台启动：

~~~shell
nohup java -jar jenkins.war --httpPort=8080 > /root/jenkins.log 2>&1 &
~~~

nuhup可以在关闭控制台后继续运行该进程，“>”号表示输出重定向，这里将输出重定向到/root/jenkins.log中，2>&1表示将标准错误信息也输出到jenkins.log文件中，最后一个&表示该命令在后台执行。

启动完成后，Jenkins默认会创建一个admin用户并生成一个初始密码：

![image-20200319135701307](https://tva1.sinaimg.cn/large/00831rSTgy1gcz73lmw02j31e70u0drg.jpg)

密码文件也会保存在`/用户/.jenkins/secrets/initialAdminPassword`的文件中。

## 4. 初始化

在浏览器输入http://localhost:8088访问Jenkins。第一次访问时，会在这个页面停留片刻，Jenkins需要完成一些准备工作，当准备好之后会跳转到解锁页面。

![image-20200319140207507](https://tva1.sinaimg.cn/large/00831rSTgy1gcz78x0pumj31nl0u0gvs.jpg)

如果等待时间很长，可以尝试替换镜像地址，打开`/用户目录/.jenkins/hudson.model.UpdateCenter.xml`文件

~~~xml
<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <url>http://updates.jenkins-ci.org/update-center.json</url>
  </site>
</sites>
~~~

将上面的url替换为：

~~~xml
<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <url>https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json</url>
  </site>
</sites>
~~~

准备好后进入如下页面，需要输入admin默认初始化的密码来解锁，填写后点击继续。

![image-20200319140607882](https://tva1.sinaimg.cn/large/00831rSTgy1gcz7d2rvjbj30yd0op78o.jpg)

接下来的这个页面中选择需要初始化的插件，这里选择安装推荐的插件即可。

![image-20200319143223245](https://tva1.sinaimg.cn/large/00831rSTgy1gcz84e2dr5j30yf0mlgr1.jpg)

接着会自动完成插件的安装。

![image-20200319144155016](https://tva1.sinaimg.cn/large/00831rSTgy1gcz8ebb5p5j30yh0mnaf6.jpg)

如果插件更新很慢或者失败，可以通过修改镜像地址来提速。

先停止jenkins，然后编辑 /用户/.jenkins/updates/default.json文件，将文件中所有的`updates.jenkins-ci.org/download`的地址替换为`mirrors.tuna.tsinghua.edu.cn/jenkins`。

![image-20200323163824201](https://tva1.sinaimg.cn/large/00831rSTgy1gd3y8qx4d0j31cb0s514w.jpg)

vim编辑器可以使用:s来替换：

~~~shell
:%s#updates.jenkins-ci.org/download#mirrors.tuna.tsinghua.edu.cn/jenkins#g
~~~

插件初始化完成后，下一步需要创建一个管理员用户，在如下页面填写相关信息，点击保存并完成即可。

![image-20200319144410628](https://tva1.sinaimg.cn/large/00831rSTgy1gcz8go2o6tj30yg0oqgoz.jpg)

最后在实例配置中指定Jenkins的URL访问地址完成初始化。

![image-20200319145257417](https://tva1.sinaimg.cn/large/00831rSTgy1gcz8psqjbsj30y60okaf1.jpg)

## 5. 全局配置

成功初始化后接下来需要做一些相应的全局的工具配置，点击首页左边菜单的Manage Jenkins，然后再点击Global Tool Configuration选项。

![image-20200320102922748](https://tva1.sinaimg.cn/large/00831rSTgy1gd06pw89oxj31ap0pkwl2.jpg)

在以下的页面中设置好Maven、JDK、Git的相关配置信息。需要注意的是，在JDK配置中取消勾选Install automatically，也就是不自动下载jdk，而是手动指定JAVA_HOME的路径。

![image-20200320103740343](https://tva1.sinaimg.cn/large/00831rSTgy1gd06yhrf74j31500mvtbe.jpg)

在下面Maven安装的选项中指定Maven的安装路径

![image-20200321174659226](https://tva1.sinaimg.cn/large/00831rSTgy1gd1oziwmyxj313j061dg8.jpg)

## 6. 创建任务

点击首页的新建Item或者创建一个新任务。

![image-20200323154933362](https://tva1.sinaimg.cn/large/00831rSTgy1gd3wtx6bm1j30z40e4taq.jpg)

在跳转的页面中输入任务名称（这里创建一个helloworld任务用于演示）。然后选择自由风格的的任务，最后点解确定按钮。

![image-20200323155128154](https://tva1.sinaimg.cn/large/00831rSTgy1gd3wvx8dwgj30wt0kkdkc.jpg)

## 7. 配置任务

在创建好任务之后，会来到任务的配置页面，下面将演示如何对任务进行一些常规的配置。

### 7.1 General配置

![image-20200323142751771](https://tva1.sinaimg.cn/large/00831rSTgy1gd3ugyfqc0j30x40jptao.jpg)

- 描述：填写构建的描述信息。
- Discard old builds：勾选，表示丢弃就的构建，并填写丢弃策略的保持构建天数和保持构建的最大个数。
- GitHub项目：勾选，填写github的项目地址。

### 7.2 源码管理

![image-20200323143415394](https://tva1.sinaimg.cn/large/00831rSTgy1gd3unkia1ej30x70k0q55.jpg)

源码管理可选择git或svn。由于案例是托管在github上，因此这里选择Git，需要注意的是，如果使用ssh加密通信，需要在本地使用ssh-keygen生成秘钥对，然后将私钥配置到jenkins，把公钥配置到github上。

点击添加按钮

![image-20200323152011995](https://tva1.sinaimg.cn/large/00831rSTgy1gd3vzdpi33j30o602xwf5.jpg)

在弹出的子窗口中，类型选择SSH Username with private key，然后填写Username（随意），勾选Enter directly，在Key的文本域中加入私有key的秘钥串，然后点击最下面的添加。

![image-20200323152413085](https://tva1.sinaimg.cn/large/00831rSTgy1gd3w3jzf1uj313z0jo76d.jpg)

### 7.3 构建环境

构建环境中勾选Delete workspace before build starts（开始构建之前删除工作空间）

![image-20200323152516441](https://tva1.sinaimg.cn/large/00831rSTgy1gd3w4nudccj30wx0ahq46.jpg)

### 7.4 构建

在构建这一项中选择构建步骤，由于案例是一个Maven项目，因此这里选择使用Maven来进行构建。点击添加构建步骤，在下拉菜单中选择Invoke top-level Maven targets。

![image-20200323152745434](https://tva1.sinaimg.cn/large/00831rSTgy1gd3w78j2irj30wo08taat.jpg)

然后选择maven版本，目标出填写Maven声明周期命令，这里设置为执行清理后进行打包，并忽略单元测试。

![image-20200323153125260](https://tva1.sinaimg.cn/large/00831rSTgy1gd3wb24rp0j30wf086aal.jpg)

### 7.5 自动部署

前面的配置已经基本完成了项目的自动构建，通常在构建完之后需要将项目进行部署，那么同样可以让Jenkins来完成项目的自动部署。在自动部署的时候，应先停止原有的应用进程然后重新启动，这些操作可以使用shell脚本来完成。

在构建的选项中再次点击增加构建步骤，选择Execute shell

![image-20200323153255506](https://tva1.sinaimg.cn/large/00831rSTgy1gd3wcmd3aij30wf0f20u6.jpg)

在命令中添加sheel脚本

~~~shell
# #!/bin/bash表示使用系统的bash
#!/bin/bash
echo "Restart SpringBootApplication..."
# 获取demo.jar的后台进程id，并赋值给变量pid
# ps用于显示进程信息的命令，-ef参数表示显示所有进程和全部格式
# “|”是一个管道符号，表示ps和grep命令同时执行
# grep是linux的一个文本搜索工具
# grep demo.jar表示查找内容包含demo.jar的进程信息
# grep -v grep表示反向查找，查找不包含grep的内容的进程信息（也就是进行一次过滤）
# awk是一个强大的文本分析工具，分析并生成报告。'{print $2}'表示打印出第二个字段
pid=`ps -ef | grep wuneng-web-1.0-SNAPSHOT.jar | grep -v grep | awk '{print $2}'`
# 如果pid不为“”，则kill掉该进程
if [ -n "$pid" ]
then
	kill -9 $pid
fi
# Jenkins默认会在Build结束后Kill掉所有的衍生进程, 
# BUILD_ID=DONTKILLME就是防止jenkins杀掉衍生的子进程
BUILD_ID=DONTKILLME
# 后台启动demo.jar,2>&1表示将标准出错也输出到springboot.log文件中，最后一个&表示该命令在后台执行
nohup java -jar /root/.jenkins/workspace/helloworld/target/wuneng-web-1.0-SNAPSHOT.jar.jar > /root/springboot.log 2>&1 &
~~~

最后点击应用并保存，此时任务的配置就基本完成。

## 8. 执行任务

回到首页，在任务列表中可以看到刚配置好的任务，点击任务名称旁边的下拉箭头，选择Build Now开始执行任务。

![image-20200323155853732](https://tva1.sinaimg.cn/large/00831rSTgy1gd3x3mwrp7j314o0dodhm.jpg)

此时在左下角的构建执行状态中会有一个helloworld任务执行的进度条。

![image-20200323160004761](https://tva1.sinaimg.cn/large/00831rSTgy1gd3x4v83frj30lo04fgln.jpg)

旁边的#1表示第几次构建，如果执行完后再次点击Build Now，那么又会产生一条新的构建记录为#2。可以点击#1进去查控制台输出任务执行日志。

![image-20200323160322134](https://tva1.sinaimg.cn/large/00831rSTgy1gd3x8akip1j31hb0qadp4.jpg)

最后，当任务正确执行完后，我们就可以正常访问项目。以后每当我们有新的版本提交到github，那么只需要在任务列表中点击Build Now来执行构建任务。那么jenkins会自动从github上拉取最新的版本，接着使用Maven将项目进行编译打包等操作，然后通过shell脚本自动部署项目。