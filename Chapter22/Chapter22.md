# 第二十二章、软件安装 RPM, SRPM 与 YUM

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

#### 原文：

由于自由软件的蓬勃发展，加上大型 Unix-Like 主机的强大性能，让很多软件开发者将他们的软件使用 Tarball 来释出。 后来 Linux 发展起来后，由一些企业或社群将这些软件收集起来制作成为 distributions 以发布这好用的 Linux 操作系统。但后来发现到，这些 distribution 的软件管理实在伤脑筋， 如果软件有漏洞时，又该如何修补呢？使用 tarball 的方式来管理吗？又常常不晓得到底我们安装过了哪些程序？ 因此，一些社群与企业就开始思考 Linux 的软件管理方式。

如同刚刚谈过的方式，Linux 开发商先在固定的硬件平台与操作系统平台上面将需要安装或升级的软件编译好， 然后将这个软件的所有相关文件打包成为一个特殊格式的文件，在这个软件文件内还包含了预先侦测系统与相依软件的脚本， 并提供记载该软件提供的所有文件信息等。最终将这个软件文件释出。用户端取得这个文件后，只要通过特定的指令来安装， 那么该软件文件就会依照内部的脚本来侦测相依的前驱软件是否存在，若安装的环境符合需求，那就会开始安装， 安装完成后还会将该软件的信息写入软件管理机制中，以达成未来可以进行升级、移除等动作呢。

目前在 Linux 界软件安装方式最常见的有两种，分别是：

- dpkg： 这个机制最早是由 Debian Linux 社群所开发出来的，通过 dpkg 的机制， Debian 提供的软件就能够简单的安装起来，同时还能提供安装后的软件信息，实在非常不错。 只要是衍生于 Debian 的其他 Linux distributions 大多使用 dpkg 这个机制来管理软件的， 包括 B2D, Ubuntu 等等。
- RPM： 这个机制最早是由 Red Hat 这家公司开发出来的，后来实在很好用，因此很多 distributions 就使用这个机制来作为软件安装的管理方式。包括 Fedora, CentOS, SuSE 等等知名的开发商都是用这咚咚。

如前所述，不论 dpkg/rpm 这些机制或多或少都会有软件属性相依的问题，那该如何解决呢？ 其实前面不是谈到过每个软件文件都有提供相依属性的检查吗？那么如果我们将相依属性的数据做成列表， 等到实际软件安装时，若发生有相依属性的软件状况时，例如安装 A 需要先安装 B 与 C ，而安装 B 则需要安装 D 与 E 时，那么当你要安装 A ，通过相依属性列表，管理机制自动去取得 B, C, D, E 来同时安装， 不就解决了属性相依的问题吗？

没错！您真聪明！目前新的 Linux 开发商都有提供这样的“线上升级”机制，通过这个机制， 原版光盘就只有第一次安装时需要用到而已，其他时候只要有网络，你就能够取得原本开发商所提供的任何软件了呢！ 在 dpkg 管理机制上就开发出 APT 的线上升级机制，RPM 则依开发商的不同，有 Red Hat 系统的 yum ， SuSE 系统的 Yast Online Update （YOU） 等。

| distribution 代表 | 软件管理机制 | 使用指令      | 线上升级机制（指令） |
| ----------------- | ------------ | ------------- | -------------------- |
| Red Hat/Fedora    | RPM          | rpm, rpmbuild | YUM （yum）          |
| Debian/Ubuntu     | DPKG         | dpkg          | APT （apt-get）      |

我们这里使用的是 CentOS 系统嘛！所以说：使用的软件管理机制为 RPM 机制，而用来作为线上升级的方式则为 yum ！下面就让我们来谈谈 RPM 与 YUM 的相关说明吧！



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

#### 原文：

RPM 全名是“ RedHat Package Manager ”简称则为 RPM 啦！顾名思义，当初这个软件管理的机制是由 Red Hat 这家公司发展出来的。 RPM 是以一种数据库记录的方式来将你所需要的软件安装到你的 Linux 系统的一套管理机制。

他最大的特点就是将你要安装的软件先编译过， 并且打包成为 RPM 机制的包装文件，通过包装好的软件里头默认的数据库记录， 记录这个软件要安装的时候必须具备的相依属性软件，当安装在你的 Linux 主机时， RPM 会先依照软件里头的数据查询 Linux 主机的相依属性软件是否满足， 若满足则予以安装，若不满足则不予安装。那么安装的时候就将该软件的信息整个写入 RPM 的数据库中，以便未来的查询、验证与反安装！这样一来的优点是：

1. 由于已经编译完成并且打包完毕，所以软件传输与安装上很方便 （不需要再重新编译）；
2. 由于软件的信息都已经记录在 Linux 主机的数据库上，很方便查询、升级与反安装

但是这也造成些许的困扰。由于 RPM 文件是已经包装好的数据，也就是说， 里面的数据已经都“编译完成”了！所以，该软件文件几乎只能安装在原本默认的硬件与操作系统版本中。 也就是说，你的主机系统环境必须要与当初创建这个软件文件的主机环境相同才行！ 举例来说，rp-pppoe 这个 ADSL 拨接软件，他必须要在 ppp 这个软件存在的环境下才能进行安装！如果你的主机并没有 ppp 这个软件，那么很抱歉，除非你先安装 ppp 否则 rp-pppoe 就是不让你安装的 （当然你可以强制安装，但是通常都会有点问题发生就是了！）。

所以，通常不同的 distribution 所释出的 RPM 文件，并不能用在其他的 distributions 上。举例来说，Red Hat 释出的 RPM 文件，通常无法直接在 SuSE 上面进行安装的。更有甚者，相同 distribution 的不同版本之间也无法互通，例如 CentOS 6.x 的 RPM 文件就无法直接套用在 CentOS 7.x ！因此，这样可以发现这些软件管理机制的问题是：

1. 软件文件安装的环境必须与打包时的环境需求一致或相当；
2. 需要满足软件的相依属性需求；
3. 反安装时需要特别小心，最底层的软件不可先移除，否则可能造成整个系统的问题！

那怎么办？如果我真的想要安装其他 distributions 提供的好用的 RPM 软件文件时？ 呵呵！还好，还有 SRPM 这个东西！SRPM 是什么呢？顾名思义，他是 Source RPM 的意思，也就是这个 RPM 文件里面含有源代码哩！特别注意的是，这个 SRPM 所提供的软件内容“并没有经过编译”， 它提供的是源代码喔！

通常 SRPM 的扩展名是以 ***.src.rpm 这种格式来命名的。不过，既然 SRPM 提供的是源代码，那么为什么我们不使用 Tarball 直接来安装就好了？这是因为 SRPM 虽然内容是源代码， 但是他仍然含有该软件所需要的相依性软件说明、以及所有 RPM 文件所提供的数据。同时，他与 RPM 不同的是，他也提供了参数配置文件 （就是 configure 与 makefile）。所以，如果我们下载的是 SRPM ，那么要安装该软件时，你就必须要：

- 先将该软件以 RPM 管理的方式编译，此时 SRPM 会被编译成为 RPM 文件；
- 然后将编译完成的 RPM 文件安装到 Linux 系统当中

怪了，怎么 SRPM 这么麻烦呐！还要重新编译一次，那么我们直接使用 RPM 来安装不就好了？通常一个软件在释出的时候，都会同时释出该软件的 RPM 与 SRPM 。我们现在知道 RPM 文件必须要在相同的 Linux 环境下才能够安装，而 SRPM 既然是源代码的格式，自然我们就可以通过修改 SRPM 内的参数配置文件，然后重新编译产生能适合我们 Linux 环境的 RPM 文件，如此一来，不就可以将该软件安装到我们的系统当中，而不必与原作者打包的 Linux 环境相同了？这就是 SRPM 的用处了！

| 文件格式 | 文件名格式  | 直接安装与否 | 内含程序类型   | 可否修改参数并编译 |
| -------- | ----------- | ------------ | -------------- | ------------------ |
| RPM      | xxx.rpm     | 可           | 已编译         | 不可               |
| SRPM     | xxx.src.rpm | 不可         | 未编译之源代码 | 可                 |

![鸟哥的图示](image/vbird_face.gif)

**Tips** 为何说 CentOS 是“社群维护的企业版”呢？ Red Hat 公司的 RHEL 释出后，连带会将 SRPM 释出。 社群的朋友就将这些 SRPM 收集起来并重新编译成为所需要的软件，再重复释出成为 CentOS，所以才能号称与 Red Hat 的 RHEL 企业版同步啊！真要感谢 SRPM 哩！如果你想要理解 CentOS 是如何编译一支程序的， 也能够通过学习 SRPM 内含的编译参数，来学习的啊！



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

#### 原文：

从上面的说明，现在我们知道 RPM 与 SRPM 的格式分别为：

```
xxxxxxxxx.rpm   &lt;==RPM 的格式，已经经过编译且包装完成的 rpm 文件；
xxxxx.src.rpm   &lt;==SRPM的格式，包含未编译的源代码信息。
```

那么我们怎么知道这个软件的版本、适用的平台、编译释出的次数呢？只要通过文件名就可以知道了！例如 rp-pppoe-3.11-5.el7.x86_64.rpm 这的文件的意义为：

```
rp-pppoe -        3.11   -     5        .el7.x86_64  .rpm
软件名称   软件的版本信息 释出的次数 适合的硬件平台 扩展名
```

除了后面适合的硬件平台与扩展名外，主要是以“-”来隔开各个部分，这样子可以很清楚的发现该软件的名称、 版本信息、打包次数与操作的硬件平台！好了，来谈一谈每个不同的地方吧：

- 软件名称： 当然就是每一个软件的名称了！上面的范例就是 rp-pppoe 。

- 版本信息： 每一次更新版本就需要有一个版本的信息，否则如何知道这一版是新是旧？这里通常又分为主版本跟次版本。以上面为例，主版本为 3 ，在主版本的架构下更动部分源代码内容，而释出一个新的版本，就是次版本啦！以上面为例，就是 11 啰！所以版本名就为 3.11

- 释出版本次数： 通常就是编译的次数啦！那么为何需要重复的编译呢？这是由于同一版的软件中，可能由于有某些 bug 或者是安全上的顾虑，所以必须要进行小幅度的 patch 或重设一些编译参数。 设置完成之后重新编译并打包成 RPM 文件！因此就有不同的打包数出现了！

- 操作硬件平台： 这是个很好玩的地方，由于 RPM 可以适用在不同的操作平台上，但是不同的平台设置的参数还是有所差异性！ 并且，我们可以针对比较高阶的 CPU 来进行最优化参数的设置，这样才能够使用高阶 CPU 所带来的硬件加速功能。 所以就有所谓的 i386, i586, i686, x86_64 与 noarch 等的文件名称出现了！

  ```
  | 平台名称 | 适合平台说明 |
  | --- | --- |
  | i386 | 几乎适用于所有的 x86 平台，不论是旧的 pentum 或者是新的 Intel Core 2 与 K8 系列的 CPU 等等，都可以正常的工作！那个 i 指的是 Intel 相容的 CPU 的意思，至于 386 不用说，就是 CPU 的等级啦！ |
  | i586 | 就是针对 586 等级的计算机进行最优化编译。那是哪些 CPU 呢？包括 pentum 第一代 MMX CPU， AMD 的 K5, K6 系列 CPU （socket 7 插脚） 等等的 CPU 都算是这个等级； |
  | i686 | 在 pentun II 以后的 Intel 系列 CPU ，及 K7 以后等级的 CPU 都属于这个 686 等级！ 由于目前市面上几乎仅剩 P-II 以后等级的硬件平台，因此很多 distributions 都直接释出这种等级的 RPM 文件。 |
  | x86_64 | 针对 64 位的 CPU 进行最优化编译设置，包括 Intel 的 Core 2 以上等级 CPU ，以及 AMD 的 Athlon64 以后等级的 CPU ，都属于这一类型的硬件平台。 |
  | noarch | 就是没有任何硬件等级上的限制。一般来说，这种类型的 RPM 文件，里面应该没有 binary program 存在， 较常出现的就是属于 shell script 方面的软件。 |
  ```

  截至目前为止 （2015），就算是旧的个人计算机系统，堪用与能用的设备大概都至少是 Intel Core 2 以上等级的计算机主机，泰半都是 64 位的系统了！ 因此目前 CentOS 7 仅推出 x86_64 的软件版本，并没有提供 i686 以下等级的软件了！如果你的系统还是很老旧的机器， 那才有可能不支持 64 位的 Linux 系统。此外，目前仅存的软件版本大概也只剩下 i686 及 x86_64 还有不分版本的 noarch 而已， i386 只有在某些很特别的软件上才看到的到啦！

  受惠于目前 x86 系统的支持方面，新的 CPU 都能够执行旧型 CPU 所支持的软件，也就是说硬件方面都可以向下相容的， 因此最低等级的 i386 软件可以安装在所有的 x86 硬件平台上面，不论是 32 位还是 64 位。但是反过来说就不行了。举例来说，目前硬件大多是 64 位的等级，因此你可以在该硬件上面安装 x86_64 或 i386 等级的 RPM 软件。但在你的旧型主机，例如 P-III/P-4 32 位机器上面，就不能够安装 x86_64 的软件！

根据上面的说明，其实我们只要选择 i686 版本来安装在你的 x86 硬件上面就肯定没问题。但是如果强调性能的话， 还是选择搭配你的硬件的 RPM 文件吧！毕竟该软件才有针对你的 CPU 硬件平台进行过参数最优化的编译嘛！





### 22.1.4 RPM 的优点

RPM 通过预编译和打包，提供以下优点：

1. 安装方便，不需重新编译。
2. 检查安装环境，避免错误安装。
3. 提供详细的软件包信息，方便管理。
4. 记录软件包信息，便于查询、升级和删除。

#### 原文：

由于 RPM 是通过预先编译并打包成为 RPM 文件格式后，再加以安装的一种方式，并且还能够进行数据库的记载。 所以 RPM 有以下的优点：

- RPM 内含已经编译过的程序与配置文件等数据，可以让使用者免除重新编译的困扰；
- RPM 在被安装之前，会先检查系统的硬盘容量、操作系统版本等，可避免文件被错误安装；
- RPM 文件本身提供软件版本信息、相依属性软件名称、软件用途说明、软件所含文件等信息，便于了解软件；
- RPM 管理的方式使用数据库记录 RPM 文件的相关参数，便于升级、移除、查询与验证。

为什么 RPM 在使用上很方便呢？我们前面提过， RPM 这个软件管理员所处理的软件，是由软件提供者在特定的 Linux 作业平台上面将该软件编译完成并且打包好。那使用者只要拿到这个打包好的软件， 然后将里头的文件放置到应该要摆放的目录，不就完成安装啰？对啦！就是这样！

