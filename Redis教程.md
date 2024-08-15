# Redis教程

## 1. 什么是NoSql

​        NoSQL一词最早出现于1998年，是Carlo Strozzi开发的一个轻量、开源、不提供SQL功能的关系数据库。2009年，Last.fm的Johan Oskarsson发起了一次关于分布式开源数据库的讨论，来自Rackspace的Eric Evans再次提出了NoSQL的概念，这时的NoSQL主要指非关系型、分布式、不提供ACID的数据库设计模式。它不同于传统的关系数据库，两者存在许多显著的不同点，其中最重要的是NoSQL不使用SQL作为查询语言。其数据存储可以不需要固定的表格模式。

## 2. Redis简介

​        2008年，意大利的一家创业公司Merzia推出了一款基于MySQL的网站实时统计系统LLOOGG，然而没过多久该公司的创始人 Salvatore Sanfilippo便 对MySQL的性能感到失望，于是他决定亲自为LLOOGG量身定做一个数据库，并于2009年开发完成，这个数据库就是Redis。 不过Salvatore Sanfilippo并不满足只将Redis用于LLOOGG这一款产品，而是希望更多的人使用它，于是在同一年Salvatore Sanfilippo将Redis开源发布，并开始和Redis的另一名主要的代码贡献者Pieter Noordhuis一起继续着Redis的开发，直到今天。短短的几年时间，Redis就拥有了庞大的用户群体。Hacker News在2012年发布了一份数据库的使用情况调查，结果显示有近12%的公司在使用Redis。国内如新浪微博、街旁网、知乎网，国外如GitHub、Stack Overflow、Flickr等都是Redis的用户。VMware公司从2010年开始赞助Redis的开发， Salvatore Sanfilippo和Pieter Noordhuis也分别在3月和5月加入VMware，全职开发Redis。

​        Redis是使用c语言开发的一个高性能键值数据库。常用于分布式系统中的缓存、电商秒杀、排行榜、访问量统计、分布式会话共享等高并发应用场景。Redis可以通过一些键值类型来存储数据。其数据类型包括字符类型、哈希类型、列表类型、集合类型、有序集合类型。

## 3. 安装Redis

访问Redis官网https://redis.io/download下载最新的版本 。

