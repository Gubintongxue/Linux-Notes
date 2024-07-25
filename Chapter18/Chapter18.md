# 第十八章、认识与分析登录文件

最近更新日期：20//

当你的 Linux 系统出现不明原因的问题时，很多人都告诉你，你要查阅一下登录文件才能够知道系统出了什么问题了，所以说， 了解登录文件是很重要的事情呢。登录文件可以记录系统在什么时间、哪个主机、哪个服务、出现了什么讯息等信息， 这些信息也包括使用者识别数据、系统故障排除须知等信息。如果你能够善用这些登录文件信息的话，你的系统出现错误时， 你将可以在第一时间发现，而且也能够从中找到解决的方案，而不是昏头转向的乱问人呢。 此外，登录文件所记录的信息量是非常大的，要人眼分析实在很困难。此时利用 shell script 或者是其他软件提供的分析工具来处理复杂的登录文件，可以帮助你很多很多喔！

## 18.1 什么是登录文件

“详细而确实地分析以及备份系统的登录文件”是系统管理员的重要任务之一。登录文件记录了系统活动的信息，例如何时、何地（来源 IP）、何人（什么服务名称）、做了什么动作（事件记录）。简而言之，登录文件记录系统在何时由哪个程序做了什么样的行为，并发生了哪些事件。

### 18.1.1 CentOS 7 登录文件简易说明

Linux 主机在后台有许多守护进程（daemons）同时工作，这些工作中的程序会产生各种信息，这些信息会被记录在登录文件中。登录文件的工作就是记录这些系统的重要信息。

#### 登录文件的重要性

为什么登录文件很重要呢？系统管理员需要随时注意它们，因为：

- **解决系统错误**：系统可能会出现硬件检测不到或某些系统服务无法顺利运行的情况。通过查询登录文件，可以了解系统的操作过程，并找到问题的原因。
- **解决网络服务问题**：如果网络服务设置后无法顺利启动，可以通过登录文件找到问题所在。例如，无法启动邮件服务器时，可以查询 `/var/log/maillog` 以获取错误信息。
- **过往事件记录**：登录文件可以记录系统的历史事件，例如某时段的流量异常，可以通过登录文件找出原因。如果系统被入侵，登录文件可以帮助追踪入侵行为。

#### Linux 常见的登录文件

登录文件的权限通常设置为仅 root 用户可读。常见的登录文件包括：

- `/var/log/boot.log`：记录系统开机时的硬件检测和启动信息。
- `/var/log/cron`：记录 crontab 调度任务的执行和错误信息。
- `/var/log/dmesg`：记录系统开机时核心检测过程的各项信息。
- `/var/log/lastlog`：记录系统所有帐号最近一次登录的相关信息。
- `/var/log/maillog` 或 `/var/log/mail/*`：记录邮件的往来信息。
- `/var/log/messages`：记录系统发生的错误信息和重要事件。
- `/var/log/secure`：记录需要输入帐号密码的程序的登录信息。
- `/var/log/wtmp` 和 `/var/log/faillog`：记录正确登录和错误登录的信息。
- `/var/log/httpd/*` 和 `/var/log/samba/*`：记录不同网络服务的日志信息。

不同的 Linux 发行版可能使用不同的登录文件名，管理员需要根据系统配置文件来了解具体的登录文件。

#### 登录文件所需相关服务和程序

登录文件的生成有两种方式：一种是由软件开发商自行定义，另一种是由系统提供的登录文件管理服务统一管理。CentOS 提供 `rsyslog.service` 来统一管理登录文件。此外，登录文件需要定期备份和更新，可以使用 `logrotate` 工具进行自动化管理。

需要的服务和程序包括：

- `systemd-journald.service`：主要的消息接收者，由 systemd 提供。
- `rsyslog.service`：主要记录系统和网络服务的信息。
- `logrotate`：主要进行登录文件的轮替功能。

CentOS 7.x 使用 systemd 提供的 `journalctl` 日志管理。systemd-journald.service 记录的信息主要存放在内存中，性能较好。可以使用 `journalctl` 和 `systemctl status unit.service` 来查看不同服务的登录文件。虽然 systemd-journald.service 提供了新的日志管理方式，但 rsyslog.service 依然重要。

### 18.1.2 登录文件内容的一般格式

系统产生的每条日志信息通常包括以下几个重要数据：

- 事件发生的日期与时间。
- 事件发生的主机名称。
- 启动事件的服务名称或指令与函数名称。
- 该信息的实际数据内容。

例如，查看 `/var/log/secure` 文件：

