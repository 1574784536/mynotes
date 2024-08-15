## Linux下使用Docker安装RabbitMQ

1、拉取镜像

docker pull rabbitmq:3.7.15



2、启动容器中的rabbitmq

docker run -d --name rabbitmq \

--publish 5671:5671 --publish 5672:5672 --publish 4369:4369 \

--publish 25672:25672 --publish 15671:15671 --publish 15672:15672 \

rabbitmq:3.7.15



3、进入容器开启web管理功能

docker exec -it rabbitmq /bin/bash

rabbitmq-plugins enable rabbitmq_management
