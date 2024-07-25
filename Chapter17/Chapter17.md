# 第十七章、认识系统服务 （daemons）

最近更新日期：20//

在 Unix-Like 的系统中，你会常常听到 daemon 这个字眼！那么什么是传说中的 daemon 呢？这些 daemon 放在什么地方？他的功能是什么？该如何启动这些 daemon ？又如何有效的将这些 daemon 管理妥当？此外，要如何视察这些 daemon 开了多少个 ports ？又这些 ports 要如何关闭？还有还有，晓得你系统的这些 port 各代表的是什么服务吗？ 这些都是最基础需要注意的呢！尤其是在架设网站之前，这里的观念就显的更重要了。

从 CentOS 7.x 这一版之后，传统的 init 已经被舍弃，取而代之的是 systemd 这个家伙～这家伙跟之前的 init 有什么差异？ 优缺点为何？如何管理不同种类的服务类型？以及如何取代原本的“执行等级”等等，很重要的改变喔！

## 17.1 什么是 Daemon 与服务 (Service)

在第十六章中，我们提到过“服务”。服务是常驻内存中的程序，可以提供系统或网络功能，英文称为“service”。在Unix-Like操作系统中，常见的说法是“启动某某daemon来提供某某功能”。Daemon和service有关，因为它们都能提供系统或网络功能。Daemon字面上意思是“守护神”或“恶魔”。

简单来说，系统为了某些功能需要提供一些服务，这些服务称为service。实现这些service的程序称为daemon。例如，实现循环型例行性工作调度服务的程序为crond daemon。这样解释更容易理解。

### 17.1.1 早期 System V 的 init 管理行为中 Daemon 的主要分类 (Optional)

在Unix System V版本中，启动系统服务的管理方式称为SysV init脚本程序处理方式。系统核心第一支调用的程序是init，然后init唤起所有系统需要的服务。

基本上，init的管理机制有以下特点：

- 服务的启动、关闭与观察方式

  ：所有服务启动脚本放在

  ```
  /etc/init.d/
  ```

  目录下，基本上都是bash shell script。需要启动、关闭、重新启动、观察状态时，可以通过以下方式处理：

  - 启动：`/etc/init.d/daemon start`
  - 关闭：`/etc/init.d/daemon stop`
  - 重新启动：`/etc/init.d/daemon restart`
  - 状态观察：`/etc/init.d/daemon status`

- 服务启动的分类

  ：

  - 独立启动模式 (stand alone)：服务独立启动，常驻内存中，提供本机或用户服务，反应速度快。
  - 总管程序 (super daemon)：由xinetd或inetd总管程序管理。当没有用户请求某socket或port时，所需服务不会启动。若有用户请求，xinetd总管才唤醒对应服务程序，服务结束时也会结束。好处是通过super daemon控制服务时程和连线需求，缺点是唤醒服务需要时间延迟。

- **服务的相依性问题**：服务可能有相依性问题。例如，启动网络服务需要网络，没有网络则无法启动网络服务。init在管理员手动处理这些服务时，无法协助相依服务唤醒。

- **执行等级的分类**：Linux提供7个执行等级(0-6)，例如单人维护模式、纯文本模式、文字加图形界面。各执行等级的启动脚本通过`/etc/rc.d/rc[0-6]/SXXdaemon`链接到`/etc/init.d/daemon`，启动脚本依序执行，解决相依服务问题。

- 制定执行等级默认启动服务

  ：通过

  ```
  chkconfig
  ```

  命令管理默认启动服务。

  - 默认启动：`chkconfig daemon on`
  - 默认不启动：`chkconfig daemon off`
  - 观察默认是否启动：`chkconfig --list daemon`

- **执行等级的切换行为**：切换执行等级时，通过`init N`命令切换，init会自动分析`/etc/rc.d/rc[35].d/`目录内的脚本，启动转换runlevel中需要的服务。

虽然CentOS 7已经不使用init管理服务，但考虑到某些脚本无法直接整合到systemd处理，这些脚本仍被保留下来。

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

