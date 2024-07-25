# 第二十二章、软件安装 RPM, SRPM 与 YUM

最近更新日期：20//

虽然使用源代码进行软件编译可以具有客制化的设置，但对于 Linux distribution 的发布商来说，则有软件管理不易的问题， 毕竟不是每个人都会进行源代码编译的。如果能够将软件预先在相同的硬件与操作系统上面编译好才发布的话， 不就能够让相同的 distribution 具有完全一致的软件版本吗？如果再加上简易的安装/移除/管理等机制的话， 对于软件控管就会简易的多。有这种东西吗？有的，那就是 RPM 与 YUM 这两个好用的咚咚。 既然这么好用，我们当然不能错过学习机会啰！赶紧来参详参详！

## 22.1 软件管理员简介

在前一章我们介绍了使用源代码的方式安装软件，这种方式需要用户了解操作系统和环境设置，并手动编译和安装软件，过程繁琐且易出错。为了简化这个过程，Linux 社区和企业开发了预编译的二进制软件包，并提供了一些工具来管理这些软件包。

### 22.1.1 Linux 界的两大主流: RPM 与 DPKG

为了简化软件管理和安装，Linux 社区开发了两大主流的软件包管理机制：RPM 和 DPKG。

- **DPKG**：由 Debian 社区开发，适用于 Debian 及其衍生版，如 Ubuntu。
- **RPM**：由 Red Hat 开发，适用于 Red Hat 系列及其衍生版，如 Fedora 和 CentOS。

这两种机制都通过预编译的软件包和软件库来管理软件的安装、升级和删除。

| Distribution   | 软件管理机制 | 使用指令      | 线上升级机制（指令） |
| -------------- | ------------ | ------------- | -------------------- |
| Red Hat/Fedora | RPM          | rpm, rpmbuild | YUM (yum)            |
| Debian/Ubuntu  | DPKG         | dpkg          | APT (apt-get)        |

### 22.1.2 什么是 RPM 与 SRPM

**RPM** (RedHat Package Manager) 是一种软件包管理机制，主要特点包括：

1. 已编译的二进制文件，方便安装。
2. 记录软件包的信息，方便查询、升级和删除。
3. 提供软件包的依赖关系检查，确保安装环境满足要求。

然而，由于 RPM 是预编译的，安装时需要确保系统环境与软件包编译时一致，这可能导致依赖性问题。

**SRPM** (Source RPM) 包含源代码，用户可以自行编译，解决系统环境不一致的问题。通过 SRPM，用户可以根据需要修改配置文件，然后编译成适合自己系统的 RPM 文件。

| 文件格式 | 文件名格式  | 直接安装与否 | 内含程序类型 | 可否修改参数并编译 |
| -------- | ----------- | ------------ | ------------ | ------------------ |
| RPM      | xxx.rpm     | 可           | 已编译       | 不可               |
| SRPM     | xxx.src.rpm | 不可         | 未编译源代码 | 可                 |

### 22.1.3 什么是 i386, i586, i686, noarch, x86_64

RPM 文件名包含软件名称、版本信息、打包次数和适用的硬件平台。例如，`rp-pppoe-3.11-5.el7.x86_64.rpm`：

- 软件名称：rp-pppoe
- 版本信息：3.11
- 释出版本次数：5
- 操作硬件平台：el7.x86_64

| 平台名称 | 适合平台说明                       |
| -------- | ---------------------------------- |
| i386     | 适用于所有 x86 平台                |
| i586     | 适用于 Pentium MMX 及同级别 CPU    |
| i686     | 适用于 Pentium II 及以上等级的 CPU |
| x86_64   | 适用于 64 位 CPU                   |
| noarch   | 与硬件无关，通常为脚本或配置文件   |

### 22.1.4 RPM 的优点

RPM 通过预编译和打包，提供以下优点：

1. 安装方便，不需重新编译。
2. 检查安装环境，避免错误安装。
3. 提供详细的软件包信息，方便管理。
4. 记录软件包信息，便于查询、升级和删除。

### 22.1.5 RPM 属性相依的克服方式： YUM 线上升级

