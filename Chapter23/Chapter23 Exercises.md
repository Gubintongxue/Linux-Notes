## 23.5 本章习题

（ 要看答案请将鼠标移动到“答：”下面的空白处，按下左键圈选空白处即可察看 ）

- 在 X 设置没问题的情况下，你在 Linux 主机如何取得窗口接口？

  如果是在 multi-user.target 模式下，可以使用 startx 进入，至于 graphical.target ，则直接进入 tty1 即可使用 display manager 登陆 X Window 系统。

- 利用 startx 可以在 multi-user.target 的环境下进入 X Window 系统。请问 startx 的主要功能？

  整个 X 窗口系统的重点在启动 X server 并载入 X client ，而执行 X server/X client 调用的任务为 xinit ，startx 只是一个较为友好的脚本程序，可以搜寻系统上面的 X server / X client 设置值， 以提供 xinit 来执行而已。

- 如何知道你系统当中 X 系统的版本与计划？

  最简单可以利用 root 的身份下达 X -version 或 Xorg -version 即可知道！

- 要了解为何 X 系统可以允许不同硬件、主机、操作系统之间的沟通，需要知道 X server / X client 的相关知识。 请问 X Server / X client / Window manager 的主要用途功能？

  X Server 主要负责屏幕的绘制，以及周边输入设备如鼠标、键盘等数据的收集，并回报给 X Client ； X Client 主要负责数据的运算，收到来自 X Server 的数据后，加以运算得到图形的数据，并回传给 X Server， 让 X server 自行绘制图形。至于 Window manager 是一个比较特殊的 X Client ，他可以管理更多控制元素， 最重要的地方还是在于窗口的大小、重叠、移动等等的功能。

- 如何重新启动 X
  - 最简单在 X Window System 下，直接按下 [alt]+[ctrl]+[backspace<--] 即可
  - 也可以 systemctl isolate multi-usertarget 再 systemctl isolate graphical.target
  - 也可以关闭 X 后，再 startx 启动等等。
  
- 试说明 ~/.xinitrc 这个文件的用途？

  当我们要启动 X 时，必须要启动 X Client 软件端。这个 ~/.xinitrc 即是在客制化自己的 X Client ， 你可以在这个文件内输入你自己的 X Client 。若无此文件，则默认以 /etc/X11/xinit/xinitrc 替代。

- 我在 CentOS 的系统中，默认使用 GNOME 登陆 X 。但我想要改以 KDE 登陆，该怎么办？
  - 首先你必须要已经安装 KDE 环境 （参考前一章的 yum grouplist 功能），
  - 然后可以借由修改 /etc/sysconfig/desktop 内的设置值即可。
  - 但如果你不是 root 无法修订该文件时，亦可以在自己的主文件夹参考 /etc/X11/xinit/xinitrc 的内容自行制作 ~/.xinitrc 文件来修改！
  
- X Server 的 port 默认开放在？

  目前默认并不会启动 TCP 端口。不过如果经过设置，则 X port 默认开放在 port 6000 ，而且称此一显示为 :0

- Linux 主机是否可以有两个以上的 X是的！

  可以！第一个 X 通常在 tty1 ，第二个在 tty2 以后，依序类推。 第几个是以启动的顺序来定义，并非 :0 , :1 的意思～

- X Server 的配置文件是 xorg.conf，在该文件中， Section Files 干嘛用的？

  相当重要！是设置显示字体用的。而字体一般放置目录在 /usr/share/X11/fonts/ 及 /usr/share/fonts/ 当中。

- 我发现我的 X 系统键盘所输入的字母老是打不出我所需要的单字，可能原因?该如何修订?

  应该是键盘符号对应表跑掉了。可以修改 xorg.conf 文件内， 关于 Keyboard 的 Option XkbLayout 项目，将他改为 us 即可！

- 当我的系统内有安装 GNOME 及 KDE 两个 X Widnow Manager ，我原本是以 KDE 为默认的 WM， 若想改为 GNOME 时，应该如何修改？

  修改 /etc/sysconfig/desktop 内部，成为 GNOME 即可！