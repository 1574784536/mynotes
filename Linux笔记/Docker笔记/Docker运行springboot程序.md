#### Docker部署springboot应用

1、打包好我们的springboot程序

![image-20200319160458724](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200319160458724.png)

2、新建Dockerfile文件（取名字一定要Dockerfile,后缀名一定要去掉）

![image-20200319160840000](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200319160840000.png)

FROM java:8
VOLUME /tmp
ADD testdocker-0.0.1-SNAPSHOT.jar /testdocker.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/testdocker.jar"]

3、把他们上传到linux下的/home/docker目录下

4、构建镜像

docker build -t test .

5、运行

docker run -d -p 8080:8080 --name mytest test