RPM 软件包管理面临的软件依赖问题，可以通过 YUM 机制解决。YUM 通过分析软件包的依赖关系，自动安装所需的软件包，简化了安装过程。

YUM 的工作流程如下：

1. 将软件包上传到 YUM 服务器，并生成依赖关系列表。
2. 用户端请求更新软件包列表，并与本地 RPM 数据库进行比较。
3. YUM 自动下载所需的软件包并安装。

![YUM 使用的流程示意图](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/img/yum-01.gif)

## 22.2 RPM 软件管理程序： rpm

使用 RPM 进行软件管理其实很简单，只需使用 `rpm` 指令即可。虽然现在大多数情况下可以用 `yum` 来安装软件，但查询和检验功能还是需要通过 `rpm` 来完成的。我们先了解 RPM 默认的安装路径以及相关文件的存放位置。

### 22.2.1 RPM 默认安装的路径

RPM 文件在安装时会读取文件内的设置参数内容，并比对系统环境以确认是否满足软件的属性相依性要求。安装成功后，软件相关的信息会记录在 `/var/lib/rpm/` 目录下的数据库文件中。这个目录非常重要，未来所有的软件升级、查询和验证都依赖于此。

通常，软件文件会放置在以下目录中：

- `/etc`：配置文件目录，例如 `/etc/crontab`
- `/usr/bin`：可执行文件目录
- `/usr/lib`：动态函数库目录
- `/usr/share/doc`：软件使用手册与说明文档目录
- `/usr/share/man`：man page 文件目录

### 22.2.2 RPM 安装（install）

安装 RPM 文件通常使用 `rpm -ivh` 命令，具体用法如下：

```
bash
复制代码
[root@study ~]# rpm -ivh /mnt/Packages/rp-pppoe-3.11-5.el7.x86_64.rpm
```

常用选项与参数：

- `-i`：install，安装
- `-v`：verbose，显示详细安装信息
- `-h`：hash，显示安装进度

例如：

```
bash复制代码[root@study ~]# rpm -ivh /mnt/Packages/rp-pppoe-3.11-5.el7.x86_64.rpm
Preparing...                          ################################# [100%]
Updating / installing...
   1:rp-pppoe-3.11-5.el7              ################################# [100%]
```

如果需要强制安装可以使用以下选项：

- `--nodeps`：忽略依赖性问题
- `--replacefiles`：覆盖已有文件
- `--replacepkgs`：重新安装已安装的软件
- `--force`：强制安装
- `--test`：测试是否可以安装
- `--justdb`：仅更新数据库
- `--nosignature`：跳过签名检查
- `--prefix`：安装到指定目录
- `--noscripts`：不执行安装脚本

### 22.2.3 RPM 升级与更新（upgrade/freshen）

升级软件使用 `rpm -Uvh` 或 `rpm -Fvh`，两者的区别在于：

- `-Uvh`：如果软件未安装，则直接安装；如果已安装旧版，则更新到新版
- `-Fvh`：仅更新已安装的软件，未安装的软件不会被安装

例如：

```
bash复制代码[root@study ~]# rpm -Uvh package_name.rpm
[root@study ~]# rpm -Fvh package_name.rpm
```

### 22.2.4 RPM 查询（query）

查询已安装的软件信息或未安装的 RPM 文件信息使用 `rpm -q` 系列命令：

```
bash复制代码[root@study ~]# rpm -qa                              # 列出所有已安装的软件
[root@study ~]# rpm -q 软件名称                       # 查询某个已安装的软件
[root@study ~]# rpm -qf 文件名                         # 查询文件属于哪个软件
[root@study ~]# rpm -qp 文件名                        # 查询未安装的 RPM 文件
```

常用选项与参数：

- `-qi`：列出详细信息
- `-ql`：列出所有文件与目录
- `-qc`：列出配置文件
- `-qd`：列出说明文档
- `-qR`：列出依赖的软件
- `-q --scripts`：列出安装后执行的脚本

例如：

