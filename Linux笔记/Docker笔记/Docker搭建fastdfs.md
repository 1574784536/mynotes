#### 使用Docker搭建FastDFS

1、查看fastdfs镜像

docker search fastdfs

2、拉取镜像

docker pull delron/fastdfs

3、启动tracker服务

docker run -d --network=host --name tracker -v /home/fdfs/docker/fastdfs/tracker:/var/fdfs delron/fastdfs tracker

4、启动storage服务

docker run -d --network=host --name storage -e TRACKER_SERVER=192.168.2.132:22123 -v /home/fdfs/docker/fastdfs/storage:/var/fdfs -e GROUP_NAME=group1 delron/fastdfs storage

5、修改nginx端口

（nginx默认端口为8888，如无需更改可跳过）

1.进入storage容器：docker exec -it 953f982bd474 bash 

2.修改storage内部http.server_port:`vi /etc/fdfs/storage.conf`,在最后一行 # the port of the web server on this storage server http.server_port=8888 

3.修改Nginx端口与上面保持一致：`vi /usr/local/nginx/conf/nginx.conf` server {        listen       8888; ... 4.重启容器：docker restart 953f982bd474

6、测试

1.拷贝一张图片（test.png）到目录/home/fdfs/docker/fastdfs/storage 

2.进入storage容器：docker exec -it 953f982bd474 bash 进入fdfs目录：cd /var/fdfs 运行命令：/usr/bin/fdfs_upload_file /etc/fdfs/client.conf test.png 运行成功后会返回地址：group1/M00/00/00/rBB8gV3OusGAKmXmAAAM4C6aVLU766.png