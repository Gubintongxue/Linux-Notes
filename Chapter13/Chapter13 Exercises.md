## 13.9 本章习题

- 情境仿真题一：想将本服务器的帐号分开管理，分为单纯邮件使用，与可登陆系统帐号两种。其中若为纯邮件帐号时， 将该帐号加入 mail 为初始群组，且此帐号不可使用 bash 等 shell 登陆系统。若为可登陆帐号时， 将该帐号加入 youcan 这个次要群组。

  - 目标：了解 /sbin/nologin 的用途；

  - 前提：可自行观察使用者是否已经创建等问题；

  - 需求：需已了解 useradd, groupadd 等指令的用法； 解决方案如下：

  - 预先察看一下两个群组是否存在？

    ```
    [root@study ~]# grep mail /etc/group
    [root@study ~]# grep youcan /etc/group
    [root@study ~]# groupadd youcan
    ```

    可发现 youcan 尚未被创建，因此如上表所示，我们主动去创建这个群组啰。

  - 开始创建三个邮件帐号，此帐号名称为 pop1, pop2, pop3 ，且密码与帐号相同。可使用如下的程序来处理：

    ```
    [root@study ~]# vim popuser.sh
    #!/bin/bash
    for username in pop1 pop2 pop3
    do
        useradd -g mail -s /sbin/nologin -M $username
        echo $username &#124; passwd --stdin $username
    done
    [root@study ~]# sh popuser.sh
    ```

  - 开始创建一般帐号，只是这些一般帐号必须要能够登陆，并且需要使用次要群组的支持！所以：

    ```
    [root@study ~]# vim loginuser.sh
    #!/bin/bash
    for username in youlog1 youlog2 youlog3
    do
        useradd -G youcan -s /bin/bash -m $username
        echo $username &#124; passwd --stdin $username
    done
    [root@study ~]# sh loginuser.sh
    ```

  - 这样就将帐号分开管理了！非常简单吧！

------

简答题部分

- **root 的 UID 与 GID 是多少？**

  而基于这个理由，我要让 test 这个帐号具有 root 的权限，应该怎么作？root 的 UID 与 GID 均为 0 ，所以要让 test 变成 root 的权限，那么就将 /etc/passwd 里面， test 的 UID 与 GID 字段变成 0 即可！

- **假设我是一个系统管理员，我有一个用户最近不乖，所以我想暂时将他的帐号停掉， 让他近期无法进行任何动作，等到未来他乖一点之后，我再将他的帐号启用，请问：我可以怎么作比较好？？由于这个帐号是暂时失效的，所以不能使用 userdel 来删除，否则很麻烦！那么应该如何设置呢？再回去瞧一瞧 /etc/shadow 的架构，可以知道有这几个可使用的方法：**
  
  - 将 /etc/passwd 的 shell 字段写成 /sbin/nologin ，即可让该帐号暂时无法登陆主机；
  - 将 /etc/shadow 内的密码字段，增加一个 * 号在最前面，这样该帐号亦无法登陆！
  - 将 /etc/shadow 的第八个字段关于帐号取消日期的那个，设置小于目前日期的数字，那么他就无法登陆系统了！
  
- **我在使用 useradd 的时候，新增的帐号里面的 UID, GID 还有其他相关的密码控制，都是在哪几个文件里面设置的？**

  在 /etc/login.defs 还有 /etc/default/useradd 里面规定好的！

- **我希望我在设置每个帐号的时候（ 使用 useradd ），默认情况中，他们的主文件夹就含有一个名称为 www 的子目录，我应该怎么作比较好？**

  由于使用 useradd 的时候，会自动以 /etc/skel 做为默认的主文件夹，所以，我可以在 /etc/skel 里面新增加一个名称为 www 的目录即可！

- **简单说明系统帐号与一般使用者帐号的差别？**

  一般而言，为了让系统能够顺利以较小的权限运行，系统会有很多帐号， 例如 mail, bin, adm 等等。而为了确保这些帐号能够在系统上面具有独一无二的权限， 一般来说 Linux 都会保留一些 UID 给系统使用。在 CentOS 5.x 上面，小于 500 以下的帐号 （UID） 即是所谓的 System account。

- **简单说明，为何 CentOS 创建使用者时，他会主动的帮使用者创建一个群组，而不是使用 /etc/default/useradd 的设置？不同的 linux distributions 对于使用者 group 的创建机制并不相同。主要的机制分为：**
  
  - Public group schemes: 使用者将会直接给予一个系统指定的群组，一般来说即是 users ， 可以 SuSE Server 9 为代表；
  - Private group schemes: 系统会创建一个与帐号一样的群组名称！以 CentOS 7.x 为例！
  
- **如何创建一个使用者名称 alex, 他所属群组为 alexgroup, 预计使用 csh, 他的全名为 "Alex Tsai"， 且他还得要加入 users 群组当中！**

  groupadd alexgroup useradd -c "Alex Tsai" -g alexgroup -G users -m alex 务必先创建群组，才能够创建使用者喔！

- **由于种种因素，导致你的使用者主文件夹以后都需要被放置到 /account 这个目录下。 请问，我该如何作，**

  可以让使用 useradd 时，默认的主文件夹就指向 /account ？最简单的方法，编辑 /etc/default/useradd ，将里头的 HOME=/home 改成 HOME=/account 即可。

- **我想要让 dmtsai 这个使用者，加入 vbird1, vbird2, vbird3 这三个群组，且不影响 dmtsai 原本已经支持的次要群组时，该如何动作？**

  usermod -a -G vbird1,vbird2,vbird3 dmtsai