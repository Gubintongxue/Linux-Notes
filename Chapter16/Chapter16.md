# 第十六章、程序管理与 SELinux 初探

最近更新日期：20//

一个程序被载入到内存当中运行，那么在内存内的那个数据就被称为程序（process）。程序是操作系统上非常重要的概念， 所有系统上面跑的数据都会以程序的型态存在。那么系统的程序有哪些状态？不同的状态会如何影响系统的运行？ 程序之间是否可以互相控管等等的，这些都是我们所必须要知道的项目。 另外与程序有关的还有 SELinux 这个加强文件存取安全性的咚咚，也必须要做个了解呢！

## 16.1 什么是程序（Process）

在前面几章中，我们强调了在Linux中所有指令和操作都与权限有关，系统通过UID（用户ID）和GID（组ID）来判定权限。每当触发一个事件时，系统会将其定义为一个程序，并赋予一个PID（进程ID），根据触发程序的用户和相关属性来设置这个PID的权限。这使得程序能够在系统中执行特定的任务。

### 16.1.1 程序与程序（Process & Program）

执行一个程序或指令时，会触发一个事件，生成一个PID。系统通过这个PID来管理程序的执行权限。例如，执行`touch`指令来创建一个文件时，不同用户执行该指令会获得不同的权限，这取决于执行者的UID和GID。

![程序被载入成为程序以及相关数据的示意图](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/img/process_1.gif)

### 程序与进程的区别

- **程序（Program）**：通常是二进制文件，存储在硬盘等介质上，是实体文件。
- **进程（Process）**：程序被触发后，加载到内存中，拥有执行者权限、程序码和数据等，并赋予一个PID。

每个程序都有三组权限（用户、组、其他），根据执行者的身份，不同用户执行同一个程序会有不同的权限。比如，当不同用户登录并启动`/bin/bash`时，尽管执行的是同一个程序，但每个用户的权限和环境是不同的。

![程序与程序之间的差异](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/img/program_process.gif)

### 父进程与子进程

在Linux中，进程之间是有相关性的。比如，当我们在一个`bash`环境中执行一个新的指令，这个指令会生成一个子进程，原来的`bash`进程则是父进程。通过`ps -l`可以查看进程间的关系。

```
bash复制代码F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
0 S  1000 13928 13927  0  80   0 - 29038 wait   pts/0    00:00:00 bash
0 S  1000 13970 13928  1  80   0 - 29033 wait   pts/0    00:00:00 bash
0 R  1000 14000 13970  0  80   0 - 30319 -      pts/0    00:00:00 ps
```

### Fork and Exec：程序调用的流程

在Linux中，程序调用通常采用`fork-and-exec`的流程。父进程通过`fork`复制自身，生成一个子进程，子进程再通过`exec`加载并执行新的程序。

![程序使用 fork and exec 调用的情况示意图](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/img/fork-and-exec.gif)

### 常驻在内存的程序（Daemon）

一些程序在执行后会一直驻留在内存中，如`crond`、`atd`等，这些程序被称为服务（Daemon）。这些服务主要负责系统的各项任务，如定时任务管理、日志记录等。Daemon程序通常以`d`结尾，例如`httpd`、`vsftpd`。

### 16.1.2 Linux 的多用户多任务环境

Linux是一个多用户、多任务的操作系统，支持多个用户同时登录，每个用户都有自己的权限和环境。多任务是指Linux可以同时运行多个程序，每个程序都有自己的PID，系统通过PID来管理和调度各个程序的执行。

- **多用户环境**：每个用户登录后，获得自己的Shell环境和权限设置。
- **多任务行为**：Linux可以让CPU在多个程序间快速切换，实现多任务并行执行。

### 多重登录环境的七个基本终端窗口

Linux默认提供了六个文本界面终端和一个图形界面终端，可以通过`Alt`+`F1`到`F7`进行切换。这些终端允许不同用户同时登录和操作，有助于在某个终端出现问题时切换到其他终端进行修复。

### 程序管理与多任务处理

Linux允许在一个Shell中同时运行多个程序，可以使用`&`符号将程序放置在后台运行。例如：

```
bash
复制代码
[root@study ~]# cp file1 file2 &
```

### 资源分配与系统性能

多用户、多任务环境需要考虑系统资源的合理分配。当用户数量增加或程序复杂度提高时，可能需要升级硬件资源（如CPU和内存）来保证系统性能。

总之，Linux的多用户、多任务特性使其在多种应用场景下具有高效和灵活的处理能力，能够满足不同用户的需求。

