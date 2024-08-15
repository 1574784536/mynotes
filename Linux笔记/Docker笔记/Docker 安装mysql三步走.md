## 拉取镜像

sudo docker pull mysql:5.7

## 创建数据映射目录

```xml
# 数据、日志、配置文件（Linux 我选择放在家目录下）
mkdir -p ~/mysql/data ~/mysql/logs ~/mysql/conf
# 创建配置文件
touch ~/mysql/conf/mysql.cnf
```

## 启动容器

```xml
sudo docker run -p 3306:3306 --name mysql -v ~/mysql/conf:/etc/mysql/conf.d -v ~/mysql/logs:/logs -v ~/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=josxy -d mysql:5.7
# 其中MYSQL_ROOT_PASSWORD=josxy 是root用户的密码
```

## 查看容器运行情况

```xml
sudo docker ps | grep mysql # docker容器mysql进程
#0f3e1c50e357        mysql:5.7           "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:3306->3306/tcp, 33060/tcp   mysql 
ps -aux | grep 3306 # 端口绑定情况
#root      3595  0.0  0.0 1076540 3868 ?        Sl   14:09   0:00 /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 3306 -container-ip 172.17.0.2 -container-port 3306
#josxy     8355  0.0  0.0   9160   892 pts/2    S+   14:13   0:00 grep 3306
```

