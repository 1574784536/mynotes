### RabbitMQ七种队列模式应用场景

#### 简单模式



![image-20210613191135777](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210613191135777.png)

做做最简单的事情，一个生产者对用一个消费者，rabbitmq相当于一个消息代理，负责将A的消息转发给B

应用场景：将发送的电子邮件放到消息队列，然后邮件服务器在队列中获取邮件并发送给收件人

#### 工作队列(Work queues)

![image-20210613191349675](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210613191349675.png)

在多个消费者之间分配任务(竞争的消费者模式)，一个生产者对应多个消费者，一般适合用于执行资源密集的任务，单个消费者处理不过来，需要多个消费者进行处理

#### 订阅模式(Publiush/Subscribe)

![image-20210613191609788](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210613191609788.png)

一次性向多个消费者发送消息，一个生产者发送的消息会被多个消费者获取，也就是将消息广播到所有的消费者中

应用场景：更新商品库存后需要通知多个缓存和多个数据库，这里的结构是

一个fanout类型的交换机扇出两个消息队列，分别缓存消息队列，数据库消息队列

一个缓存消息队列对用多个缓存消费者

一个数据库消息队对应多个数据库消费者

#### 路由模式(Routing)

![image-20210613191949972](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210613191949972.png)

有选择的(Routing key)接收消息，发送消息到交换机并且指定路由key，消费者将队列绑定到交换机时指定路由key，仅消费指定路由key的消息

应用场景：如果商品库中增加的1台手机，手机促销活动消费者绑定routing key为手机，只有此活动会收到消息，其他促销活动不关心也不会消费此routing key的消息

#### 主题模式(Topic)

![image-20210613192345779](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210613192345779.png)

根据主题来接收消息，将路由key和某种模式进行匹配，此时队列需要绑定一个模式，#匹配一个或多个词，*只匹配一个词

#### 远程过程调用(RPC)

![image-20210613192524983](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210613192524983.png)

如果我们需要在远程计算机上运算功能并等待结果就可以使用rpc，应用场景：需要等待接口返回数据，如订单支付

#### 发布确认(Publisher Confirms)

与发布者进行可靠的发布确认，发布确认是RabbitMQ扩展，可以实现可靠的发布，在通道上启用发布者确认后，RabbitMQ将异步确认发布者的消息，这意味着它们已在服务端处理