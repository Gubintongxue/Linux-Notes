## 10.8 本章习题

（ 要看答案请将鼠标移动到“答：”下面的空白处，按下左键圈选空白处即可察看 ）

- 情境仿真题一：由于 ~/.bash_history 仅能记录指令，我想要在每次登出时都记录时间，并将后续的指令 50 笔记录下来， 可以如何处理？

  - 目标：了解 history ，并通过数据流重导向的方式记录历史命令；
  - 前提：需要了解本章的数据流重导向，以及了解 bash 的各个环境配置文件信息。 其实处理的方式非常简单，我们可以了解 date 可以输出时间，而利用 ~/.myhistory 来记录所有历史记录， 而目前最新的 50 笔历史记录可以使用 history 50 来显示，故可以修改 ~/.bash_logout 成为下面的模样：

  ```
  [dmtsai@study ~]$ vim ~/.bash_logout
  date &gt;&gt; ~/.myhistory
  history 50 &gt;&gt; ~/.myhistory
  clear
  ```

------

简答题部分：

- 在 Linux 上可以找到哪些 shell（举出三个） ？那个文件记录可用的 shell ？而 Linux 默认的 shell 是？1） /bin/bash, /bin/tcsh, /bin/csh 2） /etc/shells 3） bash ，亦即是 /bin/bash。
- 你输入一串指令之后，发现前面写的一长串数据是错的，你想要删除光标所在处到最前面的指令串内容，应该如何处理？按下 [crtl]+u 组合键即可！
- 在 shell 环境下，有个提示字符 （prompt），他可以修改吗？要改什么？默认的提示字符内容是？可以修改的，改 PS1 这个变量，这个 PS1 变量的默认内容为：“[\u@\h \W]$”
- 如何显示 HOME 这个环境变量？echo $HOME
- 如何得知目前的所有变量与环境变量的设置值？环境变量用 env 或 export 而所有变量用 set 即可显示
- 我是否可以设置一个变量名称为 3myhome ？不行！变量不能以数字做为开头，参考变量设置规则的内容
- 在这样的练习中“A=B”且“B=C”，若我下达“unset $A”，则取消的变量是 A 还是 B？被取消的是 B 喔，因为 unset $A 相当于 unset B 所以取消的是 B ，A 会继续存在！
- 如何取消变量与命令别名的内容？使用 unset 及 unalias 即可
- 如何设置一个变量名称为 name 内容为 It's my name ？name=It\'s\ my\ name 或 name="It's my name"
- bash 环境配置文件主要分为哪两种类型的读取？分别读取哪些重要文件？（1）login shell：主要读取 /etc/profile 及 ~/.bash_profile （2）non-logni shell：主要读取 ~/.bashrc 而已。
- CentOS 7.x 的 man page 的路径设置文件？/etc/man_db.conf
- 试说明 ', ", 与 `这些符号在变量定义中的用途？参考变量规则那一章节，其中， " 可以具有变量的内容属性，' 则仅有一般字符，至于` 之内则是可先被执行的指令。
- 跳脱符号 \ 有什么用途？可以用来跳脱特殊字符，例如 Enter, $ 等等，使成为一般字符！
- 连续命令中， ;, &&, || 有何不同？分号可以让两个 command 连续运行，不考虑 command1 的输出状态， && 则前一个指令必需要没有错误讯息，亦即回传值需为 0 则 command2 才会被执行， || 则与 && 相反！
- 如何将 last 的结果中，独立出帐号，并且印出曾经登陆过的帐号？ last | cut -d ' ' -f1 | sort | uniq
- 请问 foo1 && foo2 | foo3 > foo4 ，这个指令串当中， foo1/foo2/foo3/foo4 是指令还是文件？ 整串指令的意义为？foo1, foo2 与 foo3 都是指令， foo4 是设备或文件。整串指令意义为： （1）当 foo1 执行结果有错误时，则该指令串结束； （2）若 foo1 执行结果没有错误时，则执行 foo2 | foo3 > foo4 ；其中： （2-1）foo2 将 stdout 输出的结果传给 foo3 处理； （2-2）foo3 将来自 foo2 的 stdout 当成 stdin ，处理完后将数据流重新导向 foo4 这个设备/文件
- 如何秀出在 /bin 下面任何以 a 为开头的文件文件名的详细数据？ls -ld /bin/a*
- 如何秀出 /bin 下面，文件名为四个字符的文件？ls -ld /bin/????
- 如何秀出 /bin 下面，文件名开头不是 a-d 的文件？ls -ld /bin/[a-d](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/94.html#fn_a-d)*
- 我想要让终端机接口的登陆提示字符修改成我自己喜好的模样，应该要改哪里？（filename）/etc/issue
- 承上题，如果我是想要让使用者登陆后，才显示欢迎讯息，又应该要改哪里？/etc/motd