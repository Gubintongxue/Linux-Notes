## 19.6 本章习题

（ 要看答案请将鼠标移动到“答：”下面的空白处，按下左键圈选空白处即可察看 ）

- 情境仿真题一：利用救援光盘来处理系统的错误导致无法开机的问题。
  - 目标：了解救援光盘的功能；
  - 前提：了解 grub 的原理，并且知道如何使用 chroot 功能；
  - 需求：打字可以再加快一点啊！ ^_^ 这个部分鸟哥就不捉图了，请大家自行处理啰～假设你的系统出问题而无法顺利开机，此时拿出原版光盘，然后重新以光盘来启动你的系统。 然后你应该要这样作的：
  - 利用光盘开机时，看到开机项目后，请选择“Troubleshooting”项目 --> “Rescue a CentOS system”项目，按下 Enter 就开始开机程序；
  - 然后就进入救援光盘模式的文件系统搜寻了！这个救援光盘会去找出目前你的主机里面与 CentOS 7.x 相关的操作系统， 并将该操作系统汇整成为一个 chroot 的环境等待你的处置！但是他会有三个模式可以选择，分别是“continue”继续成为可读写挂载； “Read-Only”将侦测到的操作系统变成只读挂载；“Skip”略过这次的救援动作。在这里我们选择“ Continue ”吧！
  - 如果你有安装多个 CentOS 7.x 的操作系统 （多重操作系统的实作），那就会出现菜单让你选择想要处理的根目录是哪个！ 选择完毕就请按 Enter 吧！
  - 然后系统会将侦测到的信息通知你！一般来说，可能会在屏幕上显示类似这样的讯息：“ chroot /mnt/sysimage”此时请按下 OK 吧！
  - 按下 OK 后，系统会丢给你一个 shell 使用，先用 df 看一下挂载情况是否正确？ 若不正确请手动挂载其他未被挂载的 partition 。等到一切搞定后，利用 chroot /mnt/sysimage 来转成你原本的操作系统环境吧！等到你将一切出问题的地方都搞定，请 reboot 系统，且取出光盘，用硬盘开机吧！

------

简答题部分：

- 因为 root 密码忘记，我使用 rd.break 的核心参数重新开机，并且修改完 root 密码，重新开机后可以顺利开机完毕， 但是我使用所有的帐号却都无法登陆系统！为何会如此？可能原因为何？最可能的原因是 /.autorelabel 没有创建，且你为 SELinux Enforcing 的模式之故。如果是这样， 那你必须要重新进入 rd.break ，然后重新创建 /.autorelabel 即可。若不想要于开机过程等太久， 可以将 /etc/selinux/config 内的 SELinux 类型设置为 permissive 再以 19.4.1 的方法去 restorecon 回复 /etc 下面的文件 SELinux 类型即可。
- 万一不幸，我的一些模块没有办法让 Linux 的核心捉到，但是偏偏这个核心明明就有支持该模块， 我要让该模块在开机的时候就被载入，那么应该写入那个文件？应该写入 /etc/modprobe.d/*.conf 这个文件，他是模块载入相关的地方呢！当然，也可以写入 /etc/sysconfig/modules/* 里面。
- 如何在 grub2 开机过程当中，指定以“ multi-user.target ”来开机？在开机进入 boot loader 之后，利用 grub shell 的功能，亦即输入“ e ”进入编辑模式， 然后在 linux16 后面增加： linux16 .... systemd.unit=multi-user.target 就能够进入纯文本模式啰！
- 如果你不小心先安装 Linux 再安装 Windows 导致 boot loader 无法找到 Linux 的开机菜单，该如何挽救？ 方法有很多，例如： （1）借助第三方软件，安装类似 spfdisk 的软件在 MBR 里面，因为他同时认识 Linux 与 Windows ，所以就可以用他来进入 Linux 啦！ （2）或者使用类似 KNOPPIX 的 Live CD 以光盘开机进入 Linux 之后，再以 chroot 软件切换根目录 （/），然后重新安装 grub 等 boot loader ，同样也可以重新让两个操作系统存在啦！ 总之，只要你知道 MBR / Super block / boot loader 之间的相关性，怎么切换都可能啊！ ^_^