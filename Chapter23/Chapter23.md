# 第二十三章、X Window 设置介绍

最近更新日期：20//

在 Linux 上头的图形接口我们称之为 X Window System，简称为 X 或 X11 啰！ 为何称之为系统呢？这是因为 X 窗口系统又分为 X server 与 X client ，既然是 Server/Client （主从架构） 这就表示其实 X 窗口系统是可以跨网络且跨平台的！X 窗口系统对于 Linux 来说仅是一个软件， 只是这个软件日趋重要喔！因为 Linux 是否能够在桌面电脑上面流行，与这个 X 窗口系统有关啦！ 好在，目前的 X 窗口系统整合到 Linux 已经非常优秀了，而且也能够具有 3D 加速的功能， 只是，我们还是得要了解一下 X 窗口系统才好，这样如果出问题，我们才有办法处理啊！

## 23.1 什么是 X Window System

Unix Like 操作系统不仅用于服务器架设，在美编、排版、制图、多媒体应用上也有其重要性，这些需求都需要图形用户界面（Graphical User Interface, GUI）。因此，X Window System（简称 X）应运而生。

### 23.1.1 X Window 的发展简史

X Window 系统最早由麻省理工学院（MIT）于1984年开发，最初用于 Unix System V 操作系统。开发目标是不与硬件强绑定，以保持广泛的适用性。X 的架构包括许多网络功能，允许跨网络和跨操作系统平台使用。

1987 年，X 版本更新至 X11，这一版取得显著进步，后来的改良均基于此版本。1994 年发布了 X11R6，后续版本为 X11R6.3 及其演化。1992 年，XFree86 计划启动，持续维护 X11R6，包括新硬件支持和新增功能。由于授权问题，XFree86 无法继续以 GPL 自由软件形式发布，Xorg 基金会接手，2004 年发布 X11R6.8，2005 年后发布 X11R7.x 版。如今 CentOS 7.x 使用的 X 版本即由 Xorg 基金会维护。

### 23.1.2 主要元件： X Server/X Client/Window Manager/Display Manager

X Window System 分为 X Server 和 X Client 两个主要元件。

- **X Server**：管理硬件（如键盘、鼠标、显示器、显卡、字体等），负责屏幕绘制和接受输入设备信息。
- **X Client**：应用程序，负责处理 X Server 的事件，并将绘图数据传回 X Server。

X Server 和 X Client 通过网络进行交互，允许跨平台和跨网络使用。

此外，X Window System 包括：

- **Window Manager**：特殊的 X Client，管理所有 X Client 软件，提供窗口控制、虚拟桌面、窗口大小调整等功能。常见的有 GNOME、KDE、XFCE、twm。
- **Display Manager**：提供图形界面登陆，常见的有 GNOME 的 GDM。

### 23.1.3 X Window 的启动流程

在命令行启动 X 窗口系统可以使用 `startx` 指令：

- **startx**：帮助找到 X Server 和 X Client 的配置文件，然后调用 `xinit` 启动 X 窗口系统。`startx` 的参数分为 X Client 和 X Server 两部分。

```
bash复制代码[root@study ~]# startx [X client 参数] -- [X server 参数]
# 例如：
[root@study ~]# startx -- -depth 16
```

- **xinit**：实际启动 X Server 和 X Client 的程序。其语法如下：

```
bash
复制代码
[root@study ~]# xinit [client option] -- [server or display option]
```

xinit 启动流程包括：

1. 启动 X Server，默认读取 `/etc/X11/xinit/xserverrc` 文件。
2. 启动 X Client，默认读取 `/etc/X11/xinit/xinitrc` 文件，载入 KDE 或 GNOME 等窗口管理员。

### 23.1.4 X 启动流程测试

通过以下步骤测试 X Server 和 X Client 的启动：

1. 启动第一个 X Server：

```
bash
复制代码
[dmtsai@study ~]$ X :1 &
```

