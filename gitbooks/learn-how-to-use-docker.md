# Learn How To Use Docker

## 1、前言

我们在理解 `docker` 之前，首先我们得先区分清楚两个概念，**容器**和**虚拟机**。

可能很多读者朋友都用过虚拟机，而对容器这个概念比较的陌生。

我们用的传统虚拟机如 `VMware` ， `VisualBox` 之类的需要模拟整台机器包括硬件，每台虚拟机都需要有自己的操作系统，虚拟机一旦被开启，预分配给它的资源将全部被占用。每一台虚拟机包括应用，必要的二进制和库，以及一个完整的用户操作系统。

而容器技术是和我们的宿主机共享硬件资源及操作系统，可以实现资源的动态分配。容器包含应用和其所有的依赖包，但是与其他容器共享内核。容器在宿主机操作系统中，在用户空间以分离的进程运行。

容器技术是实现操作系统虚拟化的一种途径，可以让您在资源受到隔离的进程中运行应用程序及其依赖关系。通过使用容器，我们可以轻松打包应用程序的代码、配置和依赖关系，将其变成容易使用的构建块，从而实现环境一致性、运营效率、开发人员生产力和版本控制等诸多目标。容器可以帮助保证应用程序快速、可靠、一致地部署，其间不受部署环境的影响。容器还赋予我们对资源更多的精细化控制能力，让我们的基础设施效率更高。通过下面这幅图我们可以很直观的反映出这两者的区别所在。

 ![img](img/learn-how-to-use-docker/1100338-20181010205426157-1788702025.png)

**Docker 属于 Linux 容器的一种封装，提供简单易用的容器使用接口。**它是目前最流行的 `Linux` 容器解决方案。

而  `Linux 容器` 是 Linux 发展出了另一种虚拟化技术，简单来讲， `Linux 容器` 不是模拟一个完整的操作系统，而是对进程进行隔离，相当于是在正常进程的外面套了一个保护层。对于容器里面的进程来说，它接触到的各种资源都是虚拟的，从而实现与底层系统的隔离。

`Docker` 将应用程序与该程序的依赖，打包在一个文件里面。运行这个文件，就会生成一个虚拟容器。程序在这个虚拟容器里运行，就好像在真实的物理机上运行一样。有了 `Docker` ，就不用担心环境问题。

总体来说， `Docker` 的接口相当简单，用户可以方便地创建和使用容器，把自己的应用放入容器。容器还可以进行版本管理、复制、分享、修改，就像管理普通的代码一样。

## 2、Docker的优势

Docker相比于传统虚拟化方式具有更多的**优势**：

- `docker` 启动快速属于秒级别。虚拟机通常需要几分钟去启动
- `docker` 需要的资源更少， `docker` 在操作系统级别进行虚拟化， `docker` 容器和内核交互，几乎没有性能损耗，性能优于通过 `Hypervisor` 层与内核层的虚拟化
- `docker` 更轻量， `docker` 的架构可以共用一个内核与共享应用程序库，所占内存极小。同样的硬件环境， `Docker` 运行的镜像数远多于虚拟机数量，对系统的利用率非常高
- 与虚拟机相比， `docker` 隔离性更弱， `docker` 属于进程之间的隔离，虚拟机可实现系统级别隔离
- 安全性： `docker` 的安全性也更弱。 `Docker` 的租户 `root` 和宿主机 `root` 等同，一旦容器内的用户从普通用户权限提升为root权限，它就直接具备了宿主机的root权限，进而可进行无限制的操作。虚拟机租户 `root` 权限和宿主机的 `root` 虚拟机权限是分离的，并且虚拟机利用如 `Intel` 的 `VT-d` 和 `VT-x` 的 `ring-1` 硬件隔离技术，这种隔离技术可以防止虚拟机突破和彼此交互，而容器至今还没有任何形式的硬件隔离，这使得容器容易受到攻击
- 可管理性： `docker` 的集中化管理工具还不算成熟。各种虚拟化技术都有成熟的管理工具，例如 `VMware vCenter` 提供完备的虚拟机管理能力
- 高可用和可恢复性： `docker` 对业务的高可用支持是通过快速重新部署实现的。虚拟化具备负载均衡，高可用，容错，迁移和数据保护等经过生产实践检验的成熟保障机制， `VMware` 可承诺虚拟机 `99.999%` 高可用，保证业务连续性
- 快速创建、删除：虚拟化创建是分钟级别的， `Docker` 容器创建是秒级别的， `Docker` 的快速迭代性，决定了无论是开发、测试、部署都可以节约大量时间
- 交付、部署：虚拟机可以通过镜像实现环境交付的一致性，但镜像分发无法体系化。 `Docker` 在 `Dockerfile` 中记录了容器构建过程，可在集群中实现快速分发和快速部署

