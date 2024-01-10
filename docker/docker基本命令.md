# docker基本命令

## docker run

```shell
Usage: docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

e.g.
docker run -it alpine:latest ls -l
docker run -it -p 81:80 --name n50-nginx-container1 nginx:1.14.2
docker run -it -d -p 81:80 --name n50-nginx-container1 nginx:1.14.2
docker run -it --rm nginx:1.14.2 cat /etc/nginx/nginx.conf 传递命令单次看结果，--rm看完就删除容器

notes:
单次执行后，容器退出。
在同一个服务器，容器的名称不能重复，从宿主机到容器所转发的端口不能重复

-i 将当前终端标准输入作为输入
-t tty
-d 后台运行daemon
```

IOPS
单机容器 300-400
k8s 150-200
k8s的优势在于集群，单机跑的话还是容器性能最好。

## docker ps

## docker rm 删除容器
```sh
docker rm -f 强制删除正在运行的容器
```

## docker logs
doker logs --tail 200 [容器名或者容器ID]

## docker port 
docker port [容器名称或者容器ID]

## docker start

## docker stop

## docker exec

```sh
docker exec -it [容器名称或ID] bash
```
## docker attach
操作会在各个终端同步显示，不方便。

## docker inspect

```sh
docker inspect -f {{.Id}} [容器名称或ID]
这种方式特别适合用于监控

docker inspect -f {{.State.Status}} [容器名称或者ID]
可以用Python脚本来遍历

docker inspect -f {{.NetworkSettings.IPAddress}} [容器名称或ID]
获取IP地址

docker inspect -f {{.State.Pid}} [容器名称或ID]
apt install util-linux 可以获得nsenter
nsenter -t pid -m -u -i-n -p
等价于docker exec，但是没有它方便
```

## 查看容器内部的hosts文件
```sh

```
docker-compose 适合一个主机中跑多个容器，在单机中跑

## 批量关闭正在运行的容器
```sh
docker stop `docker ps -q`
```

## 批量删除已退出的容器
```sh
docker rm `docker ps -a | grep "Exited" | awk '{print $1}' `
docker rm -f $(docker ps -aq -f status=exited)
两种方法都可以
每隔一天或者三天清理下已退出的容器
```

powerdns可以做分布式，前面是LVS

## 指定容器的DNS
```sh
docker run -it --dns 172.16.0.1 centos:7.6.1810 bash
cat /etc/resolv.conf
用于解析公司内部的域名
```
注意k8s中的DNS解析: coredns --> powerdns --> 公网DNS  forward

面试：容器中的域名解析

## docker cp
```sh
docker cp 容器ID:/etc/nginx/nginx.conf /opt/   # 源-->目的
作用：改配置文件

docker cp /opt/nginx.conf 容器ID:/etc/nginx/nginx.conf  # 会把容器内的文件直接覆盖掉

实际工作中是重新打镜像
```

## docker run --env 选项
```sh
docker run -it -p 3306:3306 --env MYSQL_ROOT_PASSWORD="magedu.com" mysql:5.6.44
# 必须通过传递环境变量才能启动MySQL
# k8s中在yaml文件中指定
```

## docker run -v 选项
```sh
mkdir /data/mysql -p
docker run -it -p 3306:3306 --env MYSQL_ROOT_PASSWORD="magedu.com" -v /data/mysql:/var/lib/mysql mysql:5.6.44
# 容器被删除后，数据会保留
```

## 其他命令
```sh
docker update 容器 --cpus 2 # 更新容器配置信息

docker events # 获取dockerd的实时事件

docker wait 容器 # 查看容器的退出状态码
```