## 16.2 工作管理（Job Control）

工作管理（Job Control）用于管理在单一终端接口中同时进行多个任务的行为。在登录Bash Shell之后，可以在同一终端下同时进行多个工作，如复制文件、数据搜索、编译、编写程序等，而无需重复登录多个终端。这种多任务管理在Bash环境中尤为重要。

### 16.2.1 什么是工作管理？

工作管理（Job Control）指的是在Bash环境中管理由当前Shell启动的子程序。无法跨终端管理其他Shell的工作。工作可以在前景（foreground）和背景（background）中运行。在背景中的工作不能与用户交互（如需要用户输入），而前景工作可以与用户交互。

### 16.2.2 Job Control的管理

#### 将指令丢到背景中执行：&

使用`&`符号可以将一个工作放入背景中执行。例如：

```
bash复制代码[root@study ~]# tar -zpcf /tmp/etc.tar.gz /etc &
[1] 14432  # [1]是工作号，14432是PID
```

在输入指令后加上`&`，可以将该工作放入背景中执行，不会影响前景的操作。当工作完成时，会显示类似于`[1]+ Done`的消息。

#### 将当前工作丢到背景中暂停：Ctrl+Z

在Bash环境中，可以使用`Ctrl+Z`将当前工作放入背景中暂停。例如：

```
bash复制代码[root@study ~]# vim ~/.bashrc
# 在vim中按下Ctrl+Z
[1]+  Stopped  vim ~/.bashrc
[root@study ~]#
```

#### 观察当前背景工作状态：jobs

使用`jobs`命令可以查看当前Shell中的所有背景工作。例如：

```
bash复制代码[root@study ~]# jobs -l
[1]- 14566 Stopped  vim ~/.bashrc
[2]+ 14567 Stopped  find / -print
```

#### 将背景工作拿到前景来处理：fg

使用`fg`命令可以将背景工作拿到前景中。例如：

```
bash
复制代码
[root@study ~]# fg %1  # 将工作号为1的工作拿到前景
```

#### 让工作在背景中运行：bg

使用`bg`命令可以将背景中暂停的工作设置为运行。例如：

```
bash
复制代码
[root@study ~]# bg %3  # 将工作号为3的工作在背景中运行
```

#### 管理背景工作：kill

使用`kill`命令可以发送信号来管理工作。例如：

```
bash
复制代码
[root@study ~]# kill -9 %2  # 强制终止工作号为2的工作
```

常用的信号有：

- `-1`：重新读取配置文件（类似于reload）
- `-2`：相当于键盘输入`Ctrl+C`
- `-9`：强制终止一个工作
- `-15`：以正常方式终止一个工作（默认值）

### 16.2.3 离线管理问题

在远程登录情况下，将工作放入背景中执行，如果在工作未完成前断开连接，工作将会被中断。可以使用`nohup`命令来确保工作在断开连接后继续运行。例如：

```
bash复制代码[root@study ~]# nohup ./sleep500.sh &
nohup: ignoring input and appending output to 'nohup.out'
```

`nohup`命令可以在登出或断开连接后继续运行工作，输出会被重定向到`nohup.out`文件中。

通过以上方式，可以有效地管理和控制在Bash环境下的多任务处理，提高工作效率。

## 16.3 程序管理

程序管理是系统管理的重要组成部分。通过理解程序的触发、子程序与父程序的关系、程序的相依性和僵尸程序等概念，可以有效地监控和控制系统上的各种程序，从而保障系统的稳定运行。

### 16.3.1 程序的观察

观察系统中正在运行的程序是程序管理的基础工作。以下是常用的工具和方法：

#### `ps` 命令

`ps` 命令用于显示当前系统中的进程信息。

- 查看所有系统的进程：

  ```
  bash
  复制代码
  ps aux
  ```

- 查看详细的进程信息：

  ```
  bash
  复制代码
  ps -lA
  ```

- 查看进程树状态：

  ```
  bash
  复制代码
  ps axjf
  ```

输出的信息包括用户ID、进程ID、父进程ID、CPU使用率、优先级、内存使用情况、终端类型、进程状态和启动时间等。通过这些信息，可以了解进程的运行状态和资源占用情况。

#### `top` 命令

`top` 命令可以动态地显示系统中运行的进程信息，实时监控系统资源的使用情况。

- 启动`top`：

  ```
  bash
  复制代码
  top
  ```

- 每两秒更新一次：

  ```
  bash
  复制代码
  top -d 2
  ```

