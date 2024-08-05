# 第十五章、例行性工作调度（crontab）

学习了基础篇也一阵子了，你会发现到为什么系统常常会主动的进行一些任务？这些任务到底是谁在设置工作的？ 如果你想要让自己设计的备份程序可以自动的在系统下面执行，而不需要手动来启动他，又该如何处置？ 这些例行的工作可能又分为“单一”工作与“循环”工作，在系统内又是哪些服务在负责？ 还有还有，如果你想要每年在老婆的生日前一天就发出一封信件提醒自己不要忘记，可以办的到吗？ 嘿嘿！这些种种要如何处理，就看看这一章先！

## 15.1 什么是例行性工作调度

每个人都有一些例行性或临时性的工作安排。例行性的工作，比如每年一次的加薪、每个月一次的工作报告、每周一次的午餐会、每天的打卡等；临时性的工作，比如总公司高官来访需要准备演讲器材。生活中，也有爱人生日、每天的起床时间、突发性的3C用品大降价等。

这些工作通常需要记录在行事历上，以免忘记。但如果计算机能够主动提醒我们，这将变得更加轻松。这时，Linux的例行性工作调度就派上用场了。Linux通过`crontab`和`at`来实现这些任务的调度。

### 15.1.1 Linux 工作调度的种类：at, cron

Linux下有两种工作调度方式：

- **例行性工作调度**：定期执行的任务。
- **突发性工作调度**：仅执行一次的任务。

这两种方式分别由`at`和`crontab`实现。

#### at

`at`用于处理仅执行一次的调度任务。需要`atd`服务支持。在某些新版的发行版中，`atd`默认未启动，此时`at`指令会失效。CentOS默认启动`atd`服务。

#### crontab

==`crontab`用于设置循环执行的任务，时间周期可以是分钟、小时、每天、每周、每月或每年。`crontab`可以通过指令执行，也可以编辑`/etc/crontab`来支持。`crond`服务支持`crontab`的生效。==

#### 原文：

从上面的说明当中，我们可以很清楚的发现两种工作调度的方式：

- 一种是例行性的，就是每隔一定的周期要来办的事项；
- 一种是突发性的，就是这次做完以后就没有的那一种 （ 3C 大降价...）

那么在 Linux 下面如何达到这两个功能呢？那就得使用 at 与 crontab 这两个好东西啰！

