Docker 是一个用于开发、交付和运行应用程序的开放平台。Docker 使您能够将应用程序与基础架构分开，从而可以快速交付软件。借助 Docker，您可以以与管理应用程序相同的方式来管理基础架构。通过利用 Docker 的快速交付、测试和部署代码的方法，您可以大大减少编写代码和在生产环境中运行代码之间的延迟。

## Docker 平台

Docker 提供了在松散隔离的环境（称为容器）中打包和运行应用程序的功能。隔离和安全性使您可以在给定主机上同时运行多个容器。容器是轻量级的，因为它们不需要管理程序的额外负担，而是直接在主机的内核中运行。这意味着与使用虚拟机相比，您可以在给定的硬件组合上运行更多的容器。您甚至可以在实际上是虚拟机的主机中运行 Docker 容器！

Docker 提供了工具和平台来管理容器的生命周期：

- 使用容器开发应用程序及其支持组件。
- 容器成为分发和测试应用程序的单元。
- 准备就绪后，可以将应用程序作为容器或协调服务部署到生产环境中。无论您的生产环境是本地数据中心、云提供商还是两者的混合，其工作原理都相同。

## Docker 引擎

_Docker Engine_ 是具有以下主要组件的客户端-服务器应用程序：

- 服务器是一种长期运行的程序，称为守护程序进程（`dockerd` 命令）。
- REST API，它指定程序可以用来与守护程序进行通信并指示其操作的接口。
- 命令行界面（CLI）客户端（`docker` 命令）。

![Docker引擎组件流程](../assets/engine-components-flow.png)

CLI 使用 Docker REST API 通过脚本或直接 CLI 命令控制或与 Docker 守护程序交互。许多其他 Docker 应用程序都使用基础 API 和 CLI。

守护程序创建和管理 Docker _对象_，例如图像，容器，网络和卷。

> **注意**：Docker 已获得开源 Apache 2.0 许可证的许可。