```
bash复制代码[root@study ~]# cat /var/log/secure
Aug 17 18:38:06 study login: pam_unix(login:session): session opened for user root by LOGIN(uid=0)
Aug 17 18:38:06 study login: ROOT LOGIN ON tty1
Aug 17 18:38:19 study login: pam_unix(login:session): session closed for user root
Aug 18 23:45:17 study sshd[18913]: Accepted password for dmtsai from 192.168.1.200 port 41524 ssh2
Aug 18 23:45:17 study sshd[18913]: pam_unix(sshd:session): session opened for user dmtsai by (uid=0)
Aug 18 23:50:25 study sudo: dmtsai : TTY=pts/0 ; PWD=/home/dmtsai ; USER=root ; COMMAND=/bin/su -
Aug 18 23:50:25 study su: pam_unix(su-l:session): session opened for user root by dmtsai(uid=0)
```

这些记录显示了不同时间点用户登录、会话打开和关闭的信息。

### 常见问题排查

系统不正常时，某个 daemon 无法正常启动，某个用户无法登录，某个 daemon 执行过程不顺畅时，管理员可以查询登录文件找出问题所在。

通过定期查看登录文件，管理员可以掌握系统的最新动态，快速响应和解决问题。例如，当无法成功启动某个服务时，可以立即检查登录文件，找到当前时间记录的信息，查看相关服务的日志，找出错误点。

此外，登录文件还可以作为登录文件服务器，收集来自其他服务器的日志信息，以便进行集中管理和分析。

## 18.2 rsyslog.service：记录登录文件的服务

上一小节提到，Linux 的登录文件主要是由 rsyslog.service 负责的。你可以通过以下命令检查是否启动 rsyslog，并确保其开机时启动：

```
bash复制代码[root@study ~]# ps aux | grep rsyslog
USER  PID %CPU %MEM    VSZ   RSS TTY  STAT START  TIME COMMAND
root  750  0.0  0.1 208012  4732 ?    Ssl  Aug17  0:00 /usr/sbin/rsyslogd -n
bash复制代码[root@study ~]# systemctl status rsyslog.service
rsyslog.service - System Logging Service
   Loaded: loaded (/usr/lib/systemd/system/rsyslog.service; enabled)
   Active: active (running) since Mon 2015-08-17 18:37:58 CST; 2 days ago
 Main PID: 750 (rsyslogd)
   CGroup: /system.slice/rsyslog.service
           └─750 /usr/sbin/rsyslogd -n
```

既然 rsyslog.service 已经在系统中运行，那么接下来我们将探讨它的配置文件、如何设置，以及如何将 Linux 主机作为区网的登录文件服务器。

### 18.2.1 rsyslog.service 的配置文件：/etc/rsyslog.conf

rsyslogd 是负责主机产生的各类信息登录的 daemon。它的配置文件是 /etc/rsyslog.conf，这个文件规定了“（1）什么服务（2）的什么等级讯息（3）需要被记录在哪里（设备或文件）”。配置语法如下：

```
css
复制代码
服务名称[.=!]讯息等级        讯息记录的文件名或设备或主机
```

例如：

```
bash
复制代码
mail.info            /var/log/maillog_info
```

这行表示：mail 服务产生的大于等于 info 等级的讯息，都记录到 /var/log/maillog_info 文件中。

#### 服务名称

rsyslogd 主要通过 Linux 内核提供的 syslog 规范来分类信息。常见的服务类型包括：

- kern（kernel）：核心产生的讯息
- user：用户层产生的信息
- mail：与邮件相关的讯息
- daemon：系统服务产生的信息
- auth：认证/授权相关的讯息
- syslog：rsyslogd 产生的信息
- lpr：打印相关的讯息
- news：新闻群组服务器相关的讯息
- uucp：Unix to Unix Copy Protocol
- cron：例行性工作调度产生的讯息
- authpriv：记录较多帐号私人的信息
- ftp：FTP 通讯协议的讯息
- local0 ~ local7：保留给本机用户使用的讯息

#### 讯息等级

同一服务产生的讯息有不同的严重等级。Linux 内核的 syslog 将讯息分为七个主要等级：

- 7 - debug：除错时产生的讯息
- 6 - info：基本的讯息说明
- 5 - notice：需要注意的信息
- 4 - warning（warn）：警示的讯息
- 3 - err（error）：严重错误的讯息
- 2 - crit：临界错误的信息
- 1 - alert：警告信息
- 0 - emerg（panic）：系统即将崩溃的严重错误

#### 讯息记录的文件名或设备或主机

讯息可以记录到文件、设备、用户、远端主机等。例如：

- 文件的绝对路径：如 /var/log/messages
- 打印机：如 /dev/lp0
- 用户名称：显示给用户
- 远端主机：如 @study.vbird.tsai
- *：代表所有在线用户

