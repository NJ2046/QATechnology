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