![image-20190127091355834](https://ww4.sinaimg.cn/large/006tNc79gy1fzkvil0gbuj317z0u0doe.jpg)

解压并编译安装

~~~
$ tar xzf redis-5.0.3.tar.gz
$ cd redis-5.0.3
$ make install
~~~

Redis官网并没有提供windows版本，但可以前往https://github.com/tporadowski/redis/releases下载windows的个人编译版本（注意：并不是最新的版本）。

![image-20190127091500286](https://ww1.sinaimg.cn/large/006tNc79gy1fzkvjn8c6nj31k00m2n0s.jpg)

## 4. 启动服务

### 4.1 前端启动

在redis的src目录有一个redis-server文件，用于启动一个redis服务。

![image-20190128080006944](https://ww1.sinaimg.cn/large/006tNc79gy1fzlz036d3gj31ej0u0aic.jpg)

redis的默认端口为6379，当客户端需要连接到redis服务时，就通过服务端的IP地址以及这个端口进行连接。也可以修改这个默认端口。在redis的根目录下有一个redis.conf文件，它是redis的核心配置文件，redis的所有配置信息都在此文件中。如果需要修改端口，我们在配置文件中找到port配置，并将6379改为其他的端口号。

![image-20190128081730591](https://ww1.sinaimg.cn/large/006tNc79gy1fzlzi4z0x1j31ng06wwft.jpg)

修改完后需要重新启动redis服务，需要注意的是，在使用redis-server启动服务时需要指定redis.conf文件的绝对路径，否则redis将以默认的配置启动一个服务实例。

![image-20190128082950744](https://ww3.sinaimg.cn/large/006tNc79gy1fzlzuz41srj31eh0u0wm2.jpg)

前端启动的模式我们可以在终端看到redis的启动信息和相关的操作日志，但此时如果关闭了终端或者使用control+c将会立即停止redis服务。

### 4.2 后端启动

所谓后端启动，就是以一个独立的进程来运行一个redis服务。首先修改redis.conf文件，找到daemonize选项并设置为yes，如下图：

![image-20190203082011881](https://ww1.sinaimg.cn/large/006tNc79gy1fzsxatqij9j31sw0cogna.jpg)

保存退出后重新启动redis服务，此时redis将以后台进程的方式启动服务。终端没有显示相关的启动信息，并且启动完成后，终端可以继续执行其他的操作。

![image-20190203082246538](https://ww3.sinaimg.cn/large/006tNc79gy1fzsxdgt7rqj327q0c2jui.jpg)

## 5. 客户端连接

### 5.1 Redis客户端

在redis的src目录下有一个redis-cli命令，这个就是官方提供的redis客户端，可以使用它来连接和操作redis。当然，这仅仅只是一个命令行的客户端程序，在实际的开发中会有不同的平台语言，因此官网也提供了对各种语言的客户端实现，在实际的项目开发中使用不同语言的客户端来操作redis。例如官网提供了一个Java的客户端Jedis。

![image-20190203090500297](https://ww3.sinaimg.cn/large/006tNc79gy1fzsylejgtvj318k0u00yi.jpg)

1）使用redis-cli

可以使用使用官方自带的redis-cli客户端来连接redis服务。参数-h为连接redis服务器的IP地址，-p为redis的端口号。连接完成后就可以对redis进行操作了，我们使用简单的set和get命令来进行存储和访问操作。

![image-20190203084322124](https://ww3.sinaimg.cn/large/006tNc79gy1fzsxyvx4jij31n005idgk.jpg)

2）退出客户端

如果想要退出客户端的连接只需要在连接的状态下输入quit或者exit即可。

![image-20190203084736497](https://ww1.sinaimg.cn/large/006tNc79gy1fzsy3apdajj31uc0ae40b.jpg)

3）身份认证

默认连接Redis时是不需要认证密码的，我们可以为其设置一个连接的认证密码。首先在redis.conf中找到requirepass配置项，取消注释并设置一个密码。

![image-20190213160312170](https://ww4.sinaimg.cn/large/006tNc79gy1g04uvm9rgzj31qm0r0q8b.jpg)

保存后重启服务，在连接客户端时加上-a参数并输入配置的密码。

![image-20190213160856278](https://ww4.sinaimg.cn/large/006tNc79gy1g04v1koyvzj324g06ymym.jpg)

连接时也可以不指定密码也可以正常连接，但在操作Redis时候会提示一个错误，要求输入认证密码。这时使用auth命令来输入密码即可。

![image-20190213161510434](https://ww3.sinaimg.cn/large/006tNc79gy1g04v827qeyj319a09g404.jpg)

如果设置了认证密码，在关闭客户端时也同样需要指定。

![image-20190213163407190](https://ww2.sinaimg.cn/large/006tNc79gy1g04vrs44j8j324q06mdh3.jpg)

### 5.2 可视化客户端

也可以使用第三方的redis的可视化客户端RDM（redis-desktop-manager），它同时提供了各种系统平台的编译版本，安装后即可使用。下载地址：

![image-20190203091654632](https://ww4.sinaimg.cn/large/006tNc79gy1fzsyxsqersj316l0u00yq.jpg)

点击左上角的Connect to Redis Server，在弹出的窗口中填写相关的Name（连接名称）、Address（连接地址）、端口号以及认证密码（Auth），点击OK即可。

![image-20190203091505965](https://ww3.sinaimg.cn/large/006tNc79gy1fzsyvx6n67j316m0u0aks.jpg)

![image-20190203091546340](https://ww4.sinaimg.cn/large/006tNc79gy1fzsywm60urj316m0u0wn5.jpg)

这里我们看到连接redis后默认有16个库（0 ~ 15），这是redis默认的配置，可以在redis.conf中可以找到相应的选项并修改默认数量。

![image-20190203092012415](https://ww2.sinaimg.cn/large/006tNc79gy1fzsz17wubkj31oq08q407.jpg)

当我们使用客户端连接redis时，默认选择的是index为0的数据库，然而也可以使用select命令选择其他数据库。例如选择index为15的数据库，如下操作：

![image-20190203141448968](https://ww4.sinaimg.cn/large/006tNc79gy1fzt7jr4p3pj31wo08ogmj.jpg)

### 5.3. 停止服务

如果使用前端启动redis，可以使用control+c或者kill命令来杀掉进程的方式关闭redis（注意：control+c并不能停止后端启动的redis），但这些方式都是强制性的关闭redis，由于redis保存的数据先会存储在内存，如果此时强制关闭，将导致redis还没将数据持久化到文件中就退出，可能会照成部分的数据丢失。因此，应该使用正常的退出方式来停止redis服务，正常退出redis同样使用redis-cli客户端。

![image-20190203085109054](https://ww1.sinaimg.cn/large/006tNc79gy1fzsy6zm9d5j31ti070js1.jpg)

上面的命令表示关闭本机端口为6379的redis服务。

## 6. 数据类型及常用API

Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及sorted set(zset：有序集合)。

### 6.1 string（字符串）

String 是 redis 最基本的类型，一个 key 对应一个 value。它是二进制安全的，可以包含任何数据，如jpg图片或者序列化的对象。

1）SET

*语法：set key value*

赋值操作。

![image-20190204083836073](https://ww2.sinaimg.cn/large/006tNc79gy1fzu3g8kqp5j318a06m0tg.jpg)

2）GET

*语法：get key*

取值操作。

![image-20190204083932618](https://ww3.sinaimg.cn/large/006tNc79gy1fzu3h7o192j316w06c0tg.jpg)

3）GETSET

*语法：getset key value*

取值并赋值。

![image-20190204084114612](https://ww4.sinaimg.cn/large/006tNc79gy1fzu3izgc94j31m20ac3zx.jpg)

4）MSET

*语法：mset key value [key value ...]*

同时设置多个键值。

![image-20190204084331152](https://ww4.sinaimg.cn/large/006tNc79gy1fzu3lcyfb0j31cc06kwfa.jpg)

5）MGET

*语法：mget key [key ...]*

获取多个键值。

![image-20190204084449235](https://ww3.sinaimg.cn/large/006tNc79gy1fzu3mpp24qj31io07qgmm.jpg)

6）DEL

*语法：del key [key ...]*

删除一个或多个键值对。

![image-20190204084649996](https://ww1.sinaimg.cn/large/006tNc79gy1fzu3ot6uhoj31jo0a4myn.jpg)

7）INCR

*语法：incr key* 

当存储的字符串是整数时，让当前键值递增，并返回递增或增加后的值。

![image-20190204085509956](https://ww2.sinaimg.cn/large/006tNc79gy1fzu3xgy22vj31ac06k0th.jpg)

8）INCRBY

语法：incrby key increment*

当存储的字符串是整数时，让当前键值增加指定的数值，并返回递增或增加后的值。

![image-20190204085627802](https://ww3.sinaimg.cn/large/006tNc79gy1fzu3ytmzurj31bs06gaaw.jpg)

9）DECR

*语法：decr key*

让当前键值递减，并返回递减或减少后的值。

![image-20190204090103737](https://ww1.sinaimg.cn/large/006tNc79gy1fzu43lx0epj319a06oq3q.jpg)

10）DECRBY

*语法：decrby key decrement*

让当前键值减少指定的数值，并返回递减或减少后的值。

![image-20190204090142201](https://ww2.sinaimg.cn/large/006tNc79gy1fzu449s6fij31bc06ejs9.jpg)

11）APPEND

*语法：append key value*

向键值的末尾追加value。如果键不存在则将该键的值设置为value，即相当于 SET
key value。返回值是追加后字符串的总长度。

![image-20190204090432914](https://ww1.sinaimg.cn/large/006tNc79gy1fzu478n20kj31f20diwgi.jpg)

12）获取字符串长度（STRLEN）

STRLEN命令返回键值的长度，如果键不存在则返回0。

![image-20190204090808672](https://ww3.sinaimg.cn/large/006tNc79gy1fzu4azehgoj31aq09wwfu.jpg)

### 6.2 hash（哈希）

hash是一个string类型的field和value的映射表，而field只能是String类型，hash特别适合用于存储对象。

![image-20190204091157360](https://ww4.sinaimg.cn/large/006tNc79gy1fzu4extouoj30ks09m75q.jpg)

1）HSET

*语法：HSET key field value*

HSET一次只能设置一个字段值。HSET命令不区分插入和更新操作，当执行插入操作时HSET命令返回1，当执行更新操作时返回0。

![image-20190204134214619](https://ww3.sinaimg.cn/large/006tNc79gy1fzuc87sql9j31c407g755.jpg)

2）HMSET

*语法：HMSET key field value [field value ...]* 

HMSET和HSET作用一样，只不过一次可以设置多个字段值。

![image-20190204134551593](https://ww3.sinaimg.cn/large/006tNc79gy1fzucbxnwtqj318s06uaau.jpg)

3）HSETNX

*语法：HSETNX key field value*

当字段不存在时赋值，类似HSET。区别在于如果字段存在，该命令不执行任何操作。

例如：hsetnx user name zing 

说明：如果user中不存在name字段则设置name的值为zing，否则不做任何操作。

![image-20190204135606457](https://ww1.sinaimg.cn/large/006tNc79gy1fzucmllsyoj319o07g3zf.jpg)

4）HGET

*语法：HGET key field*

HGET一次只能获取一个字段值。

![image-20190204140214213](https://ww2.sinaimg.cn/large/006tNc79gy1fzucsyzd39j317o06u3z8.jpg)

5）HMGET

*语法：HMGET key field [field ...]*   

HMGET一次可以获取多个字段值。

![image-20190204140321356](https://ww2.sinaimg.cn/large/006tNc79gy1fzucu55fhej318m08kwff.jpg)

6）HGETALL

*语法：HGETALL key*

获取所有字段值。

![image-20190204140544575](https://ww3.sinaimg.cn/large/006tNc79gy1fzucwmjn3dj317i0bsmyf.jpg)

7）HDEL

*语法：HDEL key field [field...]*

可以删除一个或多个字段，返回值是被删除的字段个数。

![image-20190204140809745](https://ww1.sinaimg.cn/large/006tNc79gy1fzucz5dr8mj317k07k3zd.jpg)

8）HINCRBY

*语法：HINCRBY key field increment*

为某个字段增加数值。

![image-20190204141142522](https://ww4.sinaimg.cn/large/006tNc79gy1fzud2tyx2hj318e0didhq.jpg)

9）HEXISTS

*语法：HEXISTS key field*

判断字段是否存在，存在则返回1，否则返回0。

![image-20190204141526465](https://ww2.sinaimg.cn/large/006tNc79gy1fzud6pu07lj315q0a0ta4.jpg)

10）HKEYS

*语法：HKEYS key*

获取所有的字段名。

![image-20190204141719881](https://ww4.sinaimg.cn/large/006tNc79gy1fzud8ojwyuj315m086757.jpg)

11）HVALS

*语法：HVALS key*

获取所有字段的值。

![image-20190204141859820](https://ww2.sinaimg.cn/large/006tNc79gy1fzudaetjx2j3184080js8.jpg)

12）HLEN

*语法：HLEN key*

获取字段数量。

![image-20190204143508898](https://ww1.sinaimg.cn/large/006tNc79gy1fzudr7vcwgj315o06s3z9.jpg)

### 6.3 list（列表）

Redis的list是采用来链表来存储的，所以对于Redis的list数据类型的操作，是操作list的两端数据来操作的。

1）LPUSH

*语法：LPUSH key value [value ...]*

向列表左边添加元素。

![image-20190204150049467](https://ww3.sinaimg.cn/large/006tNc79gy1fzuehxjllcj315u07gdgn.jpg)

2）RPUSH

*语法：RPUSH key value [value ...]*

向列表右边添加元素。

![image-20190204150259434](https://ww2.sinaimg.cn/large/006tNc79gy1fzuek73x8xj316q06saav.jpg)

3）LRANGE

*语法：LRANGE key start stop*

LRANGE命令是列表类型最常用的命令之一，用于获取列表中的某一片段，将返回start到stop之间的所有元素（包含两端的元素），索引从0开始。索引可以是负数，如：-1代表最后边的一个元素。

![image-20190204150640331](https://ww3.sinaimg.cn/large/006tNc79gy1fzueo0seclj31gs0miq56.jpg)

4）LPOP

*语法：LPOP key*

LPOP命令从列表左边弹出一个元素，会分两步完成：第一步是将列表左边的元素从列表中移除。第二步是返回被移除的元素值。

![image-20190204151255741](https://ww1.sinaimg.cn/large/006tNc79gy1fzueuj7ivvj315w068q3j.jpg)

5）RPOP

*语法：RPOP key*

RPOP命令从列表右边弹出一个元素，步骤与LPOP类似，第一步是将列表右边的元素从列表中移除。第二步是返回被移除的元素值。

![image-20190204151330740](https://ww2.sinaimg.cn/large/006tNc79gy1fzuev4tspfj316206edgg.jpg)

6）LLEN

*语法：LLEN key*

获取列表中元素的个数

![image-20190204163548551](https://ww1.sinaimg.cn/large/006tNc79gy1fzuh8ro4yfj318m06owf8.jpg)

7）LREM

*语法：LREM key count value*

LREM命令会删除列表中前count个值为value的元素，返回实际删除的元素个数。根据count值的不同，该命令的执行方式会有所不同： 

当count>0时， LREM会从列表左边开始删除。 

当count<0时， LREM会从列表右边开始删除。 

当count=0时，LREM删除所有值为value的元素。

![image-20190228095106676](https://ww4.sinaimg.cn/large/006tKfTcgy1g0lwf2yolrj31kw0si77h.jpg)

8）LINDEX

*语法：LINDEX key index*

获得指定索引的元素值。

![image-20190204165017260](https://ww1.sinaimg.cn/large/006tNc79gy1fzuhntwii7j317w0bymyi.jpg)

9）LSET

*语法：LSET key index value*

设置指定索引的元素值。

![image-20190204165126605](https://ww4.sinaimg.cn/large/006tNc79gy1fzuhp1dbfej318y0h2mz7.jpg)

10）LTRIM

*语法：LTRIM key start stop*

只保留列表的指定片段

![image-20190204165405908](https://ww1.sinaimg.cn/large/006tNc79gy1fzuhrsqgonj318y0q2q5o.jpg)

11）LINSERT

*语法：LINSERT key BEFORE|AFTER pivot value*

LINSERT首先会在列表中从左到右查找值为pivot的元素，然后根据第二个参数是BEFORE还是AFTER来决定将value插入到该元素的前面还是后面。

![image-20190204165828783](https://ww2.sinaimg.cn/large/006tNc79gy1fzuhwd1fsmj31860mijty.jpg)

12）RPOPLPUSH

*语法：RPOPLPUSH source destination*

将一个列表的最后一个元素转移到另一个列表的最前面

![image-20190204170459521](https://ww2.sinaimg.cn/large/006tNc79gy1fzui35do74j317g0hcjtg.jpg)

### 6.4 set（集合）

Redis 的 Set 是 String 类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据。

1）SADD

*语法：SADD key member [member ...]*

增加一个或多个元素。

![image-20190205085821980](https://ww4.sinaimg.cn/large/006tNc79gy1fzv9n46x68j318e0acq4b.jpg)

2）SREM

*语法：SREM key member [member ...]*

移除一个或多个元素。

![image-20190205090001732](https://ww4.sinaimg.cn/large/006tNc79gy1fzv9ouf1pmj317q09smyi.jpg)

3）SMEMBERS

*语法：SMEMBERS key*

获得集合中的所有元素。

![image-20190205090235673](https://ww4.sinaimg.cn/large/006tNc79gy1fzv9riel2wj318s0d4taa.jpg)

4）SISMEMBER

*语法：SISMEMBER key member*

判断元素是否存在集合中。存在返回1，否则返回0。

![image-20190205090445072](https://ww3.sinaimg.cn/large/006tNc79gy1fzv9tr2pmkj318609sdh9.jpg)

5）SDIFF

*语法：SDIFF key [key ...]*

查找属于集合A并且不属于集合B的元素。（差集运算）

![image-20190205090808028](https://ww1.sinaimg.cn/large/006tNc79gy1fzv9xa4jduj318s0ko772.jpg)

6）SINTER

*语法：SINTER key [key ...]*

查找属于集合A且属于集合B的元素。（交集运算）

![image-20190205091715242](https://ww4.sinaimg.cn/large/006tNc79gy1fzva6rnsytj317w06ejs2.jpg)

7）SUNION

*语法：SUNION key [key ...]*

查找属于集合A或者属于集合B的元素。（合并运算）

![image-20190205092119801](https://ww4.sinaimg.cn/large/006tNc79gy1fzvab0b10uj31880dgq45.jpg)

8）SCARD

*语法：SCARD key*

获取集合中元素的个数。

![image-20190205092312680](https://ww4.sinaimg.cn/large/006tNc79gy1fzvacymhanj317y06m3za.jpg)

9）SPOP

*语法：SPOP key [count]*

从集合中弹出一个或多个元素，由count指定。如果不指定count，默认弹出一个。由于集合是无序的，所有SPOP命令会从集合中随机选择一个元素弹出。

![image-20190205092708046](https://ww3.sinaimg.cn/large/006tNc79gy1fzvah1lqjqj317m08et9j.jpg)

### 6.5 zset（有序集合）

zset又称sorted set，称之为有序集合，可排序的，但是唯一。和set的不同支出在于zet会给集合中的元素添加一个分数，然后通过这个分数进行排序。

1）ZADD

*语法：ZADD key score member [score member ...]*

向有序集合中加入一个或多个元素和该元素的分数，如果该元素已经存在则会用新的分数替换原有的分数。返回值是新加入到集合中的元素个数，不包含之前已经存在的元素。

![image-20190205095703328](https://ww1.sinaimg.cn/large/006tNc79gy1fzvbc6izdwj319q09wgn8.jpg)

2）ZSCORE

*语法：ZSCORE key member*

获取元素的分数。

![image-20190205095948874](https://ww4.sinaimg.cn/large/006tNc79gy1fzvbf1nd26j318q06ut9f.jpg)

3）ZREM

*语法：ZREM key member [member ...]*

移除有序集合中的一个或多个成员，不存在的成员将被忽略。

![image-20190205100315588](https://ww4.sinaimg.cn/large/006tNc79gy1fzvbimtb0fj317u064t9h.jpg)

4）ZRANGE

*语法：ZRANGE key start stop [WITHSCORES]*

按照元素分数从小到大的顺序返回索引从start到stop之间的所有元素（包含两端的元素）。如果需要获得元素的分数可以在命令尾部加上WITHSCORES参数。

![image-20190206001217038](https://ww4.sinaimg.cn/large/006tNc79gy1fzw021equaj31b60himzh.jpg)

5）ZREVRANGE

*语法：ZREVRANGE key start stop [WITHSCORES]* 

按照元素分数从大到小的顺序返回索引从start到stop之间的所有元素（包含两端的元素）。如果需要获得元素的分数的可以在命令尾部加上WITHSCORES参数。

![image-20190206001913448](https://ww1.sinaimg.cn/large/006tNc79gy1fzw099cpmtj31b40heacf.jpg)

6）ZRANK

*语法：ZRANK key member*

获取元素排名（从小到大）。

![image-20190206002508701](https://ww3.sinaimg.cn/large/006tNc79gy1fzw0ff7vqgj318m0a63zz.jpg)

7）ZREVRANK

*语法：ZREVRANK key member*

获取元素排名（从大到小）。

![image-20190206002704642](https://ww1.sinaimg.cn/large/006tNc79gy1fzw0hfegf4j318o09udhc.jpg)

8）ZRANGEBYSCORE

*语法：ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]* 

获得指定分数范围的元素。

![image-20190206003420169](https://ww4.sinaimg.cn/large/006tNc79gy1fzw0ozdgvgj31fi0scq6y.jpg)

9）ZINCRBY

*语法：ZINCRBY  key increment member*

增加某个元素的分数，并返回更改后的分数。

![image-20190206003710077](https://ww4.sinaimg.cn/large/006tNc79gy1fzw0rx5qpsj317u05wwf8.jpg)

10）ZCARD

*语法：ZCARD key*

获取集合元素的数量。

![image-20190206003840231](https://ww1.sinaimg.cn/large/006tNc79gy1fzw0thu6msj316a060mxx.jpg)

11）ZCOUNT

*语法：ZCOUNT key min max*

获取指定分数范围内的元素个数。

![image-20190206004026075](https://ww4.sinaimg.cn/large/006tNc79gy1fzw0vbx0jvj317o06owfb.jpg)

12）ZREMRANGEBYRANK

*语法：ZREMRANGEBYRANK key start stop*

按照排名范围删除元素。

![image-20190206004748350](https://ww4.sinaimg.cn/large/006tNc79gy1fzw12zmyy8j317q09qq4h.jpg)

13）ZREMRANGEBYSCORE

*语法：ZREMRANGEBYSCORE key min max*

按照分数范围删除元素。

![image-20190206005026496](https://ww4.sinaimg.cn/large/006tNc79gy1fzw15qkpg6j319g0dq0v5.jpg)

## 7. Redis键（Keys）

Redis键是二进制安全的，这意味着你可以使用任何二进制序列作为键，从像”foo” 这样的字符串到一个 JPEG文件的内容。空字符串也是合法的键。

### 7.1 键的一些设计规则

* 不要使用太长的键。例如，不要使用一个1024字节的键，不仅是因为占用内存，而且在数据集中查找key时需要多次耗时的key比较。

* 不要使用太短的key。例如，user:1001比u1001更具有实际意义，相对于key本身以及value对象来说，增加的空间微乎其微。当然，短的键会消耗少的内存，需要找到平衡点。

* 规范一种模式 (schema)。用冒号或者下横线来连接多单词字段，例如：”user:1000”或者"user_1000"。

### 7.2 Key的常用API

1）KEYS

*语法：keys pattern*

返回指定pattern的所有key

![image-20190206005958619](https://ww3.sinaimg.cn/large/006tNc79gy1fzw1fnux8pj32560q8tdi.jpg)

2）EXISTS

*语法：exists key*

判断一个key是否存在。存在返回后1，否则返回0。

![image-20190206011145014](https://ww3.sinaimg.cn/large/006tNc79gy1fzw1rwtswdj317o09s75t.jpg)

3）RENAME

*语法：rename key newkey*

重命名key

![image-20190206011502678](https://ww1.sinaimg.cn/large/006tNc79gy1fzw1vc03afj318u05w0th.jpg)

4）TYPE

*语法：type key*

根据key返回value的类型。

![image-20190206011729392](https://ww2.sinaimg.cn/large/006tNc79gy1fzw1xvr97nj319206cmxz.jpg)

5）EXPIRE

*语法：expire key seconds*

设置key的生存时间。Redis的数据是缓存在内存中的，然后很多时候数据一般都会设置一个过期时间（即到期后销毁数据，从而释放更多的内存）。过期时间默认以秒为单位，默认值为-1，表示永不过期。

![image-20190206012511668](https://ww4.sinaimg.cn/large/006tNc79gy1fzw25w67ntj319e060wfd.jpg)

也可以在设值的时候指定过期时间（秒）

![image-20190301093522458](https://ww4.sinaimg.cn/large/006tKfTcgy1g0n1l1tahdj30yc07gdgv.jpg)

6）TTL

*语法：ttl key*

查看key剩余的过期时间。

![image-20190206012555966](https://ww1.sinaimg.cn/large/006tNc79gy1fzw26o6fhfj318w068756.jpg)

7）PERSIST

*语法：persist key*

清除key的过期时间。

![image-20190206012956090](https://ww1.sinaimg.cn/large/006tNc79gy1fzw2au388hj319i09udhd.jpg)

8）PEXPIRE

*语法：pexpire key*

以毫秒为单位设置key的过期时间。

![image-20190206013625664](https://ww1.sinaimg.cn/large/006tNc79gy1fzw2hl33yyj318o09oabj.jpg)

也可以在设值的时候指定过期的时间（毫秒）

![image-20190301093643502](https://ww4.sinaimg.cn/large/006tKfTcgy1g0n1mf2f3ij312407ewfi.jpg)

## 8. 持久化

### 8.1 简介

Redis是一个支持持久化的内存数据库，可以将内存中的数据同步到磁盘保证持久化。我们知道Redis会将数据缓存在内存中，如果没有持久化，在服务器关闭或重启之后数据会丢失。为了保证数据的安全以及效率，Redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件。而Redis提供了RDB和AOF两种持久化策略。

### 8.2 RDB

Redis默认是会以快照RDB的形式将数据持久化到磁盘的一个dump.rdb二进 制文件。当Redis决定要持久化时，会 fork 一个子进程将数据写到磁盘上一个临时的RDB文件中，当子进程完成写操作后，将原来的RDB替换掉。而Redis会在满足某些条件后会进行持久化，并且可以对其进行配置。

**配置RDB**

在redis.conf文件中找到“Save the DB on disk”的配置，我们可以根据需要来修改这Redis的RDB持久化策略。

![image-20190206101801079](https://ww3.sinaimg.cn/large/006tNc79gy1fzwhkcfu4yj31990u0dnb.jpg)

说明：

save 900 1（如果在900秒之内有1次操作，则执行快照保存）

save 300 10（如果在300秒内有10次操作，则执行快照保存）

save 60 10000（如果在1分钟之内有10000个次操作，则执行快照保存）

**SAVE和BGSAVE**

我们可以在客户端直接使用SAVE或者BGSAVE命令立即将Redis的数据持久化到RDB文件中。他们两者的区别在于BGSAVE命令会fork一个子进程在后台进行持久化，主进程可以继续处理客户端发送的命令（非阻塞）。而SAVE命令需要等待Redis持久化完成后才可以继续处理客户端发送的命令（阻塞）。

![image-20190208093203333](https://ww1.sinaimg.cn/large/006tNc79gy1fzyrh2zzrvj319w09wgn1.jpg)

**RDB优点 **

RDB非常适合用于数据备份， 可以在当天内每小时备份一次，或者每个月的每天都进行备份。 如果遇到断电或者宕机等其他一些灾难情况，可以随时将数据集还原。

**RDB缺点**

如果对数据的完整性和安全性要求非常高，要求每一次的操作数据都能持久化到文件中，这时RDB就不太适合了。因为RDB是按照时间范围的操作次数为条件促发持久化，如果未满足这些触发条件，Redis并不会将数据保存到文件，导致数据丢失。例如：save 60 10000，如果在1分钟之内有9000次的操作，如果此时服务器异常退出或宕机，由于未满足条件，将导致丢失这1分钟的数据。

### 8.3 AOF

AOF持久化可以记录每个写操作，将Redis执行过的所有写指令（读操作不记录）保存到appendonly.aof文件中，并且只允许追加文件而不可以改写文件。在Redis启动的时候会读取该文件重新构建缓存数据。在打开AOF持久化机制之后，Redis每当接收到一条写命令，会先写入系统缓存，然后每隔一定时间（默认是每秒钟）fsync一次（写入到指定文件）。

**启用AOF**

AOF持久化默认是关闭的，如果要启用AOF，需要在redis.conf配置文件中启用该功能，将appendonly no设置为appendonly yes。

![image-20190206225814217](https://ww1.sinaimg.cn/large/006tNc79gy1fzx3jap8o6j31ba0u0n5d.jpg)

所有写操作默认保存在appendonly.aof文件中，可以自行修改保存的路径和文件名。

![image-20190206225946094](https://ww3.sinaimg.cn/large/006tNc79gy1fzx3kw2jq5j31h407qt9j.jpg)

**同步策略**

AOF提供了三种同步策略：

* always（每次写操作就执行一次fsync）
* everysec（每秒执行一次fsync，默认）
* no（不执行fsync）

![image-20190206230709591](https://ww2.sinaimg.cn/large/006tNc79gy1fzx3sljfojj31qc0t40yg.jpg)

**AOF重写**

AOF会记录Redis所有的写操作命令，但这种方式会造成一个问题，就是随着时间的推移，大量频繁的操作将导致AOF文件体积的急剧增长，对系统会造成影响。为了解决以上的问题， Redis就需要对AOF文件进行重写。重写的过程会创建一个新的AOF文件来代替原有的AOF文件， 而新AOF文件和原有AOF 文件保存的数据状态是一致的，但新文件的体积将变得尽可能地小。以下两种方式会触发AOF重写。

1）手动出发

在客户端直接向Redis发送BGREWRITEAOF命令，这个命令会通过移除AOF文件中的冗余命令来重写（rewrite）AOF文件。

![image-20190207235535101](https://ww3.sinaimg.cn/large/006tNc79gy1fzyata4wl2j319m06st9s.jpg)

2）自动触发

其实在启用了AOF之后（appendonly yes），Redis会依据redis.conf配置文件中的auto-aof-rewrite-percentage选项和auto-aof-rewrite-min-size选项来自动执行BGREWRITEAOF命令。

![image-20190207234805782](https://ww2.sinaimg.cn/large/006tNc79gy1fzyalj56u0j31f10u07bq.jpg)

说明：

例如设置了auto-aof-rewrite-percentage为100和auto-aof-rewrite-min-size为64mb，那么当AOF文件的体积大于64MB时，并且AOF文件的体积比上一次重写之后的体积大一倍（100%）的，Redis将执行BGREWRITEAOF命令进行重写。

**AOF优点**

AOF弥补了RDB按照时间范围的操作次数为条件的缺点，即使在默认的策略中发生故障，最多也只会丢失一秒钟的数据，更大程度的保证了数据的安全性。

**AOF缺点**

AOF会保存每一次的写操作，这将导致AOF文件的体积通常要大于RDB文件。如果选用always策略，则表示每一次操作都会记录到AOF文件中，从性能的角度上来说会低于RDB。当然，使用默认的everysec策略进行持久化性能还是非常可观的。

### 8.4 混合持久化

在实际应用中，通常会同时使用RDB和AOF两种持久化来找到一个最佳的平衡点，即能保证性能的同时最大程度保证数据的安全。因此需要RDB和AOF两者同时进行合理的设置和调整。而从Redis 4.0开始，官方提供了一种更加方便的混合持久化配置。

**未启用混合持久化**

在未启用混合持久化之前，如果我们往Redis写入一条记时，RDB文件会保存操作的键值数据，AOF文件则保存的是写操作的指令，我们可以分别查看一下这两个文件的内容。

使用cat命令查看RDB文件

![image-20190208095514100](https://ww1.sinaimg.cn/large/006tNc79gy1fzys57gk6ij31re09i768.jpg)

然而显示的内容并不太直观也不易理解，因此可以借助Redis提供的redis-check-rdb工具进行查看。

![image-20190208090954360](https://ww2.sinaimg.cn/large/006tNc79gy1fzyqu1p66bj30zw0nydkr.jpg)

RDB文件中会保存Redis的相关信息以及存储的keys数量和相关的活期时间。接下来我们继续查看AOF文件的内容，直接使用cat命名进行查看。

![image-20190208093259751](https://ww2.sinaimg.cn/large/006tNc79gy1fzyri2j6y9j31bq0nmgng.jpg)

结果显示AOF文件中保存的是相关的操作指令。

**混合持久化**

要使用混合持久化，除了在redis.conf文件中启用AOF（将appendonly设置为yes），还需要将aof-use-rdb-preamble设置为yes。

![image-20190208093701377](https://ww4.sinaimg.cn/large/006tNc79gy1fzyrm9g4fnj31oy0ik783.jpg)

设置完重新启用Redis服务。启用了混合持久化之后，使用BGREWRITEAOF命令执行一次AOF重写，同时向Redis插入一条新的数据。

![image-20190208095036363](https://ww3.sinaimg.cn/large/006tNc79gy1fzys0ftxjdj31dm0d0go6.jpg)

然后再次使用cat命令再次查看AOF文件，这时会发现启用混合持久化之后的AOF文件内容和未启用时的AOF文件内容不一样。这是因为此时产生的AOF文件是一个RDB-AOF的混合文件，Redis会基于某种协议将此文件的前半部分存储RDB的数据，后半部分存储的是AOF的操作命令。

![image-20190208095307601](https://ww2.sinaimg.cn/large/006tNc79gy1fzys30punpj31ra0tcq6h.jpg)

## 9. 事务

Redis事务可以一次执行多个命令（批量命令操作），并且是一个单独的隔离操作。事务中的所有命令都会按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。

### 9.1 事务操作

Redis事务主要由MULTI 、 EXEC、DISCARD、WATCH和UNWATCH这些基础命令构成。

1）MULTI

*语法：MULTI*

用于标记事务的开始，后续客户端执行的命令都将被存入一个命令队列，直到执行EXEC时，这些命令才会被执行。

![image-20190213142811931](https://ww3.sinaimg.cn/large/006tNc79gy1g04s4sxmt6j31a80dq763.jpg)

2）EXEC

*语法：EXEC*

执行命令队列中的所有命令，但如果在启用一个事务之前执行了WATCH命令，那么只有当WATCH所监控的keys没有被修改的前提下，EXEC命令才能执行事务队列中的所有命令，并返回所有命令的执行结果，否则EXEC将放弃当前事务中的所有命令。

![image-20190213142848912](https://ww3.sinaimg.cn/large/006tNc79gy1g04s5ellh0j315q082t9j.jpg)

3）DISCARD

*语法：DISCARD*

取消事务队列中的所有命令，并将当前连接的状态恢复为非事务状态。如果WATCH命令被使用，会自动执行UNWATCH取消监视的所有keys。

![image-20190213143242324](https://ww4.sinaimg.cn/large/006tNc79gy1g04s9g83qaj31960ha0v0.jpg)

4）WATCH

*语法：WATCH key[key...]*

WATCH命令类似于关系型数据库的乐观锁，可以在启用事务之前监视某些keys的变化。在MULTI命令执行之前，可以指定需要监视的keys，在执行EXEC之前，如果被监控的keys发生修改，EXEC将放弃执行该队列中的所有指令。并且WATCH命令可以监控一个或多个键，一旦其中有一个键被修改（或删除），之后的事务就不会执行。

首先打开一个客户端，并使用watch命令监视user:1001的key，接着使用multi启用事务。

![image-20190213144044183](https://ww3.sinaimg.cn/large/006tNc79gy1g04sht9ipbj311e092myo.jpg)

然后打开第二个客户端，并修改key为user:1001的value为user01。

![image-20190213144332513](https://ww3.sinaimg.cn/large/006tNc79gy1g04skq5demj30xu0903zv.jpg)

最后回到第一个客户端再次对key为user:1001的value修改为user001，并执行exec命令。由于user:1001这个key被第一个客户端所监视，而这个key在启用事务前被第二个客户端修改了，因此当第一个客户端启用事务后再对其进行修改时这是无效的，Redis将放弃队列中的所有指令，返回了(nil)。

![image-20190213145148061](https://ww2.sinaimg.cn/large/006tNc79gy1g04stbodkej312w0iqgoi.jpg)

5）UNWATCH

*语法：WATCH key[key...]*

取消当前事务中指定监控的keys。如果执行了EXEC或DISCARD命令，则无需再手工执行该命令了，因为在此之后UNWATCH命令会自动执行，事务中所有的keys都将自动取消监控。

![image-20190213150349802](https://ww4.sinaimg.cn/large/006tNc79gy1g04t5u4h9ij317u09ogmu.jpg)

### 9.2 原子性

在关系型数据库中的原子性代表一系列不可分割的操作，要么全部执行成功，要么全部不执行。如果执行过程中产生了错误或者异常，那么事务将会自动回滚。而在Redis的事务中是否具备原子性呢？我们看看以下两种情况，并得出相关的结论。

**错误指令**

在使用multi命令开启事务之后，然后输入一些命令，其中包含一个错误的命令。

![image-20190214100249171](https://ww4.sinaimg.cn/large/006tKfTcgy1g0alxaxe0ej31hu0l0djl.jpg)

从结果来看似乎有点符合我们对事务的理解。但仔细想想，这只是在输入命令的时候产生语法的错误，Redis对其进行了校验，报错之后Redis就放弃了这个事务。因此得出的结论是：Redis在启用事务输入操作命令时是原子操作，它会对命任何一个命令进行语法检查，当输入有误时，Redis会清空队列并放弃事务。

**运行时错误**

如果输入的命令都正确，而在执行这些命令时产生了错误，Redis是否会取消所有命令并放弃事务呢？看下面的例子。

![image-20190214111004404](https://ww1.sinaimg.cn/large/006tKfTcgy1g05sa9qi68j31a80omgp4.jpg)

当执行到第二条命令时产生了错误（用户名不是一个整型数值，并不能自增），但是前面和后面的命令都执行成功。并不会因为执行了一个错误的命令而回退所有已经执行成功的命令并放弃整个事务。因此得出的结论是：Redis在执行命令队列时并不是原子性的，通俗点说就是Redis本身并不支持事务的回滚机制。

## 10. Java客户端

### 10.1 使用Lettuce

Lettuce是Redis的一个java客户端，同类的产品还有Jedis。但在多线程的环境下，使用单个Jedis连接是非线程安全的。而Lettuce的连接对象是基于Netty，在多线程并发访问时是线程安全的，并且单个连接对象可以满足多线程并发访问的要求。Lettuce还支持同步、异步、反应式、发布/订阅等多种通信方式。

**添加依赖**

~~~xml
<dependency>
      <groupId>io.lettuce</groupId>
      <artifactId>lettuce-core</artifactId>
      <version>5.2.2.RELEASE</version>
</dependency>
~~~

**编写LettuceUtils：**

~~~java
public class LettuceUtils {

    private static StatefulRedisConnection connection;

    static {
        RedisURI redisURI = RedisURI.Builder
                .redis("localhost")
                .withPort(6379)
                .withPassword("123456")
                .withDatabase(0)
                .withTimeout(Duration.ofSeconds(5))
                .build();
        connection = RedisClient.create(redisURI).connect();
    }

    /**
     * Sync
     * @return
     */
    public static RedisCommands getCommands() {
        return connection.sync();
    }

    /**
     * Async
     * @return
     */
    public static RedisAsyncCommands getAsyncCommands(){
        return connection.async();
    }

    /**
     * Reactive
     * @return
     */
    public static RedisReactiveCommands getReactiveCommands(){
        return connection.reactive();
    }
}
~~~

**示例：**

~~~java
@Test
public void testGetAndSet(){
     RedisCommands<String, String> commands = LettuceUtils.getCommands();
     commands.set("key", "Hello, Redis!");
     String value = commands.get("key");
     System.out.println(value);
}
~~~

> 参考：https://www.baeldung.com/java-redis-lettuce

### 10.2 整合Spring Boot

**添加依赖**

~~~xml
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
     <groupId>com.fasterxml.jackson.core</groupId>
     <artifactId>jackson-databind</artifactId>
</dependency>
~~~

**yml配置**

~~~yml
spring:
  # redis配置
  redis:
    # 指定使用第几个库，默认是0
    database: 0
    # 主机地址
    host: 127.0.0.1
    # 端口
    port: 6379
    # 认证密码（如果在redis.conf中配置了密码）
    password: wangl
    # 连接超时时间
    timeout: 2000
# 连接池配置
#   lettuce:
#     pool:
#       # 最大连接数
#       max-active: 10
#       # 最大空闲连接
#       max-idle: 8
#       # 最小空闲连接
#       min-idle: 5
#       # 建立连接最大等待时间
#       max-wait: 2000
#       # 每间隔多少毫秒运行一次空闲连接回收
#       time-between-eviction-runs: 60000
~~~

由于Lettuce的连接是基于Netty的，一个连接对象（StatefulRedisConnection）就可以满足多线程环境下的并发访问，许多场景是不需要配置连接池的。如果在特定场景下需要用到连接池，也可在yml进行配置，需要依赖commons-pool2。

~~~xml
<dependency>
      <groupId>org.apache.commons</groupId>
      <artifactId>commons-pool2</artifactId>
</dependency>
~~~

#### 10.2.1 使用RedisTemplate

我们可以在类中直接注入一个StringRedisTemplate，key和value都为String类型，它继承自RedisTemplate。

~~~java
@Autowired
private StringRedisTemplate stringRedisTemplate;
~~~

示例：

~~~java
@Test
void testForValue() {
     //添加
     stringRedisTemplate.opsForValue().set("user:1001", "user1");
     //依据key获取value
     String name = stringRedisTemplate.opsForValue().get("user:1001");
     System.out.println(name);
     //删除
     stringRedisTemplate.delete("user:1001");
}
~~~

Spring提供了多种Operations接口来对不同的数据结构进行操作，以下是常见的接口：

| 接口            | 获取方式                    | 说明       |
| --------------- | --------------------------- | ---------- |
| ValueOperations | redisTemplate.opsForValue() | 操作字符串 |
| ListOperations  | redisTemplate.opsForList()  | 操作List   |
| SetOperations   | redisTemplate.opsForSet()   | 操作Set    |
| ZSetOperations  | redisTemplate.opsForZSet()  | 操作ZSet   |
| HashOperations  | redisTemplate.opsForHash()  | 操作Hash   |

示例：

~~~java
stringRedisTemplate.opsForList().leftPush("list","user1");
stringRedisTemplate.opsForList().rightPush("list","user2");
~~~

> 参考：https://www.jianshu.com/p/7bf5dc61ca06

**自定义序列化器**

StringRedisTemplate操作的key和value都是字符串，如果我们需要存储其他类型的数据，那么可以自定义key和value的序列化器来装配一个RedisTemplate。

**编写配置类**

~~~java
@Configuration
public class RedisConfig {
  /**
     * 自定义RedisTemplate,指定key和value的序列化器
     * @param connectionFactory
     * @return
     */
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory connectionFactory){
        //创建RedisTemplate实例
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
      
        //使用StringRedisSerializer作为key的序列化器
        StringRedisSerializer keySerializer = new StringRedisSerializer();
        redisTemplate.setKeySerializer(keySerializer);
        redisTemplate.setHashKeySerializer(keySerializer);
      
        //使用Jackson2JsonRedisSerializer作为value的序列化器
        GenericJackson2JsonRedisSerializer valueSerializer = new GenericJackson2JsonRedisSerializer()；
        redisTemplate.setValueSerializer(valueSerializer);
        redisTemplate.setHashValueSerializer(valueSerializer);
      
      	redisTemplate.setConnectionFactory(connectionFactory);
        return redisTemplate;
    }
  
}  
~~~

spring提供了多种序列化器，以下列出常见的Serializer：

| 序列化器                           | 说明                                |
| ---------------------------------- | ----------------------------------- |
| StringRedisSerializer              | 简单的字符串序列化                  |
| GenericToStringSerializer          | 可以将任何对象泛化为字符串并序列化  |
| GenericJackson2JsonRedisSerializer | 使用Jackson将对象序列化为JSON字符串 |
| JdkSerializationRedisSerializer    | 使用JDK提供的序列化功能             |

示例：

~~~java
//注入redisTemplate
@Autowired
private RedisTemplate<String, Object> redisTemplate

@Test
void test2(){
    Users user = new Users();
    user.setId(1001);
    user.setUserName("user1");
    user.setAge(21);
  
    redisTemplate.opsForValue().set("user:1001", user);
    Users u = (Users) redisTemplate.opsForValue().get("user:1001");
    log.info(u.getUserName());
}
~~~

#### 10.2.2 使用缓存Cache

除了使用RedisTemplate，还可以使用Spring提供的Spring Cache来操作Redis。Spring Cache是一个非常灵活的缓存解决方案，对众多的缓存框架进行了统一的封装，当底层使用不同的缓存框架时，由对应的缓存管理器来进行管理。Spring 3.1内置了五个缓存管理器：

- SimpleCacheManager
- NoOpCacheManager
- ConcrrentMapCacheManager
- CompositeCacheManager
- EhCacheCacheManager

 Spring 3.2引入了另外的一个缓存管理器，这个缓存管理器可以在基于JCache（JSR-107）的缓存提供商之中。除了核心的Spring框架，Spring data又提供了两个缓存管理器。 

- RedisCacheManager 来自于Spring-data-redis项目 
- GemfireCacheManager 来自于Spring-data-GemFire项目

所以我们选择缓存管理器时候，取决于使用底层缓存供应商

##### 10.2.2.1 启用缓存

在配置类上添加@EnableCaching

~~~java
@Configuration
@EnableCaching
public class RedisConfig {
 ... 
}
~~~

##### 10.2.2.2 配置缓存管理器

这里底层使用的Redis作为缓存技术，因此需要装配RedisCacheManager这个缓存管理器。

~~~java
@Configuration
@EnableCaching
public class RedisConfig {
	/**
	* 装配RedisCacheManager，这里初始化了cache1和cache2两个缓存，并存入Map中,
	* 后续在使用时可以指定操作哪一个缓存。
	* @param redisConnectionFactory
	* @return
	*/
	@Bean
	public RedisCacheManager redisCacheManager(RedisConnectionFactory redisConnectionFactory) {
        	Map<String, RedisCacheConfiguration> map = new HashMap<>(2);
        	map.put("cache1", initRedisCacheConfiguration(1800L));
        	map.put("cache2", initRedisCacheConfiguration(3600L));
        	RedisCacheManager cacheManager = RedisCacheManager.builder(redisConnectionFactory)
                	.withInitialCacheConfigurations(map)
                	.build();
        	return cacheManager;
	}

	/**
	* Redis缓存配置，配置相关的key和value序列化器以及缓存过期时间
	* serializeKeysWith()方法用于设置key的序列化器
	* serializeValuesWith()方法用于设置value的序列化器
	* entryTtl()方法用于设置过期时间
	* @param ttl
	* @return
	*/
	private RedisCacheConfiguration initRedisCacheConfiguration(Long ttl) {
     	RedisCacheConfiguration cacheConfiguration = 	RedisCacheConfiguration.defaultCacheConfig();
     	return cacheConfiguration
        //设置key的序列化器
       	.serializeKeysWith(
             	RedisSerializationContext.SerializationPair.fromSerializer(new 	StringRedisSerializer()))
        //设置value的序列化器
       	.serializeValuesWith(
             	RedisSerializationContext.SerializationPair.fromSerializer(new 	GenericJackson2JsonRedisSerializer()))
        //设置缓存过期时间
       	.entryTtl(Duration.ofSeconds(ttl));
	}
}  
~~~

我们可以创建多个缓存实例（如：cache1、cache2），并对这些缓存做相关的配置（比如设置缓存key和value的序列化器以及指定缓存的活期时间等等）。在使用时可以指定要将数据放入哪个缓存实例中，最后将这些缓存实例纳入缓存管理器中管理。

##### 10.2.2.3 缓存注解

Spring 3.1 引入了基于注释的缓存技术，通过少量的配置 annotation 注释即可使得既有代码支持缓存。

**@Cacheable**

该注解标记在一个方法上时表示该方法是支持缓存的，当标记在一个类上时则表示该类所有的方法都是支持缓存的。对于一个支持缓存的方法，Spring会在其标注的方法调用后将其返回值缓存起来，以保证下次利用同样的参数来执行该方法时可以直接从缓存中获取结果，而不需要再次执行该方法。

示例：

~~~java
@Service
public class UserInfoServiceImpl {
  
    @Autowired
    private UserInfoDaoImpl dao;
  
    //这里指定的缓存名为cache1,与配置中的名字一样,缓存的过期时间为1800
    //如果指定的不是配置中的名字,就会使用默认的配置,比如过期时间是-1(永不过期)
    @Cacheable(value = "cache1",key = "#id")
    public UserInfo getUser(Integer id) {
        UserInfo result = dao.getById(id);
        return result;
    }

}
~~~

在业务类方法上使用@Cacheable注解，当执行完此方法后会将dao返回的结果保存在缓存中。如果下次传入相同id查询时会从缓存获取，不会再次调用getUser方法。

@Cacheable主要属性：

| 参数      | 说明                                                         | 示例                                                         |
| --------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| value     | 缓存的名称，在配置类中定义，必须指定至少一个                 | @Cacheable(value=”cache1”) <br/>或者 <br/>@Cacheable(value={”cache1”,”cache2”} |
| key       | 缓存的key，可以为空，如果指定就要按照SpEL表达式编写，如果不指定，则缺省按照方法的所有参数进行组合 | @Cacheable(value=”cache1”,key=”#userName”)                   |
| condition | 缓存的条件，可以为空，使用SpEL编写，返回true或者false，只有为true才进行缓存 | @Cacheable(value=”cache1”, key=”#userName” condition=”#userName.length()>2”) |
| unless    | 用来排除缓存,返回true的时候不放在缓存中                      | @Cacheable(value="cache1", key="#id", unless="#result==null") 表示方法返回值为null时不放置在缓存中 |

key的生成策略：

spring缓存中的key是按照一定规则来生成的，默认情况下是@Cacheable注解中value属性的值加上"::" 再加上key属性的值来生成key。例如：

~~~java
@Cacheable(value="abc",key="#id")
public UserInfo getById(Integer id)
~~~

如果调用getById方法时传入的参数为100，那么最终生成的key就是`abc::100`

自定义key的生成策略：

我们也可以在装配RedisCacheConfiguration时设置key生成策略，例如：

~~~java
RedisCacheConfiguration initRedisCacheConfiguration(Long ttl) {
     	RedisCacheConfiguration cacheConfiguration = 	RedisCacheConfiguration.defaultCacheConfig();
     	return cacheConfiguration
        //设置key的序列化器
       	.serializeKeysWith(
             	RedisSerializationContext.SerializationPair.fromSerializer(new 	StringRedisSerializer()))
        //设置value的序列化器
       	.serializeValuesWith(
             	RedisSerializationContext.SerializationPair.fromSerializer(new 	GenericJackson2JsonRedisSerializer()))
        //设置缓存过期时间
       	.entryTtl(Duration.ofSeconds(ttl));
        ////设置缓存key的前缀，cacheName的值由@Cacheable注解的value属性决定
  			.computePrefixWith(cacheName -> "demo".concat(":").concat(cacheName).concat(":"));
	}
}  
~~~

上面调用RedisCacheConfiguration的computePrefixWith方法来设置key的前缀，此时再去调用getById方法时，最终生成的key为`demo:abc:100`。

key表达式：

@Cacheable的key属性支持SpringEL表达式,这里的EL表达式可以使用方法参数及它们对应的属性。使用方法参数时我们可以直接使用“#参数名”或者“#p加上参数下标”。例如：

~~~java
@Cacheable(value="users", key="#id")
public User getUser(Integer id) {
   return dao.getUserById(id);
}

@Cacheable(value="users", key="#p0")
public User getUser(Integer id) {
   return dao.getUserById(id);
}

@Cacheable(value="users", key="#user.id")
public User getUser(User user) {
   return dao.getUserById(user.getId());
}

@Cacheable(value="users", key="#p0.id")
public User getUser(User user) {
   return dao.getUserById(user.getId());
} 
~~~

除了上述使用方法参数作为key之外，Spring还为我们提供了一个root对象可以用来生成key。通过该root对象我们可以获取到以下信息。

| 属性名称    | 说明                            | 示例                 |
| ----------- | ------------------------------- | -------------------- |
| methodName  | 当前方法名                      | #root.methodName     |
| method      | 当前方法                        | #root.method.name    |
| target      | 当前被调用的对象                | #root.target         |
| targetClass | 当前被调用对象的class           | #root.targetClass    |
| args        | 当前方法参数组成的数组          | #root.args[0]        |
| caches      | 当前被调用的方法使用的Cache名称 | #root.caches[0].name |

示例：

~~~java
@Cacheable(value="users", key="#root.methodName")
public User getUser(Integer id) {
   return dao.getUserById(id);
}
~~~

此时将使用当前方法名作为缓存的key。

**@CachePut**

@CachePut标注的方法在执行前不会去检查缓存中是否存有缓存的数据，而是每次都会执行该方法，并将执行结果再次保存到指定的缓存中，相当于覆盖缓存。

示例：

~~~java
@CachePut(value = "cache1",key = "#user.id")
public UserInfo insert(User user) {
    dao.insert(userInfo);
    return user;
}
~~~

@Cacheable主要属性：

| 属性      | 说明                                                         |
| --------- | ------------------------------------------------------------ |
| value     | 缓存的名称，在配置类中定义，必须指定至少一个                 |
| key       | 缓存的key，可以为空，如果指定就要按照SpEL表达式编写，如果不指定，则缺省按照方法的所有参数进行组合 |
| condition | 缓存的条件，可以为空，使用SpEL编写，返回true或者false，只有为true才进行缓存 |

**@CachEvict**

该注解根据一定的条件对缓存进行清空

示例：

~~~java
@CacheEvict(value="cache1", key="#id")
public void deleteById(Integer id) {
    dao.deleteById(id);
}
~~~

@Cacheable主要属性：

| 属性             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| value            | 缓存的名称，在配置类中定义，必须指定至少一个                 |
| key              | 缓存的key，可以为空，如果指定就要按照SpEL表达式编写，如果不指定，则缺省按照方法的所有参数进行组合 |
| condition        | 缓存的条件，可以为空，使用SpEL编写，返回true或者false，只有为true才进行缓存 |
| allEntries       | 是否清空所有缓存内容，缺省为 false，如果指定为 true，则方法调用后将立即清空所有缓存 |
| beforeInvocation | 是否在方法执行前就清空，缺省为 false，如果指定为 true，则在方法还没有执行的时候就清空缓存，缺省情况下，如果方法执行抛出异常，则不会清空缓存 |

> 参考：
>
> https://juejin.im/post/5b76e732f265da4376203849
>
> https://www.baeldung.com/spring-expression-language
>
> https://www.jianshu.com/p/6a0a1fa453c8

