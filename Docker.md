# 背景
虚拟化技术.操作系统层面的虚拟化技术,区别于vm,kvm,虚拟一套硬件后构建操作系统.docker基于操作系统做虚拟化.
# 简介
go语言开发,基于linux内核的cgroup,namespace以及UnionFs等技术对进程进行封装隔离.
# 常用
```
docker pull imgae_name:tag
docker run -itd -p port:port -v path_or_volume:path --name container_name --network network_name --runtime runtime_name --device /dev/video0 image_name:tag command
docker exec -it container_name command
docker restart container_name
docker logs container_name
docker kill container_name
docker rm container_name
docker image rm image_name:tag
docker network create --driver bridge --subnet 192.168.33.0/24 nj_nt
docker network ls
docker network inspect nj_nt
docker volume ls
docker volume inspect volume_name
```
# 应用
## Install
### Docker
```
1. apt-get purge docker-ce docker-ce-cli containerd.io && rm -rf /var/lib/docker
2. apt-get update &&\
	apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
3. curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
4. apt-get install docker-ce docker-ce-cli containerd.io
5. docker version
6. systemctl start docker && systemctl enable docker
```
1. 安装前检查是否已经安装docker，删除已有的docker。如果你已经装好了可以不用安装。
2. 更新apt-get工具，下载相关软件。没看明白，感觉是自己去下载一些软件，为了后续GPG的事情。
3. 添加docker官方的GPG key。什么是GPG，为什么要安装GPG。GPG是一种软件加密方式，知道的加密方式还是有RSA。安装GPG是为了保证软件的安全可靠性，这一步不是非必须。
4. 安装docker引擎。关键步骤。
5. 验证docker是否安装成功。 
6. 启动docker且设置为开机启动。
7. ref:https://docs.docker.com/engine/install/
### Nvidia For Docker
#### 设置stable存储库和GPG密钥
```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
&& curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add - \
&& curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```   
#### 更新软件包清单后，安装NVIDIA运行时软件包（及其依赖项）
```
sudo apt-get update \&& 
sudo apt-get install -y nvidia-docker2
```
#### 修改添加/etc/docker/daemon.json
```
{
   "default-runtime": "nvidia",
   "runtimes": {
        "nvidia": {
            "path": "/usr/bin/nvidia-container-runtime",
            "runtimeArgs": []
      }
   }
}
```
#### 重新启动Docker守护程序以完成安装
```
sudo systemctl restart docker
```
## mysql
```
docker pull mysql:5.6.50
docker run -itd --name rmysql -p 3306:3306 -v docker-mysql:/var/lib/mysql  -e MYSQL_ROOT_PASSWORD=nj. mysql:5.6.50
docker cp /home/nj/book.sql container_name:/
docker exec -it rmysql bash
echo lower_case_table_names=1 >>/etc/mysql/mysql.conf.d/mysqld.cnf
exit
docker restart rmysql
docker exec -it rmysql bash
mysql -u root -p nj.;
create database ittm;
use ittm;
source /ittm.sql;
```
## samba
```
docker pull dperson/samba
mkdir -p /home/shares/app
chmod 777 /home/shares/app
docker run -it --name samba_docker -p 139:139 -p 445:445 -v /home/shares/app:/home/shares/shareA -d dperson/samba -w "WORKGROUP" -u "userA;123456789" -s "app;/home/shares/shareA;yes;no;yes;"
win
\\ip
linux
smb://ip
```
## kafka
```
docker pull wurstmeister/zookeeper && docker pull wurstmeister/kafka

docker run -itd --name zookeeper  -p 2181:2181 --network ai-model wurstmeister/zookeeper
docker run -itd --name kafka --network ai-model --publish 9092:9092 \
--link zookeeper \
--env KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 \
--env KAFKA_ADVERTISED_HOST_NAME=127.0.0.1 \
--env KAFKA_ADVERTISED_PORT=9092 \
wurstmeister/kafka

docker exec -it kafka /bin/bash

写入数据：/opt/kafka/bin/kafka-console-producer.sh --topic=test --broker-list localhost:9092
读出数据：/opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 -from-beginning --topic test
```
## privateResp
```
docker run -d -p 5000:5000 --restart=always --name registry registry
docker run -d \
    -p 5000:5000 \
    -v /opt/data/registry:/var/lib/registry \
    registry
docker tag ubuntu:latest 127.0.0.1:5000/ubuntu:latest
docker push 127.0.0.1:5000/ubuntu:latest
curl 127.0.0.1:5000/v2/_catalog
vim /etc/docker/daemon.json
{
  "registry-mirror": [
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com"
  ],
  "insecure-registries": [
    "192.168.199.100:5000"
  ]
}
```
# Dockerfile
## Java
```
FROM centos:7
RUN yum install java-1.8.0-openjdk* -y && \
    yum install vim -y && \
    echo set nu >> /etc/vimrc && \
    echo set fileencoding=utf-8 >> /etc/vimrc && \
    echo set tabstop=4 >> /etc/vimrc && \
    echo set autoindent  >> /etc/vimrc && \
    yum install -y maven -y && \
    yum install net-tools -y
```
## Python
```
FROM python:3.6
ENV PYTHONPATH :/app
WORKDIR /app
RUN echo deb http://mirrors.ustc.edu.cn/debian stable main contrib non-free > /etc/apt/sources.list && \
    echo deb http://mirrors.ustc.edu.cn/debian stable-updates main contrib non-free >> /etc/apt/sources.list && \
    echo nameserver 114.114.114.114 >> /etc/resolv.conf && \
    echo nameserver 8.8.8.8 >> /etc/resolv.conf && \
    apt-get update --allow-unauthenticated &&\
    apt-get install vim -y && \
    apt-get install less && \
    echo set nu >> /etc/vim/vimrc && \
    echo set fileencoding=utf-8 >> /etc/vim/vimrc && \
    echo set tabstop=4 >> /etc/vim/vimrc && \
    echo set autoindent  >> /etc/vim/vimrc && \
    pip config set global.index-url https://mirrors.aliyun.com/pypi/simple
```
## Python
```
FROM python:3.6
ENV PYTHONPATH :/app
WORKDIR /app
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        vim  net-tools curl less\
    && rm -rf /var/lib/apt/lists/*

COPY config/requirements.txt /app/

RUN pip config set global.index-url https://mirrors.aliyun.com/pypi/simple &&\
    pip install --no-cache-dir -r requirements.txt && \
    rm -r requirements.txt
```
## Nvidia
