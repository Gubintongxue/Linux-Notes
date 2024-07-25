# 第十五章、例行性工作调度（crontab）

最近更新日期：20//

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

`crontab`用于设置循环执行的任务，时间周期可以是分钟、小时、每天、每周、每月或每年。`crontab`可以通过指令执行，也可以编辑`/etc/crontab`来支持。`crond`服务支持`crontab`的生效。

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

## 15.2 仅执行一次的工作调度

在Linux系统中，可以通过`at`指令来实现单一工作调度。下面介绍如何启动`atd`服务并使用`at`进行工作调度。

### 15.2.1 atd 的启动与 at 运行的方式

要使用单一工作调度，必须确保`atd`服务已经启动。可以通过以下命令启动和管理`atd`服务：

```
bash复制代码[root@study ~]# systemctl restart atd  # 重新启动 atd 服务
[root@study ~]# systemctl enable atd   # 设置 atd 服务开机自动启动
[root@study ~]# systemctl status atd   # 查看 atd 服务的状态
```

确认`atd`服务运行正常的关键在于看到`enabled`和`running`状态。

#### at 的运行方式

`at`指令用于产生工作任务，并将这些任务以文本文件的形式写入`/var/spool/at/`目录，等待`atd`服务执行。并非所有用户都可以使用`at`，需要通过`/etc/at.allow`和`/etc/at.deny`文件进行权限控制。

- **/etc/at.allow**：只允许文件中列出的用户使用`at`。
- **/etc/at.deny**：文件中列出的用户不能使用`at`。
- 如果这两个文件都不存在，则只有`root`用户可以使用`at`。

### 15.2.2 实际运行单一工作调度

使用`at`指令可以简单地进行单一工作调度。基本语法如下：

```
bash复制代码[root@study ~]# at [-mldv] TIME
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
bash复制代码[root@study ~]# at now + 5 minutes
at> /bin/mail -s "testing at job" root < /root/.bashrc
at> <EOT>  # 输入 [ctrl] + d 结束
job 2 at Thu Jul 30 19:35:00 2015
```

**示例2**：查看上述第2个工作的内容。

```
bash复制代码[root@study ~]# at -c 2
#!/bin/sh
# atrun uid=0 gid=0
# mail root 0
umask 22
...
/bin/mail -s "testing at job" root < /root/.bashrc
```

**示例3**：在2015年8月4日23:00关机。

```
bash复制代码[root@study ~]# at 23:00 2015-08-04
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
bash复制代码[root@study ~]# atq
3       Tue Aug  4 23:00:00 2015 a root
```

**示例2**：移除上述第3个任务。

```
bash复制代码[root@study ~]# atrm 3
[root@study ~]# atq
# 无输出表示任务已移除
```

### batch：系统有空时才进行背景任务

`batch`指令是`at`的一种扩展，用于在系统负载较低时执行任务。`batch`会在CPU负载小于0.8时才执行任务。

**示例**：使用`batch`在系统空闲时执行`updatedb`任务。

```
bash复制代码[root@study ~]# batch
at> /usr/bin/updatedb
at> <EOT>
job 4 at Thu Jul 30 19:57:00 2015
```

使用`uptime`命令查看系统负载，确认`batch`任务在系统负载低于0.8时才执行。

```
bash复制代码[root@study ~]# uptime
19:56:45 up 2 days, 19:54,  2 users,  load average: 3.93, 2.23, 0.96
```