#### rsyslog.conf 语法练习

例题：将 mail 相关的数据记录到 /var/log/maillog 中，语法如下：

```
bash
复制代码
mail.info      /var/log/maillog
```

例题：将 news 和 cron 的讯息写入 /var/log/cronnews，警告讯息写入 /var/log/cronnews.warn：

```
perl复制代码news.*;cron.*         /var/log/cronnews
news.=warn;cron.=warn /var/log/cronnews.warn
```

例题：记录所有信息到 /var/log/messages，排除 cron, mail 和 news：

```
bash
复制代码
*.*;news,cron,mail.none    /var/log/messages
```

#### CentOS 7.x 默认的 rsyslog.conf 内容

以下是 CentOS 7.x 默认的 /etc/rsyslog.conf 文件内容：

```
bash复制代码#kern.*                                              /dev/console
*.info;mail.none;authpriv.none;cron.none             /var/log/messages
authpriv.*                                           /var/log/secure
mail.*                                              -/var/log/maillog
cron.*                                               /var/log/cron
*.emerg                                              :omusrmsg:*
uucp,news.crit                                       /var/log/spooler
local7.*                                             /var/log/boot.log
```

这些设置分别表示：

1. 核心产生的讯息发送到 console（终端机）。
2. 所有信息记录到 /var/log/messages，但不包括 mail, authpriv, cron。
3. 认证相关的讯息记录到 /var/log/secure。
4. 邮件相关的讯息记录到 /var/log/maillog。
5. 例行性工作调度记录到 /var/log/cron。
6. 最严重的错误等级广播给所有在系统登录的用户。
7. uucp 和 news 的严重错误记录到 /var/log/spooler。
8. 本机开机时的讯息记录到 /var/log/boot.log。

通过添加自己的配置，可以将所有信息记录到自定义文件中，例如：

```
bash
复制代码
*.info      /var/log/admin.log
```

重新启动 rsyslogd 以应用新配置：

```
bash
复制代码
[root@study ~]# systemctl restart rsyslog.service
```

### 18.2.2 登录文件的安全性设置

为了防止登录文件被删除或修改，可以使用 chattr 命令设置文件属性，使其只能增加数据，不能删除：

```
bash复制代码[root@study ~]# chattr +a /var/log/admin.log
[root@study ~]# lsattr /var/log/admin.log
-----a---------- /var/log/admin.log
```

设置后，文件只能增加数据，不能被删除或修改。需要注意，在进行登录文件轮替（logrotate）时，需先移除此属性：

```
bash
复制代码
[root@study ~]# chattr -a /var/log/admin.log
```

### 18.2.3 登录文件服务器的设置

通过将登录信息传送到远端主机，可以集中管理多个主机的登录信息。以下是设置步骤：

#### Server 端设置

在 /etc/rsyslog.conf 文件中启用 TCP 或 UDP 端口接收日志：

```
bash复制代码[root@study ~]# vim /etc/rsyslog.conf
# Provides TCP syslog reception
$ModLoad imtcp
$InputTCPServerRun 514
```

重启 rsyslog.service：

```
bash复制代码[root@study ~]# systemctl restart rsyslog.service
[root@study ~]# netstat -ltnp | grep syslog
```

#### Client 端设置

在 /etc/rsyslog.conf 中指定将信息发送到服务器：

```
bash复制代码[root@study ~]# vim /etc/rsyslog.conf
*.*       @@192.168.1.100  # 使用 TCP 传输
#*.*       @192.168.1.100  # 使用 UDP 传输
```

重启 rsyslog.service：

```
bash
复制代码
[root@study ~]# systemctl restart rsyslog.service
```

这样，客户端的所有登录信息将发送到服务器端，集中管理多个主机的日志信息。

## 18.3 登录文件的轮替（logrotate）

