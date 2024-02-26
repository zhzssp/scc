# 打造你自己的集群

## 概览

超算集群比赛，首先要做的事情当然是组集群。机器硬件配好了，系统软件也要跟上来！在这部分的任务里，我们希望你使用虚拟机构建集群，并准备一些系统软件（例如编译器和库）。最后，你需要在集群上编译一个真实的应用程序。

这些任务要求你需要熟悉 Linux 的相关操作（不懂也没关系，按着我们材料的学习路径自行学习即可）。你不必完成所有任务。我们在下表中列出了建议，“R”为“建议完成”和“O”为“可选”。同时，根据你所完成的任务，我们将会用Level 1~Level 3来评定你的搭建集群能力。

| Task       | Level 1 | Level 2 | Level 3 |
| ---------- | ------- | ------- | ------- |
| Task 1     | r       | r       | r       |
| Task 2     | r       | r       | r       |
| Task 3     | r       | r       | r       |
| Task 4     | r       | r       | r       |
| Task 5     | r       | r       | r       |
| Task 6     | r       | r       | r       |
| Task 7     |         | r       | r       |
| Task 8     |         | r       | r       |
| Task 9     |         |         | r       |
| Task 10    |         |         | r       |
| Extra Task | o       | o       | o       |

### 对该集群，我们期望的架构

一个集群一般包括高速网络，大容量存储设备，多核处理器，以及相关的系统软件，如分布式文件系统，编译器和库等软件。

在这个项目中，你的目标是构建一个小但完整的双节点集群，如下图所示：

![](./arch.jpeg)

Node 1 包含两个物理盘（实际上是两个虚拟盘），一个物理盘（挂载在`/data/` 路径下）。一个物理盘（在`/`目录下）用于安装操作系统，另一个物理盘（在`/data/`目录下）作为数据盘存储其他的数据（如第三方库、计算数据等），Node 2 仅有一个包含操作系统的物理盘， Node 2 的 `/data/`路径会通过 NFS 把 Node 1 的 `/data/` 挂载过来。这样的设计能让两个节点共享 `/data/` 目录下的数据。

### 提示

我们假设你可能没有足够的硬件来部署真实的集群。因此，你可以使用虚拟机进行下面的任务。当然，我们也欢迎你使用真正的硬件来完成以下任务。

在下面的所有任务中，我们将为你提供目标和相应的TODO列表。此外，我们还提供一些提示，并希望你顺利完成这些任务。如果你遇到一些困难，可以随意Google。如果觉得自己遇到了难于解决的问题，可以随时向我们提问。**不要闭门造车！**

### 如何提交

你不需要提交一个额外的报告，你只需要完成下面的 **TODO** ，并直接相关信息填在该 README.md 上即可。

对于Todo列表中的每个项目，你可以放置包含命令的代码块。如果需要，可以附加相关的屏幕截图以证明你成功完成了该任务。此外，你还可以撰写笔记以解释详细信息。

TIPS: 一般来说，把命令和log直接贴出来能够让我们对你所做的事情有更清晰的认识，"talk is cheap, show me your code"，大多数无关紧要的步骤直接用代码块贴log（或者只贴命令）即可，可以仅在你觉得需要的部分放截图。



## Task 0 ：熟悉Linux基本操作（不计分，供Linux初学者参考）

在本任务中，你需要熟悉一些Linux的基本操作指令。这是一个供大家初步学习Linux的**选做**任务，我们鼓励**初学者**从这个任务开始先熟悉指令。如果你是Linux老手，请直接从Task1开始写。



你需要使用Linux指令来完成下面的任务：

- [ ] 命令行下登录登出，开关机
- [ ] 命令行下创建，复制，移动，删除文件和文件夹，切换文件夹
- [ ] 命令行下编辑文本文件
- [ ] 命令行下查看文本文件
- [ ] 命令行下标准输入输出和重定向
- [ ] 命令行下编译运行简单程序（gcc/g++/python）


### 提交

TODO: 将使用到的Linux指令，以及执行完这个指令后的结果截图贴出来。




## Task 1 ：设置一个计算节点，并安装操作系统

在本任务中，你需要在集群中设置一个标准的计算节点。