在`top`界面中，可以使用不同的按键进行操作，如按`P`按CPU使用率排序，按`M`按内存使用率排序，按`q`退出等。

#### `pstree` 命令

`pstree` 命令以树状结构显示进程之间的关系。

- 显示进程树：

  ```
  bash
  复制代码
  pstree
  ```

- 显示进程树并包含进程ID：

  ```
  bash
  复制代码
  pstree -p
  ```

- 显示进程树并包含用户信息：

  ```
  bash
  复制代码
  pstree -u
  ```

通过`pstree`，可以直观地了解进程之间的父子关系，有助于找出问题进程的父进程。

### 16.3.2 程序的管理

程序管理涉及到控制进程的行为，如终止、暂停和调整优先级等。

#### 发送信号控制进程

使用`kill`命令发送信号给进程，可以控制进程的行为。

- 发送`SIGHUP`信号，重新读取配置文件：

  ```
  bash
  复制代码
  kill -1 PID
  ```

- 强制终止进程：

  ```
  bash
  复制代码
  kill -9 PID
  ```

- 终止所有名为`httpd`的进程：

  ```
  bash
  复制代码
  killall -9 httpd
  ```

常用信号包括`SIGHUP`（1），`SIGINT`（2），`SIGKILL`（9），`SIGTERM`（15）等。

#### 调整进程的优先级

通过调整进程的`Nice`值，可以改变进程的优先级。

- 使用`nice`命令设置新进程的优先级：

  ```
  bash
  复制代码
  nice -n -5 command
  ```

- 使用`renice`命令调整已有进程的优先级：

  ```
  bash
  复制代码
  renice -5 PID
  ```

一般用户只能调整自己的进程且不能降低优先级，而`root`用户可以调整所有进程的优先级。

### 16.3.3 系统资源的观察

除了观察和管理进程，还需要监控系统资源的使用情况。以下工具可以帮助了解系统资源的使用情况：

#### `free` 命令

`free`命令显示系统内存的使用情况。

- 以MB为单位显示内存使用情况：

  ```
  bash
  复制代码
  free -m
  ```

#### `uname` 命令

`uname`命令显示系统和内核相关信息。

- 显示所有系统信息：

  ```
  bash
  复制代码
  uname -a
  ```

#### `uptime` 命令

`uptime`命令显示系统的运行时间和平均负载。

- 显示系统运行时间和平均负载：

  ```
  bash
  复制代码
  uptime
  ```

#### `netstat` 命令

`netstat`命令显示网络连接和插槽文件的信息。

- 显示所有网络连接和监听端口：

  ```
  bash
  复制代码
  netstat -tulnp
  ```

#### `dmesg` 命令

`dmesg`命令显示内核启动和运行过程中产生的消息。

- 显示内核消息：

  ```
  bash
  复制代码
  dmesg | more
  ```

#### `vmstat` 命令

`vmstat`命令监控系统资源的变化情况。

- 每秒显示一次系统资源使用情况，共显示三次：

  ```
  bash
  复制代码
  vmstat 1 3
  ```

通过上述工具和方法，可以全面监控和管理系统中的进程和资源，确保系统的稳定运行。

## 16.4 特殊文件与程序

在第六章中，我们已经详细解释了特殊权限的SUID、SGID和SBIT。然而，这些权限对程序的影响如何？程序可能会使用系统资源，例如磁盘。我们在卸载磁盘时常会遇到"device is busy"的提示，这与程序相关。以下是关于程序和系统资源管理的细节。

### 16.4.1 具有 SUID/SGID 权限的指令执行状态

SUID权限对程序有很大的影响。以下是SUID权限的特点：

- SUID权限仅对二进制程序（binary program）有效。
- 执行者需要对该程序具有执行权限（x）。
- SUID权限仅在程序执行过程中有效。
- 执行者在程序执行时具有该程序拥有者的权限。

SUID权限使得执行者在执行程序时，获得程序拥有者的权限。例如，执行passwd命令时，即使是普通用户，也会获得root权限。这是因为执行passwd命令会创建一个新的进程，赋予该进程SUID权限。

我们可以使用以下命令查找系统中的SUID/SGID文件：

```
bash
复制代码
find / -perm /6000
```

### 16.4.2 /proc/* 代表的意义

/proc目录中包含了系统内存中所有程序的相关信息。每个运行中的程序在/proc目录下都有对应的子目录，目录名为该程序的PID。我们可以通过查看这些子目录中的文件，获取程序的详细信息。

