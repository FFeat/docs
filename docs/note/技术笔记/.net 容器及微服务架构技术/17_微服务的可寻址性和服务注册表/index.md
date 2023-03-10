# 微服务可寻址性和服务注册表

每个微服务都有一个用于解析其位置的唯一名称 （URL）。
微服务需要再任何地方运行都是可寻址得。*如果靠个人去判定哪台计算机正在运行哪个微服务，将会变得很麻烦*。
为了方便寻址，与DNS解析特定计算机得URL的方式相同，微服务需要具有唯一的名称，以便可发现其当前位置。
微服务需要可寻址的名称，让其独立于运行它们的基础结构。意味着服务部署方式与服务发现之间存在交互。**因此需要一个服务注册表**。当计算机出故障时，注册表服务也能够指出服务的位置。


**服务注册表模式**是发现服务的一个关键部分。

注册表**包含服务实例的网络位置的数据库。**
注册表**需保持高度可用且是最新状态**。
·41
、07客户端可缓存从服务注册表获得的网络位置。但是该消息最终也会过时，客户端便无法再发现服务实例。

**因此，服务注册表包含了一个服务器集群，使用复制协议来维持一致性。**

在某些微服务部署环境中(集群)，服务发现是内置的。
*如Azure Kubernetes 服务 (AKS) 环境可以处理服务实例注册和注销。 它还可在每个群集主机（充当服务器端发现路由器角色）上运行一个代理。*