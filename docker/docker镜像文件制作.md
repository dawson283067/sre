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


## Dockfile 介绍 

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
FROM centos:7.6.1810  # 在整个dockerfile文件中，除了注释之外的第一行，需要使用FROM，它用于指定父镜像

ADD  # 用于添加宿主机本地的文件、目录、压缩包等资源到镜像里面去，会自动解压缩tar.gz格式的压缩包，不会自动解压zip

COPY  # 用于添加宿主机本地的文件、目录、压缩包等资源到镜像里面去，不会解压任何压缩包

MAINTAINER  # 镜像的作者信息

LABEL  # 用来指定生成镜像的元数据

ENV  # 设置环境变量

USER  # 指定运行操作的用户，如果要写就写到最下面，通常在程序中指定。比如，USER nginx

EXPOSE  # 声明，要把容器的某些端口映射到宿主机。比如，EXPOSE 80

RUN  # 执行shell命令，但是一定要以非交互式的方式执行。比如，RUN yum install vim unzip -y && cd /etc/nginx  

VOLUME  # 定义volume

WORKDIR  # 用于定义工作目录，通常使用cd 

CMD  # 镜像启动为一个容器时候默认命令或脚本 CMD ["/bin/bash"]

ENTRYPOINT  # 会把CMD后面的命令作为ENTRYPOINT的参数。ENTRYPOINT会对环境初始化，用于容器启动的时候做初始化，通常是脚本
```

面试：Dockerfile常见命令，如何使用的？


## 制作编译版 nginx 1.16.1 镜像 all-in-one

docker build命令会在当前目录下找Dockerfile文件
Dockerfile一定要放在固定的目录。根据公司的业务进行分类。

Dockerfile比镜像重要。

```sh
创建Dockerfile目录
cd /opt
mkdir dockerfile/{web/{nginx,tomcat,jdk,apache},system/{centos,ubuntu,redhat}} -pv
```

### 下载镜像并初始化系统
1. 下载基础镜像，比如centos，alpine等
2. 安装 net-tools 等基本工具包，作为基础镜像

### 编写Dockerfile
```sh
cd /opt/dockerfile/web/nginx/all-in-one

# base image for m43 nginx
FROM centos:7.8.2003
MAINTAINER "jack 2973707860@qq.com"
RUN yum install -y epel-release && yum install -y vim wget tree lrzsz gcc gcc-c++ automake pcre pcre-devel zlib zlib-devel openssl openssl-devel iproute net-tools iotop

docker build -t "harbor.magedu.com/m43/nginx-web1:1.16.1" .
```
以上制作了基础镜像。
Dockerfile是分层构建的，凡是前面没有变化的话，直接使用之前构建好的那一层。

镜像构建的过程中是临时启动一个容器，做完之后，会把这个镜像提交为一个镜像。

通过docker history命令查看镜像构建历史
docker history harbor.magedu.com/m43/nginx-web1:1.16.1

验证
docker run -it --rm harbor.magedu.com/m43/nginx-web1:1.16.1 bash

### 准备源码包与配置文件

### 执行镜像构建

### 构建完成

### 查看是否生成本地镜像

### 从镜像启动容器

### 访问web界面

找到并下载nginx源码
wget http://nginx.org/download/nginx-1.16.1.tar.gz

```sh
# base image for m43 nginx
FROM centos:7.8.2003
MAINTAINER "jack 2973707860@qq.com"
RUN yum install -y epel-release && yum install -y vim wget tree lrzsz gcc gcc-c++ automake pcre pcre-devel zlib zlib-devel openssl openssl-devel iproute net-tools iotop
ADD nginx-1.16.1.tar.gz /usr/local/src/
RUN cd /usr/local/src/nginx-1.16.1 && ./configure --prefix=/apps --with-http_sub_module && make && make install

```

验证
docker run -it -p 8088:80--rm harbor.magedu.com/m43/nginx-web1:1.16.1 bash
浏览器访问nginx页面

```sh
# base image for m43 nginx
FROM centos:7.8.2003
MAINTAINER "jack 2973707860@qq.com"
RUN yum install -y epel-release && yum install -y vim wget tree lrzsz gcc gcc-c++ automake pcre pcre-devel zlib zlib-devel openssl openssl-devel iproute net-tools iotop
ADD nginx-1.16.1.tar.gz /usr/local/src/
RUN cd /usr/local/src/nginx-1.16.1 && ./configure --prefix=/apps/nginx --with-http_sub_module && make && make install
RUN useradd nginx -u 2022
ADD nginx.conf /apps/nginx/conf/nginx.conf
ADD code.tar.gz /data/nginx/html

```

验证
docker run -it -p 8088:80--rm harbor.magedu.com/m43/nginx-web1:1.16.1 bash
浏览器访问nginx页面

```sh
# base image for m43 nginx
FROM centos:7.8.2003
MAINTAINER "jack 2973707860@qq.com"
RUN yum install -y epel-release && yum install -y vim wget tree lrzsz gcc gcc-c++ automake pcre pcre-devel zlib zlib-devel openssl openssl-devel iproute net-tools iotop
ADD nginx-1.16.1.tar.gz /usr/local/src/
RUN cd /usr/local/src/nginx-1.16.1 && ./configure --prefix=/apps/nginx --with-http_sub_module && make && make install
RUN useradd nginx -u 2022
ADD nginx.conf /apps/nginx/conf/nginx.conf
ADD code.tar.gz /data/nginx/html
EXPOSE 80 443
CMD ["/apps/nginx/sbin/nginx","-g","daemon off;"]
```

在一个容器里，要有一个能在容器tty的前端执行的进程
+ 命令
tail -f /etc/hosts
+ 服务进程
daemon off;
+ 脚本
run_nginx.sh

```sh
# base image for m43 nginx
FROM centos:7.8.2003
MAINTAINER "jack 2973707860@qq.com"
RUN yum install -y epel-release && yum install -y vim wget tree lrzsz gcc gcc-c++ automake pcre pcre-devel zlib zlib-devel openssl openssl-devel iproute net-tools iotop
ADD nginx-1.16.1.tar.gz /usr/local/src/
RUN cd /usr/local/src/nginx-1.16.1 && ./configure --prefix=/apps/nginx --with-http_sub_module && make && make install
RUN useradd nginx -u 2022
ADD nginx.conf /apps/nginx/conf/nginx.conf
ADD code.tar.gz /data/nginx/html
ADD run_nginx.sh /apps/nginx/sbin/run_nginx.sh
RUN chmod a+x /apps/nginx/sbin/run_nginx.sh
EXPOSE 80 443
CMD ["/apps/nginx/sbin/nginx/run_nginx.sh"]
```

## 分层构建 tomcat 镜像