我们可以从下面这张表格很清楚地看到容器相比于传统虚拟机的特性的优势所在：

|    特性    |        容器        |    虚拟机    |
| ---------- | ------------------ | ------------ |
| 启动       | 秒级               | 分钟级       |
| 硬盘使用   | 一般为MB           | 一般为GB     |
| 性能       | 接近原生           | 弱于         |
| 系统支持量 | 单机支持上千个容器 | 一般是几十个 |

## 3、Docker的三个基本概念

  ![img](img/learn-how-to-use-docker/1100338-20181010205425908-509725301.jpg)

从上图我们可以看到，`Docker` 中包括三个基本的概念：

- `Image`(镜像)
- `Container`(容器)
- `Repository`(仓库)

镜像是 `Docker` 运行容器的前提，仓库是存放镜像的场所，可见镜像更是 `Docker` 的核心。

### 3.1 `Image (镜像)`

那么镜像到底是什么呢？

`Docker 镜像` 可以看作是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。

镜像不包含任何动态数据，其内容在构建之后也不会被改变。

镜像`（Image）`就是一堆只读层`（read-only layer）`的统一视角，也许这个定义有些难以理解，下面的这张图能够帮助读者理解镜像的定义。

 ![img](img/learn-how-to-use-docker/1100338-20181010205425698-1711765011.png)

从左边我们看到了多个只读层，它们重叠在一起。除了最下面一层，其它层都会有一个指针指向下一层。这些层是`Docker` 内部的实现细节，并且能够在主机的文件系统上访问到。统一文件系统 `(union file system)` 技术能够将不同的层整合成一个文件系统，为这些层提供了一个统一的视角，这样就隐藏了多层的存在，在用户的角度看来，只存在一个文件系统。我们可以在图片的右边看到这个视角的形式。

### 3.2 `Container (容器)`

容器 `(container)` 的定义和镜像 `(image)` 几乎一模一样，也是一堆层的统一视角，唯一区别在于容器的最上面那一层是可读可写的。

 ![img](img/learn-how-to-use-docker/1100338-20181010205425262-960721404.png)

由于容器的定义并没有提及是否要运行容器，所以实际上，容器 = 镜像 + 读写层。

### 3.3 `Repository (仓库)`

`Docker仓库` 是集中存放镜像文件的场所。

镜像构建完成后，可以很容易的在当前宿主上运行，但是， 如果需要在其它服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，`Docker Registry` (仓库注册服务器)就是这样的服务。

有时候会把仓库 `(Repository)` 和仓库注册服务器 `(Registry)` 混为一谈，并不严格区分。

`Docker 仓库` 的概念跟 `git` 类似，注册服务器可以理解为 `GitHub` 这样的托管服务。
> 实际上，一个 `Docker Registry` 中可以包含多个仓库 `(Repository)` ，每个仓库可以包含多个标签 `(Tag)`，每个标签对应着一个镜像。所以说，镜像仓库是Docker用来集中存放镜像文件的地方类似于我们之前常用的代码仓库。

通常，一个仓库会包含同一个软件不同版本的镜像，而 **`标签`** 就常用于对应该软件的各个版本。

我们可以通过`<仓库名>:<标签>`的格式来指定具体是这个软件哪个版本的镜像。
> 如果不给出标签，将以 `latest` 作为默认标签.。