```
bash复制代码[root@study ~]# rpm -q logrotate
logrotate-3.8.6-4.el7.x86_64

[root@study ~]# rpm -ql logrotate
/etc/cron.daily/logrotate
/etc/logrotate.conf
...

[root@study ~]# rpm -qi logrotate
Name        : logrotate
Version     : 3.8.6
...

[root@study ~]# rpm -qc logrotate
/etc/logrotate.conf
...

[root@study ~]# rpm -qR logrotate
/bin/sh
coreutils >= 5.92
...

[root@study ~]# rpm -qf /bin/sh
bash-4.2.46-12.el7.x86_64
```

### 22.2.5 RPM 验证与数码签章（Verify/signature）

验证 RPM 文件是否被更改或是否存在问题使用 `rpm -V` 系列命令：

```
bash复制代码[root@study ~]# rpm -Va
[root@study ~]# rpm -V 软件名称
[root@study ~]# rpm -Vp RPM 文件名
[root@study ~]# rpm -Vf 文件名
```

例如：

```
bash复制代码[root@study ~]# rpm -V logrotate
..5....T.  c /etc/logrotate.conf
```

`rpm -V` 的输出解释：

- `S`：文件大小变化
- `M`：文件类型或属性变化
- `5`：MD5 校验和变化
- `D`：主/次设备号变化
- `L`：链接路径变化
- `U`：文件属主变化
- `G`：文件属组变化
- `T`：文件时间变化
- `P`：文件功能变化

配置文件类型：

- `c`：配置文件
- `d`：文档文件
- `g`：鬼文件
- `l`：授权文件
- `r`：读我文件

数码签章验证可以通过 `rpm --import` 导入公钥文件来实现，例如：

```
bash
复制代码
[root@study ~]# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

### 22.2.6 RPM 反安装与重建数据库（erase/rebuilddb）

反安装软件使用 `rpm -e`：

```
bash
复制代码
[root@study ~]# rpm -e 软件名称
```

重建 RPM 数据库使用 `rpm --rebuilddb`：

```
bash
复制代码
[root@study ~]# rpm --rebuilddb
```

通过以上介绍，您可以使用 `rpm` 指令进行安装、查询、验证、升级和反安装操作，管理系统中的 RPM 软件包。

## 22.3 YUM 线上升级机制

YUM 通过分析 RPM 的标头数据，根据各软件的相关性制作出属性相依时的解决方案，自动处理软件的依赖性问题，解决软件安装、移除和升级的问题。详细的 YUM 服务器与用户端之间的沟通，可以参考前面的图 22.1.1。

### 22.3.1 利用 YUM 进行查询、安装、升级与移除功能

YUM 的使用非常简单，主要通过 `yum` 指令来操作，以下是常用的查询、安装、升级和移除功能。

#### 查询功能

通过 YUM 可以查询原版 distribution 提供的软件，或查询已知软件的名称和功能：

```
bash
复制代码
[root@study ~]# yum [option] [查询工作项目] [相关参数]
```

常用选项与参数：

- `-y`：自动提供 yes 的回应
- `--installroot=/some/path`：将软件安装在指定路径

查询工作项目和相关参数：

- `search`：搜寻软件名称或描述中的关键字
- `list`：列出所有软件名称与版本
- `info`：列出详细信息
- `provides`：从文件名搜索提供该文件的软件

示例：

1. 搜寻与 RAID 相关的软件：

```
bash
复制代码
[root@study ~]# yum search raid
```

1. 查看 `mdadm` 软件的详细信息：

```
bash
复制代码
[root@study ~]# yum info mdadm
```

1. 列出所有可供安装的软件：

```
bash
复制代码
[root@study ~]# yum list
```

1. 列出可供升级的软件：

```
bash
复制代码
[root@study ~]# yum list updates
```

1. 查找提供 `passwd` 文件的软件：

```
bash
复制代码
[root@study ~]# yum provides passwd
```

#### 安装和升级功能

通过 `yum install` 和 `yum update` 进行安装和升级：

```
bash
复制代码
[root@study ~]# yum install pam-devel
bash
复制代码
[root@study ~]# yum update
```

#### 移除功能

通过 `yum remove` 移除软件：

```
bash
复制代码
[root@study ~]# yum remove pam-devel
```

### 22.3.2 YUM 的配置文件

YUM 的配置文件位于 `/etc/yum.repos.d/` 目录中，配置文件的主要内容包括：

- `[base]`：软件库名称
- `name`：软件库的说明
- `mirrorlist`：列出软件库的映射站台
- `baseurl`：指定软件库的实际网址
- `enabled`：启用该软件库
- `gpgcheck`：检查 RPM 文件的数码签章
- `gpgkey`：数码签章的公钥文件位置

示例配置：

```
bash复制代码[root@study ~]# vim /etc/yum.repos.d/CentOS-Base.repo
[base]
name=CentOS-$releasever - Base
baseurl=http://ftp.ksu.edu.tw/FTP/CentOS/7/os/x86_64/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[updates]
name=CentOS-$releasever - Updates
baseurl=http://ftp.ksu.edu.tw/FTP/CentOS/7/updates/x86_64/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[extras]
name=CentOS-$releasever - Extras
baseurl=http://ftp.ksu.edu.tw/FTP/CentOS/7/extras/x86_64/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

