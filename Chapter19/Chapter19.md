# 第十九章、开机流程、模块管理与 Loader

最近更新日期：20//

系统开机其实是一项非常复杂的程序，因为核心得要侦测硬件并载入适当的驱动程序后， 接下来则必须要调用程序来准备好系统运行的环境，以让使用者能够顺利的操作整部主机系统。 如果你能够理解开机的原理，那么将有助于你在系统出问题时能够很快速的修复系统喔！ 而且还能够顺利的配置多重操作系统的多重开机问题。为了多重开机的问题，你就不能不学学 grub2 这个 Linux 下面优秀的开机管理程序 （boot loader）。 而在系统运行期间，你也得要学会管理核心模块呢！

## 19.1 Linux 的开机流程分析

了解开机流程可以帮助解决多重开机、密码救援、切换开机模式和修复挂载错误等问题。以下是 Linux 的开机流程概述：

### 19.1.1 开机流程一览

开机过程的主要步骤：

1. **载入 BIOS 的硬件信息与进行自我测试**：
   - BIOS/UEFI 读取硬件信息并进行自我测试，找到第一个可开机设备。
2. **读取并执行第一个开机设备内 MBR 的 boot loader**：
   - 从 MBR 中载入 boot loader（如 grub2、spfdisk）。
3. **依据 boot loader 的设置载入 Kernel**：
   - Boot loader 载入 Kernel，Kernel 进行硬件侦测和驱动程序载入。
4. **Kernel 调用 systemd 并以 default.target 流程开机**：
   - systemd 执行 sysinit.target 初始化系统。
   - systemd 启动 multi-user.target 下的本机与服务器服务。
   - systemd 执行 /etc/rc.d/rc.local 文件。
   - systemd 执行 getty.target 及登陆服务。
   - systemd 执行 graphical 需要的服务。

### 19.1.2 BIOS, boot loader 与 kernel 载入

1. **BIOS 和 MBR**：
   - BIOS 通过 INT 13 读取磁盘的 MBR，执行 boot loader。
2. **Boot Loader 的功能**：
   - 提供菜单：用户选择不同的开机项目。
   - 载入核心文件：启动操作系统。
   - 转交其他 loader：进行多重开机。
3. **载入核心和 initramfs 的功能**：
   - Boot loader 载入 Kernel 和 initramfs。
   - initramfs 是一个小型根目录，帮助 Kernel 载入必要的驱动程序。

### 19.1.3 第一支程序 systemd 及使用 default.target 进入开机程序分析

Kernel 载入后，调用 systemd 作为第一支程序。systemd 的主要功能是准备软件执行环境，启动所有系统服务。

- **常见的操作环境 target**：
  - multi-user.target：多用户模式。
  - graphical.target：图形界面模式。
  - 其他：rescue.target、emergency.target、shutdown.target 等。
- **systemd 的处理流程**：
  - systemd 依次启动 sysinit.target、basic.target、multi-user.target 和 graphical.target。

### 19.1.4 systemd 执行 sysinit.target 初始化系统、basic.target 准备系统

sysinit.target 负责系统初始化，包括挂载特殊文件系统、启用特殊文件系统、启动开机过程的讯息传递与动画执行、启动日志服务、载入核心模块和设置终端机字形等。

basic.target 负责启动基本系统服务，包括音效驱动程序、防火墙、CPU 微指令、SELinux 安全本文、开机信息写入日志、加载自定义模块等。

### 19.1.5 systemd 启动 multi-user.target 下的服务

multi-user.target 启动系统的本机和服务器服务，服务脚本位于：

- /usr/lib/systemd/system：系统默认的服务启动脚本设置。
- /etc/systemd/system：管理员自定义的脚本设置。

### 19.1.6 systemd 启动 graphical.target 下面的服务

graphical.target 启动图形界面和用户管理服务，主要包括：

- accounts-daemon.service
- gdm.service
- network.service
- rtkit-daemon.service
- systemd-update-utmp-runlevel.service

### 19.1.7 开机过程会用到的主要配置文件

主要的配置文件位于 /etc 和 /usr/lib/systemd/system 下。以下是几个重要的配置文件：