但是有没有想过，我们在前一章里面提过的，有些软件是有相关性的，例如要安装网卡驱动程序，就得要有 kernel source 与 gcc 及 make 等软件。那么我们的 RPM 软件是否一定可以安装完成呢？如果该软件安装之后，却找不到他相关的前驱软件， 那不是挺麻烦的吗？因为安装好的软件也无法使用啊！

为了解决这种具有相关性的软件之间的问题 （就是所谓的软件相依属性），RPM 就在提供打包的软件时，同时加入一些讯息登录的功能，这些讯息包括软件的版本、 打包软件者、相依属性的其他软件、本软件的功能说明、本软件的所有文件记录等等，然后在 Linux 系统上面亦创建一个 RPM 软件数据库，如此一来，当你要安装某个以 RPM 型态提供的软件时，在安装的过程中， RPM 会去检验一下数据库里面是否已经存在相关的软件了， 如果数据库显示不存在，那么这个 RPM 文件“默认”就不能安装。呵呵！没有错，这个就是 RPM 类型的文件最为人所诟病的“软件的属性相依”问题啦！



### 22.1.5 RPM 属性相依的克服方式： YUM 线上升级

RPM 软件包管理面临的软件依赖问题，可以通过 YUM 机制解决。YUM 通过分析软件包的依赖关系，自动安装所需的软件包，简化了安装过程。

YUM 的工作流程如下：

1. 将软件包上传到 YUM 服务器，并生成依赖关系列表。
2. 用户端请求更新软件包列表，并与本地 RPM 数据库进行比较。
3. YUM 自动下载所需的软件包并安装。

![YUM 使用的流程示意图](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/img/yum-01.gif)

#### 原文：

为了重复利用既有的软件功能，因此很多软件都会以函数库的方式释出部分功能，以方便其他软件的调用应用， 例如 PAM 模块的验证功能。此外，为了节省使用者的数据量，目前的 distributions 在释出软件时， 都会将软件的内容分为一般使用与开发使用 （development） 两大类。所以你才会常常看到有类似 pam-x.x.rpm 与 pam-devel-x.x.rpm 之类的文件名啊！而默认情况下，大部分的 software-devel-x.x.rpm 都不会安装，因为终端用户大部分不会去开发软件嘛！

因为有上述的现象，因此 RPM 软件文件就会有所谓的属性相依的问题产生 （其实所有的软件管理几乎都有这方面的情况存在）。 那有没有办法解决啊？前面不是谈到 RPM 软件文件内部会记录相依属性的数据吗？那想一想，要是我将这些相依属性的软件先列表， 在有要安装软件需求的时候，先到这个列表去找，同时与系统内已安装的软件相比较，没安装到的相依软件就一口气同时安装起来， 那不就解决了相依属性的问题了吗？有没有这种机制啊？有啊！那就是 YUM 机制的由来！

CentOS （1）先将释出的软件放置到 YUM 服务器内，然后（2）分析这些软件的相依属性问题，将软件内的记录信息写下来 （header）。 然后再将这些信息分析后记录成软件相关性的清单列表。这些列表数据与软件所在的本机或网络位置可以称呼为容器或软件仓库或软件库 （repository）。 当用户端有软件安装的需求时，用户端主机会主动的向网络上面的 yum 服务器的软件库网址下载清单列表， 然后通过清单列表的数据与本机 RPM 数据库已存在的软件数据相比较，就能够一口气安装所有需要的具有相依属性的软件了。 整个流程可以简单的如下图说明：

![YUM 使用的流程示意图](image/yum-01.gif)图22.1.1、YUM 使用的流程示意图![鸟哥的图示](image/vbird_face-17228410666473.gif)

**Tips** 所以软件仓库内的清单会记载每个文件的相依属性关系，以及所有文件的网络位置 （URL）！由于记录了详细的软件网络位置， 所以有需要的时候，当然就会自动的从网络下载该软件啰！

当用户端有升级、安装的需求时， yum 会向软件库要求清单的更新，等到清单更新到本机的 /var/cache/yum 里面后， 等一下更新时就会用这个本机清单与本机的 RPM 数据库进行比较，这样就知道该下载什么软件。接下来 yum 会跑到软件库服务器 （yum server） 下载所需要的软件 （因为有记录软件所在的网址），然后再通过 RPM 的机制开始安装软件啦！这就是整个流程！ 谈到最后，还是需要动到 RPM 的啦！所以下个小节就让我们来谈谈 RPM 这咚咚吧！

![鸟哥的图示](image/vbird_face-17228410666473.gif)

**Tips** 为什么要做出“软件库”呢？由于 yum 服务器提供的 RPM 文件内容可能有所差异，举例来说，原厂释出的数据有 （1）原版数据； （2）更新数据 （update）； （3）特殊数据 （例如第三方协力软件，或某些特殊功能的软件）。 这些软件文件基本上不会放置到一起，那如何分辨这些软件功能呢？就用“软件库”的概念来处理的啦！ 不同的“软件库”网址，可以放置不同的功能的软件之意！



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

#### 原文：

一般来说，RPM 类型的文件在安装的时候，会先去读取文件内记载的设置参数内容，然后将该数据用来比对 Linux 系统的环境，以找出是否有属性相依的软件尚未安装的问题。例如 Openssh 这个连线软件需要通过 Openssl 这个加密软件的帮忙，所以得先安装 openssl 才能装 openssh 的意思。那你的环境如果没有 openssl ， 你就无法安装 openssh 的意思啦。

若环境检查合格了，那么 RPM 文件就开始被安装到你的 Linux 系统上。安装完毕后，该软件相关的信息就会被写入 /var/lib/rpm/ 目录下的数据库文件中了。 上面这个目录内的数据很重要喔！因为未来如果我们有任何软件升级的需求，版本之间的比较就是来自于这个数据库， 而如果你想要查询系统已经安装的软件，也是从这里查询的！同时，目前的 RPM 也提供数码签章信息， 这些数码签章也是在这个目录内记录的呢！所以说，这个目录得要注意不要被删除了啊！

