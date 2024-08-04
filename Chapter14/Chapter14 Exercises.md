## 14.5 本章习题

（ 要看答案请将鼠标移动到“答：”下面的空白处，按下左键圈选空白处即可察看 ）

- 情境仿真题一：由于 LVM 可以弹性调整 filesystem 的大小，但是缺点是可能没有加速与硬件备份（与快照不同）的功能。 而磁盘阵列则具有性能与备份的功能，但是无法提供类似 LVM 的优点。在此情境中，我们想利用“在 RAID 上面创建 LVM”的功能，以达到两者兼顾的能力。

  - 目标：测试在 RAID 磁盘上面架构 LVM 系统；

  - 需求：需要具有磁盘管理的能力，包括 RAID 与 LVM；

  - 前提：会用到本章创建出来的 /dev/vda5, /dev/vda6, /dev/vda7 三个分区！ 那要如何处理呢？如下的流程一个步骤一个步骤的实施看看吧：

  - 重新处理系统，我们在这个练习当中，需要 /dev/vda5, /dev/vda6, /dev/vda7 创建成一个 RAID5 的 /dev/md0 磁盘！详细的作法这里就不谈了！ 你得要使用 gdisk 来处理成为如下的模样：

    ```
    [root@study ~]# gdisk -l /dev/vda
    Number  Start （sector）    End （sector）  Size       Code  Name
       1            2048            6143   2.0 MiB     EF02
       2            6144         2103295   1024.0 MiB  0700
       3         2103296        65026047   30.0 GiB    8E00
       4        65026048        67123199   1024.0 MiB  8300  Linux filesystem
     5        67123200        69220351   1024.0 MiB  FD00  Linux RAID
       6        69220352        71317503   1024.0 MiB  FD00  Linux RAID
       7        71317504        73414655   1024.0 MiB  FD00  Linux RAID
    ```

  - 开始使用 mdadm 来创建一个简单的 RAID5 阵列！简易的流程如下：

    ```
    [root@study ~]# mdadm --create /dev/md0 --auto=yes  --level=5  \
    &gt; --raid-devices=3 /dev/vda{5,6,7}
    [root@study ~]# mdadm --detail /dev/md0 &#124; grep -i uuid
               UUID : efc7add0:d12ee9ca:e5cb0baa:fbdae4e6
    [root@study ~]# vim /etc/mdadm.conf
    ARRAY /dev/md0 UUID=efc7add0:d12ee9ca:e5cb0baa:fbdae4e6
    ```

    若无出现任何错误讯息，此时你已经具有 /dev/md0 这个磁盘阵列设备了！接下来让我们处理 LVM 吧！

  - 开始处理 LVM ，现在我们假设所有的参数都使用默认值，包括 PE ，然后 VG 名为 raidvg ，LV 名为 raidlv ，下面为基本的流程：

    ```
    [root@study ~]# pvcreate /dev/md0                  &lt;==创建 PV
    [root@study ~]# vgcreate raidvg /dev/md0           &lt;==创建 VG
    [root@study ~]# lvcreate -L 1.5G -n raidlv raidvg  &lt;==创建 LM
    [root@study ~]# lvscan
      ACTIVE            '/dev/raidvg/raidlv' [1.50 GiB] inherit
    ```

    这样就搞定了 LVM 了！而且这个 LVM 是架构在 /dev/md0 上面的喔！然后就是文件系统的创建与挂载了！

  - 尝试创建成为 XFS 文件系统，且挂载到 /srv/raidlvm 目录下：

    ```
    [root@study ~]# mkfs.xfs /dev/raidvg/raidlv
    [root@study ~]# blkid /dev/raidvg/raidlv
    /dev/raidvg/raidlv: UUID="4f6a587d-3257-4049-afca-7da1d405117d" TYPE="xfs"
    [root@study ~]# vim /etc/fstab
    UUID="4f6a587d-3257-4049-afca-7da1d405117d" /srv/raidlvm xfs    defaults 0 0
    
    [root@study ~]# mkdir /srv/raidlvm
    [root@study ~]# mount -a
    [root@study ~]# df -Th /srv/raidlvm
    Filesystem                Type  Size  Used Avail Use% Mounted on
    /dev/mapper/raidvg-raidlv xfs   1.5G   33M  1.5G   3% /srv/raidlvm
    ```

  - 上述就是 LVM 架构在 RAID 上面的技巧，之后的动作都能够使用本章的其他管理方式来管理， 包括 RAID 热拔插机制、LVM 放大缩小机制等等。

------

简答题部分：

- 在前一章的[第一个大量新增帐号范例](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#manual_account_1)中， 如果我想要让每个用户均具有 soft/hard 各为 40MB/50MB 的容量时，应该如何修改这个 script ？你得先要依据本章的作法，先将 /home 制作好 quota 的环境然后， 你可以在 do...done 内的最后一行，新增一行内容为： xfs_quota -x -c "limit -u bsoft=40M bhard=50M ${username}" /home 这样就可以在制作用户时，指定更新密码且给予 quota 的限制！
- 如果我想要让 RAID 具有保护数据的功能，防止因为硬件损毁而导致数据的遗失，那我应该要选择的 RAID 等级可能有哪些？ （请以本章谈到的等级来思考即可）具有备份数据的有： RAID-1, RAID-5, RAID-6
- 在默认的 LVM 设置中，请问 LVM 能否具有“备份”的功能？是有的，就是那个快照 （snopshot） 的功能，此功能即可进行数据的备份！
- 如果你的计算机主机有提供 RAID 0 的功能，你将你的三颗硬盘全部在 BIOS 阶段使用 RAID 芯片整合成为一颗大磁盘， 则此磁盘在 Linux 系统当中的文件名为何？由于硬件磁盘阵列是在 BIOS 阶段完成的，因此 Linux 系统会捉到一个完整的大的 RAID 磁盘，此磁盘的文件名就会是“ /dev/sda ”！ 但如果是 Intel 的芯片组，则还是可能会成为 /dev/md127 等相关的文件名！