仓库又可以分为两种形式：

- `public`(公有仓库)
- `private`(私有仓库)

`Docker Registry` 公有仓库是开放给用户使用、允许用户管理镜像的 `Registry` 服务。一般这类公开服务允许用户免费上传、下载公开的镜像，并可能提供收费服务供用户管理私有镜像。

除了使用公开服务外，用户还可以在本地搭建私有Docker Registry。Docker官方提供了 `Docker Registry` 镜像，可以直接使用做为私有Registry服务。当用户创建了自己的镜像之后就可以使用 push 命令将它上传到公有或者私有仓库，这样下次在另外一台机器上使用这个镜像时候，只需要从仓库上 pull 下来就可以了。

### 3.4 Docker三要素的关系

我们主要把 Docker 的一些常见概念如 Image，Container，Repository 做了详细的阐述，也从传统虚拟化方式的角度阐述了 Docker 的优势，我们从下图可以直观地看到 Docker 的架构：

 ![framework](img/learn-how-to-use-docker/1100338-20181011200344086-1510826338.png)

Docker 使用 **`C/S(客户端/服务器)`** 体系结构。

Docker客户端与 Docker服务器进行交互，Docker服务端负责构建、运行和分发 `Docker` 镜像。

Docker客户端和服务端可以运行在一台机器上，也可以通过 `RESTful`、`stock` 或网络接口与远程Docker服务端进行通信。

 ![docker-framework](img/learn-how-to-use-docker/1100338-20181011200343656-1972949758.png)

这张图展示了 Docker客户端、服务端和 Docker仓库（即 `Docker Hub` 和 `Docker Cloud` ），默认情况下Docker会在 `Docker 中央仓库` 寻找镜像文件，这种利用仓库管理镜像的设计理念类似于 `Git` ，当然这个仓库是可以通过修改配置来指定的，甚至我们可以创建我们自己的私有仓库。

## 4、Docker的安装和使用示例

Docker 的安装和使用有一些前提条件，主要体现在体系架构和内核的支持上。
> 对于体系架构，除了 `Docker` 一开始就支持的 `X86-64` ，其他体系架构的支持则一直在不断地完善和推进中。

`Docker` 分为 `CE` 和 `EE` 两大版本。

> `CE` 即社区版（免费，支持周期 `7` 个月）， `EE` 即企业版，强调安全，付费使用，支持周期 `24` 个月。

我们在安装前可以参看官方文档获取最新的 `Docker` 支持情况，官方文档在这里：

```bash
https://docs.docker.com/install/
```

Docker 对于内核支持的功能，即内核的配置选项也有一定的要求(比如必须开启 `Cgroup` 和 `Namespace` 相关选项，以及其他的网络和存储驱动等)， `Docker` 源码中提供了一个检测脚本来检测和指导内核的配置，脚本链接在这里：

```bash
wget -q https://raw.githubusercontent.com/docker/docker/master/contrib/check-config.sh -O - | bash
```

在满足前提条件后，安装就变得非常的简单了。

