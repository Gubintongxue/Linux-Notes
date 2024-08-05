# 第十七章、认识系统服务 （daemons）

在 Unix-Like 的系统中，你会常常听到 daemon 这个字眼！那么什么是传说中的 daemon 呢？这些 daemon 放在什么地方？他的功能是什么？该如何启动这些 daemon ？又如何有效的将这些 daemon 管理妥当？此外，要如何视察这些 daemon 开了多少个 ports ？又这些 ports 要如何关闭？还有还有，晓得你系统的这些 port 各代表的是什么服务吗？ 这些都是最基础需要注意的呢！尤其是在架设网站之前，这里的观念就显的更重要了。

从 CentOS 7.x 这一版之后，传统的 init 已经被舍弃，取而代之的是 systemd 这个家伙～这家伙跟之前的 init 有什么差异？ 优缺点为何？如何管理不同种类的服务类型？以及如何取代原本的“执行等级”等等，很重要的改变喔！

## 17.1 什么是 Daemon 与服务 (Service)

在第十六章中，我们提到过“服务”。服务是常驻内存中的程序，可以提供系统或网络功能，英文称为“service”。在Unix-Like操作系统中，常见的说法是“启动某某daemon来提供某某功能”。Daemon和service有关，因为它们都能提供系统或网络功能。Daemon字面上意思是“守护神”或“恶魔”。

简单来说，系统为了某些功能需要提供一些服务，这些服务称为service。实现这些service的程序称为daemon。例如，实现循环型例行性工作调度服务的程序为crond daemon。这样解释更容易理解。

#### 原文：