清除本机缓存的旧数据：

```
bash
复制代码
[root@study ~]# yum clean all
```

### 22.3.3 YUM 的软件群组功能

YUM 还支持安装和移除软件群组，通过 `yum groupinstall` 和 `yum groupremove` 可以安装和移除整个软件群组。

常用命令：

```
bash复制代码[root@study ~]# yum grouplist
[root@study ~]# yum groupinfo "Scientific Support"
[root@study ~]# yum groupinstall "Scientific Support"
```

修改 `/etc/yum.conf` 以安装 optional 软件：

```
bash复制代码[root@study ~]# vim /etc/yum.conf
group_package_types=default, mandatory, optional
```

### 22.3.4 EPEL/ELRepo 外挂软件以及自订配置文件

可以通过添加 EPEL 或 ELRepo 软件库来安装未包含在 CentOS 默认软件库中的软件。

EPEL 配置示例：

```
bash复制代码[root@study ~]# vim /etc/yum.repos.d/epel.repo
[epel]
name=epel packages
baseurl=https://dl.fedoraproject.org/pub/epel/7/x86_64/
gpgcheck=0
enabled=0
```

使用 EPEL 安装软件：

```
bash
复制代码
[root@study ~]# yum --enablerepo=epel install netcdf R
```

使用本机光盘作为软件源：

```
bash复制代码[root@study ~]# vim /etc/yum.repos.d/cdrom.repo
[mycdrom]
name=mycdrom
baseurl=file:///mnt
gpgcheck=0
enabled=0

[root@study ~]# yum --enablerepo=mycdrom install software_name
```

### 22.3.5 全系统自动升级

通过 crontab 实现系统自动升级：

```
bash
复制代码
[root@study ~]# echo '10 1 * * * root /usr/bin/yum -y update' > /etc/cron.d/yumupdate
```

### 22.3.6 管理的抉择：RPM 还是 Tarball

1. 优先选择原厂的 RPM 功能。
2. 选择软件官网释出的 RPM 或者是提供的软件库网址。
3. 利用 Tarball 安装特殊软件。
4. 用 Tarball 测试新版软件。

### 22.3.7 基础服务管理：以 Apache 为例

通过 YUM 安装、启动和配置基础服务，以 Apache 为例：

1. 安装所需的软件：

```
bash
复制代码
[root@study ~]# yum install httpd php mariadb-server php-mysql
```

1. 启动并设置开机启动：

```
bash复制代码[root@study ~]# systemctl start httpd
[root@study ~]# systemctl enable httpd
```

1. 配置防火墙：

```
bash复制代码[root@study ~]# firewall-cmd --add-service="http"
[root@study ~]# firewall-cmd --permanent --add-service="http"
```

1. 测试服务：

打开浏览器，访问 `http://localhost`，确认 Apache 服务正常运行。

通过这些步骤，可以轻松管理和配置 Linux 系统中的软件和服务。

## 22.4 SRPM 的使用：rpmbuild（Optional）

