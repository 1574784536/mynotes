### Docker命令

##### 服务相关命令

1、启动docker

systemctl start docker

2、查看docker状态

systemctl status docker

3、停止docker命令

systemctl stop docker

3、重启

systemctl restart docker

4、开机启动docker

systemctl enable docker

##### 镜像相关命令

1、查看镜像

docker images

2、搜索镜像

docker search redis

3、拉取镜像(要指定版本可在后面加上冒号紧接着输入版本)

docker pull redis

docker pull redis:5.0

4、删除镜像(后面写上镜像的id或者跟上镜像加版本)

docker rmi de25a81a51

docker rmi redis:5.0

5、删除所有镜像(rmi后面加上两个tab键上的`，在里面输入docker images -q)

docker rmi ` docker images -q `  #删除所有镜像

![image-20200317222559945](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200317222559945.png)

5、查看所有镜像id

docker images -q

##### Docker容器相关命令

1、查看容器（-i表示容器一直运行，-t给容器分配一个伪终端，--name=表示给容器起一个名字，可以随便起）

![image-20200317223359248](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200317223359248.png)

退出

exit

![image-20200317223516174](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200317223516174.png)

查看正在运行的容器

docker ps -a

2、创建容器

后台创建容器

docker run -id --name=c2 centos:7

![image-20200317224001762](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200317224001762.png)



这样创建的方式容器是不会处于关闭的状态

![image-20200317224328125](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200317224328125.png)



3、进入容器

docker exec -it c2 /bin/bash

![image-20200317224129859](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200317224129859.png)

![image-20200317225052560](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200317225052560.png)

4、停止容器

docker stop c2

![image-20200317224611733](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200317224611733.png)

5、删除容器

docker rm c1

docker pa -aq(查看所有容器的id)

删除所有容器(rm后面跟上tab键上的`符号)

![image-20200317225410521](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200317225410521.png)

6、查看容器信息

docker inspect c2











docker info——-详细信息

REPOSITORY———-镜像的仓库源
TAG———-镜像的标签
IMAGE ID———-镜像的id 镜像的创建时间
CREATED———-镜像的创建时间
SIZE———-镜像大小

docker images———-详细信息
-q ———-只显示镜像的ID

docker search mysql —filter=STARS=3000 —————查询镜像

docker pull 镜像名 [:tag(版本)]————-下载镜像 如果不写 tag，默认最新的

docker rmi -f 容器id ———删除指定镜像id

docker run [可选参数] image ———-启动镜像并进入容器
-name=“” ——-容器名字
-d ——-后台方式运行
注:docker 容器使用后台运行，就必须要一个前台进程，docker发现没有应用，就会自动停止
-it ——-使用交互方式运行，进入容器查看内容
-p ——-指定容器的端口

docker ps ————-查看当前运行的容器
-a ————-查看当前运行的容器和历史运行过的容器
-n=？ ————-查看最近创建的容器
-q ————-查看容器id

exit ————-退出容器
Ctrl+P+Q ————-退出容器后台挂起

docker start 容器id ————启动容器
docker restart 容器id ————重启容器
docker stop 容器id ————停止容器
docker kill 容器id ————杀死容器

docker logs [容器id] ———-显示docker日志
-tf ———-显示所有日志加上时间戳
—tail number ————显示日志条数

docker top 容器id ———-查看容器中进程信息
docker inspect 容器id —————-查看容器的元信息

docker exec -it 容器id /bin/bash —————-进入当前正在运行的容器
docker cp 容器id:要拷贝的文件目录 /拷贝目的



将打包tar镜像文件导入到自己的docker仓库

docker loca -i 导入tar的镜像文件名 

docker load -i tomcat-8.0-jre8.tar

![image-20210501193921098](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210501193921098.png)

