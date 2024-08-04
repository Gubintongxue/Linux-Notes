## 21.8 本章习题

实作题部分：

- 请前往企鹅游戏网站 http://xpenguins.seul.org/ 下载 xpenguins-2.2.tar.gz 源代码文件，并安装该软件。安装完毕之后，请在 GNOME 图形接口执行 xpenguins ， 看看有没有出现如同官网上面出现的小企鹅？（你有可能需要安装 yum install libX*-devel 才行喔）

------

情境仿真题部分：

- 请依照下面的方式来创建你的系统的重要文件指纹码，并每日比对此重要工作。

  1. 将 /etc/{passwd,shadow,group} 以及系统上面所有的 SUID/SGID 文件创建文件列表，该列表文件名为“ important.file ”；

     ```
     [root@study ~]# ls /etc/{passwd,shadow,group} &gt; important.file
     [root@study ~]# find /usr/sbin /usr/bin -perm /6000 &gt;&gt; important.file
     ```

  2. 通过这个文件名列表，以名为 md5.checkfile.sh 的文件名去创建指纹码，并将该指纹码文件“ finger1.file ”设置成为不可修改的属性；

     ```
     [root@study ~]# vim md5.checkfile.sh
     #!/bin/bash
     for filename in $（cat important.file）
     do
             md5sum $filename &gt;&gt; finger1.file
     done
     
     [root@study ~]# sh md5.checkfile.sh
     [root@study ~]# chattr +i finger1.file
     ```

  3. 通过相同的机制去创建后续的分析数据为 finger_new.file ，并将两者进行比对，若有问题则提供 email 给 root 查阅：

     ```
     [root@study ~]# vim md5.checkfile.sh
     #!/bin/bash
     if [ "$1" == "new" ]; then
         for filename in $（cat important.file）
         do
             md5sum $filename &gt;&gt; finger1.file
         done
         echo "New file finger1.file is created."
         exit 0
     fi
     if [ ! -f finger1.file ]; then
         echo "file: finger1.file NOT exist."
         exit 1
     fi
     
     [ -f finger_new.file ] && rm finger_new.file
     for filename in $（cat important.file）
     do
         md5sum $filename &gt;&gt; finger_new.file
     done
     
     testing=$（diff finger1.file finger_new.file）
     if [ "$testing" != "" ]; then
         diff finger1.file finger_new.file &#124; mail -s 'finger trouble..' root
     fi
     
     [root@study ~]# vim /etc/crontab
     30 2 * * * root cd /root; sh md5.checkfile.sh
     ```

     如此一来，每天系统会主动的去分析你认为重要的文件之指纹数据，然后再加以分析，看看有没有被更动过。 不过，如果该变动是正常的，例如 CentOS 自动的升级时，那么你就得要删除 finger1.file ， 再重新创建一个新的指纹数据库才行！否则你会每天收到有问题信件的回报喔！