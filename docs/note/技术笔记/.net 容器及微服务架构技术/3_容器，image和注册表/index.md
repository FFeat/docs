# 容器，image和注册表直接的关系

## 描述

![Docker 术语和概念的分类](images/2023-01-18-10-05-32.png)

Image是应用或服务及其配置和依赖项的静态表示形式。

运行运用，image将会实例化，创建一个在Docker主机上运行的容器。

Image存储在注册表中。注册表可充当Image库并在部署到生产业务流程协调程序时使用。

Docker Hub维护公共注册表

# 使用私有注册表的情况

1. 基于保密性，不能被公开分享
2. 在image和部署环境之间，希望网络延迟保持在最低。 
    > 如果生产环境是 Azure 云，为实现最低的网络延迟，需要将映像存储在 Azure 容器注册表中。
    如果生产环境是在本地，便需要使本地 Docker 信任的注册表在相同的本地网络中可用。