`Docker CE` 的安装请参考官方文档：[MacOS](<https://docs.docker.com/docker-for-mac/install>) [Windows](<https://docs.docker.com/docker-for-windows/install/>) [Ubuntu](<https://docs.docker.com/install/linux/docker-ce/ubuntu>) [Debian](<https://docs.docker.com/install/linux/docker-ce/debian>) [CentOS](<https://docs.docker.com/install/linux/docker-ce/centos>) [Fedora](<https://docs.docker.com/install/linux/docker-ce/fedora>)

### 4.1 Docker版本的 `Hello World`

我们通过最简单的 `image` 文件 `hello world`，感受一下 `Docker` 的魅力吧！

我们直接运行下面的命令，将名为 `hello-world` 的 `image` 文件从仓库抓取到本地。

```bash
# docker pull  <IMAGE-FILE-NAME>
docker pull library/hello-world
```

 `library/hello-world` 是 `image` 文件在仓库里面的位置，其中`library`是文件所在的组，`hello-world`是文件的名字。

抓取成功以后，就可以在本机看到这个 `image` 文件了。

```bash
docker images
```

现在，我们可以运行 `hello-world` 这个 `image` 文件

```bash
docker run hello-world
```

输出运行结果提示以后，`hello world` 就会停止运行，容器自动终止。有些容器不会自动终止，因为提供的是服务，比如 `MySQL` 镜像等。

## 5、Docker架构

`Docker` 提供了一套简单实用的命令来创建和更新镜像，我们可以通过网络直接下载一个已经创建好了的应用镜像，并通过 `Docker RUN` 命令就可以直接使用。当镜像通过 `RUN` 命令运行成功后，这个运行的镜像就是一个 `Docker` 容器啦，容器可以理解为一个轻量级的沙箱， `Docker` 利用容器来运行和隔离应用，容器是可以被启动、停止、删除的，这并不会影响 `Docker` 镜像。

我们可以看看下面这幅图：

 ![img](img/learn-how-to-use-docker/1100338-20181011200340694-1202370283.png)

`Docker` 客户端是 `Docker` 用户与 `Docker服务器端` 交互的主要方式。当您使用 `docker` 命令行运行命令时， `Docker` 客户端将这些命令发送给服务器端，服务端将执行这些命令。 `docker` 命令使用 `docker API` 。 `Docker` 客户端可以与多个服务端进行通信。

我们将剖析一下Docker容器是如何工作的，学习Docker容器工作的原理后，我们就可以自己去管理容器了。

在上面的学习中，我们简单地讲解了Docker的基本架构。了解到了`Docker` 使用的是 `C/S` 结构，即**客户端/服务器**体系结构。明白了 `Docker` 客户端与 `Docker` 服务器进行交互时， `Docker` 服务端负责构建、运行和分发 `Docker` 镜像。 也知道了`Docker` 客户端和服务端可以运行在一台机器上，可以通过 `RESTful` 、 `stock` 或网络接口与远程 `Docker` 服务端进行通信。

我们从下图可以很直观的了解到Docker的结构组成：

 ![img](img/learn-how-to-use-docker/1100338-20181012214103608-969706945.png)

Docker采用的是 `Client/Server` 架构。客户端向服务器发送请求，服务器负责构建、运行和分发容器。客户端和服务器可以运行在同一个 `Host` 上，客户端也可以通过 `socket` 或 `REST API` 与远程的服务器通信。

Docker的核心组件包括：`Docker Client`, `Docker daemon`, `Docker Image`, `Docker Registry`, `Docker Container`。

### 5.1 `Docker Client`

`Docker Client` ，也称Docker客户端。它其实就是Docker提供命令行界面(CLI)工具，是许多Docker用户与Docker进行交互的主要方式。
客户端可以构建，运行和停止应用程序，还可以远程与 `Docker_Host` 进行交互。
> 最常用的Docker客户端就是 `docker` 命令，我们可以通过docker命令很方便地在host上构建和运行docker容器。

 ![img](img/learn-how-to-use-docker/1100338-20181012214102869-1003920118.png)

### 5.2 `Docker daemon`

`Docker daemon` 是服务器组件，以 `Linux后台服务` 的方式运行，是Docker最核心的后台进程，我们也把它称为守护进程。
它负责响应来自 `Docker Client` 的请求，然后将这些请求翻译成系统调用完成容器管理操作。
该进程会在后台启动一个 `API Server` ，负责接收由 `Docker Client` 发送的请求，接收到的请求将通过`Docker daemon` 内部的一个路由分发调度，由具体的函数来执行请求。

 ![img](img/learn-how-to-use-docker/1100338-20181012214101750-1118997362.png)

我们大致可以将其分为以下三部分：
**`Docker Server`**, **`Engine`**, **`Job`**

**Docker Daemon** 的架构如下所示：

 ![img](img/learn-how-to-use-docker/1100338-20181012214101184-1339527466.jpg)

`Docker Daemon` 可以认为是通过 `Docker Server` 模块接受 `Docker Client` 的请求，并在 `Engine` 中处理请求，然后根据请求类型，创建出指定的 `Job` 并运行。 `Docker Daemon` 运行在 `Docker host` 上，负责创建、运行、监控容器，构建、存储镜像。

运行过程的作用有以下几种可能：

- 向 `Docker Registry` 获取镜像
- 通过 `graphdriver` 执行容器镜像的本地化操作
- 通过 `networkdriver` 执行容器网络环境的配置
- 通过 `execdriver` 执行容器内部运行的执行工作

由于 `Docker Daemon` 和 `Docker Client` 的启动都是通过可执行文件 `docker` 来完成的，因此两者的启动流程非常相似。 `Docker` 可执行文件运行时，运行代码通过不同的命令行 `flag` 参数，区分两者，并最终运行两者各自相应的部分。

启动 `Docker Daemon` 时，一般可以使用以下命令来完成

```bash
docker –d
docker --daemon=true
docker –d=true
```

再由 `docker` 的 `main()` 函数来解析以上命令的相应 `flag` 参数，并最终完成 `Docker Daemon` 的启动。

下图可以很直观地看到 `Docker Daemon` 的启动流程：

 ![img](img/learn-how-to-use-docker/1100338-20181012214100787-426925315.jpg)

默认配置下， `Docker daemon` 只能响应来自本地 `Host` 的客户端请求。如果要允许远程客户端请求，需要在配置文件中打开 `TCP` 监听。我们可以照着如下步骤进行配置：

1、编辑配置文件 `/etc/systemd/system/multi-user.target.wants/docker.service`
  
 在环境变量 `ExecStart`后面添加 `-H tcp://0.0.0.0`，允许来自任意 IP 的客户端连接。

 ![img](img/learn-how-to-use-docker/1100338-20181012214100440-905260954.jpg)

2、重启 `Docker daemon`

```bash
systemctl daemon-reload
systemctl restart docker.service
```

3、我们通过以下命令即可实现与远程服务器通信

```bash
docker -H 服务器IP地址 info
```

`-H` 是用来指定服务器主机， `info` 子命令用于查看 `Docker` 服务器的信息

### 5.3 `Docker Image`

`Docker镜像`可以看作是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。我们可将Docker镜像看成只读模板，通过它可以创建Docker容器。

镜像有多种生成方法：

1. 从无到有开始创建镜像
2. 下载并使用别人创建好的现成的镜像
3. 在现有镜像上创建新的镜像

我们可以将镜像的内容和创建步骤描述在一个文本文件中，这个文件被称作 `Dockerfile`，通过执行 `docker build <docker-file>` 命令可以构建出Docker镜像。

### 5.4 `Docker Registry`

`Docker registry` 是存储 docker image的仓库，它在Docker生态环境中的位置如下图所示：

 ![img](img/learn-how-to-use-docker/1100338-20181012214100277-775657574.jpg)

运行`docker push`、`docker pull`、`docker search`时，实际上是通过 `docker daemon` 与 `docker registry` 通信。

### 5.5 `Docker Container`

`Docker容器`就是Docker镜像的运行实例，是真正运行项目程序、消耗系统资源、提供服务的地方。
`Docker Container` 提供了系统硬件环境，我们可以使用 `Docker Images` 这些制作好的系统盘，再加上我们所编写好的项目代码， `run` 一下就可以提供服务啦。

## 6、Docker组件是如何协作运行容器

看到这里，我相信各位读者朋友们应该已经对Docker基础架构已经熟悉的差不多了，我们还记得运行的第一个容器吗？现在我们再通过hello-world这个例子来体会一下 `Docker` 各个组件是如何协作的。

容器启动过程如下：

- `Docker` 客户端执行 `docker run` 命令
- `Docker daemon` 发现本地没有 `hello-world` 镜像
- `Docker daemon` 从 `Docker Hub` 下载镜像
- 下载完成，镜像 `hello-world` 被保存到本地
- `Docker daemon` 启动容器

具体过程可以看如下这幅演示图：

 ![img](img/learn-how-to-use-docker/1100338-20181012214059835-734054348.png)

我们可以通过`docker images` 可以查看到 `hello-world` 已经下载到本地

 ![img](img/learn-how-to-use-docker/1100338-20181012214059170-1387452245.png)

我们可以通过`docker ps` 或者 `docker container ls` 显示正在运行的容器，我们可以看到， `hello-world` 在输出提示信息以后就会停止运行，容器自动终止，所以我们在查看的时候没有发现有容器在运行。

 ![img](img/learn-how-to-use-docker/1100338-20181012214058803-1594502697.png)

我们把 `Docker` 容器的工作流程剖析的十分清楚了，我们大体可以知道 `Docker` 组件协作运行容器可以分为以下几个过程：

1. `Docker` 客户端执行 `docker run` 命令
2. `Docker daemon` 发现本地没有我们需要的镜像
3. `daemon` 从 `Docker Hub` 下载镜像
4. 下载完成后，镜像被保存到本地
5. `Docker daemon` 启动容器

## 7、Docker常用命令

了解了这些过程以后，我们再来理解这些命令就不会觉得很突兀了，下面我来给大家讲讲 `Docker` 常用的一些命令操作吧。

我们可以通过 `docker -h` 去查看命令的详细的帮助文档。在这里我只会讲一些平常日常比赛或者生活中我们可能会用的比较多的一些命令。

 ![img](img/learn-how-to-use-docker/1100338-20181014202945937-1677031749.png)

例如，我们需要拉取一个 `docker` 镜像，我们可以用如下命令：

```bash
docker pull IMAGE_NAME
```

`IMAGE_NAME` 为镜像的名称，而如果我们想从 `Docker Hub` 上去下载某个镜像，我们可以使用以下命令：

```bash
docker pull centos:latest
```

`centos:lastest` 是镜像的名称， `Docker daemon` 发现本地没有我们需要的镜像，会自动去 `Docker Hub` 上去下载镜像，下载完成后，该镜像被默认保存到 `/var/lib/docker` 目录下。

接着我们如果想查看下主机下存在多少镜像，我们可以用如下命令：

```bash
docker images
```

我们要想知道当前有哪些容器在运行，我们可以用如下命令：

```bash
docker ps -a
```

`-a` 是查看当前所有的容器，包括未运行的

我们该如何去对一个容器进行启动，重启和停止呢？我们可以用如下命令：

```bash
docker start <CONTAINER_NAME|CONTAINER_ID>
docker restart <CONTAINER_NAME|CONTAINER_ID>
docker stop <CONTAINER_NAME|CONTAINER_ID>
```

这个时候我们如果想进入到这个容器中，我们可以使用 `attach` 命令：

```bash
docker attach <CONTAINER_NAME|CONTAINER_ID>
```

那如果我们想运行这个容器中的镜像的话，并且调用镜像里面的 `bash` ，我们可以使用如下命令：

```bash
docker run -t -i <CONTAINER_NAME|CONTAINER_ID> /bin/bash
```

那如果这个时候，我们想删除指定镜像的话，由于 `image` 被某个 `container` 引用（拿来运行），如果不将这个引用的 `container` 销毁（删除），那 `image` 肯定是不能被删除。我们首先得先去停止这个容器：

```bash
docker ps
docker stop <CONTAINER_NAME|CONTAINER_ID>
```

然后我们用如下命令去删除这个容器：

```bash
docker rm <CONTAINER_NAME|CONTAINER_ID>
```

然后这个时候我们再去删除这个镜像：

```bash
docker rmi image_name
```

此时，常用的 `Docker` 相关的命令就讲到这里为止了，我们在后续的文章中还会反复地提到这些命令。

## 8、Dockerfile是什么

前面我们已经提到了Docker的一些基本概念。

以 `CTF` 选手的角度来看，我们可以去使用Dockerfile定义镜像，依赖镜像来运行容器，可以去模拟出一个真实的漏洞场景。因此毫无疑问的说，Dockerfile是镜像和容器的关键，并且Dockerfile还可以很轻易的去定义镜像内容，说了这么多，那么 `Dockerfile` 到底是个什么东西呢？

`Dockerfile`是自动构建 `Docker镜像` 的配置文件， 用户可以使用 `Dockerfile` 快速创建自定义的镜像。`Dockerfile` 中的命令非常类似于Linux下的shell命令。

我们可以通过下面这幅图来直观地感受下 Docker 镜像、容器和 Dockerfile 三者之间的关系。

 ![img](img/learn-how-to-use-docker/1100338-20181014202945505-1291953865.png)

我们从上图中可以看到，`Dockerfile`可以自定义镜像，通过Docker命令去运行镜像，从而达到启动容器的目的。

`Dockerfile` 是由一行行命令语句组成，并且支持以`#`开头的注释行。

一般来说，我们可以将 `Dockerfile` 分为四个部分：

- **基础镜像(父镜像)信息指令 `FROM`**
- **维护者信息指令 `MAINTAINER`**
- **镜像操作指令 `RUN、EVN、ADD 和 WORKDIR` 等**
- **容器启动指令 `CMD、ENTRYPOINT 和 USER` 等**

下面是一段简单的Dockerfile的例子：

```Dockerfile
FROM python:2.7
MAINTAINER Angel_Kitty <angelkitty6698@gmail.com>
COPY . /app
WORKDIR /app
RUN pip install -r requirements.txt
EXPOSE 5000
ENTRYPOINT ["python"]
CMD ["app.py"]
```

我们可以分析一下上面这个过程：

- 1、从 `Docker Hub` 上 `pull` 下 `python 2.7` 的基础镜像
- 2、显示维护者的信息
- 3、`copy` 当前目录到容器中的 `/app` 目录下
  > 复制本地主机的 `<src>` ( `Dockerfile` 所在目录的相对路径)到容器里 `<dest>`
- 4、指定工作路径为 `/app`
- 5、安装依赖包
- 6、暴露 `5000` 端口
- 7、启动 `app`

这个例子是启动一个 `python flask app` 的 `Dockerfile` ( `flask` 是 `python` 的一个轻量的 `web` 框架)，相信大家从这个例子中能够稍微理解了Dockfile的组成以及指令的编写过程。

## 9、Dockerfile常用的指令

根据上面的例子，我们已经差不多知道了Dockerfile的组成以及指令的编写过程，我们再来理解一下这些常用命令就会得心应手了。

由于 `Dockerfile` 中所有的命令都是以下格式：`INSTRUCTION argument`
> 指令 `(INSTRUCTION)` 不分大小写，但是推荐大写

- **`FROM`** - 用于指定基础的 IMAGES

  一般格式为 `FROM <image>` or `FORM <image>:<tag>`
  
  所有的 `Dockerfile` 都用该以 `FROM` 开头，`FROM` 命令指明 `Dockerfile` 所创建的镜像文件以什么镜像为基础，`FROM` 以后的所有指令都会在 `FROM` 的基础上进行创建镜像。可以在同一个 `Dockerfile` 中多次使用 `FROM` 命令用于创建多个镜像。
  
  比如我们要指定 `python 2.7` 的基础镜像，我们可以像如下写法一样：
  
  ```docker
  FROM python:2.7
  ```

- **`MAINTAINER`** - 是用于指定镜像创建者和联系方式，
  
  一般格式为 `MAINTAINER <name>` 。这里我设置成我的 `ID` 和邮箱：

  ```docker
  MAINTAINER Angel_Kitty <angelkitty6698@gmail.com>
  ```

- **`COPY`** - 是用于复制本地主机的 `<src>` (为Dockerfile所在目录的相对路径)到容器中的 `<dest>`。

  当使用本地目录为源目录时，推荐使用 `COPY` 。一般格式为 `COPY <src><dest>`。
  例如我们要拷贝当前目录到容器中的 `/app` 目录下，我们可以这样操作：

  ```docker
  COPY . /app
  ```

- **`WORKDIR`** - 用于配合 `RUN`，`CMD`，`ENTRYPOINT` 命令设置当前工作路径。
  
  可以设置多次，如果是相对路径，则相对前一个 `WORKDIR` 命令。默认路径为`/`。
  
  一般格式为 `WORKDIR /path/to/work/dir` 。例如我们设置`/app` 路径，我们可以进行如下操作：

  ```docker
  WORKDIR /app
  ```

- **`RUN`** - 用于容器内部执行命令。
  
  每个 `RUN` 命令相当于在原有的镜像基础上添加了一个改动层，原有的镜像不会有变化。一般格式为 `RUN <command>` 。例如我们要安装 `python` 依赖包，我们做法如下：

  ```docker
  RUN pip install -r requirements.txt
  ```

- **`EXPOSE`** - 用来指定对外开放的端口。

  ```dockerfile
  EXPOSE <port> [<port>...]
  ```

- **`ENTRYPOINT`** - 可以让你的容器表现得像一个可执行程序一样。
  
  一个 `Dockerfile` 中只能有一个 `ENTRYPOINT`，如果有多个，则最后一个生效。

  ENTRYPOINT命令也有两种格式：
  - `ENTRYPOINT ["executable", "param1", "param2"]` ：推荐使用的 `exec`形式
  - `ENTRYPOINT command param1 param2` ：`shell` 形式

  例如下面这个，我们要将 `python` 镜像变成可执行的程序，我们可以这样去做：

  ```docker
  ENTRYPOINT ["python"]
  ```

- **`CMD`** - 命令用于启动容器时默认执行的命令
  
  CMD命令可以包含可执行文件，也可以不包含可执行文件。不包含可执行文件的情况下就要用 ENTRYPOINT指定一个，然后CMD命令的参数就会作为ENTRYPOINT的参数。

  CMD命令有三种格式：

  - `CMD ["executable","param1","param2"]`：推荐使用的 `exec` 形式。
  - `CMD ["param1","param2"]`：无可执行程序形式
  - `CMD command param1 param2`：shell 形式。

  一个 `Dockerfile` 中只能有一个`CMD`，如果有多个，则最后一个生效。而 `CMD` 的 `shell` 形式默认调用 `/bin/sh -c` 执行命令。

  CMD命令会被Docker命令行传入的参数覆盖：`docker run busybox /bin/echo Hello` Docker 会把CMD里的命令覆盖。

  例如我们要启动 `/app` ，我们可以用如下命令实现：

  ```docker
  CMD ["app.py"]
  ```

当然还有一些其他的命令，我们在用到的时候再去一一讲解一下。

## 10、构建Dockerfile

我们大体已经把 Dockerfile 的写法讲述完毕，我们可以自己动手写一个例子：

```bash
cat >> Dockerfile <<'EOF'
FROM nginx
MAINTAINER Abodu <abodu@qq.com>
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
EOF
```

我们在 `Dockerfile` 文件所在目录执行：

```bash
docker build -t angelkitty/nginx_web:v1 .
```

我们解释一下， `-t` 是为新镜像设置仓库和名称，其中 `angelkitty` 为仓库名， `nginx_web` 为镜像名， `:v1`为标签（不添加为默认 `latest` ）

我们构建完成之后，使用 `docker images` 命令查看所有镜像，如果存在 `REPOSITORY` 为 `nginx` 和 `TAG` 是 `v1` 的信息，就表示构建成功。

 ![img](img/learn-how-to-use-docker/1100338-20181015082853781-920978466.png)

接下来使用 `docker run` 命令来启动容器

```bash
docker run --name nginx_web -d -p 8080:80   angelkitty/nginx_web:v1
```

这条命令会用 nginx 镜像启动一个容器，命名为 `nginx_web` ，并且映射了 8080 端口，这样我们可以用浏览器去访问这个 `nginx` 服务器：`http://localhost:8080/`，页面返回信息：

![hello-docker](img/learn-how-to-use-docker/1100338-20181015082853396-674842375.png)

这样一个简单使用 `Dockerfile` 构建镜像，运行容器的示例就完成了！