通过以上步骤，可以使用`at`和`batch`指令有效地管理和执行单一工作调度任务。

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
bash复制代码[root@study ~]# crontab [-u username] [-l|-e|-r]
选项与参数：
-u  ：指定用户名，仅 root 可使用，帮助其他用户创建或移除 crontab 工作调度；
-e  ：编辑 crontab 的工作内容；
-l  ：列出 crontab 的工作内容；
-r  ：移除所有的 crontab 工作内容，若仅移除一项，用 -e 编辑。
```

**示例**：用户`dmtsai`每天中午12点给自己发邮件。

```
bash复制代码[dmtsai@study ~]$ crontab -e
# 进入 vi 编辑画面，编辑工作内容，每项工作一行。
0 12 * * * mail -s "at 12:00" dmtsai < /home/dmtsai/.bashrc
```

每项工作格式为：

```
plaintext
复制代码
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
bash
复制代码
59 23 1 5 * mail kiki < /home/dmtsai/lover.txt
```

- 每五分钟执行一次脚本：

```
bash
复制代码
*/5 * * * * /home/dmtsai/test.sh
```

- 每周五下午4:30提醒朋友周六的约会：

```
bash
复制代码
30 16 * * 5 mail [email protected] < /home/dmtsai/friend.txt
```

查看和移除`crontab`工作：

```
bash复制代码[dmtsai@study ~]$ crontab -l
[dmtsai@study ~]$ crontab -r
```

### 15.3.2 系统的配置文件：/etc/crontab, /etc/cron.d/*

`crontab -e`用于用户的`cron`设置，系统的例行性任务可通过编辑`/etc/crontab`文件实现。`/etc/crontab`是纯文本文件，可以直接用`vim`编辑。

**/etc/crontab**的内容：

```
plaintext复制代码[root@study ~]# cat /etc/crontab
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
bash复制代码[root@study ~]# cat /etc/cron.d/0hourly
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

### 15.3.3 一些注意事项

使用`cron`进行例行性工作调度时，应注意以下几点：

- **资源分配不均的问题**：避免在同一时间点执行大量任务。例如，将每五分钟执行的四个任务分散到不同的时间点：

```
plaintext复制代码1,6,11,16,21,26,31,36,41,46,51,56 * * * * root CMD1
2,7,12,17,22,27,32,37,42,47,52,57 * * * * root CMD2
3,8,13,18,23,28,33,38,43,48,53,58 * * * * root CMD3
4,9,14,19,24,29,34,39,44,49,54,59 * * * * root CMD4
```

- **取消不必要的输出**：将输出重定向到`/dev/null`：

```
plaintext
复制代码
* * * * * CMD > /dev/null 2>&1
```

- **安全检验**：检查`/var/log/cron`文件，确保没有被植入木马。
- **周与日月不可同时并存**：不要同时设置周和日月：

```
plaintext
复制代码
30 12 11 9 5 root echo "just test"   # 错误的写法
```

通过以上设置和注意事项，可以有效地管理和执行循环性的例行工作调度。

## 15.4 可唤醒停机期间的工作任务

在某些情况下，如星期六停电导致系统关机，星期天凌晨的例行工作调度将不会被执行。为了确保重要任务（如例行备份）在下次开机时仍能执行，`anacron`提供了解决方案。`anacron`可以在系统启动后自动执行未能按时执行的任务。

### 15.4.1 什么是 anacron

`anacron`的存在目的是弥补非24小时持续运行的Linux系统的工作调度缺失。它并非取代`crontab`，而是在特定条件下补充执行未完成的调度任务。`anacron`每小时由`crond`执行一次，检测并执行超过期限但未完成的调度任务，然后停止。

`anacron`默认以天、周、月为单位检测系统未执行的`crontab`任务，对于非24小时运行的系统尤其有用。

### 15.4.2 anacron 与 /etc/anacrontab

`anacron`是一个程序，而非服务。CentOS中`anacron`每小时被`crontab`调用执行。其配置文件位于`/etc/cron.hourly/0anacron`：

```
bash复制代码[root@study ~]# cat /etc/cron.hourly/0anacron
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
bash复制代码[root@study ~]# anacron [-sfn] [job]..
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
bash复制代码[root@study ~]# cat /etc/anacrontab
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
bash复制代码[root@study ~]# more /var/spool/anacron/*
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