# 第八章、文件与文件系统的压缩,打包与备份

最近更新日期：20//

在 Linux 下面有相当多的压缩指令可以运行喔！这些压缩指令可以让我们更方便从网络上面下载容量较大的文件呢！ 此外，我们知道在 Linux 下面的扩展名是没有什么很特殊的意义的，不过，针对这些压缩指令所做出来的压缩文件， 为了方便记忆，还是会有一些特殊的命名方式啦！就让我们来看看吧！

### 8.1 压缩文件的用途与技术总结

在本节中，我们探讨了文件压缩技术的用途、基本原理以及带来的好处。以下是关键点的回顾：

#### 压缩文件的用途

1. **减少文件大小**：
   - 便于通过电子邮件发送大文件。
   - 适合于容量受限的存储介质（如CD或DVD）。
   - 节省磁盘空间，用于备份大量数据。
2. **便于文件传输**：
   - 通过减少数据量，提高网络传输效率。
   - 压缩后的文件在网络传输中占用更少带宽，提高网站的响应速度。

#### 文件压缩的技术原理

1. **位填充压缩**：
   - 利用计算机文件中的空闲空间，通过复杂计算方式将未使用的位压缩掉，从而减少文件大小。
2. **重复数据压缩**：
   - 对文件中的重复数据进行统计记录，例如将连续的100个“1”记录为“100个1”。

#### 文件压缩的好处

1. **节省存储空间**：
   - 压缩后的文件占用更少的磁盘空间，从而可以存储更多的数据。
2. **提高传输效率**：
   - 压缩后的数据在网络上传输时，占用带宽更少，使得传输速度更快。
3. **应用于网站优化**：
   - 网站的数据传输可以通过压缩技术进行优化，提高带宽利用率和页面加载速度。

文件压缩技术通过有效地减少文件的实际存储空间和传输数据量，为我们日常的数据管理和传输带来了极大的便利。了解这些基本概念后，可以更好地应用于实际操作中，提高工作效率。

### 8.2 Linux 系统常见的压缩指令总结

在本节中，我们详细探讨了Linux系统中的几种常见压缩指令，包括它们的用途、基本用法和优缺点。以下是对各个压缩指令的详细总结。

#### 常见压缩文件的扩展名

- **.Z**：由 compress 程序压缩的文件。
- **.zip**：由 zip 程序压缩的文件。
- **.gz**：由 gzip 程序压缩的文件。
- **.bz2**：由 bzip2 程序压缩的文件。
- **.xz**：由 xz 程序压缩的文件。
- **.tar**：由 tar 程序打包的文件（未压缩）。
- **.tar.gz**：由 tar 程序打包并经过 gzip 压缩的文件。
- **.tar.bz2**：由 tar 程序打包并经过 bzip2 压缩的文件。
- **.tar.xz**：由 tar 程序打包并经过 xz 压缩的文件。

#### gzip, zcat/zmore/zless/zgrep

- **用途**：gzip 是最广泛使用的压缩指令，可以解压缩 compress、zip 和 gzip 创建的文件。

- 基本用法

  ：

  - `gzip [-cdtv#] 文件名`：压缩文件。
  - `zcat 文件名.gz`：查看压缩文件内容。
  - `gzip -d 文件名.gz`：解压缩文件。

- **优点**：压缩速度快，支持解压缩多种格式。

- **缺点**：压缩比不如 bzip2 和 xz。

- 示例

  ：

  - 压缩文件：`gzip -v services`
  - 解压缩文件：`gzip -d services.gz`
  - 查看压缩文件内容：`zcat services.gz`

#### bzip2, bzcat/bzmore/bzless/bzgrep

- **用途**：bzip2 提供比 gzip 更高的压缩比。

- 基本用法

  ：

  - `bzip2 [-cdkzv#] 文件名`：压缩文件。
  - `bzcat 文件名.bz2`：查看压缩文件内容。
  - `bzip2 -d 文件名.bz2`：解压缩文件。

- **优点**：压缩比优于 gzip。

- **缺点**：压缩和解压缩速度较慢。

- 示例

  ：

  - 压缩文件：`bzip2 -v services`
  - 解压缩文件：`bzip2 -d services.bz2`
  - 查看压缩文件内容：`bzcat services.bz2`

