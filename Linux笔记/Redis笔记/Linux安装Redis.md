### Linux安装Redis

下载编译软件

yum -y install gcc-c++

把Redis-5.0.7文件上传到/usr/local/download

然后解压

tar -zxvf Redis-5.0.7

重命名

mv Redis-5.0.7 redis

创建文件夹

mkdir -p /usr/local/src/redis

复制到/usr/local/src/

cp -r redis /usr/local/src/

切换到

cd /usr/local/src/redis

进入到redis目录

cd redis

进行编译依赖

make hiredis lua jemalloc linenoise

然后进入到

cd /usr/local/src/redis

make

把上面的redis目录安装吧它安装到/usr/local/redis

mkdir /usr/local/redis

make install PREFIX=/usr/local/redis

进入目录

cd /usr/local/redis

cd bin

可以使用which命令查看redis是否启动

将配置文件移动到/root/myredis

mkdir -p /root/myredis

cp /usr/local/src/redis/redis.conf /root/myredis

启用redis

cd /root/myredis

/usr/local/redis/bin/redis-server ./redis.conf

设置后台运行

vim redis.conf

把daemonize no 修改为daemonize yes

然后再启动

/usr/local/redis/bin/redis-server ./redis.conf

查看是否启用redis服务

ps -ef|grep redis

#### 连接客户端

cd /use/local/redis/bin

./redis-cli

退出

quit

连接其他ip地址和端口

./redis-cli -h 127.0.0.1 -p 6379 

测试有没有连接成功

输入ping

返回pong则成功

停止redis

cd /usr/local/redis/bin

./redis-cli shutdown

或者

pkill redis-server

配置开机自启

vim /etc/rc.local

加入

/usr/local/redis/bin/redis-server /root/myredis/redis-conf

