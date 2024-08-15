下载

```shell
docker pull mongo:latest
```

运行

```shell
docker run -itd --name mongo -p 27017:27017 mongo --auth
```

进入mongo

```shell
docker exec -it mongo mongo admin
```

![image-20220806112454688](/Users/yangxudong/Library/Application Support/typora-user-images/image-20220806112454688.png)

创建名为admin，密码为123456的用户

```shell
db.createUser({ user:'admin',pwd:'123456',roles:[ { role:'userAdminAnyDatabase', db: 'admin'}]});
```

![image-20220806112736053](/Users/yangxudong/Library/Application Support/typora-user-images/image-20220806112736053.png)

尝试连接

```shell
db.auth('admin','123456')
```

![image-20220806112831404](/Users/yangxudong/Library/Application Support/typora-user-images/image-20220806112831404.png)

当运行`db.stats()` 或者 `show users`,你可能会遇到错误 `not authorized on admin to execute command...`

![image-20220806112926431](/Users/yangxudong/Library/Application Support/typora-user-images/image-20220806112926431.png)

原因是用户admin没有授予`read`or`readWrite`权限，可以如下设置，然后执行`db.stats()，即可看到db的相关信息`

```shell
 db.grantRolesToUser("admin", [ { role: "readWrite", db: "admin" } ])
```

![image-20220806112955899](/Users/yangxudong/Library/Application Support/typora-user-images/image-20220806112955899.png)

添加用户时各个角色对应权限

.数据库用户角色：read、readWrite;
.数据库管理角色：dbAdmin、dbOwner、userAdmin；
.集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager；
.备份恢复角色：backup、restore
.所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
.超级用户角色：root

### 创建数据库用户（需要创建哪个数据库的用户，则需要切换到哪个数据库下）

首先保证你已经以用户管理员的身份登录 admin 数据库。然后用 use 命令切换到目标数据库，同样用 db.createUser() 命令来创建普通用户，

其中角色名为 “readWrite”。下面是一个例子：

![image-20220806113103008](/Users/yangxudong/Library/Application Support/typora-user-images/image-20220806113103008.png)

