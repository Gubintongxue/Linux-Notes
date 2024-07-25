# 第二十章、基础系统设置与备份策略

最近更新日期：20//

新的 CentOS 7 有针对不同的服务提供了相当大量的命令行设置模式，因此过去那个 setup 似乎没有什么用了！ 取而代之的是许多加入了 bash-complete 提供了不少参数补全的设置工具！甚至包括网络设置也是通过这个机制哩！ 我们这个小章节主要就是在介绍如何通过这些基本的指令来设置系统就是了。另外， 万一不幸你的 Linux 被骇客入侵了、或是你的 Linux 系统由于硬件关系 （不论是天灾还是人祸） 而挂掉了！这个时候，请问如何快速的回复你的系统呢？呵呵！当然啰，如果有备份数据的话， 那么回复系统所花费的时间与成本将降低相当的多！平时最好就养成备份的习惯， 以免突然间的系统损毁造成手足无措！此外，哪些文件最需要备份呢？又，备份是需要完整的备份还是仅备份重要数据即可？ 嗯！确实需要考虑看看呦！

## 20.1 系统基本设置

在 CentOS 7 系统中，有许多基本设置需要进行，例如网络、日期、时间、语系等。这些设置可以通过命令行或图形界面完成。以下是对这些基本设置的详细介绍。

### 20.1.1 网络设置（手动设置与 DHCP 自动取得）

网络设置包括手动设置固定 IP 和使用 DHCP 自动获取 IP。

#### 手动设置 IP 网络参数

假设已经获得 ISP 提供的网络参数，基本设置如下：

- method: manual
- IP: 172.16.1.1
- netmask: 255.255.0.0
- gateway: 172.16.200.254
- DNS: 172.16.200.254
- hostname: study.centos.vbird

使用 `nmcli` 设置网络参数：

```
bash复制代码[root@study ~]# nmcli connection modify eth0 \
  connection.autoconnect yes \
  ipv4.method manual \
  ipv4.addresses 172.16.1.1/16 \
  ipv4.gateway 172.16.200.254 \
  ipv4.dns 172.16.200.254
[root@study ~]# nmcli connection up eth0
[root@study ~]# nmcli connection show eth0
```

#### 自动获取 IP 参数

使用 DHCP 获取 IP：

```
bash复制代码[root@study ~]# nmcli connection modify eth0 \
  connection.autoconnect yes \
  ipv4.method auto
[root@study ~]# nmcli connection up eth0
[root@study ~]# nmcli connection show eth0
```

#### 修改主机名称

使用 `hostnamectl` 修改主机名称：

```
bash复制代码[root@study ~]# hostnamectl set-hostname study.centos.vbird
[root@study ~]# hostnamectl
```

### 20.1.2 日期与时间设置

日期与时间设置包括设置时区、调整时间和网络校时。

#### 时区的显示与设置

使用 `timedatectl` 查看和设置时区：

```
bash复制代码[root@study ~]# timedatectl
[root@study ~]# timedatectl list-timezones | grep -i new
[root@study ~]# timedatectl set-timezone "Asia/Taipei"
```

#### 时间的调整

使用 `timedatectl` 设置时间：

```
bash
复制代码
[root@study ~]# timedatectl set-time "2015-09-01 12:02"
```

#### 用 `ntpdate` 手动网络校时

使用 `ntpdate` 手动校时：

```
bash复制代码[root@study ~]# ntpdate tock.stdtime.gov.tw
[root@study ~]# hwclock -w
```

### 20.1.3 语系设置

使用 `localectl` 查看和设置系统语系：

```
bash复制代码[root@study ~]# localectl
[root@study ~]# localectl set-locale LANG=en_US.utf8
[root@study ~]# systemctl isolate multi-user.target
[root@study ~]# systemctl isolate graphical.target
```

### 20.1.4 防火墙简易设置

防火墙设置可以通过图形界面或命令行完成。

#### 使用图形界面设置防火墙

1. 打开防火墙图形管理界面。
2. 在 "组态" 选项中选择 "永久记录"。
3. 在 "服务" 选项中勾选需要开放的服务（如 ssh, ftp, http, https）。
4. 在 "端口" 选项中添加非标准端口（如 222, 555）。

#### 使用命令行设置防火墙

添加并永久开放特定网段：

```
bash复制代码[root@study ~]# firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" accept'
[root@study ~]# firewall-cmd --reload
```