#### xz, xzcat/xzmore/xzless/xzgrep

- **用途**：xz 提供更高的压缩比，适用于需要最大化压缩的场景。

- 基本用法

  ：

  - `xz [-dtlkc#] 文件名`：压缩文件。
  - `xzcat 文件名.xz`：查看压缩文件内容。
  - `xz -d 文件名.xz`：解压缩文件。

- **优点**：压缩比最高，适合大文件压缩。

- **缺点**：压缩和解压缩速度最慢。

- 示例

  ：

  - 压缩文件：`xz -v services`
  - 解压缩文件：`xz -d services.xz`
  - 查看压缩文件内容：`xzcat services.xz`

#### tar 指令及其应用

- **用途**：tar 主要用于打包多个文件或目录，常与 gzip、bzip2 和 xz 结合使用。

- 基本用法

  ：

  - 打包并压缩：`tar -zcvf archive.tar.gz 文件/目录`
  - 解压缩并解包：`tar -zxvf archive.tar.gz`
  - 查看压缩包内容：`tar -ztvf archive.tar.gz`
  - 指定解压目录：`tar -zxvf archive.tar.gz -C 目标目录`

- 示例

  ：

  - 打包并压缩：`tar -jcv -f /root/etc.tar.bz2 /etc`
  - 解压缩并解包：`tar -jxv -f /root/etc.tar.bz2 -C /tmp`
  - 仅解压特定文件：`tar -jxv -f /root/etc.tar.bz2 etc/shadow`
  - 打包时排除特定文件：`tar -jcv -f /root/system.tar.bz2 --exclude=/root/etc* --exclude=/root/system.tar.bz2 /etc /root`

#### 其他注意事项

- **SELinux 问题**：在解压缩系统文件时，可能需要修复 SELinux 标签，可以使用 `restorecon -Rv /etc` 或创建 `/.autorelabel` 文件后重启系统。
- **时间成本**：不同压缩工具的压缩比和压缩时间有所不同，需要根据具体情况选择合适的工具。

通过了解这些压缩指令的用途和基本用法，可以根据具体需求选择合适的压缩工具，提高工作效率和数据管理能力。

### 8.3 打包指令： tar

在 Linux 中，gzip、bzip2、xz 等指令通常只能针对单个文件进行压缩，虽然它们也能压缩目录，但指的是“将目录内的所有文件分别进行压缩”的动作。tar（Tape Archive）可以将多个文件或目录打包成一个大文件，并通过 gzip、bzip2 或 xz 进行压缩。这种功能在需要将多个文件和目录打包成一个压缩包时非常有用。

### 8.3.1 tar

#### tar 的选项与参数

- **基本语法**：
  - 打包并压缩：`tar [-z|-j|-J] [cv] [-f 新文件名] 文件或目录`
  - 查看内容：`tar [-z|-j|-J] [tv] [-f 现有的 tar 文件名]`
  - 解压并解包：`tar [-z|-j|-J] [xv] [-f 现有的 tar 文件名] [-C 目录]`
- **常用选项**：
  - `-c`：创建新的打包文件。
  - `-t`：查看打包文件的内容。
  - `-x`：解打包或解压缩。
  - `-z`：通过 gzip 支持进行压缩/解压缩。
  - `-j`：通过 bzip2 支持进行压缩/解压缩。
  - `-J`：通过 xz 支持进行压缩/解压缩。
  - `-v`：显示详细信息。
  - `-f`：指定打包文件名。
  - `-C`：指定解包目录。
  - `-p`：保留原文件的权限和属性。
  - `-P`：保留绝对路径。
  - `--exclude`：排除指定的文件或目录。

### 示例操作

#### 示例 1：使用 tar 加入 -z, -j 或 -J 的参数备份 /etc/ 目录

```
sh复制代码[root@study ~]# tar -zpcv -f /root/etc.tar.gz /etc
tar: Removing leading `/' from member names
/etc/
/etc/hostname
/etc/aliases.db

real    0m0.799s
user    0m0.767s
sys     0m0.046s