- **模块加载配置文件**：
  - /etc/modules-load.d/*.conf：指定要加载的核心模块。
  - /etc/modprobe.d/*.conf：加载模块时的参数设置。
- **其他配置文件**：
  - /etc/sysconfig/*：包括身份认证、CPU 功率管理、防火墙设置等。
  - network-scripts：网络接口配置。

通过了解上述内容，可以更好地理解和管理 Linux 的开机过程，以及解决相关的开机问题。

## 19.2 核心与核心模块

在整个开机过程中，是否能成功驱动主机硬件设备是核心（kernel）的工作。核心一般是压缩文件，因此使用核心前需解压缩，然后载入内存。此外，为了适应不断变化的硬件，目前的核心都具有“模块化驱动程序”功能，即所谓的“modules（模块化）”。模块化类似于“外挂程序”，可以由硬件开发商提供，也可以由核心本身支持。

### 核心与核心模块存放位置

- 核心：`/boot/vmlinuz` 或 `/boot/vmlinuz-version`
- 核心解压缩所需 RAM Disk：`/boot/initramfs`（`/boot/initramfs-version`）
- 核心模块：`/lib/modules/version/kernel` 或 `/lib/modules/$(uname -r)/kernel`
- 核心源代码：`/usr/src/linux` 或 `/usr/src/kernels/`（需安装）

如果核心被顺利载入系统，会有几个信息记录下来：

- 核心版本：`/proc/version`
- 系统核心功能：`/proc/sys/kernel/`

若操作系统不支持新硬件，可以通过以下方式解决：

1. 重新编译核心，并加入最新硬件驱动程序源代码。
2. 将硬件驱动程序编译成模块，在开机时载入该模块。

### 19.2.1 核心模块与相依性

核心模块的放置处在 `/lib/modules/$(uname -r)/kernel`，其中主要分为几个目录：

- arch：与硬件平台有关的项目
- crypto：核心支持的加密技术
- drivers：硬件驱动程序
- fs：核心支持的文件系统
- lib：一些函数库
- net：与网络有关的协议及防火墙模块
- sound：与音效有关的模块

模块的相依性记录在 `/lib/modules/$(uname -r)/modules.dep` 文件中，可以使用 `depmod` 指令创建或更新该文件。

```
sh
复制代码
[root@study ~]# depmod [-Ane]
```

### 19.2.2 核心模块的观察

使用 `lsmod` 指令可以查看当前载入的模块。

```
sh
复制代码
[root@study ~]# lsmod
```

可以使用 `modinfo` 指令查看模块的详细信息。

```
sh
复制代码
[root@study ~]# modinfo [-adln] [module_name|filename]
```

### 19.2.3 核心模块的载入与移除

推荐使用 `modprobe` 指令载入和移除模块，因为它会自动处理模块的相依性。

```
sh
复制代码
[root@study ~]# modprobe [-cfr] module_name
```

`insmod` 指令也可以载入模块，但需要完整路径且不处理相依性。

```
sh
复制代码
[root@study ~]# insmod [/full/path/module_name] [parameters]
```

可以使用 `rmmod` 指令移除模块。

```
sh
复制代码
[root@study ~]# rmmod [-fw] module_name
```

### 19.2.4 核心模块的额外参数设置：/etc/modprobe.d/*.conf

如果需要为核心模块设置参数，可以在 `/etc/modprobe.d/` 目录下创建扩展名为 `.conf` 的文件，并通过 `options` 指令带入参数。

例如，为某模块 `nf_conntrack_ftp` 设置参数，可以创建 `/etc/modprobe.d/vbird.conf` 文件，并加入如下内容：

```
sh
复制代码
options nf_conntrack_ftp ports=555
```

这样在下次载入该模块时，系统会自动应用这些参数。

了解和管理核心与核心模块对保证系统稳定运行至关重要，尤其是在需要支持新硬件或自定义系统行为时。

## 19.3 Boot Loader: Grub2

### 19.3.1 Boot Loader 的两个阶段

在 BIOS 读取信息后，接下来就是读取第一个启动设备的 MBR (Master Boot Record) 来执行 boot loader。boot loader 可以提供菜单功能、直接载入核心文件以及控制权移交功能，没有 boot loader 系统无法载入 kernel。

由于 MBR 的容量有限 (仅 446 Bytes)，Linux 将 boot loader 的执行和配置文件加载分成两个阶段：

- Stage 1：执行 boot loader 主程序
  - 安装在 MBR 或 boot sector 中，仅安装 boot loader 的最小主程序。
- Stage 2：主程序载入配置文件
  - 通过 boot loader 载入所有配置文件与相关环境参数文件，一般存放在 /boot/grub2 中。

/boot/grub2 中的文件：

```
plaintext复制代码[root@study ~]# ls -l /boot/grub2
-rw-r--r--.  device.map            # grub2 的设备对应文件
drwxr-xr-x.  fonts                 # 开机过程中使用的字体数据
-rw-r--r--.  grub.cfg              # grub2 的主配置文件
-rw-r--r--.  grubenv               # 一些环境区块的符号
drwxr-xr-x.  i386-pc               # 针对一般 x86 PC 所需的 grub2 相关模块
drwxr-xr-x.  locale                # 语系相关的数据
drwxr-xr-x.  themes                # 开机主题数据
```

### 19.3.2 Grub2 的配置文件 /boot/grub2/grub.cfg 初探

grub2 的优点包括：

- 支持较多文件系统，可以直接在文件系统中搜索核心文件名。
- 开机时可以编辑和修改开机设置项目，类似 bash 的指令模式。
- 动态搜索配置文件，修改后不需要重新安装 grub2，只需修改 /boot/grub2/grub.cfg 即可。

配置文件示例：

```
plaintext复制代码[root@study ~]# vim /boot/grub2/grub.cfg
### BEGIN /etc/grub.d/00_header ###
set pager=1
...
### END /etc/grub.d/00_header ###

### BEGIN /etc/grub.d/10_linux ###
menuentry 'CentOS Linux 7 (Core), with Linux 3.10.0-229.el7.x86_64' --class rhel fedora \
  --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option \
  'gnulinux-3.10.0-229.el7.x86_64-advanced-299bdc5b-de6d-486a-a0d2-375402aaab27' {
        load_video
        set gfxpayload=keep
        insmod gzio
        insmod part_gpt
        insmod xfs
        set root='hd0,gpt2'
        if [ x$feature_platform_search_hint = xy ]; then
          search --no-floppy --fs-uuid --set=root --hint='hd0,gpt2'  94ac5f77-cb8a-495e-a65b-...
        else
          search --no-floppy --fs-uuid --set=root 94ac5f77-cb8a-495e-a65b-2ef7442b837c
        fi
        linux16 /vmlinuz-3.10.0-229.el7.x86_64 root=/dev/mapper/centos-root ro \
                rd.lvm.lv=centos/root rd.lvm.lv=centos/swap crashkernel=auto rhgb quiet \
                LANG=zh_TW.UTF-8
        initrd16 /initramfs-3.10.0-229.el7.x86_64.img
}
### END /etc/grub.d/10_linux ###
...
```

### 19.3.3 Grub2 配置文件维护 /etc/default/grub 与 /etc/grub.d

配置文件示例：

```
plaintext复制代码[root@study ~]# cat /etc/default/grub
GRUB_TIMEOUT=5
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="rd.lvm.lv=centos/root rd.lvm.lv=centos/swap crashkernel=auto rhgb quiet"
GRUB_DISABLE_RECOVERY="true"
```

调整后使用 `grub2-mkconfig` 重新生成配置文件：

```
sh
复制代码
[root@study ~]# grub2-mkconfig -o /boot/grub2/grub.cfg
```

### 19.3.4 initramfs 的重要性与创建新 initramfs 文件

initramfs 用于在开机过程中提供必要的核心模块，以便系统顺利启动。创建新的 initramfs 文件使用 `dracut` 工具：

```
sh
复制代码
[root@study ~]# dracut [-fv] [--add-drivers 列表] initramfs文件名 核心版本
```

示例：

```
sh
复制代码
[root@study ~]# dracut -v initramfs-test.img $(uname -r)
```

### 19.3.5 测试与安装 Grub2

如果原来使用的不是 grub2，需要安装 grub2：

```
sh
复制代码
[root@study ~]# grub2-install [--boot-directory=DIR] INSTALL_DEVICE
```

示例：

```
sh
复制代码
[root@study ~]# grub2-install /dev/vda
```

### 19.3.6 开机前的额外功能修改

Grub2 支持开机前的在线编辑功能。按下 'e' 键进入编辑模式，可以修改启动参数。

### 19.3.7 关于开机画面与终端机画面的图形显示方式

修改配置文件以使用图形显示方式：

```
sh复制代码[root@study ~]# vim /etc/default/grub
GRUB_TERMINAL=gfxterm
GRUB_GFXMODE=1024x768x24
GRUB_GFXPAYLOAD_LINUX=keep

[root@study ~]# grub2-mkconfig -o /boot/grub2/grub.cfg
```

### 19.3.8 为个别菜单加上密码

Grub2 可以为每个菜单项设置密码。设置系统管理员密码和一般用户密码：

```
sh复制代码[root@study ~]# grub2-mkpasswd-pbkdf2
[root@study ~]# vim /etc/grub.d/01_users
cat << eof
set superusers="vbird"
password_pbkdf2 vbird grub.pbkdf2.sha512.10000.9A2EBF7A1F484904FF3681F97AE22D58DFBFE65A...
password_pbkdf2 dmtsai grub.pbkdf2.sha512.10000.B59584C33BC12F3C9DB8B18BE9F557631473AED...
eof

[root@study ~]# chmod a+x /etc/grub.d/01_users
[root@study ~]# grub2-mkconfig -o /boot/grub2/grub.cfg
```

### 小结

1. 从其他 boot loader 转成 grub2 时，先使用 `grub2-install` 安装 grub2 配置文件。
2. 修改 /etc/default/grub 和 /etc/grub.d/ 内的配置文件。
3. 使用 `grub2-mkconfig` 创建新的配置文件。
4. 可以为每个菜单项设置密码，以保护系统安全。

## 19.4 开机过程的问题解决

在 Linux 系统中，有时候因为设置错误、不正常关机等原因导致系统无法启动。以下是一些常见问题及其解决方法。

### 19.4.1 忘记 root 密码的解决之道

如果忘记了 root 密码，可以通过 `rd.break` 核心参数进行救援。具体步骤如下：

1. 重启系统，在 grub2 菜单上选择相应的启动项并按下 `e` 进入编辑模式。
2. 在 `linux16` 行末尾添加 `rd.break` 参数，然后按 `Ctrl+x` 启动。
3. 系统启动后会进入 RAM Disk 环境，此时根目录挂载在 `/sysroot` 下。

```
sh复制代码switch_root:/# mount -o remount,rw /sysroot
switch_root:/# chroot /sysroot
sh-4.2# echo "your_root_new_pw" | passwd --stdin root
sh-4.2# touch /.autorelabel
sh-4.2# exit
switch_root:/# reboot
```

### 19.4.2 直接开机就以 root 执行 bash 的方法

通过在 grub2 菜单中添加 `init=/bin/bash` 参数，可以直接以 root 身份进入 bash。

1. 重启系统，在 grub2 菜单上选择相应的启动项并按下 `e` 进入编辑模式。
2. 在 `linux16` 行末尾添加 `init=/bin/bash` 参数，然后按 `Ctrl+x` 启动。
3. 系统启动后进入 bash，此时需要重新挂载根目录为读写模式。

```
sh复制代码# 挂载根目录为读写模式
mount -o remount,rw /

# 修改 root 密码
echo "your_root_new_pw" | passwd --stdin root

# 重新标记 SELinux 标签
touch /.autorelabel

# 重启系统
reboot -f
```

### 19.4.3 因文件系统错误而无法开机

文件系统错误可能导致系统无法启动，可以通过以下步骤解决：

1. 重启系统，在 grub2 菜单上选择相应的启动项并按下 `e` 进入编辑模式。
2. 在 `linux16` 行末尾添加 `rd.break` 参数，然后按 `Ctrl+x` 启动。
3. 系统启动后会进入 RAM Disk 环境，此时根目录挂载在 `/sysroot` 下。

```
sh复制代码# 挂载根目录为读写模式
switch_root:/# mount -o remount,rw /sysroot
switch_root:/# chroot /sysroot

# 修复文件系统
fsck -y /dev/sdXn  # 使用适当的设备名称

# 修复 XFS 文件系统
xfs_repair /dev/sdXn  # 使用适当的设备名称

# 重新标记 SELinux 标签
touch /.autorelabel

# 退出并重启系统
exit
switch_root:/# reboot
```

通过以上方法，可以解决大多数由于文件系统错误或忘记 root 密码导致的系统无法启动问题。在操作过程中需要注意细节，确保正确输入命令和参数，以免造成进一步的问题。

## 19.5 重点回顾

- Linux 不可随意关机，否则容易造成文件系统错乱或者是其他无法开机的问题；
- 开机流程主要是：BIOS、MBR、Loader、kernel+initramfs、systemd 等流程
- Loader 具有提供菜单、载入核心文件、转交控制权给其他 loader 等功能。
- boot loader 可以安装在 MBR 或者是每个分区的 boot sector 区域中
- initramfs 可以提供核心在开机过程中所需要的最重要的模块，通常与磁盘及文件系统有关的模块；
- systemd 的配置文件为主要来自 /etc/systemd/system/default.target 项目；
- 额外的设备与模块对应，可写入 /etc/modprobe.d/*.conf 中；
- 核心模块的管理可使用 lsmod, modinfo, rmmod, insmod, modprobe 等指令；
- modprobe 主要参考 /lib/modules/$（uanem -r）/modules.dep 的设置来载入与卸载核心模块；
- grub2 的配置文件与相关文件系统定义文件大多放置于 /boot/grub2 目录中，配置文件名为 grub.cfg
- grub2 对磁盘的代号设置与 Linux 不同，主要通过侦测的顺序来给予设置。如 （hd0） 及 （hd0,1） 等。
- grub.cfg 内每个菜单与 menuentry 有关，而直接指定核心开机时，至少需要 linux16 及 initrd16 两个项目
- grub.cfg 内设置 loader 控制权移交时，最重要者为 chainloader +1 这个项目。
- 若想要重建 initramfs ，可使用 dracut 或 mkinitrd 处理
- 重新安装 grub2 到 MBR 或 boot sector 时，可以利用 grub2-install 来处理。
- 若想要进入救援模式，可于开机菜单过程中，在 linux16 的项目后面加入“ rd.break ”或“ init=/bin/bash ”等方式来进入救援模式。
- 我们可以对 grub2 的个别菜单给予不同的密码。