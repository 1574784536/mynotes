### Linux搭建FastDFS

阿里云服务器需开放端口

![image-20200317154034027](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200317154034027.png)

### 1、安装gcc

yum -y install gcc-c++

### 2、安装libevent

yum -y install libevent

### 创建目录并上传所需的文件

mkdir -p /fileservice/fast

上传所需的文件到 /fileservice/fast

![image-20200316132453324](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316132453324.png)

解压libfastcommon

tar -zxvf libfastcommon-1.0.35.tar.gz

![image-20200316133051545](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316133051545.png)

进入libfastcommon-1.0.35

cd libfastcommon-1.0.35

![image-20200316133205430](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316133205430.png)

进行编译

./make.sh

安装

./make.sh install

![image-20200316133251749](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316133251749.png)

![image-20200316133316596](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316133316596.png)

安装相关依赖

yum -y install perl

yum -y install pcre

yum -y install pcre-devel

yum -y install zlib

yum -y install zlib-devel

yum -y install openssl

yum -y install openssl-devel

解压fastdfs-5.11.tar.gz

tar -zxvf fastdfs-5.11.tar.gz

![image-20200316133847851](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316133847851.png)

进入fastdfs-5.11

cd fastdfs-5.11

![image-20200316133930436](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316133930436.png)

![image-20200316133945976](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316133945976.png)

执行编译：

./make.sh

安装：

./make.sh install

查看tracker和storage的可执行脚本

ll /etc/init.d/ | grep fdfs

![image-20200316134213274](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316134213274.png)

准备配置文件，默认在/etc/fdfs/下面

![image-20200316134329337](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316134329337.png)

把配置文件名中的sample去了【可以复制一份】

cp client.conf.sample client.conf

cp storage.conf.sample storage.conf

cp storage_ids.conf.sample storage_ids.conf

cp tracker.conf.sample tracker.conf

![image-20200316134817327](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316134817327.png)

然后修改tracker的存放数据和日志的目录

mkdir -p /home/yxd/fastdfs/tracker

配置和启动tracker

切换到目录：/etc/fdfs/目录下

cd /etc/fdfs/

修改tracker.conf

vim tracker.conf

把base_path=/home/yuqing/fastdfs修改为：base_path=/home/yxd/fastdfs/tracker

![image-20200316135531077](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316135531077.png)

### 启动tracker

service fdfs_trackerd start

![image-20200316135649020](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316135649020.png)

注意：你会在/home/yxd/fastdfs/tracker/目录下生成2个目录，一个是数据，一个是日志

![image-20200316135835994](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316135835994.png)

配置和启动storage

切换到:/etc/fdfs目录下

cd /etc/fdfs

修改storage.conf配置文件

vim storage.conf

把base_path=/home/yuqing/fastdfs修改为base_path=/home/yxd/fastdfs/storage

![image-20200316140142617](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316140142617.png)

修改storage的存放位置

把storage_path0=/home/yuqing/fastdfs修改为：storage_path0=/home/yxd/fastdfs/storage

![image-20200316140403511](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316140403511.png)

如需多个挂载磁盘则定义多个storage_path，如下：

store_path1=...

store_path2=...

store_path3=...

修改tracker的地址，把tracker_server修改为你的服务器ip

tracker_server=你服务ip:22122

创建storage目录

mkdir -p /home/yxd/fatsdfs/storage

### 启动storage

service fdfs_storaged start

![image-20200316140958660](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316140958660.png)

### 可以使用FastDFS自带工具进行测试

cd /etc/fdfs

修改client.conf配置文件

vim client.conf

把base_path=/home/yuqing/fatsdfs修改为base_path=/home/yxd/fatsdfs/storage

![image-20200316141315039](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316141315039.png)

修改tracker_server=你的ip地址:22122

进行测试

上传一张图片名为0.jpg到fileservice/fast下进行测试

/usr/bin/fdfs_upload_file ./client.conf /fileservice/fast/0.jpg

结果如下，表明成功

![image-20200316142014317](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316142014317.png)

### 安装nginx

进入到fileservice/fast目录

cd /fileservice/fast

解压fastdfs-nginx-module-1.20.tar.gz压缩文件

tar -zxvf fastdfs-nginx-module-1.20.tar.gz

![image-20200316142842985](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316142842985.png)

进入到fastdfs-nginx-module-1.20/src目录

cd fastdfs-nginx-module-1.20/src

修改config

1、把ngx_module_incs="/usr/local"修改为：ngx_module_incs="usr/include/fastdfs usr/include/fastcommon/"

2、把CORE_INCS="$CORE_INCS /usr/local/include"修改为：CORE_INCS="$CORE_INCS /usr/include/fastdfs /usr/include/fastcommon"

![image-20200316143728254](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316143728254.png)

将fastdfs-nginx-module/src下的mod_fastdfs.conf拷贝到/etc/fdfs目录下

cp mod_fastdfs.conf /etc/fdfs/

![image-20200316144033148](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316144033148.png)

并修改/etc/fdfs/module_fastdfs.conf的内容

vim /etc/fdfs/mod_fastdfs.conf

1、把tracker_server=server:22122修改为你的ip地址tracker_server=服务ip:22122

2、把url_have_group_name=false把false修改为true

3、把storage_path0=/home/yuqing/fastdfs修改为storage_path0=/home/yxd/fastdfs/storage

进入之前解压的fastdfs目录下

cd /fileservice/fast/fastdfs-5.11/conf

拷贝http.conf mime.types到fdfs目录下

cp http.conf mime.types /etc/fdfs/

![image-20200316145041791](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316145041791.png)

nginx安装

进入fast目录

cd fileservice/fast

解压nginx-1.15.2.tar.gz

进入nginx解压的目录下

cd nginx-1.15.2

加入模块命令

./configure --prefix=/opt/nginx --sbin-path=/usr/bin/nginx --add-module=/fileservice/fast/fastdfs-nginx-module-1.20/src

编译并安装

make && make install

修改nginx配置

cd /opt/nginx/conf

vim nginx.conf

![image-20200316150342915](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316150342915.png)

### 启用nginx

cd /usr/bin

./nginx

![image-20200316150434832](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200316150434832.png)

在浏览器输入：http://IP地址/group1/M00/00/00/rBH9ol5vGnCAX2-OAAASNOQQacs092.jpg

![image-20200317153853702](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200317153853702.png)