- at ：at 是个可以处理仅执行一次就结束调度的指令，不过要执行 at 时， 必须要有 atd 这个[服务 （第十七章）](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 的支持才行。在某些新版的 distributions 中，atd 可能默认并没有启动，那么 at 这个指令就会失效呢！不过我们的 CentOS 默认是启动的！
- crontab ：crontab 这个指令所设置的工作将会循环的一直进行下去！ 可循环的时间为分钟、小时、每周、每月或每年等。crontab 除了可以使用指令执行外，亦可编辑 /etc/crontab 来支持。 至于让 crontab 可以生效的服务则是 crond 这个服务喔！

下面我们先来谈一谈 Linux 的系统到底在做什么事情，怎么有若干多的工作调度在进行呢？然后再回来谈一谈 at 与 crontab 这两个好东西！





### 15.1.2 CentOS Linux 系统上常见的例行性工作

Linux系统会自动执行一些例行性任务来保证系统的正常运行。例如：自动更新、更新文件名数据库、分析日志文件等。常见的例行性任务包括：

#### 1. 登录文件的轮替（log rotate）

Linux会记录系统的各种信息，形成登录文件。为了防止登录文件过大，需要定期轮替旧文件。`log rotate`任务就是定期将旧的数据与新的数据分开存放。

#### 2. 登录文件分析（logwatch）

系统发生问题时，错误信息会记录到登录文件中。`logwatch`程序会主动分析登录信息，并将分析结果发送给系统管理员。系统管理员可以通过这些分析结果及时发现和处理系统问题。

#### 3. 创建 locate 的数据库

`locate`指令通过已有的文件名数据库进行文件名查询。文件名数据库放在`/var/lib/mlocate/`中，系统会定期更新该数据库，任务由`updatedb`指令完成。

#### 4. man page 查询数据库的创建

类似`locate`数据库，`man page`数据库通过`mandb`指令创建。系统会定期更新`man page`数据库，以提供快速查询功能。

#### 5. RPM 软件登录文件的创建

RPM软件管理机制会记录系统中软件的文件名变化。系统会定期创建RPM数据库，以便追踪软件的安装和更新情况。

#### 6. 移除暂存盘

某些软件运行时会产生暂存盘，关闭软件时这些暂存盘可能不会自动移除。系统通过`tmpwatch`指令删除这些无效的暂存盘，防止磁盘容量被耗尽。

#### 7. 网络服务相关的分析行为

安装类似WWW服务器软件（如`apache`）时，Linux系统会自动分析该软件的登录文件，并检查网络信息的有效性。

系统的例行性工作与安装的软件有关，安装的软件越多，系统执行的例行性工作也越多。例如，系统管理员可以编写分析工具或第三方软件来增加系统的工作量。

了解这些例行性工作的处理方式，可以更好地管理和维护Linux系统，确保系统的稳定运行。

#### ==原文：==

如果你曾经使用过 Linux 一阵子了，那么你大概会发现到 Linux 会主动的帮我们进行一些工作呢！ 比方说自动的进行线上更新 （on-line update）、自动的进行 updatedb （[第六章谈到的 locate 指令](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#locate)） 更新文件名数据库、自动的作登录文件分析 （所以 root 常常会收到标题为 logwatch 的信件） 等等。这是由于系统要正常运行的话， 某些在背景下面的工作必须要定时进行的缘故。基本上 Linux 系统常见的例行性任务有：

- 进行登录文件的轮替 （log rotate）： Linux 会主动的将系统所发生的各种信息都记录下来，这就是[登录文件 （第十八章）](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)。 由于系统会一直记录登录信息，所以登录文件将会越来越大！我们知道大型文件不但占容量还会造成读写性能的困扰， 因此适时的将登录文件数据挪一挪，让旧的数据与新的数据分别存放，则比较可以有效的记录登录信息。这就是 log rotate 的任务！这也是系统必要的例行任务；
- 登录文件分析 logwatch 的任务： 如果系统发生了软件问题、硬件错误、资安问题等，绝大部分的错误信息都会被记录到登录文件中， 因此系统管理员的重要任务之一就是分析登录文件。但你不可能手动通过 vim 等软件去检视登录文件，因为数据太复杂了！ 我们的 CentOS 提供了一只程序“ logwatch ”来主动分析登录信息，所以你会发现，你的 root 老是会收到标题为 logwatch 的信件，那是正常的！你最好也能够看看该信件的内容喔！
- 创建 locate 的数据库： 在第六章我们谈到的 [locate](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#locate) 指令时， 我们知道该指令是通过已经存在的文件名数据库来进行系统上文件名的查询。我们的文件名数据库是放置到 /var/lib/mlocate/ 中。 问题是，这个数据库怎么会自动更新啊？嘿嘿！这就是系统的例行性工作所产生的效果啦！系统会主动的进行 updatedb 喔！
- man page 查询数据库的创建： 与 locate 数据库类似的，可提供快速查询的 man page db 也是个数据库，但如果要使用 man page 数据库时，就得要执行 mandb 才能够创建好啊！ 而这个 man page 数据库也是通过系统的例行性工作调度来自动执行的哩！
- RPM 软件登录文件的创建： RPM （[第二十二章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)） 是一种软件管理的机制。由于系统可能会常常变更软件， 包括软件的新安装、非经常性更新等，都会造成软件文件名的差异。为了方便未来追踪，系统也帮我们将文件名作个排序的记录呢！ 有时候系统也会通过调度来帮忙 RPM 数据库的重新创建喔！
- 移除暂存盘： 某些软件在运行中会产生一些暂存盘，但是当这个软件关闭时，这些暂存盘可能并不会主动的被移除。 有些暂存盘则有时间性，如果超过一段时间后，这个暂存盘就没有效用了，此时移除这些暂存盘就是一件重要的工作！ 否则磁盘容量会被耗光。系统通过例行性工作调度执行名为 tmpwatch 的指令来删除这些暂存盘呢！
- 与网络服务有关的分析行为： 如果你有安装类似 WWW 服务器软件 （一个名为 apache 的软件），那么你的 Linux 系统通常就会主动的分析该软件的登录文件。 同时某些凭证与认证的网络信息是否过期的问题，我们的 Linux 系统也会很友好的帮你进行自动检查！

其实你的系统会进行的例行性工作与你安装的软件多寡有关，如果你安装过多的软件，某些服务功能的软件都会附上分析工具， 那么你的系统就会多出一些例行性工作啰！像鸟哥的主机还多加了很多自己撰写的分析工具，以及其他第三方协力软件的分析软件， 嘿嘿！俺的 Linux 工作量可是非常大的哩！因为有这么多的工作需要进行，所以我们当然得要了解例行性工作的处理方式啰！







## 15.2 仅执行一次的工作调度

在Linux系统中，可以通过`at`指令来实现单一工作调度。下面介绍如何启动`atd`服务并使用`at`进行工作调度。

### 15.2.1 atd 的启动与 at 运行的方式

要使用单一工作调度，必须确保`atd`服务已经启动。可以通过以下命令启动和管理`atd`服务：

```
[root@study ~]# systemctl restart atd  # 重新启动 atd 服务
[root@study ~]# systemctl enable atd   # 设置 atd 服务开机自动启动
[root@study ~]# systemctl status atd   # 查看 atd 服务的状态
```

确认`atd`服务运行正常的关键在于看到`enabled`和`running`状态。

#### at 的运行方式

`at`指令用于产生工作任务，并将这些任务以文本文件的形式写入`/var/spool/at/`目录，等待`atd`服务执行。并非所有用户都可以使用`at`，需要通过`/etc/at.allow`和`/etc/at.deny`文件进行权限控制。

- **/etc/at.allow**：只允许文件中列出的用户使用`at`。
- **/etc/at.deny**：文件中列出的用户不能使用`at`。
- 如果这两个文件都不存在，则只有`root`用户可以使用`at`。

#### 原文：

要使用单一工作调度时，我们的 Linux 系统上面必须要有负责这个调度的服务，那就是 atd 这个玩意儿。 不过并非所有的 Linux distributions 都默认会把他打开的，所以呢，某些时刻我们必须要手动将他启用才行。 启用的方法很简单，就是这样：

```
[root@study ~]# systemctl restart atd  # 重新启动 atd 这个服务
[root@study ~]# systemctl enable atd   # 让这个服务开机就自动启动
[root@study ~]# systemctl status atd   # 查阅一下 atd 目前的状态
atd.service - Job spooling tools
   Loaded: loaded （/usr/lib/systemd/system/atd.service; enabled）       # 是否开机启动
   Active: active （running） since Thu 2015-07-30 19:21:21 CST; 23s ago # 是否正在运行中
 Main PID: 26503 （atd）
   CGroup: /system.slice/atd.service
           └─26503 /usr/sbin/atd -f

Jul 30 19:21:21 study.centos.vbird systemd[1]: Starting Job spooling tools...
Jul 30 19:21:21 study.centos.vbird systemd[1]: Started Job spooling tools.
```

重点就是要看到上表中的特殊字体，包括“ enabled ”以及“ running ”时，这才是 atd 真的有在运行的意思喔！这部份我们在[第十七章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)会谈及。

- at 的运行方式

既然是工作调度，那么应该会有产生工作的方式，并且将这些工作排进行程表中啰！OK！那么产生工作的方式是怎么进行的？ 事实上，我们使用 at 这个指令来产生所要运行的工作，并将这个工作以文本文件的方式写入 /var/spool/at/ 目录内，该工作便能等待 atd 这个服务的取用与执行了。就这么简单。

不过，并不是所有的人都可以进行 at 工作调度喔！为什么？因为安全的理由啊～ 很多主机被所谓的“绑架”后，最常发现的就是他们的系统当中多了很多的怪客程序 （cracker program）， 这些程序非常可能运用工作调度来执行或蒐集系统信息，并定时的回报给怪客团体！ 所以啰，除非是你认可的帐号，否则先不要让他们使用 at 吧！那怎么达到使用 at 的列管呢？

我们可以利用 /etc/at.allow 与 /etc/at.deny 这两个文件来进行 at 的使用限制呢！ 加上这两个文件后， at 的工作情况其实是这样的：

1. 先找寻 **/etc/at.allow** 这个文件，写在这个文件中的使用者才能使用 at ，没有在这个文件中的使用者则不能使用 at （即使没有写在 at.deny 当中）；
2. 如果 /etc/at.allow 不存在，就寻找 **/etc/at.deny** 这个文件，若写在这个 at.deny 的使用者则不能使用 at ，而没有在这个 at.deny 文件中的使用者，就可以使用 at 咯；
3. 如果两个文件都不存在，那么只有 root 可以使用 at 这个指令。

通过这个说明，我们知道 /etc/at.allow 是管理较为严格的方式，而 /etc/at.deny 则较为松散 （因为帐号没有在该文件中，就能够执行 at 了）。在一般的 distributions 当中，由于假设系统上的所有用户都是可信任的， 因此系统通常会保留一个空的 /etc/at.deny 文件，意思是允许所有人使用 at 指令的意思 （您可以自行检查一下该文件）。 不过，万一你不希望有某些使用者使用 at 的话，将那个使用者的帐号写入 /etc/at.deny 即可！ 一个帐号写一行。



### 15.2.2 实际运行单一工作调度

使用`at`指令可以简单地进行单一工作调度。基本语法如下：

```
[root@study ~]# at [-mldv] TIME
[root@study ~]# at -c 工作号码
```

- `-m`：工作完成后即使没有输出，也会通过邮件通知用户。
- `-l`：列出当前用户的所有`at`调度任务。
- `-d`：取消一个`at`调度任务。
- `-v`：使用更明显的时间格式列出`at`调度任务。
- `-c`：列出指定工作的实际指令内容。

时间格式示例：

- `HH:MM`：当天的特定时间，若时间已过，则为次日的相同时间。
- `HH:MM YYYY-MM-DD`：指定日期的特定时间。
- `HH:MM[am|pm] [Month] [Date]`：指定日期和时间。
- `HH:MM[am|pm] + number [minutes|hours|days|weeks]`：当前时间加上一定时间后的时刻。

#### 示例

**示例1**：五分钟后将`/root/.bashrc`文件通过邮件发送给`root`用户。

```
[root@study ~]# at now + 5 minutes
at> /bin/mail -s "testing at job" root < /root/.bashrc
at> <EOT>  # 输入 [ctrl] + d 结束
job 2 at Thu Jul 30 19:35:00 2015
```

**示例2**：查看上述第2个工作的内容。

```
[root@study ~]# at -c 2
#!/bin/sh
# atrun uid=0 gid=0
# mail root 0
umask 22
...
/bin/mail -s "testing at job" root < /root/.bashrc
```

**示例3**：在2015年8月4日23:00关机。

```
[root@study ~]# at 23:00 2015-08-04
at> /bin/sync
at> /bin/sync
at> /sbin/shutdown -h now
at> <EOT>
job 3 at Tue Aug  4 23:00:00 2015
```

#### at 工作的管理

使用`atq`和`atrm`管理`at`任务。

**示例1**：查询当前主机上所有的`at`任务。

```
[root@study ~]# atq
3       Tue Aug  4 23:00:00 2015 a root
```

**示例2**：移除上述第3个任务。

```
[root@study ~]# atrm 3
[root@study ~]# atq
# 无输出表示任务已移除
```

### batch：系统有空时才进行背景任务

`batch`指令是`at`的一种扩展，用于在系统负载较低时执行任务。`batch`会在CPU负载小于0.8时才执行任务。

**示例**：使用`batch`在系统空闲时执行`updatedb`任务。

```
[root@study ~]# batch
at> /usr/bin/updatedb
at> <EOT>
job 4 at Thu Jul 30 19:57:00 2015
```

使用`uptime`命令查看系统负载，确认`batch`任务在系统负载低于0.8时才执行。

```
[root@study ~]# uptime
19:56:45 up 2 days, 19:54,  2 users,  load average: 3.93, 2.23, 0.96
```

通过以上步骤，可以使用`at`和`batch`指令有效地管理和执行单一工作调度任务。

#### 原文：

单一工作调度的进行就使用 at 这个指令啰！这个指令的运行非常简单！将 at 加上一个时间即可！基本的语法如下：

```
[root@study ~]# at [-mldv] TIME
[root@study ~]# at -c 工作号码
选项与参数：
-m  ：当 at 的工作完成后，即使没有输出讯息，亦以 email 通知使用者该工作已完成。
-l  ：at -l 相当于 atq，列出目前系统上面的所有该使用者的 at 调度；
-d  ：at -d 相当于 atrm ，可以取消一个在 at 调度中的工作；
-v  ：可以使用较明显的时间格式列出 at 调度中的工作列表；
-c  ：可以列出后面接的该项工作的实际指令内容。

TIME：时间格式，这里可以定义出“什么时候要进行 at 这项工作”的时间，格式有：
  HH:MM                ex&gt; 04:00
    在今日的 HH:MM 时刻进行，若该时刻已超过，则明天的 HH:MM 进行此工作。
  HH:MM YYYY-MM-DD        ex&gt; 04:00 2015-07-30
    强制规定在某年某月的某一天的特殊时刻进行该工作！
  HH:MM[am&#124;pm] [Month] [Date]    ex&gt; 04pm July 30
    也是一样，强制在某年某月某日的某时刻进行！
  HH:MM[am&#124;pm] + number [minutes&#124;hours&#124;days&#124;weeks]
    ex&gt; now + 5 minutes    ex&gt; 04pm + 3 days
    就是说，在某个时间点“再加几个时间后”才进行。
```

老实说，这个 at 指令的下达最重要的地方在于“时间”的指定了！鸟哥喜欢使用“ now + ... ” 的方式来定义现在过多少时间再进行工作，但有时也需要定义特定的时间点来进行！下面的范例先看看啰！

```
范例一：再过五分钟后，将 /root/.bashrc 寄给 root 自己
[root@study ~]# at now + 5 minutes  &lt;==记得单位要加 s 喔！
at&gt; /bin/mail -s "testing at job" root &lt; /root/.bashrc
at&gt; &lt;EOT&gt;   &lt;==这里输入 [ctrl] + d 就会出现 &lt;EOF&gt; 的字样！代表结束！
job 2 at Thu Jul 30 19:35:00 2015
# 上面这行信息在说明，第 2 个 at 工作将在 2015/07/30 的 19:35 进行！
# 而执行 at 会进入所谓的 at shell 环境，让你下达多重指令等待运行！

范例二：将上述的第 2 项工作内容列出来查阅
[root@study ~]# at -c 2
#!/bin/sh               &lt;==就是通过 bash shell 的啦！
# atrun uid=0 gid=0
# mail root 0
umask 22
....（中间省略许多的环境变量项目）....
cd /etc/cron\.d &#124;&#124; {
         echo 'Execution directory inaccessible' &gt;&2
         exit 1
}
${SHELL:-/bin/sh} &lt;&lt; 'marcinDELIMITER410efc26'
/bin/mail -s "testing at job" root &lt; /root/.bashrc    # 这一行最重要！
marcinDELIMITER410efc26
# 你可以看到指令执行的目录 （/root），还有多个环境变量与实际的指令内容啦！

范例三：由于机房预计于 2015/08/05 停电，我想要在 2015/08/04 23:00 关机？
[root@study ~]# at 23:00 2015-08-04
at&gt; /bin/sync
at&gt; /bin/sync
at&gt; /sbin/shutdown -h now
at&gt; &lt;EOT&gt;
job 3 at Tue Aug  4 23:00:00 2015
# 您瞧瞧！ at 还可以在一个工作内输入多个指令呢！不错吧！
```

事实上，当我们使用 at 时会进入一个 at shell 的环境来让使用者下达工作指令，此时，建议你最好使用绝对路径来下达你的指令，比较不会有问题喔！由于指令的下达与 PATH 变量有关， 同时与当时的工作目录也有关连 （如果有牵涉到文件的话），因此使用绝对路径来下达指令，会是比较一劳永逸的方法。 为什么呢？举例来说，你在 /tmp 下达“ at now ”然后输入“ mail -s "test" root < .bashrc ”， 问一下，那个 .bashrc 的文件会是在哪里？答案是“ /tmp/.bashrc ”！因为 at 在运行时，会跑到当时下达 at 指令的那个工作目录的缘故啊！

有些朋友会希望“我要在某某时刻，在我的终端机显示出 Hello 的字样”，然后就在 at 里面下达这样的信息“ echo "Hello" ”。等到时间到了，却发现没有任何讯息在屏幕上显示，这是啥原因啊？这是因为 at 的执行与终端机环境无关，而所有 standard output/standard error output 都会传送到执行者的 mailbox 去啦！所以在终端机当然看不到任何信息。那怎办？没关系， 可以通过终端机的设备来处理！假如你在 tty1 登陆，则可以使用“ echo "Hello" > /dev/tty1 ”来取代。

![鸟哥的图示](image/vbird_face.gif)

**Tips** 要注意的是，如果在 at shell 内的指令并没有任何的讯息输出，那么 at 默认不会发 email 给执行者的。 如果你想要让 at 无论如何都发一封 email 告知你是否执行了指令，那么可以使用“ at -m 时间格式 ”来下达指令喔！ at 就会传送一个讯息给执行者，而不论该指令执行有无讯息输出了！

at 有另外一个很棒的优点，那就是“背景执行”的功能了！什么是背景执行啊？很难了解吗？其实与 bash 的 nohup （[第十六章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#nohup)） 类似啦！ 鸟哥提我自己的几个例子来给您听听，您就瞭了！

- 离线继续工作的任务：鸟哥初次接触 Unix 为的是要跑空气品质模式， 那是一种大型的程序，这个程序在当时的硬件下面跑，一个案例要跑 3 天！由于鸟哥也要进行其他研究工作，因此常常使用 Windows 98 （你没看错！鸟哥是老人...） 来连线到 Unix 工作站跑那个 3 天的案例！结果你也该知道， Windows 98 连开三天而不死机的概率是很低的～@_@～ 而死机时，所有在 Windows 上的连线都会中断！包括鸟哥在跑的那个程序也中断了～呜呜～明明再三个钟头就跑完的程序， 由于死机害我又得跑 3 天！
- 另一个常用的时刻则是例如上面的范例三，由于某个突发状况导致你必须要进行某项工作时，这个 at 就很好用啦！

由于 at 工作调度的使用上，系统会将该项 at 工作独立出你的 bash 环境中， 直接交给系统的 atd 程序来接管，因此，当你下达了 at 的工作之后就可以立刻离线了， 剩下的工作就完全交给 Linux 管理即可！所以啰，如果有长时间的网络工作时，嘿嘿！ 使用 at 可以让你免除网络断线后的困扰喔！ ^_^

- at 工作的管理

那么万一我下达了 at 之后，才发现指令输入错误，该如何是好？就将他移除啊！利用 atq 与 atrm 吧！

```
[root@study ~]# atq
[root@study ~]# atrm （jobnumber）

范例一：查询目前主机上面有多少的 at 工作调度？
[root@study ~]# atq
3       Tue Aug  4 23:00:00 2015 a root
# 上面说的是：“在 2015/08/04 的 23:00 有一项工作，该项工作指令下达者为 
# root”而且，该项工作的工作号码 （jobnumber） 为 3 号喔！

范例二：将上述的第 3 个工作移除！
[root@study ~]# atrm 3
[root@study ~]# atq
# 没有任何信息，表示该工作被移除了！
```

如此一来，你可以利用 atq 来查询，利用 atrm 来删除错误的指令，利用 at 来直接下达单一工作调度！很简单吧！ 不过，有个问题需要处理一下。如果你是在一个非常忙碌的系统下运行 at ， 能不能指定你的工作在系统较闲的时候才进行呢？可以的，那就使用 batch 指令吧！

- batch：系统有空时才进行背景任务

其实 batch 是利用 at 来进行指令的下达啦！只是加入一些控制参数而已。这个 batch 神奇的地方在于：他会在 CPU 的工作负载小于 0.8 的时候，才进行你所下达的工作任务啦！ 那什么是工作负载 0.8 呢？这个工作负载的意思是： CPU 在单一时间点所负责的工作数量。不是 CPU 的使用率喔！ 举例来说，如果我有一只程序他需要一直使用 CPU 的运算功能，那么此时 CPU 的使用率可能到达 100% ， 但是 CPU 的工作负载则是趋近于“ 1 ”，因为 CPU 仅负责一个工作嘛！如果同时执行这样的程序两支呢？ CPU 的使用率还是 100% ，但是工作负载则变成 2 了！了解乎？

所以也就是说，当 CPU 的工作负载越大，代表 CPU 必须要在不同的工作之间进行频繁的工作切换。 这样的 CPU 运行情况我们在第零章有谈过，忘记的话请回去瞧瞧！因为一直切换工作，所以会导致系统忙碌啊！ 系统如果很忙碌，还要额外进行 at ，不太合理！所以才有 batch 指令的产生！

在 CentOS 7 下面的 batch 已经不再支持时间参数了，因此 batch 可以拿来作为判断是否要立刻执行背景程序的依据！ 我们下面来实验一下 batch 好了！为了产生 CPU 较高的工作负载，因此我们用了 12 章里面计算 pi 的脚本，连续执行 4 次这只程序， 来仿真高负载，然后来玩一玩 batch 的工作现象：

```
范例一：请执行 pi 的计算，然后在系统闲置时，执行 updatdb 的任务
[root@study ~]# echo "scale=100000; 4*a（1）" &#124; bc -lq &
[root@study ~]# echo "scale=100000; 4*a（1）" &#124; bc -lq &
[root@study ~]# echo "scale=100000; 4*a（1）" &#124; bc -lq &
[root@study ~]# echo "scale=100000; 4*a（1）" &#124; bc -lq &
# 然后等待个大约数十秒的时间，之后再来确认一下工作负载的情况！
[root@study ~]# uptime
 19:56:45 up 2 days, 19:54,  2 users,  load average: 3.93, 2.23, 0.96

[root@study ~]# batch
at&gt; /usr/bin/updatedb
at&gt; &lt;EOT&gt;
job 4 at Thu Jul 30 19:57:00 2015

[root@study ~]# date;atq
Thu Jul 30 19:57:47 CST 2015
4       Thu Jul 30 19:57:00 2015 b root
# 可以看得到，明明时间已经超过了，却没有实际执行 at 的任务！

[root@study ~]# jobs
[1]   Running                 echo "scale=100000; 4*a（1）" &#124; bc -lq &
[2]   Running                 echo "scale=100000; 4*a（1）" &#124; bc -lq &
[3]-  Running                 echo "scale=100000; 4*a（1）" &#124; bc -lq &
[4]+  Running                 echo "scale=100000; 4*a（1）" &#124; bc -lq &
[root@study ~]# kill -9 %1 %2 %3 %4
# 这时先用 jobs 找出背景工作，再使用 kill 删除掉四个背景工作后，慢慢等待工作负载的下降

[root@study ~]# uptime; atq
 20:01:33 up 2 days, 19:59,  2 users,  load average: 0.89, 2.29, 1.40
4       Thu Jul 30 19:57:00 2015 b root
[root@study ~]# uptime; atq
 20:02:52 up 2 days, 20:01,  2 users,  load average: 0.23, 1.75, 1.28
# 在 19:59 时，由于 loading 还是高于 0.8，因此 atq 可以看得到 at job 还是持续再等待当中喔！
# 但是到了 20:01 时， loading 降低到 0.8 以下了，所以 atq 就执行完毕啰！
```

使用 uptime 可以观察到 1, 5, 15 分钟的“平均工作负载”量，因为是平均值，所以当我们如上表删除掉四个工作后，工作负载不会立即降低， 需要一小段时间让这个 1 分钟平均值慢慢回复到接近 0 啊！当小于 0.8 之后的“整分钟时间”时，atd 就会将 batch 的工作执行掉了！

什么是“整分钟时间”呢？不论是 at 还是下面要介绍的 crontab，他们最小的时间单位是“分钟”，所以，基本上，他们的工作是“每分钟检查一次”来处理的！ 就是整分 （秒为 0 的时候），这样了解乎？同时，你会发现其实 batch 也是使用 atq/atrm 来管理的！





## 15.3 循环执行的例行性工作调度

相对于`at`仅执行一次的工作，循环执行的例行性工作调度则由`cron`（`crond`）系统服务控制。Linux系统上默认启动`crond`服务，并提供`crontab`指令供用户设置例行性工作调度。以下分别介绍如何设置用户和系统的循环工作调度。

### 15.3.1 使用者的设置

用户可以使用`crontab`指令创建循环型工作调度。为确保安全，系统提供了限制用户使用`crontab`的文件：

- `/etc/cron.allow`：允许使用`crontab`的用户列表。不在此文件中的用户不能使用`crontab`。
- `/etc/cron.deny`：禁止使用`crontab`的用户列表。不在此文件中的用户可以使用`crontab`。

一般系统默认保留`/etc/cron.deny`文件，允许所有用户使用`crontab`。用户设置的工作会被记录在`/var/spool/cron/`目录下，以用户名为文件名存储。不要直接编辑这些文件，以避免语法错误导致`cron`无法执行。每项`cron`工作都会记录在`/var/log/cron`日志文件中。

#### crontab 指令

`crontab`的基本用法如下：

```
[root@study ~]# crontab [-u username] [-l|-e|-r]
选项与参数：
-u  ：指定用户名，仅 root 可使用，帮助其他用户创建或移除 crontab 工作调度；
-e  ：编辑 crontab 的工作内容；
-l  ：列出 crontab 的工作内容；
-r  ：移除所有的 crontab 工作内容，若仅移除一项，用 -e 编辑。
```

**示例**：用户`dmtsai`每天中午12点给自己发邮件。

```
[dmtsai@study ~]$ crontab -e
# 进入 vi 编辑画面，编辑工作内容，每项工作一行。
0 12 * * * mail -s "at 12:00" dmtsai < /home/dmtsai/.bashrc
```

每项工作格式为：

```
分 时 日 月 周 指令
```

**特殊字符**：

- `*`：表示任意时间点。
- `,`：分隔时段。
- `-`：表示时间范围。
- `/n`：表示每隔 n 单位时间执行。

**示例**：

- 每年5月1日23:59给女友发邮件：

```
59 23 1 5 * mail kiki < /home/dmtsai/lover.txt
```

- 每五分钟执行一次脚本：

```
*/5 * * * * /home/dmtsai/test.sh
```

- 每周五下午4:30提醒朋友周六的约会：

```
30 16 * * 5 mail [email protected] < /home/dmtsai/friend.txt
```

查看和移除`crontab`工作：

```
[dmtsai@study ~]$ crontab -l
[dmtsai@study ~]$ crontab -r
```

#### 原文：

使用者想要创建循环型工作调度时，使用的是 crontab 这个指令啦～不过，为了安全性的问题， 与 at 同样的，我们可以限制使用 crontab 的使用者帐号喔！使用的限制数据有：

- /etc/cron.allow： 将可以使用 crontab 的帐号写入其中，若不在这个文件内的使用者则不可使用 crontab；
- /etc/cron.deny： 将不可以使用 crontab 的帐号写入其中，若未记录到这个文件当中的使用者，就可以使用 crontab 。

与 at 很像吧！同样的，以优先顺序来说， /etc/cron.allow 比 /etc/cron.deny 要优先， 而判断上面，这两个文件只选择一个来限制而已，因此，建议你只要保留一个即可， 免得影响自己在设置上面的判断！一般来说，系统默认是保留 /etc/cron.deny ， 你可以将不想让他执行 crontab 的那个使用者写入 /etc/cron.deny 当中，一个帐号一行！

当使用者使用 crontab 这个指令来创建工作调度之后，该项工作就会被纪录到 /var/spool/cron/ 里面去了，而且是以帐号来作为判别的喔！举例来说， dmtsai 使用 crontab 后， 他的工作会被纪录到 /var/spool/cron/dmtsai 里头去！但请注意，不要使用 vi 直接编辑该文件， 因为可能由于输入语法错误，会导致无法执行 cron 喔！另外， cron 执行的每一项工作都会被纪录到 /var/log/cron 这个登录文件中，所以啰，如果你的 Linux 不知道有否被植入木马时，也可以搜寻一下 /var/log/cron 这个登录文件呢！

好了，那么我们就来聊一聊 crontab 的语法吧！

```
[root@study ~]# crontab [-u username] [-l&#124;-e&#124;-r]
选项与参数：
-u  ：只有 root 才能进行这个任务，亦即帮其他使用者创建/移除 crontab 工作调度；
-e  ：编辑 crontab 的工作内容
-l  ：查阅 crontab 的工作内容
-r  ：移除所有的 crontab 的工作内容，若仅要移除一项，请用 -e 去编辑。

范例一：用 dmtsai 的身份在每天的 12:00 发信给自己
[dmtsai@study ~]$ crontab -e
# 此时会进入 vi 的编辑画面让您编辑工作！注意到，每项工作都是一行。
0   12  *  *  * mail -s "at 12:00" dmtsai &lt; /home/dmtsai/.bashrc
#分 时 日 月 周 &#124;&lt;==============指令串========================&gt;&#124;
```

默认情况下，任何使用者只要不被列入 /etc/cron.deny 当中，那么他就可以直接下达“ crontab -e ”去编辑自己的例行性命令了！整个过程就如同上面提到的，会进入 vi 的编辑画面， 然后以一个工作一行来编辑，编辑完毕之后输入“ :wq ”储存后离开 vi 就可以了！ 而每项工作 （每行） 的格式都是具有六个字段，这六个字段的意义为：

| 代表意义 | 分钟 | 小时 | 日期 | 月份 | 周   | 指令       |
| -------- | ---- | ---- | ---- | ---- | ---- | ---------- |
| 数字范围 | 0-59 | 0-23 | 1-31 | 1-12 | 0-7  | 呀就指令啊 |

比较有趣的是那个“周”喔！周的数字为 0 或 7 时，都代表“星期天”的意思！另外，还有一些辅助的字符，大概有下面这些：

| 特殊字符   | 代表意义                                                     |
| ---------- | ------------------------------------------------------------ |
| *（星号）  | 代表任何时刻都接受的意思！举例来说，范例一内那个日、月、周都是 * ， 就代表着“不论何月、何日的礼拜几的 12:00 都执行后续指令”的意思！ |
| ,（逗号）  | 代表分隔时段的意思。举例来说，如果要下达的工作是 3:00 与 6:00 时，就会是： `> 0 3,6 * * * command` 时间参数还是有五栏，不过第二栏是 3,6 ，代表 3 与 6 都适用！ |
| -（减号）  | 代表一段时间范围内，举例来说， 8 点到 12 点之间的每小时的 20 分都进行一项工作： `> 20 8-12 * * * command` 仔细看到第二栏变成 8-12 喔！代表 8,9,10,11,12 都适用的意思！ |
| /n（斜线） | 那个 n 代表数字，亦即是“每隔 n 单位间隔”的意思，例如每五分钟进行一次，则： `> */5 * * * * command` 很简单吧！用 * 与 /5 来搭配，也可以写成 0-59/5 ，相同意思！ |

我们就来搭配几个例子练习看看吧！下面的案例请实际用 dmtsai 这个身份作看看喔！后续的动作才能够搭配起来！

例题：假若你的女朋友生日是 5 月 2 日，你想要在 5 月 1 日的 23:59 发一封信给他，这封信的内容已经写在 /home/dmtsai/lover.txt 内了，该如何进行？答：直接下达 crontab -e 之后，编辑成为：

```
> 59 23 1 5 * mail kiki &lt; /home/dmtsai/lover.txt
```

那样的话，每年 kiki 都会收到你的这封信喔！（当然啰，信的内容就要每年变一变啦！）

例题：假如每五分钟需要执行 /home/dmtsai/test.sh 一次，又该如何？答：同样使用 crontab -e 进入编辑：

```
> */5 * * * * /home/dmtsai/test.sh
```

那个 crontab 每个人都只有一个文件存在，就是在 /var/spool/cron 里面啊！ 还有建议您：“指令下达时，最好使用绝对路径，这样比较不会找不到可执行文件喔！”

例题：假如你每星期六都与朋友有约，那么想要每个星期五下午 4:30 告诉你朋友星期六的约会不要忘记，则：答：还是使用 crontab -e 啊！

```
> 30 16 * * 5 mail [email protected] &lt; /home/dmtsai/friend.txt
```

真的是很简单吧！呵呵！那么，该如何查询使用者目前的 crontab 内容呢？我们可以这样来看看：

```
[dmtsai@study ~]$ crontab -l
0 12 * * * mail -s "at 12:00" dmtsai &lt; /home/dmtsai/.bashrc
59 23 1 5 * mail kiki &lt; /home/dmtsai/lover.txt
*/5 * * * * /home/dmtsai/test.sh
30 16 * * 5 mail [email protected] &lt; /home/dmtsai/friend.txt

# 注意，若仅想要移除一项工作而已的话，必须要用 crontab -e 去编辑～
# 如果想要全部的工作都移除，才使用 crontab -r 喔！
[dmtsai@study ~]$ crontab -r
[dmtsai@study ~]$ crontab -l
no crontab for dmtsai
```

看到了吗？ crontab “整个内容都不见了！”所以请注意：“如果只是要删除某个 crontab 的工作项目，那么请使用 crontab -e 来重新编辑即可！”如果使用 -r 的参数，是会将所有的 crontab 数据内容都删掉的！千万注意了！



### 15.3.2 系统的配置文件：/etc/crontab, /etc/cron.d/*

`crontab -e`用于用户的`cron`设置，系统的例行性任务可通过编辑`/etc/crontab`文件实现。`/etc/crontab`是纯文本文件，可以直接用`vim`编辑。

**/etc/crontab**的内容：

```
[root@study ~]# cat /etc/crontab
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
```

**关键字段**：

- `MAILTO=root`：指定出错或有输出时，邮件发送的目标。
- `PATH=....`：可执行文件路径。
- `分 时 日 月 周 用户名 指令`：七个字段，指定时间、执行用户和指令。

`crond`服务读取配置文件的位置：

- `/etc/crontab`
- `/etc/cron.d/*`
- `/var/spool/cron/*`

**/etc/cron.d**目录中的文件也包含`cron`配置。可以在该目录中添加新文件，设置新的定时任务。例如：

```
[root@study ~]# cat /etc/cron.d/0hourly
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
01 * * * * root run-parts /etc/cron.hourly
```

**系统定时任务目录**：

- `/etc/cron.hourly/`
- `/etc/cron.daily/`
- `/etc/cron.weekly/`
- `/etc/cron.monthly/`

这些目录下的脚本分别在每小时、每天、每周和每月执行。

#### 原文：

这个“ crontab -e ”是针对使用者的 cron 来设计的，如果是“系统的例行性任务”时， 该怎么办呢？是否还是需要以 crontab -e 来管理你的例行性工作调度呢？当然不需要，你只要编辑 /etc/crontab 这个文件就可以啦！有一点需要特别注意喔！那就是 crontab -e 这个 crontab 其实是 /usr/bin/crontab 这个可执行文件，但是 /etc/crontab 可是一个“纯文本文件”喔！你可以 root 的身份编辑一下这个文件哩！

基本上， cron 这个服务的最低侦测限制是“分钟”，所以“ cron 会每分钟去读取一次 /etc/crontab 与 /var/spool/cron 里面的数据内容 ”，因此，只要你编辑完 /etc/crontab 这个文件，并且将他储存之后，那么 cron 的设置就自动的会来执行了！

![鸟哥的图示](image/vbird_face-17228270224982.gif)

**Tips** 在 Linux 下面的 crontab 会自动的帮我们每分钟重新读取一次 /etc/crontab 的例行工作事项，但是某些原因或者是其他的 Unix 系统中，由于 crontab 是读到内存当中的，所以在你修改完 /etc/crontab 之后，可能并不会马上执行， 这个时候请重新启动 crond 这个服务吧！“systemctl restart crond”

废话少说，我们就来看一下这个 /etc/crontab 的内容吧！

```
[root@study ~]# cat /etc/crontab
SHELL=/bin/bash                     &lt;==使用哪种 shell 接口
PATH=/sbin:/bin:/usr/sbin:/usr/bin  &lt;==可执行文件搜寻路径
MAILTO=root                         &lt;==若有额外STDOUT，以 email将数据送给谁

# Example of job definition:
# .---------------- minute （0 - 59）
# &#124;  .------------- hour （0 - 23）
# &#124;  &#124;  .---------- day of month （1 - 31）
# &#124;  &#124;  &#124;  .------- month （1 - 12） OR jan,feb,mar,apr ...
# &#124;  &#124;  &#124;  &#124;  .---- day of week （0 - 6） （Sunday=0 or 7） OR sun,mon,tue,wed,thu,fri,sat
# &#124;  &#124;  &#124;  &#124;  &#124;
# *  *  *  *  * user-name  command to be executed
```

看到这个文件的内容你大概就了解了吧！呵呵，没错！这个文件与将刚刚我们下达 crontab -e 的内容几乎完全一模一样！只是有几个地方不太相同：

- MAILTO=root：

这个项目是说，当 /etc/crontab 这个文件中的例行性工作的指令发生错误时，或者是该工作的执行结果有 STDOUT/STDERR 时，会将错误讯息或者是屏幕显示的讯息传给谁？默认当然是由系统直接寄发一封 mail 给 root 啦！不过， 由于 root 并无法在用户端中以 POP3 之类的软件收信，因此，鸟哥通常都将这个 e-mail 改成自己的帐号，好让我随时了解系统的状况！例如： [[email protected\]](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection)

- PATH=....：

还记得我们在[第十章的 BASH](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#env) 当中一直提到的可执行文件路径问题吧！ 没错啦！这里就是输入可执行文件的搜寻路径！使用默认的路径设置就已经很足够了！

- “分 时 日 月 周 身份 指令”七个字段的设置

这个 /etc/crontab 里面可以设置的基本语法与 crontab -e 不太相同喔！前面同样是分、时、日、月、周五个字段， 但是在五个字段后面接的并不是指令，而是一个新的字段，那就是“执行后面那串指令的身份”为何！这与使用者的 crontab -e 不相同。由于使用者自己的 crontab 并不需要指定身份，但 /etc/crontab 里面当然要指定身份啦！以上表的内容来说，系统默认的例行性工作是以 root 的身份来进行的。

- crond 服务读取配置文件的位置

一般来说，crond 默认有三个地方会有执行脚本配置文件，他们分别是：

- /etc/crontab
- /etc/cron.d/*
- /var/spool/cron/*

这三个地方中，跟系统的运行比较有关系的两个配置文件是放在 /etc/crontab 文件内以及 /etc/cron.d/* 目录内的文件， 另外一个是跟用户自己的工作比较有关的配置文件，就是放在 /var/spool/cron/ 里面的文件群。 现在我们已经知道了 /var/spool/cron 以及 /etc/crontab 的内容，那现在来瞧瞧 /etc/cron.d 里面的东西吧！

```
[root@study ~]# ls -l /etc/cron.d
-rw-r--r--. 1 root root 128 Jul 30  2014 0hourly
-rw-r--r--. 1 root root 108 Mar  6 10:12 raid-check
-rw-------. 1 root root 235 Mar  6 13:45 sysstat
-rw-r--r--. 1 root root 187 Jan 28  2014 unbound-anchor
# 其实说真的，除了 /etc/crontab 之外，crond 的配置文件还不少耶！上面就有四个设置！
# 先让我们来瞧瞧 0hourly 这个配置文件的内容吧！

[root@study ~]# cat /etc/cron.d/0hourly
# Run the hourly jobs
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
01 * * * * root run-parts /etc/cron.hourly
# 瞧一瞧，内容跟 /etc/crontab 几乎一模一样！但实际上是有设置值喔！就是最后一行！
```

如果你想要自己开发新的软件，该软件要拥有自己的 crontab 定时指令时，就可以将“分、时、日、月、周、身份、指令”的配置文件放置到 /etc/cron.d/ 目录下！ 在此目录下的文件是“crontab 的配置文件脚本”。

![鸟哥的图示](image/vbird_face-17228270224982.gif)

**Tips** 以鸟哥来说，现在鸟哥有在开发一些虚拟化教室的软件，该软件需要定时清除一些垃圾防火墙规则， 那鸟哥就是将要执行的时间与指令设计好，然后直接将设置写入到 /etc/cron.d/newfile 即可！未来如果这个软件要升级， 直接将该文件覆盖成新文件即可！比起手动去分析 /etc/crontab 要单纯的多！

另外，请注意一下上面表格中提到的最后一行，每个整点的一分会执行“ run-parts /etc/cron.hourly ”这个指令～咦！那什么是 run-parts 呢？ 如果你有去分析一下这个可执行文件，会发现他就是 shell script，run-parts 脚本会在大约 5 分钟内随机选一个时间来执行 /etc/cron.hourly 目录内的所有可执行文件！因此，放在 /etc/cron.hourly/ 的文件，必须是能被直接执行的指令脚本， 而不是分、时、日、月、周的设置值喔！注意注意！

也就是说，除了自己指定分、时、日、月、周加上指令路径的 crond 配置文件之外，你也可以直接将指令放置到（或链接到）/etc/cron.hourly/ 目录下， 则该指令就会被 crond 在每小时的 1 分开始后的 5 分钟内，随机取一个时间点来执行啰！你无须手动去指定分、时、日、月、周就是了。

但是眼尖的朋友可能还会发现，除了可以直接将指令放到 /etc/cron.hourly/ 让系统每小时定时执行之外，在 /etc/ 下面其实还有 /etc/cron.daily/, /etc/cron.weekly/, /etc/cron.monthly/，那三个目录是代表每日、每周、每月各执行一次的意思吗？嘿嘿！ 厉害喔！没错～是这样～不过，跟 /etc/cron.hourly/ 不太一样的是，那三个目录是由 anacron 所执行的，而 anacron 的执行方式则是放在 /etc/cron.hourly/0anacron 里面耶～跟前几代 anacron 是单独的 service 不太一样喔！这部份留待下个小节再来讨论。

最后，让我们总结一下吧：

- 个人化的行为使用“ crontab -e ”：如果你是依据个人需求来创建的例行工作调度，建议直接使用 crontab -e 来创建你的工作调度较佳！ 这样也能保障你的指令行为不会被大家看到 （/etc/crontab 是大家都能读取的权限喔！）；
- 系统维护管理使用“ vim /etc/crontab ”：如果你这个例行工作调度是系统的重要工作，为了让自己管理方便，同时容易追踪，建议直接写入 /etc/crontab 较佳！
- 自己开发软件使用“ vim /etc/cron.d/newfile ”：如果你是想要自己开发软件，那当然最好就是使用全新的配置文件，并且放置于 /etc/cron.d/ 目录内即可。
- 固定每小时、每日、每周、每天执行的特别工作：如果与系统维护有关，还是建议放置到 /etc/crontab 中来集中管理较好。 如果想要偷懒，或者是一定要再某个周期内进行的任务，也可以放置到上面谈到的几个目录中，直接写入指令即可！





### 15.3.3 一些注意事项

使用`cron`进行例行性工作调度时，应注意以下几点：

- **资源分配不均的问题**：避免在同一时间点执行大量任务。例如，将每五分钟执行的四个任务分散到不同的时间点：

```
1,6,11,16,21,26,31,36,41,46,51,56 * * * * root CMD1
2,7,12,17,22,27,32,37,42,47,52,57 * * * * root CMD2
3,8,13,18,23,28,33,38,43,48,53,58 * * * * root CMD3
4,9,14,19,24,29,34,39,44,49,54,59 * * * * root CMD4
```

- **取消不必要的输出**：将输出重定向到`/dev/null`：

```
* * * * * CMD > /dev/null 2>&1
```

- **安全检验**：检查`/var/log/cron`文件，确保没有被植入木马。
- **周与日月不可同时并存**：不要同时设置周和日月：

```
30 12 11 9 5 root echo "just test"   # 错误的写法
```

通过以上设置和注意事项，可以有效地管理和执行循环性的例行工作调度。

#### 原文：

有的时候，我们以系统的 cron 来进行例行性工作的创建时，要注意一些使用方面的特性。 举例来说，如果我们有四个工作都是五分钟要进行一次的，那么是否这四个动作全部都在同一个时间点进行？ 如果同时进行，该四个动作又很耗系统资源，如此一来，每五分钟的某个时刻不是会让系统忙得要死？ 呵呵！此时好好的分配一些执行时间就 OK 啦！所以，注意一下：

- 资源分配不均的问题

当大量使用 crontab 的时候，总是会有问题发生的，最严重的问题就是“系统资源分配不均”的问题， 以鸟哥的系统为例，我有侦测主机流量的信息，包括：

- 流量
- 区域内其他 PC 的流量侦测
- CPU 使用率
- RAM 使用率
- 线上人数实时侦测

如果每个流程都在同一个时间启动的话，那么在某个时段时，我的系统会变的相当的繁忙，所以，这个时候就必须要分别设置啦！我可以这样做：

```
[root@study ~]# vim /etc/crontab
1,6,11,16,21,26,31,36,41,46,51,56 * * * * root  CMD1
2,7,12,17,22,27,32,37,42,47,52,57 * * * * root  CMD2
3,8,13,18,23,28,33,38,43,48,53,58 * * * * root  CMD3
4,9,14,19,24,29,34,39,44,49,54,59 * * * * root  CMD4
```

看到了没？那个“ , ”分隔的时候，请注意，不要有空白字符！（连续的意思）如此一来， 则可以将每五分钟工作的流程分别在不同的时刻来工作！则可以让系统的执行较为顺畅呦！

- 取消不要的输出项目

另外一个困扰发生在“ 当有执行成果或者是执行的项目中有输出的数据时，该数据将会 mail 给 MAILTO 设置的帐号 ”，好啦，那么当有一个调度一直出错（例如 DNS 的侦测系统当中，若 DNS 上层主机挂掉，那么你就会一直收到错误讯息！）怎么办？呵呵！还记得[第十章谈到的数据流重导向](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#redirect)吧？ 直接以“数据流重导向”将输出的结果输出到 /dev/null 这个垃圾桶当中就好了！

- 安全的检验

很多时候被植入木马都是以例行命令的方式植入的，所以可以借由检查 /var/log/cron 的内容来视察是否有“非您设置的 cron 被执行了？”这个时候就需要小心一点啰！

- 周与日月不可同时并存

另一个需要注意的地方在于：“你可以分别以周或者是日月为单位作为循环，但你不可使用「几月几号且为星期几」的模式工作”。 这个意思是说，你不可以这样编写一个工作调度：

```
30 12 11 9 5 root echo "just test"   &lt;==这是错误的写法
```

本来你以为九月十一号且为星期五才会进行这项工作，无奈的是，系统可能会判定每个星期五作一次，或每年的 9 月 11 号分别进行，如此一来与你当初的规划就不一样了～所以啰，得要注意这个地方！

![鸟哥的图示](image/vbird_face-17228271864944.gif)

**Tips** 根据某些人的说法，这个月日、周不可并存的问题已经在新版中被克服了～不过，鸟哥并没有实际去验证他！目前也不打算验证他！ 因为，周就是周，月日就月日，单一执行点就单一执行点，无须使用 crontab 去设置固定的日期啊！您说是吧？





## 15.4 可唤醒停机期间的工作任务

在某些情况下，如星期六停电导致系统关机，星期天凌晨的例行工作调度将不会被执行。为了确保重要任务（如例行备份）在下次开机时仍能执行，`anacron`提供了解决方案。`anacron`可以在系统启动后自动执行未能按时执行的任务。

### 15.4.1 什么是 anacron

`anacron`的存在目的是弥补非24小时持续运行的Linux系统的工作调度缺失。它并非取代`crontab`，而是在特定条件下补充执行未完成的调度任务。`anacron`每小时由`crond`执行一次，检测并执行超过期限但未完成的调度任务，然后停止。

`anacron`默认以天、周、月为单位检测系统未执行的`crontab`任务，对于非24小时运行的系统尤其有用。

#### 原文：

anacron 并不是用来取代 crontab 的，anacron 存在的目的就在于我们上头提到的，在处理非 24 小时一直启动的 Linux 系统的 crontab 的执行！ 以及因为某些原因导致的超过时间而没有被执行的调度工作。

其实 anacron 也是每个小时被 crond 执行一次，然后 anacron 再去检测相关的调度任务有没有被执行，如果有超过期限的工作在， 就执行该调度任务，执行完毕或无须执行任何调度时，anacron 就停止了。

由于 anacron 默认会以一天、七天、一个月为期去侦测系统未进行的 crontab 任务，因此对于某些特殊的使用环境非常有帮助。 举例来说，如果你的 Linux 主机是放在公司给同仁使用的，因为周末假日大家都不在所以也没有必要打开， 因此你的 Linux 是周末都会关机两天的。但是 crontab 大多在每天的凌晨以及周日的早上进行各项任务， 偏偏你又关机了，此时系统很多 crontab 的任务就无法进行。 anacron 刚好可以解决这个问题！

那么 anacron 又是怎么知道我们的系统啥时关机的呢？这就得要使用 anacron 读取的时间记录文件 （timestamps） 了！ anacron 会去分析现在的时间与时间记录文件所记载的上次执行 anacron 的时间，两者比较后若发现有差异， 那就是在某些时刻没有进行 crontab 啰！此时 anacron 就会开始执行未进行的 crontab 任务了！





### 15.4.2 anacron 与 /etc/anacrontab

`anacron`是一个程序，而非服务。CentOS中`anacron`每小时被`crontab`调用执行。其配置文件位于`/etc/cron.hourly/0anacron`：

```
[root@study ~]# cat /etc/cron.hourly/0anacron
#!/bin/sh
# Check whether 0anacron was run today already
if test -r /var/spool/anacron/cron.daily; then
    day=`cat /var/spool/anacron/cron.daily`
fi
if [ `date +%Y%m%d` = "$day" ]; then
    exit 0;
fi
# Do not run jobs when on battery power
if test -x /usr/bin/on_ac_power; then
    /usr/bin/on_ac_power >/dev/null 2>&1
    if test $? -eq 1; then
    exit 0
    fi
fi
/usr/sbin/anacron -s
```

`anacron`的基本语法如下：

```
[root@study ~]# anacron [-sfn] [job]..
[root@study ~]# anacron -u [job]..
选项与参数：
-s  ：开始执行各项工作，依据时间记录文件判断是否进行；
-f  ：强制执行，不判断时间记录文件；
-n  ：立刻执行未执行的任务，不延迟等待时间；
-u  ：仅更新时间记录文件，不进行任何工作。
job ：由 /etc/anacrontab 定义的各项工作名称。
```

`/etc/anacrontab`文件内容：

```
[root@study ~]# cat /etc/anacrontab
SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
RANDOM_DELAY=45           # 随机延迟时间，单位分钟
START_HOURS_RANGE=3-22    # 延迟执行任务的小时范围

1         5        cron.daily         nice run-parts /etc/cron.daily
7        25        cron.weekly        nice run-parts /etc/cron.weekly
@monthly 45        cron.monthly       nice run-parts /etc/cron.monthly
# 天数     延迟时间  工作名称定义       实际执行的指令串
```

时间记录文件（位于/var/spool/anacron/）记录上次执行时间：

```
[root@study ~]# more /var/spool/anacron/*
::::::::::::::
/var/spool/anacron/cron.daily
::::::::::::::
20150731
::::::::::::::
/var/spool/anacron/cron.monthly
::::::::::::::
20150703
::::::::::::::
/var/spool/anacron/cron.weekly
::::::::::::::
20150727
```

#### 原文：

anacron 其实是一支程序并非一个服务！这支程序在 CentOS 当中已经进入 crontab 的调度喔！同时 anacron 会每个小时被主动执行一次喔！ 咦！每个小时？所以 anacron 的配置文件应该放置在 /etc/cron.hourly 吗？嘿嘿！您真内行～赶紧来瞧一瞧：

```
[root@study ~]# cat /etc/cron.hourly/0anacron
#!/bin/sh
# Check whether 0anacron was run today already
if test -r /var/spool/anacron/cron.daily; then
    day=`cat /var/spool/anacron/cron.daily`
fi
if [ `date +%Y%m%d` = "$day" ]; then
    exit 0;
fi
# 上面的语法在检验前一次执行 anacron 时的时间戳记！

# Do not run jobs when on battery power
if test -x /usr/bin/on_ac_power; then
    /usr/bin/on_ac_power &gt;/dev/null 2&gt;&1
    if test $? -eq 1; then
    exit 0
    fi
fi
/usr/sbin/anacron -s
# 所以其实也仅是执行 anacron -s 的指令！因此我们得来谈谈这支程序！
```

基本上， anacron 的语法如下：

```
[root@study ~]# anacron [-sfn] [job]..
[root@study ~]# anacron -u [job]..
选项与参数：
-s  ：开始一连续的执行各项工作 （job），会依据时间记录文件的数据判断是否进行；
-f  ：强制进行，而不去判断时间记录文件的时间戳记；
-n  ：立刻进行未进行的任务，而不延迟 （delay） 等待时间；
-u  ：仅更新时间记录文件的时间戳记，不进行任何工作。
job ：由 /etc/anacrontab 定义的各项工作名称。
```

在我们的 CentOS 中，anacron 的进行其实是在每个小时都会被抓出来执行一次， 但是为了担心 anacron 误判时间参数，因此 /etc/cron.hourly/ 里面的 anacron 才会在文件名之前加个 0 （0anacron），让 anacron 最先进行！就是为了让时间戳记先更新！以避免 anacron 误判 crontab 尚未进行任何工作的意思。

接下来我们看一下 anacron 的配置文件： /etc/anacrontab 的内容好了：

```
[root@study ~]# cat /etc/anacrontab
SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
RANDOM_DELAY=45           # 随机给予最大延迟时间，单位是分钟
START_HOURS_RANGE=3-22    # 延迟多少个小时内应该要执行的任务时间

1         5        cron.daily         nice run-parts /etc/cron.daily
7        25        cron.weekly        nice run-parts /etc/cron.weekly
@monthly 45        cron.monthly       nice run-parts /etc/cron.monthly
天数     延迟时间  工作名称定义       实际要进行的指令串
# 天数单位为天；延迟时间单位为分钟；工作名称定义可自订，指令串则通常与 crontab 的设置相同！

[root@study ~]# more /var/spool/anacron/*
::::::::::::::
/var/spool/anacron/cron.daily
::::::::::::::
20150731
::::::::::::::
/var/spool/anacron/cron.monthly
::::::::::::::
20150703
::::::::::::::
/var/spool/anacron/cron.weekly
::::::::::::::
20150727
# 上面则是三个工作名称的时间记录文件以及记录的时间戳记
```

我们拿 /etc/cron.daily/ 那一行的设置来说明好了。那四个字段的意义分别是：

- 天数：anacron 执行当下与时间戳记 （/var/spool/anacron/ 内的时间纪录档） 相差的天数，若超过此天数，就准备开始执行，若没有超过此天数，则不予执行后续的指令。
- 延迟时间：若确定超过天数导致要执行调度工作了，那么请延迟执行的时间，因为担心立即启动会有其他资源冲突的问题吧！
- 工作名称定义：这个没啥意义，就只是会在 /var/log/cron 里头记载该项任务的名称这样！通常与后续的目录资源名称相同即可。
- 实际要进行的指令串：有没有跟 0hourly 很像啊！没错！相同的作法啊！通过 run-parts 来处理的！

根据上面的配置文件内容，我们大概知道 anacron 的执行流程应该是这样的 （以 cron.daily 为例）：

1. 由 /etc/anacrontab 分析到 cron.daily 这项工作名称的天数为 1 天；
2. 由 /var/spool/anacron/cron.daily 取出最近一次执行 anacron 的时间戳记；
3. 由上个步骤与目前的时间比较，若差异天数为 1 天以上 （含 1 天），就准备进行指令；
4. 若准备进行指令，根据 /etc/anacrontab 的设置，将延迟 5 分钟 + 3 小时 （看 START_HOURS_RANGE 的设置）；
5. 延迟时间过后，开始执行后续指令，亦即“ run-parts /etc/cron.daily ”这串指令；
6. 执行完毕后， anacron 程序结束。

如此一来，放置在 /etc/cron.daily/ 内的任务就会在一天后一定会被执行的！因为 anacron 是每个小时被执行一次嘛！ 所以，现在你知道为什么隔了一阵子才将 CentOS 开机，开机过后约 1 小时左右系统会有一小段时间的忙碌！而且硬盘会跑个不停！那就是因为 anacron 正在执行过去 /etc/cron.daily/, /etc/cron.weekly/, /etc/cron.monthly/ 里头的未进行的各项工作调度啦！这样对 anacron 有没有概念了呢？ ^_^

最后，我们来总结一下本章谈到的许多配置文件与目录的关系吧！这样我们才能了解 crond 与 anacron 的关系：

1. crond 会主动去读取 /etc/crontab, /var/spool/cron/*, /etc/cron.d/* 等配置文件，并依据“分、时、日、月、周”的时间设置去各项工作调度；
2. 根据 /etc/cron.d/0hourly 的设置，主动去 /etc/cron.hourly/ 目录下，执行所有在该目录下的可执行文件；
3. 因为 /etc/cron.hourly/0anacron 这个指令档的缘故，主动的每小时执行 anacron ，并调用 /etc/anacrontab 的配置文件；
4. 根据 /etc/anacrontab 的设置，依据每天、每周、每月去分析 /etc/cron.daily/, /etc/cron.weekly/, /etc/cron.monthly/ 内的可执行文件，以进行固定周期需要执行的指令。

也就是说，如果你每个周日的需要执行的动作是放置于 /etc/crontab 的话，那么该动作只要过期了就过期了，并不会被抓回来重新执行。但如果是放置在 /etc/cron.weekly/ 目录下，那么该工作就会定期，几乎一定会在一周内执行一次～如果你关机超过一周，那么一开机后的数个小时内，该工作就会主动的被执行喔！ 真的吗？对啦！因为 /etc/anacrontab 的定义啦！

![鸟哥的图示](image/vbird_face-17228272453786.gif)

**Tips** 基本上，crontab 与 at 都是“定时”去执行，过了时间就过了！不会重新来一遍～那 anacron 则是“定期”去执行，某一段周期的执行～ 因此，两者可以并行，并不会互相冲突啦！



### 15.4.3 anacron 执行流程

1. 由`/etc/anacrontab`分析工作名称的天数。
2. 读取`/var/spool/anacron/`内对应时间记录文件的时间戳记。
3. 比较当前时间与时间戳记，若超过设定天数，准备执行任务。
4. 延迟执行，避免资源冲突。
5. 执行实际指令，如`run-parts /etc/cron.daily`。
6. 任务完成，更新时间戳记。

### 15.4.4 总结

`crond`和`anacron`的关系：

1. `crond`读取`/etc/crontab`、`/var/spool/cron/*`、`/etc/cron.d/*`等配置文件，执行周期性任务。
2. 根据`/etc/cron.d/0hourly`的设置，执行`/etc/cron.hourly/`目录下的脚本。
3. `anacron`每小时被`/etc/cron.hourly/0anacron`调用，读取`/etc/anacrontab`的配置。
4. `anacron`根据配置文件，检测并执行未完成的`cron.daily`、`cron.weekly`、`cron.monthly`任务。

`crontab`和`at`是定时执行，错过时间不会重新执行。`anacron`则是定期执行，确保任务在设定周期内完成。两者可以并行运行，不会互相冲突。

## 15.5 重点回顾

- 系统可以通过 at 这个指令来调度单一工作的任务！“at TIME”为指令下达的方法，当 at 进入调度后， 系统执行该调度工作时，会到下达时的目录进行任务；
- at 的执行必须要有 atd 服务的支持，且 /etc/at.deny 为控制是否能够执行的使用者帐号；
- 通过 atq, atrm 可以查询与删除 at 的工作调度；
- batch 与 at 相同，不过 batch 可在 CPU 工作负载小于 0.8 时才进行后续的工作调度；
- 系统的循环例行性工作调度使用 crond 这个服务，同时利用 crontab -e 及 /etc/crontab 进行调度的安排；
- crontab -e 设置项目分为六栏，“分、时、日、月、周、指令”为其设置依据；
- /etc/crontab 设置分为七栏，“分、时、日、月、周、执行者、指令”为其设置依据；
- anacron 配合 /etc/anacrontab 的设置，可以唤醒停机期间系统未进行的 crontab 任务！