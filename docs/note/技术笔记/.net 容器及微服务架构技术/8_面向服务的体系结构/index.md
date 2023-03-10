# Service-oriented architecture

SOA是个过度使用的术语，不同的人有不同的理解。

共同的理解是：SOA 意味着通过将应用进程分解为多个服务（最常见的是 HTTP 服务）来构建应用进程，这些服务可以分类为不同类型的服务，如子系统或层。

## 容器化

SOA可被部署为Docker容器，因为image中包含所有依赖关系。
但当需要纵向扩展的时候，如果基于单个 Docker 主机进行部署，则可能会遇到可伸缩性和可用性挑战。Docker 群集软件或业务流程协调进程可以提供帮助。

## 跟微服务的关系

微服务源自SOA，但SOA不同于微服务体系。如大型中央代理，组织级别的中央业务流程协调程序和企业服务总线ESB等功能在SOA很典型。
但在大多数情况下，这些事微服务中的反模式。

事实上，有些人认为 微服务体系是处理得当的SOA。

**主要关注微服务体系即可，当学会了微服务程序开发，也就学会SOA程序开发。** 因为 SOA 方法的规定性低于微服务体系结构中使用的要求和技术
