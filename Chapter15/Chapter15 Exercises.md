## 15.6 本章习题

（ 要看答案请将鼠标移动到“答：”下面的空白处，按下左键圈选空白处即可察看 ） 简答题：

- 今天假设我有一个指令程序，名称为： ping.sh 这个文件名！我想要让系统每三分钟执行这个文件一次， 但是偏偏这个文件会有很多的讯息显示出来，所以我的 root 帐号每天都会收到差不多四百多封的信件，光是收信就差不多快要疯掉了！ 那么请问应该怎么设置比较好呢？这个涉及数据流重导向的问题，我们可以将他导入文件或者直接丢弃！如果该讯息不重要的话， 那么就予以丢弃，如果讯息很重要的话，才将他保留下来！假设今天这个命令不重要， 所以将他丢弃掉！因此，可以这样写：

  \> */3* * root /usr/local/ping.sh > /dev/null 2>&1

- 您预计要在 2016 年的 2 月 14 日寄出一封给 kiki ，只有该年才寄出！该如何下达指令？at 1am 2016-02-14

- 下达 crontab -e 之后，如果输入这一行，代表什么意思？

  - 15 1-5 /usr/local/bin/tea_time.sh在每星期的 1~5 ，下午 3 点的每分钟，共进行 60 次 /usr/local/bin/tea_time.sh 这个文件。 要特别注意的是，每个星期 1~5 的 3 点都会进行 60 次ㄟ！很麻烦吧～是错误的写法啦～ 应该是要写成： 30 15 1-5 /usr/local/bin/tea_time.sh

- 我用 vi 编辑 /etc/crontab 这个文件，我编辑的那一行是这样的： 25 00 0 /usr/local/bin/backup.sh 这一行代表的意义是什么？这一行代表......没有任何意义！因为语法错误！您必须要了解，在 /etc/crontab 当中每一行都必须要有使用者才行！所以，应该要将原本那行改成： 25 00 0 root /usr/local/bin/backup.sh

- 请问，您的系统每天、每周、每个月各有进行什么工作？因为 CentOS 系统默认的例行性命令都放置在 /etc/cron.* 里面，所以，你可以自行去： /etc/cron.daily/, /etc/cron.week/, /etc/cron.monthly/ 这三个目录内看一看， 就知道啦！ ^_^

- 每个星期六凌晨三点去系统搜寻一下内有 SUID/SGID 的任何文件！并将结果输出到 /tmp/uidgid.filesvi /etc/crontab 0 3 6 root find / -perm /6000 > /tmp/uidgid.files

2002/05/30：第一次完成 2003/02/10：重新编排与加入 FAQ 2005/09/07：将旧的文章移动到 [此处](http://linux.vbird.org/linux_basic/0430cron/0430cron.php) 。 2005/09/07：呼呼！终于完成风格啰～同时加入一些习题练习。 2009/03/12：将旧的文件移动到[此处](http://linux.vbird.org/linux_basic/0430cron/0430cron-fc4.php)。 2009/03/14：加入 [batch](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#batch) 这个项目的说明！与 at 有关！ 2009/03/15：加入了 anacron 这玩意的简单说明！ 2009/09/11：稍微修订一下说明语气与链接数据。 2015/07/31：将旧的基于 CentOS5 的版本移动到[这里](http://linux.vbird.org/linux_basic/0430cron/0430cron-centos5.php)。