## 17.2 通过 systemctl 管理服务

systemd通过名为systemctl的指令处理服务管理，替代了System V中需要的service、chkconfig、setup、init等指令。使用systemctl管理服务的操作更加集中和简便。以下是具体的操作方法和示例。

### 17.2.1 通过 systemctl 管理单一服务（service unit）的启动/开机启动与观察状态

服务的启动有两个阶段：一个是开机时设置服务是否启动，另一个是立即启动或停止服务。以下是一些常用的命令：

```
sh复制代码[root@study ~]# systemctl [command] [unit]
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
sh
复制代码
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
sh复制代码[root@study ~]# systemctl stop chronyd.service
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

### 17.2.2 通过 systemctl 观察系统上所有的服务

可以通过`list-units`和`list-unit-files`命令查看所有服务及其状态。

1. 查看系统上启动的unit：

```
sh
复制代码
[root@study ~]# systemctl
```

显示结果包括unit名称、加载状态（LOAD）、当前状态（ACTIVE）、详细描述（DESCRIPTION）。

1. 查看所有已安装的unit：

```
sh
复制代码
[root@study ~]# systemctl list-unit-files
```

显示结果包括unit文件名称和状态（enabled、disabled、mask、static等）。

1. 仅列出service类型的unit：

```
sh
复制代码
[root@study ~]# systemctl list-units --type=service --all
```

### 17.2.3 通过 systemctl 管理不同的操作环境（target unit）

使用target管理不同的操作环境。

1. 获取当前target：

```
sh
复制代码
[root@study ~]# systemctl get-default
```

1. 设置默认操作模式：

```
sh复制代码[root@study ~]# systemctl set-default multi-user.target
[root@study ~]# systemctl get-default
```

1. 切换操作环境：

```
sh
复制代码
[root@study ~]# systemctl isolate multi-user.target
```

恢复图形界面：

```
sh
复制代码
[root@study ~]# systemctl isolate graphical.target
```

简化操作模式切换的指令：

- 关机：`systemctl poweroff`
- 重启：`systemctl reboot`
- 暂停：`systemctl suspend`
- 休眠：`systemctl hibernate`
- 救援模式：`systemctl rescue`
- 紧急救援模式：`systemctl emergency`

### 17.2.4 通过 systemctl 分析各服务之间的相依性

使用`list-dependencies`命令分析服务之间的相依性。

1. 列出当前target环境下的依赖：

```
sh
复制代码
[root@study ~]# systemctl list-dependencies
```

1. 反向追踪某个unit的依赖：

```
sh
复制代码
[root@study ~]# systemctl list-dependencies --reverse
```

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
sh
复制代码
[root@study ~]# systemctl list-sockets
```

### 17.2.6 关闭网络服务

观察本机网络端口：

```
sh
复制代码
[root@study ~]# netstat -tlunp
```

关闭avahi-daemon服务及其socket：

```
sh复制代码[root@study ~]# systemctl stop avahi-daemon.service
[root@study ~]# systemctl stop avahi-daemon.socket
[root@study ~]# systemctl disable avahi-daemon.service avahi-daemon.socket
[root@study ~]# netstat -tlunp
```

总结，通过systemctl可以方便地管理systemd服务，观察服务状态，调整服务的启动和依赖设置，使系统服务管理更加集中和高效。

## 17.3 systemctl 针对 service 类型的配置文件

在以前的System V中，如果要创建系统服务，需要在 `/etc/init.d/` 目录下编写对应的Bash脚本。而在systemd环境下，设置相关服务启动则需要新的方式。以下是详细的配置和设置方法。

### 17.3.1 systemctl 配置文件相关目录简介

systemd 的配置文件主要存放在 `/usr/lib/systemd/system/` 目录内。但官方建议不要修改该目录下的文件，修改文件应放在 `/etc/systemd/system/` 目录下。例如，修改 `vsftpd.service` 可以放置在以下目录：

