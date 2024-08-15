# Maven教程

## 1. Maven基础

### 1.1 概述

​	Maven是Apache开源组织的顶级项目之一，是一个项目管理工具。它基于项目对象模型(POM)的概念，主要对项目的构建、依赖、测试、打包、部署、发布、生成站点文档等进行统一管理。

​	使用Maven构建工具，能够帮我们自动化构建过程，从清理、编译、测试到生成报告，再到打包和部署。我们不需要也不应该一遍又一遍地输入命令，一次又一次地点击鼠标，或者小心翼翼的写着配置文件，我们要做的是使用Maven配置好项目，然后输入简单的命令(如：mvn clean install)，Maven会帮我们处理那些烦琐的任务。这一切都体现了一句经典“约定优于配置”，当然Maven不仅是构建工具，还是一个依赖管理工具和项目信息管理工具，况且Maven也是跨平台的。

### 1.2 下载与安装

​	我们可以从Apache官网下载Maven的最新版本。下载地址：http://maven.apache.org/download.cgi，Binary是编译后的二进制压缩包，Source是源代码压缩包，如图1-1所示。

![01](https://ww4.sinaimg.cn/large/006tNbRwly1fw0lgk20fqj31kw0b0784.jpg)

​										图1-1

将下载的Binary压缩包解压到任意目录，可以看到有根目录下包含一些子目录和文件，如图1-2所示。

![02](https://ww1.sinaimg.cn/large/006tNbRwly1fw0lgrdsxyj31kw0iqq4o.jpg)

​										图1-2

目录说明：

| 目录 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| bin  | 存放Maven的运行脚本                                          |
| boot | 该目录只有一个文件plexus-classworlds-2.5.1.jar。他是一个类加载器的框架 |
| conf | 存放Maven的配置文件，最主要的就是settings.xml文件            |
| lib  | 包含Maven运行时需要的Java类库以及用到的第三方依赖            |

### 1.3 配置环境变量

​	将bin目录配置到环境变量中，方便在终端运行Maven的脚本命令。macOS或Linux 系统编辑用户下的.bash_profile文件添加MAVEN_HOME并指定根目录的路径，并将MAVEN_HOME追加到PATH中

~~~
export MAVEN_HOME=/Users/wangl/apache-maven-3.5.3
export PATH=$JAVA_HOME/bin:$MAVEN_HOME/bin:$PATH
~~~

Windows 系统在用户或系统环境变量中新建MAVEN_HOME并指定根目录路径

~~~
MAVEN_HOME=d:\apache-maven-3.5.3
~~~

接下来在path一栏的末尾追加%MAVEN_HOME%\bin

~~~
path=...;%MAVEN_HOME%\bin;
~~~

配置完成后可在命令行或者终端使用mvn命令查看Maven版本信息

```
mvn -v
```

### 1.4 配置本地仓库

​	在使用Maven之前，还可以配置一下本地仓库，这个仓库简单点说就是存放了项目需要用的jar文件，至于仓库的概念在后续的章节中会详细说明。当然，这一步操作并不是必须的，如果不指定本地仓库的位置，Maven缺省的本地仓库路径为${user.home}/.m2/repository，既在系统用户的目录下。这里我修改默认的仓库路径，指定为自定义的仓库路径。先在任意地方新建文件夹（名称任意），然后进入Maven的conf目录，打开settings.xml配置文件，找到如下配置，如图1-3所示。

![image-20190407204709885](https://ww1.sinaimg.cn/large/006tNc79gy1g1ucxgwbwgj319s0a3wgu.jpg)

​										图1-3

复制<localRepository>/path/to/local/repo</localRepository>这一行粘贴到注释的后面，并修改为自定义的仓库的绝对路径，如图1-4所示。

![image-20190407205406315](https://ww4.sinaimg.cn/large/006tNc79gy1g1ud4mpa90j319i0ca41h.jpg)

​										图1-4

注意：不同操作系统配置的绝对路径是有区别的，例如Linux是没有盘符的概念，这和windows并不一样。下面简单列出各种操作系统的配置样例：

macOS / Linux 系统：

~~~xml
<localRespsitory>/Users/wangl/repository</localRepository>
~~~

Windows 系统：

~~~xml
<localRespsitory>D:/maven/repository</localRepository>
~~~

### 1.5 settings.xml文件

settings.xml配置文件用于设置maven参数的配置文件，例如本地仓库配置、镜像配置、环境配置等等。这个配置文件可以存在两个地方：

1. Maven安装目录

```
${MAVEN_HOME}/conf/settings.xml
```

当我们解压Maven之后，在其中的conf子录中就可以找到，这里的settings.xml文件是Maven的全局的配置文件，对操作系统的所有用户都生效。

2. 系统用户目录

```
${user.home}/.m2/settings.xml
```

settings.xml还可以放在当前用户默认的.m2目录中，放在这里只针对当前系统用户生效。默认用户的.m2目录下是不会生成settings.xml文件的。如果需要，则可以从Maven安装目录中拷贝一份。此时，如果安装目录和用户目录下都存在settings.xml，那么Maven会在运行时将他们两者的配置进行合并，并且用户范围的settings.xml会覆盖全局的settings.xml，因为用户目录的优先级别要比全局的搞。

### 1.6 创建Maven项目

#### 1.6.1 使用mvn命令创建Maven项目

​	在终端使用下面的命令来创建一个Maven项目，依据提示选择项目的类型以及填写GAV坐标等信息来完成初始化工作。（注意：第一次构建过程中需要连接网络，Maven会从远程仓库下载相关的插件）

~~~
mvn archetype:generate
~~~

选择需要创建Maven项目的类型，这里选择7，表示快速创建一个简单的Maven项目，如图1-5所示。

![image-20190404101025871](https://ww4.sinaimg.cn/large/006tKfTcgy1g1qdnyqxspj31gx0rljzx.jpg)

​										图1-5

接下来填写Maven项目的组织名称（groupId）、项目名称（artifactId）、版本号（version）包名（package）等信息(注：关于GAV坐标后续章节会有详细说明)。最后输入y进行确认，这样我们就创建了一个Maven项目，如图1-6所示。

![image-20190404101825838](https://ww4.sinaimg.cn/large/006tKfTcgy1g1qdwacal7j31gy0p4af8.jpg)

​										图1-6

#### 1.6.2 使用Intellij IDEA构建

通常IDE都会内嵌一个Maven的版本，默认也是使用这个，在使用前我们可以先更换为我们自己下载的版本。打开IDEA，在欢迎页点击右下角的Configure设置项，在弹出的菜单中选择第一项Preferences，如图1-7。

![image-20190410104953984](https://ww4.sinaimg.cn/large/006tNc79gy1g1xcivxb1ej30d40g90v6.jpg)

​											图1-7					

在设置窗口中找到Build Tools下的Maven设置项，如图1-8。

![image-20190410104634466](https://ww3.sinaimg.cn/large/006tNc79gy1g1xcffay1xj30s20jbgob.jpg)

​											图1-8

在Maven home directory选项中我们看到有一个Bundled (Maven 3)，这个就是IDEA内嵌的Maven，将其更换为自己下载的Maven版本，点击右边的浏览小按钮，选择Maven的根目录即可。并在User settings file和Local repository中重新指定settings.xml和自己创建的本地仓库路径，如图1-9。

![image-20190410105627820](https://ww3.sinaimg.cn/large/006tNc79gy1g1xcpppjbzj30kx04n3yt.jpg)

​											图1-9

设置完点击OK，回到欢迎页点击Create New Project创建新项目，如图1-10。

![image-20190404104326918](https://ww3.sinaimg.cn/large/006tKfTcgy1g1qembni45j30db0dbjrx.jpg)

​										图1-10

在弹出的界面中选择左边的Maven选项，然后点击Next，如图1-11。

![image-20190404104450075](https://ww4.sinaimg.cn/large/006tKfTcgy1g1qenr8tk2j30o30kwwie.jpg)

​										图1-11

填写项目的groupId、artifactId、version，继续点击Next，如图1-12。

![image-20190404104712784](https://ww4.sinaimg.cn/large/006tKfTcgy1g1qeq8l579j30o20kx3z4.jpg)

​										图1-12

下面在Project location选择项目保存的位置，最后点击Finish完成项目的创建，如如1-13。

![image-20190404104905200](https://ww1.sinaimg.cn/large/006tKfTcgy1g1qes6vwk5j30o40kw0tc.jpg)

​										图1-13

最后等待IDEA初始化完整个Maven项目，如图1-14。

![image-20190404105203234](https://ww3.sinaimg.cn/large/006tKfTcgy1g1qev9v002j312k0np422.jpg)

​										图1-14

#### 1.6.3 使用Eclipse构建

同样，Eclipse也内嵌了Maven模块，首先替换成自己下载的Maven版本。打开Eclipse，在Preferences中找到Maven配置项并展开，如图1-15。

![image-20190410113039744](https://ww3.sinaimg.cn/large/006tNc79gy1g1xdpk5hxxj30zk0tcgqh.jpg)

​											图1-15

点击Installations，在右边点击Add按钮新建Maven配置，在弹出的子窗口中设置Installation home。点击Directory按钮，选择本地下载的Maven根目录，如图1-16。

![image-20190410113158350](https://ww2.sinaimg.cn/large/006tNc79gy1g1xdqo0600j30za0t6n6g.jpg)

​											图1-16

最后点击Finish按钮完成配置，注意勾选上新建的Maven配置项，如图1-17。

![image-20190410113447722](https://ww3.sinaimg.cn/large/006tNc79gy1g1xdtlchdzj30ze0t8wje.jpg)

​											图1-17

接着点击左边的User Settings选项，设置settings.xml文件的路径，在Global Settings处选择Maven全局settings文件的绝对路径，如果没有用户级别的settings文件则忽略，如图1-18。

![image-20190410113942972](https://ww2.sinaimg.cn/large/006tNc79gy1g1xdypx71qj30zc0taq7w.jpg)

​											图1-18

配置完成后接下来就可以创建Maven项目了，点击菜单的File—>New—>Maven Project新建Maven项目，如图1-19。

![image-20190404105634820](https://ww3.sinaimg.cn/large/006tKfTcgy1g1qezzdnw9j30fx0e840u.jpg)

​										图1-19

勾选Create a simple project创建一个简单的Maven项目，location处选择项目保存的工作空间，然后点击Next，如图1-20

![image-20190404111355498](https://ww2.sinaimg.cn/large/006tKfTcgy1g1qfi162wkj30gz0epgmg.jpg)

​											图1-20

在下面的窗口中同样填写groupId、artifactId、version以及Packaging打包方式，点击Finish完成项目的创建，如图1-21。

![image-20190404110125004](https://ww1.sinaimg.cn/large/006tKfTcgy1g1qf50klmhj30h10emjsc.jpg)

​											图1-21

最后等待Eclipse完成Maven项目初始化，如图1-22。

![image-20190404111518976](https://ww4.sinaimg.cn/large/006tKfTcgy1g1qfjh9ylwj30v20leq6m.jpg)

​											图1-22

#### 1.6.4 Maven项目结构 

​	无论使用哪种方式或者任何一种IDE创建出来的Maven项目都是符合Maven项目结构的标准。Maven项目结构主要包含以下的目录和文件：

| 目录               | 说明                                                        |
| ------------------ | ----------------------------------------------------------- |
| main/src/java      | 存放Java源代码的目录，这个目录下存放package以及Java源文件   |
| main/src/resources | 存放项目所需的资源文件和配置文件，例如xml、properties文件等 |
| test/src/java      | 存放单元测试类的源码目录                                    |
| test/src/resources | 存放单元测试所需的资源文件或配置文件                        |
| target             | 用于存放编译后的文件以及Maven打包的文件                     |
| pom.xml            | 项目对象模型                                                |

## 2. 仓库与依赖管理

### 2.1 本地仓库与远程仓库

​	Maven仓库本质上是系统中的某个目录，主要用于存放所有工程的jar文件以及Maven插件。在Maven中仓库分为远程和本地仓两种。当Maven在查找项目依赖库的时候最先是从本地仓库中查找，如果本地仓库中存在相应的jar文件，则直接从本地库中直接依赖，如果本地仓库没有，则访问远程仓库，并从远程仓库下载到本地仓库中再进行依赖。那么当下次再进行相同依赖的时候就不需要从远程仓库中获取了。Maven默认使用的是由 Maven 社区提供的一个远程仓库，仓库名为center。当本地仓库没有相关的项目时，就从center的远程中央仓库中查找，这里基本包含的绝大部分的开源项目。我们也可以指定为其他的远程仓库来替换默认的中央仓库。同时，我们开发的Maven项目最终也可以发布到本地或远程仓库中，那么其他人也可以从远程仓库进行查找并依赖，如图2-1所示。

![09](https://ww4.sinaimg.cn/large/006tNbRwly1fw0li8o764j30og0e4jsk.jpg)

​										图2-1

### 2.2 私服

​	私服也是远程仓库的一种，私服通常和开发人员的本地仓库在一个局域网中，在一些公司内部都会见到私服的存在，当对外网访问有限制时，那么私服的作用就可以体现出来了。通俗的讲，私服是建立在本地仓库和远程仓库之间，当项目需要依赖其他项目时，先从本地查找，没有则访问私服，如果私服中也没有，那么私服会执行从远程仓库中下载保存到私服中，最后再下拉取到本地仓库，如图2-2所示。

![10](https://ww2.sinaimg.cn/large/006tNbRwly1fw0limqln4j30p40dcgmw.jpg)

​											图2-2

通常构建私服我们会借助第三方的开源组件，最常用的就是Nexus，也是目前主流构建私服的组件。可以前往官网<https://www.sonatype.com/download-oss-sonatype>下载相应的版本，如图2-3所示。

![image-20190410091930388](https://ww4.sinaimg.cn/large/006tNc79gy1g1x9wvfupaj30zh0iiq65.jpg)

​											图2-3

这里不一一描述搭建的过程，需要时可以查阅相关文档。下面展示的是搭建好的Nexus私服仓库页面，如图2-4所示。

![image-20190404114644282](https://ww3.sinaimg.cn/large/006tKfTcgy1g1qgg6bo6aj31hb0qawj4.jpg)

​											图2-4

### 2.3 GAV坐标

​	无论在本地或远程仓库都会存放大量的项目，不同组织可以开发不同的项目，而同一个项目又具有多个不同的版本，那么Maven是如何快速找到相应版本的项目呢？这就是坐标的作用。Maven依据pom文件中配置的groupId（组织）、artifactId（项目名）、version（版本号）来定位到具体的项目，则三个元素就够成了GAV坐标。而在仓库中这三个元素都以目录的形式存在。如下图：

组织目录

~~~
/m2/org/springframework
~~~

说明：org.springframework为组织名称

项目目录

~~~
/m2/org/springframework/spring-jdbc
~~~

说明：spring-jdbc为该组织下的其中一个项目

版本号目录

~~~
/m2/org/springframework/spring-jdbc/5.1.1.RELEASE
~~~

说明：5.1.1.RELEASE为spring-jdbc其中的一个版本，如图2-4。

![image-20190404114039575](https://ww3.sinaimg.cn/large/006tKfTcgy1g1qg9uabd3j314005pdgi.jpg)

​										图2-4

最后根据版本就可以找到需要依赖的jar文件进行依赖，如图2-5。

![image-20190404114137529](https://ww1.sinaimg.cn/large/006tKfTcgy1g1qgauuzwoj313c05t750.jpg)

​										图2-5

### 2.4 镜像仓库

​	由于网络原因，有时候访问国外的远程仓库比较缓慢，那么这时我们可以使用镜像仓库来配置国内的一些远程仓库。镜像仓库类似于一个拦截器，它会拦截对远程仓库的相关请求，把请求里的地址重定向到镜像中所配置的地址，我们可以在settings.xml配置文件中的mirror节点进行配置，下面将访问默认远程中央仓库(center)的请求重定向到阿里云的远程仓库，如图2-6。

settings.xml示例：

~~~xml
<mirrors>
	<mirror>
  	<id>aliyun</id>
    <name>public</name>
    <url>https://maven.aliyun.com/repository/public</url>
    <mirrorOf>center</mirrorOf>
  </mirror>
</mirrors>
~~~

id表示镜像的唯一标识符（可自定义），name表示镜像的名称（可自定义），url表示镜像的地址，mirrorOf表示向center中央仓库请求都会重定向到阿里巴巴的仓库中，也可以配置成*号，表示所有请求都重定向到阿里巴巴的仓库中。

### 2.5 项目对象模型(POM)

​	Maven有两个很重的配置文件，一个是全局配置文件settings.xml，前面已经江苏过。它主要用于配置Maven环境以及一些全局性的配置。而另一个则是pom.xml（项目对象模型）。每一个Maven项目都包含一个pom文件，该文件用于配置当前Maven项目的坐标信息、打包方式、属性、相关依赖、插件、仓库等配置信息，因此它是项目级别的配置文件。下面列出pom.xml中常用的相关配置说明。

#### 2.5.1 modelVersion

pom的版本，为了确保稳定使用，这个元素是强制性的。除非Maven开发团队升级pom的模板，否则不需要修改。

pom.xml示例：

~~~xml
<modelVersion>4.0.0</modelVersion>
~~~

#### 2.5.2 当前项目的GAV坐标

每创建一个Maven项目，都应该为其制定一个坐标，那么在其他项目中需要使用到当前项目时，就可以通过坐标进行依赖。

pom.xml示例：

```xml
<!-- 组织名称 -->
<groupId>edu.nf</groupId>
<!-- 项目名称 -->
<artifactId>mavendemo</artifactId>
<!-- 版本号 -->
<version>1.0-SNAPSHOT</version>
```

#### 2.5.3 打包方式

表示Maven可将项目打包为jar或war的文件。

pom.xml示例：

```xml
<!-- 组织名称 -->
<groupId>edu.nf</groupId>
<!-- 项目名称 -->
<artifactId>mavendemo</artifactId>
<!-- 版本号 -->
<version>1.0-SNAPSHOT</version>
<!-- 打包为jar文件-->
<packaging>jar</packaging>
<!-- 打包为war文件 -->
<!--<packaging>war</packaging>-->
```

#### 2.5.4 属性配置

Maven属性可以分为好几类，常用的一类是内置属性，一类是用户自定义的属性，还有一类是pom属性。这里主要介绍内置属性和自定义属性，pom属性在模块化的章节中会进行介绍。

1.内置属性

这类属性主要用于设置Maven的基本配置以及插件属性设置。例如如项目编码，JDK版本，编译插件属性信息等等，这些属性的名称是固定的。

pom.xml示例：

```xml
<properties>
  			<!-- 设置项目的编码 -->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  			<!-- 设置java的版本为1.8-->
        <java.version>1.8</java.version>
  			<!-- 源码编译的版本为1.8-->
        <maven.compiler.source>1.8</maven.compiler.source>
  			<!-- 目标字节码的版本为1.8-->
        <maven.compiler.target>1.8</maven.compiler.target>
  			<!-- 指定编译器版本为1.8-->
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
</properties>
```

2.自定义属性

这类属性主要是由用户自定义的。例如定义所有依赖jar的版本号，方便在依赖时统一版本号的引用。

pom.xml示例：

~~~xml
<properties>
  			<!-- servlet version -->
        <servlet.version>3.1</project.build.sourceEncoding>
  			<!-- junit version-->
        <junit.version>4.12</junit.version>
  			<!-- mysql driver .version-->
        <mysql.driver.version>5.1.47</mysql.driver.version>
</properties>
~~~

#### 2.5.5 依赖配置

项目所需要的第三方jar文件的依赖配置，配置依赖通常只要填写第三方库的GAV坐标以及作用域。如果不清楚库的坐标如何填写，可到https://mvnrepository.com/这个网站中进行查找。

pom.xml示例：

```xml
<dependencies>
        <!-- Junit依赖-->
        <dependency>
            <!-- 指定GAV坐标-->
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
          	<version>4.12</version>
          	<!-- 版本号也可以使用${}引用properties中自定义属性的标签名称 -->
            <!-- <version>${junit.version}</version>-->
        </dependency>
  			<!-- mysql驱动依赖-->
  			<dependency>
             <groupId>mysql</groupId>
             <artifactId>mysql-connector-java</artifactId>
          	 <version>5.1.47</version>
             <!-- 版本号也可以使用${}引用properties中自定义属性的标签名称 -->
             <!-- <version>${mysql.driver.verion}</version> -->
        </dependency>
</dependencies>  
```

#### 2.5.6 添加远程仓库

我们都知道Maven在查找依赖的库时会先从本地仓库查找，如果查找不到会从默认的远程仓库或者指定的镜像仓库中查找，但如果有时我们需要的jar并不在这些远程仓库中时，而是存在一个特定的公共库中，那么我们可以在pom中来配置指定的远程仓库。

pom.xml示例：

~~~xml
<repositories>
	<repository>
      <!-- 仓库唯一标识 -->
      <id>jboss-repo</id>
    	<!-- 仓库名称 -->
      <name>jboss repository</name>
      <!-- 仓库的url -->
      <url>http://repository.jboss.org/nexus/content/groups/public/</url>
      <!-- 允许拉取已发布的稳定版本 -->
      <releases>
        <enabled>true</enabled>
      </releases>
      <!-- 不拉取快照版本-->
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
</repositories>
~~~

说明：releases表示已发布的版本，即稳定版本，这里设置为true表示允许从远程仓库拉取稳定版本的库。snapshots表示快照，即不稳定的版本，这里设置为false，表示禁止使用快照仓库。

#### 2.5.7 发布到远程仓库

有时我们开发的项目或者中间件需要发布到远程仓库或者私服中，便于别人从远程仓库或私服中依赖使用。

pom.xml示例：

~~~xml
<distributionManagement>
    <!-- 发布仓库 -->
    <repository>
        <id>releases</id>
        <url>http://localhost:8081/repository/maven-releases/</url>
    </repository>
    <!-- 快照仓库 -->
    <snapshotRepository>
        <id>snapshots</id>
        <url>http://localhost:8081/repository/maven-snapshots/</url>
    </snapshotRepository>
</distributionManagement>
~~~

repository为发布稳定版本的仓库，snapshotRespository为不稳定的快照仓库。id为仓库的唯一标识(可任意命名)，url为仓库地址(这里使用自行搭建的Nexus私服)。通常为了安全起见，我们会为私服的仓库设置相应的账号密码，那么我们还需要在settings.xml中的servers元素中配置仓库的账号。

settings.xml示例：

~~~xml
<servers>
		<!-- server中的id必须和pom文件中repository的id一致 -->
    <server>
      <id>snapshots</id>
      <username>admin</username>
      <password>admin123</password>
    </server>
    <server>
      <id>releases</id>
      <username>admin</username>
      <password>admin123</password>
    </server>
</servers>
~~~

server中的id必须和distributionManagement中repository的id保持一致。username和password对应仓库设置的账号密码。最后再IDE或者终端中中执行mvn deploy命令完成发布。注意，基于生命周期的特性，执行deploy这个阶段，会先执行deploy之前的所有阶段，例如package(打包)、install(安装到本地仓库)等等。

#### 2.6 Maven依赖管理

​	通过前面的学习，我们知道Maven对于项目中的依赖，会从本地仓库或远程仓库中查找。除此之外，Maven在的依赖的过程中还包括依赖的传递、依赖的优先级别、排除依赖、可选依赖、及依赖范围等特点，下面将分别进行讲解和学习。

#### 2.6.1 依赖传递性

Maven的依赖是具有传递性的。举个例子，项目中只配置依赖了A，而A又依赖了B，B又依赖了C，那么项目就间接的依赖了B和C，这就是依赖传递性。

~~~
A->B->C
~~~

#### 2.6.2 依赖传递的优先级别

上面我们知道依赖具有传递性，接下来我们看看下面的情况。

~~~
A->B->C->X(1.0)
A->D->X(2.0)
~~~

上面A依赖B，B依赖C，C依赖X，同时A又依赖了D，D也依赖了X。如果两个X的版本（version）不一样，一个是1.0和一个2.0，此时Maven会选择哪一个呢？由于只能引入一个版本。因此Maven优先选择最短路径的依赖，也就是A->D->X(2.0)。

另外一种情况就是如果路径长度是一样，像以下的情况。

~~~
A->B->C->X(1.0)
A->D->E->X(2.0)
~~~

此时Maven会按照配置顺序选择第一个，也就是A->B->C->X(1.0)。因此我们得出一个结论，当出现依赖同一个项目时，优先选择最短路径的依赖，如果路径长度相同，则按照配置顺序选择最前的进行依赖。

#### 2.6.3 依赖排除

Maven 中的依赖关系是有传递性的。例如：项目B依赖项目C（B —> C），如果有一个项目A依赖项目B（A —> B）的话，那么项目A也会依赖项目C（A —> C）。虽然，这种依赖的自动传递性给我们维护项目的必要依赖关系带来了极大地方便，但在某些情况下，需要在项目A中排除对项目C的依赖时，这时又该怎么做呢？Maven 为我们提供了两种解决方案，分别是可选依赖和依赖排除。下面先看看依赖排除的使用。

例如在项目A的pom文件中依赖了项目B。

pom.xml示例：

~~~xml
<!-- 在project-A中依赖了project-B -->
<dependencies>
	<dependency>
  	<groudId>edu.nf</groudId>
    <artifactId>project-B</artifactId>
    <version>1.0-SNAPSHOT</version>
  </dependency>
</dependencies>
~~~

接着项目B的pom文件中依赖了项目C。

pom.xml示例：

~~~xml
<!-- 在project-B中依赖了project-C -->
<dependencies>
	<dependency>
  	<groudId>edu.nf</groudId>
    <artifactId>project-C</artifactId>
    <version>1.0-SNAPSHOT</version>
  </dependency>
</dependencies>									
~~~

此时项目A会间接依赖项目C(传递依赖)，如果想要在A中排除对C的依赖，只需要在A中配置exclusions即可。(注：排除时不需要指定version）

pom.xml示例：

~~~xml
<!-- 在project-A中依赖了project-B -->
<dependencies>
	<dependency>
  	<groudId>edu.nf</groudId>
    <artifactId>project-B</artifactId>
    <version>1.0-SNAPSHOT</version>
    <exclusions>
      <!-- 排除对project-C的依赖 -->
    	<exclusion>
      	<groupId>edu.nf</groupId>
        <artifactId>project-C</artifactId>
      </exclusion>
    </exclusions>
  </dependency>
</dependencies>								
~~~

#### 2.6.4 可选依赖

上面使用排除的方式排除了A对C的依赖，还可以使用另一种方式达到同样效果，就是可选依赖。可选依赖可以在B依赖C的时候设置optional为true。

pom.xml示例：

~~~xml
<!-- 在project-B中依赖了project-C -->
<dependencies>
	<dependency>
  	<groudId>edu.nf</groudId>
    <artifactId>project-C</artifactId>
    <version>1.0-SNAPSHOT</version>
    <!-- 将project-C设置为可选依赖 -->
    <optional>true</optional>
  </dependency>
</dependencies>										
~~~

那么当A依赖B时，就不会间接依赖C。

pom.xml示例：

~~~xml
<!-- 在project-A中依赖了project-B,但不会间接依赖peoject-C -->
<dependencies>
	<dependency>
  	<groudId>edu.nf</groudId>
    <artifactId>project-B</artifactId>
    <version>1.0-SNAPSHOT</version>
  </dependency>
</dependencies>
~~~

如果A想再次用到C的话，那么可以在项目A对C重新进行依赖。

pom.xml示例：

~~~xml
<!-- 在project-A中依赖了project-B -->
<dependencies>
	<dependency>
  	<groudId>edu.nf</groudId>
    <artifactId>project-B</artifactId>
    <version>1.0-SNAPSHOT</version>
  </dependency>
  <!-- 重新依赖project-C -->
  <dependency>
  	<groudId>edu.nf</groudId>
    <artifactId>project-C</artifactId>
    <version>1.0-SNAPSHOT</version>
  </dependency>
</dependencies>									
~~~

#### 2.6.5 依赖范围

依赖范围表示依赖的项目在哪些情况下需要存在，由依赖配置中的`<scope>`决定。

pom.xml示例：

~~~xml
<dependency>
	<groupId>javax.servlet</groupId>
  <artifactId>javax.servlet-api</artifactId>
  <!-- 指定依赖的作用范围 -->
  <scope>provided</scope>
</dependency>									
~~~

Maven的依赖范围总共分为5种，compile (编译)、test (测试)、runtime (运行时)、provided（提供者）、system（系统）。如果不指定，则默认为compile。

| 范围     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| compile  | 在编译，测试，运行时都需要                                   |
| test     | 测试时需要。编译和运行不需要。（例如：Junit）                |
| runtime  | 测试和运行时需要。编译不需要。（例如：JDBC驱动）             |
| provided | 编译和测试时需要。运行时不需要，由运行环境提供。（例如：servlet-api） |
| system   | 本地依赖，不在maven中央仓库                                  |

## 3. 生命周期

Maven在构建一个项目时将一个整体的任务划分为一个个的阶段，类似一个工作流水线，按顺序依次执行，这就是Maven的构建生命周期。Maven总共有三套完整的生命周期，而每套生命周期会完成相关的一系列工作。每套生命周期又由多个阶段组成，每个阶段专门负责完成一个具体的子任务。例如：清除阶段、编译阶段、打包阶段等等。	

### 3.1 Clean生命周期

这套生命周期主要用于在进行真正的构建之前进行一些清理工作。

| 阶段       | 说明                     |
| ---------- | ------------------------ |
| pre-clean  | 执行清理前需要完成的工作 |
| clean      | 清理上一次构建生成的文件 |
| post-clean | 执行清理后需要完成的工作 |

### 3.2 Default生命周期

这套生命周期是最核心也是最重要的，从项目的验证、初始化到项目的编译、测试、部署、打包等操作都是在这套生命周期中完成。

| 阶段                    | 说明                                                         |
| ----------------------- | ------------------------------------------------------------ |
| validate                | 验证项目是否正确和所有需要的相关资源是否可用                 |
| initialize              | 初始化构建                                                   |
| generate-sources        | 为包含在编译范围内的代码生成源代码                           |
| process-sources         | 处理源代码                                                   |
| generate-resources      | 生成资源文件                                                 |
| compile                 | 编译项目的源代码                                             |
| process-classes         | 为编译生成的class文件做后期工作                              |
| generate-test-sources   | 为编译内容生成测试源代码                                     |
| process-test-source     | 处理测试源代码                                               |
| generate-test-resources | 生成测试所需的资源文件                                       |
| process-test-resources  | 复制并处理资源文件，至目标测试目录                           |
| test-compile            | 编译测试源代码                                               |
| process-test-classes    | 为编译生成测试的class文件做后期处理工作                      |
| test                    | 使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署 |
| prepare-package         | 执行打包前的预处理工作                                       |
| package                 | 接受编译好的代码，打包成可发布的格式，如 jar或war            |
| pre-integration-test    | 预集成测试                                                   |
| integration-test        | 按需求将发布包部署到运行测试环境                             |
| post-integration-test   | 执行整合测试                                                 |
| verify                  | 验证包是否有效并符合标准                                     |
| install                 | 将包安装至本地仓库                                           |
| deploy                  | 将最终的包发布到远程的仓库                                   |

### 3.3 Site生命周期

这套生命周期用于生成项目报告，站点，发布站点。

| 阶段        | 说明                                                       |
| ----------- | ---------------------------------------------------------- |
| pre-site    | 执行一些需要在生成站点文档之前完成的工作                   |
| site        | 生成项目的站点文档                                         |
| post-site   | 执行一些需要在生成站点文档之后完成的工作，并且为部署做准备 |
| site-deploy | 将生成的站点文档部署到特定的服务器上                       |

### 3.4 执行阶段命令

每个阶段的工作都是由一个具体的插件来完成，那么每一个阶段我们也可以当成是一个Maven的命令来执行。例如执行mvn package命令，其实就是调用package相关插件来完成package阶段的工作。

1. 可以选择某个生命周期中的某个阶段执行，例如:

~~~
mvn package
~~~

这里选择了Default生命周期的package（打包）阶段来执行，需要注意的是，如果选择某个阶段来执行，那么在这个阶段之前的所有阶段都会先执行。

2. 也可以选择不同生命周期的阶段来混合执行，例如：

~~~
mvn clean package
~~~

这里选择了Clean周期的clean阶段以及Default周期的package阶段来混合执行，同时这两个阶段之前的所有阶段都会被执行。

### 3.5 插件

Maven的每一套生命周期都是由不同的阶段组成，不同阶段完成不同的任务，那这些阶段是如何完成任务的呢？这就是插件的作用。在Maven中，不同的阶段都会与相关的插件进行绑定，而这个插件就是完成这个阶段的具体工作。例如，当执行mvn compile(执行编译阶段)，与之对应的是maven-compiler-plugin插件来完成这个阶段的工作。当执行mvn test(执行测试阶段)，对应的是maven-surefire-plugin这个插件来完成这个阶段的工作。插件一样也是以项目的形式保存在仓库中，默认的中央仓库就保存了大量的插件库。当我们第一次使用Maven构建时，会从远程仓库下载到我们的本地仓库中，位于本地仓库的org/apache/maven/plugins目录下。如图3-1。

![image-20190410155013840](https://ww4.sinaimg.cn/large/006tNc79gy1g1xl7dxi26j31zi0fuq8s.jpg)

​											图3-1

在这个目录下可以找到刚才我们所说的maven-compiler-plugin和maven-surefire-plugin插件。我们也可以在pom文件对插件进行的配置，修改默认行为。例如，设置maven-compiler-plugin将默认的JDK编译版本设置为1.8。因为编译插件默认是使用1.5版本，即便安装的JDK是jdk1.7、jdk1.8也是采用1.5版本进行编译。

pom.xml示例： 

~~~xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
~~~

注意，插件的设置必须放在`<build>`元素下配置，并在`<configuration>`元素下制定source和target为1.8。如果要配置多个插件，那么只需要在`<plugis>`下添加多个`<plugin><plugin/>`即可。这个例子仅仅是为了演示插件的配置。设置JDK的版本其实还可以使用另一种方式，就是在pom文件properties元素中配置插件属性，Maven会将属性的值设置到对应的插件中。

~~~xml
<properties>
  			<!-- 设置java的版本为1.8-->
        <java.version>1.8</java.version>
  			<!-- 源码编译的版本为1.8-->
        <maven.compiler.source>1.8</maven.compiler.source>
  			<!-- 目标字节码的版本为1.8-->
        <maven.compiler.target>1.8</maven.compiler.target>
  			<!-- 指定编译器版本为1.8-->
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
</properties>
~~~

再比如我们可以在打包阶段利用maven-war-plugin插件对web类型的项目进行一些设置。

~~~xml
<build>
      <plugins>
          <!-- war插件 -->
          <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-war-plugin</artifactId>
              <version>2.2</version>
              <configuration>
                  <!-- 指定项目的web目录 -->
                  <warSourceDirectory>web</warSourceDirectory>
                  <!-- 指定web.xml路径 -->
                  <!--<webXml>web\WEB-INF\web.xml</webXml>-->
                  <!-- 忽略web.xml文件-->
                  <failOnMissingWebXml>false</failOnMissingWebXml>
                </configuration>
            </plugin>
        </plugins>
    </build>
~~~

注意：failOnMissingWebXml设置为false表示忽略项目的web.xml，如果你的web项目是基于Servlet3.0或者以上的版本，并且没有创建web.xml，那么可以通过此设置进行或略。还有另一种方式来忽略，同样是在properties中配置。

~~~xml
<properties>
    <failOnMissingWebXml>false</failOnMissingWebXml>
</properties>
~~~

## 4. 模块化

​	目前越来越多的项目采用模块化开发，通过合理的模块拆分，实现代码的复用性，便于维护和管理。尤其在很多的开源框架中也采用多模块的方式，用户可以根据需要配置指定的模块。Maven的模块化可分为父模块与子模块，它同样具备面向对象思想中的继承和聚合。所有子模块都可以继承自父模块，在父模块中可以聚合（包含）所有的子模块。通常父模块并不包含业务代码的实现，它的作用就是用于包含所有的子模块并统一管理。如图3-1所示。

![17](https://ww4.sinaimg.cn/large/006tNbRwly1fw0lk3xabcj30km0gota9.jpg)

​										图3-1

每个子模块都是一个具体的maven项目来完成不同的业务功能，同时它们拥有自己的pom文件，不同的子模块可以在自己的pom文件中依赖自己所需要的类库以及相关配置。而父模块则只有一个pom文件，通常将所有子模块的公共依赖或配置可以放在父模块的pom文件中，从而子模块无需重复配置。

### 4.1 继承

面向对象的思想中继承是为了提取共性，避免重复。而Maven的继承也是如此，将所有子模块共性的配置都可以统一放到父模块的pom文件中进行配置，不需要在每个子模块都配置一遍，这也是基于面向对象的思想。（注意：父模块的pom.xml文件中的packaging必须设置为pom，即文档对象模型）

父模块pom.xml示例：

~~~xml
<groupId>edu.nf</groupId>
<artifactId>project-parent</artifactId>
<version>1.0-SNAPSHOT</version>
<packaging>pom</packaging>

<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  	<junit.version>4.12</junit.version>
</properties>

<dependencies>
		<dependency>
  			<groupId>junit</groupId>
      	<artifactId>junit</artifactId>
      	<verison>${junit.version}</verison>
    </dependency>
</dependencies>
~~~

这里将项目的一些属性配置以及junit都放到了父模块中配置。properties中可以设置项目的构建编码、JDK版本等配置。还可以统一所有依赖项目的版本号的管理。

子模块pom.xml示例：

~~~xml
<parent>
		<artifactId>project-parent</artifactId>
    <groupId>edu.nf</groupId>
  	<version>1.0-SNAPSHOT</version>
  	<relativePath>../pom.xml</relativePath>
</parent>
<artifactId>project-module1</artifactId>
<packaging>jar</packaging>
~~~

在子模块需在`<parent>`中配置父模块的GAV坐标，以及在`<relativePath>`中设置父模块的pom文件的相对路径。而在父模块中配置的属性以及所有的依赖都会被子模块继承下来。子模块需要重新定义`<artifactId>`的名称以及`<packaging>`即可。

### 4.2 聚合

所谓聚合，顾名思义，就是把多个模块或项目聚合到一起。可以在父模块中聚合所有的子模块，只需要在父模块的pom文件中将所有的子模块配置在`<modules>`中，`<modules>`中的每个`<module>`分别指定各个子模块的名称。

父模块pom.xml示例：

~~~xml
<groupId>edu.nf</groupId>
<artifactId>project-parent</artifactId>
<version>1.0-SNAPSHOT</version>
<packaging>pom</packaging>
<!-- 聚合所有子模块 -->
<modules>
		<module>project-module1</module>
  	<module>project-module2</module>
  	<module>project-module3</module>
</modules>
~~~

使用聚合的好处就是当构建整个项目的时候，不需要为每个子模块分别都构建一次，只需要在父模块（相当于整个项目）执行一次构建命令即可，此时会将所有的子模块一并进行构建。

### 4.3 按需依赖

有时候并不是所有个子模块都需要继承父类全部的依赖配置，不同的子模块可以根据需要分别继承不同的依赖配置，这时我们就可以在父模块中使用`<dependencyManagement>`来管理所有依赖配置。

父模块pom.xml示例：

~~~xml
<groupId>edu.nf</groupId>
<artifactId>project-parent</artifactId>
<version>1.0-SNAPSHOT</version>
<packaging>pom</packaging>

<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  	<junit.version>4.12</junit.version>
</properties>

<!-- 依赖管理 -->
<dependencyManagement>
		<dependencies>
				<dependency>
  					<groupId>junit</groupId>
      			<artifactId>junit</artifactId>
      			<verison>${junit.version}</verison>
    		</dependency>
		</dependencies>
</dependencyManagement>
~~~

上面的junit依赖配置是放在`<dependencyManagement>`中，这时所有的子模块并不会继承junit的依赖，各个子模块可以根据自身需要再对其进行依赖配置。

子模块pom.xml示例：

~~~xml
<parent>
		<artifactId>project-parent</artifactId>
    <groupId>edu.nf</groupId>
  	<version>1.0-SNAPSHOT</version>
  	<relativePath>../pom.xml</relativePath>
</parent>
<artifactId>project-module1</artifactId>
<packaging>jar</packaging>

<dependencies>
			<dependency>
  				<groupId>junit</groupId>
      		<artifactId>junit</artifactId>
    	</dependency>
</dependencies>
~~~

（注：子模块在依赖时只需要指定groupId和artifactId即可，不需要指定版本号）

### 4.4 依赖子模块

通常项目划分多个子模块之后，某些子模块需要使用其他模块的类来完成相关的逻辑处理。例如，model-a需要调用model-b的某些类，那么model-a就需要依赖model-b。

model-a的pom示例：

~~~xml
<parent>
		<artifactId>project-parent</artifactId>
    <groupId>edu.nf</groupId>
  	<version>1.0-SNAPSHOT</version>
  	<relativePath>../pom.xml</relativePath>
</parent>
<artifactId>project-module1</artifactId>
<packaging>jar</packaging>

<dependencies>
      <!-- 依赖model-b -->
			<dependency>
  				<groupId>edu.nf</groupId>
      		<artifactId>model-b</artifactId>
          <!-- version使用一个pom属性引用当前项目的版本号 -->
          <version>${project.version}</version>
    	</dependency>
</dependencies>
~~~

这里version为${project.version}，这个Maven的一个pom属性，表示引用当前项目的版本号`<version>`1.0-SNAPSHOT`</version>`。Maven的pom属性还有很多，下面列出常见的pom属性。

| pom属性                          | 说明                                   |
| -------------------------------- | -------------------------------------- |
| ${project.build.directory}       | 表示主源码路径，缺省为target           |
| ${project.build.outputDirectory} | 构建过程输出目录，缺省为target/classes |
| ${project.build.sourceEncoding}  | 表示主源码的编码格式                   |
| ${project.build.sourceDirectory} | 表示主源码路径                         |
| ${project.build.finalName}       | 表示输出文件名称                       |
| ${project.packaging}             | 打包类型，缺省为jar                    |
| ${project.version}               | 表示项目版本                           |