那么软件内的文件到底是放置到哪里去啊？当然与文件系统有关对吧！我们在[第五章的目录配置](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#dir)谈过每个目录的意义， 这里再次的强调啰：

| /etc           | 一些配置文件放置的目录，例如 /etc/crontab |
| -------------- | ----------------------------------------- |
| /usr/bin       | 一些可可执行文件案                        |
| /usr/lib       | 一些程序使用的动态函数库                  |
| /usr/share/doc | 一些基本的软件使用手册与说明文档          |
| /usr/share/man | 一些 man page 文件                        |

好了，下面我们就来针对每个 RPM 的相关指令来进行说明啰！



### 22.2.2 RPM 安装（install）

安装 RPM 文件通常使用 `rpm -ivh` 命令，具体用法如下：

```
[root@study ~]# rpm -ivh /mnt/Packages/rp-pppoe-3.11-5.el7.x86_64.rpm
```

常用选项与参数：

- `-i`：install，安装
- `-v`：verbose，显示详细安装信息
- `-h`：hash，显示安装进度

例如：

```
[root@study ~]# rpm -ivh /mnt/Packages/rp-pppoe-3.11-5.el7.x86_64.rpm
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

#### 原文：

因为安装软件是 root 的工作，因此你得要是 root 的身份才能够操作 rpm 这指令的。 用 rpm 来安装很简单啦！假设我要安装一个文件名为 rp-pppoe-3.11-5.el7.x86_64.rpm 的文件，那么我可以这样：（假设原版光盘已经放在 /mnt 下面了）

```
[root@study ~]# rpm -i /mnt/Packages/rp-pppoe-3.11-5.el7.x86_64.rpm
```

不过，这样的参数其实无法显示安装的进度，所以，通常我们会这样下达安装指令：

```
[root@study ~]# rpm -ivh package_name
选项与参数：
-i ：install 的意思
-v ：察看更细部的安装信息画面
-h ：以安装信息列显示安装进度

范例一：安装原版光盘上的 rp-pppoe 软件
[root@study ~]# rpm -ivh /mnt/Packages/rp-pppoe-3.11-5.el7.x86_64.rpm
Preparing...                          ################################# [100%]
Updating / installing...
   1:rp-pppoe-3.11-5.el7              ################################# [100%]

范例二、一口气安装两个以上的软件时：
[root@study ~]# rpm -ivh a.i386.rpm b.i386.rpm *.rpm
# 后面直接接上许多的软件文件！

范例三、直接由网络上面的某个文件安装，以网址来安装：
[root@study ~]# rpm -ivh http://website.name/path/pkgname.rpm
```

另外，如果我们在安装的过程当中发现问题，或者已经知道会发生的问题， 而还是“执意”要安装这个软件时，可以使用如下的参数“强制”安装上去：

rpm 安装时常用的选项与参数说明

| 可下达的选项    | 代表意义                                                     |
| --------------- | ------------------------------------------------------------ |
| --nodeps        | 使用时机：当发生软件属性相依问题而无法安装，但你执意安装时 危险性： 软件会有相依性的原因是因为彼此会使用到对方的机制或功能，如果强制安装而不考虑软件的属性相依， 则可能会造成该软件的无法正常使用！ |
| --replacefiles  | 使用时机： 如果在安装的过程当中出现了“某个文件已经被安装在你的系统上面”的信息，又或许出现版本不合的讯息 （confilcting files） 时，可以使用这个参数来直接覆盖文件。危险性： 覆盖的动作是无法复原的！所以，你必须要很清楚的知道被覆盖的文件是真的可以被覆盖喔！否则会欲哭无泪！ |
| --replacepkgs   | 使用时机： 重新安装某个已经安装过的软件！如果你要安装一堆 RPM 软件文件时，可以使用 rpm -ivh *.rpm ，但若某些软件已经安装过了， 此时系统会出现“某软件已安装”的信息，导致无法继续安装。此时可使用这个选项来重复安装喔！ |
| --force         | 使用时机：这个参数其实就是 --replacefiles 与 --replacepkgs 的综合体！ |
| --test          | 使用时机： 想要测试一下该软件是否可以被安装到使用者的 Linux 环境当中，可找出是否有属性相依的问题。范例为： `rpm -ivh pkgname.i386.rpm --test` |
| --justdb        | 使用时机： 由于 RPM 数据库破损或者是某些缘故产生错误时，可使用这个选项来更新软件在数据库内的相关信息。 |
| --nosignature   | 使用时机： 想要略过数码签章的检查时，可以使用这个选项。      |
| --prefix 新路径 | 使用时机： 要将软件安装到其他非正规目录时。举例来说，你想要将某软件安装到 /usr/local 而非正规的 /bin, /etc 等目录， 就可以使用“ --prefix /usr/local ”来处理了。 |
| --noscripts     | 使用时机：不想让该软件在安装过程中自行执行某些系统指令。说明： RPM 的优点除了可以将文件放置到定位之外，还可以自动执行一些前置作业的指令，例如数据库的初始化。 如果你不想要让 RPM 帮你自动执行这一类型的指令，就加上他吧！ |

一般来说，rpm 的安装选项与参数大约就是这些了。通常鸟哥建议直接使用 -ivh 就好了， 如果安装的过程中发现问题，一个一个去将问题找出来，尽量不要使用“ 暴力安装法 ”，就是通过 --force 去强制安装！ 因为可能会发生很多不可预期的问题呢！除非你很清楚的知道使用上面的参数后，安装的结果是你预期的！

例题：在没有网络的前提下，你想要安装一个名为 pam-devel 的软件，你手边只有原版光盘，该如何是好？答：你可以通过挂载原版光盘来进行数据的查询与安装。请将原版光盘放入光驱，下面我们尝试将光盘挂载到 /mnt 当中， 并据以处理软件的下载啰：

- 挂载光盘，使用： mount /dev/sr0 /mnt
- 找出文件的实际路径：find /mnt -name 'pam-devel*'
- 测试此软件是否具有相依性： rpm -ivh pam-devel... --test
- 直接安装： rpm -ivh pam-devel...
- 卸载光盘： umount /mnt

在鸟哥的系统中，刚好这个软件并没有属性相依的问题，因此最后一个步骤可以顺利的进行下去呢！



### 22.2.3 RPM 升级与更新（upgrade/freshen）

升级软件使用 `rpm -Uvh` 或 `rpm -Fvh`，两者的区别在于：

- `-Uvh`：如果软件未安装，则直接安装；如果已安装旧版，则更新到新版
- `-Fvh`：仅更新已安装的软件，未安装的软件不会被安装

例如：

```
[root@study ~]# rpm -Uvh package_name.rpm
[root@study ~]# rpm -Fvh package_name.rpm
```

#### 原文：

使用 RPM 来升级真是太简单了！就以 -Uvh 或 -Fvh 来升级即可，而 -Uvh 与 -Fvh 可以用的选项与参数，跟 install 是一样的。不过， -U 与 -F 的意义还是不太一样的，基本的差别是这样的：

|      |                                                              |
| ---- | ------------------------------------------------------------ |
| -Uvh | 后面接的软件即使没有安装过，则系统将予以直接安装； 若后面接的软件有安装过旧版，则系统自动更新至新版； |
| -Fvh | 如果后面接的软件并未安装到你的 Linux 系统上，则该软件不会被安装；亦即只有已安装至你 Linux 系统内的软件会被“升级”！ |

由上面的说明来看，如果你想要大量的升级系统旧版本的软件时，使用 -Fvh 则是比较好的作法，因为没有安装的软件才不会被不小心安装进系统中。但是需要注意的是，如果你使用的是 -Fvh ，偏偏你的机器上尚无这一个软件，那么很抱歉，该软件并不会被安装在你的 Linux 主机上面，所以请重新以 ivh 来安装吧！

早期没有 yum 的环境下面，同时网络带宽也很糟糕的状况下，通常有的朋友在进行整个操作系统的旧版软件修补时，喜欢这么进行：

1. 先到各发展商的 errata 网站或者是国内的 FTP 图像站捉下来最新的 RPM 文件；
2. 使用 -Fvh 来将你的系统内曾安装过的软件进行修补与升级！（真是方便呀！）

所以，在不晓得 yum 功能的情况下，你依旧可以到 CentOS 的映设站台下载 updates 数据，然后利用上述的方法来一口气升级！ 当然啰，升级也是可以利用 --nodeps/--force 等等的参数啦！ 不过，现在既然有 yum 的机制在，这个笨方法当然也就不再需要了！





### 22.2.4 RPM 查询（query）

查询已安装的软件信息或未安装的 RPM 文件信息使用 `rpm -q` 系列命令：

```
[root@study ~]# rpm -qa                              # 列出所有已安装的软件
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
[root@study ~]# rpm -q logrotate
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

#### 原文：

RPM 在查询的时候，其实查询的地方是在 /var/lib/rpm/ 这个目录下的数据库文件啦！另外， RPM 也可以查询未安装的 RPM 文件内的信息喔！那如何去查询呢？ 我们先来谈谈可用的选项有哪些？

```
[root@study ~]# rpm -qa                              &lt;==已安装软件
[root@study ~]# rpm -q[licdR] 已安装的软件名称       &lt;==已安装软件
[root@study ~]# rpm -qf 存在于系统上面的某个文件名     &lt;==已安装软件
[root@study ~]# rpm -qp[licdR] 未安装的某个文件名称  &lt;==查阅RPM文件
选项与参数：
查询已安装软件的信息：
-q  ：仅查询，后面接的软件名称是否有安装；
-qa ：列出所有的，已经安装在本机 Linux 系统上面的所有软件名称；
-qi ：列出该软件的详细信息 （information），包含开发商、版本与说明等；
-ql ：列出该软件所有的文件与目录所在完整文件名 （list）；
-qc ：列出该软件的所有配置文件 （找出在 /etc/ 下面的文件名而已）
-qd ：列出该软件的所有说明文档 （找出与 man 有关的文件而已）
-qR ：列出与该软件有关的相依软件所含的文件 （Required 的意思）
-qf ：由后面接的文件名称，找出该文件属于哪一个已安装的软件；
-q --scripts：列出是否含有安装后需要执行的脚本档，可用以 debug 喔！
查询某个 RPM 文件内含有的信息：
-qp[icdlR]：注意 -qp 后面接的所有参数以上面的说明一致。但用途仅在于找出
        某个 RPM 文件内的信息，而非已安装的软件信息！注意！
```

在查询的部分，所有的参数之前都需要加上 -q 才是所谓的查询！查询主要分为两部分， 一个是查已安装到系统上面的的软件信息，这部份的信息都是由 /var/lib/rpm/ 所提供。另一个则是查某个 rpm 文件内容， 等于是由 RPM 文件内找出一些要写入数据库内的信息就是了，这部份就得要使用 -qp （p 是 package 的意思）。 那就来看看几个简单的范例吧！

```
范例一：找出你的 Linux 是否有安装 logrotate 这个软件？
[root@study ~]# rpm -q logrotate
logrotate-3.8.6-4.el7.x86_64
[root@study ~]# rpm -q logrotating
package logrotating is not installed
# 注意到，系统会去找是否有安装后面接的软件名称。注意，不必要加上版本喔！
# 至于显示的结果，一看就知道有没有安装啦！

范例二：列出上题当中，属于该软件所提供的所有目录与文件：
[root@study ~]# rpm -ql logrotate
/etc/cron.daily/logrotate
/etc/logrotate.conf
....（以下省略）....
# 可以看出该软件到底提供了多少的文件与目录，也可以追踪软件的数据。

范例三：列出 logrotate 这个软件的相关说明数据：
[root@study ~]# rpm -qi logrotate
Name        : logrotate                          # 软件名称
Version     : 3.8.6                              # 软件的版本
Release     : 4.el7                              # 释出的版本
Architecture: x86_64                             # 编译时所针对的硬件等级
Install Date: Mon 04 May 2015 05:52:36 PM CST    # 这个软件安装到本系统的时间
Group       : System Environment/Base            # 软件是放再哪一个软件群组中
Size        : 102451                             # 软件的大小
License     : GPL+                               # 释出的授权方式
Signature   : RSA/SHA256, Fri 04 Jul 2014 11:34:56 AM CST, Key ID 24c6a8a7f4a80eb5
Source RPM  : logrotate-3.8.6-4.el7.src.rpm      # 这就是 SRPM 的文件名
Build Date  : Tue 10 Jun 2014 05:58:02 AM CST    # 软件编译打包的时间
Build Host  : worker1.bsys.centos.org            # 在哪一部主机上面编译的
Relocations : （not relocatable）   
Packager    : CentOS BuildSystem &lt;http://bugs.centos.org&gt;
Vendor      : CentOS
URL         : https://fedorahosted.org/logrotate/
Summary     : Rotates, compresses, removes and mails system log files
Description :                                    # 这个是详细的描述！
The logrotate utility is designed to simplify the administration of
log files on a system which generates a lot of log files.  Logrotate
allows for the automatic rotation compression, removal and mailing of
log files.  Logrotate can be set to handle a log file daily, weekly,
monthly or when the log file gets to a certain size.  Normally,
logrotate runs as a daily cron job.

Install the logrotate package if you need a utility to deal with the
log files on your system.
# 列出该软件的 information （信息），里面的信息可多着呢，包括了软件名称、
# 版本、开发商、SRPM文件名称、打包次数、简单说明信息、软件打包者、
# 安装日期等等！如果想要详细的知道该软件的数据，用这个参数来了解一下

范例四：分别仅找出 logrotate 的配置文件与说明文档
[root@study ~]# rpm -qc logrotate
[root@study ~]# rpm -qd logrotate

范例五：若要成功安装 logrotate ，他还需要什么文件的帮忙？
[root@study ~]# rpm -qR logrotate
/bin/sh
config（logrotate） = 3.8.6-4.el7
coreutils &gt;= 5.92
....（以下省略）....
# 由这里看起来，呵呵～还需要很多文件的支持才行喔！

范例六：由上面的范例五，找出 /bin/sh 是那个软件提供的？
[root@study ~]# rpm -qf /bin/sh
bash-4.2.46-12.el7.x86_64
# 这个参数后面接的可是“文件”呐！不像前面都是接软件喔！
# 这个功能在查询系统的某个文件属于哪一个软件所有的。

范例七：假设我有下载一个 RPM 文件，想要知道该文件的需求文件，该如何？
[root@study ~]# rpm -qpR filename.i386.rpm
# 加上 -qpR ，找出该文件需求的数据！
```

常见的查询就是这些了！要特别说明的是，在查询本机上面的 RPM 软件相关信息时， 不需要加上版本的名称，只要加上软件名称即可！因为他会由 /var/lib/rpm 这个数据库里面去查询， 所以我们可以不需要加上版本名称。但是查询某个 RPM 文件就不同了，我们必须要列出整个文件的完整文件名才行～ 这一点朋友们常常会搞错。下面我们就来做几个简单的练习吧！

例题：

1. 我想要知道我的系统当中，以 c 开头的软件有几个，如何实做？
2. 我的 WWW 服务器为 Apache ，我知道他使用的 RPM 软件文件名为 httpd 。现在，我想要知道这个软件的所有配置文件放置在何处，可以怎么作？
3. 承上题，如果查出来的设置文件已经被我改过，但是我忘记了曾经修改过哪些地方，所以想要直接重新安装一次该软件，该如何作？
4. 如果我误砍了某个重要文件，例如 /etc/crontab，偏偏不晓得他属于哪一个软件，该怎么办？

答：

1. rpm -qa | grep ^c | wc -l
2. rpm -qc httpd
3. 假设该软件在网络上的网址为： http://web.site.name/path/httpd-x.x.xx.i386.rpm 则我可以这样做： rpm -ivh http://web.site.name/path/httpd-x.x.xx.i386.rpm --replacepkgs
4. 虽然已经没有这个文件了，不过没有关系，因为 RPM 有记录在 /var/lib/rpm 当中的数据库啊！所以直接下达： rpm -qf /etc/crontab 就可以知道是那个软件啰！重新安装一次该软件即可！





### 22.2.5 RPM 验证与数码签章（Verify/signature）

验证 RPM 文件是否被更改或是否存在问题使用 `rpm -V` 系列命令：

```
[root@study ~]# rpm -Va
[root@study ~]# rpm -V 软件名称
[root@study ~]# rpm -Vp RPM 文件名
[root@study ~]# rpm -Vf 文件名
```

例如：

```
[root@study ~]# rpm -V logrotate
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
[root@study ~]# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

验证 （Verify） 的功能主要在于提供系统管理员一个有用的管理机制！作用的方式是“使用 /var/lib/rpm 下面的数据库内容来比对目前 Linux 系统的环境下的所有软件文件 ”也就是说，当你有数据不小心遗失， 或者是因为你误杀了某个软件的文件，或者是不小心不知道修改到某一个软件的文件内容， 就用这个简单的方法来验证一下原本的文件系统吧！好让你了解这一阵子到底是修改到哪些文件数据了！验证的方式很简单：

```
[root@study ~]# rpm -Va
[root@study ~]# rpm -V  已安装的软件名称
[root@study ~]# rpm -Vp 某个 RPM 文件的文件名
[root@study ~]# rpm -Vf 在系统上面的某个文件
选项与参数：
-V  ：后面加的是软件名称，若该软件所含的文件被更动过，才会列出来；
-Va ：列出目前系统上面所有可能被更动过的文件；
-Vp ：后面加的是文件名称，列出该软件内可能被更动过的文件；
-Vf ：列出某个文件是否被更动过～

范例一：列出你的 Linux 内的 logrotate 这个软件是否被更动过？
[root@study ~]# rpm -V logrotate
# 如果没有出现任何讯息，恭喜你，该软件所提供的文件没有被更动过。
# 如果有出现任何讯息，才是有出现状况啊！

范例二：查询一下，你的 /etc/crontab 是否有被更动过？
[root@study ~]# rpm -Vf /etc/crontab
.......T.  c /etc/crontab
# 瞧！因为有被更动过，所以会列出被更动过的信息类型！
```

好了，那么我怎么知道到底我的文件被更动过的内容是什么？例如上面的范例二。呵呵！简单的说明一下吧！ 例如，我们检查一下 logrotate 这个软件：

```
[root@study ~]# rpm -ql logrotate
/etc/cron.daily/logrotate
/etc/logrotate.conf
/etc/logrotate.d
/usr/sbin/logrotate
/usr/share/doc/logrotate-3.8.6
/usr/share/doc/logrotate-3.8.6/CHANGES
/usr/share/doc/logrotate-3.8.6/COPYING
/usr/share/man/man5/logrotate.conf.5.gz
/usr/share/man/man8/logrotate.8.gz
/var/lib/logrotate.status
# 呵呵！共有 10 个文件啊！请修改 /etc/logrotate.conf 内的 rotate 变成 5

[root@study ~]# rpm -V logrotate
..5....T.  c /etc/logrotate.conf
```

你会发现在文件名之前有个 c ，然后就是一堆奇怪的文字了。那个 c 代表的是 configuration ， 就是配置文件的意思。至于最前面的几个信息是：

- S ：（file Size differs） 文件的容量大小是否被改变
- M ：（Mode differs） 文件的类型或文件的属性 （rwx） 是否被改变？如是否可执行等参数已被改变
- 5 ：（MD5 sum differs） MD5 这一种指纹码的内容已经不同
- D ：（Device major/minor number mis-match） 设备的主/次代码已经改变
- L ：（readLink（2） path mis-match） Link 路径已被改变
- U ：（User ownership differs） 文件的所属人已被改变
- G ：（Group ownership differs） 文件的所属群组已被改变
- T ：（mTime differs） 文件的创建时间已被改变
- P ：（caPabilities differ） 功能已经被改变

所以，如果当一个配置文件所有的信息都被更动过，那么他的显示就会是：

```
SM5DLUGTP c filename
```

至于那个 c 代表的是“ Config file ”的意思，也就是文件的类型，文件类型有下面这几类：

- c ：配置文件 （config file）
- d ：文件数据文件 （documentation）
- g ：鬼文件～通常是该文件不被某个软件所包含，较少发生！（ghost file）
- l ：授权文件 （license file）
- r ：读我文件 （read me）

经过验证的功能，你就可以知道那个文件被更动过。那么如果该文件的变更是“预期中的”， 那么就没有什么大问题，但是如果该文件是“非预期的”，那么是否被入侵了呢？呵呵！得注意注意啰！ 一般来说，配置文件 （configure） 被更动过是很正常的，万一你的 binary program 被更动过呢？ 那就得要特别特别小心啊！

![鸟哥的图示](image/vbird_face-17228413324805.gif)

**Tips** 虽说家丑不可外扬，不过有件事情还是跟大家分享一下的好。鸟哥之前的主机曾经由于安装一套软件，导致被攻击成为跳板。 会发现的原因是系统中只要出现 *.patch 的扩展名时，使用 ls -l 就是显示不出来该文件名 （该文件名确实存在）。 找了好久，用了好多工具都找不出问题，最终利用 rpm -Va 找出来，原来好多 binary program 被更动过，连 init 都被恶搞！此时，赶紧重新安装 Linux 并移除那套软件，之后就比较正常了。所以说，这个 rpm -Va 是个好功能喔！

- 数码签章 （digital signature）

谈完了软件的验证后，不知道你有没有发现一个问题，那就是，验证只能验证软件内的信息与 /var/lib/rpm/ 里面的数据库信息而已，如果该软件文件所提供的数据本身就有问题，那你使用验证的手段也无法确定该软件的正确性啊！ 那如何解决呢？在 Tarball 与文件的验证方面，我们可以使用前一章谈到的 md5 指纹码来检查， 不过，连指纹码也可能会被窜改的嘛！那怎办？没关系，我们可以通过数码签章来检验软件的来源的！

就像你自己的签名一样，我们的软件开发商原厂所推出的软件也会有一个厂商自己的签章系统！ 只是这个签章被数码化了而已。厂商可以数码签章系统产生一个专属于该软件的签章，并将该签章的公钥 （public key） 释出。 当你要安装一个 RPM 文件时：

1. 首先你必须要先安装原厂释出的公钥文件；
2. 实际安装原厂的 RPM 软件时， rpm 指令会去读取 RPM 文件的签章信息，与本机系统内的签章信息比对，
3. 若签章相同则予以安装，若找不到相关的签章信息时，则给予警告并且停止安装喔。

我们 CentOS 使用的数码签章系统为 GNU 计划的 GnuPG （GNU Privacy Guard, GPG）[[1\]](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/195.html#ps1)。 GPG 可以通过杂凑运算，算出独一无二的专属金钥系统或者是数码签章系统，有兴趣的朋友可以参考文末的延伸阅读， 去了解一下 GPG 加密的机制喔！这里我们仅简单的说明数码签章在 RPM 文件上的应用而已。 而根据上面的说明，我们也会知道首先必须要安装原厂释出的 GPG 数码签章的公钥文件啊！CentOS 的数码签章位于：

```
[root@study ~]# ll /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
-rw-r--r--. 1 root root 1690 Apr  1 06:27 /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
[root@study ~]# cat /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1.4.5 （GNU/Linux）

mQINBFOn/0sBEADLDyZ+DQHkcTHDQSE0a0B2iYAEXwpPvs67cJ4tmhe/iMOyVMh9
....（中间省略）....
-----END PGP PUBLIC KEY BLOCK-----
```

从上面的输出，你会知道该数码签章码其实仅是一个乱数而已，这个乱数对于数码签章有意义而已， 我们看不懂啦！那么这个文件如何安装呢？通过下面的方式来安装即可喔！

```
[root@study ~]# rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

由于不同版本 GPG 金钥文件放置的位置可能不同，不过文件名大多是以 GPG-KEY 来说明的， 因此你可以简单的使用 locate 或 find 来找寻，如以下的方式来搜寻即可：

```
[root@study ~]# locate GPG-KEY
[root@study ~]# find /etc -name '*GPG-KEY*'
```

那安装完成之后，这个金钥的内容会以什么方式呈现呢？基本上都是使用 pubkey 作为软件的名称的！ 那我们先列出金钥软件名称后，再以 -qi 的方式来查询看看该软件的信息为何：

```
[root@study ~]# rpm -qa &#124; grep pubkey
gpg-pubkey-f4a80eb5-53a7ff4b
[root@study ~]# rpm -qi gpg-pubkey-f4a80eb5-53a7ff4b
Name        : gpg-pubkey
Version     : f4a80eb5
Release     : 53a7ff4b
Architecture: （none）
Install Date: Fri 04 Sep 2015 11:30:46 AM CST
Group       : Public Keys
Size        : 0
License     : pubkey
Signature   : （none）
Source RPM  : （none）
Build Date  : Mon 23 Jun 2014 06:19:55 PM CST
Build Host  : localhost
Relocations : （not relocatable）
Packager    : CentOS-7 Key （CentOS 7 Official Signing Key） &lt;[email protected]&gt;
Summary     : gpg（CentOS-7 Key （CentOS 7 Official Signing Key） &lt;[email protected]&gt;）
Description :
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: rpm-4.11.1 （NSS-3）
....（下面省略）....
```

重点就是最后面出现的那一串乱码啦！那可是作为数码签章非常重要的一环哩！ 如果你忘记加上数码签章，很可能很多原版软件就不能让你安装啰～除非你利用 rpm 时选择略过数码签章的选项。





### 22.2.6 RPM 反安装与重建数据库（erase/rebuilddb）

反安装软件使用 `rpm -e`：

```
[root@study ~]# rpm -e 软件名称
```

重建 RPM 数据库使用 `rpm --rebuilddb`：

```
[root@study ~]# rpm --rebuilddb
```

通过以上介绍，您可以使用 `rpm` 指令进行安装、查询、验证、升级和反安装操作，管理系统中的 RPM 软件包。

#### 原文：

反安装就是将软件解除安装啦！要注意的是，“解安装的过程一定要由最上层往下解除”，以 rp-pppoe 为例，这一个软件主要是依据 ppp 这个软件来安装的，所以当你要解除 ppp 的时候，就必须要先解除 rp-pppoe 才行！否则就会发生结构上的问题啦！这个可以由建筑物来说明， 如果你要拆除五、六楼，那么当然要由六楼拆起，否则先拆的是第五楼时，那么上面的楼层难道会悬空？

移除的选项很简单，就通过 -e 即可移除。不过，很常发生软件属性相依导致无法移除某些软件的问题！ 我们以下面的例子来说明：

```
# 1\. 找出与 pam 有关的软件名称，并尝试移除 pam 这个软件：
[root@study ~]# rpm -qa &#124; grep pam
fprintd-pam-0.5.0-4.0.el7_0.x86_64
pam-1.1.8-12.el7.x86_64
gnome-keyring-pam-3.8.2-10.el7.x86_64
pam-devel-1.1.8-12.el7.x86_64
pam_krb5-2.4.8-4.el7.x86_64
[root@study ~]# rpm -e pam
error: Failed dependencies:  &lt;==这里提到的是相依性的问题
        libpam.so.0（）（64bit） is needed by （installed） systemd-libs-208-20.el7.x86_64
        libpam.so.0（）（64bit） is needed by （installed） libpwquality-1.2.3-4.el7.x86_64
....（以下省略）....

# 2\. 若仅移除 pam-devel 这个之前范例安装上的软件呢？
[root@study ~]# rpm -e pam-devel  &lt;==不会出现任何讯息！
[root@study ~]# rpm -q pam-devel
package pam-devel is not installed
```

从范例一我们知道 pam 所提供的函数库是让非常多其他软件使用的，因此你不能移除 pam ，除非将其他相依软件一口气也全部移除！你当然也能加 --nodeps 来强制移除， 不过，如此一来所有会用到 pam 函数库的软件，都将成为无法运行的程序，我想，你的主机也只好准备停机休假了吧！ 至于范例二中，由于 pam-devel 是依附于 pam 的开发工具，你可以单独安装与单独移除啦！

由于 RPM 文件常常会安装/移除/升级等，某些动作或许可能会导致 RPM 数据库 /var/lib/rpm/ 内的文件破损。果真如此的话，那你该如何是好？别担心，我们可以使用 --rebuilddb 这个选项来重建一下数据库喔！ 作法如下：

```
[root@study ~]# rpm --rebuilddb   &lt;==重建数据库
```



## 22.3 YUM 线上升级机制

YUM 通过分析 RPM 的标头数据，根据各软件的相关性制作出属性相依时的解决方案，自动处理软件的依赖性问题，解决软件安装、移除和升级的问题。详细的 YUM 服务器与用户端之间的沟通，可以参考前面的图 22.1.1。

### 22.3.1 利用 YUM 进行查询、安装、升级与移除功能

YUM 的使用非常简单，主要通过 `yum` 指令来操作，以下是常用的查询、安装、升级和移除功能。

#### 查询功能

通过 YUM 可以查询原版 distribution 提供的软件，或查询已知软件的名称和功能：

```
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
[root@study ~]# yum search raid
```

1. 查看 `mdadm` 软件的详细信息：

```
[root@study ~]# yum info mdadm
```

1. 列出所有可供安装的软件：

```
[root@study ~]# yum list
```

1. 列出可供升级的软件：

```
[root@study ~]# yum list updates
```

1. 查找提供 `passwd` 文件的软件：

```
[root@study ~]# yum provides passwd
```

#### 安装和升级功能

通过 `yum install` 和 `yum update` 进行安装和升级：

```
[root@study ~]# yum install pam-devel
[root@study ~]# yum update
```

#### 移除功能

通过 `yum remove` 移除软件：

```
[root@study ~]# yum remove pam-devel
```

#### 原文：

yum 的使用真是非常简单，就是通过 yum 这个指令啊！那么这个指令怎么用呢？用法很简单，就让我们来简单的谈谈：

- 查询功能：yum [list|info|search|provides|whatprovides] 参数

如果想要查询利用 yum 来查询原版 distribution 所提供的软件，或已知某软件的名称，想知道该软件的功能， 可以利用 yum 相关的参数为：

```
[root@study ~]# yum [option] [查询工作项目] [相关参数]
选项与参数：
[option]：主要的选项，包括有：
  -y ：当 yum 要等待使用者输入时，这个选项可以自动提供 yes 的回应；
  --installroot=/some/path ：将该软件安装在 /some/path 而不使用默认路径
[查询工作项目] [相关参数]：这方面的参数有：
  search  ：搜寻某个软件名称或者是描述 （description） 的重要关键字；
  list    ：列出目前 yum 所管理的所有的软件名称与版本，有点类似 rpm -qa；
  info    ：同上，不过有点类似 rpm -qai 的执行结果；
  provides：从文件去搜寻软件！类似 rpm -qf 的功能！

范例一：搜寻磁盘阵列 （raid） 相关的软件有哪些？
[root@study ~]# yum search raid
Loaded plugins: fastestmirror, langpacks      # yum 系统自己找出最近的 yum server
Loading mirror speeds from cached hostfile    # 找出速度最快的那一部 yum server
 * base: ftp.twaren.net                       # 下面三个软件库，且来源为该服务器！
 * extras: ftp.twaren.net
 * updates: ftp.twaren.net
....（前面省略）....
dmraid-events-logwatch.x86_64 : dmraid logwatch-based email reporting
dmraid-events.x86_64 : dmevent_tool （Device-mapper event tool） and DSO
iprutils.x86_64 : Utilities for the IBM Power Linux RAID adapters
mdadm.x86_64 : The mdadm program controls Linux md devices （software RAID arrays）
....（后面省略）....
# 在冒号 （:）  左边的是软件名称，右边的则是在 RPM 内的 name 设置 （软件名）
# 瞧！上面的结果，这不就是与 RAID 有关的软件吗？如果想了解 mdadm 的软件内容呢？

范例二：找出 mdadm 这个软件的功能为何
[root@study ~]# yum info mdadm
Installed Packages       &lt;==这说明该软件是已经安装的了
Name        : mdadm      &lt;==这个软件的名称
Arch        : x86_64     &lt;==这个软件的编译架构
Version     : 3.3.2      &lt;==此软件的版本
Release     : 2.el7      &lt;==释出的版本
Size        : 920 k      &lt;==此软件的文件总容量
Repo        : installed  &lt;==软件库回报说已安装的
From repo   : anaconda
Summary     : The mdadm program controls Linux md devices （software RAID arrays）
URL         : http://www.kernel.org/pub/linux/utils/raid/mdadm/
License     : GPLv2+
Description : The mdadm program is used to create, manage, and monitor Linux MD （software
            : RAID） devices.  As such, it provides similar functionality to the raidtools
            : package.  However, mdadm is a single program, and it can perform
            : almost all functions without a configuration file, though a configuration
            : file can be used to help with some common tasks.
# 不要跟我说，上面说些啥？自己找字典翻一翻吧！拜托拜托！

范例三：列出 yum 服务器上面提供的所有软件名称
[root@study ~]# yum list
Installed Packages   &lt;==已安装软件
GConf2.x86_64                           3.2.6-8.el7                    @anaconda
LibRaw.x86_64                           0.14.8-5.el7.20120830git98d925 @base
ModemManager.x86_64                     1.1.0-6.git20130913.el7        @anaconda
....（中间省略）....
Available Packages   &lt;==还可以安装的其他软件
389-ds-base.x86_64                      1.3.3.1-20.el7_1               updates
389-ds-base-devel.x86_64                1.3.3.1-20.el7_1               updates
389-ds-base-libs.x86_64                 1.3.3.1-20.el7_1               updates
....（下面省略）....
# 上面提供的意义为：“ 软件名称   版本   在那个软件库内 ”

范例四：列出目前服务器上可供本机进行升级的软件有哪些？
[root@study ~]# yum list updates  &lt;==一定要是 updates 喔！
Updated Packages
NetworkManager.x86_64          1:1.0.0-16.git20150121.b4ea599c.el7_1       updates
NetworkManager-adsl.x86_64     1:1.0.0-16.git20150121.b4ea599c.el7_1       updates
....（下面省略）....
# 上面就列出在那个软件库内可以提供升级的软件与版本！

范例五：列出提供 passwd 这个文件的软件有哪些
[root@study ~]# yum provides passwd
passwd-0.79-4.el7.x86_64 : An utility for setting or changing passwords using PAM
Repo        : base

passwd-0.79-4.el7.x86_64 : An utility for setting or changing passwords using PAM
Repo        : @anaconda
# 找到啦！就是上面的这个软件提供了 passwd 这个程序！
```

通过上面的查询，你应该大致知道 yum 如何用在查询上面了吧？那么实际来应用一下：

例题：利用 yum 的功能，找出以 pam 为开头的软件名称有哪些？而其中尚未安装的又有哪些？答：可以通过如下的方法来查询：

```
[root@study ~]# yum list pam*
Installed Packages
pam.x86_64                            1.1.8-12.el7                 @anaconda
pam_krb5.x86_64                       2.4.8-4.el7                  @base
Available Packages &lt;==下面则是“可升级”的或“未安装”的
pam.i686                              1.1.8-12.el7_1.1             updates
pam.x86_64                            1.1.8-12.el7_1.1             updates
pam-devel.i686                        1.1.8-12.el7_1.1             updates
pam-devel.x86_64                      1.1.8-12.el7_1.1             updates
pam_krb5.i686                         2.4.8-4.el7                  base
pam_pkcs11.i686                       0.6.2-18.el7                 base
pam_pkcs11.x86_64                     0.6.2-18.el7                 base
```

如上所示，所以可升级者有 pam 这两个软件，完全没有安装的则是 pam-devel 等其他几个软件啰！

- 安装/升级功能：yum [install|update] 软件

既然可以查询，那么安装与升级呢？很简单啦！就利用 install 与 update 这两项工作来处理即可喔！

```
[root@study ~]# yum [option] [安装与升级的工作项目] [相关参数]
选项与参数：
  install ：后面接要安装的软件！
  update  ：后面接要升级的软件，若要整个系统都升级，就直接 update 即可

范例一：将前一个练习找到的未安装的 pam-devel 安装起来
[root@study ~]# yum install pam-devel
Loaded plugins: fastestmirror, langpacks    # 首先的 5 行在找出最快的 yum server
Loading mirror speeds from cached hostfile
 * base: ftp.twaren.net
 * extras: ftp.twaren.net
 * updates: ftp.twaren.net
Resolving Dependencies                      # 接下来先处理“属性相依”的软件问题
--&gt; Running transaction check
---&gt; Package pam-devel.x86_64 0:1.1.8-12.el7_1.1 will be installed
--&gt; Processing Dependency: pam（x86-64） = 1.1.8-12.el7_1.1 for package: pam-devel-
       1.1.8-12.el7_1.1.x86_64
--&gt; Running transaction check
---&gt; Package pam.x86_64 0:1.1.8-12.el7 will be updated
---&gt; Package pam.x86_64 0:1.1.8-12.el7_1.1 will be an update
--&gt; Finished Dependency Resolution
Dependencies Resolved

# 由上面的检查发现到 pam 这个软件也需要同步升级，这样才能够安装新版 pam-devel 喔！
# 至于下面则是一个总结的表格显示！
==========================================================================================
 Package             Arch             Version                     Repository         Size
==========================================================================================
Installing:
 pam-devel           x86_64           1.1.8-12.el7_1.1            updates           183 k
Updating for dependencies:
 pam                 x86_64           1.1.8-12.el7_1.1            updates           714 k

Transaction Summary
==========================================================================================
Install  1 Package                          # 要安装的是一个软件
Upgrade             （ 1 Dependent package）  # 因为相依属性问题，需要额外加装一个软件！

Total size: 897 k
Total download size: 183 k                  # 总共需要下载的容量！
Is this ok [y/d/N]: y   # 你得要自己决定是否要下载与安装！当然是 y 啊！
Downloading packages:                       # 开始下载啰！
warning: /var/cache/yum/x86_64/7/updates/packages/pam-devel-1.1.8-12.el7_1.1.x86_64.rpm:
         Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY
Public key for pam-devel-1.1.8-12.el7_1.1.x86_64.rpm is not installed
pam-devel-1.1.8-12.el7_1.1.x86_64.rpm                              &#124; 183 kB  00:00:00
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Importing GPG key 0xF4A80EB5:
 Userid     : "CentOS-7 Key （CentOS 7 Official Signing Key） &lt;[email protected]&gt;"
 Fingerprint: 6341 ab27 53d7 8a78 a7c2 7bb1 24c6 a8a7 f4a8 0eb5
 Package    : centos-release-7-1.1503.el7.centos.2.8.x86_64 （@anaconda）
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
Is this ok [y/N]: y  # 只有在第一次安装才会出现这个项目“确定要安装数码签章”才能继续！
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Warning: RPMDB altered outside of yum.
  Updating   : pam-1.1.8-12.el7_1.1.x86_64                                            1/3
  Installing : pam-devel-1.1.8-12.el7_1.1.x86_64                                      2/3
  Cleanup    : pam-1.1.8-12.el7.x86_64                                                3/3
  Verifying  : pam-1.1.8-12.el7_1.1.x86_64                                            1/3
  Verifying  : pam-devel-1.1.8-12.el7_1.1.x86_64                                      2/3
  Verifying  : pam-1.1.8-12.el7.x86_64                                                3/3

Installed:
  pam-devel.x86_64 0:1.1.8-12.el7_1.1

Dependency Updated:
  pam.x86_64 0:1.1.8-12.el7_1.1

Complete!
```

有没有很高兴啊！你不必知道软件在哪里，你不必手动下载软件，你也不必拿出原版光盘出来 mount 之后查询再安装！全部不需要，只要有了 yum 这个家伙，你的安装、升级再也不是什么难事！ 而且还能主动的进行软件的属性相依处理流程，如上所示，一口气帮我们处理好了所有事情！ 是不是很过瘾啊！而且整个动作完全免费！够酷吧！

- 移除功能：yum [remove] 软件

那能不能用 yum 移除软件呢？将刚刚的软件移除看看，会出现啥状况啊？

```
[root@study ~]# yum remove pam-devel
Loaded plugins: fastestmirror, langpacks
Resolving Dependencies   &lt;==同样的，先解决属性相依的问题
--&gt; Running transaction check
---&gt; Package pam-devel.x86_64 0:1.1.8-12.el7_1.1 will be erased
--&gt; Finished Dependency Resolution

Dependencies Resolved

==========================================================================================
 Package             Arch             Version                    Repository          Size
==========================================================================================
Removing:
 pam-devel           x86_64           1.1.8-12.el7_1.1           @updates           528 k

Transaction Summary
==========================================================================================
Remove  1 Package       # 还好！没有相依属性的问题，仅移除一个软件！

Installed size: 528 k
Is this ok [y/N]: y
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Erasing    : pam-devel-1.1.8-12.el7_1.1.x86_64                                      1/1
  Verifying  : pam-devel-1.1.8-12.el7_1.1.x86_64                                      1/1

Removed:
  pam-devel.x86_64 0:1.1.8-12.el7_1.1

Complete!
```

连移除也这么简单！看来，似乎不需要 rpm 这个指令也能够快乐的安装所有的软件了！ 虽然是如此，但是 yum 毕竟是架构在 rpm 上面所发展起来的，所以，鸟哥认为你还是得需要了解 rpm 才行！不要学了 yum 之后就将 rpm 的功能忘记了呢！切记切记！



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
[root@study ~]# vim /etc/yum.repos.d/CentOS-Base.repo
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
[root@study ~]# yum clean all
```

#### 原文：

虽然 yum 是你的主机能够连线上 Internet 就可以直接使用的，不过，由于 CentOS 的映射站台可能会选错， 举例来说，我们在台湾，但是 CentOS 的映射站台却选择到了大陆北京或者是日本去，有没有可能发生啊！ 有啊！鸟哥教学方面就常常发生这样的问题，要知道，我们连线到大陆或日本的速度是非常慢的呢！那怎办？ 当然就是手动的修改一下 yum 的配置文件就好啰！

在台湾，CentOS 的映射站台主要有高速网络中心与义守大学，鸟哥近来比较偏好高速网络中心， 似乎更新的速度比较快，而且连接台湾学术网络也非常快速哩！因此，鸟哥下面建议台湾的朋友使用高速网络中心的 ftp 主机资源来作为 yum 服务器来源喔！不过因为鸟哥也在崑大服务，崑大目前也加入了 CentOS 的映射站， 如果在昆山或台南地区，也能够选择崑大的 FTP 喔！目前高速网络中心与崑大对于 CentOS 所提供的相关网址如下：

- http://ftp.twaren.net/Linux/CentOS/7/
- http://ftp.ksu.edu.tw/FTP/CentOS/7/

如果你连接到上述的网址后，就会发现里面有一堆链接，那些链接就是这个 yum 服务器所提供的软件库了！ 所以高速网络中心也提供了 centosplus, cloud, extras, fasttrack, os, updates 等软件库，最好认的软件库就是 os （系统默认的软件） 与 updates （软件升级版本） 啰！由于鸟哥在我的测试用主机是利用 x86_64 的版本， 因此那个 os 再点进去就会得到如下的可提供安装的网址：

- http://ftp.ksu.edu.tw/FTP/CentOS/7/os/x86_64/

为什么在上述的网址内呢？有什么特色！最重要的特色就是那个“ repodata ”的目录！该目录就是分析 RPM 软件后所产生的软件属性相依数据放置处！因此，当你要找软件库所在网址时， 最重要的就是该网址下面一定要有个名为 repodata 的目录存在！那就是软件库的网址了！ 其他的软件库正确网址，就请各位看倌自行寻找一下喔！现在让我们修改配置文件吧！

```
[root@study ~]# vim /etc/yum.repos.d/CentOS-Base.repo
[base]
name=CentOS-$releasever - Base
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

如上所示，鸟哥仅列出 base 这个软件库内容而已，其他的软件库内容请自行查阅啰！上面的数据需要注意的是：

- [base]：代表软件库的名字！中括号一定要存在，里面的名称则可以随意取。但是不能有两个相同的软件库名称， 否则 yum 会不晓得该到哪里去找软件库相关软件清单文件。
- name：只是说明一下这个软件库的意义而已，重要性不高！
- mirrorlist=：列出这个软件库可以使用的映射站台，如果不想使用，可以注解到这行；
- baseurl=：这个最重要，因为后面接的就是软件库的实际网址！ mirrorlist 是由 yum 程序自行去捉映射站台， baseurl 则是指定固定的一个软件库网址！我们刚刚找到的网址放到这里来啦！
- enable=1：就是让这个软件库被启动。如果不想启动可以使用 enable=0 喔！
- gpgcheck=1：还记得 RPM 的数码签章吗？这就是指定是否需要查阅 RPM 文件内的数码签章！
- gpgkey=：就是数码签章的公钥档所在位置！使用默认值即可

了解这个配置文件之后，接下来让我们修改整个文件的内容，让我们这部主机可以直接使用高速网络中心的资源吧！ 修改的方式鸟哥仅列出 base 这个软件库项目而已，其他的项目请您自行依照上述的作法来处理即可！

```
[root@study ~]# vim /etc/yum.repos.d/CentOS-Base.repo
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
# 默认情况下，软件仓库仅有这三个有启用！所以鸟哥仅修改这三个软件库的 baseurl 而已喔！
```

接下来当然就是给它测试一下这些软件库是否正常的运行中啊！如何测试呢？再次使用 yum 即可啊！

```
范例一：列出目前 yum server 所使用的软件库有哪些？
[root@study ~]# yum repolist all
repo id                              repo name                         status
C7.0.1406-base/x86_64                CentOS-7.0.1406 - Base            disabled
C7.0.1406-centosplus/x86_64          CentOS-7.0.1406 - CentOSPlus      disabled
C7.0.1406-extras/x86_64              CentOS-7.0.1406 - Extras          disabled
C7.0.1406-fasttrack/x86_64           CentOS-7.0.1406 - CentOSPlus      disabled
C7.0.1406-updates/x86_64             CentOS-7.0.1406 - Updates         disabled
base                                 CentOS-7 - Base                   enabled: 8,652
base-debuginfo/x86_64                CentOS-7 - Debuginfo              disabled
base-source/7                        CentOS-7 - Base Sources           disabled
centosplus/7/x86_64                  CentOS-7 - Plus                   disabled
centosplus-source/7                  CentOS-7 - Plus Sources           disabled
cr/7/x86_64                          CentOS-7 - cr                     disabled
extras                               CentOS-7 - Extras                 enabled:   181
extras-source/7                      CentOS-7 - Extras Sources         disabled
fasttrack/7/x86_64                   CentOS-7 - fasttrack              disabled
updates                              CentOS-7 - Updates                enabled: 1,302
updates-source/7                     CentOS-7 - Updates Sources        disabled
repolist: 10,135
# 上面最右边有写 enabled 才是有启动的！由于 /etc/yum.repos.d/
# 有多个配置文件，所以你会发现还有其他的软件库存在。
```

- 修改软件库产生的问题与解决之道

由于我们是修改系统默认的配置文件，事实上，我们应该要在 /etc/yum.repos.d/ 下面新建一个文件， 该扩展名必须是 .repo 才行！但因为我们使用的是指定特定的映射站台，而不是其他软件开发商提供的软件库， 因此才修改系统默认配置文件。但是可能由于使用的软件库版本有新旧之分，你得要知道， yum 会先下载软件库的清单到本机的 /var/cache/yum 里面去！那我们修改了网址却没有修改软件库名称 （中括号内的文字）， 可能就会造成本机的清单与 yum 服务器的清单不同步，此时就会出现无法更新的问题了！

那怎么办啊？很简单，就清除掉本机上面的旧数据即可！需要手动处理吗？不需要的， 通过 yum 的 clean 项目来处理即可！

```
[root@study ~]# yum clean [packages&#124;headers&#124;all]
选项与参数：
 packages：将已下载的软件文件删除
 headers ：将下载的软件文件开始删除
 all     ：将所有软件库数据都删除！

范例一：删除已下载过的所有软件库的相关数据 （含软件本身与清单）
[root@study ~]# yum clean all
```



### 22.3.3 YUM 的软件群组功能

YUM 还支持安装和移除软件群组，通过 `yum groupinstall` 和 `yum groupremove` 可以安装和移除整个软件群组。

常用命令：

```
[root@study ~]# yum grouplist
[root@study ~]# yum groupinfo "Scientific Support"
[root@study ~]# yum groupinstall "Scientific Support"
```

修改 `/etc/yum.conf` 以安装 optional 软件：

```
[root@study ~]# vim /etc/yum.conf
group_package_types=default, mandatory, optional
```

#### 原文：

通过 yum 来线上安装一个软件是非常的简单，但是，如果要安装的是一个大型专案呢？ 举例来说，鸟哥使用默认安装的方式安装了测试机，这部主机就只有 GNOME 这个窗口管理员， 那我如果想要安装 KDE 呢？难道需要重新安装？当然不需要，通过 yum的软件群组功能即可！ 来看看指令先：

```
[root@study ~]# yum [群组功能] [软件群组]
选项与参数：
   grouplist   ：列出所有可使用的“软件群组组”，例如 Development Tools 之类的；
   groupinfo   ：后面接 group_name，则可了解该 group 内含的所有软件名；
   groupinstall：这个好用！可以安装一整组的软件群组，相当的不错用！
   groupremove ：移除某个软件群组；

范例一：查阅目前软件库与本机上面的可用与安装过的软件群组有哪些？
[root@study ~]# yum grouplist
Installed environment groups:            # 已经安装的系统环境软件群组
   Development and Creative Workstation
Available environment groups:            # 还可以安装的系统环境软件群组
   Minimal Install
   Compute Node
   Infrastructure Server
   File and Print Server
   Basic Web Server
   Virtualization Host
   Server with GUI
   GNOME Desktop
   KDE Plasma Workspaces
Installed groups:                        # 已经安装的软件群组！
   Development Tools
Available Groups:                        # 还能额外安装的软件群组！
   Compatibility Libraries
   Console Internet Tools
   Graphical Administration Tools
   Legacy UNIX Compatibility
   Scientific Support
   Security Tools
   Smart Card Support
   System Administration Tools
   System Management
Done
```

你会发现系统上面的软件大多是群组的方式一口气来提供安装的！还记全新安装 CentOS 时， 不是可以选择所需要的软件吗？而那些软件不是利用 GNOME/KDE/X Window ... 之类的名称存在吗？ 其实那就是软件群组啰！如果你执行上述的指令后，在“Available Groups”下面应该会看到一个 “Scientific Support”的软件群组，想知道那是啥吗？就这样做：

```
[root@study ~]# yum groupinfo "Scientific Support"
Group: Scientific Support
 Group-Id: scientific
 Description: Tools for mathematical and scientific computations, and parallel computing.
 Optional Packages:
   atlas
   fftw
   fftw-devel
   fftw-static
   gnuplot
   gsl-devel
   lapack
   mpich
....（以下省略）....
```

你会发现那就是一个科学运算、平行运算会用到的各种工具就是了！而下方则列出许多应该会在该群组安装时被下载与安装的软件们！ 让我们直接来安装看看！

```
[root@study ~]# yum groupinstall "Scientific Support"
```

正常情况下系统是会帮你安装好各项软件的。只是伤脑筋的是，刚刚好 Scientific Support 里面的软件都是“可选择的”！而不是“主要的 （mandatory）”， 因此默认情况下，上面这些软件通通不会帮你安装！！如果你想要安装上述的软件，可以使用 yum install atlas fftw .. 一个一个写进去安装～ 如果想要让 groupinstall 默认安装好所有的 optional 软件呢？那就得要修改配置文件！更改选 groupinstall 选择的软件项目即可！如下所示：

```
[root@study ~]# vim /etc/yum.conf
.....（前面省略）.....
distroverpkg=centos-release   # 找到这一行，下面新增一行！
group_package_types=default, mandatory, optional
.....（下面省略）.....

[root@study ~]# yum groupinstall "Scientific Support"
```

你就会发现系统开始进行了一大堆软件的安装！那就是啦！这个 group 功能真是非常的方便呢！这个功能请一定要记下来，对你未来安装软件是非常有帮助的喔！ ^_^



### 22.3.4 EPEL/ELRepo 外挂软件以及自订配置文件

可以通过添加 EPEL 或 ELRepo 软件库来安装未包含在 CentOS 默认软件库中的软件。

EPEL 配置示例：

```
[root@study ~]# vim /etc/yum.repos.d/epel.repo
[epel]
name=epel packages
baseurl=https://dl.fedoraproject.org/pub/epel/7/x86_64/
gpgcheck=0
enabled=0
```

使用 EPEL 安装软件：

```
[root@study ~]# yum --enablerepo=epel install netcdf R
```

使用本机光盘作为软件源：

```
[root@study ~]# vim /etc/yum.repos.d/cdrom.repo
[mycdrom]
name=mycdrom
baseurl=file:///mnt
gpgcheck=0
enabled=0

[root@study ~]# yum --enablerepo=mycdrom install software_name
```

#### 原文：

鸟哥因为工作的关系，在 Linux 上面经常需要安装第三方协力软件，这包括 NetCDF 以及 MPICH 等等的软件。现在由于平行处理的函数库需求大增， 所以 MPICH 已经纳入默认的 CentOS 7 软件库中。但是 NetCDF 这个软件就没有包含在里头了～同时，Linux 上面还有个很棒的统计软件，这个软件名称为“ R ”！ 默认也是不在 CentOS 的软件库内～唉～那怎办？要使用前一章介绍的 Tarball 去编译与安装吗？这倒不需要～因为有很多我们好棒的网友提供预先编译版本了！

在 Fedora 基金会里面发展了一个外加软件计划 （Extra Packages for Enterprise Linux, EPEL），这个计划主要是针对 Red Hat Enterprise Linux 的版本来开发的， 刚刚好 CentOS 也是针对 RHEL 的版本来处理的嘛！所以也就能够支持该软件库的相关软件相依环境了。这个计划的主网站在下面网页：

- https://fedoraproject.org/wiki/EPEL

而我们的 CentOS 7 主要可以使用的软件仓库网址为：

- https://dl.fedoraproject.org/pub/epel/7/x86_64/

除了上述的 Fedora 计划所提供的额外软件库之外，其实社群里面也有朋友针对 CentOS 与 EPEL 的不足而提供的许多软件仓库喔！ 下面鸟哥是列出当初鸟哥为了要处理 PCI passthrough 虚拟化而使用到的 ELRepo 这个软件仓库，若有其他的需求，你就得要自己搜寻了！ 这个 ELRepo 软件仓库与提供给 CentOS 7.x 的网址如下：

- http://elrepo.org/tiki/tiki-index.php
- http://elrepo.org/linux/elrepo/el7/x86_64
- http://elrepo.org/linux/kernel/el7/x86_64

这个 ELRepo 的软件库跟其他软件库比较不同的地方在于这个软件库提供的数据大多是与核心、核心模块与虚拟化相关软件有关，例如 NVidia 的驱动程序也在里面咧！ 尤其提供了最新的核心 （取名为 kernel-ml 的软件名称，其实就是最新的 Linux 核心啊！），如果你的系统像鸟哥的某些发展服务器一样，那就有可能会使用到这个软件库喔！

好了！根据上面的说明，来玩一玩下面这个仿真案例看看：

问：我的系统上面想要通过上述的 CentOS 7 的 EPEL 计划来安装 netcdf 以及 R 这两套软件，该如何处理？答：

- 首先，你的系统应该要针对 epel 进行 yum 的配置文件处理，处理方式如下：

  |

  ```
  [root@study ~]# vim /etc/yum.repos.d/epel.repo
  [epel]
  name = epel packages
  baseurl = https://dl.fedoraproject.org/pub/epel/7/x86_64/
  gpgcheck = 0
  enabled = 0
  ```

  |

  鸟哥故意不要启动这个软件仓库，只是未来有需要的时候才进行安装，默认不要去找这个软件库！

- 接下来使用这个软件库来进行安装 netcdf 与 R 的行为喔！

  |

  ```
  [root@study ~]# yum --enablerepo=epel install netcdf R
  ```

  |

  这样就可以安装起来了！未来你没有加上 --enablerepo=epel 时，这个 EPEL 的软件并不会更新喔！

- 使用本机的原版光盘

万一你的主机并没有网络，但是你却有很多软件安装的需求～假设你的系统也都还没有任何升级的动作过， 这个时候我能不能用本机的光盘来作为主要的软件来源呢？答案当然是可以啊！那要怎么做呢？ 很简单，将你的光盘挂载到某个目录，我们这里还是继续假设在 /mnt 好了，然后设置如下的 yum 配置文件：

```
[root@study ~]# vim /etc/yum.repos.d/cdrom.repo
[mycdrom]
name = mycdrom
baseurl = file:///mnt
gpgcheck = 0
enabled = 0

[root@study ~]# yum --enablerepo=mycdrom install software_name
```

这个设置功能在你没有网络但是却需要解决很多软件相依性的状况时，相当好用啊！



### 22.3.5 全系统自动升级

通过 crontab 实现系统自动升级：

```
[root@study ~]# echo '10 1 * * * root /usr/bin/yum -y update' > /etc/cron.d/yumupdate
```

#### 原文：

我们可以手动选择是否需要升级，那能不能让系统自动升级，让我们的系统随时保持在最新的状态呢？ 当然可以啊！通过“ yum -y update ”来自动升级，那个 -y 很重要，因为可以自动回答 yes 来开始下载与安装！ 然后再通过 crontab 的功能来处理即可！假设我每天在台湾时间 3:00am 网络带宽比较轻松的时候进行升级， 你可以这样做的：

```
[root@study ~]# echo '10 1 * * * root /usr/bin/yum -y --enablerepo=epel update' &gt; /etc/cron.d/yumupdate
[root@study ~]# vim /etc/crontab
```

从此你的系统就会自动升级啦！很棒吧！此外，你还是得要分析登录文件与收集 root 的信件的， 因为如果升级的是核心软件 （kernel），那么你还是得要重新开机才会让安装的软件顺利运行的！ 所以还是得分析登录文件，若有新核心安装，就重新开机，否则就让系统自动维持在最新较安全的环境吧！ 真是轻松愉快的管理啊！



### 22.3.6 管理的抉择：RPM 还是 Tarball

1. 优先选择原厂的 RPM 功能。
2. 选择软件官网释出的 RPM 或者是提供的软件库网址。
3. 利用 Tarball 安装特殊软件。
4. 用 Tarball 测试新版软件。

#### 原文：

这一直是个有趣的问题：“如果我要升级的话，或者是全新安装一个新的软件， 那么该选择 RPM 还是 Tarball 来安装呢？”，事实上考虑的因素很多，不过鸟哥通常是这样建议的：

1. 优先选择原厂的 RPM 功能：

   由于原厂释出的软件通常具有一段时间的维护期，举例来说， RHEL 与 CentOS 每一个版本至少提供五年以上的更新期限。这对于我们的系统安全性来说，实在是非常好的选项！ 何解？既然 yum 可以自动升级，加上原厂会持续维护软件更新，那么我们的系统就能够自己保持在软件最新的状态， 对于资安来说当然会比较好一些的！ 此外，由于 RPM 与 yum 具有容易安装/移除/升级等特点，且还提供查询与验证的功能，安装时更有数码签章的保护， 让你的软件管理变的更轻松自在！因此，当然首选就是利用 RPM 来处理啦！

2. 选择软件官网释出的 RPM 或者是提供的软件库网址：

   不过，原厂并不会包山包海，因此某些特殊软件你的原版厂商并不会提供的！举例来说 CentOS 就没有提供 NTFS 的相关模块。此时你可以自行到官网去查阅，看看有没有提供相对到你的系统的 RPM 文件， 如果有提供软件库网址，那就更好啦！可以修改 yum 配置文件来加入该软件库，就能够自动安装与升级该软件！ 你说方不方便啊！

3. 利用 Tarball 安装特殊软件：

   某些特殊用途的软件并不会特别帮你制作 RPM 文件的，此时建议你也不要妄想自行制作 SRPM 来转成 RPM 啦！ 因为你只有区区一部主机而已，若是你要管理相同的 100 部主机，那么将源代码转制作成 RPM 就有价值！ 单机版的特殊软件，例如学术网络常会用到的 MPICH/PVM 等平行运算函数库，这种软件建议使用 tarball 来安装即可， 不需要特别去搜寻 RPM 啰！

4. 用 Tarball 测试新版软件：

   某些时刻你可能需要使用到新版的某个软件，但是原版厂商仅提供旧版软件，举例来说，我们的 CentOS 主要是定位于企业版，因此很多软件的要求是“稳”而不是“新”，但你就是需要新软件啊！ 然后又担心新软件装好后产生问题，回不到旧软件，那就惨了！此时你可以用 tarball 安装新软件到 /usr/local 下面， 那么该软件就能够同时安装两个版本在系统上面了！而且大多数软件安装数种版本时还不会互相干扰的！ 嘿嘿！用来作为测试新软件是很不错的呦！只是你就得要知道你使用的指令是新版软件还是旧版软件了！

所以说，RPM 与 Tarball 各有其优缺点，不过，如果有 RPM 的话，那么优先权还是在于 RPM 安装上面，毕竟管理上比较便利，但是如果软件的架构差异性太大， 或者是无法解决相依属性的问题，那么与其花大把的时间与精力在解决属性相依的问题上，还不如直接以 tarball 来安装，轻松又惬意！



### 22.3.7 基础服务管理：以 Apache 为例

通过 YUM 安装、启动和配置基础服务，以 Apache 为例：

1. 安装所需的软件：

```
[root@study ~]# yum install httpd php mariadb-server php-mysql
```

1. 启动并设置开机启动：

```
[root@study ~]# systemctl start httpd
[root@study ~]# systemctl enable httpd
```

1. 配置防火墙：

```
[root@study ~]# firewall-cmd --add-service="http"
[root@study ~]# firewall-cmd --permanent --add-service="http"
```

1. 测试服务：

打开浏览器，访问 `http://localhost`，确认 Apache 服务正常运行。

通过这些步骤，可以轻松管理和配置 Linux 系统中的软件和服务。

#### 原文：

我们在 17 章谈到 systemd 的服务管理，那个时候仅使用 vsftpd 这个比较简单的服务来做个说明，那是因为还没有谈到 yum 这个东东的缘故。 现在，我们已经处理好了网络问题 （20 章的内容），这个 yum 也能够顺利的使用！那么有没有其他的服务可以拿来做个测试呢？有的，我们就拿网站服务器来说明吧！

一般来说， WWW 网站服务器需要的有 WWW 服务器软件 + 网页程序语言 + 数据库系统 + 程序语言与数据库的链接软件等等，在 CentOS 上面， 我们需要的软件就有“ httpd + php + mariadb-server + php-mysql ”这些软件。不过我们默认仅要启用 httpd 而已，因此等一下虽然上面的软件都要安装， 不过仅有 httpd 默认要启动而已喔！

另外，在默认的情况下，你无须修改服务的配置文件，都通过系统默认值来处理你的服务即可！那么有个江湖口诀你可以将它背下来～ 让你在处理服务的时候就不会掉漆了～

1. 安装： yum install （你的软件）
2. 启动： systemctl start （你的软件）
3. 开机启动： systemctl enable （你的软件）
4. 防火墙： firewall-cmd --add-service="（你的服务）"; firewall-cmd --permanent --add-service="（你的服务）"
5. 测试： 用软件去查阅你的服务正常与否～

下面就让我们一步一步来实验吧！

```
# 0\. 先检查一下有哪些软件没有安装或已安装～这个不太需要进行～单纯是鸟哥比较龟毛要先查看看而已！
[root@study ~]# rpm -q httpd php mariadb-server php-mysql
httpd-2.4.6-31.el7.centos.1.x86_64        # 只有这个安装好了，下面三个都没装！
package php is not installed
package mariadb-server is not installed
package php-mysql is not installed

# 1\. 安装所需要的软件！
[root@study ~]# yum install httpd php mariadb-server php-mysql
# 当然，大前提是你的网络没问题！这样就可以直接线上安装或升级！

# 2\. 3\. 启动与开机启动，这两个步骤要记得一定得进行！
[root@study ~]# systemctl daemon-reload
[root@study ~]# systemctl start httpd
[root@study ~]# systemctl enable httpd
[root@study ~]# systemctl status httpd
httpd.service - The Apache HTTP Server
   Loaded: loaded （/usr/lib/systemd/system/httpd.service; enabled）
   Active: active （running） since Wed 2015-09-09 16:52:04 CST; 9s ago
 Main PID: 8837 （httpd）
   Status: "Total requests: 0; Current requests/sec: 0; Current traffic:   0 B/sec"
   CGroup: /system.slice/httpd.service
           ├─8837 /usr/sbin/httpd -DFOREGROUND

# 4\. 防火墙
[root@study ~]# firewall-cmd --add-service="http"
[root@study ~]# firewall-cmd --permanent  --add-service="http"
[root@study ~]# firewall-cmd --list-all
public （default, active）
  interfaces: eth0
  sources:
  services: dhcpv6-client ftp http https ssh   # 这个是否有启动才是重点！
  ports: 222/tcp 555/tcp
  masquerade: no
  forward-ports:
  icmp-blocks:
  rich rules:
        rule family="ipv4" source address="192.168.1.0/24" accept
```

在最后的测试中，进入图形界面，打开你的浏览器，在网址列输入“ [http://localhost](http://localhost/) ”就会出现如下的画面！ 那就代表成功了！你的 Linux 已经是 Web server 啰！就是这么简单！

![服务创建的第五步骤，测试一下有没有成功！](image/testing.jpg)图22.3.1、服务创建的第五步骤，测试一下有没有成功！





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
   wget http://vault.centos.org/7.1.1503/updates/Source/SPackages/ntp-4.2.6p5-19.el7.centos.1.src.rpm
   ```
   
2. 再尝试直接编译：

   ```
   rpmbuild --rebuild ntp-4.2.6p5-19.el7.centos.1.src.rpm
   ```
   
3. 安装相依软件：

   ```
   yum install libcap-devel openssl-devel libedit-devel pps-tools-devel autogen autogen-libopts-devel
   ```
   
4. 再次尝试编译：

   ```
   rpmbuild --rebuild ntp-4.2.6p5-19.el7.centos.1.src.rpm
   ```

最终的软件会放置在 `/root/rpmbuild/RPMS/x86_64/ntp-4.2.6p5-19.el7.centos.1.x86_64.rpm`。

#### 原文：

假设我下载了一个 SRPM 的文件，又不想要修订这个文件内的源代码与相关的设置值， 那么我可以直接编译并安装吗？当然可以！利用 rpmbuild 配合选项即可。选项主要有下面两个：

|             |                                                              |
| ----------- | ------------------------------------------------------------ |
| --rebuild   | 这个选项会将后面的 SRPM 进行“编译”与“打包”的动作，最后会产生 RPM 的文件，但是产生的 RPM 文件并没有安装到系统上。当你使用 --rebuild 的时候，最后通常会发现一行字体： `Wrote: /root/rpmbuild/RPMS/x86_64/pkgname.x86_64.rpm` 这个就是编译完成的 RPM 文件啰！这个文件就可以用来安装啦！安装的时候请加绝对路径来安装即可！ |
| --recompile | 这个动作会直接的“编译”“打包”并且“安装”啰！请注意， rebuild 仅“编译并打包”而已，而 recompile 不但进行编译跟打包，还同时进行“安装”了！ |

不过，要注意的是，这两个选项都没有修改过 SRPM 内的设置值，仅是通过再次编译来产生 RPM 可安装软件文件而已。 一般来说，如果编译的动作顺利的话，那么编译过程所产生的中间暂存盘都会被自动删除，如果发生任何错误， 则该中间文件会被保留在系统上，等待使用者的除错动作！

问：请由 http://vault.centos.org/ 下载正确的 CentOS 版本中， 在 updates 软件库当中的 ntp 软件 SRPM，请下载最新的那个版本即可，然后进行编译的行为。答：目前 （2015/09） 最新的版本为：ntp-4.2.6p5-19.el7.centos.1.src.rpm 这一个，所以我是这样作的：

- 先下载软件： wget http://vault.centos.org/7.1.1503/updates/Source/SPackages/ntp-4.2.6p5-19.el7.centos.1.src.rpm
- 再尝试直接编译看看： rpmbuild --rebuild ntp-4.2.6p5-19.el7.centos.1.src.rpm
- 上面的动作会告诉我还有一堆相依软件没有安装～所以我得要安装起来才行： yum install libcap-devel openssl-devel libedit-devel pps-tools-devel autogen autogen-libopts-devel
- 再次尝试编译的行为： rpmbuild --rebuild ntp-4.2.6p5-19.el7.centos.1.src.rpm
- 最终的软件就会被放置到： /root/rpmbuild/RPMS/x86_64/ntp-4.2.6p5-19.el7.centos.1.x86_64.rpm

上面的测试案例是将一个 SRPM 文件抓下来之后，依据你的系统重新进行编译。一般来说，因为该编译可能会依据你的系统硬件而最优化， 所以可能性能会好一些些，但是...人类根本感受不到那种性能优化的效果～所以并不建议你这么作。此外， 这种情况也很能发生在你从不同的 Linux distribution 所下载的 SRPM 拿来想要安装在你的系统上，这样作才算是有点意义。

一般来说，如果你有需要用到 SRPM 的文件，大部分的原因就是...你需要重新修改里面的某些设置，让软件加入某些特殊功能等等的。 所以啰，此时就得要将 SRPM 拆开，编辑一下编译配置文件，然后再予以重新编译啦！下个小节我们来玩玩修改设置的方式！



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
   rpm -ivh ntp-4.2.6p5-19.el7.centos.1.src.rpm
   ```
   
2. 查阅 `/root/rpmbuild` 目录内容：

   ```
   ll -l /root/rpmbuild
   ```

#### 原文：

SRPM 既然含有 source code ，那么其中必定有配置文件啰，所以首先我们必需要知道，这个 SRPM 在进行编译的时候会使用到哪些目录呢？这样一来才能够来修改嘛！ 不过从 CentOS 6.x 开始 （当然包含我们的 CentOS 7.x 啰），因为每个用户应该都有能力自己安装自己的软件，因此 SRPM 安装、设置、编译、最终结果所使用的目录都与操作者的主文件夹有关～鸟哥假设你用 root 的身份来进行 SRPM 的操作， 那么你应该就会使用到下列的目录喔：

|                        |                                                              |
| ---------------------- | ------------------------------------------------------------ |
| /root/rpmbuild/SPECS   | 这个目录当中放置的是该软件的配置文件，例如这个软件的信息参数、设置项目等等都放置在这里； |
| /root/rpmbuild/SOURCES | 这个目录当中放置的是该软件的原始文件 （*.tar.gz 的文件） 以及 config 这个配置文件； |
| /root/rpmbuild/BUILD   | 在编译的过程中，有些暂存的数据都会放置在这个目录当中；       |
| /root/rpmbuild/RPMS    | 经过编译之后，并且顺利的编译成功之后，将打包完成的文件放置在这个目录当中。里头有包含了 x86_64, noarch.... 等等的次目录。 |
| /root/rpmbuild/SRPMS   | 与 RPMS 内相似的，这里放置的就是 SRPM 封装的文件啰！有时候你想要将你的软件用 SRPM 的方式释出时， 你的 SRPM 文件就会放置在这个目录中了。 |

![鸟哥的图示](image/vbird_face-17228418623248.gif)

**Tips** 早期要使用 SRPM 时，必须是 root 的身份才能够使用编译行为，同时源代码都会被放置到 /usr/src/redhat/ 目录内喔！ 跟目前放置到 /~username/rpmbuild/ 的情况不太一样！

此外，在编译的过程当中，可能会发生不明的错误，或者是设置的错误，这个时候就会在 /tmp 下面产生一个相对应的错误文件，你可以根据该错误文件进行除错的工作呢！ 等到所有的问题都解决之后，也编译成功了，那么刚刚解压缩之后的文件，就是在 /root/rpmbild/{SPECS, SOURCES, BUILD} 等等的文件都会被杀掉，而只剩下放置在 /root/rpmbuild/RPMS 下面的文件了！

由于 SRPM 需要重新编译，而编译的过程当中，我们至少需要有 make 与其相关的程序，及 gcc, c, c++ 等其他的编译用的程序语言来进行编译，更多说明请参考[第二十一章源代码所需基础软件](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#tarball_pack)吧。 所以，如果你在安装的过程当中没有选取软件开发工具之类的软件，这时就得要使用上一小节介绍的 yum 来安装就是了！ 当然，那个 "Development Tools" 的软件群组请不要忘记安装了！

问：尝试将上个练习下载的 ntp 的 SRPM 软件直接安装到系统中 （不要编译），然后查阅一下所有用到的目录为何？答：

```
# 1\. 鸟哥这里假设你用 root 的身份来进行安装的行为喔！
[root@study ~]# rpm -ivh ntp-4.2.6p5-19.el7.centos.1.src.rpm
Updating / installing...
   1:ntp-4.2.6p5-19.el7.centos.1      ################################# [100%]
warning: user mockbuild does not exist - using root
warning: group mockbuild does not exist - using root
# 会有一堆 warning 的问题，那个不要理它！可以忽略没问题的！

# 2\. 查阅一下 /root/rpmbuild 目录的内容！
[root@study ~]# ll -l /root/rpmbuild
drwxr-xr-x. 3 root root   39 Sep  8 16:16 BUILD
drwxr-xr-x. 2 root root    6 Sep  8 16:16 BUILDROOT
drwxr-xr-x. 4 root root   32 Sep  8 16:16 RPMS
drwxr-xr-x. 2 root root 4096 Sep  9 09:43 SOURCES
drwxr-xr-x. 2 root root   39 Sep  9 09:43 SPECS     # 这个家伙最重要！
drwxr-xr-x. 2 root root    6 Sep  8 14:51 SRPMS

[root@study ~]# ll -l /root/rpmbuild/{SOURCES,SPECS}
/root/rpmbuild/SOURCES:
-rw-rw-r--. 1 root root      559 Jun 24 07:44 ntp-4.2.4p7-getprecision.patch
-rw-rw-r--. 1 root root      661 Jun 24 07:44 ntp-4.2.6p1-cmsgalign.patch
.....（中间省略）.....
/root/rpmbuild/SPECS:
-rw-rw-r--. 1 root root   41422 Jun 24 07:44 ntp.spec   # 这就是重点！
```





### 22.4.3 配置文件的主要内容（`.spec`）

在 `/root/rpmbuild/SOURCES` 放置原始文件和补丁文件后，编译步骤如 `./configure, make, make check, make install` 写入 SPECS 目录的配置文件（`.spec`）。

**示例：查看和修改 ntp.spec**

1. 查看 `ntp.spec`：

   ```
   vim /root/rpmbuild/SPECS/ntp.spec
   ```

配置文件中重要的部分：

- **系统整体信息**：

  ```
  Summary: The NTP daemon and utilities
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

#### 原文：

如前一个小节的练习，我们知道在 /root/rpmbuild/SOURCES 里面会放置原始文件 （tarball） 以及相关的修补档 （patch file）， 而我们也知道编译需要的步骤大抵就是 ./configure, make, make check, make install 等，那这些动作写入在哪里呢？ 就在 SPECS 目录中啦！让我们来瞧一瞧 SPECS 里面的文件说些什么吧！

```
[root@study ~]# cd /root/rpmbuild/SPECS
[root@study SPECS]# vim ntp.spec
# 1\. 首先，这个部分在介绍整个软件的基本相关信息！不论是版本还是释出次数等。
Summary: The NTP daemon and utilities           # 简易的说明这个软件的功能
Name: ntp                                       # 软件的名称
Version: 4.2.6p5                                # 软件的版本
Release: 19%{?dist}.1                           # 软件的释出版次
# primary license （COPYRIGHT） : MIT             # 下面有很多 # 的注解说明！
.....（中间省略）.....
License: （MIT and BSD and BSD with advertising） and GPLv2
Group: System Environment/Daemons
Source0: http://www.eecis.udel.edu/~ntp/ntp_spool/ntp4/ntp-4.2/ntp-%{version}.tar.gz
Source1: ntp.conf                               # 写 SourceN 的就是源代码！
Source2: ntp.keys                               # 源代码可以有很多个！
.....（中间省略）.....
Patch1: ntp-4.2.6p1-sleep.patch                 # 接下来则是补丁文件，就是 PatchN 的目的！
Patch2: ntp-4.2.6p4-droproot.patch
.....（中间省略）.....

# 2\. 这部分则是在设置相依属性需求的地方！
URL: http://www.ntp.org                         # 下面则是说明这个软件的相依性，
Requires（post）: systemd-units                   # 还有编译过程需要的软件有哪些等等！
Requires（preun）: systemd-units
Requires（postun）: systemd-units
Requires: ntpdate = %{version}-%{release}
BuildRequires: libcap-devel openssl-devel libedit-devel perl-HTML-Parser
BuildRequires: pps-tools-devel autogen autogen-libopts-devel systemd-units
.....（中间省略）.....
%package -n ntpdate                             # 其实这个软件包含有很多次软件喔！
Summary: Utility to set the date and time via NTP
Group: Applications/System
Requires（pre）: shadow-utils
Requires（post）: systemd-units
Requires（preun）: systemd-units
Requires（postun）: systemd-units
.....（中间省略）.....

# 3\. 编译前的预处理，以及编译过程当中所需要进行的指令，都写在这里
#    尤其 %build 下面的数据，几乎就是 makefile 里面的信息啊！
%prep                                           # 这部份大多在处理补丁的动作！
%setup -q -a 5
%patch1 -p1 -b .sleep                           # 这些 patch 当然与前面的 PatchN 有关！
%patch2 -p1 -b .droproot
.....（中间省略）.....
%build                                          # 其实就是 ./configure, make 等动作！
sed -i 's&#124;$CFLAGS -Wstrict-overflow&#124;$CFLAGS&#124;' configure sntp/configure
export CFLAGS="$RPM_OPT_FLAGS -fPIE -fno-strict-aliasing -fno-strict-overflow"
export LDFLAGS="-pie -Wl,-z,relro,-z,now"
%configure \                                    # 不就是 ./configure 的意思吗！
        --sysconfdir=%{_sysconfdir}/ntp/crypto \
        --with-openssl-libdir=%{_libdir} \
        --without-ntpsnmpd \
        --enable-all-clocks --enable-parse-clocks \
        --enable-ntp-signd=%{_localstatedir}/run/ntp_signd \
        --disable-local-libopts
echo '#define KEYFILE "%{_sysconfdir}/ntp/keys"' &gt;&gt; ntpdate/ntpdate.h
echo '#define NTP_VAR "%{_localstatedir}/log/ntpstats/"' &gt;&gt; config.h

make %{?_smp_mflags}                            # 不就是 make 了吗！
.....（中间省略）.....

%install                                        # 就是安装过程所进行的各项动作了！
make DESTDIR=$RPM_BUILD_ROOT bindir=%{_sbindir} install

mkdir -p $RPM_BUILD_ROOT%{_mandir}/man{5,8}
sed -i 's/sntp\.1/sntp\.8/' $RPM_BUILD_ROOT%{_mandir}/man1/sntp.1
mv $RPM_BUILD_ROOT%{_mandir}/man{1/sntp.1,8/sntp.8}
rm -rf $RPM_BUILD_ROOT%{_mandir}/man1
.....（中间省略）.....

# 4\. 这里列出，这个软件释出的文件有哪些的意思！
%files                                          # 这软件所属的文件有哪些的意思！
%dir %{ntpdocdir}
%{ntpdocdir}/COPYRIGHT
%{ntpdocdir}/ChangeLog
.....（中间省略）.....

# 5\. 列出这个软件的更改历史纪录档！
%changelog
* Tue Jun 23 2015 CentOS Sources &lt;[email protected]&gt; - 4.2.6p5-19.el7.centos.1
- rebrand vendorzone

* Thu Apr 23 2015 Miroslav Lichvar &lt;[email protected]&gt; 4.2.6p5-19.el7_1.1
- don't step clock for leap second with -x option （#1191122）
.....（后面省略）.....
```

要注意到的是 ntp.sepc 这个文件，这是主要的将 SRPM 编译成 RPM 的配置文件，他的基本规则可以这样看：

1. 整个文件的开头以Summary为开始，这部份的设置都是最基础的说明内容；
2. 然后每个不同的段落之间，都以 % 来做为开头，例如 %prep 与 %install 等；

我们来谈一谈几个常见的 SRPM 设置段落：

- 系统整体信息方面：

刚刚你看到的就有下面这些重要的咚咚啰：

| 参数                                             | 参数意义                                                     |
| ------------------------------------------------ | ------------------------------------------------------------ |
| Summary                                          | 本软件的主要说明，例如上表中说明了本软件是针对 NTP 的软件功能与工具等啦！ |
| Name                                             | 本软件的软件名称 （最终会是 RPM 文件的文件名构成之一）       |
| Version                                          | 本软件的版本 （也会是 RPM 文件名的构成之一）                 |
| Release                                          | 这个是该版本打包的次数说明 （也会是 RPM 文件名的构成之一）。由于我们想要动点手脚，所以请将“ 19%{?dist}.1 ” 修改为“ 20.vbird ” 看看 |
| License                                          | 这个软件的授权模式，看起来涵盖了所有知名的 Open source 授权啊！！ |
| Group                                            | 这个软件在安装的时候，主要是放置于哪一个软件群组当中 （yum grouplist 的特点！）； |
| URL                                              | 这个源代码的主要官方网站；                                   |
| SourceN                                          | 这个软件的来源，如果是网络上下载的软件，通常一定会有这个信息来告诉大家这个原始文件的来源！ 此外，如果有多个软件来源，就会以 Source0, Source1... 来处理源代码喔！ |
| PatchN                                           | 就是作为补丁的 patch file 啰！也是可以有好多个！             |
| BuildRoot                                        | 设置作为编译时，该使用哪个目录来暂存中间文件 （如编译过程的目标文件/链接文件等档）。 |
| 上述为必须要存在的项目，下面为可使用的额外设置值 |                                                              |
| Requires                                         | 如果你这个软件还需要其他的软件的支持，那么这里就必需写上来，则当你制作成 RPM 之后，系统就会自动的去检查啦！这就是“相依属性”的主要来源啰！ |
| BuildRequires                                    | 编译过程中所需要的软件。Requires 指的是“安装时需要检查”的，因为与实际运行有关，这个 BuildRequires 指的是“编译时”所需要的软件，只有在 SRPM 编译成为 RPM 时才会检查的项目。 |

上面几个数据通常都必需要写啦！但是如果你的软件没有相依属性的关系时，那么就可以不需要那个 Requires 啰！ 根据上面的设置，最终的文件名就会是“{Name}-{Version}-{Release}.{Arch}.rpm”的样式， 以我们上面的设置来说，文件名应该会是“ntp-4.2.6p5-20.vbird.x86_64.rpm”的样子啰！

- %description：

将你的软件做一个简短的说明！这个也是必需要的。还记得使用“ rpm -qi 软件名称 ”会出现一些基础的说明吗？ 上面这些东西包括 Description 就是在显示这些重要信息的啦！所以，这里记得要详加解释喔！

- %prep：

pre 这个关键字原本就有“在...之前”的意思，因此这个项目在这里指的就是“尚未进行设置或安装之前，你要编译完成的 RPM 帮你事先做的事情”，就是 prepare 的简写啰！那么他的工作事项主要有：

1. 进行软件的补丁 （patch） 等相关工作；
2. 寻找软件所需要的目录是否已经存在？确认用的！
3. 事先创建你的软件所需要的目录，或者事先需要进行的任务；
4. 如果待安装的Linux系统内已经有安装的时候可能会被覆盖掉的文件时，那么就必需要进行备份（backup）的工作了！

在本案例中，你会发现程序会使用 patch 去进行补丁的动作啦！所以程序的源代码才会更新到最新啊！

- %build：

build 就是创建啊！所以当然啰，这个段落就是在谈怎么 make 编译成为可执行的程序啰！ 你会发现在此部分的程序码方面，就是 ./configure, make 等项目哩！一般来说，如果你会使用 SRPM 来进行重新编译的行为， 通常就是要重新 ./configure 并给予新的参数设置！于是这部份就可能会修改到！

- %install：

编译完成 （build） 之后，就是要安装啦！安装就是写在这里，也就是类似 Tarball 里面的 make install 的意思啰！

- %files：

这个软件安装的文件都需要写到这里来，当然包括了“目录”喔！所以连同目录请一起写到这个段落当中！以备查验呢！^_^ ！此外，你也可以指定每个文件的类型，包括文档文件 （%doc 后面接的） 与配置文件 （%config 后面接的） 等等。

- %changelog：

这个项目主要则是在记录这个软件曾经的更新纪录啰！星号 （*） 后面应该要以时间，修改者， email 与软件版本来作为说明， 减号 （-） 后面则是你要作的详细说明啰！在这部份鸟哥就新增了两行，内容如下：

```
%changelog
* Wed Sep 09 2015 VBird Tsai &lt;[email protected]&gt;- 4.2.6p5-20.vbird
- only rbuild this SRPM to RPM

* Tue Jun 23 2015 CentOS Sources &lt;[email protected]&gt; - 4.2.6p5-19.el7.centos.1
- rebrand vendorzone
....（下面省略）....
```

修改到这里也差不多了，您也应该要了解到这个 ntp.spec 有多么重要！我们用 rpm -q 去查询一堆信息时， 其实都是在这里写入的！这样了解否？接下来，就让我们来了解一下如何将 SRPM 给他编译出 RPM 来吧！



### 22.4.4 SRPM 的编译指令（-ba/-bb）

使用 `rpmbuild` 编译 SRPM：

```
[root@study ~]# rpmbuild -ba ntp.spec  # 编译并同时产生 RPM 与 SRPM 文件
[root@study ~]# rpmbuild -bb ntp.spec  # 仅编译成 RPM 文件
```

示例：编译 ntp.spec 并查找生成的 RPM 文件：

```
cd /root/rpmbuild/SPECS
rpmbuild -ba ntp.spec
find /root/rpmbuild -name 'ntp*rpm'
```

#### 原文：

要将在 /root/rpmbuild 下面的数据编译或者是单纯的打包成为 RPM 或 SRPM 时，就需要 rpmbuild 指令与相关选项的帮忙了！我们只介绍两个常用的选项给您了解一下：

```
[root@study ~]# rpmbuild -ba ntp.spec  &lt;==编译并同时产生 RPM 与 SRPM 文件
[root@study ~]# rpmbuild -bb ntp.spec  &lt;==仅编译成 RPM 文件
```

这个时候系统就会这样做：

1. 先进入到 BUILD 这个目录中，亦即是： /root/rpmbuild/BUILD 这个目录；
2. 依照 *.spec 文件内的 Name 与 Version 定义出工作的目录名称，以我们上面的例子为例，那么系统就会在 BUILD 目录中先删除 ntp-4.2.6p5 的目录，再重新创建一个 ntp-4.2.6p5 的目录，并进入该目录；
3. 在新建的目录里面，针对 SOURCES 目录下的来源文件，也就是 *.spec 里面的 Source 设置的那个文件，以 tar 进行解压缩，以我们这个例子来说，则会在 /root/rpmbuild/BUILD/ntp-4.2.6p5 当中，将 /root/rpmbuild/SOURCES/ntp-* 等等多个源代码文件进行解压缩啦！
4. 再来开始 %build 及 %install 的设置与编译！
5. 最后将完成打包的文件给他放置到该放置的地方去，如果你的系统是 x86_64 的话，那么最后编译成功的 *.x86_64.rpm文件就会被放置在 /root/rpmbuild/RPMS/x86_64 里面啰！如果是 noarch 那么自然就是 /root/rpmbuild/RPMS/noarch 目录下啰！

整个步骤大概就是这样子！最后的结果数据会放置在 RPMS 那个目录下面就对啦！我们这个案例中想要同时打包 RPM 与 SRPM ， 因此请您自行处理一下“ rpmbuild -ba ntp.spec ”吧！

```
[root@study ~]# cd /root/rpmbuild/SPECS
[root@study SPECS]# rpmbuild -ba ntp.spec
.....（前面省略）.....
Wrote: /root/rpmbuild/SRPMS/ntp-4.2.6p5-20.vbird.src.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/ntp-4.2.6p5-20.vbird.x86_64.rpm
Wrote: /root/rpmbuild/RPMS/noarch/ntp-perl-4.2.6p5-20.vbird.noarch.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/ntpdate-4.2.6p5-20.vbird.x86_64.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/sntp-4.2.6p5-20.vbird.x86_64.rpm
Wrote: /root/rpmbuild/RPMS/noarch/ntp-doc-4.2.6p5-20.vbird.noarch.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/ntp-debuginfo-4.2.6p5-20.vbird.x86_64.rpm
Executing（%clean）: /bin/sh -e /var/tmp/rpm-tmp.xZh6yz
+ umask 022
+ cd /root/rpmbuild/BUILD
+ cd ntp-4.2.6p5
+ /usr/bin/rm -rf /root/rpmbuild/BUILDROOT/ntp-4.2.6p5-20.vbird.x86_64
+ exit 0

[root@study SPECS]# find /root/rpmbuild -name 'ntp*rpm'
/root/rpmbuild/RPMS/x86_64/ntp-4.2.6p5-20.vbird.x86_64.rpm
/root/rpmbuild/RPMS/x86_64/ntpdate-4.2.6p5-20.vbird.x86_64.rpm
/root/rpmbuild/RPMS/x86_64/ntp-debuginfo-4.2.6p5-20.vbird.x86_64.rpm
/root/rpmbuild/RPMS/noarch/ntp-perl-4.2.6p5-20.vbird.noarch.rpm
/root/rpmbuild/RPMS/noarch/ntp-doc-4.2.6p5-20.vbird.noarch.rpm
/root/rpmbuild/SRPMS/ntp-4.2.6p5-20.vbird.src.rpm
# 上面分别是 RPM 与 SRPM 的文件文件名！
```

您瞧！嘿嘿～有 vbird 的软件出现了！相当有趣吧！另外，有些文件软件是与硬件等级无关的 （因为单纯的文件啊！），所以如上表所示， 你会发现 ntp-doc-4.2.6p5-20.vbird.noarch.rpm 是 noarch 喔！有趣吧！





### 22.4.5 一个打包自己软件的范例

创建并打包自己的软件：

1. **创建源代码文件 tarball**：

   ```
   cd /root/rpmbuild/SOURCES
   wget http://linux.vbird.org/linux_basic/0520source/main-0.1.tgz
   wget http://linux.vbird.org/linux_basic/0520source/main_0.1_to_0.2.patch
   ```

2. **创建 `.spec` 文件**：

   ```
   cd /root/rpmbuild/SPECS
   vim main.spec
   ```

   ```
   Name:           main
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
   rpmbuild -ba main.spec
   ```
   
4. **安装和测试**：

   ```
   yum install /root/rpmbuild/RPMS/x86_64/main-0.1-1.el7.centos.x86_64.rpm
   rpm -ql main
   rpm -qi main
   ```

通过上述步骤，可以将自己的软件打包为 RPM 文件，便于安装和管理。

#### 原文：

这个就有趣了！我们自己来编辑一下自己制作的 RPM 怎么样？会很难吗？完全不会！ 我们这里就举个例子来玩玩吧！还记得我们在前一章谈到 Tarball 与 make 时，曾经谈到的 [main](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#make) 这个程序吗？现在我们将这个程序加上 Makefile 后， 将他制作成为 main-0.1-1.x86_64.rpm 好吗？那该如何进行呢？下面就让我们来处理处理吧！

- 制作源代码文件 tarball 产生：

因为鸟哥的网站并没有直接释出 main-0.2，所以假设官网提供的是 main-0.l 版本之外，同时提供了一个 patch 文件～ 那我们就得要这样作：

- main-0.1.tar.gz 放在 /root/rpmbuild/SOURCES/
- main_0.1_to_0.2_patch 放在 /root/rpmbuild/SOURCES/
- main.spec 自行撰写放在 /root/rpmbuild/SPECS/

```
# 1\. 先来处理源代码的部份，假设你的 /root/rpmbuild/SOURCES 已经存在了喔！
[root@study ~]# cd /root/rpmbuild/SOURCES
[root@study SOURCES]# wget http://linux.vbird.org/linux_basic/0520source/main-0.1.tgz
[root@study SOURCES]# wget http://linux.vbird.org/linux_basic/0520source/main_0.1_to_0.2.patch
[root@study SOURCES]# ll main*
-rw-r--r--. 1 root root  703 Sep  4 14:47 main-0.1.tgz
-rw-r--r--. 1 root root 1538 Sep  4 14:51 main_0.1_to_0.2.patch
```

接下来就是 spec 文件的创建啰！

- 创建 *.spec 的配置文件

这个文件的创建是所有 RPM 制作里面最重要的课题！你必须要仔细的设置他，不要随便处理！仔细看看吧！ 有趣的是，CentOS 7.x 会主动的将必要的设置参数列出来喔！相当有趣！ ^_^

```
[root@study ~]# cd /root/rpmbuild/SPECS
[root@study SPECS]# vim main.spec
Name:           main
Version:        0.1
Release:        1%{?dist}
Summary:        Shows sin and cos value.
Group:          Scientific Support
License:        GPLv2
URL:            http://linux.vbird.org/
Source0:        main-0.1.tgz             # 这两个文件名要正确喔！
Patch0:         main_0.1_to_0.2.patch

%description
This package will let you input your name and calculate sin cos value.

%prep
%setup -q
%patch0 -p1                              # 要用来作为 patch 的动作！

%build
make clean main                          # 编译就好！不要安装！

%install
mkdir -p %{buildroot}/usr/local/bin
install -m 755 main %{buildroot}/usr/local/bin # 这才是顺利的安装行为！

%files
/usr/local/bin/main

%changelog
* Wed Sep 09 2015 VBird Tsai &lt;[email protected]&gt; 0.2
- build the program
```

- 编译成为 RPM 与 SRPM

老实说，那个 spec 文件创建妥当后，后续的动作就简单的要命了！开始来编译吧！

```
[root@study SPECS]# rpmbuild -ba main.spec
.....（前面省略）.....
Wrote: /root/rpmbuild/SRPMS/main-0.1-1.el7.centos.src.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/main-0.1-1.el7.centos.x86_64.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/main-debuginfo-0.1-1.el7.centos.x86_64.rpm
```

很快的，我们就已经创建了几个 RPM 文件啰！接下来让我们好好测试一下打包起来的成果吧！

- 安装/测试/实际查询

```
[root@study ~]# yum install /root/rpmbuild/RPMS/x86_64/main-0.1-1.el7.centos.x86_64.rpm
[root@study ~]# rpm -ql main
/usr/local/bin/main   &lt;==自己尝试执行 main 看看！

[root@study ~]# rpm -qi main
Name        : main
Version     : 0.1
Release     : 1.el7.centos
Architecture: x86_64
Install Date: Wed 09 Sep 2015 04:29:08 PM CST
Group       : Scientific Support
Size        : 7200
License     : GPLv2
Signature   : （none）
Source RPM  : main-0.1-1.el7.centos.src.rpm
Build Date  : Wed 09 Sep 2015 04:27:29 PM CST
Build Host  : study.centos.vbird
Relocations : （not relocatable）
URL         : http://linux.vbird.org/
Summary     : Shows sin and cos value.
Description :
This package will let you input your name and calculate sin cos value.
# 看到没？属于你自己的软件喔！真是很愉快的啦！
```

用很简单的方式，就可以将自己的软件或者程序给他修改与设置妥当！以后你就可以自行设置你的 RPM 啰！当然，也可以手动修改你的 SRPM 的来源文件内容啰！





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