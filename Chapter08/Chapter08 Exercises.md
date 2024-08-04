## 8.8 本章习题

（要看答案请将鼠标移动到“答：”下面的空白处，按下左键圈选空白处即可察看）

- 情境仿真题一：请将本章练习过程中产生的不必要的文件删除，以保持系统容量不要被恶搞！
  - rm /home/CentOS-7-x86_64-Minimal-1503-01.iso
  - rm -rf /srv/newcd/
  - rm /custom.iso
  - rm -rf /tmp/vda2.img /tmp/boot.cpio /tmp/boot /tmp/boot2 /tmp/boot3
  - rm -rf /tmp/services */tmp/system.*
  - rm -rf /root/etc* /root/system.tar.bz2 /root/boot
- 情境仿真题二：你想要逐时备份 /home 这个目录内的数据，又担心每次备份的信息太多， 因此想要使用 xfsdump 的方式来逐一备份数据到 /backups 这个目录下。该如何处理？
  - 目标：了解到 xfsdump 以及各个不同 level 的作用；
  - 前提：被备份的数据为单一 partition ，亦即本例中的 /home 实际处理的方法其实还挺简单的！我们可以这样做看看：
  - 先替该目录制作一些数据，亦即复制一些东西过去吧！ mkdir /home/chapter8; cp -a /etc /boot /home/chapter8
  - 开始进行 xfsdump ，记得，一开始是使用 level 0 的完整备份喔！ mkdir /backups xfsdump -l 0 -L home_all -M home_all -f /backups/home.dump /home
  - 尝试将 /home 这个文件系统加大，将 /var/log/ 的数据复制进去吧！ cp -a /var/log/ /home/chapter8 此时原本的 /home 已经被改变了！继续进行备份看看！
  - 将 /home 以 level 1 来进行备份： xfsdump -l 1 -L home_1 -M home_1 -f /backups/home.dump.1 /home ls -l /backups 你应该就会看到两个文件，其中第二个文件 （home.dump.1） 会小的多！这样就搞定啰备份数据！
- 情境仿真三：假设过了一段时间后，你的 /home 变的怪怪的，你想要将该 filesystem 以刚刚的备份数据还原， 此时该如何处理呢？你可以这样做的：
  1. 由于 /home 这个 partition 是用户只要有登陆就会使用，因此你应该无法卸载这个东西！因此，你必须要登出所有一般用户， 然后在 tty2 直接以 root 登陆系统，不要使用一般帐号来登陆后 su 转成 root ！这样才有办法卸载 /home 喔！
  2. 先将 /home 卸载，并且将该 partition 重新格式化！ df -h /home /dev/mapper/centos-home 5.0G 245M 4.8G 5% /home umount /home mkfs.xfs -f /dev/mapper/centos-home
  3. 重新挂载原本的 partition ，此时该目录内容应该是空的！ mount -a 你可以自行使用 df 以及 ls -l /home 查阅一下该目录的内容，是空的啦！
  4. 将完整备份的 level 0 的文件 /backups/home.dump 还原回来： cd /home xfsrestore -f /backups/home.dump . 此时该目录的内容为第一次备份的状态！还需要进行后续的处理才行！
  5. 将后续的 level 1 的备份也还原回来： xfsrestore -f /backups/home.dump.1 . 此时才是恢复到最后一次备份的阶段！如果还有 level 2, level 3 时，就得要一个一个的依序还原才行！
  6. 最后删除本章练习的复制档 rm -rf /home/chapter8