例如，查看PID为1（systemd）的程序信息：

```
bash
复制代码
ls /proc/1
```

常用的文件包括：

- cmdline：程序的启动命令。
- environ：程序的环境变量。

/proc目录下的其他重要文件包括：

- /proc/cmdline：内核启动参数。
- /proc/cpuinfo：CPU信息。
- /proc/meminfo：内存使用情况。
- /proc/loadavg：系统负载平均值。
- /proc/uptime：系统运行时间。

### 16.4.3 查询已打开文件或已执行程序打开之文件

以下是一些用于查询文件和程序相关信息的命令：

#### fuser

fuser命令用于查找正在使用指定文件或文件系统的程序。

```
bash
复制代码
fuser [-umv] [-k [i] [-signal]] file/dir
```

示例：

- 找出当前目录的使用情况：

  ```
  bash
  复制代码
  fuser -uv .
  ```

- 查找使用/proc文件系统的程序：

  ```
  bash
  复制代码
  fuser -mvu /proc
  ```

- 强制终止使用/home文件系统的程序：

  ```
  bash
  复制代码
  fuser -mki /home
  ```

#### lsof

lsof命令用于列出被程序所打开的文件。

```
bash
复制代码
lsof [-aUu] [+d]
```

示例：

- 列出所有打开的文件：

  ```
  bash
  复制代码
  lsof
  ```

- 列出属于root用户的程序打开的socket文件：

  ```
  bash
  复制代码
  lsof -u root -a -U
  ```

- 查找/dev目录下的所有已打开的设备：

  ```
  bash
  复制代码
  lsof +d /dev
  ```

- 查找root用户的bash进程打开的文件：

  ```
  bash
  复制代码
  lsof -u root | grep bash
  ```

#### pidof

pidof命令用于查找正在执行的程序的PID。

```
bash
复制代码
pidof [-sx] program_name
```

示例：

- 查找systemd和rsyslogd程序的PID：

  ```
  bash
  复制代码
  pidof systemd rsyslogd
  ```

通过这些工具，我们可以更好地管理和监控系统中的程序和资源，确保系统的正常运行。

## 16.5 SELinux 初探

在 CentOS 5.x 之后的版本（包括 CentOS 7），SELinux 已经成为一个非常完备的核心模块。CentOS 提供了很多管理 SELinux 的指令与机制，使其架构单纯且易于操作管理。因此，在使用 CentOS 官方提供的软件时，建议不要关闭 SELinux。接下来，我们将深入了解 SELinux 的相关内容。

### 16.5.1 什么是 SELinux

SELinux，全称 Security Enhanced Linux，是一个安全强化的 Linux 模块。它的设计目的是避免资源的误用。

- **设计目标**：避免资源的误用

SELinux 是由美国国家安全局（NSA）开发的，主要目的是为了防止内部员工误用资源，而非外部攻击。内部员工的误操作，例如将网页目录的权限设置为 777，可能导致严重的安全问题。SELinux 的出现就是为了在操作系统层面进行更细致的权限管理，从而防止这些问题。

SELinux 是一个内核模块，旨在通过细致的权限设置来控制程序和文件的访问权限。它不仅可以控制网络服务，还能控制程序对系统资源的访问。

- **传统的文件权限与帐号关系：自主式存取控制（DAC）**

在传统的 Linux 系统中，权限控制是通过 DAC 实现的，基于用户身份和文件的权限设置来决定访问权限。然而，DAC 存在一些问题，例如 root 拥有最高权限，任何程序获取了 root 权限后都可以访问系统资源。SELinux 引入了委任式存取控制（MAC），通过制定规则来控制特定程序对特定文件的访问权限。

### 16.5.2 SELinux 的运行模式

SELinux 通过 MAC 来控制程序的权限。其主要概念包括：

- **主体（Subject）**：指程序，可以与进程等同。
- **目标（Object）**：指文件系统资源。
- **政策（Policy）**：SELinux 的规则集，用于定义程序对资源的访问权限。
- **安全性本文（Security Context）**：定义了主体和目标的安全性信息。

SELinux 的运行模式有三种：

- **enforcing**：强制模式，SELinux 运行并限制访问。
- **permissive**：宽容模式，SELinux 运行但不限制访问，只记录警告。
- **disabled**：关闭模式，SELinux 不运行。

可以通过 `getenforce` 和 `sestatus` 查看当前的 SELinux 模式和政策。配置文件在 `/etc/selinux/config`。

