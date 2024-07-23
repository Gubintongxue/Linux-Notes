# 第二章、主机规划与磁盘分区

最近更新日期：20//

事实上，要安装好一部Linux主机并不是那么简单的事情，你必须要针对distributions的特性、服务器软件的能力、 未来的升级需求、硬件扩充性需求等等来考虑，还得要知道磁盘分区、文件系统、Linux操作较频繁的目录等等， 都得要有一定程度的了解才行，所以，安装Linux并不是那么简单的工作喔！ 不过，要学习Linux总得要有Linux系统存在吧？所以鸟哥在这里还是得要提前说明如何安装一部Linux练习机。 在这一章里面，鸟哥会介绍一下，在开始安装Linux之前，您应该要先思考哪些工作？ 好让您后续的主机维护轻松愉快啊！此外，要了解这个章节的重要性，您至少需要了解到Linux文件系统的基本概念， 这部份初学者是不可能具备的！所以初学者在这个章节里面可能会觉得很多部份都是莫名其妙！没关系！ 在您完成了后面的相关章节之后，记得要再回来这里看看如何规划主机即可！ ^_^

### 2.1 Linux与硬件的搭配

Linux系统的硬件支持是一个重要的方面，特别是对于希望使用Linux系统的用户来说。了解Linux与硬件的搭配，以及如何选择适合的硬件，可以帮助用户更好地运行和维护Linux系统。

### 2.1.1 认识计算机的硬件配备

在安装Linux之前，了解你的计算机硬件配备以及系统的主要用途非常重要。例如，如果你计划使用Linux作为桌面系统，那么显卡和内存的配置就显得尤为重要。相反，如果你计划使用Linux作为文件服务器，那么硬盘的容量和速度则更为重要。

#### 游戏机/工作机的考虑

游戏机和工作机的硬件配置需求是不同的。游戏机通常需要高性能的显卡和CPU，而工作机更多关注办公软件的性能需求，较低的硬件配置也能满足需求。

#### 性能/价格比与性能/消耗瓦数比的考虑

在选择硬件时，不仅要考虑性能，还要考虑价格和能耗。高性能硬件通常价格较高，并且能耗较大。在选择硬件时，需要权衡性能与价格、性能与能耗之间的关系。

#### 支持度的考虑

并非所有硬件都支持Linux系统，因此在购买硬件时需要确保所选硬件有相应的Linux驱动支持。特别是新硬件，有时候可能需要等待一段时间才能获得Linux支持。

### 2.1.2 选择与Linux搭配的主机配备

不同用途的Linux系统对硬件有不同的要求。以下是一些常见配置的建议：

-   **一般小型主机且不含X Window系统**：
    
    -   CPU：五年内出产的产品即可
    -   RAM：至少512MB，建议1GB以上
    -   网卡：一般以太网卡即可
    -   显卡：只要能启动计算机即可
    -   硬盘：20GB以上
-   **桌上型（Desktop）Linux系统/含X Window**：
    
    -   CPU：Intel I5, I7 以上等级
    -   RAM：1GB以上，建议4GB以上
    -   网卡：普通以太网卡
    -   显卡：256MB以上内存
    -   硬盘：60GB以上
-   **中型以上Linux服务器**：
    
    -   CPU：多核心系统，如I5, I7
    -   RAM：4GB以上，建议8GB以上
    -   网卡：如Intel或Broadcom等稳定性能较好的网卡
    -   硬盘：大容量硬盘或RAID配置
    -   显卡：64MB内存以上，如果使用图形功能

### 2.1.3 各硬件设备在Linux中的文件名

在Linux系统中，每个硬件设备都被当作一个文件来对待。常见硬件设备及其对应的文件名如下：

