## Docker安装Redis

##### 1、下载docker

docker pull redis:5.0.7

##### 2、运行redis

docker run -d --name redis-6379 -p 6379:6379 redis:5.0.7 --requirepass "1574784536"

##### 3、进入redis

docker exec -it redis-6379 bash

##### 4、进入客户端

docker exec -it redis-6379 redis-cli -h 127.0.0.1 -a 1574784536