### 总结

通过以上步骤，可以完成网络设置、时间与日期设置、语系设置以及防火墙设置。这些基本设置确保系统能够正常运行，并具备基础的安全防护。

## 20.2 服务器硬件数据的收集

“工欲善其事，必先利其器。”了解服务器硬件状态和性能是确保其高效运行的前提。以下内容介绍了如何使用各种工具和命令来收集和分析服务器硬件数据。

### 20.2.1 以系统内置 dmidecode 解析硬件配备

`dmidecode` 是一个强大的工具，可以解析系统中的硬件信息，包括 CPU、主板和内存等。以下是使用 `dmidecode` 的示例：

#### 示例一：显示系统的硬件信息

```
bash复制代码[root@study ~]# dmidecode -t 1
# 输出主板型号、硬件基础数据等信息。
```

#### 示例二：显示内存相关的数据

```
bash复制代码[root@study ~]# dmidecode -t 17
# 输出每个内存插槽的规格和当前已安装的内存信息。
```

### 20.2.2 硬件资源的收集与分析

Linux 系统可以通过读取 `/proc` 和 `/sys` 目录下的文件来获取硬件信息，还可以使用以下命令进行硬件资源的收集与分析：

- **`lspci`**：列出系统的 PCI 设备
- **`lsusb`**：列出系统的 USB 设备
- **`iostat`**：实时显示 CPU 和存储设备的 I/O 状态

#### 使用 `lspci` 命令

`lspci` 命令可以列出系统内所有的 PCI 设备：

```
bash复制代码[root@study ~]# lspci
# 列出所有的 PCI 设备。
```

#### 使用 `lsusb` 命令

`lsusb` 命令可以列出系统内所有的 USB 设备：

```
bash复制代码[root@study ~]# lsusb
# 列出所有的 USB 设备。
```

#### 使用 `iostat` 命令

`iostat` 命令可以实时监测 CPU 和存储设备的 I/O 状态：

```
bash复制代码[root@study ~]# iostat -d 2 3 vda
# 每两秒钟监测一次 vda 设备，共监测三次。
```

### 20.2.3 了解磁盘的健康状态

为了确保数据安全，了解磁盘的健康状态非常重要。可以使用 `smartctl` 命令来监测磁盘的健康状况。以下是使用 `smartctl` 的示例：

#### 显示磁盘信息

```
bash复制代码[root@study ~]# smartctl -a /dev/sda
# 显示 /dev/sda 磁盘的详细信息。
```

#### 进行磁盘自我检测

```
bash复制代码[root@study ~]# smartctl -t short /dev/sda
# 进行一次短时间的自我检测。
```

检测完成后，可以再次使用 `smartctl -a /dev/sda` 查看检测结果。

### 总结

通过使用 `dmidecode`、`lspci`、`lsusb`、`iostat` 和 `smartctl` 等命令，可以全面了解服务器的硬件配备和健康状态。这些工具可以帮助管理员及时发现和解决硬件问题，确保服务器的稳定和高效运行。

## 20.3 备份要点

备份是个很重要的工作，很多人总是在系统损毁的时候才在哀嚎说：“我的数据啊！天哪！”此时才会发现备份数据的重要性。备份不仅是为防止数据丢失，同时也是为应对系统损毁提供保障。备份使用的媒体选择也非常多样，但每种媒体各有其优劣。下面我们来探讨备份的重要性和策略。

### 20.3.1 备份数据的考虑

备份是系统损毁时的救星，因为重新安装系统时，备份的质量会影响系统的恢复进度。了解系统为什么会损毁，以及备份的重要性，有助于制定有效的备份策略。

#### 造成系统损毁的问题

- **硬件问题**：硬件故障，如硬盘损坏，是常见的系统损毁原因。尽管有 RAID 等技术提供硬盘冗余，但 RAID 控制器本身也可能故障，因此额外的备份仍然必要。
- **软件与人为问题**：用户操作不当和软件故障也是常见的系统损毁原因。恶意攻击和系统漏洞可能导致严重的数据丢失。及时备份可以在遭受攻击后迅速恢复系统。

#### 主机角色不同，备份任务也不同

根据主机的角色和数据的重要性，备份的频率和内容会有所不同。例如，个人电脑可能每月备份一次，而服务器需要每天备份数据库和用户数据。备份策略应根据具体需求制定。

