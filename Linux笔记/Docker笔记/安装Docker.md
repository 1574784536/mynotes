### Docker安装

1、yum包更新到最新

yum -update

2、安装所需要的软件包，yum-util提供的yum-config-manager功能，另外两个是devicemapper驱动依赖

yum -y install yum-utils device-mapper-persistent-data lvm2

3、设置yum源

yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

4、安装docker

yum -y install docker-ce

5、查看docker版本

docker -v



### 配置阿里云镜像加速

进入docker目录

cd /etc/docker

修改daemon.json文件

vim daemon.json

修改为如下：

```json
{
  "registry-mirrors":["https://ieqtbysv.mirror.aliyuncs.com"]
}
```

```shell
{ 
 
        "registry-mirrors": [  
            "https://registry.docker-cn.com", 
            "http://hub-mirror.c.163.com", 
                "https://docker.mirrors.ustc.edu.cn"] 
 
}
```



重新加载daemon

systemctl daemon-reload

重启docker

systemctl restart docker

![image-20200317214652206](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200317214652206.png)

可执行docker run hello-world拉取hello-world镜像

![image-20200317214911902](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200317214911902.png)





![image-20200318151805411](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200318151805411.png)