1. 启动 X Client，例如 xterm：

```
bash复制代码[dmtsai@study ~]$ xterm -display :1 &
[dmtsai@study ~]$ xclock -display :1 &
[dmtsai@study ~]$ xeyes -display :1 &
```

1. 启动简单的窗口管理员 twm：

```
bash复制代码[root@study ~]# yum install xorg-x11-twm
[dmtsai@study ~]$ twm -display :1 &
```

1. 关闭所有 X 相关进程：

```
bash
复制代码
[dmtsai@study ~]# kill %6 %5 %4 %3 %2 %1
```

### 23.1.5 我是否需要启用 X Window System

是否启用 X Window System 取决于主机用途。如果主机作为服务器，X Window 可能不需要，因为主要服务的配置文件是文本格式。但如果主机用于桌面应用，X Window 则必不可少。

现代硬件性能通常足够运行 X Window，因此是否默认启用 X Window 主要根据具体需求和服务器用途决定。

## 23.2 X Server 配置文件解析与设置

启动 X Window System 成败的关键在于 X Server，因为它负责整个画面的描绘。如果 X Server 不能成功启动，即使 X Client 启动了也无法将图像显示出来。因此，下面针对 X Server 的配置文件做简单说明，以确保成功启动 X Window System。

### 23.2.1 解析 xorg.conf 设置

X Server 管理显卡、屏幕分辨率、鼠标按键对应等，尤其是显卡芯片的识别非常重要。此外，显示字体也是 X Server 管理的一部分。X Server 的配置文件通常放在 `/etc/X11` 目录下，相关模块则主要放置在 `/usr/lib64/xorg/modules` 下。比较重要的字体文件与芯片组分别放在：

- 提供的屏幕字体: `/usr/share/X11/fonts/`
- 显卡的芯片组: `/usr/lib64/xorg/modules/drivers/`

在 CentOS 下，通过统一的配置文件 `/etc/X11/xorg.conf` 来规范 X Server。

从 CentOS 6 开始，X Server 每次启动时会自动侦测系统上的显示芯片、屏幕类型等，并自动搭配最优的驱动程序。因此，`/etc/X11/xorg.conf` 不再是必须的。如果你不满意 X 系统的自动设置，可以自行创建 xorg.conf 文件。如果只想修改部分设置，可以在 `/etc/X11/xorg.conf.d/` 目录下创建 `.conf` 文件，添加需要的额外项目。

在修改这个文件之前，务必备份，以防出错导致 X Server 无法启动。文件内容分为多个段落，每个段落以 Section 开始，以 EndSection 结束，常见的 section name 有：

1. Module: 被载入到 X Server 的模块（某些功能的驱动程序）
2. InputDevice: 包括输入的键盘格式、鼠标格式等
3. Files: 设置字体所在的目录位置
4. Monitor: 监视器的格式，主要是设置水平、垂直的更新频率
5. Device: 显卡芯片组的相关设置
6. Screen: 屏幕上显示的分辨率与色彩深度的设置
7. ServerLayout: 设置此 X Server 要取用的项目

要生成新的 xorg.conf 文件，可以在 multi-user.target 环境下使用以下命令：

```
bash
复制代码
[root@study ~]# Xorg -configure
```

生成的配置文件位于 `/root/xorg.conf.new`，可以查看内容并根据需要修改：

```
bash
复制代码
[root@study ~]# vim xorg.conf.new
```

完成后，将其更名并移动到 `/etc/X11/xorg.conf.d/` 目录下：

```
bash
复制代码
[root@study ~]# mv /root/xorg.conf.new /etc/X11/xorg.conf.d/00-vbird.conf
```

### 23.2.2 字体管理

Xorg 使用的字体大部分放置于以下目录中：

- `/usr/share/X11/fonts/`
- `/usr/share/fonts/`

