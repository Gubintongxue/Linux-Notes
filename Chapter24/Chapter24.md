## 24.1 编译前的任务：认识核心与取得核心源代码

在第一章中，我们谈到过 Linux 实际上指的是核心。这个“核心（kernel）”是操作系统的最底层，负责整个硬件的驱动，并提供系统所需的核心功能，如防火墙机制、文件系统支持等。因此，核心的作用至关重要。以下是核心的一些基本概念和相关知识。

### 24.1.1 什么是核心（Kernel）

#### 核心的作用

核心负责控制计算机硬件，执行系统所需的各项功能。如果核心不支持某个硬件或功能，那么该硬件或功能在系统中将无法使用。例如，如果核心没有包含某个网络功能的支持，不论如何设置，该功能都无法使用。因此，核心在操作系统中起着至关重要的作用。

#### 核心模块（Kernel Module）

为了应对硬件更新速度快的情况，Linux 采用了模块化设置。一些不常用的驱动程序被独立出来，编译成模块，系统运行时可以动态加载这些模块，无需重新编译整个核心。核心模块存放在 `/lib/modules/$(uname -r)/kernel/` 目录下。

#### 核心编译

核心是通过源代码编译而成的。源代码可以通过编译生成系统可识别的核心文件。编译核心可以使系统支持新的硬件或功能，同时也可以优化系统性能和稳定性。

#### 原文：

这已经是整个 Linux 基础的最后一篇了，所以，下面这些数据你应该都要“很有概念”才行～ 不能只是“好像有印象”～好了，那就复习一下核心的相关知识吧！

- Kernel