有关更多详细信息，请参阅下面的[Docker体系结构](#Docker架构)。

## 我可以将 Docker 用于什么？

**快速，一致地交付您的应用程序**

Docker 通过允许开发人员使用提供您的应用程序和服务的本地容器在标准化环境中工作，从而简化了开发生命周期。容器非常适合持续集成和持续交付（CI / CD）工作流程。

请考虑以下示例方案：

- 您的开发人员在本地编写代码，并使用 Docker 容器与同事共享他们的工作。
- 他们使用 Docker 将其应用程序推送到测试环境中，并执行自动和手动测试。
- 当开发人员发现错误时，他们可以在开发环境中对其进行修复，然后将其重新部署到测试环境中以进行测试和验证。
- 测试完成后，将修补程序推送给生产环境就像将更新的映像推送到生产环境一样简单。

**响应式部署和扩展**

Docker 基于容器的平台允许高度可移植的工作负载。Docker 容器可以在开发人员的本地笔记本电脑上，数据中心中的物理或虚拟机上、云提供商上或混合环境中运行。

Docker 的可移植性和轻量级的特性还使您可以轻松地动态管理工作负载，并根据业务需求指示实时扩展或关闭应用程序和服务。

**在同一硬件上运行更多工作负载**

Docker 轻巧快速。它为基于虚拟机管理程序的虚拟机提供了可行且经济高效的替代方案，因此您可以利用更多的计算能力来实现业务目标。Docker 非常适合高密度环境和中小型部署，而您需要用更少的资源做更多的事情。

## Docker 架构

Docker 使用客户端-服务器架构。Docker *客户端* 与 Docker *守护进程* 进行对话，该 *守护进程* 完成了构建，运行和分发 Docker 容器的繁重工作。Docker 客户端和守护程序 *可以* 在同一系统上运行，或者您可以将 Docker 客户端连接到远程 Docker 守护程序。Docker 客户端和守护程序在 UNIX 套接字或网络接口上使用 REST API 进行通信。

![Docker架构图](../assets/architecture.svg)

### Docker 守护程序

Docker 守护程序（`dockerd`）侦听 Docker API 请求并管理 Docker 对象，例如镜像、容器、网络和数据卷。守护程序还可以与其他守护程序通信以管理 Docker 服务。

### Docker 客户端

Docker 客户端（`docker`）是许多 Docker 用户与 Docker 交互的主要方式。当您使用诸如之类的命令时 `docker run`，客户端会将这些命令发送到 `dockerd`，以执行这些命令。该 `docker` 命令使用 Docker API。Docker 客户端可以与多个守护程序通信。

### Docker 注册表

Docker *注册表* 存储 Docker 镜像。Docker Hub 是任何人都可以使用的公共注册表，并且 Docker 配置为默认在 Docker Hub 上查找映像。您甚至可以运行自己的私人注册表。如果使用 Docker 数据中心（DDC），则其中包括 Docker 可信注册表（DTR）。

使用 `docker pull` 或 `docker run` 命令时，所需的图像将从配置的注册表中提取。使用该 `docker push` 命令时，会将镜像推送到配置的注册表。

### Docker 对象

使用 Docker 时，您正在创建和使用镜像、容器、网络、卷、插件和其他对象。本节是其中一些对象的简要概述。

#### 镜像（Image）

一个 *镜像* 是用于创建一个 Docker 容器指令的只读模板。通常，一个镜像 *基于* 另一个镜像，并进行一些其他自定义。例如，您可以基于该 `ubuntu` 镜像构建镜像，但安装 Apache Web 服务器和您的应用程序，以及运行该应用程序所需的配置详细信息。

您可以创建自己的镜像，也可以仅使用其他人创建并在注册表中发布的镜像。要构建自己的镜像，您可以 使用简单的语法创建一个 _Dockerfile_，以定义创建镜像并运行它所需的步骤。Dockerfile 中的每条指令都会在镜像中创建一个层。更改 Dockerfile 并重建镜像时，仅重建那些已更改的层。与其他虚拟化技术相比，这是使镜像如此轻巧、小型和快速的部分原因。

#### 容器（Container）

容器是镜像的可运行实例。您可以使用 Docker API 或 CLI 创建、启动、停止、移动或删除容器。您可以将容器连接到一个或多个网络，将存储附加到该网络，甚至根据其当前状态创建新映像。

默认情况下，容器与其他容器及其主机之间的隔离程度相对较高。您可以控制容器的网络，存储或其他基础子系统与其他容器或与主机的隔离程度。

容器由其镜像以及在创建或启动时为其提供的任何配置选项定义。删除容器后，未存储在持久性存储中的状态更改将消失。

##### 示例 `docker run` 命令

以下命令运行一个 `ubuntu` 容器，以交互方式附加到本地命令行会话，然后运行 `/bin/bash`。

```bash
$ docker run -i -t ubuntu /bin/bash
```

当您运行此命令时，会发生以下情况（假设您使用的是默认注册表配置）：

1. 如果 `ubuntu` 在本地没有该映像，则 Docker 会将其从已配置的注册表中拉出，就像以 `docker pull ubuntu` 手动运行一样。
2. Docker 会创建一个新容器，就像您已 `docker container create` 手动运行命令一样。
3. Docker 将一个读写文件系统分配给容器，作为其最后一层。这允许运行中的容器在其本地文件系统中创建或修改文件和目录。
4. Docker 创建了一个网络接口以将容器连接到默认网络，因为您未指定任何网络选项。这包括为容器分配 IP 地址。默认情况下，容器可以使用主机的网络连接连接到外部网络。
5. Docker 启动容器并执行 `/bin/bash`。因为容器是交互式运行的，并且已附加到您的终端（由于 `-i` 和 `-t` 标志），所以您可以在输出记录到终端时使用键盘提供输入。
6. 当您键入 `exit` 以终止 `/bin/bash` 命令时，容器将停止但不会被删除。您可以重新启动或删除它。

#### 服务

服务允许你扩展在多个 Docker 守护进程的容器，这是所有工作一起作为一个 *群（Group）* 有多个 *管理人员* 和 *工人*。一个集群的每个成员都是一个 Docker 守护程序，并且所有守护程序都使用 Docker API 进行通信。服务允许您定义所需的状态，例如在任何给定时间必须可用的服务副本数。默认情况下，该服务在所有工作节点之间实现负载平衡。对于消费者而言，Docker 服务似乎是一个单独的应用程序。Docker Engine 在 Docker 1.12 及更高版本中支持集群模式。

## 底层技术

Docker 用 [Go](https://golang.org/) 编写，并利用 Linux 内核的多个功能来交付其功能。

### 命名空间

Docker 使用一种称为 `namespaces` 提供 _容器_ 的隔离工作区的技术。运行容器时，Docker 会为该容器创建一组 _名称空间_。

这些名称空间提供了一层隔离。容器的每个方面都在单独的名称空间中运行，并且其访问仅限于该名称空间。

Docker Engine 在 Linux 上使用以下名称空间：

- **`pid` 命名空间：**进程隔离（PID：进程 ID）。
- **`net` 命名空间：**管理网络接口（NET：网络）。
- **`ipc` 命名空间：**管理访问IPC资源（IPC：进程间通信）。
- **`mnt` 命名空间：**管理文件系统挂载点（MNT：挂载）。
- **`uts` 命名空间：**隔离内核和版本标识符。（UTS：Unix时间共享系统）。

### 控制组

Linux上 的 Docker Engine 还依赖于另一种称为 _控制组_ （`cgroups`）的技术。cgroup 将应用程序限制为一组特定的资源。控制组允许 Docker Engine 将可用的硬件资源共享给容器，并有选择地实施限制和约束。例如，您可以限制特定容器可用的内存。

### 联合文件系统

联合文件系统（UnionFS）是通过创建图层进行操作的文件系统，使其非常轻便且快速。Docker Engine 使用 UnionFS 为容器提供构建模块。Docker Engine 可以使用多个 UnionFS 变体，包括 AUFS、btrfs、vfs 和 DeviceMapper。

### 容器格式

Docker Engine 将名称空间、控制组和 UnionFS 组合到一个称为容器格式的包装器中。默认容器格式为 `libcontainer`。将来，Docker 可以通过与 BSD Jails 或 Solaris Zones 等技术集成来支持其他容器格式。

## Docker 仓库

镜像构建完成后，可以很容易的在当前宿主机上运行，但是，如果需要在其它服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，`Docker Registry` 就是这样的服务。

一个 **Docker Registry** 中可以包含多个**仓库**（`Repository`）；每个仓库可以包含多个**标签**（`Tag`）；每个标签对应一个镜像。

通常，一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的各个版本。我们可以通过 `<仓库名>:<标签>` 的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 `latest` 作为默认标签。

以 [Ubuntu 镜像](https://store.docker.com/images/ubuntu) 为例，`ubuntu` 是仓库的名字，其内包含有不同的版本标签，如，`14.04`, `16.04`。我们可以通过 `ubuntu:14.04`，或者 `ubuntu:16.04` 来具体指定所需哪个版本的镜像。如果忽略了标签，比如 `ubuntu`，那将视为 `ubuntu:latest`。

仓库名经常以 *两段式路径* 形式出现，比如 `jwilder/nginx-proxy`，前者往往意味着 Docker Registry 多用户环境下的用户名，后者则往往是对应的软件名。但这并非绝对，取决于所使用的具体 Docker Registry 的软件或服务。

### 公有 Docker Registry

Docker Registry 公开服务是开放给用户使用、允许用户管理镜像的 Registry 服务。一般这类公开服务允许用户免费上传、下载公开的镜像，并可能提供收费服务供用户管理私有镜像。

最常使用的 Registry 公开服务是官方的 [Docker Hub](https://hub.docker.com/)，这也是默认的 Registry，并拥有大量的高质量的官方镜像。除此以外，还有 [CoreOS](https://coreos.com/) 的 [Quay.io](https://quay.io/repository/)，CoreOS 相关的镜像存储在这里；Google 的 [Google Container Registry](https://cloud.google.com/container-registry/)，[Kubernetes](http://kubernetes.io/) 的镜像使用的就是这个服务。

由于某些原因，在国内访问这些服务可能会比较慢。国内的一些云服务商提供了针对 Docker Hub 的镜像服务（`Registry Mirror`），这些镜像服务被称为 **加速器**。常见的有 [阿里云加速器](https://cr.console.aliyun.com/#/accelerator)、[DaoCloud 加速器](https://www.daocloud.io/mirror#accelerator-doc) 等。使用加速器会直接从国内的地址下载 Docker Hub 的镜像，比直接从 Docker Hub 下载速度会提高很多。

国内也有一些云服务商提供类似于 Docker Hub 的公开服务。比如 [时速云镜像仓库](https://hub.tenxcloud.com/)、[网易云镜像服务](https://c.163.com/hub#/m/library/)、[DaoCloud 镜像市场](https://hub.daocloud.io/)、[阿里云镜像库](https://cr.console.aliyun.com/) 等。

### 私有 Docker Registry

除了使用公开服务外，用户还可以在本地搭建私有 Docker Registry。Docker 官方提供了 [Docker Registry](https://store.docker.com/images/registry/) 镜像，可以直接使用做为私有 Registry 服务。

开源的 Docker Registry 镜像只提供了 [Docker Registry API](https://docs.docker.com/registry/spec/api/) 的服务端实现，足以支持 `docker` 命令，不影响使用。但不包含图形界面，以及镜像维护、用户管理、访问控制等高级功能。在官方的商业化版本 [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/) 中，提供了这些高级功能。

除了官方的 Docker Registry 外，还有第三方软件实现了 Docker Registry API，甚至提供了用户界面以及一些高级功能。比如，[VMWare Harbor](https://github.com/vmware/harbor) 和 [Sonatype Nexus](https://www.sonatype.com/docker)。