### 20.3.2 哪些 Linux 数据具有备份的意义

通常，备份的数据可以分为系统配置文件和服务数据两大类。

#### 操作系统本身需要备份的文件

- **系统配置文件**：`/etc/` 目录包含系统的配置文件，是备份的重点。
- **用户数据**：`/home/` 目录包含用户的个人数据，需要备份。
- **邮件数据**：`/var/spool/mail/` 目录包含用户的邮件数据，也应备份。
- **其他重要目录**：如 `/boot/`、`/root/` 和 `/usr/local/` 等目录，根据实际情况备份。

#### 网络服务的数据

- **配置文件**：网络服务的配置文件通常位于 `/etc/` 或 `/usr/local/` 目录。
- **服务数据**：如 Web 服务的数据目录 `/var/www/` 或 `/srv/www/`，以及数据库目录 `/var/lib/mysql/` 等。

#### 推荐需要备份的目录

- `/etc/`
- `/home/`
- `/root/`
- `/var/spool/mail/`
- `/var/spool/cron/`
- `/var/lib/`

#### 不需要备份的目录

- `/dev/`
- `/proc/`
- `/sys/`
- `/run/`
- `/mnt/`
- `/media/`
- `/tmp/`

### 20.3.3 备份用储存媒体的选择

备份储存媒体的选择取决于数据的重要性、备份频率、成本和方便性。

#### 储存媒体的考虑

常见的备份媒体包括磁带、可读写光盘、外接硬盘、NAS（网络附加存储）等。现代的备份媒体多采用容量大、传输速度快的硬盘或 NAS 设备。

#### 异地备援系统

异地备援是将备份数据存储在不同地点，以防止本地灾难对数据的影响。可以通过网络将数据传输到远程服务器，实现异地备援。

### 小结

备份是保障数据安全的重要手段。选择合适的备份策略和储存媒体，根据系统的重要性和数据的变化频率，制定合理的备份计划，可以有效应对系统损毁和数据丢失。确保备份数据的安全性和完整性，定期检查和更新备份策略，以应对不断变化的系统环境和安全威胁。

## 20.4 备份的种类、频率与工具的选择

备份的方式影响到备份的频率、存储媒体的选择以及备份工具的使用。常见的备份方式有累积备份和差异备份。了解不同的备份方式有助于选择合适的工具和策略。

### 20.4.1 完整备份之累积备份（Incremental Backup）

完整备份是将系统的所有数据备份出来，适用于需要在最短时间内恢复系统的场景。然而，完整备份耗时且占用存储空间，因此不适合频繁执行。累积备份则是在第一次完整备份后，仅备份自上次备份以来发生变化的文件，从而减少备份时间和存储需求。

#### 累积备份的原则

累积备份通过比较当前系统与上一次备份的差异，仅备份发生变化的文件。随着时间的推移，累积备份的备份数据量较小，备份速度较快。然而，恢复时需要依次还原所有累积备份，恢复过程较为繁琐。

累积备份的示意图如下：

![累积备份 （incremental backup） 操作示意图](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/img/incremental.gif)

#### 累积备份使用的备份软件

常用的完整备份工具有 `dd`、`cpio` 和 `xfsdump/xfsrestore`。其中 `dd` 可以直接读取磁盘扇区，适用于镜像备份；`cpio` 适合文件备份；`xfsdump` 支持累积备份。

示例：

```
bash复制代码# 用 dd 来将 /dev/sda 备份到完全一模一样的 /dev/sdb 硬盘上：
dd if=/dev/sda of=/dev/sdb

# 使用 cpio 来备份与还原整个系统，假设储存媒体为 SATA 磁带机：
find / -print | cpio -covB > /dev/st0  # 备份到磁带机
cpio -iduv < /dev/st0                  # 还原

# 用 xfsdump 进行完整备份和累积备份：
xfsdump -l 0 -L 'full' -M 'full' -f /backupdata/home.dump /home  # 完整备份
xfsdump -l 1 -L 'full-1' -M 'full-1' -f /backupdata/home.dump1 /home  # 第一次累积备份
```

### 20.4.2 完整备份之差异备份（Differential Backup）

差异备份也是在第一次完整备份后执行，但每次备份是与原始的完整备份比较的结果。随着时间推移，差异备份的数据量可能较大，但恢复速度较快，因为只需还原完整备份和最近一次差异备份。

差异备份的示意图如下：