[root@study ~]# tar -jpcv -f /root/etc.tar.bz2 /etc
real    0m1.913s
user    0m1.881s
sys     0m0.038s

[root@study ~]# tar -Jpcv -f /root/etc.tar.xz /etc
real    0m9.023s
user    0m8.984s
sys     0m0.086s
```

#### 示例 2：查看 tar 文件内容

```
sh复制代码[root@study ~]# tar -jtv -f /root/etc.tar.bz2
-rw-r--r-- root/root       131 2015-05-25 17:48 etc/locale.conf
-rw-r--r-- root/root        19 2015-05-04 17:56 etc/hostname
-rw-r--r-- root/root     12288 2015-05-04 17:59 etc/aliases.db
```

#### 示例 3：将 tar 文件解压缩到指定目录

```
sh复制代码[root@study ~]# tar -jxv -f /root/etc.tar.bz2 -C /tmp
[root@study ~]# ll /tmp
drwxr-xr-x. 131 root root 8192 Jun 26 22:14 etc
```

#### 示例 4：仅解压单一文件

```
sh复制代码[root@study ~]# tar -jtv -f /root/etc.tar.bz2 | grep 'shadow'
---------- root/root       721 2015-06-17 00:20 etc/gshadow
---------- root/root      1183 2015-06-17 00:20 etc/shadow-
---------- root/root      1210 2015-06-17 00:20 etc/shadow
---------- root/root       707 2015-06-17 00:20 etc/gshadow-

[root@study ~]# tar -jxv -f /root/etc.tar.bz2 etc/shadow
etc/shadow
[root@study ~]# ll etc
total 4
----------. 1 root root 1210 Jun 17 00:20 shadow
```

#### 示例 5：打包目录但不包含某些文件

```
sh
复制代码
[root@study ~]# tar -jcv -f /root/system.tar.bz2 --exclude=/root/etc* --exclude=/root/system.tar.bz2 /etc /root
```

#### 示例 6：仅备份比某个时刻还要新的文件

```
sh复制代码[root@study ~]# find /etc -newer /etc/passwd
-rw-r--r--. 1 root root 2092 Jun 17 00:20 /etc/passwd

[root@study ~]# tar -jcv -f /root/etc.newer.tar.bz2 --newer-mtime="2015/06/17" /etc/*
tar: Removing leading `/' from member names
/etc/abrt/
/etc/alsa/
/etc/yum.repos.d/
tar: /etc/yum.repos.d/CentOS-fasttrack.repo: file is unchanged; not dumped

[root@study ~]# tar -jtv -f /root/etc.newer.tar.bz2 | grep -v '/$'
```

### 备份系统的例题

假设我们需要备份以下重要目录：

- /etc/
- /home/
- /var/spool/mail/
- /var/spool/cron/
- /root/

同时排除 /root 目录下的压缩文件以及 /home/loop* 文件，备份文件名要包含日期：

```
sh复制代码[root@study ~]# mkdir /backups
[root@study ~]# chmod 700 /backups
[root@study ~]# tar -jcv -f /backups/backup-system-20150701.tar.bz2 --exclude=/root/*.bz2 --exclude=/root/*.gz --exclude=/home/loop* /etc /home /var/spool/mail /var/spool/cron /root
[root@study ~]# ll -h /backups/
-rw-r--r--. 1 root root 21M Jul 1 17:26 backup-system-20150701.tar.bz2
```

### 解压缩后的 SELinux 问题

复原系统后，如果遇到 SELinux 导致的权限问题，可以通过以下方法解决：

1. 修改 /etc/selinux/config 文件，将 SELinux 改成 permissive 模式，并重新启动。
2. 使用 `restorecon -Rv /etc` 修复 SELinux 类型。
3. 创建 /.autorelabel 文件，并重新启动系统让 SELinux 自动修复类型。

通过以上方法，可以解决解压缩后可能遇到的 SELinux 问题

### 8.4 XFS 文件系统的备份与还原

使用 tar 通常是针对目录树系统进行备份，那么如果想要备份和还原整个文件系统，可以使用 CentOS 7 默认的 XFS 文件系统的工具 xfsdump 和 xfsrestore。下面详细介绍这些工具的使用方法。