Xorg 默认载入的字体记录在 `/etc/X11/fontpath.d/` 目录中，使用链接文件模式进行链接。xorg.conf 文件中的 `Files` 项目指定了字体路径：

```
plaintext复制代码Section "Files"
    FontPath     "catalogue:/etc/X11/fontpath.d"
EndSection
```

安装中文字体并检查是否添加到 fontpath.d 中：

```
bash复制代码[root@study ~]# yum install cjkuni-ukai-fonts
[root@study ~]# ll /etc/X11/fontpath.d/
```

创建字体高速缓存数据并检查是否被使用：

```
bash复制代码[root@study ~]# fc-cache -v
[root@study ~]# fc-list | grep ukai
```

如果想要使用额外的字体，可以从其他地方获取并放置到系统字体目录中，然后更新字体缓存：

```
bash复制代码[root@study ~]# cp /root/font/*.ttf /usr/share/fonts/windows/
[root@study ~]# fc-cache -f -v
```

### 23.2.3 显示器参数微调

如果屏幕分辨率不理想，可能需要调整显示器的更新频率。更新频率指屏幕在一段时间内重新绘制画面的速度。可以使用 `gtf` 命令生成 Modeline，并将其添加到 xorg.conf 文件中。

先测试当前屏幕的分辨率与更新频率：

```
bash
复制代码
[root@study ~]# xrandr
```

使用 gtf 生成 Modeline：

```
bash
复制代码
[root@study ~]# gtf 1024 768 75 -x
```

将生成的 Modeline 添加到 xorg.conf.d 中的配置文件：

```
bash
复制代码
[root@study ~]# vim /etc/X11/xorg.conf.d/00-vbird.conf
```

完成后重新启动 X Server：

```
bash复制代码[root@study ~]# systemctl isolate multi-user.target
[root@study ~]# systemctl isolate graphical.target
```

通过以上步骤，可以调整 X Server 的配置，确保 X Window System 的成功启动和正常运行。

## 23.3 显卡驱动程序安装范例

虽然 X 窗口系统已顺利启动并调整到理想的分辨率，但在需要显卡提供的 3D 加速功能时，默认驱动程序可能不支持。这时需要安装官方网站提供的驱动程序。当前针对 x86 提供显卡的主要厂商是 Nvidia、AMD（ATI）和 Intel。以下是这些显卡驱动程序的安装介绍。

### 23.3.1 NVidia

虽然 Xorg 已提供 "nouveau" 模块，但它不支持许多额外功能。因此，需要安装 NVidia 官方提供的驱动程序。由于硬件驱动程序与核心相关，建议先参考[第二十一章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)与[第二十二章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)的介绍，并安装必要的编译工具和 `kernel-devel` 软件。

#### 查询硬件与下载驱动程序

使用 `lspci` 命令确认硬件：

```
bash复制代码[root@study ~]# lspci | grep -Ei '(vga|display)'
00:02.0 Display controller: Intel Corporation Xeon E3-1200 v3/4th Gen Core Processor Integrated Graphics Controller (rev 06)
01:00.0 VGA compatible controller: NVIDIA Corporation GF119 [GeForce GT 610] (rev a1)
```

建议到 NVidia 官网下载最新驱动程序：

- http://www.nvidia.com.tw
- http://www.nvidia.com.tw/object/unix_tw.html

选择与你系统相关的版本，如 Linux x86_64/AMD64/EM64T。下载后，假设文件名为 `NVIDIA-Linux-x86_64-352.41.run`，并放置在 `/root` 目录下。

#### 系统升级与取消 nouveau 模块的载入

先进行系统升级和安装编译工具：

```
bash复制代码[root@study ~]# yum update
[root@study ~]# yum groupinstall "Development Tools"
[root@study ~]# yum install kernel-devel kernel-headers
```

禁止 nouveau 模块：