假设我们已经将登录数据写入了记录文件，并利用 `chattr` 设置了 `+a` 这个属性，那么该如何进行 logrotate 的工作呢？特别留意的是：`rsyslogd` 利用的是 daemon 的方式来启动，当有需求时立即执行。而 `logrotate` 则是在规定的时间到了之后才进行登录文件的轮替。因此，`logrotate` 程序是挂在 [cron](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 下面进行的。

仔细看一下 `/etc/cron.daily/` 里面的文件，看到 `/etc/cron.daily/logrotate` 吧？它记录了每天要进行的登录文件轮替的行为。下面我们来谈一谈如何设计这个 logrotate。

### 18.3.1 logrotate 的配置文件

logrotate 主要是针对登录文件进行轮替，因此它需要配置“在什么状态下进行轮替”的设置。logrotate 的配置文件包括：

- `/etc/logrotate.conf`
- `/etc/logrotate.d/`

`/etc/logrotate.conf` 是主要的参数文件，而 `/etc/logrotate.d/` 是一个目录，里面的所有文件都会被读入 `/etc/logrotate.conf` 来执行。如果某个文件中没有规定的细节设置，则以 `/etc/logrotate.conf` 中的默认值为准。

logrotate 的基本工作过程如下图所示：

![登录文件进行 logrotate 的结果](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/img/logrotate.gif) 图18.3.1、登录文件进行 logrotate 的结果

上图显示了 logrotate 的工作过程。第一次执行 logrotate 后，原本的 `messages` 文件会变成 `messages.1`，并创建一个新的空 `messages` 文件来记录日志。第二次执行后，`messages.1` 变成 `messages.2`，`messages` 变成 `messages.1`，再次创建一个新的 `messages` 文件。如果设置只保留三个日志文件，那么第四次执行时，`messages.3` 文件将被删除。

下面是默认的 `/etc/logrotate.conf` 文件内容：

```
bash复制代码[root@study ~]# vim /etc/logrotate.conf
# 下面的设置是 "logrotate 的默认设置值"
weekly      # 默认每周进行一次 logrotate
rotate 4    # 默认保留四个日志文件
create      # 创建新的日志文件
dateext     # 使用日期作为文件名扩展
#include /etc/logrotate.d  # 包含 /etc/logrotate.d/ 目录中的所有文件

/var/log/wtmp {
    monthly       # 每月进行一次 logrotate
    create 0664 root utmp # 指定新建文件的权限与所属用户和组
    minsize 1M    # 文件大小超过 1M 才进行 logrotate
    rotate 1      # 只保留一个日志文件
}
```

通过 `/etc/logrotate.d/` 目录，可以将各个服务的日志轮替设置独立成文件，放在该目录中。例如，查看 `/etc/logrotate.d/syslog` 文件：

```
bash复制代码[root@study ~]# vim /etc/logrotate.d/syslog
/var/log/cron
/var/log/maillog
/var/log/messages
/var/log/secure
/var/log/spooler
{
    sharedscripts
    postrotate
        /bin/kill -HUP `cat /var/run/syslogd.pid 2> /dev/null` 2> /dev/null || true
    endscript
}
```

上述设置只对指定的日志文件有效，日志轮替每周一次，保留四个日志文件，轮替后重新启动 `syslogd`。

假设我们针对 `/var/log/messages` 文件增加了 `chattr +a` 属性，可以通过 `prerotate` 和 `postrotate` 设置在轮替前后执行命令：

```
bash复制代码[root@study ~]# vim /etc/logrotate.d/syslog
/var/log/cron
/var/log/maillog
/var/log/messages
/var/log/secure
/var/log/spooler
{
    sharedscripts
    prerotate
        /usr/bin/chattr -a /var/log/messages
    endscript
    postrotate
        /bin/kill -HUP `cat /var/run/syslogd.pid 2> /dev/null` 2> /dev/null || true
        /usr/bin/chattr +a /var/log/messages
    endscript
}
```

### 18.3.2 实际测试 logrotate 的动作

设置完成后，可以通过以下命令测试 logrotate：

```
bash复制代码[root@study ~]# logrotate [-vf] /etc/logrotate.conf
选项与参数：
-v  ：启动显示模式，显示 logrotate 运行过程
-f  ：强制每个日志文件进行 logrotate

范例一：执行一次 logrotate 查看整个流程
[root@study ~]# logrotate -v /etc/logrotate.conf

范例二：强制进行 logrotate
[root@study ~]# logrotate -vf /etc/logrotate.conf
```

通过上述命令，可以看到 logrotate 的运行过程，并检查是否有错误。

### 18.3.3 自定义登录文件的轮替功能

假设你创建了 `/var/log/admin.log` 文件，并希望设置以下 logrotate 参数：

- 每月轮替一次；
- 文件大于 10MB 时强制轮替；
- 保留五个备份文件；
- 备份文件需要压缩。

可以通过以下步骤设置：

1. 为文件添加 `+a` 属性：

```
bash复制代码[root@study ~]# chattr +a /var/log/admin.log
[root@study ~]# lsattr /var/log/admin.log
-----a---------- /var/log/admin.log
```

1. 创建 logrotate 配置文件：

```
bash复制代码[root@study ~]# vim /etc/logrotate.d/admin
# This configuration is from VBird 2015/08/19
/var/log/admin.log {
    monthly
    size=10M
    rotate 5
    compress
    sharedscripts
    prerotate
        /usr/bin/chattr -a /var/log/admin.log
    endscript
    postrotate
        /bin/kill -HUP `cat /var/run/syslogd.pid 2> /dev/null` 2> /dev/null || true
        /usr/bin/chattr +a /var/log/admin.log
    endscript
}
```

1. 测试 logrotate 设置：

```
bash复制代码[root@study ~]# logrotate -v /etc/logrotate.conf
[root@study ~]# logrotate -vf /etc/logrotate.d/admin
```

通过上述步骤，可以创建和测试自定义的 logrotate 设置，确保日志文件按预期轮替。

## 18.4 systemd-journald.service 简介

在过去只有 rsyslogd 的时代，rsyslogd 必须在开机完成并执行 rsyslogd 这个 daemon 之后，登录文件才会开始记录。因此，核心需要一个 klogd 服务来记录开机过程和服务启动过程中的信息，然后再传送给 rsyslogd 处理。

现在有了 systemd 之后，由于 systemd 是核心唤醒的第一个软件，它可以主动调用 systemd-journald 来记录登录文件，包括开机过程中的所有信息、启动服务及服务启动失败的情况等。这些信息都可以直接被记录到 systemd-journald 中。

虽然 systemd-journald 使用内存来记录登录文件信息，重新开机后，这些信息就不会被保留。因此，建议仍然启动 rsyslogd 来分类记录日志信息，这样可以管理和查询开机后的日志，同时也能将所有数据记录到磁盘文件中，方便以后查询。

### 18.4.1 使用 journalctl 观察登录信息

systemd-journald.service 的数据可以通过 journalctl 命令来查阅。journalctl 的使用方法如下：

```
bash复制代码[root@study ~]# journalctl [-nrpf] [--since TIME] [--until TIME]
选项与参数：
  -n  ：显示最近的几行
  -r  ：反向输出，从最新的输出到最旧的数据
  -p  ：显示指定严重等级的消息
  -f  ：类似 tail -f，持续显示 journal 日志内容
  --since --until：设置开始与结束的时间
  _SYSTEMD_UNIT=unit.service ：只输出指定服务的消息
  _COMM=bash ：只输出与指定命令有关的消息
  _PID=pid   ：只输出指定 PID 的消息
  _UID=uid   ：只输出指定 UID 的消息
  SYSLOG_FACILITY=[0-23] ：使用 syslog.h 规范的服务相对序号来调用数据
```

示例：

1. 显示系统中所有的 journal 日志数据：

   ```
   bash
   复制代码
   [root@study ~]# journalctl
   ```

2. 显示指定时间范围的日志数据：

   ```
   bash复制代码[root@study ~]# journalctl --since "2015-08-18 00:00:00" --until "2015-08-19 00:00:00"
   [root@study ~]# journalctl --since today
   [root@study ~]# journalctl --since yesterday --until today
   ```

3. 显示 crond.service 的最新 10 条日志数据：

   ```
   bash
   复制代码
   [root@study ~]# journalctl _SYSTEMD_UNIT=crond.service -n 10
   ```

4. 显示 su 和 login 的最新 10 条日志数据：

   ```
   bash
   复制代码
   [root@study ~]# journalctl _COMM=su _COMM=login -n 10
   ```

5. 显示错误等级的日志数据：

   ```
   bash
   复制代码
   [root@study ~]# journalctl -p err
   ```

6. 显示与认证服务（auth, authpriv）相关的日志数据：

   ```
   bash
   复制代码
   [root@study ~]# journalctl SYSLOG_FACILITY=4 SYSLOG_FACILITY=10
   ```

使用 `journalctl -f` 可以实时监测系统日志的变化。

### 18.4.2 logger 指令的应用

如果需要将数据写入登录文件，可以使用 logger 命令。基本用法如下：

```
bash复制代码[root@study ~]# logger [-p 服务名称.等级] "讯息"
选项与参数：
  服务名称.等级 ：这个项目请参考 rsyslogd 的相关信息
```

示例：

1. 使用 logger 传送数据到登录文件：

   ```
   bash复制代码[root@study ~]# logger -p user.info "I will check logger command"
   [root@study ~]# journalctl SYSLOG_FACILITY=1 -n 3
   ```

2. 在备份脚本中记录备份的开始与结束时间：

   ```
   bash复制代码[root@study ~]# vim /backups/backup.sh
   #!/bin/bash
   
   if [ "${1}" == "log" ]; then
       logger -p syslog.info "backup.sh is starting"
   fi
   source="/etc /home /root /var/lib /var/spool/{cron,at,mail}"
   target="/backups/backup-system-$(date +%Y-%m-%d).tar.gz"
   [ ! -d /backups ] && mkdir /backups
   tar -zcvf ${target} ${source} &> /backups/backup.log
   if [ "${1}" == "log" ]; then
       logger -p syslog.info "backup.sh is finished"
   fi
   ```

### 18.4.3 保存 journal 的方式

systemd-journald.service 的日志记录在内存中，重新开机后信息会被清除。要保存 journal 日志信息，可以创建一个 `/var/log/journal` 目录，并设置相应权限：

```
bash复制代码[root@study ~]# mkdir /var/log/journal
[root@study ~]# chown root:systemd-journal /var/log/journal
[root@study ~]# chmod 2775 /var/log/journal
[root@study ~]# systemctl restart systemd-journald.service
```

重新启动 systemd-journald.service 后，日志会保存到 `/var/log/journal` 目录中。

对于系统日志文件的存储和管理，建议使用 rsyslog.service 和 logrotate，因为它们可以更好地分类和管理日志文件，且性能更优。systemd-journald.service 更适合短期内存日志记录和实时监控。

## 18.5 分析登录文件

登录文件的分析是很重要的！你可以自行以 vim 或者是 journalctl 进入登录文件去查阅相关的信息。而系统也提供一些软件可以让你从登录文件中取得数据， 例如之前谈过的 last, lastlog, dmesg 等等指令。不过，这些数据毕竟都非常的分散，如果你想要一口气读取所有的登录信息， 其实有点困扰的。不过，好在 CentOS 有提供 logwatch 这个登录文件分析程序，你可以借由该程序来了解登录文件信息。 此外，鸟哥也依据 Red Hat 系统的 journalctl 搭配 syslog 函数写了一支小程序给大家使用喔！

### 18.5.1 CentOS 默认提供的 logwatch

虽然有一些有用的系统指令，不过，要了解系统的状态，还是得要分析整个登录文件才行～ 事实上，目前已经有相当多的登录文件分析工具，例如 CentOS 7.x 上面默认的 logwatch 这个套件所提供的分析工具， 他会每天分析一次登录文件，并且将数据以 email 的格式寄送给 root 呢！ 你也可以直接到 logwatch 的官方网站上面看看：

- http://www.logwatch.org/

不过在我们的安装方式里面，默认并没有安装 logwatch 就是了！所以，我们先来安装一下 logwatch 这套软件再说。假设你已经将 CentOS 7.1 的原版光盘挂载在 /mnt 当中了，那使用下面的方式来处理即可：

```
[root@study ~]# yum install /mnt/Packages/perl-5.*.rpm
&gt;  /mnt/Packages/perl-Date-Manip-*.rpm \
&gt;  /mnt/Packages/perl-Sys-CPU-*.rpm \
&gt;  /mnt/Packages/perl-Sys-MemInfo-*.rpm \
&gt;  /mnt/Packages/logwatch-*.rpm
# 得要安装数个软件才能够顺利的安装好 logwatch 喔！当然，如果你有网络，直接安装就好了！

[root@study ~]# ll /etc/cron.daily/0logwatch
-rwxr-xr-x. 1 root root 434 Jun 10  2014 /etc/cron.daily/0logwatch

[root@study ~]# /etc/cron.daily/0logwatch
```

安装完毕以后，logwatch 就已经写入 cron 的运行当中了！详细的执行方式你可以参考上表中 0logwatch 文件内容来处理，未来则每天会送出一封 email 给 root 查阅就是了。因为我们刚刚安装，那可以来分析一下吗？很简单啦！你就直接执行 0logwatch 即可啊！如上表最后一个指令的示意。因为鸟哥的测试机目前的服务很少， 所以产生的信息量也不多，因此执行的速度很快。比较忙的系统信息量比较大，分析过程会花去一小段时间。如果顺利执行完毕，那请用 root 的身份去读一下 email 啰！

```
[root@study ~]# mail
Heirloom Mail version 12.5 7/5/10\.  Type ? for help.
"/var/spool/mail/root": 5 messages 2 new 4 unread
&gt;N  4 root                  Thu Jul 30 19:35  29/763   "testing at job"
 N  5 [email protected]  Thu Aug 20 17:55  97/3045  "Logwatch for study.centos.vbird （Linux）"
& 5
Message  5:
From [email protected]  Thu Aug 20 17:55:23 2015
Return-Path: &lt;[email protected]&gt;
X-Original-To: root
Delivered-To: [email protected]
To: [email protected]
From: [email protected]
Subject: Logwatch for study.centos.vbird （Linux）
Auto-Submitted: auto-generated
Precedence: bulk
Content-Type: text/plain; charset="iso-8859-1"
Date: Thu, 20 Aug 2015 17:55:23 +0800 （CST）
Status: R

# logwatch 会先说明分析的时间与 logwatch 版本等等信息
 ################### Logwatch 7.4.0 （03/01/11） ####################
        Processing Initiated: Thu Aug 20 17:55:23 2015
        Date Range Processed: yesterday
                              （ 2015-Aug-19 ）
                              Period is day.
        Detail Level of Output: 0
        Type of Output/Format: mail / text
        Logfiles for Host: study.centos.vbird
 ##################################################################

# 开始一项一项的数据进行分析！分析得很有道理啊！
 --------------------- pam_unix Begin ------------------------
 su-l:
    Sessions Opened:
       dmtsai -&gt; root: 2 Time（s）
 ---------------------- pam_unix End -------------------------

 --------------------- Postfix Begin ------------------------
      894   Bytes accepted                                 894
      894   Bytes delivered                                894
 ========   ==================================================
        2   Accepted                                   100.00%
 --------   --------------------------------------------------
        2   Total                                      100.00%
 ========   ==================================================
        2   Removed from queue
        2   Delivered
 ---------------------- Postfix End -------------------------

 --------------------- SSHD Begin ------------------------
 Users logging in through sshd:
    dmtsai:
       192.168.1.200: 2 times
 Received disconnect:
    11: disconnected by user : 1 Time（s）
 ---------------------- SSHD End -------------------------

 --------------------- Sudo （secure-log） Begin ------------------------
 dmtsai =&gt; root
 --------------
 /bin/su                        -   2 Time（s）.
 ---------------------- Sudo （secure-log） End -------------------------

# 当然也得说明一下目前系统的磁盘使用状态喔！
 --------------------- Disk Space Begin ------------------------
 Filesystem                 Size  Used Avail Use% Mounted on
 /dev/mapper/centos-root     10G  3.7G  6.3G  37% /
 devtmpfs                   1.4G     0  1.4G   0% /dev
 /dev/vda2                 1014M  141M  874M  14% /boot
 /dev/vda4                 1014M   33M  982M   4% /srv/myproject
 /dev/mapper/centos-home    5.0G  642M  4.4G  13% /home
 /dev/mapper/raidvg-raidlv  1.5G   33M  1.5G   3% /srv/raidlvm
 ---------------------- Disk Space End -------------------------
```

由于鸟哥的测试用主机尚未启动许多服务，所以分析的项目很少。若你的系统已经启动许多服务的话， 那么分析的项目理应会多很多才对。

### 18.5.2 鸟哥自己写的登录文件分析工具：

虽然已经有了类似 logwatch 的工具，但是鸟哥自己想要分析的数据毕竟与对方不同～所以啰，鸟哥就自己写了一支小程序 （shell script 的语法） 用来分析自己的登录文件，这支程序分析的登录文件主要由 journalctl 所产生，而且只会抓前一天的登录文件来分析而已～ 若比对 rsyslog.service 所产生的登录文件，则主要用到下面几个对应的文件名 （虽然真的没用到！ ^_^）：

- /var/log/secure
- /var/log/messages
- /var/log/maillog

当然啦，还不只这些啦，包括各个主要常见的服务，如 pop3, mail, ftp, su 等会使用到 pam 的服务， 都可以通过鸟哥写的这个小程序来分析与处理呢～整个数据还会输出一些系统信息。如果你想要使用这个程序的话， 欢迎下载：

- http://linux.vbird.org//linux_basic/0570syslog//logfile_centos7.tar.gz

安装的方法也很简单，你只要将上述的文件在根目录下面解压缩，自然就会将 cron 调度与相对应的文件放到正确的目录去。 基本上鸟哥会用到的目录有 /etc/cron.d 以及 /root/bin/logfile 而已！鸟哥已经写了一个 crontab 在文件中，设置每日 00:10 去分析一次系统登录文件。 不过请注意，这次鸟哥使用的登录文件真的是来自于 journalctl ，所以 CentOS 6 以前的版本千万不要使用喔！现在假设我将下载的文件放在跟目录，所以：

```
[root@study ~]# tar -zxvf /logfile_centos7.tar.gz -C /
[root@study ~]# cat /etc/cron.d/vbirdlogfile
10 0 * * * root /bin/bash /root/bin/logfile/logfile.sh &&gt; /dev/null

[root@study ~]# sh /root/bin/logfile/logfile.sh
# 开始尝试分析系统的登录文件，依据你的登录文件大小，分析的时间不固定！

[root@study ~]# mail
# 自己找到刚刚输出的结果，该结果的输出有点像下面这样：
Heirloom Mail version 12.5 7/5/10\.  Type ? for help.
"/var/spool/mail/root": 9 messages 4 new 7 unread
 N  8 root                  Thu Aug 20 19:26  60/2653  "study.centos.vbird logfile analysis results"
&gt;N  9 root                  Thu Aug 20 19:37  59/2612  "study.centos.vbird logfile analysis results"
& 9

# 先看看你的硬件与操作系统的相关情况，尤其是 partition 的使用量更需要随时注意！
=============== system summary =================================
Linux kernel  :  Linux version 3.10.0-229.el7.x86_64 （[email protected]）
CPU informatin:  2 Intel（R） Xeon（R） CPU E5-2650 v3 @ 2.30GHz
CPU speed     :  2299.996 MHz
hostname is   :  study.centos.vbird
Network IP    :  192.168.1.100 
Check time    :  2015/August/20 19:37:25 （ Thursday ）
Summary date  :  Aug 20
Up times      :  3 days, 59 min,
Filesystem summary:
       Filesystem                Type      Size  Used Avail Use% Mounted on
       /dev/mapper/centos-root   xfs        10G  3.7G  6.3G  37% /
       devtmpfs                  devtmpfs  1.4G     0  1.4G   0% /dev
       tmpfs                     tmpfs     1.4G   48K  1.4G   1% /dev/shm
       tmpfs                     tmpfs     1.4G  8.7M  1.4G   1% /run
       tmpfs                     tmpfs     1.4G     0  1.4G   0% /sys/fs/cgroup
       /dev/vda2                 xfs      1014M  141M  874M  14% /boot
       /dev/vda4                 xfs      1014M   33M  982M   4% /srv/myproject
       /dev/mapper/centos-home   xfs       5.0G  642M  4.4G  13% /home
       /dev/mapper/raidvg-raidlv xfs       1.5G   33M  1.5G   3% /srv/raidlvm
       /dev/sr0                  iso9660   7.1G  7.1G     0 100% /mnt

# 这个程序会将针对 internet 与内部监听的端口分开来显示！
================= Ports 的相关分析信息 =======================
主机启用的 port 与相关的 process owner：
对外部接口开放的 ports （PID&#124;owner&#124;command）
       tcp 21&#124;（root）&#124;/usr/sbin/vsftpd /etc/vsftpd/vsftpd.conf
       tcp 22&#124;（root）&#124;/usr/sbin/sshd -D
       tcp 25&#124;（root）&#124;/usr/libexec/postfix/master -w
       tcp 222&#124;（root）&#124;/usr/sbin/sshd -f /etc/ssh/sshd2_config -D
       tcp 514&#124;（root）&#124;/usr/sbin/rsyslogd -n
       tcp 555&#124;（root）&#124;/usr/sbin/vsftpd /etc/vsftpd/vsftpd2.conf

# 以下针对有启动的服务个别进行分析！
================= SSH 的登录文件信息汇整 =======================
今日没有使用 SSH 的纪录

================= Postfix 的登录文件信息汇整 ===================
使用者信箱受信次数：
```

目前鸟哥都是通过这支程序去分析自己管理的主机，然后再据以了解系统状况，如果有特殊状况则实时进行系统处理！ 而且鸟哥都是将上述的 email 调整成自己可以在 Internet 上面读到的邮件，这样我每天都可以收到正确的登录文件分析信息哩！

## 18.6 重点回顾

- 登录文件可以记录一个事件的何时、何地、何人、何事等四大信息，故系统有问题时务必查询登录文件；
- 系统的登录文件默认都集中放置到 /var/log/ 目录内，其中又以 messages 记录的信息最多！
- 登录文件记录的主要服务与程序为： systemd-journald.service, rsyslog.service, rsyslogd
- rsyslogd 的配置文件在 /etc/rsyslog.conf ，内容语法为：“ 服务名称.等级 记载设备或文件”
- 通过 linux 的 syslog 函数查询，了解上述服务名称有 kernel, user, mail...从 0 到 23 的服务序号
- 承上，等级从不严重到严重依序有 info, notice, warning, error, critical, alert, emergency 等
- rsyslogd 本身有提供登录文件服务器的功能，通过修改 /etc/rsyslog.conf 内容即可达成；
- logrotate 程序利用 crontab 来进行登录文件的轮替功能；
- logrotate 的配置文件为 /etc/logrotate.conf ，而额外的设置则可写入 /etc/logrotate.d/* 内；
- 新的 CentOS 7 由于内置 systemd-journald.service 的功能，可以使用 journalctl 直接从内存读出登录文件，查询性能较佳
- logwatch 为 CentOS 7 默认提供的一个登录文件分析软件。