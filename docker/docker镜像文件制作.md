## docker commit
容器发生变化后提交，创建新的镜像

通常这种方法不用

```sh
docker run -it -p 9999:80 centos:7.6.1810 bash

# 安装epel源
yum install epel-release

mkdir /data/nginx -p

docker ps

docker commit -a "jack 2973707860@qq.com" -m "magedu m43 image" --change="EXPOSE 80" 容器ID harbor.magedu.com/boss/nginx-web1:v1

docker images

docker run -it --rm harbor.magedu.com/boss/nginx-web1:v1 bash

# 这种方式用的不多，后期维护比较麻烦

代码升级/发版/部署
代码回滚/
```


## Dockfile 介绍 制作编译版 nginx 1.16.1 镜像

```sh
https://docs.docker.com/engine/reference/builder/

ADD
COPY
ENV
EXPOSE
LABEL
STOPSIGNAL
USER
VOLUME
WORKDIR
RUN
```
```sh
FROM centos:7.6.1810  # 在整个dockerfile文件中，除了注释之外的第一行，要是from，用于指定父镜像

ADD  # 用于添加宿主机本地的文件、目录、压缩包等资源到镜像里面去，会自动解压缩tar.gz格式的压缩包，不会自动解压zip

COPY  # 用于添加宿主机本地的文件、目录、压缩包等资源到镜像里面去，不会解压任何压缩包

LABEL  # 设置镜像的属性标签（镜像的作者信息）

ENV  # 设置环境变量

USER nginx  # 指定运行操作的用户，如果要写就写到最下面，通常在程序中指定

EXPOSE 80  # 声明，要把容器的某些端口映射到宿主机

RUN yum install vim unzip -y && cd /etc/nginx  # 执行shell命令，但是一定要以非交互式的方式执行

VOLUME  # 定义volume

WORKDIR  # 用于定义工作目录，通常使用cd 

CMD  # 镜像启动为一个容器时候默认命令或脚本 CMD ["/bin/bash"]

ENTRYPOINT  # 会把CMD后面的命令作为ENTRYPOINT的参数。ENTRYPOINT会对环境初始化，用于容器启动的时候做初始化，通常是脚本
```

面试：Dockerfile常见命令，如何使用的？