- `/usr/lib/systemd/system/vsftpd.service`：官方默认配置文件；
- `/etc/systemd/system/vsftpd.service.d/custom.conf`：在 `/etc/systemd/system` 下创建与配置文件同名的目录并加上 `.d` 扩展名，在该目录下创建 `.conf` 文件，这些文件会累加设置进入官方配置文件；
- `/etc/systemd/system/vsftpd.service.wants/*`：设置相依服务的链接文件，表示启动该服务后，建议启动此目录下的服务；
- `/etc/systemd/system/vsftpd.service.requires/*`：设置相依服务的链接文件，表示启动该服务前，需要启动此目录下的服务。

虽然官方建议在 `/etc/systemd/system/` 下进行客制化设置，但也可以直接修改 `/usr/lib/systemd/system/` 下的原始配置文件。

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

### 17.3.3 两个 vsftpd 运行的实例

假设需要同时运行两个vsftpd实例，分别监听端口21和555，以下是具体设置方法：

1. 创建并修改配置文件：

```
sh复制代码[root@study ~]# cp /etc/vsftpd/vsftpd.conf /etc/vsftpd/vsftpd2.conf
[root@study ~]# vim /etc/vsftpd/vsftpd2.conf
# 修改配置文件中的端口号
listen_port=555
```

1. 复制并修改服务启动脚本：

```
sh复制代码[root@study ~]# cp /usr/lib/systemd/system/vsftpd.service /etc/systemd/system/vsftpd2.service
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
sh复制代码[root@study ~]# systemctl daemon-reload
[root@study ~]# systemctl restart vsftpd.service vsftpd2.service
[root@study ~]# systemctl enable vsftpd.service vsftpd2.service
[root@study ~]# systemctl status vsftpd.service vsftpd2.service
```

### 17.3.4 多重的重复设置方式：以 getty 为例

CentOS 7启动后有6个终端机tty1到tty6，这是由agetty命令实现的。实际管理这些终端的是getty.target和 [email protectedemail protectedemail protected](https://wizardforcel.gitbooks.io/cdn-cgi/l/email-protection) 配置文件。以下是其具体设置和机制：

1. 查看getty.target的内容：

```
sh
复制代码
[root@study ~]# systemctl show getty.target
```

1. 修改终端机数量：

```
sh复制代码[root@study ~]# vim /etc/systemd/logind.conf
# 修改NAutoVTs为4
NAutoVTs=4
ReserveVT=0
[root@study ~]# systemctl stop [email protected]
[root@study ~]# systemctl stop [email protected]
[root@study ~]# systemctl restart systemd-logind.service
```

1. 临时启动额外的tty终端：

```
sh
复制代码
[root@study ~]# systemctl start [email protected]
```

### 17.3.5 自己的服务自己作

假设要创建一个备份系统的服务脚本，以下是具体步骤：

1. 创建备份脚本：

```
sh复制代码[root@study ~]# vim /backups/backup.sh
#!/bin/bash
source="/etc /home /root /var/lib /var/spool/{cron,at,mail}"
target="/backups/backup-system-$(date +%Y-%m-%d).tar.gz"
[ ! -d /backups ] && mkdir /backups
tar -zcvf ${target} ${source} &> /backups/backup.log
[root@study ~]# chmod a+x /backups/backup.sh
```

1. 创建服务启动脚本：

```
sh复制代码[root@study ~]# vim /etc/systemd/system/backup.service
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
bash复制代码[root@study ~]# vim /etc/systemd/system/backup.timer
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
bash复制代码[root@study ~]# systemctl daemon-reload
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
bash复制代码[root@study ~]# vim /etc/systemd/system/backup2.timer
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
bash复制代码[root@study ~]# systemctl daemon-reload
[root@study ~]# systemctl enable backup2.timer
[root@study ~]# systemctl start backup2.timer
[root@study ~]# systemctl show backup2.timer
NextElapseUSecRealtime=45y 7month 1w 6d 10h 30min
```

### 总结

通过上述步骤，您可以使用 systemd 的 timer 功能来替代 crond，实现更加灵活和精确的任务调度。

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