| 设备              | 文件名                                                       |
| ----------------- | ------------------------------------------------------------ |
| SCSI/SATA/USB硬盘 | /dev/sda−pa-pa−p                                             |
| USB闪存盘         | /dev/sda−pa-pa−p                                             |
| VirtI/O界面       | /dev/vda−pa-pa−p                                             |
| 软盘机            | /dev/fd0−70-70−7                                             |
| 打印机            | /dev/lp0−20-20−2（25针打印机） /dev/usb/lp0−150-150−15（USB接口） |
| 鼠标              | /dev/input/mouse0−150-150−15（通用） /dev/psaux（PS/2界面） /dev/mouse（当前鼠标） |
| CDROM/DVDROM      | /dev/scd0−10-10−1（通用） /dev/sr0−10-10−1（通用，CentOS较常见） /dev/cdrom（当前CDROM） |
| 磁带机            | /dev/ht0（IDE界面） /dev/st0（SATA/SCSI界面） /dev/tape（当前磁带） |
| IDE硬盘机         | /dev/hda−da-da−d（旧式系统才有）                             |

### 2.1.4 使用虚拟机学习

硬件虚拟化技术的成熟使得在普通个人计算机上运行多个逻辑独立的系统成为可能。虚拟化系统可以提供相同的硬件资源，便于学习和测试Linux系统。

推荐的虚拟化软件包括VirtualBox和KVM：

-   **VirtualBox**：适用于Windows用户。
-   **KVM**：适用于Linux用户，如Fedora或Ubuntu系统。

虚拟化学习资源：

