# RabbitMQ基础教程

## 1.什么是消息队列

消息队列（Message Queue），我们一般简称为MQ。消息队列中间件是分布式系统中重要的组件，具有异步性、松耦合、分布式、可靠性等特点。用于实现高性能、高可用、可伸缩和最终一致性架构。是大型分布式系统不可缺少的中间件。目前主流的消息队列有ActiveMQ、Kafka、RabbitMQ、ZeroMQ、MetaMQ、RocketMQ等。消息队列在很多业务场景中都会使用到，例如：异步处理、应用解耦、流量消锋、数据同步、日志处理等等。下面是一个消息队列最简单的架构模型。

![image-20200305112000802](https://tva1.sinaimg.cn/large/00831rSTgy1gcivvybirdj30u306z74v.jpg)

**名词解释：**

- Producer：消息的生产者，负责将消息发送到Broker
- Broker：消息处理中心（内部通常包含多个队列，称之为queue），负责消息的存储等操作
- Consumer：消息消费者，负责从Broker中获取消息并进行相应处理

## 2.RabbitMQ入门

### 2.1 简介

RabbitMQ是流行的开源消息队列其中的一种，用erlang语言开发。它基于AMQP协议（AMQP是应用层协议的一个开放标准，称为高级消息队列协议，专门为面向消息的中间件设计）的标准实现。RabbitMQ支持多种语言客户端（如：Java、C#、Python、Ruby、C、PHP）等。在易用性、扩展性、高可用性等方面表现都不错。

### 2.2 安装

由于RabbitMQ是基于erlang语言编写，在安装前先必须安装erlang环境。

官网地址：https://www.erlang.org/downloads。

![image-20200305115820428](https://tva1.sinaimg.cn/large/00831rSTgy1gciwzsrq19j30w80dgjtl.jpg)

最新版本为22.2，Windows用户可直接下载OPT 22.2 Windows 64-bit Binary直接安装即可。

![image-20200305123113620](https://tva1.sinaimg.cn/large/00831rSTgy1gcixy0mndoj310y0rgtga.jpg)

接着去RabbitMQ官网下载最新版本的安装包进行安装。

官网地址：https://www.rabbitmq.com/install-windows.html#chocolatey

![image-20200305120810690](https://tva1.sinaimg.cn/large/00831rSTgy1gcixa19v88j30yi0h70wn.jpg)

点击下载rabbitmq-server-3.8.2.exe的Bintray安装包，下载后直接打开安装。

![image-20200305123032711](https://tva1.sinaimg.cn/large/00831rSTgy1gcixxawv5nj30zc0ougrl.jpg)

如果是macOS用户，可以通过Homebrew直接安装，并且Homebrew在安装RabbitMQ时会自动下载并安装erlang环境。

![image-20200305123233634](https://tva1.sinaimg.cn/large/00831rSTgy1gcixze90kfj31h407ajsr.jpg)

### 2.3 配置环境变量

将RabbitMQ安装目录下的sbin子目录加入到环境变量的Path中。

![image-20200305124431879](https://tva1.sinaimg.cn/large/00831rSTgy1gciybuxn9bj31ep0u0wm9.jpg)

![image-20200305124601263](https://tva1.sinaimg.cn/large/00831rSTgy1gciydemdepj30wu0u0jzy.jpg)

### 2.4 启动/停止服务

启动或停止服务有应用方式启动和服务启动两种方式。

**应用方式启动：**

| 命令                      | 说明                                                   |
| ------------------------- | ------------------------------------------------------ |
| rabbitmq-server           | 直接启动，关闭窗口后应用就会停止                       |
| rabbitmq-server -detached | 后台启动，后台独立进程方式运行，关闭窗口后应用不会关闭 |
| rabbitmqctl stop          | 停止应用                                               |

示例：

![image-20200305131956179](https://tva1.sinaimg.cn/large/00831rSTgy1gcizcpavgnj31ie0liwih.jpg)

**服务方式启动：**

当安装完后可以在服务列表中查看到RabbitMQ这个服务，可以在这里直接启用或停止。

![image-20200305130956496](https://tva1.sinaimg.cn/large/00831rSTgy1gciz2aukawj30xw0u0qas.jpg)

也可以在命令行使用相关命令启动或关闭服务（注意：控制台要以管理员方式运行）

| 命令                     | 说明     |
| ------------------------ | -------- |
| rabbitmq-service start   | 启动服务 |
| rabbitmq-service stop    | 停止服务 |
| rabbitmq-service disable | 禁用服务 |
| rabbitmq-service disable | 启用服务 |

示例：

![image-20200305132153801](https://tva1.sinaimg.cn/large/00831rSTgy1gcizeqcntpj31ii08wq45.jpg)

### 2.5 可视化管理插件

RabbitMQ默认提供了一个rabbitmq_management可视化管理插件，方便我们通过web访问的方式来管理和查看RabbitMQ。此插件默认是禁用的，因此需要手动启用它。在命令行使用rabbitmq-plugins来启用插件。如下：

~~~
rabbitmq-plugins enable rabbitmq_management
~~~

![image-20200305133217477](https://tva1.sinaimg.cn/large/00831rSTgy1gcizpjy1pxj31ic0t4tbu.jpg)

启用后可以在浏览器中输入http://localhost:15672来访问登录页面，默认登陆账号和密码都为`guest`。

![image-20200305133422455](https://tva1.sinaimg.cn/large/00831rSTgy1gcizrpqcs2j32rc0g8wh1.jpg)

登陆成功后进入功能管理首页。

![image-20200305133614051](https://tva1.sinaimg.cn/large/00831rSTgy1gciztnk7lkj31j50u0497.jpg)

在后续的示例中会讲解这里面的具体内容。

### 2.6 用户管理

RabbitMQ默认提供了一个guest用户，我们也可以创建新用户并给用户分配相应的权限。创建用户有两种方式，一种是使用rabbitmqctl工具，另一种是使用可视化的方式操作。

**使用可视化操作：**

在web管理登陆页面登陆后，点击Admin选项，这里会列出所有的用户信息，默认只有一个guest用户，如下：

![image-20200305172937716](https://tva1.sinaimg.cn/large/00831rSTgy1gcj6ki44juj31dq0pcgok.jpg)

点击下面的Add a user，在展开的页面中填写新用户的姓名、密码以及身份标签，确认无误后点击Add User按钮保存。如下：

![image-20200305173112183](https://tva1.sinaimg.cn/large/00831rSTgy1gcj6m4t1knj31f00h00ub.jpg)

此时用户列表就会多出一个新建的用户，如下：

![image-20200305173342355](https://tva1.sinaimg.cn/large/00831rSTgy1gcj6oqrxskj31ig0g4402.jpg)

但这个用户还不能正常使用，因为还未分配访问的虚拟主机（虚拟主机的概念会在下个章节说明）以及权限，所以点击列表中的用户名（也就是wangl）跳转到如下页面：

![image-20200305173710814](https://tva1.sinaimg.cn/large/00831rSTgy1gcj6scyvfjj31fo0u0tca.jpg)

说明：

- Virtual Host：设置虚拟主机的路径，默认为“/”，因为没有新创建别的虚拟主机，所以只有一个默认的。
- Configure regexp：设置用户的配置权限，支持正则表达式（.*表示所有）。
- Write regexp：设置用户的写权限，支持正则表达式（.*表示所有）。
- Read regexp：设置用户的读权限，支持正则表达式（.*表示所有）。

最后点击Set permission按钮保存，然后回到用户列表，这时新建的用户就能正常使用了

![image-20200305174251836](https://tva1.sinaimg.cn/large/00831rSTgy1gcj6y9lz3kj316w0ey3zs.jpg)

登出后使用新用户登陆来访问。

**使用rabbitmqctl工具：**

在命令行可以使用rabbitmqctl，它是RabbitMQ中间件的一个命令行管理工具。

1.创建用户：

命令：rabbitmqctl add_user username password

示例：rabbitmqctl add_user user1 123

2.删除用户：

命令：rabbitmqctl delete_user username

示例：rabbitmqctl delete_user user1

3.修改密码：

命令：rabbitmqctl change_password username newpassword

示例：rabbitmqctl change_password user1 321

4.列出所有用户：

命令：rabbitmqctl list_users

5.设置用户权限：

命令：rabbitmqctl set_permissions [-p vhostpath] username <conf> <write> <read>

示例：rabbitmqctl set_permissions -p / user1 .* .* .*

6.删除用户权限：

命令：rabbitmqctl clear_permissions [-p vhostpath] username

示例：rabbitmqctl clear_permissions -p / user1

### 2.7 配置文件

不同的操作系统默认存放的配置文件目录是不一样的（也可以通过环境变量指定配置文件的目录），下面列出在不同系统中默认配置文件的存放位置。

| 系统    | 位置                                         |
| ------- | -------------------------------------------- |
| Debian  | /etc/rabbitmq/                               |
| Windows | C:\Users\%USERNAME%\AppData\Roaming\RabbitMQ |
| macOS   | /usr/local/etc/rabbitmq/                     |

以Windows为例，我们在C:\Users\%USERNAME%\AppData\Roaming\RabbitMQ目录下创建一个名为rabbitmq.conf的配置文件。

![image-20200305164522588](https://tva1.sinaimg.cn/large/00831rSTgy1gcj5ag61qyj31z20gedj9.jpg)

使用记事本打开添加如下配置信息可以修改默认的配置。

~~~properties
listeners.tcp.default = 5673
management.listener.port = 15673
num_acceptors.tcp = 10
~~~

说明：

| 属性                     | 描述                                                   | 默认值 |
| ------------------------ | ------------------------------------------------------ | ------ |
| listeners.tcp.default    | AMQP连接的默认监听端口，也就是访问RabbitMQ的默认端口号 | 5672   |
| management.listener.port | 访问web管理插件的默认端口                              | 15672  |
| num_acceptors.tcp        | 接受tcp连接的erlang进程数                              | 10     |

这里我们修改了默认的tcp连接端口以及web管理插件的默认端口，配置完成之后记得要重启RabbitMQ服务，接着重新打开web管理页面，使用修改后的端口进行访问。

![image-20200305171623664](https://tva1.sinaimg.cn/large/00831rSTgy1gcj66qawgfj31e608t754.jpg)

> 参考：https://www.linuxidc.com/Linux/2019-03/157354.htm

### 2.8 AMQP通信模型

![image-20200305231337756](https://tva1.sinaimg.cn/large/00831rSTgy1gcjgifl28aj31qe0u07d8.jpg)

名词解释：

- Broker：消息处理中心，也就是RabbitMQ Server。
- Virutal Host：虚拟主机相当于一个命名空间。用于隔离不同的Exchange和Queue。每个Virutal Host内部有自己的Exchange和Queue，他们之间互不影响。我们可以为不同用户指定不同的Virutal Host，这样不同用户只能访问当前设置的Virutal Host下的Exchange和Queue。而不能访问其他的Virutal Host。在RabbitMQ有一个默认的Virutal Host就是“/”。我们也可以通过可视化插件或者使用rabbitmqctl工具来创建新的Virutal Host。
- Exchange：Exchange也称之为交换机，核心作用就是将消息生产者（Producer）发送过来的message依据指定的路由规则发送到特定的Queue中。
- Queue：存放message的队列，消息最终会被消息消费者（Consumer）取出消费。
- Producer：消息的生产者，负责将消息发送到交换机（Exchange）中。
- Consumer：消息消费者，负责从Queue中获取消息并进行相应处理。
- Binding：Binding就是将一个或者多个消息队列（Queue）绑定到交换机（Exchange）上。绑定时会设置一个路由的key（一种路由规则表达式）。这样当Exchange接收到Producer发送的消息时，会根据路由规则将消息发送到具体的Queue中。

## 3. 基本应用

RabbitMQ支持多种语言的客户端，在这个章节中将使用Java客户端来操作RabbitMQ。新建Maven项目并添加依赖。

~~~xml
<dependency>
      <groupId>com.rabbitmq</groupId>
      <artifactId>amqp-client</artifactId>
      <version>5.7.0</version>
</dependency>
~~~

### 3.1 Queue

使用Queue是最简单的一种方式，消息不会经过Exchange。消息生产者（P）只需要把消息发送到Queue，消息消费者（C）从Queue中取出消费即可。

![image-20200312095942310](/Users/wangl/Library/Application Support/typora-user-images/image-20200312095942310.png)

Producer示例：

~~~java
public class Producer {

    /**
     * 消息队列名称
     */
    private static final String QUEUE_NAME = "test_queue";

    public static void main(String[] args) {
        //创建连接工厂并设置RabbitMQ主机地址，默认端口为5672
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("localhost");
        //创建连接对象，并使用连接对象构建一个消息通信的通道
        try (Connection conn = connectionFactory.newConnection();
             //使用连接对象构建一个消息通信的通道
             Channel channel = conn.createChannel()) {
            /**
             * 创建队列
             * 参数一：队列名称
             * 参数二：队列是否持久化（true为持久化）
             * 参数三：是否排他（true为排他），排他性指的是当exclusive为true时，
             *        队列只对首次创建的connection是可见的，false则表示被所有创建的connection都可见
             * 参数四：如果设置为true，表示连接断开时会自动删除此队列
             * 参数五：队列的其他属性设置，一个map集合
             */
             channel.queueDeclare(QUEUE_NAME, false, false, false, null);
             String message = "hello world";
            /**
             * 发布消息
             * 参数一：交换机名称，这里未使用任何交换机，设置为""串
             * 参数二：队列名称
             * 参数三：消息路由头的其他属性，这里未添添加任何属性，设置为null
             * 参数四：消息体，将其转换为字节数组
             */
             channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
~~~

运行Producer，打开web管理界面，在Queues的选项里可以查看到新创建一个名为"test_queue"的队列，并且存有一条发布的消息，如下：

![image-20200309115453628](https://tva1.sinaimg.cn/large/00831rSTgy1gcnjdhl64ij31vi0tstdx.jpg)

注意：队列会在第一次使用时创建，如果之前已经创建则不会再创建。

Cosumer示例：

~~~java
public class Consumer {

    /**
     * 消息队列名称
     */
    private final static String QUEUE_NAME = "test_queue";

    public static void main(String[] argv) throws Exception {
        //创建连接工厂并设置RabbitMQ主机地址，默认端口为5672
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("localhost");
        //创建连接对象，并使用连接对象构建一个消息通信的通道
        Connection connection = connectionFactory.newConnection();
        //创建通信通道
        Channel channel = connection.createChannel();
        //创建队列
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);
        //接收消息时所需的回调接口
        DeliverCallback callback = (consumerTag, delivery) -> {
            //获取消息体
            String message = new String(delivery.getBody(), "UTF-8");
            System.out.println("ConsumerTag：" + consumerTag);
        };
        //接收消息
        /**
         * 接收消息
         * 参数一：队列名称
         * 参数二：是否自动签收（true为自动签收），自动签收就是
         *        消息处理完后会自动给rabbitmq回馈一条消息，表示这条消息已经处理完毕
         * 参数三：消息的回调接口，也就是上面声明的DeliverCallback，用于接收消息体
         * 参数四：消费者取消订阅时的回调接口,会传入一个consumerTag签收标签
         */
        channel.basicConsume(QUEUE_NAME, true, callback, consumerTag -> {});

    }
}
~~~

注意：Consumer在创建Connection时不要放在try-with-resources语句块中，避免Connection自动关闭导致程序结束。因为Consumer运行后会产生阻塞，需要一直监听队列是否有新的消息，如果有则从队列取出并消费。

运行Consumer，在控制台查看接收的消息。

![image-20200309121439274](https://tva1.sinaimg.cn/large/00831rSTgy1gcnjy0fqcaj32da0ckq5x.jpg)

再次查看web管理控制台，此时队列的消息已经被消费掉。

![image-20200309121622943](https://tva1.sinaimg.cn/large/00831rSTgy1gcnjzsxt97j31yw0ti79m.jpg)

大家可以反复运行Producer进行测试。

### 3.2 Exchange

前面的例子主要是讲解Queue的用法，并没有涉及到Exchange（交换机）的概念。而在这个章节中我们主要来了解Exchange的用法，Exchange的概念在前面的AMQP的通信模型中已经介绍过，它主要是根据路由key将转发消息到绑定的队列（Queue）上。

![image-20200312100026587](https://tva1.sinaimg.cn/large/00831rSTgy1gcqwxa8fdgj31e608u0tr.jpg)

Exchange的类型有Topic、Direct、Fanout、Headers这四种。而Headers类型的交换机使用场景较少，我们主要学习Topic、Direct、Fanout这几种交换机的用法。

备注：示例中创建一个Exchange和两个不同的Queue来演示不同类型交换机的功能。

#### 3.2.1 Topic

作用：将消息中的`Routing key`与该`Exchange`关联的所有`Binding`中的`Routing key`进行比较，如果匹配（可以通过通配符进行模糊匹配），则发送到该`Binding`对应的`Queue`中。 

CusumerA示例：

~~~java
public class ConsumerA {

    /**
     * 定义Exchange名称
     */
    private final static String EXCHANGE_NAME = "logs.topic";
    /**
     * 定义一个Queue名称，这里指定为info.queue
     */
    private final static String QUEUE_NAME = "info.queue";

    public static void main(String[] args) throws Exception {
        //初始化连接工厂，并指定rabbitmq的主机地址, 默认端口为5672
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        //创建连接对象，并使用连接对象构建一个消息通信的通道
        Connection conn = factory.newConnection();
        Channel channel = conn.createChannel();
        //声明Exchange，类型指定为为topic
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);
        //声明queue
        channel.queueDeclare(QUEUE_NAME, false, false, true, null);
        //为queue和exchange绑定路由key（使用"*"进行模糊绑定），表示任意以".info"结尾的key
        //的消息都会发送到这个queue中
        channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "*.info");
        //消息回调接口
        DeliverCallback callback = (consumerTag, delivery) -> {
            //获取路由key
            System.out.println("Routing key: " + delivery.getEnvelope().getRoutingKey());
            //获取消息体
            String message = new String(delivery.getBody(), "UTF-8");
            System.out.println("ConsumerA receive message: " + message);
        };
        //接收消息
        channel.basicConsume(QUEUE_NAME, true, callback, consumerTag -> {});
    }
~~~

CusomerB示例：

~~~java
public class ConsumerB {

    /**
     * 定义Exchange名称
     */
    private final static String EXCHANGE_NAME = "logs.topic";
    /**
     * 定义一个Queue名称，这里指定为error.queue
     */
    private final static String QUEUE_NAME = "error.queue";

    public static void main(String[] args) throws Exception {
        //初始化连接工厂，并指定rabbitmq的主机地址, 默认端口为5672
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        //创建连接对象，并使用连接对象构建一个消息通信的通道
        Connection conn = factory.newConnection();
        Channel channel = conn.createChannel();
        //声明交换机，类型为topic
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);
        //声明queue
        channel.queueDeclare(QUEUE_NAME, false, false, true, null);
        //为queue和exchange绑定路由key（使用"*"进行模糊绑定），表示任意以".error"结尾的key
        //的消息都会发送到这个queue中
        channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "*.error");
        //接收消息
        DeliverCallback callback = (consumerTag, delivery) -> {
            //获取路由key
            System.out.println("Routing key: " + delivery.getEnvelope().getRoutingKey());
            //获取消息体
            String message = new String(delivery.getBody(), "UTF-8");
            System.out.println("ConsumerB receive message: " + message);
        };
        channel.basicConsume(QUEUE_NAME, true, callback, consumerTag -> {});
    }
}
~~~

分别运行ConsumerA和ConsumerB，打开web管理页面，在Exchanges的页面中我们可以看到创建了一个名为logs.exchange，类型为topic的Exchange。

![image-20200310104116652](https://tva1.sinaimg.cn/large/00831rSTgy1gcomv5u4xkj30x30lgdit.jpg)

在Queues的页面中可以看到创建了error.queue和info.queue两个queue。

![image-20200310104153025](https://tva1.sinaimg.cn/large/00831rSTgy1gcomvs9y64j30y70fldhw.jpg)

在Exchanges页面的列表中点击logs.topic我们创建的这个exchange，可以查看Exchange和queue的绑定信息，以及路由的key。

![image-20200311134703000](https://tva1.sinaimg.cn/large/00831rSTgy1gcpxuslf99j31ie0j8gnm.jpg)

同样在Queues页面的的列表中点击error.queue或者info.queue，也可以查看相互绑定的信息。

![image-20200311134821405](https://tva1.sinaimg.cn/large/00831rSTgy1gcpxw4jklwj31kc0hstan.jpg)

Producer示例：

~~~java
public class Producer {

    /**
     * Exchange名称
     */
    private final static String EXCHANGE_NAME = "logs.topic";

    public static void main(String[] args) throws Exception {
        //初始化连接工厂，并指定rabbitmq的主机地址，默认端口为5672
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        //创建连接对象，并使用连接对象构建一个消息通信的通道
        try(Connection conn = factory.newConnection();
            Channel channel = conn.createChannel()) {
            //创建交换机，类型为topic
            channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC);
            //定义一个info的message
            String infoMessage = "info message...";
            //定义一个error的message
            String errorMessage = "error message...";
            //将消息发送到交换机，并指定不同路由key
            channel.basicPublish(EXCHANGE_NAME, "log.info", null, infoMessage.getBytes());
            channel.basicPublish(EXCHANGE_NAME, "log.error", null, errorMessage.getBytes());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

运行Producer，将两条消息发送到Exchange，此时Exchange会根据消息中指定的路由key将消息不同的消息发送到不同的Queue中。

结果：

![image-20200310104708120](https://tva1.sinaimg.cn/large/00831rSTgy1gcon18ywz7j30ov02fjre.jpg)

![image-20200310104757126](https://tva1.sinaimg.cn/large/00831rSTgy1gcon23dvjij30om02eaa2.jpg)

#### 3.2.2 Direct

作用：将消息中的`Routing key`与该`Exchange`关联的所有`Binding`中的`Routing key`进行比较，如果完全匹配（注意：是完全匹配），则发送到该`Binding`对应的`Queue`中。

ConsumerA示例：

~~~java
public class ConsumerA {

    /**
     * Exchange名称
     */
    private final static String EXCHANGE_NAME = "logs.direct";
    /**
     * Queue名称
     */
    private final static String QUEUE_NAME = "info.queue";

    public static void main(String[] args) throws Exception {
        //初始化连接工厂，并指定rabbitmq的主机地址, 默认端口为5672
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        //创建连接对象，并使用连接对象构建一个消息通信的通道
        Connection conn = factory.newConnection();
        Channel channel = conn.createChannel();
        //声明Exchange，类型为direct
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);
        //声明queue
        channel.queueDeclare(QUEUE_NAME, false, false, true, null);
        //为queue和exchange绑定路由key，这里不能使用模糊匹配，direct类型要求路由的key必须完全匹配
        channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "log.info");
        //接收消息
        DeliverCallback callback = (consumerTag, delivery) -> {
            System.out.println("Routing key: " + delivery.getEnvelope().getRoutingKey());
            String message = new String(delivery.getBody(), "UTF-8");
            System.out.println("ConsumerA receive message: " + message);
        };
        channel.basicConsume(QUEUE_NAME, true, callback, consumerTag -> {});
    }
}
~~~

ConsumerB示例：

~~~java
public class ConsumerB {

    /**
     * 定义Exchange名称
     */
    private final static String EXCHANGE_NAME = "logs.direct";
    /**
     * 定义一个Queue名称，这里指定为error.queue
     */
    private final static String QUEUE_NAME = "error.queue";

    public static void main(String[] args) throws Exception {
        //初始化连接工厂，并指定rabbitmq的主机地址, 默认端口为5672
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        //创建连接对象，并使用连接对象构建一个消息通信的通道
        Connection conn = factory.newConnection();
        Channel channel = conn.createChannel();
        //创建交换机，类型为direct
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);
        //创建queue
        channel.queueDeclare(QUEUE_NAME, false, false, true, null);
        //为queue和exchange绑定路由key，这里不能使用模糊匹配，direct类型要求路由的key必须完全匹配
        channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "log.error");
        //接收消息
        DeliverCallback callback = (consumerTag, delivery) -> {
            System.out.println("Routing key: " + delivery.getEnvelope().getRoutingKey());
            String message = new String(delivery.getBody(), "UTF-8");
            System.out.println("ConsumerB receive message: " + message);
        };
        channel.basicConsume(QUEUE_NAME, true, callback, consumerTag -> {});
    }
}
~~~

Producer示例：

~~~java
public class Producer {

    /**
     * Exchange名称
     */
    private final static String EXCHANGE_NAME = "logs.direct";

    public static void main(String[] args) throws Exception {
        //初始化连接工厂，并指定rabbitmq的主机地址，默认端口为5672
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        //创建连接对象，并使用连接对象构建一个消息通信的通道
        try(Connection conn = factory.newConnection();
            Channel channel = conn.createChannel()) {
            //创建交换机，类型为direct
            channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT);
            //定义一个info的message
            String infoMessage = "info message...";
            //定义一个error的message
            String errorMessage = "error message...";
            //将消息发送到交换机，并指定不同路由key
            channel.basicPublish(EXCHANGE_NAME, "log.info", null, infoMessage.getBytes());
            channel.basicPublish(EXCHANGE_NAME, "log.error", null, errorMessage.getBytes());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

运行ConsumerA，ConsumerB以及Producer

结果：

![image-20200310112752689](https://tva1.sinaimg.cn/large/00831rSTgy1gcoo7mxsx4j30n602jglm.jpg)

![image-20200310112812799](https://tva1.sinaimg.cn/large/00831rSTgy1gcoo7zhv01j30ob02cdfu.jpg)

#### 3.2.3 Fanout

说明：直接将消息转发到所有`binding`的对应`queue`中，这种`exchange`在路由转发的时候，忽略`Routing key`，直接将消息发送到所有绑定的queue中。

ConsumerA示例：

~~~java
public class ConsumerA {

    /**
     * Exchange名称
     */
    private final static String EXCHANGE_NAME = "logs.fanout";
    /**
     * Queue名称
     */
    private final static String QUEUE_NAME = "info.queue";

    public static void main(String[] args) throws Exception {
        //初始化连接工厂，并指定rabbitmq的主机地址, 默认端口为5672
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        //创建连接对象，并使用连接对象构建一个消息通信的通道
        Connection conn = factory.newConnection();
        Channel channel = conn.createChannel();
        //声明Exchange，类型为fanout
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.FANOUT);
        //声明queue
        channel.queueDeclare(QUEUE_NAME, false, false, true, null);
        //为queue和exchange绑定路由key，这里将路由key可设置为任意字符，通常设置为""
        channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "aa");
        //接收消息
        DeliverCallback callback = (consumerTag, delivery) -> {
            String message = new String(delivery.getBody(), "UTF-8");
            System.out.println("ConsumerA receive message: " + message);
        };
        channel.basicConsume(QUEUE_NAME, true, callback, consumerTag -> {});
    }
}
~~~

ConsumerB示例：

~~~java
public class ConsumerB {

    /**
     * 定义Exchange名称
     */
    private final static String EXCHANGE_NAME = "logs.fanout";
    /**
     * 定义一个Queue名称，这里指定为error.queue
     */
    private final static String QUEUE_NAME = "error.queue";

    public static void main(String[] args) throws Exception {
        //初始化连接工厂，并指定rabbitmq的主机地址, 默认端口为5672
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        //创建连接对象，并使用连接对象构建一个消息通信的通道
        Connection conn = factory.newConnection();
        Channel channel = conn.createChannel();
        //声明Exchange，类型为fanout
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.FANOUT);
        //声明queue
        channel.queueDeclare(QUEUE_NAME, false, false, true, null);
        //为queue和exchange绑定路由key，这里将路由key可设置为任意字符，通常设置为""
        channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "bb");
        //接收消息
        DeliverCallback callback = (consumerTag, delivery) -> {
            String message = new String(delivery.getBody(), "UTF-8");
            System.out.println("ConsumerB receive message: " + message);
        };
        channel.basicConsume(QUEUE_NAME, true, callback, consumerTag -> {});
    }
}
~~~

Producer示例：

~~~java
public class Producer {

    /**
     * Exchange名称
     */
    private final static String EXCHANGE_NAME = "logs.fanout";

    public static void main(String[] args) throws Exception {
        //初始化连接工厂，并指定rabbitmq的主机地址，默认端口为5672
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        //创建连接对象，并使用连接对象构建一个消息通信的通道
        try(Connection conn = factory.newConnection();
            Channel channel = conn.createChannel()) {
            //创建交换机，类型为fanout
            channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.FANOUT);
            //定义一个info的message
            String infoMessage = "info message...";
            //定义一个error的message
            String errorMessage = "error message...";
            //将消息发送到交换机，路由key可任意设置，通常设置为""
            channel.basicPublish(EXCHANGE_NAME, "", null, infoMessage.getBytes());
            channel.basicPublish(EXCHANGE_NAME, "", null, errorMessage.getBytes());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
~~~

运行ConsumerA，ConsumerB以及Producer

结果:

![image-20200310115435931](https://tva1.sinaimg.cn/large/00831rSTgy1gcoozfz4d6j30mg02e74b.jpg)

![image-20200310115453760](https://tva1.sinaimg.cn/large/00831rSTgy1gcoozr4v5pj30ms02haa3.jpg)

ConsumerA和ConsumerB同时都收到info和error的消息。

## 4. 整合Spring Boot

### 4.1 示例

**添加依赖：**

~~~xml
<dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
~~~

**yml配置：**

~~~yml
spring:
  rabbitmq:
    addresses: 127.0.0.1
    # 连接端口,默认5672
    port: 5672
    # 设置登陆认证的账号密码，默认为guest
    username: guest
    password: guest
    # 虚拟主机地址，默认为"/"
    virtual-host: /
    # 设置连接诶超时时间
    connection-timeout: 5000
    # 配置消费者监听设置
    listener:
      simple:
        # 最小消息消费线程数
        concurrency: 2
        # 最大消息消费线程数
        max-concurrency: 5
        # 限流，每个消费线程能从队列获取的消息数量
        prefetch: 1
        # 消息签收模式，消息被消费后会回馈一条确认信息给rabbitmq
        # 可以设置为自动或者手动签收，这里设置为手动
        acknowledge-mode: manual
~~~

更多属性配置可以参阅官方文档

> 参考：https://docs.spring.io/spring-boot/docs/2.2.5.RELEASE/reference/html/appendix-application-properties.html#integration-properties

**配置类**

在配置类中主要声明Exchange、Queue等Bean的装配

~~~java
@Configuration
public class RabbitConfig {

    public static final String EXCHANGE_NAME = "order.exchange";
    public static final String QUEUE_NAME = "order.queue";
    public static final String ROUTER_KEY = "order.*";

    /**
     * 装配Topic类型的Exchange
     * 也可以装配其他类型如：DirectExchange、FanoutExchange
     */
    @Bean
    public TopicExchange exchange(){
        return new TopicExchange(EXCHANGE_NAME);
    }

    /**
     * 装配消息队列
     * Queue构造方法第一个参数指定Queue的名称，第二个参数表示是否持久化消息
     * @return
     */
    @Bean
    public Queue queue() {
        return new Queue(QUEUE_NAME, true);
    }

    /**
     * 将queue绑定到exchange
     */
    @Bean
    public Binding binding(){
        return BindingBuilder.bind(queue()).to(exchange()).with(ROUTER_KEY);
    }
}
~~~

**Consumer示例：**

使用@RabbitListener注解标注在方法上用于监听指定的Queue。

~~~java
@Service
public class ConsumerService {

    /**
     * 使用自定义消息转换器
     * 使用@RabbitListener注解进行监听，通过queues属性指定要从哪个queue中消费消息
     * @Payload注解标注的参数为转换后的消息对象
     * @Headers注解标注的参数为消息头
     * @param message 消息体内容
     * @param headers 消息头
     * @param channel 消息通道
     */
    @RabbitListener(queues = RabbitConfig.QUEUE_NAME)
    public void receiveMessage(@Payload String message,
                               @Headers Map<String, Object> headers,
                               Channel channel) throws IOException {
        System.out.println("接收消息：" + message);
        //当手动确认签收时，需要自行给rabbitmq回馈一条消息，这条消息已经处理完毕
        //从headers获取一个签收标签
        Long deliveryTag = (Long) headers.get(AmqpHeaders.DELIVERY_TAG);
        //确认签收，basicAck方法参入一个签收标签，第二个参数表示是否支持批量签收，false表示单个签收
        channel.basicAck(deliveryTag, false);
    }
}
~~~

**Producer示例：**

注入RabbitTemplate用于发送消息。

~~~java
@Service
public class ProducerService {

    /**
     * 注入RabbitTemplate
     */
    @Autowired
    private RabbitTemplate rabbitTemplate;

    /**
     * 发送文本消息
     * @param message
     */
    public void sendMessage(String message){
        //创建消息的唯一ID
        CorrelationData correlationData = new CorrelationData();
        //这里使用订单ID作为消息的ID
        correlationData.setId(UUID.randomUUID().toString());
        //发送消息
        rabbitTemplate.convertAndSend(RabbitConfig.EXCHANGE_NAME, "order.message", message, correlationData);
    }
}
~~~

**测试：**

编写单元测试，注入ProducerService来s发送消息。

~~~java
@SpringBootTest
class Ch04ApplicationTests {

    @Autowired
    private ProducerService service;

    @Test
    public void testSendMessage() {
        service.sendMessage("Hello world");
    }

}
~~~

先运行SpringBoot启动类，然后执行单元测试，查看ConsumerService的接收结果。

![image-20200311152121161](https://tva1.sinaimg.cn/large/00831rSTgy1gcq0kvpupwj313j07cq4q.jpg)

### 4.2 自定义消息转换器

Spring默认使用的消息转换器是SimpleMessageConverter，只能处理基于文本的内容，序列化的Java对象和字节数组。

![image-20200311152537219](https://tva1.sinaimg.cn/large/00831rSTgy1gcq0pbr058j31vy09o41a.jpg)

当然也可以自定义MessageConverter，例如将发送的一个实体把它序列化成Json，接收时又将Json自动转换为一个实体，那么可以使用Jackson2JsonMessageConverter。

**添加依赖：**

转换Json时需要用到Jackson

~~~xml
<dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-json</artifactId>
</dependency>
~~~

**配置类：**

只需在配置类中添加Jackson2JsonMessageConverter的装配

~~~java
@Configuration
public class RabbitConfig {	
    ...
    @Bean
    public MessageConverter messageConverter(){
        return new Jackson2JsonMessageConverter();
    }
}
~~~

**Order示例：**

用于Producer将一个Order序列化为Json后发送到MQ，Consumer从MQ接收Json后将其反序列化为一个Order对象。

~~~java
public class Order {
    /**
     * 订单ID
     */
    private String orderId;
    /**
     * 订单消息
     */
    private String message;

    public String getOrderId() {
        return orderId;
    }

    public void setOrderId(String orderId) {
        this.orderId = orderId;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}
~~~

**Producer示例：**

~~~java
@Service
public class ProducerService {

    /**
     * 注入RabbitTemplate
     */
    @Autowired
    private RabbitTemplate rabbitTemplate;

    /**
     * 发送对象，使用自定义消息转换器转换为json
     * @param order
     */
    public void sendObject(Order order) {
        //创建消息的唯一ID
        CorrelationData correlationData = new CorrelationData();
        //这里使用订单ID作为消息的ID
        correlationData.setId(order.getOrderId());
        //发送消息
        rabbitTemplate.convertAndSend(RabbitConfig.EXCHANGE_NAME, "order.message", order, 		correlationData);
    }
}
~~~

**Consumer示例：**

~~~java
@Service
public class ConsumerService {

    /**
     * 使用自定义消息转换器
     * 使用@RabbitListener注解进行监听，通过queues属性指定要从哪个queue中消费消息
     * @Payload注解标注的参数为转换后的消息对象
     * @Headers注解标注的参数为消息头
     * @param order 转换后的消息对象
     * @param headers 消息头
     * @param channel 消息通道
     */
    @RabbitListener(queues = RabbitConfig.QUEUE_NAME)
    public void receiveObject(@Payload Order order,
                              @Headers Map<String, Object> headers,
                              Channel channel) throws IOException {
        System.out.println("接收消息：");
        System.out.println("订单编号：" + order.getOrderId());
        System.out.println("订单明细：" + order.getMessage());
        //当手动确认签收时，需要自行给rabbitmq回馈一条消息，这条消息已经处理完毕
        //从headers获取一个签收标签
        Long deliveryTag = (Long) headers.get(AmqpHeaders.DELIVERY_TAG);
        //确认签收，basicAck方法参入一个签收标签，第二个参数表示是否支持批量签收，false表示单个签收
        channel.basicAck(deliveryTag, false);
    }
}
~~~

**测试：**

编写单元测试方法

~~~java
@Test
public void testSendObject() {
    Order orderDTO = new Order();
    orderDTO.setOrderId("10001");
    orderDTO.setMessage("test order...");
    service.sendObject(orderDTO);
}
~~~

先运行SpringBoot启动类，执行单元测试并查看Consumer接收结果：

![image-20200311154651697](https://tva1.sinaimg.cn/large/00831rSTgy1gcq1bfbkopj312y08wmz6.jpg)

## 5. 延迟消费

所谓延迟消费，就是放在该队列里的消息不会立即消费，而是等待一段时间之后取出消费。以下场景中都适合使用延迟消费。

- 在电商中，用户下单后并没有立即支付，如果在指定的时间内未支付，则取消该订单

- 在系统发布一个通告，在某时刻之后通知到指定的人

### 5.1 实现方式

Rabbitmq实现延时队列通常有两种形式：

1. 利用自身Time To Live（TTL)以及Dead Letter Exchanges（DLX）的特性实现
2. 利用Rabbitmq插件rabbitmq_delayed_message_exchange

以下案例中将使用插件的方式实现消息的延迟消费，实现起来相对简单。但需要注意的是，插件有一个最大延迟时间，延时时长最长为2^32-1毫秒, 大约49天。

**官方说明：**

For each message that crosses an `"x-delayed-message"` exchange, the plugin will try to determine if the message has to be expired by making sure the delay is within range, ie: `Delay > 0, Delay =< ?ERL_MAX_T` (In Erlang a timer can be set up to (2^32)-1 milliseconds in the future).

### 5.2 安装插件

在官网https://www.rabbitmq.com/community-plugins.html下载延迟消息插件。

![image-20200206155431017](https://tva1.sinaimg.cn/large/006tNbRwgy1gbmqhft8y1j30wp06m0tn.jpg)

注意对应rabbitmq版本，下载后将插件拷贝到rabbitmq的plugins目录，拷贝后在终端使用以下命令可以看插件列表

~~~
rabbitmq-plugins list
~~~

![image-20200311224623570](https://tva1.sinaimg.cn/large/00831rSTgy1gcqdfzccbxj314h0u0jyi.jpg)

**启用插件：**

在终端使用以下命令启用延迟插件。

~~~
rabbitmq-plugins enable rabbitmq_delayed_message_exchange
~~~

![image-20200311224756476](https://tva1.sinaimg.cn/large/00831rSTgy1gcqdhjv0vnj319a0fm40v.jpg)

启用插件后重启RabbitMQ服务。

### 5.4 示例

这里以用户下单后未支持的场景为例，如果在指定的时间内未支付，则取消该订单。

**创建订单表：**

~~~sql
create table order_info(
	order_id varchar(50) primary key,
	order_status tinyint(1) not null, -- 0:取消订单 1:未支付 2:已支付
	order_message varchar(100)
);
~~~

**添加依赖：**

~~~xml
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>

<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-json</artifactId>
</dependency>

<dependency>
     <groupId>org.mybatis.spring.boot</groupId>
     <artifactId>mybatis-spring-boot-starter</artifactId>
     <version>2.0.0</version>
</dependency>

<dependency>
     <groupId>com.alibaba</groupId>
     <artifactId>druid-spring-boot-starter</artifactId>
     <version>1.1.10</version>
</dependency>

<dependency>
     <groupId>mysql</groupId>
     <artifactId>mysql-connector-java</artifactId>
</dependency>

<dependency>
     <groupId>org.projectlombok</groupId>
     <artifactId>lombok</artifactId>
</dependency>

<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-test</artifactId>
     <scope>test</scope>
     <exclusions>
          <exclusion>
              <groupId>org.junit.vintage</groupId>
              <artifactId>junit-vintage-engine</artifactId>
          </exclusion>
     </exclusions>
</dependency>
~~~

**yml配置：**

~~~yaml
spring:
  # 数据源配置
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/order?serverTimezone=GMT&useUnicode=true&characterEncoding=utf-8
    username: root
    password: root
    # 配置druid连接池
    type: com.alibaba.druid.pool.DruidDataSource
    druid:
      maxActive: 1000
      initialSize: 10
      maxWait: 1000
      minIdle: 10
      timeBetweenEvictionRunsMillis: 60000
      minEvictableIdleTimeMillis: 300000
      validationQuery: select 1
      testWhileIdle: true
      testOnBorrow: false
      testOnReturn: false
      maxPoolPreparedStatementPerConnectionSize: 0
  # 配置RabbitMQ
  rabbitmq:
    addresses: 127.0.0.1
    # 连接端口,默认5672
    port: 5672
    # 设置登陆认证的账号密码，默认为guest
    username: guest
    password: guest
    # 虚拟主机地址，默认为"/"
    virtual-host: /
    # 设置连接超时时间
    connection-timeout: 5000
    # 配置消费者监听设置
    listener:
      simple:
        # 最小消息消费线程数
        concurrency: 2
        # 最大消息消费线程数
        max-concurrency: 5
        # 限流，每个消费线程能从队列获取的消息数量
        prefetch: 1
        # 消息签收模式，消息被消费后会回馈一条确认信息给rabbitmq
        # 可以设置为自动或者手动签收，这里设置为手动
        acknowledge-mode: manual
# mybatis配置
mybatis:
  type-aliases-package: edu.nf.ch05.entity
  mapper-locations: classpath:/mappers/*.xml
~~~

**配置类：**

~~~java
@Configuration
public class RabbitConfig {

    public static final String EXCHANGE_NAME = "delay.exchange";
    public static final String QUEUE_NAME = "delay.queue";
    public static final String ROUTER_KEY = "order.message";

    /**
     * 自定义Exchange，设置延迟交换机类型为direct，也可以设置为topic等其他类型
     */
    @Bean
    public CustomExchange delayExchange() {
        Map<String, Object> params = new HashMap<>();
        params.put("x-delayed-type", "direct");
        return new CustomExchange(EXCHANGE_NAME, "x-delayed-message", true, false, params);
    }

    /**
     * 装配消息队列
     * Queue构造方法第二个参数表示是否持久化消息
     * @return
     */
    @Bean
    public Queue queue(){
        return new Queue(QUEUE_NAME, true);
    }

    /**
     * 将queue绑定到exchange
     */
    @Bean
    public Binding binding(){
        return BindingBuilder.bind(queue()).to(delayExchange()).with(ROUTER_KEY).noargs();
    }

    /**
     * 自定义消息转换器
     * @return
     */
    @Bean
    public MessageConverter messageConverter(){
        return new Jackson2JsonMessageConverter();
    }
}
~~~

**Order示例：**

~~~java
@Data
public class Order {

    private String orderId;
    private Integer status;
    private String message;

}
~~~

**OrderDao示例：**

~~~java
public interface OrderDao {

    /**
     * 根据ID查询订单信息
     * @param orderId
     * @return
     */
    Order getOrderById(String orderId);

    /**
     * 保存订单信息
     * @param order
     */
    void saveOrder(Order order);

    /**
     * 修改订单
     * @param order
     */
    void updateOrder(Order order);
}
~~~

**Mapper映射配置：**

~~~xml
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="edu.nf.ch05.dao.OrderDao">

    <resultMap id="orderMap" type="order">
        <id property="orderId" column="order_id"/>
        <result property="status" column="order_status"/>
        <result property="message" column="order_message"/>
    </resultMap>

    <select id="getOrderById" parameterType="string" resultMap="orderMap">
        select order_id, order_status, order_message from order_info where order_id = #{orderId}
    </select>

    <insert id="saveOrder" parameterType="order">
        insert into order_info(order_id, order_status, order_message) values(#{orderId}, #{status}, #{message})
    </insert>

    <update id="updateOrder" parameterType="order">
        update order_info set order_status = #{status} where order_id = #{orderId}
    </update>
</mapper>
~~~

**ProducerService示例：**

~~~java
@Service
public class ProducerService {

    /**
     * 注入RabbitTemplate
     */
    @Autowired
    private RabbitTemplate rabbitTemplate;

    /**
     * 注入OrderDao
     */
    @Autowired
    private OrderDao orderDao;

    /**
     * 发送消息
     * @param order 订单对象
     * @param delayTime 延迟消费时长
     */
    public void send(Order order, int delayTime) {
        //创建消息的唯一ID
        CorrelationData correlationData = new CorrelationData();
        correlationData.setId(order.getOrderId());
        //将订单信息入库，此时订单状态1，表示未支付
        orderDao.saveOrder(order);
        //发送消息
        rabbitTemplate.convertAndSend(RabbitConfig.EXCHANGE_NAME, "order.message", order, message -> {
            //设置延迟时间
            message.getMessageProperties().setDelay(delayTime);
            return message;
        }, correlationData);
    }
}
~~~

**ConsumerService示例：**

~~~java
@Service
@Slf4j
public class ConsumerService {

    /**
     * 注入OrderDao
     */
    @Autowired
    private OrderDao orderDao;

    /**
     * 接收消息
     * 这里会延迟接收，也就是在发送端指定的延迟时间后才才进行接收
     */
    @RabbitListener(queues = RabbitConfig.QUEUE_NAME)
    public void receiveMessage(Order order,
                               @Headers Map<String, Object> headers,
                               Channel channel) throws IOException {
        log.info("接收消息,订单编号：" + order.getOrderId());
        //依据订单编号查询数据库，如果订单状态为1则将其更新为0，表示取消订单
        order = orderDao.getOrderById(order.getOrderId());
        if(order.getStatus() == 1){
            order.setStatus(0);
            orderDao.updateOrder(order);
            System.out.println("订单已取消");
        }
        //确认签收
        Long deliveryTag = (Long) headers.get(AmqpHeaders.DELIVERY_TAG);
        channel.basicAck(deliveryTag, false);
    }
}
~~~

**测试：**

运行SpringBoot启动程序：

~~~java
@SpringBootApplication
@MapperScan("edu.nf.ch05.dao")
public class Ch05Application {

    public static void main(String[] args) {
        SpringApplication.run(Ch05Application.class, args);
    }

}
~~~

执行单元测试：

~~~java
@SpringBootTest
class ProducerServiceTests {

    @Autowired
    private ProducerService producerService;

    @Test
    void testSend() {
        Order order = new Order();
        order.setOrderId("100001");
        order.setMessage("test order...");
        order.setStatus(1);
        producerService.send(order, 10000);
    }

}
~~~

查看数据库，测试会录入一条订单信息，其状态为1。

![image-20200312002940022](https://tva1.sinaimg.cn/large/00831rSTgy1gcqgfeg9v9j30x203ajrv.jpg)

如果在指定的过期时间内未其他服务处理该订单，那么消费者会从队列中取出这条订单信息，根据ID去数据库查询该订单的状态，如果为1（未支付）则自动取消订单，将其状态更新为0。

![image-20200312004055436](https://tva1.sinaimg.cn/large/00831rSTgy1gcqgr3sr7hj314i0a7413.jpg)

再次查看这条订单记录，此时的状态已更新为0。

![image-20200312004152269](https://tva1.sinaimg.cn/large/00831rSTgy1gcqgs3ebj1j30vu03ojrx.jpg)

## 6. 案例

> 所有案例：git@github.com:baiczsy/rabbitmq-demo.git