在了解了 RPM 类型的软件之后，我们来讨论一下包含源代码的 SRPM 该如何使用。假如我们从网络上下载了一个 SRPM 文件，该如何安装它？如果想要修改这个 SRPM 里面的源代码与相关设置值，又该如何修改并重新编译？新版的 RPM 已经将 RPM 与 SRPM 的指令分开，SRPM 使用的是 `rpmbuild` 指令，而不是 `rpm`。

### 22.4.1 利用默认值安装 SRPM 文件（--rebuild/--recompile）

假设我们下载了一个 SRPM 文件，不打算修改里面的源代码与相关设置值，可以直接编译并安装。可以使用 `rpmbuild` 配合选项来实现，主要选项有：

- `--rebuild`：将后面的 SRPM 进行“编译”和“打包”，产生的 RPM 文件没有安装到系统上。
- `--recompile`：直接“编译”“打包”并且“安装”。

这两个选项都没有修改 SRPM 内的设置值，仅是通过再次编译来产生 RPM 可安装软件文件。如果编译顺利，编译过程中的中间暂存盘会被自动删除，如果发生错误，中间文件会被保留在系统上，等待除错。

**示例：编译并安装 ntp 软件的 SRPM**

1. 先下载软件：

   ```
   bash
   复制代码
   wget http://vault.centos.org/7.1.1503/updates/Source/SPackages/ntp-4.2.6p5-19.el7.centos.1.src.rpm
   ```

2. 再尝试直接编译：

   ```
   bash
   复制代码
   rpmbuild --rebuild ntp-4.2.6p5-19.el7.centos.1.src.rpm
   ```

3. 安装相依软件：

   ```
   bash
   复制代码
   yum install libcap-devel openssl-devel libedit-devel pps-tools-devel autogen autogen-libopts-devel
   ```

4. 再次尝试编译：

   ```
   bash
   复制代码
   rpmbuild --rebuild ntp-4.2.6p5-19.el7.centos.1.src.rpm
   ```

最终的软件会放置在 `/root/rpmbuild/RPMS/x86_64/ntp-4.2.6p5-19.el7.centos.1.x86_64.rpm`。

### 22.4.2 SRPM 使用的路径与需要的软件

SRPM 包含源代码，其中有配置文件。编译时会使用到一些目录，这些目录与操作者的主文件夹有关。假设以 root 身份操作，使用的目录如下：

- `/root/rpmbuild/SPECS`：放置软件的配置文件。
- `/root/rpmbuild/SOURCES`：放置软件的原始文件（如 `.tar.gz` 文件）和 `config` 配置文件。
- `/root/rpmbuild/BUILD`：编译过程中暂存的数据。
- `/root/rpmbuild/RPMS`：编译成功后打包完成的文件。
- `/root/rpmbuild/SRPMS`：放置 SRPM 封装的文件。

**示例：安装并查阅 ntp 的 SRPM**

1. 安装 SRPM：

   ```
   bash
   复制代码
   rpm -ivh ntp-4.2.6p5-19.el7.centos.1.src.rpm
   ```

2. 查阅 `/root/rpmbuild` 目录内容：

   ```
   bash
   复制代码
   ll -l /root/rpmbuild
   ```

### 22.4.3 配置文件的主要内容（`.spec`）

在 `/root/rpmbuild/SOURCES` 放置原始文件和补丁文件后，编译步骤如 `./configure, make, make check, make install` 写入 SPECS 目录的配置文件（`.spec`）。

**示例：查看和修改 ntp.spec**

1. 查看 `ntp.spec`：

   ```
   bash
   复制代码
   vim /root/rpmbuild/SPECS/ntp.spec
   ```

配置文件中重要的部分：

- **系统整体信息**：

  ```
  text复制代码Summary: The NTP daemon and utilities
  Name: ntp
  Version: 4.2.6p5
  Release: 19%{?dist}.1
  License: (MIT and BSD and BSD with advertising) and GPLv2
  Group: System Environment/Daemons
  Source0: http://www.eecis.udel.edu/~ntp/ntp_spool/ntp4/ntp-%{version}.tar.gz
  ```

- **%description**：简短说明软件。

- **%prep**：编译前的预处理，如补丁。

- **%build**：编译步骤，如 `./configure, make`。

- **%install**：安装步骤。