### 8.4.1 XFS 文件系统备份 xfsdump

xfsdump 的功能强大，可以进行完整备份（full backup）和累积备份（Incremental backup）。完整备份被定义为 level 0，累积备份则是记录与之前备份所有差异的文件。

![xfsdump 运行时，完整备份与累积备份示意图](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/img/centos7_dump-1.gif)

#### 使用 xfsdump 的注意事项：

- 只能备份已挂载的文件系统
- 需要 root 权限
- 只能备份 XFS 文件系统
- 备份数据只能由 xfsrestore 解析
- 使用文件系统的 UUID 来分辨备份文件，不能备份两个具有相同 UUID 的文件系统

#### xfsdump 常用选项：

```
shell复制代码[root@study ~]# xfsdump [-L S_label] [-M M_label] [-l #] [-f 备份文件] 待备份数据
[root@study ~]# xfsdump -I
```

- `-L`：记录每次备份的 session 标头
- `-M`：记录储存媒体的标头
- `-l`：指定等级，有 0~9 共 10 个等级（默认 0，即完整备份）
- `-f`：指定生成的备份文件或设备文件名
- `-I`：列出当前备份的信息状态

#### 备份完整的文件系统：

1. 确认 /boot 是独立的文件系统：

```
shell
复制代码
[root@study ~]# df -h /boot
```

1. 备份文件系统：

```
shell
复制代码
[root@study ~]# xfsdump -l 0 -L boot_all -M boot_all -f /srv/boot.dump /boot
```

1. 查看备份文件和信息：

```
shell复制代码[root@study ~]# ll /srv/boot.dump
[root@study ~]# ll /var/lib/xfsdump/inventory
```

#### 进行累积备份：

1. 确认已有完整备份：

```
shell
复制代码
[root@study ~]# xfsdump -I
```

1. 创建大约 10 MB 的文件：

```
shell
复制代码
[root@study ~]# dd if=/dev/zero of=/boot/testing.img bs=1M count=10
```

1. 创建差异备份文件：

```
shell
复制代码
[root@study ~]# xfsdump -l 1 -L boot_2 -M boot_2 -f /srv/boot.dump1 /boot
```

1. 查看差异备份文件：

```
shell复制代码[root@study ~]# ll /srv/boot*
[root@study ~]# xfsdump -I
```

### 8.4.2 XFS 文件系统还原 xfsrestore

xfsrestore 用于还原由 xfsdump 备份的数据。

#### xfsrestore 常用选项：

```
shell复制代码[root@study ~]# xfsrestore -I
[root@study ~]# xfsrestore [-f 备份文件] [-L S_label] [-s] 待复原目录
[root@study ~]# xfsrestore [-f 备份文件] -r 待复原目录
[root@study ~]# xfsrestore [-f 备份文件] -i 待复原目录
```

- `-I`：查询备份数据，包括 Label 名称与备份时间等
- `-f`：指定备份文件
- `-L`：指定 Session 的 Label name
- `-s`：仅复原某一个文件或目录
- `-r`：用于累积复原
- `-i`：进入互动模式

#### 观察备份数据内容：

```
shell
复制代码
[root@study ~]# xfsrestore -I
```

#### 简单复原 level 0 的文件系统：

1. 将数据覆盖回去：

```
shell
复制代码
[root@study ~]# xfsrestore -f /srv/boot.dump -L boot_all /boot
```

1. 将备份数据在 /tmp/boot 下解开：

```
shell复制代码[root@study ~]# mkdir /tmp/boot
[root@study ~]# xfsrestore -f /srv/boot.dump -L boot_all /tmp/boot
```

1. 检查文件差异：

```
shell
复制代码
[root@study ~]# diff -r /boot /tmp/boot
```

#### 仅复原备份文件内的特定目录：

```
shell复制代码[root@study ~]# mkdir /tmp/boot2
[root@study ~]# xfsrestore -f /srv/boot.dump -L boot_all
```

### 8.5 光盘写入工具

虽然企业仍然喜欢使用磁带进行备份，因为它们具有高容量、长储存时限和耐摔等优点，但 DVD/CD 等光盘由于存储速度慢和容量有限，已经逐渐被 U 盘和 USB 外接式硬盘所取代。不过，某些情况下还是需要使用光盘，所以了解创建光盘镜像文件和烧录软件还是很有必要的。

