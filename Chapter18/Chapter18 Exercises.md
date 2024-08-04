## 18.7 本章习题

（ 要看答案请将鼠标移动到“答：”下面的空白处，按下左键圈选空白处即可察看 ） 实作题：

- 请在你的 CentOS 7.x 上面，依照鸟哥提供的 logfile.sh 去安装，并将结果取出分析看看。

------

简答题部分：

- 如果你想要将 auth 这个服务的结果中，只要讯息等级高于 warn 就给予发送 email 到 root 的信箱，该如何处理？利用 vim 去编辑 /etc/rsyslog.conf 文件，内容为 auth.warn root
- 启动系统登录信息时，需要启动哪两个 daemon 呢？systemd-journald.service, rsyslog.service
- rsyslogd 以及 logrotate 个别通过什么机制来执行？rsyslogd 为 stand alone daemon 的机制； logrotate 则是通过 crontab 来执行的！只是个指令而已。