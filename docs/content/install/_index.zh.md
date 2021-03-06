---
title: "安装 Athens"
date: 2018-09-20T15:38:01-07:00
weight: 2
---

Go的生态系统一直是联合的，完全开放的。任何拥有GitHub或GitLab（或任何其他支持的VCS）帐户的人都可以轻松地通过git push（或类似命令）来提供库服务。 无需创建额外的帐户或设置凭据。

## 一个联合的生态系统

我们认为Athens应该保持社区的联合与开放，并且用户在构建应用程序和库时，无需更改工作流。 因此，为确保社区能够保持联合与开放，我们已为每个人提供了轻松安装Athens的方式，以便：

-任何人都可以运行自己的全功能镜像，无论是公共镜像还是私有镜像
-任何组织都可以运行自己的私有镜像，这样他们就可以像管理公共代码一样管理自己的私有代码


## 不变性

如您所知，`go get`和`go mod download` 命令将直接从GitHub等版本控制系统中获取软件包。 这个方式对于软件包开发人员和相关应用程序都非常好，但是与此同时，我们长期以来一直遇到一个基本问题。即，版本控制系统中的代码即使在提交之后也可以随时更改。例如，包开发人员可以运行"git push -f"并覆盖项目中依赖的提交或标记。在这些情况下，您经常会看到校验和验证错误，相关例子可参见[这里](https://github.com/go-ole/go-ole/issues/185)

_Athens通过将代码存储在其自己的不可变数据库中_ 来防止这些问题的发生。下面是当你运行`go get`时，发生的交互情况：

1. `go get`向Athens请求一个模块
2. Athens接受请求并开始搜寻该模块
3. 首先，在后端存储中查找，如果找到了这个模块，Athens将立即返回（1）中发出请求的`go get` 客户端
4. 如果找不到这个模块，则从版本控制系统中获取模块，保存在后端存储中，然后返回到（1）中的客户端


一旦将模块保存到存储中，Athens就不会更改任何内容，因此Athens具有以下两个重要属性：

- _对某个模块的一个版本，Athens永远只调用 `go mod download` **一次** 。换句话说，对于任何给定的模块和版本，Athens只会执行步骤（4）一次。_ 
- _Athens处理存储的策略为仅追加，因此一旦模块被保存，它就永远不会改变，即使开发人员在GitHub上对其进行了更改_

## 发布规则

我们遵循[semver语义化版本号](https://semver.org)。 同时我们的Docker镜像打上了下列标签以表示镜像的稳定性:

* latest = 最新的稳定版本
* canary = mater分支最新构建出的版本

我们强烈建议您使用带标签的发行版镜像，例如 `gomods/athens:v0.3.0`, 而不是使用latest或canary标签的镜像。

## 拓展资源

为了确保易于安装，我们尝试提供尽可能多的方式来安装和运行Athens:

- Athens是用Go语言编写的，因此您可以轻松地在几乎任何平台上自行构建它。您还可以根据自己的版本号和构建时间来构建二进制文件. See [here](./build-from-source)
- 我们提供了 [Docker 镜像](https://hub.docker.com/r/gomods/athens/) 以及 [如何运行的指导](./shared-team-instance)
- 我们提供了 [Kubernetes](https://kubernetes.io)上的 [Helm Charts](https://helm.sh) 以及 [如何在Kubernetes上运行Athens的指导](./install-on-kubernetes)
