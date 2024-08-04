## 7.8 本章习题 - 第一题一定要做

（ 要看答案请将鼠标移动到“答：”下面的空白处，按下左键圈选空白处即可察看 ）

- 情境仿真题一：复原本章的各例题练习，本章新增非常多 partition ，请将这些 partition 删除，恢复到原本刚安装好时的状态。

  - 目标：了解到删除分区需要注意的各项信息；

  - 前提：本章的各项范例练习你都必须要做过，才会拥有 /dev/vda4 ~ /dev/vda7 出现；

  - 需求：熟悉 gdisk, parated, umount, swapoff 等指令。 由于本章处理完毕后，将会有许多新增的 partition ，所以请删除掉这两个 partition 。删除的过程需要注意的是：

  - 需先以 free / swapon -s / mount 等指令查阅，要被处理的文件系统不可以被使用！ 如果有被使用，则你必须要使用 umount 卸载文件系统。如果是内存交换空间，则需使用 swapon -s 找出被使用的分区， 再以 swapoff 去卸载他！

    ```
    [root@study ~]# umount /data/ext4 /data/xfs /data/file /data/win
    [root@study ~]# swapoff /dev/vda6 /tmp/swap
    ```

  - 观察 /etc/fstab ，该文件新增的行全部删除或注解！

    ```
    [root@study ~]# nano /etc/fstab
    .....（前面省略）.....
    /dev/mapper/centos-swap swap                 swap    defaults        0 0  # 从这行之后全删除
    UUID="e0fa7252-b374-4a06-987a-3cb14f415488"  /data/xfs  xfs   defaults      0 0
    /srv/loopdev                                 /data/file xfs   defaults,loop 0 0
    UUID="6b17e4ab-9bf9-43d6-88a0-73ab47855f9d"  swap       swap  defaults      0 0
    /tmp/swap                                    swap       swap  defaults      0 0
    UUID="6032-BF38"                             /data/win  vfat  defaults      0 0
    ```

  - 使用“ gdisk /dev/vda ”删除，也可以使用“ parted /dev/vda rm 号码”删除喔！

    ```
    [root@study ~]# parted /dev/vda rm 7
    [root@study ~]# parted /dev/vda rm 6
    [root@study ~]# parted /dev/vda rm 5
    [root@study ~]# parted /dev/vda rm 4
    [root@study ~]# partprobe
    [root@study ~]# rm /tmp/swap /srv/loopdev
    ```

- 情境仿真题二：由于我的系统原本分区的不够好，我的用户希望能够独立一个 filesystem 附挂在 /srv/myproject 目录下。 那你该如何创建新的 filesystem ，并且让这个 filesystem 每次开机都能够自动的挂载到 /srv/myproject ， 且该目录是给 project 这个群组共享的，其他人不可具有任何权限。且该 filesystem 具有 1GB 的容量。

  - 目标：理解文件系统的创建、自动挂载文件系统与专案开发必须要的权限；
  - 前提：你需要进行过第六章的情境仿真才可以继续本章；
  - 需求：本章的所有概念必须要清楚！ 那就让我们开始来处理这个流程吧！
  - 首先，我们必须要使用 gdisk /dev/vda 来创建新的 partition。 然后按下“ n ”，按下“Enter”选择默认的分区号码，再按“Enter”选择默认的启始柱面， 按下“+1G”创建 1GB 的磁盘分区，再按下“Enter”选择默认的文件系统 ID。 可以多按一次“p ”看看是否正确，若无问题则按下“w”写入分区表；
  - 避免重新开机，因此使用“ partprobe ”强制核心更新分区表；
  - 创建完毕后，开始进行格式化的动作如下：“mkfs.xfs -f /dev/vda4”，这样就 OK 了！
  - 开始创建挂载点，利用：“ mkdir /srv/myproject ”来创建即可；
  - 编写自动挂载的配置文件：“ nano /etc/fstab ”，这个文件最下面新增一行，内容如下： /dev/vda4 /srv/myproject xfs defaults 0 0
  - 测试自动挂载：“ mount -a ”，然后使用“ df /srv/myproject ”观察看看有无挂载即可！
  - 设置最后的权限，使用：“ chgrp project /srv/myproject ”以及“ chmod 2770 /srv/myproject ”即可。

------

简答题部分：

- 我们常常说，开机的时候，“发现磁盘有问题”，请问，这个问题的产生是“filesystem 的损毁”，还是“磁盘的损毁”？ 特别需要注意的是，如果您某个 filesystem 里面，由于操作不当，可能会造成 Superblock 数据的损毁， 或者是 inode 的架构损毁，或者是 block area 的记录遗失等等，这些问题当中，其实您的“磁盘”还是好好的， 不过，在磁盘上面的“文件系统”则已经无法再利用！一般来说，我们的 Linux 很少会造成 filesystem 的损毁， 所以，发生问题时，很可能整个磁盘都损毁了。但是，如果您的主机常常不正常断电，那么， 很可能磁盘是没问题的，但是，文件系统则有损毁之虞。此时，重建文件系统 （reinstall） 即可！ 不需要换掉磁盘啦！ ^_^
- 当我有两个文件，分别是 file1 与 file2 ，这两个文件互为 hard link 的文件，请问， 若我将 file1 删除，然后再以类似 vi 的方式重新创建一个名为 file1 的文件， 则 file2 的内容是否会被更动？ 这是来自网友的疑问。当我删除 file1 之后， file2 则为一个正规文件，并不会与他人共同分享同一个 inode 与 block ，因此，当我重新创建一个文件名为 file1 时，他所利用的 inode 与 block 都是由我们的 filesystem 主动去搜寻 meta data ，找到空的 inode 与 block 来创建的， 与原本的 file1 并没有任何关连性喔！所以，新建的 file1 并不会影响 file2 呢！