![差异备份 （differential backup） 操作示意图](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/img/differential.gif)

使用 `tar` 和 `rsync` 可以进行差异备份。例如：

```
bash复制代码# 使用 tar 进行差异备份
tar -N '2015-09-01' -jpcv -f /backupdata/home.tar.bz2 /home  # 备份自 2015-09-01 以来的变化

# 使用 rsync 进行镜像备份
rsync -av /home /backupdata/  # 将 /home 镜像到 /backupdata/
```

### 20.4.3 关键数据备份

关键数据备份针对系统中的重要数据，而非整个系统。适用于非 24 小时服务的主机，重装系统后只需还原关键数据即可。使用 `tar` 备份关键数据，结合 `date` 命令可以创建带日期的备份文件。

示例：

```
bash
复制代码
tar -jpcvf mysql.`date +%Y-%m-%d`.tar.bz2 /var/lib/mysql  # 备份 mariadb 数据库
```

备份工作应自动化，通过 `crontab` 定期执行备份任务。除了基本的备份工具，台湾国家高速网络中心开发的再生龙软件（Clonezilla）也提供了裸机恢复功能，适用于大规模系统恢复。

更多信息和下载请参考：[Clonezilla 官方网站](http://clonezilla.nchc.org.tw/)

## 20.5 鸟哥的备份策略

每个系统的备份策略应根据其任务和数据的重要性量身定制。鸟哥的策略是根据系统数据的变动频率进行分阶段备份，以减少对系统性能的影响并确保数据安全。具体策略如下：

1. **硬件准备**：使用独立文件系统存储备份数据，该文件系统挂载到 `/backup` 目录。
2. **每日备份**：备份经常性变动的重要数据，例如 MySQL 数据库。
3. **每周备份**：备份不常变动的数据，如 `/home`、`/var`、`/etc`、`/boot`、`/usr/local` 目录和特定服务的配置。
4. **自动处理**：使用 `/etc/crontab` 进行自动备份。
5. **异地备援**：每月将数据烧录到光盘或通过网络传输到另一部机器。

### 20.5.1 每周系统备份脚本

以下是每周系统备份的脚本，假设你已经挂载了一个新的文件系统到 `/backup`。

```
bash复制代码#!/bin/bash
# ====================================================================
# 使用者参数输入位置：
# basedir=你用来储存此脚本所预计备份的数据之目录（请独立文件系统）
basedir=/backup/weekly  # 您只要改这里就好了！

# ====================================================================
# 下面请不要修改了！用默认值即可！
PATH=/bin:/usr/bin:/sbin:/usr/sbin; export PATH
export LANG=C

# 设置要备份的服务的配置文件，以及备份的目录
named=$basedir/named
postfixd=$basedir/postfix
vsftpd=$basedir/vsftp
sshd=$basedir/ssh
sambad=$basedir/samba
wwwd=$basedir/www
others=$basedir/others
userinfod=$basedir/userinfo

# 判断目录是否存在，若不存在则予以创建。
for dirs in $named $postfixd $vsftpd $sshd $sambad $wwwd $others $userinfod
do
    [ ! -d "$dirs" ] && mkdir -p $dirs
done

# 1. 将系统主要的服务之配置文件分别备份下来，同时也备份 /etc 全部。
cp -a /var/named/chroot/{etc,var}    $named
cp -a /etc/postfix /etc/dovecot.conf    $postfixd
cp -a /etc/vsftpd/*            $vsftpd
cp -a /etc/ssh/*            $sshd
cp -a /etc/samba/*            $sambad
cp -a /etc/{my.cnf,php.ini,httpd}    $wwwd
cd /var/lib
  tar -jpc -f $wwwd/mysql.tar.bz2     mysql
cd /var/www
  tar -jpc -f $wwwd/html.tar.bz2     html cgi-bin
cd /
  tar -jpc -f $others/etc.tar.bz2    etc
cd /usr/
  tar -jpc -f $others/local.tar.bz2    local

# 2. 关于使用者参数方面
cp -a /etc/{passwd,shadow,group}    $userinfod
cd /var/spool
  tar -jpc -f $userinfod/mail.tar.bz2    mail
cd /
  tar -jpc -f $userinfod/home.tar.bz2    home
cd /var/spool
  tar -jpc -f $userinfod/cron.tar.bz2    cron at
```

保存脚本为 `/backup/backupwk.sh` 并赋予执行权限：

```
bash复制代码chmod 700 /backup/backupwk.sh
/backup/backupwk.sh  # 记得自己试跑看看！
```

### 20.5.2 每日备份脚本

以下是每日备份的脚本，主要备份 MySQL 数据库和 CGI 程序。

```
bash复制代码#!/bin/bash
# =========================================================
# 请输入，你想让备份数据放置到那个独立的目录去
basedir=/backup/daily/  # 你只要改这里就可以了！

# =========================================================
PATH=/bin:/usr/bin:/sbin:/usr/sbin; export PATH
export LANG=C
basefile1=$basedir/mysql.$(date +%Y-%m-%d).tar.bz2
basefile2=$basedir/cgi-bin.$(date +%Y-%m-%d).tar.bz2
[ ! -d "$basedir" ] && mkdir $basedir

# 1. MysQL （数据库目录在 /var/lib/mysql）
cd /var/lib
  tar -jpc -f $basefile1 mysql

# 2. WWW 的 CGI 程序 （如果有使用 CGI 程序的话）
cd /var/www
  tar -jpc -f $basefile2 cgi-bin
```

保存脚本为 `/backup/backupday.sh` 并赋予执行权限：

```
bash复制代码chmod 700 /backup/backupday.sh
/backup/backupday.sh  # 记得自己试跑看看！
```

### 20.5.3 设置定时任务

在 `/etc/crontab` 文件中添加定时任务，确保备份脚本按计划执行：

```
plaintext复制代码30 3 * * 0 root /backup/backupwk.sh
30 2 * * * root /backup/backupday.sh
```

### 20.5.4 远端备援脚本

远端备援可以使用 `rsync` 通过 `ssh` 将数据备份到远端服务器。以下是一个示例脚本：

```
bash复制代码#!/bin/bash
remotedir=/home/backup/
basedir=/backup/weekly
host=192.168.1.100
id=dmtsai

# 下面为程序阶段！不需要修改喔！
rsync -av -e ssh $basedir ${id}@${host}:${remotedir}
```

保存脚本为 `/backup/rsync.sh` 并赋予执行权限：

```
bash
复制代码
chmod 700 /backup/rsync.sh
```

通过 `rsync` 和 `sshd`，可以实现数据的加密传输，确保备份数据的安全性。

### 20.5.5 小结

通过上述脚本和策略，鸟哥实现了数据的自动化备份和远端备援，确保数据安全和系统稳定。根据具体需求，读者可以调整脚本中的配置和路径，制定适合自己的备份策略。

## 20.6 灾难复原的考虑

之所以要备份当然就是预防系统挂点啦！如果系统真的挂点的话，那么你该如何还原系统呢？

- 硬件损毁，且具有完整备份的数据时

由于是硬件损毁，所以我们不需要考虑系统软件的不稳定问题，所以可以直接将完整的系统复原回去即可。 首先，你必须要先处理好你的硬件，举例来说，将你的硬盘作个适当的处理，譬如创建成为磁盘阵列之类的。 然后依据你的备份状态来复原。举例来说，如果是使用差异备份，那么将完整备份复原后， 将最后一次的差异备份复原回去，你的系统就恢复了！非常简单吧！

- 由于软件的问题产生的被攻破资安事件

由于系统的损毁是因为被攻击，此时即使你恢复到正常的系统，那么这个系统既然会被攻破， 没道理你还原成旧系统就不会被再次攻破！所以，此时完整备份的复原可能不是个好方式喔！最好是需要这样进行啦：

1. 先拔除网络线，最好将系统进行完整备份到其他媒体上，以备未来查验
2. 开始查阅登录文件，尝试找出各种可能的问题
3. 开始安装新系统 （最好找最新的 distribution）
4. 进行系统的升级，与防火墙相关机制的制订
5. 根据 2 的错误，在安装完成新系统后，将那些 bug 修复
6. 进行各项服务与相关数据的恢复
7. 正式上线提供服务，并且开始测试

软件资安事件造成的问题可大可小，一般来说，标准流程都是建议你将出问题的系统备份下来， 如果被追踪到你的主机曾经攻击过别人的话，那么你至少可以拿出备份数据来佐证说，你是被攻击者， 而不是主动攻击别人的坏人啊！然后，记得一定要找出问题点并予以克服，不然的话，你的系统将一再地被攻击啊！ 那样可就伤脑筋啰～