-   [VirtualBox 官网](https://www.virtualbox.org)
-   [VirtualBox 使用手册](https://www.virtualbox.org/manual/ch01.html)
-   [Fedora 虚拟化指南](http://docs.fedoraproject.org/en-US/Fedora/13/html/Virtualization_Guide/part-Virtualization-Virtualization_Reference_Guide.html)

通过虚拟机学习Linux系统，可以在不影响主机系统的情况下进行各种实验和操作，是学习Linux的有效途径。

## 2.2 磁盘分区

在安装Linux之前，了解磁盘分区的相关知识是非常重要的。磁盘分区涉及到如何将硬盘划分成多个部分，每个部分可以独立使用和管理。这一小节将探讨磁盘连接方式、设备文件名、分区表格式以及开机流程等内容。

### 2.2.1 磁盘连接的方式与设备文件名的关系

个人计算机常见的磁盘接口有SATA与SAS接口，现代计算机主要使用SATA接口。虚拟机环境中的磁盘设备文件名通常为/dev/vda−pa-pa−p，而物理机环境下通常为/dev/sda−pa-pa−p。磁盘设备文件名的确定是根据Linux核心侦测到磁盘的顺序，而不是物理插槽的位置。

例题：假设有两个SATA磁盘和一个USB磁盘，分别插在SATA1和SATA5插槽上，USB磁盘在系统启动后被识别，它们在Linux中的设备文件名为：

1. SATA1插槽上的文件名：/dev/sda
2. SATA5插槽上的文件名：/dev/sdb
3. USB磁盘：/dev/sdc

### 2.2.2 MSDOS（MBR） 与 GPT 磁盘分区表（partition table）

分区表有两种格式：MBR（Master Boot Record）和GPT（GUID Partition Table）。

#### MSDOS（MBR） 分区表格式与限制

MBR分区表存在于磁盘的第一个扇区，大小为512字节，其中包含446字节的主要开机记录区和64字节的分区表。MBR最多只能记录四个分区，称为主要分区（Primary）或延伸分区（Extended）。延伸分区可以包含多个逻辑分区（Logical Partition）。

示例：

- P1: /dev/sda1
- P2: /dev/sda2
- P3: /dev/sda3
- P4: /dev/sda4

延伸分区内的逻辑分区示例：

- L1: /dev/sda5
- L2: /dev/sda6
- L3: /dev/sda7
- L4: /dev/sda8
- L5: /dev/sda9

MBR的限制：

- 操作系统无法识别2.2TB以上的磁盘容量。
- MBR仅有一个区块，若损坏，难以恢复。
- MBR内的开机管理程序区块仅446字节，无法容纳较多的程序码。

#### GPT 分区表格式

GPT分区表使用了34个LBA区块来记录分区信息，第一个LBA为LBA0，最后33个LBA用于备份。GPT可以记录更多的分区信息，且支持更大的磁盘容量。

GPT分区的特点：

- 默认可以提供多达128笔记录。
- 无主分区、延伸分区、逻辑分区的概念，每个分区都是主分区。
- 单一分区的最大容量限制为8ZB（1ZB = 1024TB）。

### 2.2.3 开机流程中的 BIOS 与 UEFI 开机检测程序

计算机开机时，通过BIOS或UEFI进行硬件检测和操作系统引导。

#### BIOS 搭配 MBR/GPT 的开机流程

BIOS是主板上的一个固件，开机时会加载MBR中的开机管理程序，进一步加载操作系统核心文件。

#### UEFI BIOS 搭配 GPT 开机的流程

UEFI是BIOS的升级版本，使用C语言编写，支持图形化界面和更快的启动速度。UEFI搭配GPT可以更好地管理大容量磁盘，并支持更多的分区。UEFI还引入了安全启动机制，确保只有经过验证的操作系统才能启动。

### 2.2.4 Linux安装模式下，磁盘分区的选择（极重要）

安装Linux时，需要规划磁盘分区和挂载点。

#### 目录树结构 （directory tree）

Linux使用目录树架构，根目录为“/”，所有文件和目录都从根目录开始。挂载是将磁盘分区的数据放置在指定目录下，称为挂载点。

#### 文件系统与目录树的关系（挂载）

挂载就是利用一个目录作为进入点，将磁盘分区的数据放置在该目录下。例如，将/home挂载到partition 2，这样/home目录下的数据就存储在partition 2中。

#### Linux安装程序提供的默认硬盘分区方式

初次安装Linux时，可以选择默认的分区方式或自定义分区方式。对于初学者，建议将整个系统分区为一个根分区“/”和一个交换分区“swap”。有经验的用户可以根据需要进行详细的分区规划。

分区示例：

- 初次接触Linux：只需分区“/”及“swap”即可。
- 建议分区方法：预留一个备用的剩余磁盘容量，方便后续练习和备份。

了解Linux的目录树和挂载点是安装和管理Linux系统的基础。通过合理的分区规划，可以提高系统的性能和数据的安全性。

## 2.3 安装Linux前的规划

在安装Linux之前，有许多需要考虑和规划的事项。这一小节将讨论如何选择适当的Linux发行版（distribution）、主机的服务规划与硬件的关系，以及如何进行主机硬盘的主要规划。

### 2.3.1 选择适当的distribution

选择适当的Linux发行版是安装Linux的第一步。每个发行版使用的都是Linux内核，不同之处主要在于软件安装模式和预装的软件包。选择适合自己需求的发行版尤为重要。

#### 选择发行版的几点建议：

1. **用途考虑**：如果是用于服务器，可以选择Red Hat Enterprise Linux (RHEL) 或 SuSE Enterprise Linux，因为它们稳定且有长期支持。对于个人学习和桌面使用，可以选择Ubuntu、Fedora、CentOS等。
2. **社区和支持**：选择有活跃社区和良好支持的发行版，可以获得更多的帮助和资源。
3. **更新和维护**：考虑发行版的更新频率和维护周期，确保系统的安全性和稳定性。

#### 下载Linux发行版

在台湾，有许多镜像站点可以快速下载Linux发行版。以下是一些下载CentOS 7的镜像站点：

- [国家高速网络中心](http://ftp.twaren.net/Linux/CentOS/7/isos/)
- [昆山科技大学](http://ftp.ksu.edu.tw/FTP/Linux/CentOS/7/isos/)
- [CentOS官方网站](http://mirror.centos.org/centos/7/isos/)

#### 镜像文件的下载和烧录

下载文件后，需要使用烧录软件以镜像文件格式烧录到光盘或USB启动盘。切记不要以数据文件格式烧录。

### 2.3.2 主机的服务规划与硬件的关系

主机的服务目的决定了所需的硬件配置。不同的服务类型需要不同的硬件支持。

#### 常见服务类型与硬件配置：

1. **NAT（IP分享器）**：需要较好的网卡，CPU和内存需求较小。适合小型企业或家庭网络。
2. **SAMBA（文件分享）**：需要较大的硬盘容量和较好的网卡。适合文件服务器。
3. **Mail（邮件服务器）**：需要大容量硬盘和高效能的网卡。/var目录需要独立并加大容量。
4. **Web（WWW服务器）**：需要较多的内存和较高性能的CPU。如果还提供数据库服务，RAM尤为重要。
5. **DHCP（自动分配IP）**：硬件需求较低，适合局域网内自动分配IP地址。
6. **FTP**：需要较大的硬盘容量和较好的网卡。适合大文件传输和共享。

### 2.3.3 主机硬盘的主要规划

硬盘分区不仅关系到系统的性能，还关系到数据的安全性。合理的分区规划可以在系统出现故障时更容易恢复数据。

#### 分区的基本模式：

1. **最简单的分区方法**：仅分区根目录和交换分区（/ 和 swap）。适合初学者，但数据安全性较低。
2. **稍微麻烦一点的方式**：根据用途将一些重要目录独立出来，如/boot, /home, /var和/tmp等。这样可以在某些分区出现问题时，不影响系统的其他部分。

#### 分区示例：

1. **根目录**：/
2. **交换分区**：swap
3. **用户目录**：/home
4. **可变数据目录**：/var
5. **临时文件目录**：/tmp

### 2.3.4 鸟哥的两个实际案例

#### 案例一：家用的小型Linux服务器，IP分享与文件分享中心

- **提供服务**：NAT功能，SAMBA文件分享。

- 硬件配置

  ：

  - CPU：AMD Athlon 4850e 省电型 CPU
  - 内存：4GB
  - 网卡：两张Realtek网卡
  - 硬盘：一颗640GB的磁盘
  - 显卡：内置显卡 Radeon HD 3200

- 硬盘分区

  ：

  - /, /usr, /var, /tmp独立分区
  - 1GB的Swap
  - 安装CentOS 5.x最新版本

#### 案例二：提供Linux的PC丛集（Cluster）计算机群

- **提供服务**：模式仿真软、硬件平台，内部研究工作分析。

- 硬件配置

  ：

  - 两部多核系统处理器（20核40绪和12核24绪），搭配10G网卡
  - 内置显卡
  - 运算用主机：一颗磁盘
  - 储存用主机：8颗2TB磁盘组成的磁盘阵列
  - 内存：128GB和96GB

- 硬盘分区

  ：

  - 运算主机：/boot, / 及 swap分区
  - 储存主机：系统磁盘（/boot, /, /home, /tmp, /var）和数据磁盘

通过这些实例，可以看到不同服务和用途的主机在硬件配置和分区规划上的差异。实际安装Linux前，必须仔细考虑主机的用途和服务需求，进行合理的规划

## 2.4 重点回顾

- 新添购计算机硬件配备时，需要考虑的角度有“游戏机/工作机”、“性能/价格比”、“性能/消耗瓦数”、“支持度”等；
- 旧的硬件配备可能由于保存的问题或者是电子零件老化的问题，导致计算机系统非常容易在运行过程中出现不明的死机情况
- Red Hat的硬件支持：https://hardware.redhat.com/?pagename=hcl
- 在Linux系统中，每个设备都被当成一个文件来对待，每个设备都会有设备文件名。
- 磁盘设备文件名通常分为两种，实际SATA/USB设备文件名为/dev/sd[a-p]，而虚拟机的设备可能为/dev/vd[a-p]
- 磁盘的第一个扇区主要记录了两个重要的信息，分别是： （1）主要开机记录区（Master Boot Record, MBR）：可以安装开机管理程序的地方，有446 Bytes （1）分区表（partition table）：记录整颗硬盘分区的状态，有64 Bytes；
- 磁盘的 MBR 分区方式中，主要与延伸分区最多可以有四个，逻辑分区的设备文件名号码，一定由5号开始；
- 如果磁盘容量大于 2TB 以上时，系统会自动使用 GPT 分区方式来处理磁盘分区。
- GPT 分区已经没有延伸与逻辑分区的概念，你可以想像成所有的分区都是主分区！
- 某些操作系统要使用 GPT 分区时，必须要搭配 UEFI 的新型 BIOS 格式才可安装使用。
- 开机的流程由：BIOS-->MBR-->-->boot loader-->核心文件；
- boot loader的功能主要有：提供菜单、载入核心、转交控制权给其他loader
- boot loader可以安装的地点有两个，分别是 MBR 与 boot sector
- Linux操作系统的文件使用目录树系统，与磁盘的对应需要有“挂载”的动作才行；
- 新手的简单分区，建议只要有/及swap两个分区即可