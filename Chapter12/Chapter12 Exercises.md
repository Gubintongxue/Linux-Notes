## 12.8 本章习题

（ 要看答案请将鼠标移动到“答：”下面的空白处，按下左键圈选空白处即可察看 ） 下面皆为实作题，请自行撰写出程序喔！

- 请创建一支 script ，当你执行该 script 的时候，该 script 可以显示： 1. 你目前的身份 （用 whoami ） 2. 你目前所在的目录 （用 pwd）

  ```
  #!/bin/bash
  echo -e "Your name is ==&gt; $（whoami）"
  echo -e "The current directory is ==&gt; $（pwd）"
  ```

- 请自行创建一支程序，该程序可以用来计算“你还有几天可以过生日”啊？

  ```
  #!/bin/bash
  read -p "Pleas input your birthday （MMDD, ex&gt; 0709）: " bir
  now=`date +%m%d`
  if [ "$bir" == "$now" ]; then
  echo "Happy Birthday to you!!!"
  elif [ "$bir" -gt "$now" ]; then
  year=`date +%Y`
  total_d=$（（$（（`date --date="$year$bir" +%s`-`date +%s`））/60/60/24））
  echo "Your birthday will be $total_d later"
  else
  year=$（（`date +%Y`+1））
  total_d=$（（$（（`date --date="$year$bir" +%s`-`date +%s`））/60/60/24））
  echo "Your birthday will be $total_d later"
  fi
  ```

- 让使用者输入一个数字，程序可以由 1+2+3... 一直累加到使用者输入的数字为止。

  ```
  #!/bin/bash
  read -p "Please input an integer number: " number
  i=0
  s=0
  while [ "$i" != "$number" ]
  do
  i=$（（$i+1））
  s=$（（$s+$i））
  done
  echo "the result of '1+2+3+...$number' is ==&gt; $s"
  ```

- 撰写一支程序，他的作用是: 1.） 先查看一下 /root/test/logical 这个名称是否存在； 2.） 若不存在，则创建一个文件，使用 touch 来创建，创建完成后离开； 3.） 如果存在的话，判断该名称是否为文件，若为文件则将之删除后创建一个目录，文件名为 logical ，之后离开； 4.） 如果存在的话，而且该名称为目录，则移除此目录！

  ```
  #!/bin/bash
  if [ ! -e logical ]; then
  touch logical
  echo "Just make a file logical"
  exit 1
  elif [ -e logical ] && [ -f logical ]; then
  rm logical
  mkdir logical
  echo "remove file ==&gt; logical"
  echo "and make directory logical"
  exit 1
  elif [ -e logical ] && [ -d logical ]; then
  rm -rf logical
  echo "remove directory ==&gt; logical"
  exit 1
  else
  echo "Does here have anything?"
  fi
  ```

- 我们知道 /etc/passwd 里面以 : 来分隔，第一栏为帐号名称。请写一只程序，可以将 /etc/passwd 的第一栏取出，而且每一栏都以一行字串“The 1 account is "root" ”来显示，那个 1 表示行数。

  ```
  #!/bin/bash
  accounts=`cat /etc/passwd | cut -d':' -f1`
  for account in $accounts
  do
  declare -i i=$i+1
  echo "The $i account is \"$account\" "
  done
  ```

2002/06/27：第一次完成 2003/02/10：重新编排与加入 FAQ 2005/08/29：将旧的文章移动到 [这里](http://linux.vbird.org/linux_basic/0340bashshell-scripts/0340bashshell-scripts.php) 了。 2005/08/29：呼呼～加入了一些比较有趣的练习题～比第一版要难的多～大家多多玩一玩喔～ 2009/02/10：将旧的基于 FC4 版本的文章移动到[此处](http://linux.vbird.org/linux_basic/0340bashshell-scripts/0340bashshell-scripts-fc4.php) 2009/02/17：加入 [shift](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#shift) 的介绍 2009/02/18：加入了一些额外的练习，包括 [for 的 ping](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#for_ping) 处理！ 2015/07/17：全部的脚本通通小修改过，并且加上阵列、乱数与午餐大乱斗的脚本程序