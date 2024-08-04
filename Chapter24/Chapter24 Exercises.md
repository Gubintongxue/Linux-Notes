## 24.7 本章习题

（ 要看答案请将鼠标移动到“答：”下面的空白处，按下左键圈选空白处即可察看）

- 简单说明核心编译的步骤为何？
  1. 先下载核心源代码，可以从 [http://www.kernel.org](http://www.kernel.org/) 或者是 distributions 的 SRPM 来着手；
  2. 以下以 Tarball 来处理，解开源代码到 /usr/src/kernels 目录下；
  3. 先进行旧数据删除的动作：“make mrproper”；
  4. 开始挑选核心功能，可以利用“make menuconfig”、“make oldconfig”、“make gconfig”等等；
  5. 清除过去的中间暂存盘数据：“make clean”
  6. 开始核心文件与核心模块的编译：“make bzImage”、“make modules”
  7. 开始核心模块的安装：“make modules_install”
  8. 开始核心文件的安装，可以使用的方式有：“make install”或者是通过手动的方式复制核心文件到 /boot/ 当中；
  9. 创建 initramfs 文件；
  10. 使用 grub2-mkconfig 修改 /boot/grub2/grub.cfg 文件；
- 如果你利用新编译的核心来操作系统，发现系统并不稳定，你想要移除这个自行编译的核心该如何处理？
  1. 重新开机，并使用旧的稳定的核心开机！
  2. 此时才可以将新版核心模块删除： rm -rf /lib/modules/3.10.89vbird
  3. 删除掉 /boot 里面的新核心： rm /boot/vmlinuz-3.10.89vbird /boot/initramfs-3.10.89vbird.img ...
  4. 重建 grub.cfg： grub2-mkconfig -o /boot/grub2/grub.cfg