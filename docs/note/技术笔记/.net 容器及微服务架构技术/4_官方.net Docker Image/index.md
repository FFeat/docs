# 官方 .NET Docker 映像

官方 .NET Docker 映像是由 Microsoft 创建和优化的 Docker 映像。

## 地址

1. [Docker Hub](https://hub.docker.com/_/microsoft-dotnet/)

## 开发环境和生产环境的image选择

### 开发阶段

1. 更快速的迭代更改
2. 调试更改的能力

如开发image：**mcr.microsoft.com/dotnet/sdk:6.0**

### build阶段

选择同上，用于构建放置在生产image中的内容。
使用Docker多阶段生成时，该image将在CI和build环境中使用。

### 生产环境

生产环境中最重要的是**基于生产部署和启动容器的速度**

基于mcr.microsoft.com/dotnet/aspnet:6.0的image很小，它可以在网络中从注册表中快速传输到docker主机。而内容是已经准备好运行的，从而实现启动容器到处理结果的最快速度。

仅放置了应用进程所需的二进制文档和其他内容。
 如`dotnet publish`生成的内容，仅包含已编译的.net二进制文档、图像、js和css文档。
 后期可能会增加预编译功能的image.

## 磁盘占用

 虽然 .NET 和 ASP.NET Core image有多个版本，但它们全都共享一个或多个层，包括基本层。 因此，存储image所需的磁盘空间量很小；它仅包含自定义image和其基础image之间的增量。 所以，从注册表中提取image速度会很快。

## 版本

[git地址](https://github.com/dotnet/dotnet-docker)

|类型|描述|地址和介绍|
|--|--|--|
|dotnet/sdk|.net SDK|[docker地址](https://hub.docker.com/_/microsoft-dotnet-sdk/)，包含三个部分：.net cli, .net runtime,asp.net core|
|dotnet/aspnet|asp.net core runtime|[docker地址](https://hub.docker.com/_/microsoft-dotnet-aspnet/)，此图像包含 ASP.NET Core 和 .NET 运行时和库，并针对在生产中运行 ASP.NET Core 应用程序进行了优化。|
|dotnet/runtime|.net runtime|[docker地址](https://hub.docker.com/_/microsoft-dotnet-runtime/)，此映像包含 .NET 运行时和库，并针对在生产中运行 .NET 应用程序进行了优化。|
|dotnet/runtime-deps|.net Runtime Dependencies|[docker地址](https://hub.docker.com/_/microsoft-dotnet-runtime-deps/)，此映像包含 .NET 所需的本机依赖项。它不包括 .NET。它适用于独立的应用程序。|
|dotnet/monitor|.net Monitor Tool|[docker地址](https://hub.docker.com/_/microsoft-dotnet-monitor/)，此映像包含 .NET Monitor 工具。将此图像用作 sidecar 容器，以从运行 .NET Core 3.1 或更高版本进程的其他容器收集诊断信息。|
|dotnet/samples|.net Samples|[docker地址](https://hub.docker.com/_/microsoft-dotnet-samples/)，这些图像包含示例 .NET 和 ASP.NET Core 应用程序。|