- **%files**：安装的文件和目录。

- **%changelog**：更新历史记录。

### 22.4.4 SRPM 的编译指令（-ba/-bb）

使用 `rpmbuild` 编译 SRPM：

```
bash复制代码[root@study ~]# rpmbuild -ba ntp.spec  # 编译并同时产生 RPM 与 SRPM 文件
[root@study ~]# rpmbuild -bb ntp.spec  # 仅编译成 RPM 文件
```

示例：编译 ntp.spec 并查找生成的 RPM 文件：

```
bash复制代码cd /root/rpmbuild/SPECS
rpmbuild -ba ntp.spec
find /root/rpmbuild -name 'ntp*rpm'
```

### 22.4.5 一个打包自己软件的范例

创建并打包自己的软件：

1. **创建源代码文件 tarball**：

   ```
   bash复制代码cd /root/rpmbuild/SOURCES
   wget http://linux.vbird.org/linux_basic/0520source/main-0.1.tgz
   wget http://linux.vbird.org/linux_basic/0520source/main_0.1_to_0.2.patch
   ```

2. **创建 `.spec` 文件**：

   ```
   bash复制代码cd /root/rpmbuild/SPECS
   vim main.spec
   ```

   ```
   text复制代码Name:           main
   Version:        0.1
   Release:        1%{?dist}
   Summary:        Shows sin and cos value.
   Group:          Scientific Support
   License:        GPLv2
   URL:            http://linux.vbird.org/
   Source0:        main-0.1.tgz
   Patch0:         main_0.1_to_0.2.patch
   
   %description
   This package will let you input your name and calculate sin cos value.
   
   %prep
   %setup -q
   %patch0 -p1
   
   %build
   make clean main
   
   %install
   mkdir -p %{buildroot}/usr/local/bin
   install -m 755 main %{buildroot}/usr/local/bin
   
   %files
   /usr/local/bin/main
   
   %changelog
   * Wed Sep 09 2015 VBird Tsai <[email protected]> 0.2
   - build the program
   ```

3. **编译生成 RPM 和 SRPM**：

   ```
   bash
   复制代码
   rpmbuild -ba main.spec
   ```

4. **安装和测试**：

   ```
   bash复制代码yum install /root/rpmbuild/RPMS/x86_64/main-0.1-1.el7.centos.x86_64.rpm
   rpm -ql main
   rpm -qi main
   ```

通过上述步骤，可以将自己的软件打包为 RPM 文件，便于安装和管理。

## 22.5 重点回顾

- 为了避免使用者自行编译的困扰，开发商自行在特定的硬件与操作系统平台上面预先编译好软件， 并将软件以特殊格式封包成文件，提供终端用户直接安装到固定的操作系统上，并提供简单的查询/安装/移除等流程。 此称为软件管理员。常见的软件管理员有 RPM 与 DPKG 两大主流。
- RPM 的全名是 RedHat Package Manager，原本是由 Red Hat 公司所发展的，流传甚广；
- RPM 类型的软件中，所含有的软件是经过编译后的 binary program ，所以可以直接安装在使用者端的系统上， 不过，也由于如此，所以 RPM 对于安装者的环境要求相当严格；
- RPM 除了将软件安装至使用者的系统上之外，还会将该软件的版本、名称、文件与目录配置、系统需求等等均记录于数据库 （/var/lib/rpm） 当中，方便未来的查询与升级、移除；
- RPM 可针对不同的硬件等级来加以编译，制作出来的文件可于扩展名 （i386, i586, i686, x86_64, noarch） 来分辨；
- RPM 最大的问题为软件之间的相依性问题；
- SRPM 为 Source RPM ，内含的文件为 Source code 而非为 binary file ，所以安装 SRPM 时还需要经过 compile ，不过，SRPM 最大的优点就是可以让使用者自行修改设置参数 （makefile/configure 的参数） ，以符合使用者自己的 Linux 环境；
- RPM 软件的属性相依问题，已经可以借由 yum 或者是 APT 等方式加以克服。 CentOS 使用的就是 yum 机制。
- yum 服务器提供多个不同的软件库放置个别的软件，以提供用户端分别管理软件类别。