```
bash复制代码[root@study ~]# vim /etc/modprobe.d/blacklist.conf
blacklist nouveau
options nouveau modeset=0

[root@study ~]# vim /etc/default/grub
GRUB_CMDLINE_LINUX="vconsole.keymap=us crashkernel=auto vconsole.font=latarcyrheb-sun16 rhgb quiet rd.driver.blacklist=nouveau nouveau.modeset=0"

[root@study ~]# grub2-mkconfig -o /boot/grub2/grub.cfg
[root@study ~]# reboot

[root@study ~]# lsmod | grep nouveau
# 确认没有 nouveau 模块载入
```

#### 安装驱动程序

完成上述步骤后，进行驱动程序安装：

```
bash复制代码[root@study ~]# systemctl isolate multi-user.target
[root@study ~]# sh NVIDIA-Linux-x86_64-352.41.run
```

按提示操作，安装驱动程序。

安装完成后，检查 `xorg.conf` 文件内容，确保 `Device` 的 `Driver` 设置为 `nvidia`。

```
bash复制代码[root@study ~]# nvidia-installer --update
# 检查驱动程序更新
```

### 23.3.2 AMD（ATI）

AMD 提供了开源显卡驱动程序，可以通过 ELrepo 网站安装。安装 ELrepo 的 yum 配置文件：

- http://elrepo.org

```
bash复制代码[root@study ~]# rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
[root@study ~]# rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
[root@study ~]# yum clean all
[root@study ~]# yum --enablerepo elrepo-testing search fglrx
[root@study ~]# yum --enablerepo elrepo-testing install fglrx-x11-drv
```

安装完毕后，使用 `aticonfig` 指令帮助设置 `xorg.conf` 文件：

```
bash
复制代码
[root@study ~]# aticonfig -v
```

重新启动 X 以启用新的驱动程序。

### 23.3.3 Intel

Intel 提供了开源显卡驱动程序，通常无需重新安装。若需要更新驱动程序，可参考 Intel 的显卡驱动程序网站：

- https://01.org/zh/linuxgraphics

检查系统中的 Intel 显卡驱动程序文件：

```
bash复制代码[root@study ~]# locate intel | grep xorg
/usr/lib64/xorg/modules/drivers/intel_drv.so
```

如果需要，修改 `xorg.conf` 配置文件：

```
bash复制代码[root@study ~]# vi /etc/X11/xorg.conf
Section "Device"
    Identifier  "Videocard0"
    Driver      "intel"
EndSection

Section "Module"
    Load  "glx"
    Load  "dri"
EndSection

Section "DRI"
    Mode 0666
EndSection
```

完成后重新启动 X 以启用新的驱动程序。

## 23.4 重点回顾

- Unix Like 操作系统上面的 GUI 使用的是最初由 MIT 所开发的 X window system，在 1987 释出 X11 版，并于 1994 更改为 X11R6 ，故此 GUI 接口也被称为 X 或 X11
- X window system 的 X server 最初由 XFree86 计划所开发，后来则由 Xorg 基金会所持续开发；
- X window system 主要分为 X server 与 X client ，其中 X Server 在管理硬件，而 X Client 则是应用程序。
- 在运行上，X Client 应用程序会将所想要呈现的画面告知 X Server ，最终由 X server 来将结果通过他所管理的硬件绘制出来！
- 每一支 X client 都不知道对方的存在，必须要通过特殊的 X client ，称为 Window Manager 的， 来管理各窗口的重叠、移动、最小化等工作。
- 若有需要登陆图形接口，有时会有 Display Manager 来管理这方面的动作
- startx 可以侦测 X server / X client 的启动脚本，并调用 xinit 来分别执行；
- X 可以启动多个，各个 X 显示的位置使用 -display 来处理，显示位置为 :0, :1...
- Xorg 是一个 X server ，配置文件位于 /etc/X11/xorg.conf ，里面含有 Module, Files, Monitor, Device 等设置阶段。目前较新的设置中， 会将额外的设置放置于 /etc/X11/xorg.conf.d/*.conf