```
bash复制代码getenforce
sestatus
```

### 16.5.3 SELinux 的启动与关闭

SELinux 的启动和关闭需要修改配置文件 `/etc/selinux/config`。修改模式后，需要重启系统。

```
bash
复制代码
vim /etc/selinux/config
```

可以使用 `setenforce` 命令在 enforcing 和 permissive 模式之间切换。

```
bash复制代码setenforce 0
setenforce 1
```

### 16.5.4 SELinux 政策内的规则管理

SELinux 的政策包含多个规则，可以通过 `getsebool` 查看所有规则的状态。

```
bash
复制代码
getsebool -a
```

通过 `setsebool` 修改规则的布林值。

```
bash
复制代码
setsebool -P 规则名称 1
```

### 16.5.5 SELinux 安全本文的修改

可以使用 `chcon` 手动修改文件的 SELinux 类型，也可以使用 `restorecon` 恢复文件的默认 SELinux 类型。

```
bash复制代码chcon -t 类型 文件
restorecon -Rv 目录
```

使用 `semanage` 可以查询和修改默认目录的安全性本文。

```
bash复制代码semanage fcontext -l
semanage fcontext -a -t 类型 目录
restorecon -Rv 目录
```

### 16.5.6 一个网络服务案例及登录文件协助

为了演示 SELinux 的实际应用，我们以 vsftpd FTP 服务器为例。通过配置 vsftpd，可以观察 SELinux 对 FTP 服务的影响，并使用相关工具解决权限问题。例如，修改 FTP 服务的 SELinux 规则或类型，以允许用户上传和下载文件。

```
bash复制代码sealert -l 错误ID
setsebool -P ftp_home_dir 1
```

通过这些步骤，可以解决由于 SELinux 引起的网络服务问题，确保系统的安全性与稳定性。

## 16.6 重点回顾

- 程序 （program）：通常为 binary program ，放置在储存媒体中 （如硬盘、光盘、软盘、磁带等），为实体文件的型态存在；
- 程序 （process）：程序被触发后，执行者的权限与属性、程序的程序码与所需数据等都会被载入内存中， 操作系统并给予这个内存内的单元一个识别码 （PID），可以说，程序就是一个正在运行中的程序。
- 程序彼此之间是有相关性的，故有父程序与子程序之分。而 Linux 系统所有程序的父程序就是 init 这个 PID 为 1 号的程序。
- 在 Linux 的程序调用通常称为 fork-and-exec 的流程！程序都会借由父程序以复制 （fork） 的方式产生一个一模一样的子程序， 然后被复制出来的子程序再以 exec 的方式来执行实际要进行的程序，最终就成为一个子程序的存在。
- 常驻在内存当中的程序通常都是负责一些系统所提供的功能以服务使用者各项任务，因此这些常驻程序就会被我们称为：服务 （daemon）。
- 在工作管理 （job control） 中，可以出现提示字符让你操作的环境就称为前景 （foreground），至于其他工作就可以让你放入背景 （background） 去暂停或运行。
- 与 job control 有关的按键与关键字有： &, [ctrl]-z, jobs, fg, bg, kill %n 等；
- 程序管理的观察指令有： ps, top, pstree 等等；
- 程序之间是可以互相控制的，传递的讯息 （signal） 主要通过 kill 这个指令在处理；
- 程序是有优先顺序的，该项目为 Priority，但 PRI 是核心动态调整的，使用者只能使用 nice 值去微调 PRI
- nice 的给予可以有： nice, renice, top 等指令；
- vmstat 为相当好用的系统资源使用情况观察指令；
- SELinux 当初的设计是为了避免使用者资源的误用，而 SELinux 使用的是 MAC 委任式存取设置；
- SELinux 的运行中，重点在于主体程序 （Subject） 能否存取目标文件资源 （Object） ，这中间牵涉到政策 （Policy） 内的规则， 以及实际的安全性本文类别 （type）；
- 安全性本文的一般设置为：“Identify:role:type”其中又以 type 最重要；
- SELinux 的模式有： enforcing, permissive, disabled 三种，而启动的政策 （Policy） 主要是 targeted
- SELinux 启动与关闭的配置文件在： /etc/selinux/config
- SELinux 的启动与观察： getenforce, sestatus 等指令
- 重设 SELinux 的安全性本文可使用 restorecon 与 chcon
- 在 SELinux 有启动时，必备的服务至少要启动 auditd 这个！
- 若要管理默认的 SELinux 布林值，可使用 getsebool, setsebool 来管理！