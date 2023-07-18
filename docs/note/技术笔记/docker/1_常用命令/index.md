# Docker 常用命令

## 列出运行在本地 docker 主机上的全部网络

`docker network ls`

## 提供 Docker 网络的详细配置信息

`docker network inspect <NETWORK_NAME>`

## 创建新的单机桥接网络，名为 localnet，其中 -d 不指定的话，默认是 bridge 驱动。并且主机内核中也会创建一个新的网桥

`docker network create -d bridge localnet`

## 删除 Docker 主机上指定的网络

`docker network rm`

## 删除主机上全部未使用的网络

`docker network prune`

## 运行一个新的容器，并且让这个容器加入 Docker 的 localnet 这个网络中

`docker container run -d --name demo1 --network localnet alpine sleep 3600`

# 运行一个新的容器，并且让这个容器暴露 22、20 两个端口

`docker container run -d --name web --expose 22 --expose 20 nginx`

# 运行一个新的容器，并且将这个容器的 80 端口映射到主机的 5000 端口

`docker container run -d --name web --network localnet -p 5000:80 nginx`

## 查看系统中的网桥

`brctl show`