还记得我们在[第十章的 BASH shell](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 提到过：计算机真正在工作的东西其实是“硬件”， 例如数值运算要使用到 CPU、数据储存要使用到硬盘、图形显示会用到显卡、音乐发声要有音效芯片、连接 Internet 可能需要网卡等等。那么如何控制这些硬件呢？那就是核心的工作了！也就是说，你所希望计算机帮你达成的各项工作， 都需要通过“核心”的帮助才行！当然啰，如果你想要达成的工作是核心所没有提供的， 那么你自然就没有办法通过核心来控制计算机使他工作啰！

举例来说，如果你想要有某个网络功能 （例如核心防火墙机制） ，但是你的核心偏偏忘记加进去这项功能， 那么不论你如何“卖力”的设置该网络套件，很抱歉！不来电！换句话说，你想要让计算机进行的工作，都必须要“核心有支持”才可以！这个标准不论在 Windows 或 Linux 这几个操作系统上都相同！如果有一个人开发出来一个“全新的硬件”，目前的核心不论 Windows 或 Linux 都不支持，那么不论你用什么系统，哈哈！这个硬件都是英雄无用武之地啦！ 那么是否了解了“核心”的重要了呢？所以我们才需要来了解一下如何编译我们的核心啦！

那么核心到底是什么啊？其实核心就是系统上面的一个文件而已， 这个文件包含了驱动主机各项硬件的侦测程序与驱动模块。在[第十九章的开机流程分析](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)中，我们也提到这个文件被读入内存的时机， 当系统读完 BIOS 并载入 MBR 内的开机管理程序后，就能够载入核心到内存当中。然后核心开始侦测硬件， 挂载根目录并取得核心模块来驱动所有的硬件，之后调用 systemd 就能够依序启动所有系统所需要的服务了！

这个核心文件通常被放置成 /boot/vmlinuz-xxx ，不过也不见得， 因为一部主机上面可以拥有多个核心文件，只是开机的时候仅能选择一个来载入而已。 甚至我们也可以在一个 distribution 上面放置多个核心，然后以这些核心来做成多重开机呢！

- 核心模块 （kernel module） 的用途

既然核心文件都已经包含了硬件侦测与驱动模块，那么什么是核心模块啊？要注意的是， 现在的硬件更新速度太快了，如果我的核心比较旧，但我换了新的硬件，那么，这个核心肯定无法支持！ 怎么办？重新拿一个新的核心来处理吗？开玩笑～核心的编译过程可是很麻烦的～

所以啰，为了这个缘故，我们的 Linux 很早之前就已经开始使用所谓的模块化设置了！ 亦即是将一些不常用的类似驱动程序的咚咚独立出核心，编译成为模块，然后， 核心可以在系统正常运行的过程当中载入这个模块到核心的支持。如此一来， 我在不需要更动核心的前提之下，只要编译出适当的核心模块，并且载入他，呵呵！我的 Linux 就可以使用这个硬件啦！简单又方便！

那我的模块放在哪里啊？可恶！怎么会问这个傻问题呢？当然一定要知道的啦！就是 /lib/modules/$（uname -r）/kernel/ 当中啦！

- 自制核心 - 核心编译

刚刚上面谈到的核心其实是一个文件，那么这个文件怎么来的？当然是通过源代码 （source code） 编译而成的啊！因为核心是直接被读入到内存当中的，所以当然要将他编译成为系统可以认识的数据才行！也就是说， 我们必须要取得核心的源代码，然后利用[第二十一章 Tarball](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 安装方式提到的编译概念来达成核心的编译才行啊！（这也是本章的重点啊！ ^_^）

- 关于驱动程序 - 是厂商的责任还是核心的责任？

现在我们知道硬件的驱动程序可以编译成为核心模块，所以可以在不改变核心的前提下驱动你的新硬件。 但是，很多朋友还是常常感到困惑，就是 Linux 上面针对最新硬件的驱动程序总是慢了几个脚步， 所以觉得好像 Linux 的支持度不足！其实不可以这么说的，为什么呢？因为在 Windows 上面，对于最新硬件的驱动程序需求，基本上，也都是厂商提供的驱动程序才能让该硬件工作的， 因此，在这个“驱动程序开发”的工作上面来说，应该是属于硬件发展厂商的问题， 因为他要我们买他的硬件，自然就要提供消费者能够使用的驱动程序啦！

所以，如果大家想要让某个硬件能够在 Linux 上面跑的话，那么似乎可以发起一人一信的方式，强烈要求硬件开发商发展 Linux 上面的驱动程序！这样一来，也可以促进 Linux 的发展呢！





### 24.1.2 更新核心的目的

核心是操作系统中最早被加载到内存的部分，包含所有硬件和软件工作所需的信息。因此，更新核心的主要目的是让系统支持新的硬件或功能，提升系统稳定性和性能。

编译核心的可能原因包括：

- **新功能需求**：例如，需要新的核心版本才能支持某些新功能。
- **原本核心太臃肿**：去除不需要的功能，使核心更简洁高效。
- **与硬件搭配的稳定性**：针对特定硬件进行优化。
- **嵌入式系统**：为特定用途的系统定制核心。

#### 原文：

除了 BIOS （或 UEFI） 之外，核心是操作系统中最早被载入到内存的咚咚， 他包含了所有可以让硬件与软件工作的信息，所以，如果没有搞定核心的话， 那么你的系统肯定会有点小问题！好了，那么是不是将“所有目前核心有支持的东西都给他编译进去我的核心中， 那就可以支持目前所有的硬件与可执行的工作啦！”！

这话说的是没错啦，但是你是否曾经看过一个为了怕自己今天出门会口渴、会饿、会冷、会热、会被车撞、 会摔跤、会被性骚扰，而在自己的大包包里面放了大瓶矿泉水、便当、厚外套、短裤、防撞钢梁、止滑埝、 电击棒....等一大堆东西，结果却累死在半路上的案例吗？当然有！但是很少啦！我相信不太有人会这样做！ （会这么做的人通常都已经在医院了～） 取而代之的是会看一下天气，冷了就只带外套， 热了就只带短衣、如果穿的漂亮一点又预计晚点回家就多带个电击棒、 出远门到没有便利商店的地方才多带矿泉水....

说这个干什么！对啦！就是要你了解到，核心的编译重点在于“你要你的 Linux 作什么？”，是啦！如果没有必要的工作，就干脆不要加在你的核心当中了！这样才能让你的 Linux 跑得更稳、更顺畅！这也是为什么我们要编译核心的最主要原因了！

- Linux 核心特色，与默认核心对终端用户的角色

Linux 的核心有几个主要的特色，除了“Kernel 可以随时、随各人喜好而更动”之外，Kernel 的“版本更动次数太频繁”也是一个特点！所以啰，除非你有特殊需求， 否则一次编译成功就可以啦！不需要随时保持最新的核心版本，而且也没有必要 （编译一次核心要粉久的ㄋㄟ！） 。

那么是否“我就一定需要在安装好了 Linux 之后就赶紧给他编译核心呢？”， 老实说，“并不需要的”！这是因为几乎每一个 distribution 都已经默认编译好了相当大量的模块了， 所以使用者常常或者可能会使用到的数据都已经被编译成为模块，也因此，呵呵！ 我们使用者确实不太需要重新来编译核心！尤其是“一般的使用者， 由于系统已经将核心编译的相当的适合一般使用者使用了，因此一般入门的使用者，基本上， 不太需要编译核心”。

- 核心编译的可能目的

OK！那么鸟哥闲闲没事干跑来写个什么东西？既然都不需要编译核心还写编译核心的分享文章， 鸟哥卖弄才学呀？很抱歉，鸟哥虽然是个“不学有术”的混混，却也不会平白无故的写东西请您来指教～ 当然是有需要才会来编译核心啦！编译核心的时机可以归纳为几大类：

- 新功能的需求： 我需要新的功能，而这个功能只有在新的核心里面才有，那么为了获得这个功能，只好来重新编译我的核心了。例如 iptables 这个防火墙机制只有在 2.4.xx 以后的版本里面才有，而新开发的主板芯片组， 很多也需要新的核心推出之后，才能正常而且有效率的工作！
- 原本核心太过臃肿： 如果你是那种对于系统“稳定性”很要求的人，对于核心多编译了很多莫名其妙的功能而不太喜欢的时候， 那么就可以重新编译核心来取消掉该功能啰；
- 与硬件搭配的稳定性： 由于原本 Linux 核心大多是针对 Intel 的 CPU 来作开发的，所以如果你的 CPU 是 AMD 的系统时，有可能 （注意！只是有可能，不见得一定会如此） 会让系统跑得“不太稳！”。此外，核心也可能没有正确的驱动新的硬件，此时就得重新编译核心来让系统取得正确的模块才好。
- 其他需求 （如嵌入式系统）： 就是你需要特殊的环境需求时，就得自行设计你的核心啰！（ 像是一些商业的套装软件系统，由于需要较为小而美的操作系统， 那么他们的核心就需要更简洁有力了！）

![鸟哥的图示](image/vbird_face.gif)

**Tips** 话说，2014 年鸟哥为了要搞定 banana pi （一种单版计算机，或者可以称为手机的硬件拿来作 Linux 安装的硬件） 的 CPU 最高频率限制， 因为该限制是直接写入到 Linux 核心当中的，这时就只好针对该硬件的 Linux 核心，修改不到 10 行的程序码之后，重新编译！ 才能将原本限制到 900MHz 的频率提升到 1.2GHz 哩！

另外，需要注意重新编译核心虽然可以针对你的硬件作最优化的步骤 （例如刚刚提到的 CPU 的问题！） ，不过由于这些最优化的步骤对于整体性能的影响是很小很小的， 因此如果是为了增加性能来编译核心的话，基本上，效益不大！然而，如果是针对“系统稳定性”来考虑的话， 那么就有充分的理由来支持你重新编译核心啰！

“如果系统已经运行很久了，而且也没有什么大问题， 加上我又不增加冷门的硬件设备，那么建议就不需要重新编译核心了”， 因为重新编译核心的最主要目的是“想让系统变的更稳！”既然你的 Linux 主机已经达到这个目的了，何必再编译核心？不过，就如同前面提到的， 由于默认的核心不见得适合你的需要，加上默认的核心可能并无法与你的硬件配备相配合， 此时才开始考虑重新编译核心吧！

![鸟哥的图示](image/vbird_face.gif)

**Tips** 早期鸟哥是强调最好重新编译核心的一群啦！不过，这个想法改变好久了～ 既然原本的 distribution 都已经帮我们考虑好如何使用核心了，那么， 我们也不需要再重新的编译核心啦！尤其是 distribution 都会主动的释出新版的核心 RPM 版本， 所以，实在不需要自己重新编译的！当然啦，如同前面提到的，如果你有特殊需求的话，那就另当别论噜！ ^_^

由于“核心的主要工作是在控制硬件！”所以编译核心之前， 请先了解一下你的硬件配备，与你这部主机的未来功能！由于核心是“越简单越好！”所以只要将这部主机的未来功能给他编进去就好了！ 其他的就不用去理他啦！





### 24.1.3 核心的版本

CentOS 7 使用 3.10.x 版本的长期维护核心。虽然理论上可以升级到更新的主线版本，但通常建议使用长期维护版本的最新版本。例如，CentOS 7 使用的是 3.10.0 版本，最新的长期维护版本是 3.10.89。

#### 原文：

核心的版本问题，我们在[第一章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#torvalds_team)已经谈论过， 目前 CentOS 7 使用的 3.10.x 版本为长期维护版本，不过理论上我们也可以升级到后续的主线版本上面！不会像以前 2.6.x 只能升级到 2.6.x 的后续版本， 而不能改成其他主线版本。不过这也只是“理论上”而已，因为目前许多的软件依旧与核心版本有关，例如那个虚拟化软件 qemu 之类的， 与核心版本之间是有搭配性的关系的，所以，除非你要一口气连同核心相依的软件通通升级，否则最好使用长期维护版本的最新版来处理较佳。

举例来说，CentOS 7 使用的是 3.10.0 这个长期版本，而目前 （2015/09） 这个 3.10 长期版本，最新的版本为 3.10.89，意思是说， 你最好是拿 3.10.89 来作为核心升级的依据，而不是拿最新的 4.2.1 来升级的意思。

虽然理论上还是拿自家长期维护版本的最新版本来处理比较好，不过鸟哥因为需要研究虚拟化的 PCI passthrough 技术， 确实也曾经在 CentOS 7.1 的系统中将 3.10.x 的版本升级到 4.2.3 这个版本上！这样才完成了 VGA 的 PCI passthrough 功能！ 所以说，如果你真的想要使用较新的版本来升级，也不是不可以，只是后果会发生什么问题，就得要自行负责啰！



### 24.1.4 核心源代码的取得方式

核心源代码可以通过以下几种方式获取：

#### 1. 使用 Distribution 提供的核心源代码文件

大多数主流 distributions 都提供了核心源代码。CentOS 的源代码可以从以下网址获取：

- [全部的 CentOS 原始 SRPM](http://vault.centos.org/)
- [CentOS 7.1 的 SRPM](http://vault.centos.org/7.1.1503/)

使用 distribution 提供的源代码的好处是可以了解默认的核心设置参数，便于修改和定制。

#### 2. 获取最新的稳定版核心源代码

最新的核心源代码可以从 kernel 官方网站下载：

- [核心官网](http://www.kernel.org/)
- [交大资科]()
- [国高中心]()

#### 3. 利用 patch 升级核心源代码

每次核心更新时，会发布与前一版本的差异性 patch 文件。可以通过 patch 文件升级核心源代码。需要依序应用 patch 文件，例如从 3.10.85 升级到 3.10.89，需要下载并依次应用 patch-3.10.86, patch-3.10.87, patch-3.10.88 和 patch-3.10.89。



#### 原文：

既然核心是个文件，要制作这个文件给系统使用则需要编译，既然要有编译，当然就得要有源代码啊！ 那么源代码怎么来？基本上，依据你的 distributions 去挑选的核心源代码来源主要有：

- 原本 distribution 提供的核心源代码文件

事实上，各主要 distributions 在推出他们的产品时，其实已经都附上了核心源代码了！ 不过因为目前数据量太庞大，因此 SRPM 默认已经不给映射站下载了！主要的源代码都放置于下面的网站上：

- 全部的 CentOS 原始 SRPM：http://vault.centos.org/
- CentOS 7.1 的 SRPM：http://vault.centos.org/7.1.1503/

CentOS 7.x 开始的版本中，其版本后面会接上释出的日期，因为 CentOS 7.1 是 2015/03 释出的，因此它的下载点就会是在 7.1.1503 啰！1503 指的就是 2015/03 的意思～ 你可以进入上述的网站后，到 updates 目录下，一层一层的往下找，就可以找到 kernel 相关的 SRPM 啰！

你或许会说：既然要重新编译，那么干嘛还要使用原本 distributions 释出的源代码啊？真没创意～ 话不是这么说，因为原本的 distribution 释出的源代码当中，含有他们设置好的默认设置值， 所以，我们可以轻易的就了解到当初他们是如何选择与核心及模块有关的各项设置项目的参数值， 那么就可以利用这些可以配合我们 Linux 系统的默认参数来加以修改，如此一来， 我们就可以“修改核心，调整到自己喜欢的样子”啰！而且编译的难度也会比较低一点！

- 取得最新的稳定版核心源代码

虽然使用 distribution 释出的核心 source code 来重新编译比较方便，但是，如此一来， 新硬件所需要的新驱动程序，也就无法借由原本的核心源代码来编译啊！ 所以啰，如果是站在要更新驱动程序的立场来看，当然使用最新的核心可能会比较好啊！

Linux 的核心目前是由其发明者 Linus Torvalds 所属团队在负责维护的，而其网站在下面的站址上，在该网站上可以找到最新的 kernel 信息！不过，美中不足的是目前的核心越来越大了 （linux-3.10.89.tar.gz 这一版，这一个文件大约 105MB 了！），所以如果你的 ISP 连外很慢的话，那么使用台湾的映射站台来下载不失为一个好方法：

- [核心官网：http://www.kernel.org/](http://www.kernel.org/pub/linux/kernel/)
- [交大资科：ftp://linux.cis.nctu.edu.tw/kernel/linux/kernel/](ftp://linux.cis.nctu.edu.tw/kernel/linux/kernel/)
- [国高中心：ftp://ftp.twaren.net/pub/Unix/Kernel/linux/kernel/](ftp://ftp.twaren.net/pub/Unix/Kernel/linux/kernel/)
- 保留原本设置：利用 patch 升级核心源代码

如果 （1）你曾经自行编译过核心，那么你的系统当中应该已经存在前几个版本的核心源代码， 以及上次你自行编译的参数设置值才对； （2）如果你只是想要在原本的核心下面加入某些特殊功能， 而该功能已经针对核心源代码推出 patch 补丁文件时。那你该如何进行核心源代码的更新，以便后续的编译呢？

其实每一次核心释出时，除了释出完整的核心压缩文件之外，也会释出“该版本与前一版本的差异性 patch 文件”， 关于 patch 的制作我们已经在[第二十一章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#tarball_patch)当中提及， 你可以自行前往参考。这里仅是要提供给你的信息是，每个核心的 patch 仅有针对前一版的核心来分析而已， 所以，万一你想要由 3.10.85 升级到 3.10.89 的话，那么你就得要下载 patch-3.10.86, patch-3.10.87, patch-3.10.88, patch-3.10.89 等文件，然后“依序”一个一个的去进行 patch 的动作后， 才能够升级到 3.10.89 喔！这个重要！不要忘记了。

同样的，如果是某个硬件或某些非官方认定的核心添加功能网站所推出的 patch 文件时，你也必须要了解该 patch 文件所适用的核心版本，然后才能够进行 patch ，否则容易出现重大错误喔！这个项目对于某些商业公司的工程师来说是很重要的。 举例来说，鸟哥的一个高中同学在业界服务，他主要是进行类似 Eee PC 开发的计划，然而该计划的硬件是该公司自行推出的！ 因此，该公司必须要自行搭配核心版本来设计他们自己的驱动程序，而该驱动程序并非 GPL 授权，因此他们就得要自行将驱动程序整合进核心！如果改天他们要将这个驱动程序释出，那么就得要利用 patch 的方式， 将硬件驱动程序文件释出，我们就得要自行以 patch 来更新核心啦！

在进行完 patch 之后，你可以直接检查一下原本的设置值，如果没有问题， 就可以直接编译，而不需要再重新的选择核心的参数值，这也是一个省时间的方法啊！ 至于 patch file 的下载，同样是在 kernel 的相同目录下，寻找文件名是 patch 开头的就是了。

### 24.1.5 核心源代码的解压缩/安装/观察

核心源代码通常下载为一个 tarball 文件。以下是解压缩和安装的步骤：

#### 解压缩核心源代码

假设下载的核心源代码文件为 `linux-3.10.89.tar.xz`，放置在 `/root` 目录下。解压缩并放置在 `/usr/src/kernels/` 目录下：

```
[root@study ~]# tar -Jxvf linux-3.10.89.tar.xz -C /usr/src/kernels/
```

解压后，会在 `/usr/src/kernels` 目录下生成 `linux-3.10.89` 目录。编译和设置核心都将在这个目录下进行。

#### 核心源代码目录结构

核心源代码目录下包含以下重要数据：

- `arch`：与硬件平台有关的设置，主要是 CPU 类型。
- `block`：与区块设备相关的设置。
- `crypto`：核心支持的加密技术。
- `Documentation`：核心相关的文档。
- `drivers`：硬件驱动程序。
- `firmware`：旧式硬件的固件数据。
- `fs`：核心支持的文件系统。
- `include`：其他程序调用的头文件定义。
- `init`：核心初始化定义功能。
- `ipc`：定义系统内各程序的通信。
- `kernel`：定义核心程序、状态、线程、调度、信号等。
- `lib`：一些函数库。
- `mm`：与内存单元相关的数据。
- `net`：与网络协议相关的数据。
- `security`：包括 selinux 等安全设置。
- `sound`：与音效相关的模块。
- `virt`：与虚拟化机器相关的信息。

这些目录和文件提供了核心编译和定制的基础信息。在进行核心编译和设置时，可以参考这些目录下的内容。

#### 原文：

其实，不论是从 CentOS 官网取得的 SRPM 或者是从 Linux kernel 官网取得的 tarball 核心源代码，最终都会有一个 tarball 的核心源代码就是了！ 因此，鸟哥从 linux kernel 官网取得 linux-3.10.89.tar.xz 这个核心文件，这个核心文件的源代码是从下面的网址取得的：

- [ftp://ftp.twaren.net/pub/Unix/Kernel/linux/kernel/v3.x/linux-3.10.89.tar.xz](ftp://ftp.twaren.net/pub/Unix/Kernel/linux/kernel/v3.x/linux-3.10.89.tar.xz)
- 核心源代码的解压缩与放置目录

鸟哥这里假设你也是下载上述的链接内的文件，然后该文件放置到 /root 下面。由于 Linux 核心源代码一般建议放置于 /usr/src/kernels/ 目录下面，因此你可以这样处理：

```
[root@study ~]# tar -Jxvf linux-3.10.89.tar.xz -C /usr/src/kernels/
```

此时会在 /usr/src/kernels 下面产生一个新的目录，那就是 linux-3.10.89 这个目录啰！ 我们在下个小节会谈到的各项编译与设置，都必须要在这个目录下面进行才行喔！好了，那么这个目录下面的相关文件有啥咚咚？ 下面就来谈谈：

- 核心源代码下的次目录

在上述核心目录下含有哪些重要数据呢？基本上有下面这些东西：

- arch ：与硬件平台有关的项目，大部分指的是 CPU 的类别，例如 x86, x86_64, Xen 虚拟支持等；
- block ：与区块设备较相关的设置数据，区块数据通常指的是大量储存媒体！还包括类似 ext3 等文件系统的支持是否允许等。
- crypto ：核心所支持的加密的技术，例如 md5 或者是 des 等等；
- Documentation ：与核心有关的一堆说明文档，若对核心有极大的兴趣，要瞧瞧这里！
- drivers ：一些硬件的驱动程序，例如显卡、网卡、PCI 相关硬件等等；
- firmware ：一些旧式硬件的微指令码 （固件） 数据；
- fs ：核心所支持的 filesystems ，例如 vfat, reiserfs, nfs 等等；
- include ：一些可让其他程序调用的标头 （header） 定义数据；
- init ：一些核心初始化的定义功能，包括挂载与 init 程序的调用等；
- ipc ：定义 Linux 操作系统内各程序的沟通；
- kernel ：定义核心的程序、核心状态、线程、程序的调度 （schedule）、程序的讯号 （signle） 等
- lib ：一些函数库；
- mm ：与内存单元有关的各项数据，包括 swap 与虚拟内存等；
- net ：与网络有关的各项协定数据，还有防火墙模块 （net/ipv4/netfilter/*） 等等；
- security ：包括 selinux 等在内的安全性设置；
- sound ：与音效有关的各项模块；
- virt ：与虚拟化机器有关的信息，目前核心支持的是 KVM （Kernel base Virtual Machine）

这些数据先大致有个印象即可，至少未来如果你想要使用 patch 的方法加入额外的新功能时， 你要将你的源代码放置于何处？这里就能够提供一些指引了。当然，最好还是跑到 Documentation 那个目录下面去瞧瞧正确的说明， 对你的核心编译会更有帮助喔！

## 24.2 核心编译的前处理与核心功能选择

编译核心需要进行前处理，以确保能正确管理硬件并提供系统核心功能。核心编译的关键是挑选适合的功能。以下是具体步骤：

### 24.2.1 硬件环境检视与核心功能要求

鸟哥的硬件环境如下：

- CPU: Intel(R) Xeon(R) CPU E5-2650
- 主板芯片组: KVM 虚拟化仿真主板 (Intel 440FX 相容)
- 显卡: Red Hat, Inc. QXL paravirtual graphic card
- 内存: 2.0GB
- 硬盘: KVM Virtio 界面磁盘 40G
- 网卡: Red Hat, Inc Virtio network device

需求是配置成小型服务器环境，支持虚拟化、防火墙、WWW、FTP 等功能。

### 24.2.2 保持干净源代码：make mrproper

处理核心源代码的残留文件：

```
bash复制代码[root@study ~]# cd /usr/src/kernels/linux-3.10.89/
[root@study linux-3.10.89]# make mrproper
```

- `make mrproper` 删除所有编译过程的目标文件和配置文件。
- `make clean` 仅删除目标文件，不删除配置文件。

### 24.2.3 开始挑选核心功能：make XXconfig

挑选核心功能并生成配置文件：

- `make menuconfig`: 文字模式下显示菜单，不需 X Window。
- `make oldconfig`: 使用已存在的 .config 文件，简化选择过程。
- `make xconfig`: 基于 Qt 的图形化接口，需要 X Window 支持。
- `make gconfig`: 基于 Gtk 的图形化接口，需要 X Window 支持。
- `make config`: 旧式条列式功能选择。

通过既有设置来处理核心项目与功能选择：

```
bash
复制代码
[root@study linux-3.10.89]# cp /boot/config-3.10.0-229.11.1.el7.x86_64 .config
```

使用 `make menuconfig` 进行选择。菜单使用方法：

- “上下方向键”选择项目，“左右方向键”移动至 <Select>, <Exit>, <Help>，按 Enter 确认。
- 使用“空白键”选择功能，<M> 表示编译成模块，<*> 表示编译进核心。

### 24.2.4 核心功能细项选择

核心功能选择分为多个部分：

1. **General setup**: 选择核心基本设置，如版本信息和压缩模式。

   ```
   bash复制代码(vbird)  Local version - append to kernel release
   [*] Automatically append version information to the version string
   Kernel compression mode (Bzip2)  --->
   ```

2. **Loadable module + block layer**: 启用可加载模块支持和块层支持。

   ```
   bash复制代码[*] Enable loadable module support  ---> 
     [*] Forced module loading
     [*] Module unloading
   ```

3. **CPU 类型与功能选择**: 设置 CPU 相关选项和虚拟化支持。

   ```
   bash复制代码Processor family (Generic-x86-64)  --->
   [*] Linux guest support  --->
   [*] Enable paravirtualization code
   ```

4. **电源管理功能**: 设置电源管理和 ACPI 选项。

   ```
   bash复制代码[*] ACPI (Advanced Configuration and Power Interface) Support  --->
   CPU Frequency scaling  --->
   <M>   CPU frequency translation statistics
   ```

5. **总线选项**: 设置 PCI、PCI-Express 和 PCMCIA 支持。

   ```
   bash复制代码[*] PCI support
   [*] PCI Express support
   ```

6. **编译后可执行文件的格式**: 支持 ELF 二进制文件和其他格式。

   ```
   bash复制代码-*- Kernel support for ELF binaries
   <M> Kernel support for MISC binaries
   ```

7. **核心的网络功能**: 设置网络支持和防火墙选项。

   ```
   bash复制代码--- Networking support
   [*] Network packet filtering framework (Netfilter)  --->
   ```

8. **各项设备的驱动程序**: 设置硬件驱动程序，如 SATA、RAID 和网络设备。

   ```
   bash复制代码<M> Serial ATA and Parallel ATA drivers  --->
   [*] Multiple devices driver support (RAID and LVM)  --->
   ```

9. **文件系统的支持**: 设置文件系统支持，如 ext3、ext4、XFS 和 NTFS。

   ```
   bash复制代码<M> The Extended 4 (ext4) filesystem
   <M> XFS filesystem support
   <M> NTFS file system support
   ```

10. **核心骇客、信息安全、密码应用**: 设置核心开发选项和安全选项。

    ```
    bash复制代码-*- Kernel hacking
    --- Security options
    ```

11. **虚拟化与函数库**: 设置虚拟化支持和核心函数库。

    ```
    bash复制代码[*] Virtualization  --->
    <M> Kernel-based Virtual Machine (KVM) support
    ```

保存设置：

```
bash
复制代码
# Save the configuration
```

## 总结

编译核心的前处理和功能选择是确保系统稳定和高效运行的重要步骤。通过检查硬件环境、保持源代码干净和细致挑选核心功能，可以创建适合特定用途的定制核心。

## 24.3 核心的编译与安装

在完成核心功能选择后，下一步是编译和安装核心以及核心模块。编译完成后，需要配置 Grub 来使用新核心。

### 24.3.1 编译核心与核心模块

编译核心和模块的过程如下：

```
bash复制代码[root@study linux-3.10.89]# make -j 4 clean    # 清除暂存文件
[root@study linux-3.10.89]# make -j 4 bzImage  # 编译核心
[root@study linux-3.10.89]# make -j 4 modules  # 编译模块
[root@study linux-3.10.89]# make -j 4 clean bzImage modules  # 连续编译所有部分
```

- `-j 4` 参数表示使用四个 CPU 核心进行并行编译，以加快速度。

编译完成后，生成的核心文件位于 `/usr/src/kernels/linux-3.10.89/arch/x86/boot/bzImage`。

### 24.3.2 实际安装模块

安装模块到系统目录：

```
bash
复制代码
[root@study linux-3.10.89]# make modules_install
```

模块将安装到 `/lib/modules/` 目录下。为了避免模块冲突，可以在 `make menuconfig` 中更改 `General setup` 中的 `Local version`，以区分不同版本的模块目录。

### 24.3.3 开始安装新核心与多重核心菜单（grub）

将编译好的核心文件移动到 `/boot` 目录并保留旧核心：

```
bash复制代码[root@study linux-3.10.89]# cp arch/x86/boot/bzImage /boot/vmlinuz-3.10.89vbird
[root@study linux-3.10.89]# cp .config /boot/config-3.10.89vbird
[root@study linux-3.10.89]# chmod a+x /boot/vmlinuz-3.10.89vbird
[root@study linux-3.10.89]# cp System.map /boot/System.map-3.10.89vbird
[root@study linux-3.10.89]# gzip -c Module.symvers > /boot/symvers-3.10.89vbird.gz
[root@study linux-3.10.89]# restorecon -Rv /boot
```

创建相对应的初始 RAM 磁盘（initrd）：

```
bash
复制代码
[root@study ~]# dracut -v /boot/initramfs-3.10.89vbird.img 3.10.89vbird
```

生成新的 Grub 配置文件：

```
bash复制代码[root@study ~]# grub2-mkconfig -o /boot/grub2/grub.cfg
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.10.89vbird
Found initrd image: /boot/initramfs-3.10.89vbird.img
```

确保新核心是默认启动项，然后重新启动系统。

### 24.3.4 重新启动并测试新核心

重新启动系统，选择新核心启动。启动后使用 `uname -a` 检查核心版本：

```
bash复制代码[root@study ~]# uname -a
Linux study.centos.vbird 3.10.89vbird #1 SMP Tue Oct 20 09:09:11 CST 2015 x86_64 x86_64 x86_64 GNU/Linux
```

如果系统稳定运行，可以将新核心设为默认启动核心。

## 总结

通过编译和安装新核心，可以根据需要定制 Linux 核心功能。保留旧核心确保系统在新核心出现问题时仍可启动。通过 Grub 配置，可以方便地管理多个核心版本。

## 24.4 额外（单一）核心模块编译

在 Linux 系统中，核心模块用于扩展核心功能，类似于驱动程序。核心模块存放在 `/lib/modules/$(uname -r)/kernel/` 目录下，不同的硬件驱动程序则放置在 `/lib/modules/$(uname -r)/kernel/drivers/` 目录中。如果某个模块在初次编译时被遗漏或需要更新硬件厂商提供的驱动程序，可以通过重新编译单个模块来解决。

### 24.4.1 编译前注意事项

编译核心模块需要核心源代码提供的头文件和函数库。核心源代码通常放在 `/usr/src/` 下，编译模块前需确保安装了 `kernel-devel` 包。对于多个核心版本，核心源代码目录通过 `/lib/modules/$(uname -r)/build` 和 `/lib/modules/$(uname -r)/source` 链接到正确的位置。

查看核心模块目录：

```
bash复制代码[root@study ~]# ll -h /lib/modules/3.10.89vbird/
lrwxrwxrwx.  1 root root   30 Oct 20 14:27 build -> /usr/src/kernels/linux-3.10.89
drwxr-xr-x. 11 root root 4.0K Oct 20 14:29 kernel
-rw-r--r--.  1 root root 668K Oct 20 14:29 modules.alias
-rw-r--r--.  1 root root 649K Oct 20 14:29 modules.alias.bin
-rw-r--r--.  1 root root 5.8K Oct 20 14:27 modules.builtin
-rw-r--r--.  1 root root 7.5K Oct 20 14:29 modules.builtin.bin
-rw-r--r--.  1 root root 208K Oct 20 14:29 modules.dep
-rw-r--r--.  1 root root 301K Oct 20 14:29 modules.dep.bin
-rw-r--r--.  1 root root  316 Oct 20 14:29 modules.devname
-rw-r--r--.  1 root root  81K Oct 20 14:27 modules.order
-rw-r--r--.  1 root root  131 Oct 20 14:29 modules.softdep
-rw-r--r--.  1 root root 269K Oct 20 14:29 modules.symbols
-rw-r--r--.  1 root root 339K Oct 20 14:29 modules.symbols.bin
lrwxrwxrwx.  1 root root   30 Oct 20 14:27 source -> /usr/src/kernels/linux-3.10.89
```

### 24.4.2 单一模块编译

有两种常见情况需要重新编译模块：

1. 默认核心未包含某个模块，且该模块可编译为独立模块。
2. 核心源代码没有特定硬件的驱动程序，但硬件厂商提供了驱动程序源代码。

#### 硬件开发商提供的额外模块

下载并编译硬件厂商提供的驱动程序模块。例如，编译 Highpoint 的 RocketRAID RR640L 驱动程序：

1. 解压并编译源代码：

```
bash复制代码[root@study ~]# cd /root/raidcard
[root@study raidcard]# tar -zxvf RR64xl_Linux_Src_v1.3.9_15_03_07.tar.gz
[root@study raidcard]# cd rr64xl-linux-src-v1.3.9/product/rr64xl/linux/
[root@study linux]# make
```

1. 将编译好的模块放到正确的位置：

```
bash复制代码[root@study linux]# cp rr640l.ko /lib/modules/3.10.89vbird/kernel/drivers/scsi/
[root@study linux]# depmod -a
[root@study linux]# grep rr640 /lib/modules/3.10.89vbird/modules.dep
```

1. 测试载入模块：

```
bash
复制代码
[root@study linux]# modprobe rr640l
```

1. 如果需要在开机时加载模块，将模块加入 initramfs：

```
bash复制代码[root@study linux]# dracut --force -v --add-drivers rr640l /boot/initramfs-3.10.89vbird.img 3.10.89vbird
[root@study linux]# lsinitrd /boot/initramfs-3.10.89vbird.img | grep rr640
```

#### 利用旧有的核心源代码进行编译

如果忘记编译某个模块，可以使用 `make menuconfig` 添加功能后，只重新编译该模块。例如，编译 NTFS 模块：

1. 进入核心源代码目录：

```
bash
复制代码
[root@study linux-3.10.89]# make menuconfig
```

1. 将 NTFS 选项设置为模块，然后编译：

```
bash
复制代码
[root@study linux-3.10.89]# make fs/ntfs/
```

1. 将模块复制到相应目录并更新模块依赖：

```
bash复制代码[root@study linux-3.10.89]# cp fs/ntfs/ntfs.ko /lib/modules/3.10.89vbird/kernel/fs/ntfs/
[root@study linux-3.10.89]# depmod -a
```

### 24.4.3 核心模块管理

核心与核心模块管理密不可分，驱动程序模块编译时依赖核心源代码。了解核心、核心模块、驱动程序模块、核心源代码及头文件的相关性，才能顺利编译和管理核心模块。核心更新时需重新编译模块，并通过 `modprobe` 和 `/etc/modprobe.conf` 配置管理模块加载。

通过上述方法，可以灵活地添加和管理核心模块，以满足不同硬件和系统需求。

## 24.5 以最新核心版本编译 CentOS 7.x 的核心

如果需要使用最新的 4.x.y 版核心来实现某些特定功能，可以通过 ELRepo 网站提供的 SRPM 文件来重新编译打包核心。下面是具体步骤：

### 24.5.1 下载和安装 ELRepo 的 SRPM 文件

1. 从 ELRepo 网站下载不含源代码的 SRPM 文件，并安装：

```
bash复制代码[root@study ~]# wget http://elrepo.org/linux/kernel/el7/SRPMS/kernel-ml-4.2.3-1.el7.elrepo.nosrc.rpm
[root@study ~]# rpm -ivh kernel-ml-4.2.3-1.el7.elrepo.nosrc.rpm
```

### 24.5.2 下载核心源代码

1. 根据上述的 SRPM 文件，下载正确的核心源代码：

```
bash复制代码[root@study ~]# cd rpmbuild/SOURCES
[root@study SOURCES]# wget https://cdn.kernel.org/pub/linux/kernel/v4.x/linux-4.2.3.tar.xz
[root@study SOURCES]# ll -tr
.....（前面省略）.....
-rw-r--r--. 1 root root 85523884 Oct  3 19:58 linux-4.2.3.tar.xz
-rw-rw-r--. 1 root root      294 Oct  3 22:04 cpupower.service
-rw-rw-r--. 1 root root      150 Oct  3 22:04 cpupower.config
-rw-rw-r--. 1 root root   162752 Oct  3 22:04 config-4.2.3-x86_64
```

### 24.5.3 修改核心功能设置

1. 修改核心功能设置，启用需要的选项：

```
bash复制代码[root@study SOURCES]# vim config-4.2.3-x86_64
# 在约第 5623 行找到以下行，并在其下添加一行：
# CONFIG_VFIO_PCI_VGA is not set
CONFIG_VFIO_PCI_VGA=y

[root@study SOURCES]# cd ../SPECS
[root@study SPECS]# vim kernel-ml-4.2.spec
# 在约第 145 行找到以下行：
Source0: ftp://ftp.kernel.org/pub/linux/kernel/v4.x/linux-%{LKAver}.tar.xz
# 修改为：
Source0: linux-%{LKAver}.tar.xz
```

### 24.5.4 编译并打包核心

1. 开始编译并打包核心：

```
bash复制代码[root@study SPECS]# rpmbuild -bb kernel-ml-4.2.spec
# 编译过程可能需要较长时间，请耐心等待。
Wrote: /root/rpmbuild/RPMS/x86_64/kernel-ml-4.2.3-1.el7.centos.x86_64.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/kernel-ml-devel-4.2.3-1.el7.centos.x86_64.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/kernel-ml-headers-4.2.3-1.el7.centos.x86_64.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/perf-4.2.3-1.el7.centos.x86_64.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/python-perf-4.2.3-1.el7.centos.x86_64.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/kernel-ml-tools-4.2.3-1.el7.centos.x86_64.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/kernel-ml-tools-libs-4.2.3-1.el7.centos.x86_64.rpm
Wrote: /root/rpmbuild/RPMS/x86_64/kernel-ml-tools-libs-devel-4.2.3-1.el7.centos.x86_64.rpm
```

### 24.5.5 安装新核心

1. 使用 `yum install` 安装新的核心版本：

```
bash复制代码[root@study ~]# yum install /root/rpmbuild/RPMS/x86_64/kernel-ml-4.2.3-1.el7.centos.x86_64.rpm
[root@study ~]# reboot
```

1. 验证安装：

```
bash复制代码[root@study ~]# uname -a
Linux study.centos.vbird 4.2.3-1.el7.centos.x86_64 #1 SMP Wed Oct 21 02:31:18 CST 2015 x86_64 x86_64 x86_64 GNU/Linux
```

通过上述步骤，你可以在 CentOS 7.x 上安装和使用最新的 4.x.y 版本核心。这个过程不仅能够保证系统功能的完整性，还可以实现特定硬件和功能的支持。