我们在[第十六章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#the_daemon)就曾经谈过“服务”这东西！ 当时的说明是“常驻在记体体中的程序，且可以提供一些系统或网络功能，那就是服务”。而服务一般的英文说法是“ service ”。

但如果你常常上网去查看一些数据的话，尤其是 Unix-Like 的相关操作系统，应该常常看到“请启动某某 daemon 来提供某某功能”，唔！那么 daemon 与 service 有关啰？否则为什么都能够提供某些系统或网络功能？此外，这个 daemon 是什么东西呀？ daemon 的字面上的意思就是“守护神、恶魔？”还真是有点奇怪呦！^_^""！

简单的说，系统为了某些功能必须要提供一些服务 （不论是系统本身还是网络方面），这个服务就称为 service 。 但是 service 的提供总是需要程序的运行吧！否则如何执行呢？所以达成这个 service 的程序我们就称呼他为 daemon 啰！ 举例来说，达成循环型例行性工作调度服务 （service） 的程序为 crond 这个 daemon 啦！这样说比较容易理解了吧！

![鸟哥的图示](image/vbird_face.gif)

**Tips** 你不必去区分什么是 daemon 与 service ！事实上，你可以将这两者视为相同！因为达成某个服务是需要一支 daemon 在背景中运行， 没有这支 daemon 就不会有 service ！所以不需要分的太清楚啦！

一般来说，当我们以文字模式或图形模式 （非单人维护模式） 完整开机进入 Linux 主机后， 系统已经提供我们很多的服务了！包括打印服务、工作调度服务、邮件管理服务等等； 那么这些服务是如何被启动的？他们的工作型态如何？下面我们就来谈一谈啰！

![鸟哥的图示](image/vbird_face.gif)

**Tips** daemon 既然是一只程序执行后的程序，那么 daemon 所处的那个原本的程序通常是如何命名的呢 （daemon 程序的命名方式）。 每一个服务的开发者，当初在开发他们的服务时，都有特别的故事啦！不过，无论如何，这些服务的名称被创建之后，被挂上 Linux 使用时，通常在服务的名称之后会加上一个 d ，例如例行性命令的创建的 at, 与 cron 这两个服务， 他的程序文件名会被取为 atd 与 crond，这个 d 代表的就是 daemon 的意思。所以，在[第十六章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)中，我们使用了 ps 与 top 来观察程序时，都会发现到很多的 {xxx}d 的程序，呵呵！通常那就是一些 daemon 的程序啰！



### 17.1.1 早期 System V 的 init 管理行为中 Daemon 的主要分类 (Optional)

在Unix System V版本中，启动系统服务的管理方式称为SysV init脚本程序处理方式。系统核心第一支调用的程序是init，然后init唤起所有系统需要的服务。

基本上，init的管理机制有以下特点：

- 服务的启动、关闭与观察方式：所有服务启动脚本放在

  ```
  /etc/init.d/
  ```
  
  目录下，基本上都是bash shell script。需要启动、关闭、重新启动、观察状态时，可以通过以下方式处理：

  - 启动：`/etc/init.d/daemon start`
  - 关闭：`/etc/init.d/daemon stop`
  - 重新启动：`/etc/init.d/daemon restart`
  - 状态观察：`/etc/init.d/daemon status`
  
- 服务启动的分类：

  - 独立启动模式 (stand alone)：服务独立启动，常驻内存中，提供本机或用户服务，反应速度快。
  - 总管程序 (super daemon)：由xinetd或inetd总管程序管理。当没有用户请求某socket或port时，所需服务不会启动。若有用户请求，xinetd总管才唤醒对应服务程序，服务结束时也会结束。好处是通过super daemon控制服务时程和连线需求，缺点是唤醒服务需要时间延迟。
  
- **服务的相依性问题**：服务可能有相依性问题。例如，启动网络服务需要网络，没有网络则无法启动网络服务。init在管理员手动处理这些服务时，无法协助相依服务唤醒。

- **执行等级的分类**：Linux提供7个执行等级(0-6)，例如单人维护模式、纯文本模式、文字加图形界面。各执行等级的启动脚本通过`/etc/rc.d/rc[0-6]/SXXdaemon`链接到`/etc/init.d/daemon`，启动脚本依序执行，解决相依服务问题。

- 制定执行等级默认启动服务：

  通过

  ```
  chkconfig
  ```

  命令管理默认启动服务。

  - 默认启动：`chkconfig daemon on`
  - 默认不启动：`chkconfig daemon off`
  - 观察默认是否启动：`chkconfig --list daemon`

- **执行等级的切换行为**：切换执行等级时，通过`init N`命令切换，init会自动分析`/etc/rc.d/rc[35].d/`目录内的脚本，启动转换runlevel中需要的服务。

虽然CentOS 7已经不使用init管理服务，但考虑到某些脚本无法直接整合到systemd处理，这些脚本仍被保留下来。

#### 原文：

还记得我们在[第一章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)谈到过 Unix 的 system V 版本吧？那个很纯种的 Unix 版本～ 在那种年代下面，我们启动系统服务的管理方式被称为 SysV 的 init 脚本程序的处理方式！亦即系统核心第一支调用的程序是 init ， 然后 init 去唤起所有的系统所需要的服务，不论是本机服务还是网络服务就是了。

基本上 init 的管理机制有几个特色如下：

- 服务的启动、关闭与观察等方式： 所有的服务启动脚本通通放置于 /etc/init.d/ 下面，基本上都是使用 bash shell script 所写成的脚本程序，需要启动、关闭、重新启动、观察状态时， 可以通过如下的方式来处理：
  - 启动：/etc/init.d/daemon start
  - 关闭：/etc/init.d/daemon stop
  - 重新启动：/etc/init.d/daemon restart
  - 状态观察：/etc/init.d/daemon status
- 服务启动的分类： init 服务的分类中，依据服务是独立启动或被一只总管程序管理而分为两大类：
  - 独立启动模式 （stand alone）：服务独立启动，该服务直接常驻于内存中，提供本机或用户的服务行为，反应速度快。
  - 总管程序 （super daemon）：由特殊的 xinetd 或 inetd 这两个总管程序提供 socket 对应或 port 对应的管理。当没有用户要求某 socket 或 port 时， 所需要的服务是不会被启动的。若有用户要求时， xinetd 总管才会去唤醒相对应的服务程序。当该要求结束时，这个服务也会被结束掉～ 因为通过 xinetd 所总管，因此这个家伙就被称为 super daemon。好处是可以通过 super daemon 来进行服务的时程、连线需求等的控制，缺点是唤醒服务需要一点时间的延迟。
- 服务的相依性问题： 服务是可能会有相依性的～例如，你要启动网络服务，但是系统没有网络， 那怎么可能可以唤醒网络服务呢？如果你需要连线到外部取得认证服务器的连线，但该连线需要另一个A服务的需求，问题是，A服务没有启动， 因此，你的认证服务就不可能会成功启动的！这就是所谓的服务相依性问题。init 在管理员自己手动处理这些服务时，是没有办法协助相依服务的唤醒的！
- 执行等级的分类： 上面说到 init 是开机后核心主动调用的， 然后 init 可以根据使用者自订的执行等级 （runlevel） 来唤醒不同的服务，以进入不同的操作界面。基本上 Linux 提供 7 个执行等级，分别是 0, 1, 2...6 ， 比较重要的是 1）单人维护模式、3）纯文本模式、5）文字加图形界面。而各个执行等级的启动脚本是通过 /etc/rc.d/rc[0-6]/SXXdaemon 链接到 /etc/init.d/daemon ， 链接文件名 （SXXdaemon） 的功能为： S为启动该服务，XX是数字，为启动的顺序。由于有 SXX 的设置，因此在开机时可以“依序执行”所有需要的服务， 同时也能解决相依服务的问题。这点与管理员自己手动处理不太一样就是了。
- 制定执行等级默认要启动的服务： 若要创建如上提到的 SXXdaemon 的话，不需要管理员手动创建链接文件， 通过如下的指令可以来处理默认启动、默认不启动、观察默认启动否的行为：
  - 默认要启动： chkconfig daemon on
  - 默认不启动： chkconfig daemon off
  - 观察默认为启动否： chkconfig --list daemon
- 执行等级的切换行为： 当你要从纯命令行 （runlevel 3） 切换到图形界面 （runlevel 5）， 不需要手动启动、关闭该执行等级的相关服务，只要“ init 5 ”即可切换，init 这小子会主动去分析 /etc/rc.d/rc[35].d/ 这两个目录内的脚本， 然后启动转换 runlevel 中需要的服务～就完成整体的 runlevel 切换。

基本上 init 主要的功能都写在上头了，重要的指令包括 daemon 本身自己的脚本 （/etc/init.d/daemon） 、xinetd 这个特殊的总管程序 （super daemon）、设置默认开机启动的 chkconfig， 以及会影响到执行等级的 init N 等。虽然 CentOS 7 已经不使用 init 来管理服务了，不过因为考虑到某些脚本没有办法直接塞入 systemd 的处理，因此这些脚本还是被保留下来， 所以，我们在这里还是稍微介绍了一下。更多更详细的数据就请自己查询旧版本啰！如下就是一个可以参考的版本：

- [http://linux.vbird.org/linux_basic/0560daemons/0560daemons-centos5.php](http://linux.vbird.org/linux_basic/0560daemons//0560daemons-centos5.php)

### 17.1.2 systemd 使用的 Unit 分类

从CentOS 7.x以后，Red Hat系列放弃了SysV init启动服务流程，改用systemd启动服务管理机制。systemd有以下好处：

- **平行处理所有服务，加速开机流程**：旧的init启动脚本是依序启动，未相依的服务也需等待。systemd允许所有服务同时启动，加快系统启动速度。
- **按需启动方式**：systemd通过systemctl指令处理，无需额外指令。systemd常驻内存，可立即处理服务启动任务。
- **服务相依性的自我检查**：systemd自订服务相依性检查，若B服务依赖A服务，未启动A服务时启动B服务，systemd会自动启动A服务。
- **按功能分类服务**：systemd将服务分类为unit，并归类为不同类型（type），如service、socket、target等。
- **多个daemons集合为一个群组**：类似init的runlevel，systemd将多个功能集合为target，执行某个target即执行多个daemon。
- **向下兼容旧有的init服务脚本**：systemd兼容init启动脚本，旧的init脚本可通过systemd管理。

systemd的配置文件放置目录如下：

- `/usr/lib/systemd/system/`：主要的启动脚本设置。
- `/run/systemd/system/`：系统执行过程中产生的服务脚本，优先序比`/usr/lib/systemd/system/`高。
- `/etc/systemd/system/`：管理员依据需求创建的执行脚本，优先序最高。

systemd的unit类型分类说明：

- `.service`：一般服务类型，系统服务和网络服务。
- `.socket`：数据交换插槽服务，通常用于本机服务。
- `.target`：执行环境类型，是一群unit的集合。
- `.mount`和`.automount`：文件系统挂载相关服务。
- `.path`：侦测特定文件或目录类型。
- `.timer`：循环执行的服务。

其中，`.service`是最常见的类型，因为大多数网络服务都是这种类型。

#### 原文：

从 CentOS 7.x 以后，Red Hat 系列的 distribution 放弃沿用多年的 System V 开机启动服务的流程，就是前一小节提到的 init 启动脚本的方法， 改用 systemd 这个启动服务管理机制～那么 systemd 有什么好处呢？

- 平行处理所有服务，加速开机流程： 旧的 init 启动脚本是“一项一项任务依序启动”的模式，因此不相依的服务也是得要一个一个的等待。但目前我们的硬件主机系统与操作系统几乎都支持多核心架构了， 没道理未相依的服务不能同时启动啊！systemd 就是可以让所有的服务同时启动，因此你会发现到，系统启动的速度变快了！
- 一经要求就回应的 on-demand 启动方式： systemd 全部就是仅有一只 systemd 服务搭配 systemctl 指令来处理，无须其他额外的指令来支持。不像 systemV 还要 init, chkconfig, service... 等等指令。 此外， systemd 由于常驻内存，因此任何要求 （on-demand） 都可以立即处理后续的 daemon 启动的任务。
- 服务相依性的自我检查： 由于 systemd 可以自订服务相依性的检查，因此如果 B 服务是架构在 A 服务上面启动的，那当你在没有启动 A 服务的情况下仅手动启动 B 服务时， systemd 会自动帮你启动 A 服务喔！这样就可以免去管理员得要一项一项服务去分析的麻烦～（如果读者不是新手，应该会有印象，当你没有启动网络， 但却启动 NIS/NFS 时，那个开机时的 timeout 甚至可达到 10~30 分钟...）
- 依 daemon 功能分类： systemd 旗下管理的服务非常多，包山包海啦～为了厘清所有服务的功能，因此，首先 systemd 先定义所有的服务为一个服务单位 （unit），并将该 unit 归类到不同的服务类型 （type） 去。 旧的 init 仅分为 stand alone 与 super daemon 实在不够看，systemd 将服务单位 （unit） 区分为 service, socket, target, path, snapshot, timer 等多种不同的类型（type）， 方便管理员的分类与记忆。
- 将多个 daemons 集合成为一个群组： 如同 systemV 的 init 里头有个 runlevel 的特色，systemd 亦将许多的功能集合成为一个所谓的 target 项目，这个项目主要在设计操作环境的创建， 所以是集合了许多的 daemons，亦即是执行某个 target 就是执行好多个 daemon 的意思！
- 向下相容旧有的 init 服务脚本： 基本上， systemd 是可以相容于 init 的启动脚本的，因此，旧的 init 启动脚本也能够通过 systemd 来管理，只是更进阶的 systemd 功能就没有办法支持就是了。

虽然如此，不过 systemd 也是有些地方无法完全取代 init 的！包括：

- 在 runlevel 的对应上，大概仅有 runlevel 1, 3, 5 有对应到 systemd 的某些 target 类型而已，没有全部对应；
- 全部的 systemd 都用 systemctl 这个管理程序管理，而 systemctl 支持的语法有限制，不像 /etc/init.d/daemon 就是纯脚本可以自订参数，systemctl 不可自订参数。；
- 如果某个服务启动是管理员自己手动执行启动，而不是使用 systemctl 去启动的 （例如你自己手动输入 crond 以启动 crond 服务），那么 systemd 将无法侦测到该服务，而无法进一步管理。
- systemd 启动过程中，无法与管理员通过 standard input 传入讯息！因此，自行撰写 systemd 的启动设置时，务必要取消互动机制～（连通过启动时传进的标准输入讯息也要避免！）

不过，光是同步启动服务脚本这个功能就可以节省你很多开机的时间～同时 systemd 还有很多特殊的服务类型 （type） 可以提供更多有趣的功能！确实值得学一学～ 而且 CentOS 7 已经用了 systemd 了！想不学也不行啊～哈哈哈！好～既然要学，首先就得要针对 systemd 管理的 unit 来了解一下。

- systemd 的配置文件放置目录

基本上， systemd 将过去所谓的 daemon 执行脚本通通称为一个服务单位 （unit），而每种服务单位依据功能来区分时，就分类为不同的类型 （type）。 基本的类型有包括系统服务、数据监听与交换的插槽档服务 （socket）、储存系统状态的快照类型、提供不同类似执行等级分类的操作环境 （target） 等等。 哇！这么多类型，那设置时会不会很麻烦呢？其实还好，因为配置文件都放置在下面的目录中：

- /usr/lib/systemd/system/：每个服务最主要的启动脚本设置，有点类似以前的 /etc/init.d 下面的文件；
- /run/systemd/system/：系统执行过程中所产生的服务脚本，这些脚本的优先序要比 /usr/lib/systemd/system/ 高！
- /etc/systemd/system/：管理员依据主机系统的需求所创建的执行脚本，其实这个目录有点像以前 /etc/rc.d/rc5.d/Sxx 之类的功能！执行优先序又比 /run/systemd/system/ 高喔！

也就是说，到底系统开机会不会执行某些服务其实是看 /etc/systemd/system/ 下面的设置，所以该目录下面就是一大堆链接文件。而实际执行的 systemd 启动脚本配置文件， 其实都是放置在 /usr/lib/systemd/system/ 下面的喔！因此如果你想要修改某个服务启动的设置，应该要去 /usr/lib/systemd/system/ 下面修改才对！ /etc/systemd/system/ 仅是链接到正确的执行脚本配置文件而已。所以想要看执行脚本设置，应该就得要到 /usr/lib/systemd/system/ 下面去查阅才对！

- systemd 的 unit 类型分类说明

那 /usr/lib/systemd/system/ 以下的数据如何区分上述所谓的不同的类型 （type） 呢？很简单！看扩展名！举例来说，我们来瞧瞧上一章谈到的 vsftpd 这个范例的启动脚本设置， 还有 crond 与纯文本模式的 multi-user 设置：

```
[root@study ~]# ll /usr/lib/systemd/system/ &#124; grep -E '（vsftpd&#124;multi&#124;cron）'
-rw-r--r--. 1 root root  284  7月 30  2014 crond.service
-rw-r--r--. 1 root root  567  3月  6 06:51 multipathd.service
-rw-r--r--. 1 root root  524  3月  6 13:48 multi-user.target
drwxr-xr-x. 2 root root 4096  5月  4 17:52 multi-user.target.wants
lrwxrwxrwx. 1 root root   17  5月  4 17:52 runlevel2.target -&gt; multi-user.target
lrwxrwxrwx. 1 root root   17  5月  4 17:52 runlevel3.target -&gt; multi-user.target
lrwxrwxrwx. 1 root root   17  5月  4 17:52 runlevel4.target -&gt; multi-user.target
-rw-r--r--. 1 root root  171  6月 10  2014 vsftpd.service
-rw-r--r--. 1 root root  184  6月 10  2014 [email protected]
-rw-r--r--. 1 root root   89  6月 10  2014 vsftpd.target
# 比较重要的是上头提供的那三行特殊字体的部份！
```

所以我们可以知道 vsftpd 与 crond 其实算是系统服务 （service），而 multi-user 要算是执行环境相关的类型 （target type）。根据这些扩展名的类型， 我们大概可以找到几种比较常见的 systemd 的服务类型如下：

| 扩展名            | 主要服务功能                                                 |
| ----------------- | ------------------------------------------------------------ |
| .service          | 一般服务类型 （service unit）：主要是系统服务，包括服务器本身所需要的本机服务以及网络服务都是！比较经常被使用到的服务大多是这种类型！ 所以，这也是最常见的类型了！ |
| .socket           | 内部程序数据交换的插槽服务 （socket unit）：主要是 IPC （Inter-process communication） 的传输讯息插槽档 （socket file） 功能。 这种类型的服务通常在监控讯息传递的插槽档，当有通过此插槽档传递讯息来说要链接服务时，就依据当时的状态将该用户的要求传送到对应的 daemon， 若 daemon 尚未启动，则启动该 daemon 后再传送用户的要求。使用 socket 类型的服务一般是比较不会被用到的服务，因此在开机时通常会稍微延迟启动的时间 （因为比较没有这么常用嘛！）。一般用于本机服务比较多，例如我们的图形界面很多的软件都是通过 socket 来进行本机程序数据交换的行为。 （这与早期的 xinetd 这个 super daemon 有部份的相似喔！） |
| .target           | 执行环境类型 （target unit）：其实是一群 unit 的集合，例如上面表格中谈到的 multi-user.target 其实就是一堆服务的集合～也就是说， 选择执行 multi-user.target 就是执行一堆其他 .service 或/及 .socket 之类的服务就是了！ |
| .mount .automount | 文件系统挂载相关的服务 （automount unit / mount unit）：例如来自网络的自动挂载、NFS 文件系统挂载等与文件系统相关性较高的程序管理。 |
| .path             | 侦测特定文件或目录类型 （path unit）：某些服务需要侦测某些特定的目录来提供伫列服务，例如最常见的打印服务，就是通过侦测打印伫列目录来启动打印功能！ 这时就得要 .path 的服务类型支持了！ |
| .timer            | 循环执行的服务 （timer unit）：这个东西有点类似 anacrontab 喔！不过是由 systemd 主动提供的，比 anacrontab 更加有弹性！ |

其中又以 .service 的系统服务类型最常见了！因为我们一堆网络服务都是通过这种类型来设计的啊！接下来，让我们来谈谈如何管理这些服务的启动与关闭。



## 17.2 通过 systemctl 管理服务

systemd通过名为systemctl的指令处理服务管理，替代了System V中需要的service、chkconfig、setup、init等指令。使用systemctl管理服务的操作更加集中和简便。以下是具体的操作方法和示例。

### 17.2.1 通过 systemctl 管理单一服务（service unit）的启动/开机启动与观察状态

服务的启动有两个阶段：一个是开机时设置服务是否启动，另一个是立即启动或停止服务。以下是一些常用的命令：

```
[root@study ~]# systemctl [command] [unit]
command 主要有：
start     ：立刻启动后面接的 unit
stop      ：立刻关闭后面接的 unit
restart   ：立刻关闭后启动后面接的 unit，亦即执行 stop 再 start 的意思
reload    ：不关闭后面接的 unit 的情况下，重新载入配置文件，让设置生效
enable    ：设置下次开机时，后面接的 unit 会被启动
disable   ：设置下次开机时，后面接的 unit 不会被启动
status    ：目前后面接的这个 unit 的状态，会列出有没有正在执行、开机默认执行否、登录等信息等！
is-active ：目前有没有正在运行中
is-enable ：开机时有没有默认要启用这个 unit
```

#### 示例：

1. 查看atd服务的状态：

```
[root@study ~]# systemctl status atd.service
```

输出结果中：

- **Loaded**：显示开机时是否启动（enabled为开机启动，disabled为不开机启动）。
- **Active**：显示当前服务状态（running表示正在运行，dead表示未运行）。
- **Login文件信息**：显示服务的日志信息。

1. 正常关闭atd服务：

```
sh复制代码[root@study ~]# systemctl stop atd.service
[root@study ~]# systemctl status atd.service
```

关闭后，服务状态应为inactive，但Loaded状态保持不变（enabled）。

1. 设置chronyd服务在开机时不启动并立即关闭：

```
[root@study ~]# systemctl stop chronyd.service
[root@study ~]# systemctl disable chronyd.service
[root@study ~]# systemctl status chronyd.service
```

#### Daemon状态解释：

- **active (running)**：程序正在执行。
- **active (exited)**：程序执行一次后正常结束。
- **active (waiting)**：程序在等待其他事件。
- **inactive**：服务未运行。

#### Daemon默认状态：

- **enabled**：开机时启动。
- **disabled**：开机时不启动。
- **static**：不可单独启动，但可被其他enabled服务唤醒。
- **mask**：强制注销，无法启动。

### 原文：

在开始这个小节之前，鸟哥要先来跟大家报告一下，那就是：一般来说，服务的启动有两个阶段，一个是“开机的时候设置要不要启动这个服务”， 以及“你现在要不要启动这个服务”，这两者之间有很大的差异喔！举个例子来说，假如我们现在要“立刻取消 atd 这个服务”时，正规的方法 （不要用 kill） 要怎么处理？

```
[root@study ~]# systemctl [command] [unit]
command 主要有：
start     ：立刻启动后面接的 unit
stop      ：立刻关闭后面接的 unit
restart   ：立刻关闭后启动后面接的 unit，亦即执行 stop 再 start 的意思
reload    ：不关闭后面接的 unit 的情况下，重新载入配置文件，让设置生效
enable    ：设置下次开机时，后面接的 unit 会被启动
disable   ：设置下次开机时，后面接的 unit 不会被启动
status    ：目前后面接的这个 unit 的状态，会列出有没有正在执行、开机默认执行否、登录等信息等！
is-active ：目前有没有正在运行中
is-enable ：开机时有没有默认要启用这个 unit

范例一：看看目前 atd 这个服务的状态为何？
[root@study ~]# systemctl status atd.service
atd.service - Job spooling tools
   Loaded: loaded （/usr/lib/systemd/system/atd.service; enabled）
   Active: active （running） since Mon 2015-08-10 19:17:09 CST; 5h 42min ago
 Main PID: 1350 （atd）
   CGroup: /system.slice/atd.service
           └─1350 /usr/sbin/atd -f

Aug 10 19:17:09 study.centos.vbird systemd[1]: Started Job spooling tools.
# 重点在第二、三行喔～
# Loaded：这行在说明，开机的时候这个 unit 会不会启动，enabled 为开机启动，disabled 开机不会启动
# Active：现在这个 unit 的状态是正在执行 （running） 或没有执行 （dead）
# 后面几行则是说明这个 unit 程序的 PID 状态以及最后一行显示这个服务的登录文件信息！
# 登录文件信息格式为：“时间” “讯息发送主机” “哪一个服务的讯息” “实际讯息内容”
# 所以上面的显示讯息是：这个 atd 默认开机就启动，而且现在正在运行的意思！

范例二：正常关闭这个 atd 服务
[root@study ~]# systemctl stop atd.service
[root@study ~]# systemctl status atd.service
atd.service - Job spooling tools
   Loaded: loaded （/usr/lib/systemd/system/atd.service; enabled）
   Active: inactive （dead） since Tue 2015-08-11 01:04:55 CST; 4s ago
  Process: 1350 ExecStart=/usr/sbin/atd -f $OPTS （code=exited, status=0/SUCCESS）
 Main PID: 1350 （code=exited, status=0/SUCCESS）

Aug 10 19:17:09 study.centos.vbird systemd[1]: Started Job spooling tools.
Aug 11 01:04:55 study.centos.vbird systemd[1]: Stopping Job spooling tools...
Aug 11 01:04:55 study.centos.vbird systemd[1]: Stopped Job spooling tools.
# 目前这个 unit 下次开机还是会启动，但是现在是没在运行的状态中！同时，
# 最后两行为新增加的登录讯息，告诉我们目前的系统状态喔！
```

上面的范例中，我们已经关掉了 atd 啰！这样作才是对的！不应该使用 kill 的方式来关掉一个正常的服务喔！否则 systemctl 会无法继续监控该服务的！ 那就比较麻烦。而使用 systemtctl status atd 的输出结果中，第 2, 3 两行很重要～因为那个是告知我们该 unit 下次开机会不会默认启动，以及目前启动的状态！ 相当重要！最下面是这个 unit 的登录文件～如果你的这个 unit 曾经出错过，观察这个地方也是相当重要的！

那么现在问个问题，你的 atd 现在是关闭的，未来重新开机后，这个服务会不会再次的启动呢？答案是？当然会！ 因为上面出现的第二行中，它是 enabled 的啊！这样理解所谓的“现在的状态”跟“开机时默认的状态”两者的差异了吗？

好！再回到 systemctl status atd.service 的第三行，不是有个 Active 的 daemon 现在状态吗？除了 running 跟 dead 之外， 有没有其他的状态呢？有的～基本上有几个常见的状态：

- active （running）：正有一只或多只程序正在系统中执行的意思，举例来说，正在执行中的 vsftpd 就是这种模式。
- active （exited）：仅执行一次就正常结束的服务，目前并没有任何程序在系统中执行。 举例来说，开机或者是挂载时才会进行一次的 quotaon 功能，就是这种模式！ quotaon 不须一直执行～只须执行一次之后，就交给文件系统去自行处理啰！通常用 bash shell 写的小型服务，大多是属于这种类型 （无须常驻内存）。
- active （waiting）：正在执行当中，不过还再等待其他的事件才能继续处理。举例来说，打印的伫列相关服务就是这种状态！ 虽然正在启动中，不过，也需要真的有伫列进来 （打印工作） 这样他才会继续唤醒打印机服务来进行下一步打印的功能。
- inactive：这个服务目前没有运行的意思。

既然 daemon 目前的状态就有这么多种了，那么 daemon 的默认状态有没有可能除了 enable/disable 之外，还有其他的情况呢？当然有！

- enabled：这个 daemon 将在开机时被执行
- disabled：这个 daemon 在开机时不会被执行
- static：这个 daemon 不可以自己启动 （enable 不可），不过可能会被其他的 enabled 的服务来唤醒 （相依属性的服务）
- mask：这个 daemon 无论如何都无法被启动！因为已经被强制注销 （非删除）。可通过 systemctl unmask 方式改回原本状态
- 服务启动/关闭与观察的练习

问题：找到系统中名为 chronyd 的服务，观察此服务的状态，观察完毕后，将此服务设置为： 1）开机不会启动 2）现在状况是关闭的情况！回答：我们直接使用指令的方式来查询与设置看看：

```
# 1\. 观察一下状态，确认是否为关闭/未启动呢？
[root@study ~]# systemctl status chronyd.service
hronyd.service - NTP client/server
   Loaded: loaded （/usr/lib/systemd/system/chronyd.service; enabled）
   Active: active （running） since Mon 2015-08-10 19:17:07 CST; 24h ago
.....（下面省略）.....

# 2\. 由上面知道目前是启动的，因此立刻将他关闭，同时开机不会启动才行！
[root@study ~]# systemctl stop chronyd.service
[root@study ~]# systemctl disable chronyd.service
rm '/etc/systemd/system/multi-user.target.wants/chronyd.service'
# 看得很清楚～其实就是从 /etc/systemd/system 下面删除一条链接文件而已～

[root@study ~]# systemctl status chronyd.service
chronyd.service - NTP client/server
   Loaded: loaded （/usr/lib/systemd/system/chronyd.service; disabled）
   Active: inactive （dead）
# 如此则将 chronyd 这个服务完整的关闭了！
```

上面是一个很简单的练习，你先不要知道 chronyd 是啥东西，只要知道通过这个方式，可以将一个服务关闭就是了！好！那再来一个练习， 看看有没有问题呢？

问题：因为我根本没有打印机安装在服务器上，目前也没有网络打印机，因此我想要将 cups 服务整个关闭，是否可以呢？回答：同样的，眼见为凭，我们就动手作看看：

```
# 1\. 先看看 cups 的服务是开还是关？
[root@study ~]# systemctl status cups.service
cups.service - CUPS Printing Service
   Loaded: loaded （/usr/lib/systemd/system/cups.service; enabled）
   Active: inactive （dead） since Tue 2015-08-11 19:19:20 CST; 3h 29min ago
# 有趣得很！竟然是 enable 但是却是 inactive 耶！相当特别！

# 2\. 那就直接关闭，同时确认没有启动喔！
[root@study ~]# systemctl stop    cups.service
[root@study ~]# systemctl disable cups.service
rm '/etc/systemd/system/multi-user.target.wants/cups.path'
rm '/etc/systemd/system/sockets.target.wants/cups.socket'
rm '/etc/systemd/system/printer.target.wants/cups.service'
# 也是非常特别！竟然一口气取消掉三个链接文件！也就是说，这三个文件可能是有相依性的问题喔！

[root@study ~]# netstat -tlunp &#124; grep cups
# 现在应该不会出现任何数据！因为根本没有 cups 的任务在执行当中～所以不会有 port 产生

# 3\. 尝试启动 cups.socket 监听用户端的需求喔！
[root@study ~]# systemctl start cups.socket
[root@study ~]# systemctl status cups.service cups.socket cups.path
cups.service - CUPS Printing Service
   Loaded: loaded （/usr/lib/systemd/system/cups.service; disabled）
   Active: inactive （dead） since Tue 2015-08-11 22:57:50 CST; 3min 41s ago
cups.socket - CUPS Printing Service Sockets
   Loaded: loaded （/usr/lib/systemd/system/cups.socket; disabled）
   Active: active （listening） since Tue 2015-08-11 22:56:14 CST; 5min ago
cups.path - CUPS Printer Service Spool
   Loaded: loaded （/usr/lib/systemd/system/cups.path; disabled）
   Active: inactive （dead）
# 确定仅有 cups.socket 在启动，其他的并没有启动的状态！

# 4\. 尝试使用 lp 这个指令来打印看看？
[root@study ~]# echo "testing" &#124; lp
lp: Error - no default destination available. # 实际上就是没有打印机！所以有错误也没关系！

[root@study ~]# systemctl status cups.service
cups.service - CUPS Printing Service
   Loaded: loaded （/usr/lib/systemd/system/cups.service; disabled）
   Active: active （running） since Tue 2015-08-11 23:03:18 CST; 34s ago
[root@study ~]# netstat -tlunp &#124; grep cups
tcp        0      0 127.0.0.1:631    0.0.0.0:*   LISTEN     25881/cupsd
tcp6       0      0 ::1:631          :::*        LISTEN     25881/cupsd
# 见鬼！竟然 cups 自动被启动了！明明我们都没有驱动他啊！怎么回事啊？
```

上面这个范例的练习在让您了解一下，很多服务彼此之间是有相依性的！cups 是一种打印服务，这个打印服务会启用 port 631 来提供网络打印机的打印功能。 但是其实我们无须一直启动 631 端口吧？因此，多了一个名为 cups.socket 的服务，这个服务可以在“用户有需要打印时，才会主动唤醒 cups.service ”的意思！ 因此，如果你仅是 disable/stop cups.service 而忘记了其他两个服务的话，那么当有用户向其他两个 cups.path, cups.socket 提出要求时， cups.service 就会被唤醒！所以，你关掉也没用！

- 强迫服务注销 （mask） 的练习

比较正规的作法是，要关闭 cups.service 时，连同其他两个会唤醒 service 的 cups.socket 与 cups.path 通通关闭，那就没事了！ 比较不正规的作法是，那就强迫 cups.service 注销吧！通过 mask 的方式来将这个服务注销看看！

```
# 1\. 保持刚刚的状态，关闭 cups.service，启动 cups.socket，然后注销 cups.servcie
[root@study ~]# systemctl stop cups.service
[root@study ~]# systemctl mask cups.service
ln -s '/dev/null' '/etc/systemd/system/cups.service'
# 喔耶～其实这个 mask 注销的动作，只是让启动的脚本变成空的设备而已！

[root@study ~]# systemctl status cups.service
cups.service
   Loaded: masked （/dev/null）
   Active: inactive （dead） since Tue 2015-08-11 23:14:16 CST; 52s ago

[root@study ~]# systemctl start cups.service
Failed to issue method call: Unit cups.service is masked.  # 再也无法唤醒！
```

上面的范例你可以仔细推敲一下～原来整个启动的脚本配置文件被链接到 /dev/null 这个空设备～因此，无论如何你是再也无法启动这个 cups.service 了！ 通过这个 mask 功能，你就可以不必管其他相依服务可能会启动到这个想要关闭的服务了！虽然是非正规，不过很有效！ ^_^

那如何取消注销呢？当然就是 unmask 即可啊！

```
[root@study ~]# systemctl unmask cups.service
rm '/etc/systemd/system/cups.service'
[root@study ~]# systemctl status cups.service
cups.service - CUPS Printing Service
   Loaded: loaded （/usr/lib/systemd/system/cups.service; disabled）
   Active: inactive （dead） since Tue 2015-08-11 23:14:16 CST; 4min 35s ago
# 好佳在有恢复正常！
```

### 17.2.2 通过 systemctl 观察系统上所有的服务

可以通过`list-units`和`list-unit-files`命令查看所有服务及其状态。

1. 查看系统上启动的unit：

```
[root@study ~]# systemctl
```

显示结果包括unit名称、加载状态（LOAD）、当前状态（ACTIVE）、详细描述（DESCRIPTION）。

1. 查看所有已安装的unit：

```
[root@study ~]# systemctl list-unit-files
```

显示结果包括unit文件名称和状态（enabled、disabled、mask、static等）。

1. 仅列出service类型的unit：

```
[root@study ~]# systemctl list-units --type=service --all
```

#### 原文：

上一小节谈到的是单一服务的启动/关闭/观察，以及相依服务要注销的功能。那系统上面有多少的服务存在呢？这个时候就得要通过 list-units 及 list-unit-files 来观察了！ 细部的用法如下：

```
[root@study ~]# systemctl [command] [--type=TYPE] [--all]
command:
    list-units      ：依据 unit 列出目前有启动的 unit。若加上 --all 才会列出没启动的。
    list-unit-files ：依据 /usr/lib/systemd/system/ 内的文件，将所有文件列表说明。
--type=TYPE：就是之前提到的 unit type，主要有 service, socket, target 等

范例一：列出系统上面有启动的 unit
[root@study ~]# systemctl
UNIT                      LOAD   ACTIVE SUB       DESCRIPTION
proc-sys-fs-binfmt_mis... loaded active waiting   Arbitrary Executable File Formats File System
sys-devices-pc...:0:1:... loaded active plugged   QEMU_HARDDISK
sys-devices-pc...0:1-0... loaded active plugged   QEMU_HARDDISK
sys-devices-pc...0:0-1... loaded active plugged   QEMU_DVD-ROM
.....（中间省略）.....
vsftpd.service            loaded active running   Vsftpd ftp daemon
.....（中间省略）.....
cups.socket               loaded failed failed    CUPS Printing Service Sockets
.....（中间省略）.....
LOAD   = Reflects whether the unit definition was properly loaded.
ACTIVE = The high-level unit activation state, i.e. generalization of SUB.
SUB    = The low-level unit activation state, values depend on unit type.

141 loaded units listed. Pass --all to see loaded but inactive units, too.
To show all installed unit files use 'systemctl list-unit-files'.
# 列出的项目中，主要的意义是：
# UNIT   ：项目的名称，包括各个 unit 的类别 （看扩展名）
# LOAD   ：开机时是否会被载入，默认 systemctl 显示的是有载入的项目而已喔！
# ACTIVE ：目前的状态，须与后续的 SUB 搭配！就是我们用 systemctl status 观察时，active 的项目！
# DESCRIPTION ：详细描述啰
# cups 比较有趣，因为刚刚被我们玩过，所以 ACTIVE 竟然是 failed 的喔！被玩死了！ ^_^
# 另外，systemctl 都不加参数，其实默认就是 list-units 的意思！

范例二：列出所有已经安装的 unit 有哪些？
[root@study ~]# systemctl list-unit-files
UNIT FILE                                   STATE
proc-sys-fs-binfmt_misc.automount           static
dev-hugepages.mount                         static
dev-mqueue.mount                            static
proc-fs-nfsd.mount                          static
.....（中间省略）.....
systemd-tmpfiles-clean.timer                static

336 unit files listed.
```

使用 systemctl list-unit-files 会将系统上所有的服务通通列出来～而不像 list-units 仅以 unit 分类作大致的说明。 至于 STATE 状态就是前两个小节谈到的开机是否会载入的那个状态项目啰！主要有 enabled / disabled / mask / static 等等。

假设我不想要知道这么多的 unit 项目，我只想要知道 service 这种类别的 daemon 而已，而且不论是否已经启动，通通要列出来！ 那该如何是好？

```
[root@study ~]# systemctl list-units --type=service --all
# 只剩下 *.service 的项目才会出现喔！

范例一：查询系统上是否有以 cpu 为名的服务？
[root@study ~]# systemctl list-units --type=service --all &#124; grep cpu
cpupower.service  loaded inactive dead    Configure CPU power related settings
# 确实有喔！可以改变 CPU 电源管理机制的服务哩！
```

### 17.2.3 通过 systemctl 管理不同的操作环境（target unit）

使用target管理不同的操作环境。

1. 获取当前target：

```
[root@study ~]# systemctl get-default
```

1. 设置默认操作模式：

```
[root@study ~]# systemctl set-default multi-user.target
[root@study ~]# systemctl get-default
```

1. 切换操作环境：

```
[root@study ~]# systemctl isolate multi-user.target
```

恢复图形界面：

```
[root@study ~]# systemctl isolate graphical.target
```

简化操作模式切换的指令：

- 关机：`systemctl poweroff`
- 重启：`systemctl reboot`
- 暂停：`systemctl suspend`
- 休眠：`systemctl hibernate`
- 救援模式：`systemctl rescue`
- 紧急救援模式：`systemctl emergency`

#### 原文：

通过上个小节我们知道系统上所有的 systemd 的 unit 观察的方式，那么可否列出跟操作界面比较有关的 target 项目呢？ 很简单啊！就这样搞一下：

```
[root@study ~]# systemctl list-units --type=target --all
UNIT                   LOAD   ACTIVE   SUB    DESCRIPTION
basic.target           loaded active   active Basic System
cryptsetup.target      loaded active   active Encrypted Volumes
emergency.target       loaded inactive dead   Emergency Mode
final.target           loaded inactive dead   Final Step
getty.target           loaded active   active Login Prompts
graphical.target       loaded active   active Graphical Interface
local-fs-pre.target    loaded active   active Local File Systems （Pre）
local-fs.target        loaded active   active Local File Systems
multi-user.target      loaded active   active Multi-User System
network-online.target  loaded inactive dead   Network is Online
network.target         loaded active   active Network
nss-user-lookup.target loaded inactive dead   User and Group Name Lookups
paths.target           loaded active   active Paths
remote-fs-pre.target   loaded active   active Remote File Systems （Pre）
remote-fs.target       loaded active   active Remote File Systems
rescue.target          loaded inactive dead   Rescue Mode
shutdown.target        loaded inactive dead   Shutdown
slices.target          loaded active   active Slices
sockets.target         loaded active   active Sockets
sound.target           loaded active   active Sound Card
swap.target            loaded active   active Swap
sysinit.target         loaded active   active System Initialization
syslog.target          not-found inactive dead   syslog.target
time-sync.target       loaded inactive dead   System Time Synchronized
timers.target          loaded active   active Timers
umount.target          loaded inactive dead   Unmount All Filesystems

LOAD   = Reflects whether the unit definition was properly loaded.
ACTIVE = The high-level unit activation state, i.e. generalization of SUB.
SUB    = The low-level unit activation state, values depend on unit type.

26 loaded units listed.
To show all installed unit files use 'systemctl list-unit-files'.
```

喔！在我们的 CentOS 7.1 的默认情况下，就有 26 个 target unit 耶！而跟操作界面相关性比较高的 target 主要有下面几个：

- graphical.target：就是文字加上图形界面，这个项目已经包含了下面的 multi-user.target 项目！
- multi-user.target：纯文本模式！
- rescue.target：在无法使用 root 登陆的情况下，systemd 在开机时会多加一个额外的暂时系统，与你原本的系统无关。这时你可以取得 root 的权限来维护你的系统。 但是这是额外系统，因此可能需要动到 chroot 的方式来取得你原有的系统喔！再后续的章节我们再来谈！
- emergency.target：紧急处理系统的错误，还是需要使用 root 登陆的情况，在无法使用 rescue.target 时，可以尝试使用这种模式！
- shutdown.target：就是关机的流程。
- getty.target：可以设置你需要几个 tty 之类的，如果想要降低 tty 的项目，可以修改这个东西的配置文件！

正常的模式是 multi-user.target 以及 graphical.target 两个，救援方面的模式主要是 rescue.target 以及更严重的 emergency.target。 如果要修改可提供登陆的 tty 数量，则修改 getty.target 项目。基本上，我们最常使用的当然就是 multi-user 以及 graphical 啰！ 那么我如何知道目前的模式是哪一种？又得要如何修改呢？下面来玩一玩吧！

```
[root@study ~]# systemctl [command] [unit.target]
选项与参数：
command:
    get-default ：取得目前的 target 
    set-default ：设置后面接的 target 成为默认的操作模式
    isolate     ：切换到后面接的模式

范例一：我们的测试机器默认是图形界面，先观察是否真为图形模式，再将默认模式转为文字界面
[root@study ~]# systemctl get-default 
graphical.target  # 果然是图形界面喔！

[root@study ~]# systemctl set-default multi-user.target
[root@study ~]# systemctl get-default 
multi-user.target

范例二：在不重新开机的情况下，将目前的操作环境改为纯文本模式，关掉图形界面
[root@study ~]# systemctl isolate multi-user.target

范例三：若需要重新取得图形界面呢？
[root@study ~]# systemctl isolate graphical.target
```

要注意，改变 graphical.target 以及 multi-user.target 是通过 isolate 来处理的！鸟哥刚刚接触到 systemd 的时候，在 multi-user.target 环境下转成 graphical.target 时， 可以通过 systemctl start graphical.target 喔！然后鸟哥就以为关闭图形界面即可回到 multi-user.target 的！但使用 systemctl stop graphical.target 却完全不理鸟哥～这才发现错了...在 service 部份用 start/stop/restart 才对，在 target 项目则请使用 isolate （隔离不同的操作模式） 才对！

在正常的切换情况下，使用上述 isolate 的方式即可。不过为了方便起见， systemd 也提供了数个简单的指令给我们切换操作模式之用喔！ 大致上如下所示：

```
[root@study ~]# systemctl poweroff  系统关机
[root@study ~]# systemctl reboot    重新开机
[root@study ~]# systemctl suspend   进入暂停模式
[root@study ~]# systemctl hibernate 进入休眠模式
[root@study ~]# systemctl rescue    强制进入救援模式
[root@study ~]# systemctl emergency 强制进入紧急救援模式
```

关机、重新开机、救援与紧急模式这没啥问题，那么什么是暂停与休眠模式呢？

- suspend：暂停模式会将系统的状态数据保存到内存中，然后关闭掉大部分的系统硬件，当然，并没有实际关机喔！ 当使用者按下唤醒机器的按钮，系统数据会重内存中回复，然后重新驱动被大部分关闭的硬件，就开始正常运行！唤醒的速度较快。
- hibernate：休眠模式则是将系统状态保存到硬盘当中，保存完毕后，将计算机关机。当使用者尝试唤醒系统时，系统会开始正常运行， 然后将保存在硬盘中的系统状态恢复回来。因为数据是由硬盘读出，因此唤醒的性能与你的硬盘速度有关。

### 17.2.4 通过 systemctl 分析各服务之间的相依性

使用`list-dependencies`命令分析服务之间的相依性。

1. 列出当前target环境下的依赖：

```
[root@study ~]# systemctl list-dependencies
```

1. 反向追踪某个unit的依赖：

```
[root@study ~]# systemctl list-dependencies --reverse
```

#### 原文：

我们在本章一开始谈到 systemd 的时候就有谈到相依性的问题克服，那么，如何追踪某一个 unit 的相依性呢？ 举例来说好了，我们怎么知道 graphical.target 会用到 multi-user.target 呢？那 graphical.target 下面还有哪些东西呢？ 下面我们就来谈一谈：

```
[root@study ~]# systemctl list-dependencies [unit] [--reverse]
选项与参数：
--reverse ：反向追踪谁使用这个 unit 的意思！

范例一：列出目前的 target 环境下，用到什么特别的 unit 
[root@study ~]# systemctl get-default
multi-user.target

[root@study ~]# systemctl list-dependencies
default.target
├─abrt-ccpp.service
├─abrt-oops.service
├─vsftpd.service
├─basic.target
│ ├─alsa-restore.service
│ ├─alsa-state.service
.....（中间省略）.....
│ ├─sockets.target
│ │ ├─avahi-daemon.socket
│ │ ├─dbus.socket
.....（中间省略）.....
│ ├─sysinit.target
│ │ ├─dev-hugepages.mount
│ │ ├─dev-mqueue.mount
.....（中间省略）.....
│ └─timers.target
│   └─systemd-tmpfiles-clean.timer
├─getty.target
│ └─[email protected]
└─remote-fs.target
```

因为我们前一小节的练习将默认的操作模式变成 multi-user.target 了，因此这边使用 list-dependencies 时，所列出的 default.target 其实是 multi-user.target 的内容啦！根据线条连线的流程，我们也能够知道， multi-user.target 其实还会用到 basic.target + getty.target + remote-fs.target 三大项目， 而 basic.target 又用到了 sockets.target + sysinit.target + timers.target... 等一堆～所以啰，从这边就能够清楚的查询到每种 target 模式下面还有的相依模式。 那么如果要查出谁会用到 multi-user.target 呢？就这么作！

```
[root@study ~]# systemctl list-dependencies --reverse
default.target
└─graphical.target
```

reverse 本来就是反向的意思，所以加上这个选项，代表“谁还会用到我的服务”的意思～所以看得出来， multi-user.target 主要是被 graphical.target 所使用喔！ 好～那再来，graphical.target 又使用了多少的服务呢？可以这样看：

```
[root@study ~]# systemctl list-dependencies graphical.target
graphical.target
├─accounts-daemon.service
├─gdm.service
├─network.service
├─rtkit-daemon.service
├─systemd-update-utmp-runlevel.service
└─multi-user.target
  ├─abrt-ccpp.service
  ├─abrt-oops.service
.....（下面省略）.....
```

所以可以看得出来，graphical.target 就是在 multi-user.target 下面再加上 accounts-daemon, gdm, network, rtkit-deamon, systemd-update-utmp-runlevel 等服务而已！ 这样会看了吗？了解 daemon 之间的相关性也是很重要的喔！出问题时，可以找到正确的服务相依流程！



### 17.2.5 与 systemd的daemon运行过程相关的目录简介

相关目录：

- `/usr/lib/systemd/system/`：系统默认的启动脚本配置文件。
- `/run/systemd/system/`：系统运行过程中产生的服务脚本。
- `/etc/systemd/system/`：管理员创建的执行脚本，优先级最高。
- `/etc/sysconfig/*`：初始化选项设置。
- `/var/lib/`：服务产生的数据存放目录。
- `/run/`：daemon的暂存文件。

查看本机服务需求的socket file：

```
[root@study ~]# systemctl list-sockets
```

#### 原文：

我们在前几小节曾经谈过比较重要的 systemd 启动脚本配置文件在 /usr/lib/systemd/system/, /etc/systemd/system/ 目录下，那还有哪些目录跟系统的 daemon 运行有关呢？ 基本上是这样的：

- /usr/lib/systemd/system/： 使用 CentOS 官方提供的软件安装后，默认的启动脚本配置文件都放在这里，这里的数据尽量不要修改～ 要修改时，请到 /etc/systemd/system 下面修改较佳！
- /run/systemd/system/： 系统执行过程中所产生的服务脚本，这些脚本的优先序要比 /usr/lib/systemd/system/ 高！
- /etc/systemd/system/： 管理员依据主机系统的需求所创建的执行脚本，其实这个目录有点像以前 /etc/rc.d/rc5.d/Sxx 之类的功能！执行优先序又比 /run/systemd/system/ 高喔！
- /etc/sysconfig/*： 几乎所有的服务都会将初始化的一些选项设置写入到这个目录下，举例来说，mandb 所要更新的 man page 索引中，需要加入的参数就写入到此目录下的 man-db 当中喔！而网络的设置则写在 /etc/sysconfig/network-scripts/ 这个目录内。所以，这个目录内的文件也是挺重要的；
- /var/lib/： 一些会产生数据的服务都会将他的数据写入到 /var/lib/ 目录中。举例来说，数据库管理系统 Mariadb 的数据库默认就是写入 /var/lib/mysql/ 这个目录下啦！
- /run/： 放置了好多 daemon 的暂存盘，包括 lock file 以及 PID file 等等。

我们知道 systemd 里头有很多的本机会用到的 socket 服务，里头可能会产生很多的 socket file ～那你怎么知道这些 socket file 放置在哪里呢？ 很简单！还是通过 systemctl 来管理！

```
[root@study ~]# systemctl list-sockets
LISTEN                          UNIT                         ACTIVATES
/dev/initctl                    systemd-initctl.socket       systemd-initctl.service
/dev/log                        systemd-journald.socket      systemd-journald.service
/run/dmeventd-client            dm-event.socket              dm-event.service
/run/dmeventd-server            dm-event.socket              dm-event.service
/run/lvm/lvmetad.socket         lvm2-lvmetad.socket          lvm2-lvmetad.service
/run/systemd/journal/socket     systemd-journald.socket      systemd-journald.service
/run/systemd/journal/stdout     systemd-journald.socket      systemd-journald.service
/run/systemd/shutdownd          systemd-shutdownd.socket     systemd-shutdownd.service
/run/udev/control               systemd-udevd-control.socket systemd-udevd.service
/var/run/avahi-daemon/socket    avahi-daemon.socket          avahi-daemon.service
/var/run/cups/cups.sock         cups.socket                  cups.service
/var/run/dbus/system_bus_socket dbus.socket                  dbus.service
/var/run/rpcbind.sock           rpcbind.socket               rpcbind.service
@ISCSIADM_ABSTRACT_NAMESPACE    iscsid.socket                iscsid.service
@ISCSID_UIP_ABSTRACT_NAMESPACE  iscsiuio.socket              iscsiuio.service
kobject-uevent 1                systemd-udevd-kernel.socket  systemd-udevd.service

16 sockets listed.
Pass --all to see loaded but inactive sockets, too.
```

这样很清楚的就能够知道正在监听本机服务需求的 socket file 所在的文件名位置啰！

- 网络服务与端口对应简介

从[第十六章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)与前一小节对服务的说明后，你应该要知道的是， 系统所有的功能都是某些程序所提供的，而程序则是通过触发程序而产生的。同样的，系统提供的网络服务当然也是这样的！ 只是由于网络牵涉到 TCP/IP 的概念，所以显的比较复杂一些就是了。

玩过网际网络 （Internet） 的朋友应该知道 IP 这玩意儿，大家都说 IP 就是代表你的主机在网际网络上面的“门牌号码”。 但是你的主机总是可以提供非常多的网络服务而不止一项功能而已，但我们仅有一个 IP 呢！当用户端连线过来我们的主机时， 我们主机是如何分辨不同的服务要求呢？那就是通过埠号 （port number） 啦！埠号简单的想像，他就是你家门牌上面的第几层楼！ 这个 IP 与 port 就是网际网络连线的最重要机制之一啰。我们拿下面的网址来说明：

- http://ftp.ksu.edu.tw/
- [ftp://ftp.ksu.edu.tw/](ftp://ftp.ksu.edu.tw/)

有没有发现，两个网址都是指向 ftp.ksu.edu.tw 这个昆山科大的 FTP 网站，但是浏览器上面显示的结果却是不一样的？ 是啊！这是因为我们指向不同的服务嘛！一个是 http 这个 WWW 的服务，一个则是 ftp 这个文件传输服务，当然显示的结果就不同了。

![port 与 daemon 的对应](image/port_daemon.gif)图17.2.1、port 与 daemon 的对应

事实上，为了统一整个网际网络的埠号对应服务的功能，好让所有的主机都能够使用相同的机制来提供服务与要求服务， 所以就有了“通讯协定”这玩意儿。也就是说，有些约定俗成的服务都放置在同一个埠号上面啦！举例来说， 网址列上面的 http 会让浏览器向 WWW 服务器的 80 埠号进行连线的要求！而 WWW 服务器也会将 httpd 这个软件启动在 port 80， 这样两者才能够达成连线的！

嗯！那么想一想，系统上面有没有什么设置可以让服务与埠号对应在一起呢？那就是 /etc/services 啦！

```
[root@study ~]# cat /etc/services
....（前面省略）....
ftp             21/tcp
ftp             21/udp          fsp fspd
ssh             22/tcp                          # The Secure Shell （SSH） Protocol
ssh             22/udp                          # The Secure Shell （SSH） Protocol
....（中间省略）....
http            80/tcp          www www-http    # WorldWideWeb HTTP
http            80/udp          www www-http    # HyperText Transfer Protocol
....（下面省略）....
# 这个文件的内容是以下面的方式来编排的：
# &lt;daemon name&gt;   &lt;port/封包协定&gt;   &lt;该服务的说明&gt;
```

像上面说的是，第一栏为 daemon 的名称、第二栏为该 daemon 所使用的埠号与网络数据封包协定， 封包协定主要为可靠连线的 TCP 封包以及较快速但为非连线导向的 UDP 封包。 举个例子说，那个远端连线机制使用的是 ssh 这个服务，而这个服务的使用的埠号为 22 ！就是这样啊！

![鸟哥的图示](image/vbird_face-17228312936703.gif)

**Tips** 请特别注意！虽然有的时候你可以借由修改 /etc/services 来更改一个服务的埠号，不过并不建议如此做， 因为很有可能会造成一些协定的错误情况！这里特此说明一番呦！（除非你要架设一个地下网站，否则的话，使用 /etc/services 原先的设置就好啦！）



### 17.2.6 关闭网络服务

观察本机网络端口：

```
[root@study ~]# netstat -tlunp
```

关闭avahi-daemon服务及其socket：

```
[root@study ~]# systemctl stop avahi-daemon.service
[root@study ~]# systemctl stop avahi-daemon.socket
[root@study ~]# systemctl disable avahi-daemon.service avahi-daemon.socket
[root@study ~]# netstat -tlunp
```

总结，通过systemctl可以方便地管理systemd服务，观察服务状态，调整服务的启动和依赖设置，使系统服务管理更加集中和高效。

#### 原文：

当你第一次使用 systemctl 去观察本机服务器启动的服务时，不知道有没有吓一跳呢？怎么随随便便 CentOS 7.x 就给我启动了几乎 100 多个以上的 daemon？ 会不会有事啊？没关系啦！因为 systemd 将许多原本不被列为 daemon 的程序都纳入到 systemd 自己的管辖监测范围内，因此就多了很多 daemon 存在！ 那些大部分都属于 Linux 系统基础运行所需要的环境，没有什么特别需求的话，最好都不要更动啦！除非你自己知道自己需要什么。

除了本机服务之外，其实你一定要观察的，反而是网络服务喔！虽然网络服务默认有 SELinux 管理，不过，在鸟哥的立场上， 我还是建议非必要的网络服务就关闭他！那么什么是网络服务呢？基本上，会产生一个网络监听端口 （port） 的程序，你就可以称他是个网络服务了！ 那么如何观察网络端口？就这样追踪啊！

```
[root@study ~]# netstat -tlunp
Proto Recv-Q Send-Q Local Address   Foreign Address  State    PID/Program name
tcp        0      0 0.0.0.0:22      0.0.0.0:*        LISTEN   1340/sshd
tcp        0      0 127.0.0.1:25    0.0.0.0:*        LISTEN   2387/master
tcp6       0      0 :::555          :::*             LISTEN   29113/vsftpd
tcp6       0      0 :::22           :::*             LISTEN   1340/sshd
tcp6       0      0 ::1:25          :::*             LISTEN   2387/master
udp        0      0 0.0.0.0:5353    0.0.0.0:*                 750/avahi-daemon: r
udp        0      0 0.0.0.0:36540   0.0.0.0:*                 750/avahi-daemon: r
```

如上表所示，我们的系统上至少开了 22, 25, 555, 5353, 36540 这几个端口～而其中 5353, 36540 是由 avahi-daemon 这个东西所启动的！ 接下来我们使用 systemctl 去观察一下，到底有没有 avahi-daemon 为开头的服务呢？

```
[root@study ~]# systemctl list-units --all &#124; grep avahi-daemon
avahi-daemon.service   loaded active   running   Avahi mDNS/DNS-SD Stack
avahi-daemon.socket    loaded active   running   Avahi mDNS/DNS-SD Stack Activation Socket
```

通过追查，知道这个 avahi-daemon 的目的是在区域网络进行类似网芳的搜寻，因此这个服务可以协助你在区网内随时了解随插即用的设备！ 包括笔记本电脑等，只要连上你的区网，你就能够知道谁进来了。问题是，你可能不要这个协定啊！所以，那就关闭他吧！

```
[root@study ~]# systemctl stop avahi-daemon.service
[root@study ~]# systemctl stop avahi-daemon.socket
[root@study ~]# systemctl disable avahi-daemon.service avahi-daemon.socket
[root@study ~]# netstat -tlunp
Proto Recv-Q Send-Q Local Address   Foreign Address  State    PID/Program name
tcp        0      0 0.0.0.0:22      0.0.0.0:*        LISTEN   1340/sshd
tcp        0      0 127.0.0.1:25    0.0.0.0:*        LISTEN   2387/master
tcp6       0      0 :::555          :::*             LISTEN   29113/vsftpd
tcp6       0      0 :::22           :::*             LISTEN   1340/sshd
tcp6       0      0 ::1:25          :::*             LISTEN   2387/master
```

一般来说，你的本机服务器至少需要 25 号端口，而 22 号端口则最好加上防火墙来管理远端连线登陆比较妥当～因此，上面的端口中， 除了 555 是我们上一章因为测试而产生的之外，这样的系统能够被爬墙的机会已经少很多了！ ^_^！OK！现在如果你的系统里面有一堆网络端口在监听， 而你根本不知道那是干麻用的，鸟哥建议你，现在就通过上面的方式，关闭他吧！



## 17.3 systemctl 针对 service 类型的配置文件

在以前的System V中，如果要创建系统服务，需要在 `/etc/init.d/` 目录下编写对应的Bash脚本。而在systemd环境下，设置相关服务启动则需要新的方式。以下是详细的配置和设置方法。

### 17.3.1 systemctl 配置文件相关目录简介

systemd 的配置文件主要存放在 `/usr/lib/systemd/system/` 目录内。但官方建议不要修改该目录下的文件，修改文件应放在 `/etc/systemd/system/` 目录下。例如，修改 `vsftpd.service` 可以放置在以下目录：

- `/usr/lib/systemd/system/vsftpd.service`：官方默认配置文件；
- `/etc/systemd/system/vsftpd.service.d/custom.conf`：在 `/etc/systemd/system` 下创建与配置文件同名的目录并加上 `.d` 扩展名，在该目录下创建 `.conf` 文件，这些文件会累加设置进入官方配置文件；
- `/etc/systemd/system/vsftpd.service.wants/*`：设置相依服务的链接文件，表示启动该服务后，建议启动此目录下的服务；
- `/etc/systemd/system/vsftpd.service.requires/*`：设置相依服务的链接文件，表示启动该服务前，需要启动此目录下的服务。

虽然官方建议在 `/etc/systemd/system/` 下进行客制化设置，但也可以直接修改 `/usr/lib/systemd/system/` 下的原始配置文件。

#### 原文：

现在我们知道服务的管理是通过 systemd，而 systemd 的配置文件大部分放置于 /usr/lib/systemd/system/ 目录内。但是 Red Hat 官方文件指出， 该目录的文件主要是原本软件所提供的设置，建议不要修改！而要修改的位置应该放置于 /etc/systemd/system/ 目录内。举例来说，如果你想要额外修改 vsftpd.service 的话， 他们建议要放置到哪些地方呢？

- /usr/lib/systemd/system/vsftpd.service：官方释出的默认配置文件；
- /etc/systemd/system/vsftpd.service.d/custom.conf：在 /etc/systemd/system 下面创建与配置文件相同文件名的目录，但是要加上 .d 的扩展名。然后在该目录下创建配置文件即可。另外，配置文件最好附文件名取名为 .conf 较佳！ 在这个目录下的文件会“累加其他设置”进入 /usr/lib/systemd/system/vsftpd.service 内喔！
- /etc/systemd/system/vsftpd.service.wants/*：此目录内的文件为链接文件，设置相依服务的链接。意思是启动了 vsftpd.service 之后，最好再加上这目录下面建议的服务。
- /etc/systemd/system/vsftpd.service.requires/*：此目录内的文件为链接文件，设置相依服务的链接。意思是在启动 vsftpd.service 之前，需要事先启动哪些服务的意思。

基本上，在配置文件里面你都可以自由设置相依服务的检查，并且设置加入到哪些 target 里头去。但是如果是已经存在的配置文件，或者是官方提供的配置文件， Red Hat 是建议你不要修改原设置，而是到上面提到的几个目录去进行额外的客制化设置比较好！当然，这见仁见智～如果你硬要修改原始的 /usr/lib/systemd/system 下面的配置文件，那也是 OK 没问题的！并且也能够减少许多配置文件的增加～鸟哥自己认为，这样也不错！反正，就完全是个人喜好啰～



### 17.3.2 systemctl 配置文件的设置项目简介

配置文件内容可以分为三个部分：[Unit]、[Service]、[Install]。以下是对各部分内容的详细说明：

#### [Unit] 部分

| 设置参数      | 参数意义说明                   |
| ------------- | ------------------------------ |
| Description   | 服务的简要说明                 |
| Documentation | 提供管理员查询的文件链接       |
| After         | 此服务应在其他服务启动后启动   |
| Before        | 此服务应在其他服务启动前启动   |
| Requires      | 启动此服务前需先启动的其他服务 |
| Wants         | 启动此服务后建议启动的其他服务 |
| Conflicts     | 与此服务冲突的其他服务         |

#### [Service] 部分

| 设置参数        | 参数意义说明                                     |
| --------------- | ------------------------------------------------ |
| Type            | 服务启动方式（simple、forking、oneshot等）       |
| EnvironmentFile | 指定环境配置文件                                 |
| ExecStart       | 启动服务的实际指令                               |
| ExecStop        | 停止服务时的指令                                 |
| ExecReload      | 重新加载服务时的指令                             |
| Restart         | 当服务终止后是否重新启动                         |
| RemainAfterExit | 服务所属程序终止后是否重新启动                   |
| TimeoutSec      | 启动或关闭服务的超时时间                         |
| KillMode        | 终止服务时的模式（process、control-group、none） |
| RestartSec      | 服务重新启动前的等待时间                         |

#### [Install] 部分

| 设置参数 | 参数意义说明                   |
| -------- | ------------------------------ |
| WantedBy | 此服务附属于哪个target         |
| Also     | 启用此服务时，同时启用其他服务 |
| Alias    | 创建服务链接的别名             |

#### 原文：

了解了配置文件的相关目录与文件之后，再来，当然得要了解一下配置文件本身的内容了！让我们先来瞧一瞧 sshd.service 的内容好了！ 原本想拿 vsftpd.service 来讲解，不过该文件的内容比较阳春，还是看一下设置项目多一些的 sshd.service 好了！

```
[root@study ~]# cat /usr/lib/systemd/system/sshd.service
[Unit]           # 这个项目与此 unit 的解释、执行服务相依性有关
Description=OpenSSH server daemon
After=network.target sshd-keygen.service
Wants=sshd-keygen.service

[Service]        # 这个项目与实际执行的指令参数有关
EnvironmentFile=/etc/sysconfig/sshd
ExecStart=/usr/sbin/sshd -D $OPTIONS
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
RestartSec=42s

[Install]        # 这个项目说明此 unit 要挂载哪个 target 下面
WantedBy=multi-user.target
```

分析上面的配置文件，我们大概能够将整个设置分为三个部份，就是：

- [Unit]： unit 本身的说明，以及与其他相依 daemon 的设置，包括在什么服务之后才启动此 unit 之类的设置值；
- [Service], [Socket], [Timer], [Mount], [Path]..：不同的 unit type 就得要使用相对应的设置项目。我们拿的是 sshd.service 来当范本，所以这边就使用 [Service] 来设置。 这个项目内主要在规范服务启动的脚本、环境配置文件文件名、重新启动的方式等等。
- [Install]：这个项目就是将此 unit 安装到哪个 target 里面去的意思！

至于配置文件内有些设置规则还是得要说明一下：

- 设置项目通常是可以重复的，例如我可以重复设置两个 After 在配置文件中，不过，后面的设置会取代前面的喔！因此，如果你想要将设置值归零， 可以使用类似“ After= ”的设置，亦即该项目的等号后面什么都没有，就将该设置归零了 （reset）。
- 如果设置参数需要有“是/否”的项目 （布林值, boolean），你可以使用 1, yes, true, on 代表启动，用 0, no, false, off 代表关闭！随你喜好选择啰！
- 空白行、开头为 # 或 ; 的那一行，都代表注解！

每个部份里面还有很多的设置细项，我们使用一个简单的表格来说明每个项目好了！

| [Unit] 部份   |                                                              |
| ------------- | ------------------------------------------------------------ |
| 设置参数      | 参数意义说明                                                 |
| Description   | 就是当我们使用 systemctl list-units 时，会输出给管理员看的简易说明！当然，使用 systemctl status 输出的此服务的说明，也是这个项目！ |
| Documentation | 这个项目在提供管理员能够进行进一步的文件查询的功能！提供的文件可以是如下的数据：`Documentation=http://www....` `Documentation=man:sshd（8）` `Documentation=file:/etc/ssh/sshd_config` |
| After         | 说明此 unit 是在哪个 daemon 启动之后才启动的意思！基本上仅是说明服务启动的顺序而已，并没有强制要求里头的服务一定要启动后此 unit 才能启动。 以 sshd.service 的内容为例，该文件提到 After 后面有 network.target 以及 sshd-keygen.service，但是若这两个 unit 没有启动而强制启动 sshd.service 的话， 那么 sshd.service 应该还是能够启动的！这与 Requires 的设置是有差异的喔！ |
| Before        | 与 After 的意义相反，是在什么服务启动前最好启动这个服务的意思。不过这仅是规范服务启动的顺序，并非强制要求的意思。 |
| Requires      | 明确的定义此 unit 需要在哪个 daemon 启动后才能够启动！就是设置相依服务啦！如果在此项设置的前导服务没有启动，那么此 unit 就不会被启动！ |
| Wants         | 与 Requires 刚好相反，规范的是这个 unit 之后最好还要启动什么服务比较好的意思！不过，并没有明确的规范就是了！主要的目的是希望创建让使用者比较好操作的环境。 因此，这个 Wants 后面接的服务如果没有启动，其实不会影响到这个 unit 本身！ |
| Conflicts     | 代表冲突的服务！亦即这个项目后面接的服务如果有启动，那么我们这个 unit 本身就不能启动！我们 unit 有启动，则此项目后的服务就不能启动！ 反正就是冲突性的检查啦！ |

接下来了解一下在 [Service] 当中有哪些项目可以使用！

| [Service] 部份  |                                                              |
| --------------- | ------------------------------------------------------------ |
| 设置参数        | 参数意义说明                                                 |
| Type            | 说明这个 daemon 启动的方式，会影响到 ExecStart 喔！一般来说，有下面几种类型 simple：默认值，这个 daemon 主要由 ExecStart 接的指令串来启动，启动后常驻于内存中。forking：由 ExecStart 启动的程序通过 spawns 延伸出其他子程序来作为此 daemon 的主要服务。原生的父程序在启动结束后就会终止运行。 传统的 unit 服务大多属于这种项目，例如 httpd 这个 WWW 服务，当 httpd 的程序因为运行过久因此即将终结了，则 systemd 会再重新生出另一个子程序持续运行后， 再将父程序删除。据说这样的性能比较好！！oneshot：与 simple 类似，不过这个程序在工作完毕后就结束了，不会常驻在内存中。dbus：与 simple 类似，但这个 daemon 必须要在取得一个 D-Bus 的名称后，才会继续运行！因此设置这个项目时，通常也要设置 BusName= 才行！idle：与 simple 类似，意思是，要执行这个 daemon 必须要所有的工作都顺利执行完毕后才会执行。这类的 daemon 通常是开机到最后才执行即可的服务！比较重要的项目大概是 simple, forking 与 oneshot 了！毕竟很多服务需要子程序 （forking），而有更多的动作只需要在开机的时候执行一次（oneshot），例如文件系统的检查与挂载啊等等的。 |
| EnvironmentFile | 可以指定启动脚本的环境配置文件！例如 sshd.service 的配置文件写入到 /etc/sysconfig/sshd 当中！你也可以使用 Environment= 后面接多个不同的 Shell 变量来给予设置！ |
| ExecStart       | 就是实际执行此 daemon 的指令或脚本程序。你也可以使用 ExecStartPre （之前） 以及 ExecStartPost （之后） 两个设置项目来在实际启动服务前，进行额外的指令行为。 但是你得要特别注意的是，指令串仅接受“指令 参数 参数...”的格式，不能接受 <, >, >>, \|, & 等特殊字符，很多的 bash 语法也不支持喔！ 所以，要使用这些特殊的字符时，最好直接写入到指令脚本里面去！不过，上述的语法也不是完全不能用，亦即，若要支持比较完整的 bash 语法，那你得要使用 Type=oneshot 才行喔！ 其他的 Type 才不能支持这些字符。 |
| ExecStop        | 与 systemctl stop 的执行有关，关闭此服务时所进行的指令。     |
| ExecReload      | 与 systemctl reload 有关的指令行为                           |
| Restart         | 当设置 Restart=1 时，则当此 daemon 服务终止后，会再次的启动此服务。举例来说，如果你在 tty2 使用文字界面登陆，操作完毕后登出，基本上，这个时候 tty2 就已经结束服务了。 但是你会看到屏幕又立刻产生一个新的 tty2 的登陆画面等待你的登陆！那就是 Restart 的功能！除非使用 systemctl 强制将此服务关闭，否则这个服务会源源不绝的一直重复产生！ |
| RemainAfterExit | 当设置为 RemainAfterExit=1 时，则当这个 daemon 所属的所有程序都终止之后，此服务会再尝试启动。这对于 Type=oneshot 的服务很有帮助！ |
| TimeoutSec      | 若这个服务在启动或者是关闭时，因为某些缘故导致无法顺利“正常启动或正常结束”的情况下，则我们要等多久才进入“强制结束”的状态！ |
| KillMode        | 可以是 process, control-group, none 的其中一种，如果是 process 则 daemon 终止时，只会终止主要的程序 （ExecStart 接的后面那串指令），如果是 control-group 时， 则由此 daemon 所产生的其他 control-group 的程序，也都会被关闭。如果是 none 的话，则没有程序会被关闭喔！ |
| RestartSec      | 与 Restart 有点相关性，如果这个服务被关闭，然后需要重新启动时，大概要 sleep 多少时间再重新启动的意思。默认是 100ms （毫秒）。 |

最后，再来看看那么 Install 内还有哪些项目可用？

| [Install] 部份 |                                                              |
| -------------- | ------------------------------------------------------------ |
| 设置参数       | 参数意义说明                                                 |
| WantedBy       | 这个设置后面接的大部分是 *.target unit ！意思是，这个 unit 本身是附挂在哪一个 target unit 下面的！一般来说，大多的服务性质的 unit 都是附挂在 multi-user.target 下面！ |
| Also           | 当目前这个 unit 本身被 enable 时，Also 后面接的 unit 也请 enable 的意思！也就是具有相依性的服务可以写在这里呢！ |
| Alias          | 进行一个链接的别名的意思！当 systemctl enable 相关的服务时，则此服务会进行链接文件的创建！以 multi-user.target 为例，这个家伙是用来作为默认操作环境 default.target 的规划， 因此当你设置用成 default.target 时，这个 /etc/systemd/system/default.target 就会链接到 /usr/lib/systemd/system/multi-user.target 啰！ |

大致的项目就有这些，接下来让我们根据上面这些数据来进行一些简易的操作吧！



### 17.3.3 两个 vsftpd 运行的实例

假设需要同时运行两个vsftpd实例，分别监听端口21和555，以下是具体设置方法：

1. 创建并修改配置文件：

```
[root@study ~]# cp /etc/vsftpd/vsftpd.conf /etc/vsftpd/vsftpd2.conf
[root@study ~]# vim /etc/vsftpd/vsftpd2.conf
# 修改配置文件中的端口号
listen_port=555
```

1. 复制并修改服务启动脚本：

```
[root@study ~]# cp /usr/lib/systemd/system/vsftpd.service /etc/systemd/system/vsftpd2.service
[root@study ~]# vim /etc/systemd/system/vsftpd2.service
[Unit]
Description=Vsftpd second ftp daemon
After=network.target

[Service]
Type=forking
ExecStart=/usr/sbin/vsftpd /etc/vsftpd/vsftpd2.conf

[Install]
WantedBy=multi-user.target
```

1. 重新加载systemd并启动服务：

```
[root@study ~]# systemctl daemon-reload
[root@study ~]# systemctl restart vsftpd.service vsftpd2.service
[root@study ~]# systemctl enable vsftpd.service vsftpd2.service
[root@study ~]# systemctl status vsftpd.service vsftpd2.service
```

#### 原文：

我们在上一章将 vsftpd 的 port 改成 555 号了。不过，因为某些原因，所以你可能需要使用到两个端口，分别是正常的 21 以及特殊的 555 ！ 这两个 port 都启用的情况下，你可能就得要使用到两个配置文件以及两个启动脚本设置了！现在假设是这样：

- 默认的 port 21：使用 /etc/vsftpd/vsftpd.conf 配置文件，以及 /usr/lib/systemd/system/vsftpd.service 设置脚本；
- 特殊的 port 555：使用 /etc/vsftpd/vsftpd2.conf 配置文件，以及 /etc/systemd/system/vsftpd2.service 设置脚本。

我们可以这样作：

```
# 1\. 先创建好所需要的配置文件
[root@study ~]# cd /etc/vsftpd
[root@study vsftpd]# cp vsftpd.conf vsftpd2.conf
[root@study vsftpd]# vim vsftpd.conf
#listen_port=555

[root@study vsftpd]# diff vsftpd.conf vsftpd2.conf
128c128
&lt; #listen_port=555
---
&gt; listen_port=555
# 注意这两个配置文件的差别喔！只有这一行不同而已！

# 2\. 开始处理启动脚本设置
[root@study vsftpd]# cd /etc/systemd/system
[root@study system]# cp /usr/lib/systemd/system/vsftpd.service vsftpd2.service
[root@study system]# vim vsftpd2.service
[Unit]
Description=Vsftpd second ftp daemon
After=network.target

[Service]
Type=forking
ExecStart=/usr/sbin/vsftpd /etc/vsftpd/vsftpd2.conf

[Install]
WantedBy=multi-user.target
# 重点在改了 vsftpd2.conf 这个配置文件喔！

# 3\. 重新载入 systemd 的脚本配置文件内容
[root@study system]# systemctl daemon-reload
[root@study system]# systemctl list-unit-files --all &#124; grep vsftpd
vsftpd.service                              enabled
vsftpd2.service                             disabled
[email protected]                             disabled
vsftpd.target                               disabled

[root@study system]# systemctl status vsftpd2.service
vsftpd2.service - Vsftpd second ftp daemon
   Loaded: loaded （/etc/systemd/system/vsftpd2.service; disabled）
   Active: inactive （dead）

[root@study system]# systemctl restart vsftpd.service vsftpd2.service
[root@study system]# systemctl enable  vsftpd.service vsftpd2.service
[root@study system]# systemctl status  vsftpd.service vsftpd2.service
vsftpd.service - Vsftpd ftp daemon
   Loaded: loaded （/usr/lib/systemd/system/vsftpd.service; enabled）
   Active: active （running） since Wed 2015-08-12 22:00:17 CST; 35s ago
 Main PID: 12670 （vsftpd）
   CGroup: /system.slice/vsftpd.service
           └─12670 /usr/sbin/vsftpd /etc/vsftpd/vsftpd.conf

Aug 12 22:00:17 study.centos.vbird systemd[1]: Started Vsftpd ftp daemon.

vsftpd2.service - Vsftpd second ftp daemon
   Loaded: loaded （/etc/systemd/system/vsftpd2.service; enabled）
   Active: active （running） since Wed 2015-08-12 22:00:17 CST; 35s ago
 Main PID: 12672 （vsftpd）
   CGroup: /system.slice/vsftpd2.service
           └─12672 /usr/sbin/vsftpd /etc/vsftpd/vsftpd2.conf

[root@study system]# netstat -tlnp
Active Internet connections （only servers）
Proto Recv-Q Send-Q Local Address  Foreign Address   State    PID/Program name
tcp        0      0 0.0.0.0:22     0.0.0.0:*         LISTEN   1340/sshd
tcp        0      0 127.0.0.1:25   0.0.0.0:*         LISTEN   2387/master
tcp6       0      0 :::555         :::*              LISTEN   12672/vsftpd
tcp6       0      0 :::21          :::*              LISTEN   12670/vsftpd
tcp6       0      0 :::22          :::*              LISTEN   1340/sshd
tcp6       0      0 ::1:25         :::*              LISTEN   2387/master
```

很简单的将你的 systemd 所管理的 vsftpd 做了另一个服务！未来如果有相同的需求，同样的方法作一遍即可！



### 17.3.4 多重的重复设置方式：以 getty 为例

CentOS 7启动后有6个终端机tty1到tty6，这是由agetty命令实现的。实际管理这些终端的是getty.target和 [email protectedemail protectedemail protected](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 配置文件。以下是其具体设置和机制：

1. 查看getty.target的内容：

```
[root@study ~]# systemctl show getty.target
```

1. 修改终端机数量：

```
[root@study ~]# vim /etc/systemd/logind.conf
# 修改NAutoVTs为4
NAutoVTs=4
ReserveVT=0
[root@study ~]# systemctl stop [email protected]
[root@study ~]# systemctl stop [email protected]
[root@study ~]# systemctl restart systemd-logind.service
```

1. 临时启动额外的tty终端：

```
[root@study ~]# systemctl start [email protected]
```

#### 原文：

我们的 CentOS 7 开机完成后，不是说有 6 个终端机可以使用吗？就是那个 tty1~tty6 的啊！那个东西是由 agetty 这个指令达成的。 OK！那么这个终端机的功能又是从哪个项目所提供的呢？其实，那个东东涉及很多层面，主要管理的是 getty.target 这个 target unit ， 不过，实际产生 tty1~tty6 的则是由 [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 所提供的！咦！那个 @ 是啥东西？

先来查阅一下 /usr/lib/systemd/system/[[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 的内容好了：

```
[root@study ~]# cat //usr/lib/systemd/system/[email protected]
[Unit]
Description=Getty on %I
Documentation=man:agetty（8） man:systemd-getty-generator（8）
Documentation=http://0pointer.de/blog/projects/serial-console.html
After=systemd-user-sessions.service plymouth-quit-wait.service
After=rc-local.service
Before=getty.target
ConditionPathExists=/dev/tty0

[Service]
ExecStart=-/sbin/agetty --noclear %I $TERM
Type=idle
Restart=always
RestartSec=0
UtmpIdentifier=%I
TTYPath=/dev/%I
TTYReset=yes
TTYVHangup=yes
TTYVTDisallocate=yes
KillMode=process
IgnoreSIGPIPE=no
SendSIGHUP=yes

[Install]
WantedBy=getty.target
```

比较重要的当然就是 ExecStart 项目啰！那么我们去 man agetty 时，发现到它的语法应该是“ agetty --noclear tty1 ”之类的字样， 因此，我们如果要启动六个 tty 的时候，基本上应该要有六个启动配置文件。亦即是可能会用到 getty1.service, getty2.service...getty6.service 才对！ 哇！这样控管很麻烦啊～所以，才会出现这个 @ 的项目啦！咦！这个 @ 到底怎么回事呢？我们先来看看 [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 的上游，亦即是 getty.target 这个东西的内容好了！

```
[root@study ~]# systemctl show getty.target
# 那个 show 的指令可以将 getty.target 的默认设置值也取出来显示！
Names=getty.target
[email protected]
WantedBy=multi-user.target
Conflicts=shutdown.target
Before=multi-user.target
[email protected] [email protected] [email protected] [email protected]
  [email protected] [email protected]
.....（后面省略）.....
```

你会发现，咦！怎么会多出六个怪异的 service 呢？我们拿 [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 来说明一下好了！当我们执行完 getty.target 之后， 他会持续要求 [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 等六个服务继续启动。那我们的 systemd 就会这么作：

- 先看 /usr/lib/systemd/system/, /etc/systemd/system/ 有没有 [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 的设置，若有就执行，若没有则执行下一步；
- 找 [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 的设置，若有则将 @ 后面的数据带入成 %I 的变量，进入 [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 执行！

这也就是说，其实 [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 实际上是不存在的！他主要是通过 [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 来执行～也就是说， [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 的目的是为了要简化多个执行的启动设置， 他的命名方式是这样的：

```
原始文件：执行服务名称@.service
可执行文件案：执行服务名称@范例名称.service
```

因此当有范例名称带入时，则会有一个新的服务名称产生出来！你再回头看看 [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 的启动脚本：

```
ExecStart=-/sbin/agetty --noclear %I $TERM
```

上表中那个 %I 指的就是“范例名称”！根据 getty.target 的信息输出来看，[[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 的 %I 就是 tty1 啰！因此执行脚本就会变成“ /sbin/agetty --noclear tty1 ”！ 所以我们才有办法以一个配置文件来启动多个 tty1 给用户登陆啰！

- 将 tty 的数量由 6 个降低到 4 个

现在你应该要感到困扰的是，那么“ 6 个 tty 是谁规定的”为什么不是 5 个还是 7 个？这是因为 systemd 的登陆配置文件 /etc/systemd/logind.conf 里面规范的啦！ 假如你想要让 tty 数量降低到剩下 4 个的话，那么可以这样实验看看：

```
# 1\. 修改默认的 logind.conf 内容，将原本 6 个虚拟终端机改成 4 个
[root@study ~]# vim /etc/systemd/logind.conf
[Login]
NAutoVTs=4
ReserveVT=0
# 原本是 6 个而且还注解，请取消注解，然后改成 4 吧！

# 2\. 关闭不小心启动的 tty5, tty6 并重新启动 getty.target 啰！
[root@study ~]# systemctl stop [email protected]
[root@study ~]# systemctl stop [email protected]
[root@study ~]# systemctl restart systemd-logind.service
```

现在你再到桌面环境下，按下 [ctrl]+[alt]+[F1]~[F6] 就会发现，只剩下四个可用的 tty 啰！后面的 tty5, tty6 已经被放弃了！不再被启动喔！ 好！那么我暂时需要启动 tty8 时，又该如何处理呢？需要重新创建一个脚本吗？不需要啦！可以这样作！

```
[root@study ~]# systemctl start [email protected]
```

无须额外创建其他的启动服务配置文件喔！

- 暂时新增 vsftpd 到 2121 端口

不知道你有没有发现，其实在 /usr/lib/systemd/system 下面还有个特别的 [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 喔！来看看他的内容：

```
[root@study ~]# cat /usr/lib/systemd/system/[email protected]
[Unit]
Description=Vsftpd ftp daemon
After=network.target
PartOf=vsftpd.target

[Service]
Type=forking
ExecStart=/usr/sbin/vsftpd /etc/vsftpd/%i.conf

[Install]
WantedBy=vsftpd.target
```

根据前面 [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 的说明，我们知道在启动的脚本设置当中， %i 或 %I 就是代表 @ 后面接的范例文件名的意思！ 那我能不能创建 vsftpd3.conf 文件，然后通过该文件来启动新的服务呢？就来玩玩看！

```
# 1\. 根据 [email protected] 的建议，于 /etc/vsftpd/ 下面先创建新的配置文件
[root@study ~]# cd /etc/vsftpd
[root@study vsftpd]# cp vsftpd.conf vsftpd3.conf
[root@study vsftpd]# vim vsftpd3.conf
listen_port=2121

# 2\. 暂时启动这个服务，不要永久启动他！
[root@study vsftpd]# systemctl start [email protected]
[root@study vsftpd]# systemctl status [email protected]
[email protected] - Vsftpd ftp daemon
   Loaded: loaded （/usr/lib/systemd/system/[email protected]; disabled）
   Active: active （running） since Thu 2015-08-13 01:34:05 CST; 5s ago

[root@study vsftpd]# netstat -tlnp
Active Internet connections （only servers）
Proto Recv-Q Send-Q Local Address  Foreign Address  State    PID/Program name
tcp6       0      0 :::2121        :::*             LISTEN   16404/vsftpd
tcp6       0      0 :::555         :::*             LISTEN   12672/vsftpd
tcp6       0      0 :::21          :::*             LISTEN   12670/vsftpd
```

因为我们启用了 [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) ，代表要使用的配置文件在 /etc/vsftpd/vsftpd3.conf 的意思！所以可以直接通过 [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 而无须重新设置启动脚本！ 这样是否比前几个小节的方法还要简便呢？ ^*^。通过这个方式，你就可以使用到新的配置文件啰！只是你得要注意到 @ 这个东西就是了！ ^*^

![鸟哥的图示](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/img/vbird_face.gif)

**Tips** 聪明的读者可能立刻发现一件事，为啥这次 FTP 增加了 2121 端口却不用修改 SELinux 呢？这是因为默认启动小于 1024 号码以下的端口时， 需要使用到 root 的权限，因此小于 1024 以下端口的启动较可怕。而这次范例中，我们使用 2121 端口，他对于系统的影响可能小一些 （其实一样可怕！）， 所以就忽略了 SELinux 的限制了。

### 17.3.5 自己的服务自己作

假设要创建一个备份系统的服务脚本，以下是具体步骤：

1. 创建备份脚本：

```
[root@study ~]# vim /backups/backup.sh
#!/bin/bash
source="/etc /home /root /var/lib /var/spool/{cron,at,mail}"
target="/backups/backup-system-$(date +%Y-%m-%d).tar.gz"
[ ! -d /backups ] && mkdir /backups
tar -zcvf ${target} ${source} &> /backups/backup.log
[root@study ~]# chmod a+x /backups/backup.sh
```

1. 创建服务启动脚本：

```
[root@study ~]# vim /etc/systemd/system/backup.service
[Unit]
Description=backup my server
Requires=atd.service

[Service]
Type=simple
ExecStart=/bin/bash -c " echo /backups/backup.sh | at now"

[Install]
WantedBy=multi-user.target
[root@study ~]# systemctl daemon-reload
[root@study ~]# systemctl start backup.service
[root@study ~]# systemctl status backup.service
```

通过上述步骤，可以创建并管理自定义的systemd服务，进一步提升系统的自动化管理能力。

#### 原文：

我们来仿真自己作一个服务吧！假设我要作一只可以备份自己系统的服务，这只脚本我放在 /backups 下面，内容有点像这样：

```
[root@study ~]# vim /backups/backup.sh
#!/bin/bash

source="/etc /home /root /var/lib /var/spool/{cron,at,mail}"
target="/backups/backup-system-$（date +%Y-%m-%d）.tar.gz"
[ ! -d /backups ] && mkdir /backups
tar -zcvf ${target} ${source} &&gt; /backups/backup.log

[root@study ~]# chmod a+x /backups/backup.sh
[root@study ~]# ll /backups/backup.sh
-rwxr-xr-x. 1 root root 220 Aug 13 01:57 /backups/backup.sh
# 记得要有可执行的权限才可以喔！
```

接下来，我们要如何设计一只名为 backup.service 的启动脚本设置呢？可以这样做喔！

```
[root@study ~]# vim /etc/systemd/system/backup.service
[Unit]
Description=backup my server
Requires=atd.service

[Service]
Type=simple
ExecStart=/bin/bash -c " echo /backups/backup.sh &#124; at now"

[Install]
WantedBy=multi-user.target
# 因为 ExecStart 里面有用到 at 这个指令，因此， atd.service 就是一定要的服务！

[root@study ~]# systemctl daemon-reload
[root@study ~]# systemctl start backup.service
[root@study ~]# systemctl status backup.service
backup.service - backup my server
   Loaded: loaded （/etc/systemd/system/backup.service; disabled）
   Active: inactive （dead）

Aug 13 07:50:31 study.centos.vbird systemd[1]: Starting backup my server...
Aug 13 07:50:31 study.centos.vbird bash[20490]: job 8 at Thu Aug 13 07:50:00 2015
Aug 13 07:50:31 study.centos.vbird systemd[1]: Started backup my server.
# 为什么 Active 是 inactive 呢？这是因为我们的服务仅是一个简单的 script 啊！
# 因此执行完毕就完毕了，不会继续存在内存中喔！
```

完成上述的动作之后，以后你都可以直接使用 systemctl start backup.service 进行系统的备份了！而且会直接丢进 atd 的管理中， 你就无须自己手动用 at 去处理这项任务了～好像还不赖喔！ ^_^

这样自己做一个服务好像也不难啊！ ^_^！自己动手玩玩看吧！



## 17.4 systemctl 针对 timer 的配置文件

### systemd.timer 的优势

相较于传统的 crond 服务，systemd.timer 具有以下优势：

- 所有的 systemd 服务产生的信息都会被记录 (log)，便于调试和排查问题。
- 可以与其他 systemd 服务结合，进行更灵活的任务管理。
- 可以与 control group (cgroup) 结合，限制任务的资源使用。

### 使用 systemd.timer 的需求

要使用 systemd.timer 功能，需要满足以下条件：

- 系统的 `timers.target` 必须启动。
- 必须存在一个 `sname.service` 服务。
- 必须存在一个 `sname.timer` 时间启动服务。

### sname.timer 的设置值

timer 配置文件的基本设置如下：

| TimerTimerTimer 部分 | 参数意义说明                                                 |
| -------------------- | ------------------------------------------------------------ |
| OnActiveSec          | timers.target 启动多久之后执行此服务。                       |
| OnBootSec            | 开机完成后多久执行此服务。                                   |
| OnStartupSec         | systemd 第一次启动后多久执行此服务。                         |
| OnUnitActiveSec      | 最后一次启动服务后多久再次执行此服务。                       |
| OnUnitInactiveSec    | 最后一次停止服务后多久再次执行此服务。                       |
| OnCalendar           | 使用实际时间（非循环时间）启动服务。                         |
| Unit                 | 指定要启动的 service unit（如果名称不同于 timer 配置文件名称）。 |
| Persistent           | 持续进行功能（用于 OnCalendar）。                            |

### OnCalendar 的时间设置

常见的时间格式和单位如下：

- `YYYY-MM-DD HH:MM:SS`：例如 `2022-12-25 12:00:00`。
- 时间间隔单位：us（微秒），ms（毫秒），s（秒），m（分钟），h（小时），d（天），w（周），month（月），y（年）。

常用的时间设置例子：

- 隔 3 小时：`3h` 或 `3hr` 或 `3hours`
- 隔 300 分钟和 10 秒：`10s 300m`
- 隔 5 天和 100 分钟：`100m 5day`

英文口语化日期示例：

- `now`：当前时间
- `today`：当天的 00:00:00
- `tomorrow`：第二天的 00:00:00
- `hourly`：每小时的 00:00
- `daily`：每天的 00:00
- `weekly`：每周一的 00:00
- `monthly`：每月的 1 号 00:00
- `+3h10m`：当前时间后 3 小时 10 分钟
- `2015-08-16`：具体日期

### 示例：定期执行任务

假设我们有一个 `backup.service` 服务需要定期执行，具体需求如下：

- 开机后 2 小时执行一次 `backup.service`。
- 第一次执行后，每两天执行一次 `backup.service`。

创建并配置 `backup.timer`：

```
[root@study ~]# vim /etc/systemd/system/backup.timer
[Unit]
Description=backup my server timer

[Timer]
OnBootSec=2hrs
OnUnitActiveSec=2days

[Install]
WantedBy=multi-user.target
```

重新加载并启动 timer：

```
[root@study ~]# systemctl daemon-reload
[root@study ~]# systemctl enable backup.timer
[root@study ~]# systemctl restart backup.timer
[root@study ~]# systemctl list-unit-files | grep backup
backup.service          disabled
backup.timer            enabled
```

### 示例：固定日期执行任务

假设我们希望每周日凌晨 2 点执行一次 `backup.service`：

创建并配置 `backup2.timer`：

```
[root@study ~]# vim /etc/systemd/system/backup2.timer
[Unit]
Description=backup my server timer2

[Timer]
OnCalendar=Sun *-*-* 02:00:00
Persistent=true
Unit=backup.service

[Install]
WantedBy=multi-user.target
```

重新加载并启动 timer：

```
[root@study ~]# systemctl daemon-reload
[root@study ~]# systemctl enable backup2.timer
[root@study ~]# systemctl start backup2.timer
[root@study ~]# systemctl show backup2.timer
NextElapseUSecRealtime=45y 7month 1w 6d 10h 30min
```

##### 总结

通过上述步骤，您可以使用 systemd 的 timer 功能来替代 crond，实现更加灵活和精确的任务调度。

#### 原文：

有时候，某些服务你想要定期执行，或者是开机后执行，或者是什么服务启动多久后执行等等的。在过去，我们大概都是使用 crond 这个服务来定期处理， 不过，既然现在有一直常驻在内存当中的 systemd 这个好用的东西，加上这 systemd 有个协力服务，名为 timers.target 的家伙，这家伙可以协助定期处理各种任务！ 那么，除了 crond 之外，如何使用 systemd 内置的 time 来处理各种任务呢？这就是本小节的重点啰！

- systemd.timer 的优势

在 archlinux 的官网 wiki 上面有提到，为啥要使用 systemd.timer 呢？

- 由于所有的 systemd 的服务产生的信息都会被纪录 （log），因此比 crond 在 debug 上面要更清楚方便的多；
- 各项 timer 的工作可以跟 systemd 的服务相结合；
- 各项 timer 的工作可以跟 control group （cgroup，用来取代 /etc/secure/limit.conf 的功能） 结合，来限制该工作的资源利用

虽然还是有些弱点啦～例如 systemd 的 timer 并没有 email 通知的功能 （除非自己写一个），也没有类似 anacron 的一段时间内的随机取样功能 （random_delay）， 不过，总体来说，还是挺不错的！此外，相对于 crond 最小的单位到分， systemd 是可以到秒甚至是毫秒的单位哩！相当有趣！

- 任务需求

基本上，想要使用 systemd 的 timer 功能，你必须要有几个要件：

- 系统的 timer.target 一定要启动
- 要有个 sname.service 的服务存在 （sname 是你自己指定的名称）
- 要有个 sname.timer 的时间启动服务存在

满足上面的需求就 OK 了！有没有什么案例可以来实作看看？这样说好了，我们上个小节不是才自己做了个 backup.service 的服务吗？那么能不能将这个 backup.service 用在定期执行上面呢？好啊！那就来测试看看！

- sname.timer 的设置值

你可以到 /etc/systemd/system 下面去创建这个 *.timer 档，那这个文件的内容要项有哪些东西呢？基本设置主要有下面这些： （man systemd.timer & man systemd.time）

| [Timer] 部份      |                                                              |
| ----------------- | ------------------------------------------------------------ |
| 设置参数          | 参数意义说明                                                 |
| OnActiveSec       | 当 timers.target 启动多久之后才执行这只 unit                 |
| OnBootSec         | 当开机完成后多久之后才执行                                   |
| OnStartupSec      | 当 systemd 第一次启动之后过多久才执行                        |
| OnUnitActiveSec   | 这个 timer 配置文件所管理的那个 unit 服务在最后一次启动后，隔多久后再执行一次的意思 |
| OnUnitInactiveSec | 这个 timer 配置文件所管理的那个 unit 服务在最后一次停止后，隔多久再执行一次的意思。 |
| OnCalendar        | 使用实际时间 （非循环时间） 的方式来启动服务的意思！至于时间的格式后续再来谈。 |
| Unit              | 一般来说不太需要设置，因此如同上面刚刚提到的，基本上我们设置都是 sname.server + sname.timer，那如果你的 sname 并不相同时，那在 .timer 的文件中， 就得要指定是哪一个 service unit 啰！ |
| Persistent        | 当使用 OnCalendar 的设置时，指定该功能要不要持续进行的意思。通常是设置为 yes ，比较能够满足类似 anacron 的功能喔！ |

基本的项目仅有这些而已，在设置上其实并不困难啦！

- 使用于 OnCalendar 的时间

如果你想要从 crontab 转成这个 timer 功能的话，那么对于时间设置的格式就得要了解了解～基本上的格式如下所示：

```
语法：英文周名  YYYY-MM-DD  HH:MM:SS
范例：Thu       2015-08-13  13:40:00
```

上面谈的是基本的语法，你也可以直接使用间隔时间来处理！常用的间隔时间单位有：

- us 或 usec：微秒 （10-6 秒）
- ms 或 msec：毫秒 （10-3 秒）
- s, sec, second, seconds
- m, min, minute, minutes
- h, hr, hour, hours
- d, day, days
- w, week, weeks
- month, months
- y, year, years

常见的使用范例有：

```
隔 3 小时：             3h  或 3hr 或 3hours
隔 300 分钟过 10 秒：   10s 300m
隔 5 天又 100 分钟：    100m 5day
# 通常英文的写法，小单位写前面，大单位写后面～所以先秒、再分、再小时、再天数等～
```

此外，你也可以使用英文常用的口语化日期代表，例如 today, tomorrow 等！假设今天是 2015-08-13 13:50:00 的话，那么：

| 英文口语   | 实际的时间格式代表      |
| ---------- | ----------------------- |
| now        | Thu 2015-08-13 13:50:00 |
| today      | Thu 2015-08-13 00:00:00 |
| tomorrow   | Thu 2015-08-14 00:00:00 |
| hourly     | *-*- :00:00             |
| daily      | *-*-* 00:00:00          |
| weekly     | Mon *-*-* 00:00:00      |
| monthly    | *-*-01 00:00:00         |
| +3h10m     | Thu 2015-08-13 17:00:00 |
| 2015-08-16 | Sun 2015-08-16 00:00:00 |

- 一个循环时间运行的案例

现在假设这样：

- 开机后 2 小时开始执行一次这个 backup.service
- 自从第一次执行后，未来我每两天要执行一次 backup.service

好了，那么应该如何处理这个脚本呢？可以这样做喔！

```
[root@study ~]# vim /etc/systemd/system/backup.timer
[Unit]
Description=backup my server timer

[Timer]
OnBootSec=2hrs
OnUnitActiveSec=2days

[Install]
WantedBy=multi-user.target
# 只要这样设置就够了！储存离开吧！

[root@study ~]# systemctl daemon-reload
[root@study ~]# systemctl enable backup.timer
[root@study ~]# systemctl restart backup.timer
[root@study ~]# systemctl list-unit-files &#124; grep backup
backup.service          disabled   # 这个不需要启动！只要 enable backup.timer 即可！
backup.timer            enabled

[root@study ~]# systemctl show timers.target
ConditionTimestamp=Thu 2015-08-13 14:31:11 CST      # timer 这个 unit 启动的时间！

[root@study ~]# systemctl show backup.service
ExecMainExitTimestamp=Thu 2015-08-13 14:50:19 CST   # backup.service 上次执行的时间

[root@study ~]# systemctl show backup.timer
NextElapseUSecMonotonic=2d 19min 11.540653s         # 下一次执行距离 timers.target 的时间
```

如上表所示，我上次执行 backup.service 的时间是在 2015-08-13 14:50 ，由于设置两个小时执行一次，因此下次应该是 2015-08-15 14:50 执行才对！ 由于 timer 是由 timers.target 这个 unit 所管理的，而这个 timers.target 的启动时间是在 2015-08-13 14:31 ， 要注意，最终 backup.timer 所纪录的下次执行时间，其实是与 timers.target 所纪录的时间差！因此是“ 2015-08-15 14:50 - 2015-08-13 14:31 ”才对！ 所以时间差就是 2d 19min 啰！

- 一个固定日期运行的案例

上面的案例是固定周期运行一次，那如果我希望不管上面如何运行了，我都希望星期天凌晨 2 点运行这个备份程序一遍呢？请注意，因为已经存在 backup.timer 了！ 所以，这里我用 backup2.timer 来做区隔喔！

```
[root@study ~]# vim /etc/systemd/system/backup2.timer
[Unit]
Description=backup my server timer2

[Timer]
OnCalendar=Sun *-*-* 02:00:00
Persistent=true
Unit=backup.service

[Install]
WantedBy=multi-user.target

[root@study ~]# systemctl daemon-reload
[root@study ~]# systemctl enable backup2.timer
[root@study ~]# systemctl start backup2.timer
[root@study ~]# systemctl show backup2.timer
NextElapseUSecRealtime=45y 7month 1w 6d 10h 30min
```

与循环时间运行差异比较大的地方，在于这个 OnCalendar 的方法对照的时间并不是 times.target 的启动时间，而是 Unix 标准时间！ 亦即是 1970-01-01 00:00:00 去比较的！因此，当你看到最后出现的 NextElapseUSecRealtime 时，哇！下一次执行还要 45 年 + 7 个月 + 1 周 + 6 天 + 10 小时过 30 分～刚看到的时候，鸟哥确实因此揉了揉眼睛～确定没有看错...这才了解原来比对的是“日历时间”而不是某个 unit 的启动时间啊！呵呵！

通过这样的方式，你就可以使用 systemd 的 timer 来制作属于你的时程规划服务啰！



## 17.5 CentOS 7.x 默认启动的服务简易说明

随着 Linux 上面软件支持性越来越多，加上自由软件蓬勃的发展，我们可以在 Linux 上面用的 daemons 真的越来越多了。所以，想要写完所有的 daemons 介绍几乎是不可能的，因此，鸟哥这里仅介绍几个很常见的 daemons 而已， 更多的信息呢，就得要麻烦你自己使用 systemctl list-unit-files --type=service 去查询啰！ 下面的建议主要是针对 Linux 单机服务器的角色来说明的，不是桌上型的环境喔！

| CentOS 7.x 默认启动的服务内容        |                                                              |
| ------------------------------------ | ------------------------------------------------------------ |
| 服务名称                             | 功能简介                                                     |
| abrtd                                | （系统）abrtd 服务可以提供使用者一些方式，让使用者可以针对不同的应用软件去设计错误登录的机制， 当软件产生问题时，使用者就可以根据 abrtd 的登录文件来进行错误克服的行为。还有其他的 abrt-xxx.service 均是使用这个服务来加强应用程序 debug 任务的。 |
| accounts-daemon（可关闭）            | （系统）使用 accountsservice 计划所提供的一系列 D-Bus 界面来进行使用者帐号信息的查询。 基本上是与 useradd, usermod, userdel 等软件有关。 |
| alsa-X（可关闭）                     | （系统）开头为 alsa 的服务有不少，这些服务大部分都与音效有关！一般来说， 服务器且不开图形界面的话，这些服务可以关闭！ |
| atd                                  | （系统）单一的例行性工作调度，详细说明请参考[第十五章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#atjob)。 抵挡机制的配置文件在 /etc/at.{allow,deny} 喔！ |
| auditd                               | （系统）还记得前一章的 SELinux 所需服务吧？ 这就是其中一项，可以让系统需 SELinux 稽核的讯息写入 /var/log/audit/audit.log 中。 |
| avahi-daemon（可关闭）               | （系统）也是一个用户端的服务，可以通过 Zeroconf 自动的分析与管理网络。 Zeroconf 较常用在笔记本电脑与行动设备上，所以我们可以先关闭他啦！ |
| brandbot rhel-*                      | （系统）这些服务大多用于开机过程中所需要的各种侦测环境的脚本，同时也提供网络界面的启动与关闭。 基本上，你不要关闭掉这些服务比较妥当！ |
| chronyd ntpd ntpdate                 | （系统）都是网络校正时间的服务！一般来说，你可能需要的仅有 chronyd 而已！ |
| cpupower                             | （系统）提供 CPU 的运行规范～可以参考 /etc/sysconfig/cpupower 得到更多的信息！ 这家伙与你的 CPU 使用情况有关喔！ |
| crond                                | （系统）系统配置文件为 /etc/crontab，详细数据可参考[第十五章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#cron)的说明。 |
| cups（可关闭）                       | （系统/网络）用来管理打印机的服务，可以提供网络连线的功能，有点类似打印服务器的功能哩！ 你可以在 Linux 本机上面以浏览器的 [http://localhost:631](http://localhost:631/) 来管理打印机喔！由于我们目前没有打印机，所以可以暂时关闭他。 |
| dbus                                 | （系统）使用 D-Bus 的方式在不同的应用程序之间传送讯息， 使用的方向例如应用程序间的讯息传递、每个使用者登陆时提供的讯息数据等。 |
| dm-event multipathd                  | （系统）监控设备对应表 （device mapper） 的主要服务，当然不能关掉啊！ 否则就无法让 Linux 使用我们的周边设备与储存设备了！ |
| dmraid-activation mdmonitor          | （系统）用来启动 Software RAID 的重要服务！最好不要关闭啦！虽然你可能没有 RAID。 |
| dracut-shutdown                      | （系统）用来处理 initramfs 的相关行为，这与开机流程相关性较高～ |
| ebtables                             | （系统/网络）通过类似 iptables 这种防火墙规则的设置方式，设计网卡作为桥接时的封包分析政策。 其实就是防火墙。不过与下面谈到的防火墙应用不太一样。如果没有使用虚拟化，或者启用了 firewalld ，这个服务可以不启动。 |
| emergency rescue                     | （系统）进入紧急模式或者是救援模式的服务                     |
| firewalld                            | （系统/网络）就是防火墙！以前有 iptables 与 ip6tables 等防火墙机制，新的 firewalld 搭配 firewall-cmd 指令，可以快速的创建好你的防火墙系统喔！因此，从 CentOS 7.1 以后，iptables 服务的启动脚本已经被忽略了！ 请使用 firewalld 来取代 iptables 服务喔！ |
| gdm                                  | （系统）GNOME 的登陆管理员，就是图形界面上一个很重要的登陆管理服务！ |
| getty@                               | （系统）就是要在本机系统产生几个文字界面 （tty） 登陆的服务啰！ |
| hyper *ksm* libvirt* vmtoolsd        | （系统）跟创建虚拟机有关的许多服务！如果你不玩虚拟机， 那么这些服务可以先关闭。此外，如果你的 Linux 本来就在虚拟机的环境下，那这些服务对你就没有用！因为这些服务是让实体机器来创建虚拟机的！ |
| irqbalance                           | （系统）如果你的系统是多核心的硬件，那么这个服务要启动， 因为它可以自动的分配系统中断 （IRQ） 之类的硬件资源。 |
| iscsi*                               | （系统）可以挂载来自网络磁盘机的服务！这个服务可以在系统内仿真好贵的 SAN 网络磁盘。 如果你确定系统上面没有挂载这种网络磁盘，也可以将他关闭的。 |
| kdump（可关闭）                      | （系统）在安装 CentOS 的章节就谈过这东西，主要是 Linux 核心如果出错时，用来纪录内存的东西。 鸟哥觉得不需要启动他！除非你是核心骇客！ |
| lvm2-*                               | （系统）跟 LVM 相关性较高的许多服务，当然也不能关！不然系统上面的 LVM2 就没人管了！ |
| microcode                            | （系统）Intel 的 CPU 会提供一个外挂的微指令集提供系统运行， 不过，如果你没有下载 Intel 相关的指令集文件，那么这个服务不需要启动的，也不会影响系统运行。 |
| ModemManager network NetworkManager* | （系统/网络）主要就是调制解调器、网络设置等服务！进入 CentOS 7 之后，系统似乎不太希望我们使用 network 服务了， 比较建议的是使用 NetworkManager 搭配 nmcli 指令来处理网络设置～所以，反而是 NetworkManager 要开，而 network 不用开哩！ |
| quotaon                              | （系统）启动 Quota 要用到的服务喔！                          |
| rc-local                             | （系统）相容于 /etc/rc.d/rc.local 的调用方式！只是，你必须要让 /etc/rc.d/rc.local 具有 x 的权限后， 这个服务才能真的运行！否则，你写入 /etc/rc.d/rc.local 的脚本还是不会运行的喔！ |
| rsyslog                              | （系统）这个服务可以记录系统所产生的各项讯息， 包括 /var/log/messages 内的几个重要的登录文件啊。 |
| smartd                               | （系统）这个服务可以自动的侦测硬盘状态，如果硬盘发生问题的话， 还能够自动的回报给系统管理员，是个非常有帮助的服务喔！不可关闭他啊！ |
| sysstat                              | （系统）事实上，我们的系统有只名为 sar 的指令会记载某些时间点下，系统的资源使用情况，包括 CPU/流量/输入输出量等， 当 sysstat 服务启动后，这些纪录的数据才能够写入到纪录档 （log） 里面去！ |
| systemd-*                            | （系统）大概都是属于系统运行过程所需要的服务，没必要都不要更动它的默认状态！ |
| plymount* upower                     | （系统）与图形界面的使用相关性较高的一些服务！没启动图形界面时，这些服务可以暂时不管他！ |

上面的服务是 CentOS 7.x 默认有启动的，这些默认启动的服务很多是针对桌面电脑所设计的，所以啰，如果你的 Linux 主机用途是在服务器上面的话，那么有很多服务是可以关闭的啦！如果你还有某些不明白的服务想要关闭的， 请务必要搞清楚该服务的功能为何喔！举例来说，那个 rsyslog 就不能关闭，如果你关掉他的话，系统就不会记录登录文件， 那你的系统所产生的警告讯息就无法记录起来，你将无法进行 debug 喔。

下面鸟哥继续说明一些可能在你的系统当中的服务，只是默认并没有启动这个服务就是了。只是说明一下， 各服务的用途还是需要您自行查询相关的文章啰。

| 其他服务的简易说明 |                                                              |
| ------------------ | ------------------------------------------------------------ |
| 服务名称           | 功能简介                                                     |
| dovecot            | （网络）可以设置 POP3/IMAP 等收受信件的服务，如果你的 Linux 主机是 email server 才需要这个服务，否则不需要启动他啦！ |
| httpd              | （网络）这个服务可以让你的 Linux 服务器成为 www server 喔！  |
| named              | （网络）这是领域名称服务器 （Domain Name System） 的服务， 这个服务非常重要，但是设置非常困难！目前应该不需要这个服务啦！ |
| nfs nfs-server     | （网络）这就是 Network Filesystem，是 Unix-Like 之间互相作为网络磁盘机的一个功能。 |
| smb nmb            | （网络）这个服务可以让 Linux 仿真成为 Windows 上面的网络上的芳邻。 如果你的 Linux 主机想要做为 Windows 用户端的网络磁盘机服务器，这玩意儿得要好好玩一玩。 |
| vsftpd             | （网络）作为文件传输服务器 （FTP） 的服务。                  |
| sshd               | （网络）这个是远端连线服务器的软件功能， 这个通讯协定比 telnet 好的地方在于 sshd 在传送数据时可以进行加密喔！这个服务不要关闭他啦！ |
| rpcbind            | （网络）达成 RPC 协定的重要服务！包括 NFS, NIS 等等都需要这东西的协助！ |
| postfix            | （网络）寄件的邮件主机～因为系统还是会产生很多 email 讯息！例如 crond / atd 就会传送 email 给本机用户！ 所以这个服务千万不能关！即使你不是 mail server 也是要启用这服务才行！ |

## 17.6 重点回顾

- 早期的服务管理使用 systemV 的机制，通过 /etc/init.d/*, service, chkconfig, setup 等指令来管理服务的启动/关闭/默认启动；
- 从 CentOS 7.x 开始，采用 systemd 的机制，此机制最大功能为平行处理，并采单一指令管理 （systemctl），开机速度加快！
- systemd 将各服务定义为 unit，而 unit 又分类为 service, socket, target, path, timer 等不同的类别，方便管理与维护
- 启动/关闭/重新启动的方式为： systemctl [start|stop|restart] unit.service
- 设置默认启动/默认不启动的方式为： systemctl [enable|disable] unit.service
- 查询系统所有启动的服务用 systemctl list-units --type=service 而查询所有的服务 （含不启动） 使用 systemctl list-unit-files --type=service
- systemd 取消了以前的 runlevel 概念 （虽然还是有相容的 target），转而使用不同的 target 操作环境。常见操作环境为 multi-user.targer 与 graphical.target。 不重新开机而转不同的操作环境使用 systemctl isolate unit.target，而设置默认环境则使用 systemctl set-default unit.target
- systemctl 系统默认的配置文件主要放在 /usr/lib/systemd/system，管理员若要修改或自行设计时，则建议放在 /etc/systemd/system/ 目录下。
- 管理员应使用 man systemd.unit, man systemd.service, man systemd.timer 查询 /etc/systemd/system/ 下面配置文件的语法， 并使用 systemctl daemon-reload 载入后，才能自行撰写服务与管理服务喔！
- 除了 atd 与 crond 之外，可以 通过 systemd.timer 亦即 timers.target 的功能，来使用 systemd 的时间管理功能。
- 一些不需要的服务可以关闭喔！