# 通过CICD流程把微服务应用部署在容器集群中

本次学习过程会通过挑战的方式进行。通过完成各项挑战环节，大家会学习到如何在Azure云端创建容器集群及私有镜像仓库；如何将自己的应用部署到云端；如何使用CICD即持续集成持续部署的方式来进行这些工作的自动化构建。

## 背景介绍

在我们完成挑战之前，首先可以先熟悉一些基本的技术知识，如果大家已经对这些理论知识比较了解，则可以忽略相关的知识继续往下走。

* 当我们希望我们的应用程序运行时不受平台其他程序干扰，同时能够在不同的平台绿色迁移部署时，我们需要容器技术进行隔离。Docker作为容器技术中最受欢迎的技术有哪些特点和优势。[参考文档](https://docs.microsoft.com/zh-cn/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)
* 当我们的应用程序需要多个不同的容器来提供不同的服务时，不同容器之间如何通信，容器如何做扩展等等，我们需要容器编排工具来进行编排。Kubernetes作为至今在三方社区中最受欢迎的编排工具，基本概念介绍如下。[参考文档](https://kubernetes.io/docs/concepts/)

## 准备工作

在熟悉了相关容器基础知识后，我们可以来完成挑战前的准备：
* 我们需要使用一个现有的应用程序完成挑战，你也可以使用自己的程序完成挑战，也可以使用提供的相关应用程序；该应用程序基于visual studio 2017 和 .net core, 是一个web应用，用来进行不同API或者不同功能的测试。用来模拟微服务应用程序。程序下载地址可见如下[github地址](https://github.com/DandelionWenjing/API-Playground)
* 我们需要在自己的开发环境中安装docker，可以使用命令`docker images`测试是否安装成功:
[安装参考文档](https://docs.docker.com/install/)
* 我们需要在开发环境中安装Azure-cli工具：
[安装参考文档](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
* 确保自己有Azure global账号
* 确保自己有VSTS账号，[如果没有账号，可以参考如下文档创建免费vsts账号](https://www.visualstudio.com/zh-hans/)

## 挑战1

首先我们需要将应用程序在本地进行容器镜像的打包并运行起来，查看应用程序本地运行后的效果。

### 挑战步骤
- 步骤1
    - 将github中的代码下载到本地，用visual studio打开，并[添加linux visual studio docker支持](https://docs.microsoft.com/en-us/dotnet/standard/containerized-lifecycle-architecture/design-develop-containerized-apps/visual-studio-tools-for-docker)
    - 添加visual studio docker 支持后，生成相关的dockerfile文件
    - 查看添加支持后代码的变化，并成功运行代码
    - [查看创建的镜像个数，名称](https://docs.docker.com/engine/reference/commandline/images/)
    - 查看容器镜像的创建结果(web页面成功运行)
- 步骤2
    - 确认自己的vsts账户处于可以使用状态，创建vsts项目，并将添加过docker支持的代码导入到该项目中。
    - 这里我们可以使用VSTS代码版本管理，也可以使用Git repo来进行代码的版本管理，对于VSTS代码版本管理这里更加方便，因为我们可以构建私有的代码版本管理库。[参考文档](https://docs.microsoft.com/en-us/vsts/accounts/create-team-project?view=vsts)
    - 注意如果是团队共同的vsts项目，你需要是该项目的管理员才能够顺利完成。

## 挑战2

为了将代码部署到云端的容器集群中，我们首先需要创建Azure平台上的容器集群以及私有镜像仓库。

### 挑战步骤
- 步骤1
    - 使用Azure工具AKS服务创建Azure云平台kubernetes容器集群。[参考文档](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough)
    - AKS集群可以选择使用命令行工具进行创建，也可以选择在portal上进行创建。
    - 下载kube config文件，使用Azure命令行工具进行集群的连接
    - 安装相关命令行工具：kubectl [参考文档](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
    - 使用kubectl查看容器集群的状态，获取容器集群的名称，节点信息，pod信息。[参考文档](https://kubernetes.io/docs/reference/kubectl/overview/)
    - 登陆到kubernetes dashboard上查看集群状态
- 步骤2
    - 使用Azure工具ACR服务创建Azure云平台私有镜像仓库[参考文档](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-azure-cli)
    - 查看当前镜像仓库的login server以及密码
    - 使用azure命令行工具登陆到ACR私有镜像仓库中

## 挑战3

在之前的挑战过程中，我们完成了基本环境创建的过程，查看了代码运行的结果。在本次挑战过程中，我们将这一步骤进行自动化。首先我们使用CICD的工具完成CI过程的自动化。

### 挑战步骤
- 步骤1
    - 使用vsts或者第三方工具jenkins简单创建自己的CI即持续集成过程。这里建议使用vsts来进行CI过程的构建。   
    [vsts ASP .net core build参考文档](https://docs.microsoft.com/en-us/vsts/build-release/apps/aspnet/build-aspnet-core?view=vsts&tabs=gitvsts%2Cweb%2Cdeploy-windows)  
    [vsts docker image build 参考文档](https://docs.microsoft.com/en-us/vsts/build-release/apps/containers/build?view=vsts&tabs=web)
    [jenkins参考文档](https://www.pgs-soft.com/blog/cross-platform-application-using-net-core-jenkins-docker/)  
    - 注意当trigger完成后，我们在进行代码提交更改时，会自动触发代码的构建。
- 步骤2
    - 我们首先可以在master branch中进行该操作，如果感兴趣，可以学习如何对其他branch进行pull request的自动化构建操作。[参考文档](https://docs.microsoft.com/en-us/vsts/build-release/actions/ci-build-git?view=vsts)

## 挑战4

在上一挑战过程中，我们完成CI这个部分的自动化构建，这个过程自动化了打包镜像，并将镜像推送到镜像仓库中，而还没有执行运行和部署的过程。我们在接下来的挑战中，要完成将容器部署到容器集群中去，即完成CD的过程的自动化构建。

###挑战步骤
- 步骤1
    - 将Azure container service中的身份验证添加到VSTS的pipeline中，同时添加CD持续交付的trigger.
- 步骤2
    - 实现进行代码版本更新的时候，可以自动触发持续交付的trigger,部署新的代码版本到Azure容器集群中。
- [参考文档](https://docs.microsoft.com/en-us/vsts/build-release/actions/azure-devops-project-aks?view=vsts)

挑战愉快:)