#### 8.5.1 mkisofs：创建镜像文件

mkisofs 用于将数据打包成 ISO 镜像文件，该镜像文件可以用于光盘烧录。基本使用方法如下：

```
bash
复制代码
[root@study ~]# mkisofs [-o 镜像文件] [-Jrv] [-V vol] [-m file] 待备份文件...
```

常用选项：

- `-o`：指定生成的镜像文件名
- `-J`：生成适用于 Windows 机器的文件名结构
- `-r`：支持 Unix/Linux 的文件数据，记录更多信息（如 UID/GID 等）
- `-v`：显示创建 ISO 文件的过程
- `-V`：创建卷标，类似于 Windows 中的 CD title
- `-m`：排除指定文件（支持 * 万用字符）
- `-graft-point`：定义镜像文件中的目录结构

示例：创建包含 /root、/home、/etc 目录的镜像文件

```
bash
复制代码
[root@study ~]# mkisofs -r -v -o /tmp/system.img /root /home /etc
```

如果文件名有冲突，使用 `-graft-point` 选项来指定目录结构：

```
bash
复制代码
[root@study ~]# mkisofs -r -V 'linux_file' -o /tmp/system.img -m /root/etc -graft-point /root=/root /home=/home /etc=/etc
```

#### 8.5.2 cdrecord：光盘烧录工具

新版 CentOS 7 使用 wodim 进行烧录，兼容旧版的 cdrecord 命令。常用选项：

```
bash复制代码[root@study ~]# wodim --devices dev=/dev/sr0...                  # 查询烧录机位置
[root@study ~]# wodim -v dev=/dev/sr0 blank=[fast|all]           # 抹除可重复读写光盘
[root@study ~]# wodim -v dev=/dev/sr0 -format                    # 格式化 DVD+RW
[root@study ~]# wodim -v dev=/dev/sr0 [可用选项功能] file.iso    # 烧录 ISO 文件
```

常用参数：

- `--devices`：扫描磁盘总线并找出可用烧录机
- `-v`：显示过程
- `dev=/dev/sr0`：指定光驱设备
- `blank=[fast|all]`：抹除可重复写入的光盘，fast 较快，all 较完整
- `-format`：格式化 DVD+RW
- `-data`：指定以数据格式写入
- `speed=X`：指定烧录速度
- `-eject`：烧录完毕后自动退出光盘
- `fs=Ym`：指定缓冲内存大小
- `driveropts=burnfree`：打开 Buffer Underrun Free 功能
- `-sao`：支持 DVD-RW 格式

#### 步骤：找到光驱设备并进行烧录

1. 查找光驱位置：

```
bash
复制代码
[root@study ~]# wodim --devices dev=/dev/sr0
```

1. 抹除光盘内容（若非可重复读写光盘可略过）：

```
bash
复制代码
[root@demo ~]# wodim -v dev=/dev/sr0 blank=fast
```

1. 烧录镜像文件：

```
bash
复制代码
[root@demo ~]# wodim -v dev=/dev/sr0 speed=4 -dummy -eject /tmp/system.img
```

1. 测试挂载烧录的光盘：

```
bash复制代码[root@demo ~]# mount /dev/sr0 /mnt
[root@demo ~]# df -h /mnt
[root@demo ~]# umount /mnt
```

使用文字模式进行光盘烧录较为复杂，但掌握基本命令可以应对一些特殊情况。对于服务器备份，选择合适的工具和方法至关重要。

### 8.6 其他常见的压缩与备份工具

除了前面介绍的工具，dd 和 cpio 也是非常有用的备份工具，特别是 dd。

#### 8.6.1 dd

dd 命令不仅可以制作文件，还可以直接读取和备份磁盘设备的内容。dd 的使用非常灵活，以下是一些常用选项：

```
bash
复制代码
[root@study ~]# dd if="input_file" of="output_file" bs="block_size" count="number"
```

- `if`：输入文件或设备
- `of`：输出文件或设备
- `bs`：每个块的大小，默认为 512 Bytes
- `count`：块的数量