我们建议使用 **VMware Workstation player** 创建虚拟机。**[VMware workstation player](https://www.vmware.com/products/workstation-player/workstation-player-evaluation.html)**可以免费下载使用，没有必要花时间寻找Pro版本。当然， 也可以用[VirtualBox](https://www.virtualbox.org/) 或 [virt-manager](https://virt-manager.org/) 。



为了安装和设置一个标准的计算节点，你需要：

- [ ] 在虚拟机上安装一个操作系统
  - 你可以选择 **AlmaLinux 8** , **Rocky Linux 8** 或者 **Debian 11**, **Debian 12** 作为操作系统
  - TODO：解释你选择的理由？不存在标准答案，说你自己的理解即可。
  - 把系统盘的大小设置在大概 25 GB 就够了。
  - 设置用户名时，我们推荐把用户名设置为 `scc` 。
- [ ] 通过 OS 自带的包管理器（如 `yum` 或 `apt` ）安装一些必要的软件
  - 安装一些必要的软件： gcc/g++/gfortran/nfs-utils/tmux/htop/vim/git
  - 你还可以安装一些其他你喜欢的软件。
    - TODO：你可以简单说说你为啥喜欢这些软件
- [ ] 把该计算节点的 hostname 设置为 `node1`
  - 如何保证该设置的效果重启后还能生效？
- [ ] 关闭一些无用的服务
  - 例如： firewall/SELinux
  - TODO：为什么我们说这些服务是没用的，关闭他们有什么好处？
  - TODO：如何关闭服务？如何保证已关闭的服务不会开机自启？
  - TODO：你觉得还有什么服务是没用的吗？
- [ ] （可选）编写一个脚本把上面的这些琐碎事情自动化，并用这个脚本一键初始化 node2
  - 如何编写 bash 脚本？一些参考资料 [link1](https://www.bilibili.com/video/BV1bU4y1a73h) [link2](https://www.bilibili.com/video/BV1y44y1v7c3) [link3](https://missing.csail.mit.edu/2020/shell-tools/)


### 提交

TODO: 你需要添加一些详细的信息，说明你如何完成上面的任务。


## Task 2: 设置node2，测试网络，测试 ssh 链接

在本任务中，你需要设置第二个计算节点 `node2`，配置与 `node1` 相同。此外，你还需要测试 `node1` 和 `node2` 之间的网络。


- [ ] 设置 `node2`，`node2` 和 `node1` 配置一样
  - 你可以直接复制 `node1` 的系统镜像，也可以重复 `node1` 上的操作。
  - 注意: `node1` 和 `node2` 需要有相同的用户名，设置 hostname 的时候需要设置成 `node2`
- [ ] 找到并查看 `node1` 和 `node2` 上的ip地址和路由表。
  - Tips: 学习使用命令 `ifconfig`/`ip`/...
- [ ] 测试 `node1` 和 `node2` 之间的网络是否连通。
  - Tips: 学习使用命令 `ping`/`traceroute`
- [ ] 学习使用 `ssh` 
  - [ ] TODO: 从物理机，使用 `ssh` 直接连接 `node1` 和 `node2` 操作这两个节点 ，而不使用 GUI 界面
  - [ ] TODO: 在 `node1` 上 使用 `ssh` 连接到 `node2`
  - [ ] TODO: 在 `node2` 上 使用 `ssh` 连接到 `node1`
  - [ ] TODO: 配置免密登陆，让物理机能够不需要输入密码就能登陆 `node1` 和 `node2`
  - [ ] TODO: 配置免密登陆，让 `node1` 和 `node2` 之间可以免密登陆
    - Tips: 学习使用私钥登陆，[ssh 相关教程](https://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)
- [ ] 学习一个配置文件 `/etc/hosts`
  - [ ] 在前面的操作中，需要经常输入虚拟机的ip地址，有没有办法可以偷懒不去记？
    - 配好后，你应该可以在 `node1` 上直接输入 `ssh node2` 就可以连接 `node2`.
    - 或者在`node1` 上直接 `ping node2` 就可以测试 `node1` 和 `node2` 之间的网络

### 提交

TODO: 你需要添加一些详细的信息，说明你如何完成上面的任务。




## Task 3: 在 `node1` 上增加一个大容量存储设备

目前 `node1` 和 `node2` 的系统盘都挺小的，在这个任务中，你需要在 `node1` 上增加一个比较大的盘（80GB），后面我们会把这一块盘通过 NFS 分享给 `node2` 使用。

- [ ] 在 `node1` 上增加一块盘
  - 在虚拟机管理软件上操作一下就可以了。
  - 在 `node1` 上，可以使用命令 `lsblk` / `fdisk -l` 去检查有没有新的盘加进来。
- [ ] 在新的盘上进行分区
  - 学习命令 `fdisk`
  - 一般情况下，只需要把整个盘都划为一个分区。
  - 同样可以使用命令 `lsblk` / `fdisk -l` 去检查由于分区而增加的设备。
- [ ] 把刚刚分好的区进行格式化，格式化成 ext4 文件系统。
  - 学习命令 `mkfs.ext4`
  - 你也可以尝试其他文件系统，说说你为什么选择其他文件系统
- [ ] 把格式化好的分区挂载到 `node1` 上的 `/data/` 路径下 。
  - 学习命令 `mount` / `umount`
  - 使用命令 `df -h` 检查挂载结果
- [ ] 使用非特权用户（如 `scc`）访问 `/data` 路径
  - 学习使用命令 `chown` / `chmod`
  - 路径 `/data` 需要能够被普通用户（如 `scc` 用户）可读可写。
  - 学习 Linux 中的权限模型
  - 在后面的任务中，你要尽量避免直接使用 root 用户 和 `sudo` 命令。
    - 除了一些必须使用root用户的操作


在后面的任务中，我们将只在 `/data` 下操作、存储数据。


### 提交

TODO: 你需要添加一些详细的信息，说明你如何完成上面的任务。




## Task 4: 学习 Linux 中的软件环境



我们将尝试在没有root和sudo的情况下配置、安装和运行一些软件。本任务将尝试让您详细了解Linux中某些环境变量的影响。在下一个任务中，我们将使用Environment Module来管理环境。

在这个任务中，你需要在没有root的情况下安装最新版本的CMake。




- [ ] 安装最新版本的 CMake

  - [ ] 下载CMake的源码到 `/data/software/source/` 并且解压

    - [ ] 可以参考 [link](./linux-software-environment.md)
    - [ ] 阅读 `README` 去看看怎么从源码编译安装
  - [ ] 编译源代码并安装

    - [ ] 你可以使用系统包管理器安装一些缺失的cmake依赖
    - [ ] 您必须将CMake安装到 `/data/software/install/cmake/${version}`，`${version}` 是 cmake 的版本号
    - [ ] 编译是一个计算密集型的过程，另外它可能需要较大的内存。因此，你可以临时扩展虚拟机的CPU /内存。 
  - [ ] 看看命令 `cmake --version` 的结果来检查cmake的版本

    - [ ] 如何不需要绝对路径，直接只用 `cmake` 命令？
    - [ ] 看看命令 `which cmake` 的结果
    - [ ] TIPS：看看环境变量 `PATH`
- [ ] 了解其他环境变量

  - [ ] 仔细阅读该部分学习材料：[link](./linux-software-environment.md)
  - [ ] 这些环境变量，在后面配置 modulefile 的时候非常重要
  - [ ] 回答问题：类似于 `PATH` 的环境变量，把自己的路径加载最前面和最后面，有什么区别吗？为什么？
    - `export PATH=/path/to/..:${PATH}` 和 `export PATH=${PATH}:/path/to/...`
  - [ ] （optional）还有没有什么重要的环境变量？可以介绍一下
    - 例子：pkg-config 是什么？有什么作用？有命令行参数或环境变量去控制搜索路径吗？
    - 例子：cmake 如何 find package ? 有命令行参数或环境变量去控制搜索路径吗？
    - 例子：python 中的 import package，是从什么路径寻找的？如果自己手动操作安装到非默认路径，如何让 python 找到你的package？（tips： `PYTHONHOME` 和 `PYTHONPATH` 环境变量）
    - ...



### 提交

TODO: 你需要添加一些详细的信息，说明你如何完成上面的任务。





## Task 5: 安装HPC集群必备软件



我们将会配置、安装一些HPC集群的必备软件（尽量避免使用root权限）。在本部分任务中，我们需要自己编写脚本管理软件环境，或使用 Environment Module 来管理我们的软件，并初步学习HPC集群常见的软件环境管理方法。

Environment Module 是控制 Shell 当前会话环境变量的应用程序。例如，使用 Environment Module 加载最新的 gcc，gcc 的路径会加到 `PATH` 环境变量最前面了，由此，旧的 gcc 命令就能被新的所覆盖。在其他情况下，某些运行时所需的库需要将其路径加到 `LD_LIBRARY_PATH` 中，这些使用 Environment Module 都能轻松处理。



- [ ] 首先安装Environment Module 模块！
  - [ ] Environment Module 有好几个以不同语言实现的变体。我们推荐安装系统包管理器提供的版本（通常用TCL编写）。



**你的目标**: 创建一些 modulefiles，让非特权用户可以加载常用的编译器、第三方库、应用等。为了让集群存储环境更为规范，我们要求所有的软件都需要安装在 `/data/software/install` 下，并且遵循一定的规则（如gcc 7.5.0 安装在 `/data/software/install/gcc/7.5.0/`下) ，不同版本的软件需要有独立的目录。


- [ ] CMake 3.x
- [ ] GNU Compiler Collection 7.x + 10.x
  - 使用包管理器默认提供的编译器(通常也是gcc)从源代码编译gcc。不要忘记启用对C、c++和Fortran的语言支持。
- [ ] Intel oneAPI toolkit 2022 (Intel oneAPI Base Toolkit + Intel oneAPI HPC Toolkit)
  - Intel oneAPI Base Toolkit [download link](https://registrationcenter-download.intel.com/akdlm/IRC_NAS/7deeaac4-f605-4bcf-a81b-ea7531577c61/l_BaseKit_p_2023.1.0.46401_offline.sh)
  - Intel oneAPI HPC Toolkit [download link](https://registrationcenter-download.intel.com/akdlm/IRC_NAS/1ff1b38a-8218-4c53-9956-f0b264de35a4/l_HPCKit_p_2023.1.0.46346_offline.sh)
  - Follow the instructions of `Intel oneAPI toolkit 2024` [reference link](https://www.intel.cn/content/www/cn/zh/developer/tools/oneapi/toolkits.html) to install Intel oneAPI toolkit 2022
  - TIPS: use the scripts in the installed package to add modulefiles automatically
- [ ] CUDA Toolkit 11.x/12.x (不需要安装驱动)
  - [ ] 如果你有显卡且想在 linux 下装驱动，可补充到报告中
- [ ] OpenMPI 3.x + 4.x + 5.x
  - mpi是如何使用的，说明从代码编写到编译到运行的流程
  - mpicc和gcc有什么本质不同吗？说明mpicc -show的结果
  - 编译一个使用外置ucx的mpi **（加分项）**
  - 编译一个CUDA-Aware的ucx **（加分项）**
  - 编译一个CUDA-Aware的mpi **（加分项）**
- [ ] llvm15+**（加分项）**




你可以使用类似下面的命令检查环境，这里是一个 `gcc` 的例子，你应该用类似的命令检查所有你安装的软件，同时最好附带部分例子表明你安装的软件能够正常使用，如编译一个`hello_world.c` 。


```shell
# use gcc provided by the system package manager
which gcc
gcc --version
# use gcc 7.5.0
module load gcc/7.5.0
which gcc
gcc --version
# compile a hello_world.c and run to check the gcc compiler
module unload gcc/7.5.0
# use gcc 10.3.0
module load gcc/10.3.0
which gcc
gcc --version
# compile a hello_world.c and run to check the gcc compiler
module unload gcc/10.3.0
```


其他材料：

1. [Environment Module](https://modules.readthedocs.io/en/latest/cookbook/compiler-etc-dependencies.html)
   1. 提供一个参考链接： [link](https://blog.csdn.net/kongxx/article/details/52253593)
   2. [env2](https://software.intel.com/content/www/us/en/develop/articles/using-environment-modules-with-the-intel-development-tools.html) 也提供一个参考链接： [link](https://www.intel.com/content/www/us/en/developer/articles/technical/using-environment-modules-with-the-intel-development-tools.html)
2. [Spack](https://github.com/spack/spack) 是下一代的 HPC 集群软件管理工具，有空可以了解一下！
   1. （可选）能不能在你的集群上使用[Spack](https://github.com/spack/spack)安装相同的一套环境呢？
3. [Nix](https://github.com/NixOS/nix) 也可以试试，作为可选项，可以试试用它装 openmpi



### 提交

TODO: 你需要添加一些详细的信息，说明你如何完成上面的任务。





## Task 6: Network File System

现在所有软件都安装在 `node1` 的 `/data/software/install` 目录下，我们现在将通过 Network File System 将目录 `/data` 分享到 `node2` 上，这样 `node2` 就不用重复安装这些软件。（类似的做法可以扩展到更多规模的集群）。

你的任务：

- [ ] 在 `node1` 上将目录 `/data` 导出成 Network File System。
  - 你可以在 `node2` 上使用命令 `showmount -e node1` 检查。
- [ ] 在 `node2` 上把`node1` 的 `/data` 挂载到本地的 `/data` 目录
- [ ] 在 `node2` 上检查软件环境是否正常。
  - 在 `node2` 上使用脚本导入 `/data` 中的软件，并且做一些事情使用这些软件确保正常。
- [ ] TODO: 对于很大很大规模的集群，NFS 还会好使吗？
  - 如果不，可以推荐一些其他的分布式文件系统，并且简单介绍一下其优劣。
  - 可以自行了解现在超算的存储架构和性能 [io500](https://io500.org/)，并在自己机器上跑通io500测试**（加分项）**


### 提交

TODO: 你需要添加一些详细的信息，说明你如何完成上面的任务。





## Task 7: 【测试网络性能】在集群上跑 OSU benchmark

在这部分，你需要运行 [osu-micro-benchmark](https://mvapich.cse.ohio-state.edu/benchmarks/) 去测量`node1` 和 `node2` 之间的网络带宽和延迟。

这部分任务只是让你熟悉MPI的使用和测试，不需调参获得更好的结果。

你的任务：

- [ ] 下载源代码 [osu-micro-benchmark](https://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.7.1.tgz) 并安装 
- [ ] 使用 Open MPI 在两个进程上测试 `osu_bw` and `osu_latency`
  - 确保一个进程在 `node1` 另一个进程在 `node2`.，可以通过 `mpirun ..... hostname` 来测试
- [ ] 使用 Intel MPI (包含在 Intel Parallel Studio 中) 在两个进程上测试 `osu_bw` and `osu_latency`
  - 确保一个进程在 `node1` 另一个进程在 `node2`.
- [ ] 回答问题：现在超算一般用的是什么网络？在不同的网络上跑这些benchmark，你猜测结果分别是多少呢？
- [ ] （可选）思考：如果软件环境和osu benchmark没有装在 `/data` 下，而是在node1的系统盘里（即这些内容只能在 node1 上访问，node2 上不能直接访问），执行这些多节点的程序可能会遇到什么问题？（可以自己试试看）

### 提交

TODO: 你需要添加一些详细的信息，说明你如何完成上面的任务。



## Task 8: 【测试磁盘性能】在集群上跑磁盘性能测试

在这部分，你需要运行 [fio](https://github.com/axboe/fio) 对集群的存储设备的性能进行测试。



1. 三个需要注意的选项：
   1. `--ioengine=libaio`，选取该异步IO库可以尽可能压榨盘的性能。
   2. `--direct=1`，该选项可以跳过操作系统的cache，以测得真实性能。
   3. `--rw=XXXX`，这里选择read/write/randread/randwrite，分别对应顺序读、顺序写、随机读、随机写。
2. 测试项一：测试写入、读取最大带宽（顺序，随机）
   1. 一般使用较大的块大小（如1MB）和较大的队列深度进行多线程测试。
3. 测试项二：测试4K写入、读取最大IOPS （顺序，随机）
   1. 一般使用较大的队列深度进行多线程测试。
4. 测试项三：测试4K写入、读取延时 （顺序，随机）
   1. 一般使用队列深度为1进行单线程测试。

你需要做的是：

- [ ] 在 `node1` 上测试 `/data/` 下的顺序/随机 写入、读取带宽，写入、读取 IOPS，以及写入读取的延时
- [ ] 在 `node2` 上测试 `/data/` 下的顺序/随机 写入、读取带宽，写入、读取 IOPS，以及写入读取的延时
- [ ] 尝试理解 fio 测试的日志含义，从而回答下面问题：
  - [ ] 回答问题：在 `node2` 上测试的结果和 `node1` 上的结果有哪些区别，原因可能是什么？
  - [ ] 回答问题：现在比较常见的存储设备有哪几种？这几种存储设备的性能大致如何描述？
- [ ] （optional）写个脚本自动搞定这个任务？



参考资料：[link](https://cloud.google.com/compute/docs/disks/benchmarking-pd-performance)



### 提交

TODO: 你需要添加一些详细的信息，说明你如何完成上面的任务。




## Task 9: 【测试软件环境】在集群上编译一个真实应用

天气研究和预报模型（WRF，发音为"worf"）是一个主要用于数值天气预报（NWP）的高度并行，基于CPU的Fortran软件包。此应用程序是 ISC21 中的基准测试应用程序之一。(细节在[这里](https://hpcadvisorycouncil.atlassian.net/wiki/spaces/HPCWORKS/pages/1827438600/WRF+with+Single+Domain+-+Practice+case+for+ISC21+SCC))

在这个任务中，你只需要编译该应用即可。

这里有一些tips：

1. 认真阅读 [link](https://hpcadvisorycouncil.atlassian.net/wiki/pages/viewpage.action?pageId=1827438600&pageVersion=27) 
2. 安装依赖并且使用 Environment Module 进行管理。
   1. 这些依赖包括 MPI, hdf5, netcdf (netcdf-c 和 netcdf-fortran)
3. 编译时你需要选择 "dm+sm" 
4. 编译时间可能较久，从十几分钟到一小时不等。
5. 你可以选择 gcc 编译器，也可以选择 intel 编译器。

你的目标是看到这样的log：


```
==========================================================================
build started:   Fri Apr 9 18:44:39 EDT 2021
build completed: Fri Apr 9 19:53:47 EDT 2021
 
--->                  Executables successfully built                  <---
 
-rwxr-x--- 1 lcl_davidg iscscc-scinet 52523904 Apr  9 19:53 main/ndown.exe
-rwxr-x--- 1 lcl_davidg iscscc-scinet 52486664 Apr  9 19:53 main/real.exe
-rwxr-x--- 1 lcl_davidg iscscc-scinet 51479608 Apr  9 19:53 main/tc.exe
-rwxr-x--- 1 lcl_davidg iscscc-scinet 60223864 Apr  9 19:52 main/wrf.exe
 
==========================================================================
```

### 提交

TODO: 你需要添加一些详细的信息，说明你如何完成上面的任务。




## Task 10: 集群监控系统

在这个任务中，你需要搭建一个集群监控系统，你需要了解以下工具的使用：


1. [node_exporter](https://github.com/prometheus/node_exporter)

2. [prometheus](https://github.com/prometheus/prometheus)

3. [grafana](https://github.com/grafana/grafana)

   

- [ ] 使用 `node_exporter` 导出 `node1` 和 `node2` 的监控指标
- [ ] 使用 `prometheus` 记录 `node_exporter` 导出的指标
- [ ] 使用  `Grafana` 导出存储在 `prometheus` 的指标
  - 你可以按你自己的想法安排 `Grafana` 面板，网上也有很多现成的面板可以学习
- [ ] 在集群上生成一些负载，并使用`Grafana` 上的面板展示这些负载
  - `stress` 可以帮助你生成一些特定的负载 (CPU/Memory/..).

### 提交

TODO: 你需要添加一些详细的信息，说明你如何完成上面的任务。




## Extra Task :

可以看看这里面的小任务，发挥你自己的想法随意发挥

[final task](final_task.md)