**示例一：将 /etc/passwd 备份到 /tmp/passwd.back**

```
bash
复制代码
[root@study ~]# dd if=/etc/passwd of=/tmp/passwd.back
```

**示例二：将光驱内容备份为镜像文件**

```
bash
复制代码
[root@study ~]# dd if=/dev/sr0 of=/tmp/system.iso
```

**示例三：将镜像文件写入 USB 磁盘**

```
bash
复制代码
[root@study ~]# dd if=/tmp/system.iso of=/dev/sda
```

**示例四：备份 /boot 文件系统**

```
bash
复制代码
[root@study ~]# dd if=/dev/vda2 of=/tmp/vda2.img
```

#### 8.6.2 cpio

cpio 是一个功能强大的备份工具，可以备份包括设备文件在内的任何文件。但 cpio 需要其他工具（如 find）来提供要备份的文件列表。常用选项如下：

```
bash复制代码[root@study ~]# cpio -ovcB > [file|device]    # 备份
[root@study ~]# cpio -ivcdu < [file|device]   # 还原
[root@study ~]# cpio -ivct < [file|device]    # 察看
```

- `-o`：将数据输出到文件或设备
- `-B`：增加 Blocks 大小至 5120 Bytes，加快大文件储存速度
- `-i`：将数据从文件或设备恢复到系统
- `-d`：自动创建目录
- `-u`：覆盖较旧的文件
- `-t`：查看 cpio 创建的文件或设备内容
- `-v`：显示过程中的文件名称
- `-c`：使用新的 portable format 储存

**示例：备份 /boot 目录并还原**

1. 使用 find 找出所有文件并备份到 /tmp/boot.cpio：

```
bash复制代码[root@study ~]# cd /
[root@study /]# find boot | cpio -ocvB > /tmp/boot.cpio
```

1. 在 /root 目录下还原备份的文件：

```
bash复制代码[root@study ~]# cd ~
[root@study ~]# cpio -idvc < /tmp/boot.cpio
```

**示例：使用 cpio 备份和还原磁带机**

```
bash复制代码[root@study ~]# find / | cpio -ocvB > /dev/st0   # 备份
[root@study ~]# cpio -idvc < /dev/st0           # 还原
```

通过这些示例，可以看出 dd 和 cpio 是非常强大的备份工具，尤其适用于备份设备文件和进行系统恢复。掌握这些工具的使用，可以大大提高系统管理和数据保护的效率。

## 8.7 重点回顾

- 压缩指令为通过一些运算方法去将原本的文件进行压缩，以减少文件所占用的磁盘容量。 压缩前与压缩后的文件所占用的磁盘容量比值， 就可以被称为是“压缩比”
- 压缩的好处是可以减少磁盘容量的浪费，在 WWW 网站也可以利用文件压缩的技术来进行数据的传送，好让网站带宽的可利用率上升喔
- 压缩文件的扩展名大多是：“*.gz,* .bz2, *.xz,* .tar, *.tar.gz,* .tar.bz2, *.tar.xz”
- 常见的压缩指令有 gzip, bzip2, xz。压缩率最佳的是 xz，若可以不计时间成本，建议使用 xz 进行压缩。
- tar 可以用来进行文件打包，并可支持 gzip, bzip2, xz 的压缩。
- 压　缩：tar -Jcv -f filename.tar.xz 要被压缩的文件或目录名称
- 查　询：tar -Jtv -f filename.tar.xz
- 解压缩：tar -Jxv -f filename.tar.xz -C 欲解压缩的目录
- xfsdump 指令可备份文件系统或单一目录
- xfsdump 的备份若针对文件系统时，可进行 0-9 的 level 差异备份！其中 level 0 为完整备份；
- xfsrestore 指令可还原被 xfsdump 创建的备份文件；
- 要创建光盘烧录数据时，可通过 mkisofs 指令来创建；
- 可通过 wodim 来写入 CD 或 DVD 烧录机
- dd 可备份完整的 partition 或 disk ，因为 dd 可读取磁盘的 sector 表面数据
- cpio 为相当优秀的备份指令，不过必须要搭配类似 find 指令来读入欲备份的文件名数据，方可进行备份动作。