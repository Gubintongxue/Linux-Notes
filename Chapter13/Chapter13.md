# 第十三章、Linux 帐号管理与 ACL 权限设置

要登陆 Linux 系统一定要有帐号与密码才行，否则怎么登陆，您说是吧？不过， 不同的使用者应该要拥有不同的权限才行吧？我们还可以通过 user/group 的特殊权限设置， 来规范出不同的群组开发专案呢～在 Linux 的环境下，我们可以通过很多方式来限制使用者能够使用的系统资源， 包括 [第十章、bash](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 提到的 [ulimit](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#variable_ulimit) 限制、还有特殊权限限制，如 [umask](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#umask) 等等。 通过这些举动，我们可以规范出不同使用者的使用资源。另外，还记得系统管理员的帐号吗？对！ 就是 root 。请问一下，除了 root 之外，是否可以有其他的系统管理员帐号？ 为什么大家都要尽量避免使用数字体态的帐号？如何修改使用者相关的信息呢？这些我们都得要了解了解的！

## 13.1 Linux 的帐号与群组

管理员的工作中，相当重要的一环就是“管理帐号”。Linux 系统通过 UID 和 GID 来识别每一个用户和群组。了解如何管理好一个服务器主机的帐号对于系统管理员来说至关重要。

### 13.1.1 使用者识别码：UID 与 GID

尽管我们登录 Linux 主机时输入的是帐号名称，但实际上，Linux 主机识别的是与帐号对应的 UID（用户识别码）和 GID（群组识别码）。这些信息保存在 /etc/passwd 文件中。

**示例：**

1. 确认系统中有名为 dmtsai 的用户。

   ```
   id dmtsai
   ```
   
2. 修改 dmtsai 的 UID。

   ```
   vim /etc/passwd
   # 将 dmtsai 的 UID 从 1000 改为 2000
   ```

**注意：** 在正常运行的系统中，不建议随意修改 UID，因为可能导致系统权限问题。

#### 原文：

虽然我们登陆 Linux 主机的时候，输入的是我们的帐号，但是其实 Linux 主机并不会直接认识你的“帐号名称”的，他仅认识 ID 啊 （ID 就是一组号码啦）。 由于计算机仅认识 0 与 1，所以主机对于数字比较有概念的；至于帐号只是为了让人们容易记忆而已。 而你的 ID 与帐号的对应就在 /etc/passwd 当中哩。

**Tips** 如果你曾经在网络上下载过 [tarball](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#pack) 类型的文件， 那么应该不难发现，在解压缩之后的文件中，文件拥有者的字段竟然显示“不明的数字”？奇怪吧？这没什么好奇怪的，因为 Linux 说实在话，他真的只认识代表你身份的号码而已！

那么到底有几种 ID 呢？还记得我们在[第五章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)内有提到过， 每一个文件都具有“拥有人与拥有群组”的属性吗？没错啦～每个登陆的使用者至少都会取得两个 ID ，一个是使用者 ID （User ID ，简称 UID）、一个是群组 ID （Group ID ，简称 GID）。

那么文件如何判别他的拥有者与群组呢？其实就是利用 UID 与 GID 啦！每一个文件都会有所谓的拥有者 ID 与拥有群组 ID ，当我们有要显示文件属性的需求时，系统会依据 /etc/passwd 与 /etc/group 的内容， 找到 UID / GID 对应的帐号与群组名称再显示出来！我们可以作个小实验，你可以用 root 的身份 vim /etc/passwd ，然后将你的一般身份的使用者的 ID 随便改一个号码，然后再到你的一般身份的目录下看看原先该帐号拥有的文件，你会发现该文件的拥有人变成了 “数字了”呵呵！这样可以理解了吗？来看看下面的例子：

```
# 1\. 先察看一下，系统里面有没有一个名为 dmtsai 的用户？
[root@study ~]# id dmtsai
uid=1000（dmtsai） gid=1000（dmtsai） groups=1000（dmtsai）,10（wheel）  &lt;==确定有这个帐号喔！

[root@study ~]# ll -d /home/dmtsai
drwx------. 17 dmtsai dmtsai 4096 Jul 17 19:51 /home/dmtsai
# 瞧一瞧，使用者的字段正是 dmtsai 本身喔！

# 2\. 修改一下，将刚刚我们的 dmtsai 的 1000 UID 改为 2000 看看：
[root@study ~]# vim /etc/passwd
....（前面省略）....
dmtsai:x:2000:1000:dmtsai:/home/dmtsai:/bin/bash &lt;==修改一下特殊字体部分，由 1000 改过来
[root@study ~]# ll -d /home/dmtsai
drwx------. 17 1000 dmtsai 4096 Jul 17 19:51 /home/dmtsai
# 很害怕吧！怎么变成 1000 了？因为文件只会记录 UID 的数字而已！
# 因为我们乱改，所以导致 1000 找不到对应的帐号，因此显示数字！

# 3\. 记得将刚刚的 2000 改回来！
[root@study ~]# vim /etc/passwd
....（前面省略）....
dmtsai:x:1000:1000:dmtsai:/home/dmtsai:/bin/bash  &lt;==“务必一定要”改回来！
```

你一定要了解的是，上面的例子仅是在说明 UID 与帐号的对应性，在一部正常运行的 Linux 主机环境下，上面的动作不可随便进行， 这是因为系统上已经有很多的数据被创建存在了，随意修改系统上某些帐号的 UID 很可能会导致某些程序无法进行，这将导致系统无法顺利运行的结果， 因为权限的问题啊！所以，了解了之后，请赶快回到 /etc/passwd 里面，将数字改回来喔！

![鸟哥的图示](image/vbird_face.gif)

**Tips** 举例来说，如果上面的测试最后一个步骤没有将 2000 改回原本的 UID，那么当 dmtsai 下次登陆时将没有办法进入自己的主文件夹！ 因为他的 UID 已经改为 2000 ，但是他的主文件夹 （/home/dmtsai） 却记录的是 1000 ，由于权限是 700 ， 因此他将无法进入原本的主文件夹！是否非常严重啊？



### 13.1.2 使用者帐号

当用户登录 Linux 系统时，系统会进行以下步骤：

1. 在 /etc/passwd 中查找帐号，获取 UID、GID、主文件夹和 shell 设置。
2. 在 /etc/shadow 中核对密码。
3. 若核对通过，进入 Shell 控管阶段。

关键文件包括：

- **/etc/passwd：管理用户 UID/GID 和其他重要参数。**
- **/etc/shadow：管理密码相关数据。**

#### /etc/passwd 文件结构

每一行代表一个帐号，分为七个字段：

1. 帐号名称
2. 密码（实际保存在 /etc/shadow 中，这里通常为 "x"）
3. UID
4. GID
5. 使用者信息说明栏
6. 主文件夹
7. Shell

#### /etc/shadow 文件结构

分为九个字段：

1. 帐号名称
2. 密码（经过加密）
3. 最近更动密码的日期（自1970年1月1日起的天数）
4. 密码不可更动的天数
5. 密码需要重新变更的天数
6. 密码需要变更期限前的警告天数
7. 密码过期后的帐号宽限时间（密码失效日）
8. 帐号失效日期
9. 保留字段

**示例：**

假设 /etc/shadow 文件中 dmtsai 的记录如下：

```
dmtsai:$6$M4IphgNP2TmlXaSS$B418YFroYxxmm....:16559:5:60:7:5:16679:
```

这表示 dmtsai 的密码在 2015/05/04 修改过，密码需要 5 天后才能再次修改，60 天内必须更改密码，密码到期前 7 天会有警告，密码到期后有 5 天的宽限期，帐号将在 2015/09/01 失效。

#### **原文：**

Linux 系统上面的使用者如果需要登陆主机以取得 shell 的环境来工作时，他需要如何进行呢？ 首先，他必须要在计算机前面利用 tty1~tty6 的终端机提供的 login 接口，并输入帐号与密码后才能够登陆。 如果是通过网络的话，那至少使用者就得要学习 ssh 这个功能了 （服务器篇再来谈）。 那么你输入帐号密码后，系统帮你处理了什么呢？

1. 先找寻 /etc/passwd 里面是否有你输入的帐号？如果没有则跳出，如果有的话则将该帐号对应的 UID 与 GID （在 /etc/group 中） 读出来，另外，该帐号的主文件夹与 shell 设置也一并读出；
2. 再来则是核对密码表啦！这时 Linux 会进入 /etc/shadow 里面找出对应的帐号与 UID，然后核对一下你刚刚输入的密码与里头的密码是否相符？
3. 如果一切都 OK 的话，就进入 Shell 控管的阶段啰！

大致上的情况就像这样，所以当你要登陆你的 Linux 主机的时候，那个 /etc/passwd 与 /etc/shadow 就必须要让系统读取啦 （这也是很多攻击者会将特殊帐号写到 /etc/passwd 里头去的缘故），所以呢，如果你要备份 Linux 的系统的帐号的话，那么这两个文件就一定需要备份才行呦！

由上面的流程我们也知道，跟使用者帐号有关的有两个非常重要的文件，一个是管理使用者 UID/GID 重要参数的 /etc/passwd ，一个则是专门管理密码相关数据的 /etc/shadow 啰！那这两个文件的内容就非常值得进行研究啦！ 下面我们会简单的介绍这两个文件，详细的说明可以参考 man 5 passwd 及 man 5 shadow [[1\]](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/114.html#ps1)。

- /etc/passwd 文件结构

这个文件的构造是这样的：每一行都代表一个帐号，有几行就代表有几个帐号在你的系统中！ 不过需要特别留意的是，里头很多帐号本来就是系统正常运行所必须要的，我们可以简称他为系统帐号， 例如 bin, daemon, adm, nobody 等等，这些帐号请不要随意的杀掉他呢！这个文件的内容有点像这样：

![鸟哥的图示](image/vbird_face-17224112902504.gif)

**Tips** 鸟哥在接触 Linux 之前曾经碰过 Solaris 系统 （1999 年），当时鸟哥啥也不清楚！由于“听说”Linux 上面的帐号越复杂会导致系统越危险！所以鸟哥就将 /etc/passwd 上面的帐号全部删除到只剩下 root 与鸟哥自己用的一般帐号！结果你猜发生什么事？那就是....调用升阳的工程师来维护系统 @_@！糗到一个不行！大家不要学啊！

```
[root@study ~]# head -n 4 /etc/passwd
root:x:0:0:root:/root:/bin/bash  &lt;==等一下做为下面说明用
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
```

我们先来看一下每个 Linux 系统都会有的第一行，就是 root 这个系统管理员那一行好了， 你可以明显的看出来，每一行使用“:”分隔开，共有七个咚咚，分别是：

1. 帐号名称： 就是帐号啦！用来提供给对数字不太敏感的人类使用来登陆系统的！需要用来对应 UID 喔。例如 root 的 UID 对应就是 0 （第三字段）；

2. 密码： 早期 Unix 系统的密码就是放在这字段上！但是因为这个文件的特性是所有的程序都能够读取，这样一来很容易造成密码数据被窃取， 因此后来就将这个字段的密码数据给他改放到 [/etc/shadow](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#shadow_file) 中了。所以这里你会看到一个“ x ”，呵呵！

3. UID： 这个就是使用者识别码啰！通常 Linux 对于 UID 有几个限制需要说给您了解一下：

   ```
   | id 范围 | 该 ID 使用者特性 |
   | 0（系统管理员） | 当 UID 是 0 时，代表这个帐号是“系统管理员”！ 所以当你要让其他的帐号名称也具有 root 的权限时，将该帐号的 UID 改为 0 即可。 这也就是说，一部系统上面的系统管理员不见得只有 root 喔！ 不过，很不建议有多个帐号的 UID 是 0 啦～容易让系统管理员混乱！ |
   | 1~999（系统帐号） | 保留给系统使用的 ID，其实除了 0 之外，其他的 UID 权限与特性并没有不一样。默认 1000 以下的数字让给系统作为保留帐号只是一个习惯。由于系统上面启动的网络服务或背景服务希望使用较小的权限去运行，因此不希望使用 root 的身份去执行这些服务， 所以我们就得要提供这些运行中程序的拥有者帐号才行。这些系统帐号通常是不可登陆的， 所以才会有我们在[第十章](../Text/index.html)提到的 /sbin/nologin 这个特殊的 shell 存在。根据系统帐号的由来，通常这类帐号又约略被区分为两种：1~200：由 distributions 自行创建的系统帐号；201~999：若使用者有系统帐号需求时，可以使用的帐号 UID。 |
   | 1000~60000（可登陆帐号） | 给一般使用者用的。事实上，目前的 linux 核心 （3.10.x 版）已经可以支持到 4294967295 （2^32-1） 这么大的 UID 号码喔！ |
   ```

   上面这样说明可以了解了吗？是的， UID 为 0 的时候，就是 root 呦！所以请特别留意一下你的 /etc/passwd 文件！

4. GID： 这个与 /etc/group 有关！其实 /etc/group 的观念与 /etc/passwd 差不多，只是他是用来规范群组名称与 GID 的对应而已！

5. 使用者信息说明栏： 这个字段基本上并没有什么重要用途，只是用来解释这个帐号的意义而已！不过，如果您提供使用 finger 的功能时， 这个字段可以提供很多的讯息呢！本章后面的 [chfn](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#chfn) 指令会来解释这里的说明。

6. 主文件夹： 这是使用者的主文件夹，以上面为例， root 的主文件夹在 /root ，所以当 root 登陆之后，就会立刻跑到 /root 目录里头啦！呵呵！ 如果你有个帐号的使用空间特别的大，你想要将该帐号的主文件夹移动到其他的硬盘去该怎么作？ 没有错！可以在这个字段进行修改呦！默认的使用者主文件夹在 /home/yourIDname

7. Shell： 我们在[第十章 BASH](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 提到很多次，当使用者登陆系统后就会取得一个 Shell 来与系统的核心沟通以进行使用者的操作任务。那为何默认 shell 会使用 bash 呢？就是在这个字段指定的啰！ 这里比较需要注意的是，有一个 shell 可以用来替代成让帐号无法取得 shell 环境的登陆动作！那就是 /sbin/nologin 这个东西！这也可以用来制作纯 pop 邮件帐号者的数据呢！

8. /etc/shadow 文件结构

我们知道很多程序的运行都与权限有关，而权限与 UID/GID 有关！因此各程序当然需要读取 /etc/passwd 来了解不同帐号的权限。 因此 /etc/passwd 的权限需设置为 -rw-r--r-- 这样的情况， 虽然早期的密码也有加密过，但却放置到 /etc/passwd 的第二个字段上！这样一来很容易被有心人士所窃取的， 加密过的密码也能够通过暴力破解法去 trial and error （试误） 找出来！

因为这样的关系，所以后来发展出将密码移动到 /etc/shadow 这个文件分隔开来的技术， 而且还加入很多的密码限制参数在 /etc/shadow 里头呢！在这里，我们先来了解一下这个文件的构造吧！ 鸟哥的 /etc/shadow 文件有点像这样：

```
[root@study ~]# head -n 4 /etc/shadow
root:$6$wtbCCce/PxMeE5wm$KE2IfSJr.YLP7Rcai6oa/T7KFhO...:16559:0:99999:7:::  &lt;==下面说明用
bin:*:16372:0:99999:7:::
daemon:*:16372:0:99999:7:::
adm:*:16372:0:99999:7:::
```

基本上， shadow 同样以“:”作为分隔符号，如果数一数，会发现共有九个字段啊，这九个字段的用途是这样的：

1. 帐号名称： 由于密码也需要与帐号对应啊～因此，这个文件的第一栏就是帐号，必须要与 /etc/passwd 相同才行！

2. 密码： 这个字段内的数据才是真正的密码，而且是经过编码的密码 （加密） 啦！ 你只会看到有一些特殊符号的字母就是了！需要特别留意的是，虽然这些加密过的密码很难被解出来， 但是“很难”不等于“不会”，所以，这个文件的默认权限是“-rw-------”或者是“----------”，亦即只有 root 才可以读写就是了！你得随时注意，不要不小心更动了这个文件的权限呢！

   另外，由于各种密码编码的技术不一样，因此不同的编码系统会造成这个字段的长度不相同。 举例来说，旧式的 DES, MD5 编码系统产生的密码长度就与目前惯用的 SHA 不同[[2\]](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/114.html#ps2)！SHA 的密码长度明显的比较长些。由于固定的编码系统产生的密码长度必须一致，因此“当你让这个字段的长度改变后，该密码就会失效（算不出来）”。 很多软件通过这个功能，在此字段前加上 ! 或 * 改变密码字段长度，就会让密码“暂时失效”了。

3. 最近更动密码的日期： 这个字段记录了“更动密码那一天”的日期，不过，很奇怪呀！在我的例子中怎么会是 16559 呢？呵呵，这个是因为计算 Linux 日期的时间是以 1970 年 1 月 1 日作为 1 而累加的日期，1971 年 1 月 1 日则为 366 啦！ 得注意一下这个数据呦！上述的 16559 指的就是 2015-05-04 那一天啦！了解乎？ 而想要了解该日期可以使用本章后面 [chage](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#chage) 指令的帮忙！至于想要知道某个日期的累积日数， 可使用如下的程序计算：

   ```
   [root@study ~]# echo $（（$（date --date="2015/05/04" +%s）/86400+1））
   16559
   ```

   上述指令中，2015/05/04 为你想要计算的日期，86400 为每一天的秒数， %s 为 1970/01/01 以来的累积总秒数。 由于 bash 仅支持整数，因此最终需要加上 1 补齐 1970/01/01 当天。

4. 密码不可被更动的天数：（与第 3 字段相比） 第四个字段记录了：这个帐号的密码在最近一次被更改后需要经过几天才可以再被变更！如果是 0 的话， 表示密码随时可以更动的意思。这的限制是为了怕密码被某些人一改再改而设计的！如果设置为 20 天的话，那么当你设置了密码之后， 20 天之内都无法改变这个密码呦！

5. 密码需要重新变更的天数：（与第 3 字段相比） 经常变更密码是个好习惯！为了强制要求使用者变更密码，这个字段可以指定在最近一次更改密码后， 在多少天数内需要再次的变更密码才行。你必须要在这个天数内重新设置你的密码，否则这个帐号的密码将会“变为过期特性”。 而如果像上面的 99999 （计算为 273 年） 的话，那就表示，呵呵，密码的变更没有强制性之意。

6. 密码需要变更期限前的警告天数：（与第 5 字段相比） 当帐号的密码有效期限快要到的时候 （第 5 字段），系统会依据这个字段的设置，发出“警告”言论给这个帐号，提醒他“再过 n 天你的密码就要过期了，请尽快重新设置你的密码呦！”，如上面的例子，则是密码到期之前的 7 天之内，系统会警告该用户。

7. 密码过期后的帐号宽限时间（密码失效日）：（与第 5 字段相比） 密码有效日期为“更新日期（第3字段）”+“重新变更日期（第5字段）”，过了该期限后使用者依旧没有更新密码，那该密码就算过期了。 虽然密码过期但是该帐号还是可以用来进行其他工作的，包括登陆系统取得 bash 。不过如果密码过期了， 那当你登陆系统时，系统会强制要求你必须要重新设置密码才能登陆继续使用喔，这就是密码过期特性。

   那这个字段的功能是什么呢？是在密码过期几天后，如果使用者还是没有登陆更改密码，那么这个帐号的密码将会“失效”， 亦即该帐号再也无法使用该密码登陆了。要注意密码过期与密码失效并不相同。

8. 帐号失效日期： 这个日期跟第三个字段一样，都是使用 1970 年以来的总日数设置。这个字段表示： 这个帐号在此字段规定的日期之后，将无法再使用。 就是所谓的“帐号失效”，此时不论你的密码是否有过期，这个“帐号”都不能再被使用！ 这个字段会被使用通常应该是在“收费服务”的系统中，你可以规定一个日期让该帐号不能再使用啦！

9. 保留： 最后一个字段是保留的，看以后有没有新功能加入。

举个例子来说好了，假如我的 dmtsai 这个使用者的密码栏如下所示：

```
dmtsai:$6$M4IphgNP2TmlXaSS$B418YFroYxxmm....:16559:5:60:7:5:16679:
```

这表示什么呢？先要注意的是 16559 是 2015/05/04 。所以 dmtsai 这个使用者的密码相关意义是：

- 由于密码几乎仅能单向运算（由明码计算成为密码，无法由密码反推回明码），因此由上表的数据我们无法得知 dmstai 的实际密码明文 （第二个字段）；

- 此帐号最近一次更动密码的日期是 2015/05/04 （16559）；

- 能够再次修改密码的时间是 5 天以后，也就是 2015/05/09 以前 dmtsai 不能修改自己的密码；如果使用者还是尝试要更动自己的密码，系统就会出现这样的讯息：

  ```
  You must wait longer to change your password
  passwd: Authentication token manipulation error
  ```

  画面中告诉我们：你必须要等待更久的时间才能够变更密码之意啦！

- 由于密码过期日期定义为 60 天后，亦即累积日数为： 16559+60=16619，经过计算得到此日数代表日期为 2015/07/03。 这表示：“使用者必须要在 2015/05/09 （前 5 天不能改） 到 2015/07/03 之间的 60 天限制内去修改自己的密码，若 2015/07/03 之后还是没有变更密码时，该密码就宣告为过期”了！

- 警告日期设为 7 天，亦即是密码过期日前的 7 天，在本例中则代表 2015/06/26 ~ 2015/07/03 这七天。 如果使用者一直没有更改密码，那么在这 7 天中，只要 dmtsai 登陆系统就会发现如下的讯息：

  ```
  Warning: your password will expire in 5 days
  ```

- 如果该帐号一直到 2015/07/03 都没有更改密码，那么密码就过期了。但是由于有 5 天的宽限天数， 因此 dmtsai 在 2015/07/08 前都还可以使用旧密码登陆主机。 不过登陆时会出现强制更改密码的情况，画面有点像下面这样：

  ```
  You are required to change your password immediately （password aged）
  WARNING: Your password has expired.
  You must change your password now and login again!
  Changing password for user dmtsai.
  Changing password for dmtsai
  （current） UNIX password:
  ```

  你必须要输入一次旧密码以及两次新密码后，才能够开始使用系统的各项资源。如果你是在 2015/07/08 以后尝试以 dmtsai 登陆的话，那么就会出现如下的错误讯息且无法登陆，因为此时你的密码就失效去啦！

  ```
  Your account has expired; please contact your system administrator
  ```

- 如果使用者在 2015/07/03 以前变更过密码，那么第 3 个字段的那个 16559 的天数就会跟着改变，因此， 所有的限制日期也会跟着相对变动喔！^_^

- 无论使用者如何动作，到了 16679 （大约是 2015/09/01 左右） 该帐号就失效了～

通过这样的说明，您应该会比较容易理解了吧？由于 shadow 有这样的重要性，因此可不能随意修改喔！ 但在某些情况下面你得要使用各种方法来处理这个文件的！举例来说，常常听到人家说：“我的密码忘记了”， 或者是“我的密码不晓得被谁改过，跟原先的不一样了”，这个时候怎么办？

- 一般用户的密码忘记了：这个最容易解决，请系统管理员帮忙， 他会重新设置好你的密码而不需要知道你的旧密码！利用 root 的身份使用 [passwd](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#passwd) 指令来处理即可。
- root 密码忘记了：这就麻烦了！因为你无法使用 root 的身份登陆了嘛！ 但我们知道 root 的密码在 /etc/shadow 当中，因此你可以使用各种可行的方法开机进入 Linux 再去修改。 例如重新开机进入单人维护模式（[第十九章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)）后，系统会主动的给予 root 权限的 bash 接口， 此时再以 passwd 修改密码即可；或以 Live CD 开机后挂载根目录去修改 /etc/shadow，将里面的 root 的密码字段清空， 再重新开机后 root 将不用密码即可登陆！登陆后再赶快以 passwd 指令去设置 root 密码即可。

![鸟哥的图示](image/vbird_face-17224112902504.gif)

**Tips** 曾经听过一则笑话，某位老师主要是在教授 Linux 操作系统，但是他是兼任的老师，因此对于该系的计算机环境不熟。 由于当初安装该计算机教室 Linux 操作系统的人员已经离职且找不到联络方式了，也就是说 root 密码已经没有人晓得了！ 此时该老师就对学生说：“在 Linux 里面 root 密码不见了，我们只能重新安装”...感觉有点无力～ 又是个被 Windows 制约的人才！

另外，由于 Linux 的新旧版本差异颇大，旧的版本 （CentOS 5.x 以前） 还活在很多服务器内！因此，如果你想要知道 shadow 是使用哪种加密的机制时， 可以通过下面的方法去查询喔！

```
[root@study ~]# authconfig --test &#124; grep hashing
 password hashing algorithm is sha512
# 这就是目前的密码加密机制！
```

### 13.1.3 关于群组：有效与初始群组、groups, newgrp

#### /etc/group 文件结构

分为四个字段：

1. 群组名称
2. 群组密码（通常为 "x"）
3. GID
4. 此群组支持的帐号名称

#### 有效群组与初始群组

每个用户在 /etc/passwd 文件中的第四栏是初始群组（initial group），表示用户登录后默认所属的群组。用户可以通过 newgrp 命令切换有效群组（effective group），但只能切换到用户支持的群组。

**示例：**

```
groups
# 输出：dmtsai wheel users
```

表示 dmtsai 支持 dmtsai、wheel 和 users 这三个群组，其中 dmtsai 为有效群组。

**切换有效群组：**

```
newgrp users
groups
# 输出：users wheel dmtsai
```

#### /etc/gshadow 文件结构

分为四个字段：

1. 群组名称
2. 密码栏（若为 "!" 或空，则表示无群组管理员）
3. 群组管理员的帐号
4. 有加入该群组支持的所属帐号

gshadow 文件主要用于创建群组管理员。群组管理员可以管理群组内的用户，而无需系统管理员（root）干预。

通过了解这些文件和命令，系统管理员可以有效地管理 Linux 系统中的用户和群组，确保系统的安全和正常运行。

#### 原文：

认识了帐号相关的两个文件 /etc/passwd 与 /etc/shadow 之后，你或许还是会觉得奇怪， 那么群组的配置文件在哪里？还有，在 /etc/passwd 的第四栏不是所谓的 GID 吗？那又是啥？ 呵呵～此时就需要了解 /etc/group 与 /etc/gshadow 啰～

- /etc/group 文件结构

这个文件就是在记录 GID 与群组名称的对应了～鸟哥测试机的 /etc/group 内容有点像这样：

```
[root@study ~]# head -n 4 /etc/group
root:x:0:
bin:x:1:
daemon:x:2:
sys:x:3:
```

这个文件每一行代表一个群组，也是以冒号“:”作为字段的分隔符号，共分为四栏，每一字段的意义是：

1. 群组名称： 就是群组名称啦！同样用来给人类使用的，基本上需要与第三字段的 GID 对应。
2. 群组密码： 通常不需要设置，这个设置通常是给“群组管理员”使用的，目前很少有这个机会设置群组管理员啦！ 同样的，密码已经移动到 /etc/gshadow 去，因此这个字段只会存在一个“x”而已；
3. GID： 就是群组的 ID 啊。我们 /etc/passwd 第四个字段使用的 GID 对应的群组名，就是由这里对应出来的！
4. 此群组支持的帐号名称： 我们知道一个帐号可以加入多个群组，那某个帐号想要加入此群组时，将该帐号填入这个字段即可。 举例来说，如果我想要让 dmtsai 与 alex 也加入 root 这个群组，那么在第一行的最后面加上“dmtsai,alex”，注意不要有空格， 使成为“ root:x:0:dmtsai,alex ”就可以啰～

谈完了 /etc/passwd, /etc/shadow, /etc/group 之后，我们可以使用一个简单的图示来了解一下 UID / GID 与密码之间的关系， 图示如下。其实重点是 /etc/passwd 啦，其他相关的数据都是根据这个文件的字段去找寻出来的。 下图中， root 的 UID 是 0 ，而 GID 也是 0 ，去找 /etc/group 可以知道 GID 为 0 时的群组名称就是 root 哩。 至于密码的寻找中，会找到 /etc/shadow 与 /etc/passwd 内同帐号名称的那一行，就是密码相关数据啰。

![帐号相关文件之间的 UID/GID 与密码相关性示意图](image/centos7_id_link.jpg)图13.1.1、帐号相关文件之间的 UID/GID 与密码相关性示意图

至于在 /etc/group 比较重要的特色在于第四栏啦，因为每个使用者都可以拥有多个支持的群组，这就好比在学校念书的时候， 我们可以加入多个社团一样！ ^_^。不过这里你或许会觉得奇怪的，那就是：“假如我同时加入多个群组，那么我在作业的时候，到底是以那个群组为准？” 下面我们就来谈一谈这个“有效群组”的概念。

![鸟哥的图示](image/vbird_face-17224113605587.gif)

**Tips** 请注意，新版的 Linux 中，初始群组的用户群已经不会加入在第四个字段！例如我们知道 root 这个帐号的主要群组为 root，但是在上面的范例中， 你已经不会看到 root 这个“用户”的名称在 /etc/group 的 root 那一行的第四个字段内啰！这点还请留意一下即可！

- 有效群组（effective group）与初始群组（initial group）

还记得每个使用者在他的 /etc/passwd 里面的第四栏有所谓的 GID 吧？那个 GID 就是所谓的“初始群组 （initial group） ”！也就是说，当使用者一登陆系统，立刻就拥有这个群组的相关权限的意思。 举例来说，我们上面提到 dmtsai 这个使用者的 /etc/passwd 与 /etc/group 还有 /etc/gshadow 相关的内容如下：

```
[root@study ~]# usermod -a -G users dmtsai  &lt;==先设置好次要群组
[root@study ~]# grep dmtsai /etc/passwd /etc/group /etc/gshadow
/etc/passwd:dmtsai:x:1000:1000:dmtsai:/home/dmtsai:/bin/bash
/etc/group:wheel:x:10:dmtsai    &lt;==次要群组的设置、安装时指定的
/etc/group:users:x:100:dmtsai   &lt;==次要群组的设置
/etc/group:dmtsai:x:1000:       &lt;==因为是初始群组，所以第四字段不需要填入帐号
/etc/gshadow:wheel:::dmtsai     &lt;==次要群组的设置
/etc/gshadow:users:::dmtsai     &lt;==次要群组的设置
/etc/gshadow:dmtsai:!!::
```

仔细看到上面这个表格，在 /etc/passwd 里面，dmtsai 这个使用者所属的群组为 GID=1000 ，搜寻一下 /etc/group 得到 1000 是那个名为 dmtsai 的群组啦！这就是 initial group。因为是初始群组， 使用者一登陆就会主动取得，不需要在 /etc/group 的第四个字段写入该帐号的！

但是非 initial group 的其他群组可就不同了。举上面这个例子来说，我将 dmtsai 加入 users 这个群组当中，由于 users 这个群组并非是 dmtsai 的初始群组，因此， 我必须要在 /etc/group 这个文件中，找到 users 那一行，并且将 dmtsai 这个帐号加入第四栏， 这样 dmtsai 才能够加入 users 这个群组啊。

那么在这个例子当中，因为我的 dmtsai 帐号同时支持 dmtsai, wheel 与 users 这三个群组， 因此，在读取/写入/可执行文件案时，针对群组部分，只要是 users, wheel 与 dmtsai 这三个群组拥有的功能， 我 dmtsai 这个使用者都能够拥有喔！这样瞭呼？不过，这是针对已经存在的文件而言， 如果今天我要创建一个新的文件或者是新的目录，请问一下，新文件的群组是 dmtsai, wheel 还是 users ？呵呵！这就得要检查一下当时的有效群组了 （effective group）。

- groups: 有效与支持群组的观察

如果我以 dmtsai 这个使用者的身份登陆后，该如何知道我所有支持的群组呢？ 很简单啊，直接输入 groups 就可以了！注意喔，是 groups 有加 s 呢！结果像这样：

```
[dmtsai@study ~]$ groups
dmtsai wheel users
```

在这个输出的讯息中，可知道 dmtsai 这个用户同时属于 dmtsai, wheel 及 users 这三个群组，而且， 第一个输出的群组即为有效群组 （effective group） 了。 也就是说，我的有效群组为 dmtsai 啦～此时，如果我以 touch 去创建一个新文件，例如： “ touch test ”，那么这个文件的拥有者为 dmtsai ，而且群组也是 dmtsai 的啦。

```
[dmtsai@study ~]$ touch test
[dmtsai@study ~]$ ll test
-rw-rw-r--. 1 dmtsai dmtsai 0 Jul 20 19:54 test
```

这样是否可以了解什么是有效群组了？通常有效群组的作用是在新建文件啦！那么有效群组是否能够变换？

- newgrp: 有效群组的切换

那么如何变更有效群组呢？就使用 newgrp 啊！不过使用 newgrp 是有限制的，那就是你想要切换的群组必须是你已经有支持的群组。举例来说， dmtsai 可以在 dmtsai/wheel/users 这三个群组间切换有效群组，但是 dmtsai 无法切换有效群组成为 sshd 啦！使用的方式如下：

```
[dmtsai@study ~]$ newgrp users
[dmtsai@study ~]$ groups
users wheel dmtsai
[dmtsai@study ~]$ touch test2
[dmtsai@study ~]$ ll test*
-rw-rw-r--. 1 dmtsai dmtsai 0 Jul 20 19:54 test
-rw-r--r--. 1 dmtsai users  0 Jul 20 19:56 test2
[dmtsai@study ~]$ exit   # 注意！记得离开 newgrp 的环境喔！
```

此时，dmtsai 的有效群组就成为 users 了。我们额外的来讨论一下 newgrp 这个指令，这个指令可以变更目前使用者的有效群组， 而且是另外以一个 shell 来提供这个功能的喔，所以，以上面的例子来说， dmtsai 这个使用者目前是以另一个 shell 登陆的，而且新的 shell 给予 dmtsai 有效 GID 为 users 就是了。如果以图示来看就是如下所示：

![newgrp 的运行示意图](image/newgrp.gif)图13.1.2、newgrp 的运行示意图

虽然使用者的环境设置（例如环境变量等等其他数据）不会有影响，但是使用者的“群组权限”将会重新被计算。 但是需要注意，由于是新取得一个 shell ，因此如果你想要回到原本的环境中，请输入 exit 回到原本的 shell 喔！

既然如此，也就是说，只要我的用户有支持的群组就是能够切换成为有效群组！好了， 那么如何让一个帐号加入不同的群组就是问题的所在啰。你要加入一个群组有两个方式，一个是通过系统管理员 （root） 利用 [usermod](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#usermod) 帮你加入，如果 root 太忙了而且你的系统有设置群组管理员，那么你可以通过群组管理员以 [gpasswd](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#gpasswd) 帮你加入他所管理的群组中！详细的作法留待下一小节再来介绍啰！

- /etc/gshadow

刚刚讲了很多关于“有效群组”的概念，另外，也提到 newgrp 这个指令的用法，但是，如果 /etc/gshadow 这个设置没有搞懂得话，那么 newgrp 是无法动作的呢！ 鸟哥测试机的 /etc/gshadow 的内容有点像这样：

```
[root@study ~]# head -n 4 /etc/gshadow
root:::
bin:::
daemon:::
sys:::
```

这个文件内同样还是使用冒号“:”来作为字段的分隔字符，而且你会发现，这个文件几乎与 /etc/group 一模一样啊！是这样没错～不过，要注意的大概就是第二个字段吧～第二个字段是密码栏， 如果密码栏上面是“!”或空的时，表示该群组不具有群组管理员！至于第四个字段也就是支持的帐号名称啰～ 这四个字段的意义为：

1. 群组名称
2. 密码栏，同样的，开头为 ! 表示无合法密码，所以无群组管理员
3. 群组管理员的帐号 （相关信息在 [gpasswd](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#gpasswd) 中介绍）
4. 有加入该群组支持的所属帐号 （与 /etc/group 内容相同！）

以系统管理员的角度来说，这个 gshadow 最大的功能就是创建群组管理员啦！ 那么什么是群组管理员呢？由于系统上面的帐号可能会很多，但是我们 root 可能平时太忙碌，所以当有使用者想要加入某些群组时， root 或许会没有空管理。此时如果能够创建群组管理员的话，那么该群组管理员就能够将那个帐号加入自己管理的群组中！ 可以免去 root 的忙碌啦！不过，由于目前有类似 [sudo](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#sudo) 之类的工具， 所以这个群组管理员的功能已经很少使用了。我们会在后续的 gpasswd 中介绍这个实作。



## 13.2 帐号管理

管理帐号包括新增、移除与更改使用者的相关信息。下面我们详细介绍如何新增、移除与更改使用者的相关信息。



### 13.2.1 新增与移除使用者： useradd, 相关配置文件, passwd, usermod, userdel

要在 Linux 系统新增一个使用者，可以使用 `useradd` 创建帐号，使用 `passwd` 设置密码。

#### useradd

语法：

```
useradd [-u UID] [-g 初始群组] [-G 次要群组] [-mM] [-c 说明栏] [-d 主文件夹绝对路径] [-s shell] 使用者帐号名
```

常用选项：

- `-u UID`：指定使用者的 UID。
- `-g`：指定初始群组。
- `-G`：指定次要群组。
- `-M`：不创建使用者主文件夹（系统帐号默认值）。
- `-m`：创建使用者主文件夹（一般帐号默认值）。
- `-c`：说明栏。
- `-d`：指定主文件夹的绝对路径。
- `-r`：创建系统帐号，UID 会小于 1000。
- `-s`：指定 shell。
- `-e`：帐号失效日期（格式：YYYY-MM-DD）。
- `-f`：密码失效天数。

**示例一：创建一个使用者 vbird1**

```
useradd vbird1
```

会创建 `/home/vbird1` 主文件夹，权限为 700。

**示例二：创建一个指定群组和 UID 的使用者 vbird2**

```
useradd -u 1500 -g users vbird2
```

**示例三：创建一个系统帐号 vbird3**

```
useradd -r vbird3
```

#### useradd 参考文件

useradd 的默认值可以通过 `/etc/default/useradd` 调用出来。常见默认值：

- `GROUP=100`：默认群组。
- `HOME=/home`：默认主文件夹目录。
- `SHELL=/bin/bash`：默认 shell。
- `SKEL=/etc/skel`：主文件夹内容参考目录。

其他默认设置包括密码有效期、UID/GID 范围等，可以在 `/etc/login.defs` 中配置。

#### passwd

语法：

```
passwd [--stdin] [帐号名称]
```

常用选项：

- `--stdin`：通过管道输入密码。
- `-l`：锁定密码。
- `-u`：解锁密码。
- `-S`：显示密码状态。
- `-n`：最小密码修改天数。
- `-x`：密码有效期。
- `-w`：密码过期警告天数。
- `-i`：密码失效日期。

**示例一：设置 vbird2 的密码**

```
passwd vbird2
```

**示例二：使用 stdin 设置密码**

```
echo "abc543CC" | passwd --stdin vbird2
```

#### chage

显示或更改用户密码的过期信息。

语法：

```
chage [-ldEImMW] 帐号名
```

常用选项：

- `-l`：列出密码参数。
- `-d`：修改密码上次更改日期。
- `-E`：修改帐号失效日期。
- `-I`：密码失效日期。
- `-m`：密码最短保留天数。
- `-M`：密码最大有效天数。
- `-W`：密码过期警告天数。

**示例一：列出 vbird2 的密码参数**

```
chage -l vbird2
```

**示例二：强制用户在首次登录时更改密码**

```
chage -d 0 agetest
```

#### usermod

修改现有用户的属性。

语法：

```
usermod [-cdegGlsuLU] username
```

常用选项：

- `-c`：说明栏。
- `-d`：主文件夹。
- `-e`：帐号失效日期。
- `-f`：密码失效天数。
- `-g`：初始群组。
- `-G`：次要群组。
- `-a`：与 -G 合用，增加次要群组。
- `-l`：修改帐号名称。
- `-s`：修改 shell。
- `-u`：修改 UID。
- `-L`：锁定密码。
- `-U`：解锁密码。

**示例一：修改 vbird2 的说明栏**

```
usermod -c "VBird's test" vbird2
```

**示例二：设置 vbird2 的帐号失效日期**

```
usermod -e "2015-12-31" vbird2
```

#### userdel

删除用户。

语法：

```
userdel [-r] username
```

常用选项：

- `-r`：删除用户的主文件夹。

**示例：删除 vbird2，包括主文件夹**

```
userdel -r vbird2
```

#### **原文：**

要如何在 Linux 的系统新增一个使用者啊？呵呵～真是太简单了～我们登陆系统时会输入 （1）帐号与 （2）密码， 所以创建一个可用的帐号同样的也需要这两个数据。那帐号可以使用 useradd 来新建使用者，密码的给予则使用 passwd 这个指令！这两个指令下达方法如下：

- useradd

```
[root@study ~]# useradd [-u UID] [-g 初始群组] [-G 次要群组] [-mM]\
&gt;  [-c 说明栏] [-d 主文件夹绝对路径] [-s shell] 使用者帐号名
选项与参数：
-u  ：后面接的是 UID ，是一组数字。直接指定一个特定的 UID 给这个帐号；
-g  ：后面接的那个群组名称就是我们上面提到的 initial group 啦～
      该群组的 GID 会被放置到 /etc/passwd 的第四个字段内。
-G  ：后面接的群组名称则是这个帐号还可以加入的群组。
      这个选项与参数会修改 /etc/group 内的相关数据喔！
-M  ：强制！不要创建使用者主文件夹！（系统帐号默认值）
-m  ：强制！要创建使用者主文件夹！（一般帐号默认值）
-c  ：这个就是 /etc/passwd 的第五栏的说明内容啦～可以随便我们设置的啦～
-d  ：指定某个目录成为主文件夹，而不要使用默认值。务必使用绝对路径！
-r  ：创建一个系统的帐号，这个帐号的 UID 会有限制 （参考 /etc/login.defs）
-s  ：后面接一个 shell ，若没有指定则默认是 /bin/bash 的啦～
-e  ：后面接一个日期，格式为“YYYY-MM-DD”此项目可写入 shadow 第八字段，
      亦即帐号失效日的设置项目啰；
-f  ：后面接 shadow 的第七字段项目，指定密码是否会失效。0为立刻失效，
      -1 为永远不失效（密码只会过期而强制于登陆时重新设置而已。）

范例一：完全参考默认值创建一个使用者，名称为 vbird1
[root@study ~]# useradd vbird1
[root@study ~]# ll -d /home/vbird1
drwx------. 3 vbird1 vbird1 74 Jul 20 21:50 /home/vbird1
# 默认会创建使用者主文件夹，且权限为 700 ！这是重点！

[root@study ~]# grep vbird1 /etc/passwd /etc/shadow /etc/group
/etc/passwd:vbird1:x:1003:1004::/home/vbird1:/bin/bash
/etc/shadow:vbird1:!!:16636:0:99999:7:::
/etc/group:vbird1:x:1004:     &lt;==默认会创建一个与帐号一模一样的群组名
```

其实系统已经帮我们规定好非常多的默认值了，所以我们可以简单的使用“ useradd 帐号 ”来创建使用者即可。 CentOS 这些默认值主要会帮我们处理几个项目：

- 在 /etc/passwd 里面创建一行与帐号相关的数据，包括创建 UID/GID/主文件夹等；
- 在 /etc/shadow 里面将此帐号的密码相关参数填入，但是尚未有密码；
- 在 /etc/group 里面加入一个与帐号名称一模一样的群组名称；
- 在 /home 下面创建一个与帐号同名的目录作为使用者主文件夹，且权限为 700

由于在 /etc/shadow 内仅会有密码参数而不会有加密过的密码数据，因此我们在创建使用者帐号时， 还需要使用“ passwd 帐号 ”来给予密码才算是完成了使用者创建的流程。如果由于特殊需求而需要改变使用者相关参数时， 就得要通过上述表格中的选项来进行创建了，参考下面的案例：

```
范例二：假设我已知道我的系统当中有个群组名称为 users ，且 UID 1500 并不存在，
        请用 users 为初始群组，以及 uid 为 1500 来创建一个名为 vbird2 的帐号
[root@study ~]# useradd -u 1500 -g users vbird2
[root@study ~]# ll -d /home/vbird2
drwx------. 3 vbird2 users 74 Jul 20 21:52 /home/vbird2

[root@study ~]# grep vbird2 /etc/passwd /etc/shadow /etc/group
/etc/passwd:vbird2:x:1500:100::/home/vbird2:/bin/bash
/etc/shadow:vbird2:!!:16636:0:99999:7:::
# 看一下，UID 与 initial group 确实改变成我们需要的了！
```

在这个范例中，我们创建的是指定一个已经存在的群组作为使用者的初始群组，因为群组已经存在， 所以在 /etc/group 里面就不会主动的创建与帐号同名的群组了！ 此外，我们也指定了特殊的 UID 来作为使用者的专属 UID 喔！了解了一般帐号后，我们来瞧瞧那啥是系统帐号 （system account） 吧！

```
范例三：创建一个系统帐号，名称为 vbird3
[root@study ~]# useradd -r vbird3
[root@study ~]# ll -d /home/vbird3
ls: cannot access /home/vbird3: No such file or directorya   &lt;==不会主动创建主文件夹

[root@study ~]# grep vbird3 /etc/passwd /etc/shadow /etc/group
/etc/passwd:vbird3:x:699:699::/home/vbird3:/bin/bash
/etc/shadow:vbird3:!!:16636::::::
/etc/group:vbird3:x:699:
```

我们在谈到 UID 的时候曾经说过一般帐号应该是 1000 号以后，那使用者自己创建的系统帐号则一般是小于 1000 号以下的。 所以在这里我们加上 -r 这个选项以后，系统就会主动将帐号与帐号同名群组的 UID/GID 都指定小于 1000 以下， 在本案例中则是使用 699（UID） 与 699（GID） 啰！此外，由于系统帐号主要是用来进行运行系统所需服务的权限设置， 所以系统帐号默认都不会主动创建主文件夹的！

由这几个范例我们也会知道，使用 useradd 创建使用者帐号时，其实会更改不少地方，至少我们就知道下面几个文件：

- 使用者帐号与密码参数方面的文件：/etc/passwd, /etc/shadow
- 使用者群组相关方面的文件：/etc/group, /etc/gshadow
- 使用者的主文件夹：/home/帐号名称

那请教一下，你有没有想过，为何“ useradd vbird1 ”会主动在 /home/vbird1 创建起使用者的主文件夹？主文件夹内有什么数据且来自哪里？为何默认使用的是 /bin/bash 这个 shell ？为何密码字段已经都规范好了 （0:99999:7 那一串）？呵呵！这就得要说明一下 useradd 所使用的参考文件啰！

- useradd 参考档

其实 useradd 的默认值可以使用下面的方法调用出来：

```
[root@study ~]# useradd -D
GROUP=100        &lt;==默认的群组
HOME=/home        &lt;==默认的主文件夹所在目录
INACTIVE=-1        &lt;==密码失效日，在 shadow 内的第 7 栏
EXPIRE=            &lt;==帐号失效日，在 shadow 内的第 8 栏
SHELL=/bin/bash        &lt;==默认的 shell
SKEL=/etc/skel        &lt;==使用者主文件夹的内容数据参考目录
CREATE_MAIL_SPOOL=yes   &lt;==是否主动帮使用者创建邮件信箱（mailbox）
```

这个数据其实是由 /etc/default/useradd 调用出来的！你可以自行用 vim 去观察该文件的内容。搭配上头刚刚谈过的范例一的运行结果，上面这些设置项目所造成的行为分别是：

- GROUP=100：新建帐号的初始群组使用 GID 为 100 者

系统上面 GID 为 100 者即是 users 这个群组，此设置项目指的就是让新设使用者帐号的初始群组为 users 这一个的意思。 但是我们知道 CentOS 上面并不是这样的，在 CentOS 上面默认的群组为与帐号名相同的群组。 举例来说， vbird1 的初始群组为 vbird1 。怎么会这样啊？这是因为针对群组的角度有两种不同的机制所致， 这两种机制分别是：

- 私有群组机制：

系统会创建一个与帐号一样的群组给使用者作为初始群组。 这种群组的设置机制会比较有保密性，这是因为使用者都有自己的群组，而且主文件夹权限将会设置为 700 （仅有自己可进入自己的主文件夹） 之故。使用这种机制将不会参考 GROUP=100 这个设置值。代表性的 distributions 有 RHEL, Fedora, CentOS 等；

- 公共群组机制：

就是以 GROUP=100 这个设置值作为新建帐号的初始群组，因此每个帐号都属于 users 这个群组， 且默认主文件夹通常的权限会是“ drwxr-xr-x ... username users ... ”，由于每个帐号都属于 users 群组，因此大家都可以互相分享主文件夹内的数据之故。代表 distributions 如 SuSE等。

由于我们的 CentOS 使用私有群组机制，因此这个设置项目是不会生效的！不要太紧张啊！

- HOME=/home：使用者主文件夹的基准目录（basedir）

使用者的主文件夹通常是与帐号同名的目录，这个目录将会摆放在此设置值的目录后。所以 vbird1 的主文件夹就会在 /home/vbird1/ 了！很容易理解吧！

- INACTIVE=-1：密码过期后是否会失效的设置值

我们在 [shadow](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#shadow_file) 文件结构当中谈过，第七个字段的设置值将会影响到密码过期后， 在多久时间内还可使用旧密码登陆。这个项目就是在指定该日数啦！如果是 0 代表密码过期立刻失效， 如果是 -1 则是代表密码永远不会失效，如果是数字，如 30 ，则代表过期 30 天后才失效。

- EXPIRE=：帐号失效的日期

就是 [shadow](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#shadow_file) 内的第八字段，你可以直接设置帐号在哪个日期后就直接失效，而不理会密码的问题。 通常不会设置此项目，但如果是付费的会员制系统，或许这个字段可以设置喔！

- SHELL=/bin/bash：默认使用的 shell 程序文件名

系统默认的 shell 就写在这里。假如你的系统为 mail server ，你希望每个帐号都只能使用 email 的收发信件功能， 而不许使用者登陆系统取得 shell ，那么可以将这里设置为 /sbin/nologin ，如此一来，新建的使用者默认就无法登陆！ 也免去后续使用 [usermod](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#usermod) 进行修改的手续！

- SKEL=/etc/skel：使用者主文件夹参考基准目录

这个咚咚就是指定使用者主文件夹的参考基准目录啰～举我们的范例一为例， vbird1 主文件夹 /home/vbird1 内的各项数据，都是由 /etc/skel 所复制过去的～所以呢，未来如果我想要让新增使用者时，该使用者的环境变量 ~/.bashrc 就设置妥当的话，您可以到 /etc/skel/.bashrc 去编辑一下，也可以创建 /etc/skel/www 这个目录，那么未来新增使用者后，在他的主文件夹下就会有 www 那个目录了！这样瞭呼？

- CREATE_MAIL_SPOOL=yes：创建使用者的 mailbox

你可以使用“ ll /var/spool/mail/vbird1 ”看一下，会发现有这个文件的存在喔！这就是使用者的邮件信箱！

除了这些基本的帐号设置值之外， UID/GID 还有密码参数又是在哪里参考的呢？那就得要看一下 /etc/login.defs 啦！ 这个文件的内容有点像下面这样：

```
MAIL_DIR        /var/spool/mail  &lt;==使用者默认邮件信箱放置目录

PASS_MAX_DAYS   99999    &lt;==/etc/shadow 内的第 5 栏，多久需变更密码日数
PASS_MIN_DAYS   0        &lt;==/etc/shadow 内的第 4 栏，多久不可重新设置密码日数
PASS_MIN_LEN    5        &lt;==密码最短的字符长度，已被 pam 模块取代，失去效用！
PASS_WARN_AGE   7        &lt;==/etc/shadow 内的第 6 栏，过期前会警告的日数

UID_MIN          1000    &lt;==使用者最小的 UID，意即小于 1000 的 UID 为系统保留
UID_MAX         60000    &lt;==使用者能够用的最大 UID
SYS_UID_MIN       201    &lt;==保留给使用者自行设置的系统帐号最小值 UID
SYS_UID_MAX       999    &lt;==保留给使用者自行设置的系统帐号最大值 UID
GID_MIN          1000    &lt;==使用者自订群组的最小 GID，小于 1000 为系统保留
GID_MAX         60000    &lt;==使用者自订群组的最大 GID
SYS_GID_MIN       201    &lt;==保留给使用者自行设置的系统帐号最小值 GID
SYS_GID_MAX       999    &lt;==保留给使用者自行设置的系统帐号最大值 GID

CREATE_HOME     yes      &lt;==在不加 -M 及 -m 时，是否主动创建使用者主文件夹？
UMASK           077      &lt;==使用者主文件夹创建的 umask ，因此权限会是 700
USERGROUPS_ENAB yes      &lt;==使用 userdel 删除时，是否会删除初始群组
ENCRYPT_METHOD SHA512    &lt;==密码加密的机制使用的是 sha512 这一个机制！
```

这个文件规范的数据则是如下所示：

- mailbox 所在目录： 使用者的默认 mailbox 文件放置的目录在 /var/spool/mail，所以 vbird1 的 mailbox 就是在 /var/spool/mail/vbird1 啰！

- shadow 密码第 4, 5, 6 字段内容： 通过 PASS*MAX_DAYS 等等设置值来指定的！所以你知道为何默认的 /etc/shadow 内每一行都会有“ 0:99999:7 ”的存在了吗？^*^！不过要注意的是，由于目前我们登陆时改用 PAM 模块来进行密码检验，所以那个 PASS_MIN_LEN 是失效的！

- UID/GID 指定数值： 虽然 Linux 核心支持的帐号可高达 232 这么多个，不过一部主机要作出这么多帐号在管理上也是很麻烦的！ 所以在这里就针对 UID/GID 的范围进行规范就是了。上表中的 UID_MIN 指的就是可登陆系统的一般帐号的最小 UID ，至于 UID_MAX 则是最大 UID 之意。

  要注意的是，系统给予一个帐号 UID 时，他是 （1）先参考 UID_MIN 设置值取得最小数值； （2）由 /etc/passwd 搜寻最大的 UID 数值， 将 （1） 与 （2） 相比，找出最大的那个再加一就是新帐号的 UID 了。我们上面已经作出 UID 为 1500 的 vbird2 ， 如果再使用“ useradd vbird4 ”时，你猜 vbird4 的 UID 会是多少？答案是： 1501 。 所以中间的 1004~1499 的号码就空下来啦！

  而如果我是想要创建系统用的帐号，所以使用 useradd -r sysaccount 这个 -r 的选项时，就会找“比 201 大但比 1000 小的最大的 UID ”就是了。 ^_^

- 使用者主文件夹设置值： 为何我们系统默认会帮使用者创建主文件夹？就是这个“CREATE_HOME = yes”的设置值啦！这个设置值会让你在使用 useradd 时， 主动加入“ -m ”这个产生主文件夹的选项啊！如果不想要创建使用者主文件夹，就只能强制加上“ -M ”的选项在 useradd 指令执行时啦！至于创建主文件夹的权限设置呢？就通过 [umask](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#umask) 这个设置值啊！因为是 077 的默认设置，因此使用者主文件夹默认权限才会是“ drwx------ ”哩！

- 使用者删除与密码设置值： 使用“USERGROUPS_ENAB yes”这个设置值的功能是： 如果使用 userdel 去删除一个帐号时，且该帐号所属的初始群组已经没有人隶属于该群组了， 那么就删除掉该群组，举例来说，我们刚刚有创建 vbird4 这个帐号，他会主动创建 vbird4 这个群组。 若 vbird4 这个群组并没有其他帐号将他加入支持的情况下，若使用 userdel vbird4 时，该群组也会被删除的意思。 至于“ENCRYPT_METHOD SHA512”则表示使用 SHA512 来加密密码明文，而不使用旧式的 MD5。

现在你知道啦，使用 useradd 这支程序在创建 Linux 上的帐号时，至少会参考：

- /etc/default/useradd
- /etc/login.defs
- /etc/skel/*

这些文件，不过，最重要的其实是创建 /etc/passwd, /etc/shadow, /etc/group, /etc/gshadow 还有使用者主文件夹就是了～所以，如果你了解整个系统运行的状态，也是可以手动直接修改这几个文件就是了。 OK！帐号创建了，接下来处理一下使用者的密码吧！

- passwd

刚刚我们讲到了，使用 useradd 创建了帐号之后，在默认的情况下，该帐号是暂时被封锁的， 也就是说，该帐号是无法登陆的，你可以去瞧一瞧 /etc/shadow 内的第二个字段就晓得啰～ 那该如何是好？怕什么？直接给他设置新密码就好了嘛！对吧～设置密码就使用 passwd 啰！

```
[root@study ~]# passwd [--stdin] [帐号名称]  &lt;==所有人均可使用来改自己的密码
[root@study ~]# passwd [-l] [-u] [--stdin] [-S] \
&gt;  [-n 日数] [-x 日数] [-w 日数] [-i 日期] 帐号 &lt;==root 功能
选项与参数：
--stdin ：可以通过来自前一个管线的数据，作为密码输入，对 shell script 有帮助！
-l  ：是 Lock 的意思，会将 /etc/shadow 第二栏最前面加上 ! 使密码失效；
-u  ：与 -l 相对，是 Unlock 的意思！
-S  ：列出密码相关参数，亦即 shadow 文件内的大部分信息。
-n  ：后面接天数，shadow 的第 4 字段，多久不可修改密码天数
-x  ：后面接天数，shadow 的第 5 字段，多久内必须要更动密码
-w  ：后面接天数，shadow 的第 6 字段，密码过期前的警告天数
-i  ：后面接“日期”，shadow 的第 7 字段，密码失效日期

范例一：请 root 给予 vbird2 密码
[root@study ~]# passwd vbird2
Changing password for user vbird2.
New UNIX password: &lt;==这里直接输入新的密码，屏幕不会有任何反应
BAD PASSWORD: The password is shorter than 8 characters &lt;==密码太简单或过短的错误！
Retype new UNIX password:  &lt;==再输入一次同样的密码
passwd: all authentication tokens updated successfully.  &lt;==竟然还是成功修改了！
```

root 果然是最伟大的人物！当我们要给予使用者密码时，通过 root 来设置即可。 root 可以设置各式各样的密码，系统几乎一定会接受！所以您瞧瞧，如同上面的范例一，明明鸟哥输入的密码太短了， 但是系统依旧可接受 vbird2 这样的密码设置。这个是 root 帮忙设置的结果，那如果是使用者自己要改密码呢？ 包括 root 也是这样修改的喔！

```
范例二：用 vbird2 登陆后，修改 vbird2 自己的密码
[vbird2@study ~]$ passwd   &lt;==后面没有加帐号，就是改自己的密码！
Changing password for user vbird2.
Changing password for vbird2
（current） UNIX password: &lt;==这里输入“原有的旧密码”
New UNIX password: &lt;==这里输入新密码
BAD PASSWORD: The password is shorter than 8 characters &lt;==密码太短！不可以设置！重新想
New password:  &lt;==这里输入新想的密码
BAD PASSWORD: The password fails the dictionary check - it is based on a dictionary word
# 同样的，密码设置在字典里面找的到该字串，所以也是不建议！无法通过，再想新的！
New UNIX password: &lt;==这里再想个新的密码来输入吧
Retype new UNIX password: &lt;==通过密码验证！所以重复这个密码的输入
passwd: all authentication tokens updated successfully. &lt;==有无成功看关键字
```

passwd 的使用真的要很注意，尤其是 root 先生啊！鸟哥在课堂上每次讲到这里，说是要帮自己的一般帐号创建密码时， 有一小部分的学生就是会忘记加上帐号，结果就变成改变 root 自己的密码，最后.... root 密码就这样不见去！唉～ 要帮一般帐号创建密码需要使用“ passwd 帐号 ”的格式，使用“ passwd ”表示修改自己的密码！拜托！千万不要改错！

与 root 不同的是，一般帐号在更改密码时需要先输入自己的旧密码 （亦即 current 那一行），然后再输入新密码 （New 那一行）。 要注意的是，密码的规范是非常严格的，尤其新的 distributions 大多使用 PAM 模块来进行密码的检验，包括太短、 密码与帐号相同、密码为字典常见字串等，都会被 PAM 模块检查出来而拒绝修改密码，此时会再重复出现“ New ”这个关键字！ 那时请再想个新密码！若出现“ Retype ”才是你的密码被接受了！重复输入新密码并且看到“ successfully ”这个关键字时才是修改密码成功喔！

![鸟哥的图示](image/vbird_face-172241161145210.gif)

**Tips** 与一般使用者不同的是， root 并不需要知道旧密码就能够帮使用者或 root 自己创建新密码！ 但如此一来有困扰～就是如果你的亲密爱人老是告诉你“我的密码真难记，帮我设置简单一点的！”时， 千万不要妥协啊！这是为了系统安全...

为何使用者要设订自己的密码会这么麻烦啊？这是因为密码的安全性啦！如果密码设置太简单， 一些有心人士就能够很简单的猜到你的密码，如此一来人家就可能使用你的一般帐号登陆你的主机或使用其他主机资源， 对主机的维护会造成困扰的！所以新的 distributions 是使用较严格的 PAM 模块来管理密码，这个管理的机制写在 /etc/pam.d/passwd 当中。而该文件与密码有关的测试模块就是使用：pam_cracklib.so，这个模块会检验密码相关的信息， 并且取代 /etc/login.defs 内的 PASS_MIN_LEN 的设置啦！关于 PAM 我们在本章后面继续介绍，这里先谈一下， 理论上，你的密码最好符合如下要求：

- 密码不能与帐号相同；
- 密码尽量不要选用字典里面会出现的字串；
- 密码需要超过 8 个字符；
- 密码不要使用个人信息，如身份证、手机号码、其他电话号码等；
- 密码不要使用简单的关系式，如 1+1=2， Iamvbird 等；
- 密码尽量使用大小写字符、数字、特殊字符（$,_,-等）的组合。

为了方便系统管理，新版的 passwd 还加入了很多创意选项喔！鸟哥个人认为最好用的大概就是这个“ --stdin ”了！ 举例来说，你想要帮 vbird2 变更密码成为 abc543CC ，可以这样下达指令呢！

```
范例三：使用 standard input 创建用户的密码
[root@study ~]# echo "abc543CC" &#124; passwd --stdin vbird2
Changing password for user vbird2.
passwd: all authentication tokens updated successfully.
```

这个动作会直接更新使用者的密码而不用再次的手动输入！好处是方便处理，缺点是这个密码会保留在指令中， 未来若系统被攻破，人家可以在 /root/.bash_history 找到这个密码呢！所以这个动作通常仅用在 shell script 的大量创建使用者帐号当中！要注意的是，这个选项并不存在所有 distributions 版本中， 请使用 man passwd 确认你的 distribution 是否有支持此选项喔！

如果你想要让 vbird2 的密码具有相当的规则，举例来说你要让 vbird2 每 60 天需要变更密码， 密码过期后 10 天未使用就宣告帐号失效，那该如何处理？

```
范例四：管理 vbird2 的密码使具有 60 天变更、密码过期 10 天后帐号失效的设置
[root@study ~]# passwd -S vbird2
vbird2 PS 2015-07-20 0 99999 7 -1 （Password set, SHA512 crypt.）
# 上面说明密码创建时间 （2015-07-20）、0 最小天数、99999 变更天数、7 警告日数与密码不会失效 （-1）

[root@study ~]# passwd -x 60 -i 10 vbird2
[root@study ~]# passwd -S vbird2
vbird2 PS 2015-07-20 0 60 7 10 （Password set, SHA512 crypt.）
```

那如果我想要让某个帐号暂时无法使用密码登陆主机呢？举例来说， vbird2 这家伙最近老是胡乱在主机乱来， 所以我想要暂时让她无法登陆的话，最简单的方法就是让她的密码变成不合法 （shadow 第 2 字段长度变掉）！ 处理的方法就更简单的！

```
范例五：让 vbird2 的帐号失效，观察完毕后再让她失效
[root@study ~]# passwd -l vbird2
[root@study ~]# passwd -S vbird2
vbird2 LK 2015-07-20 0 60 7 10 （Password locked.）
# 嘿嘿！状态变成“ LK, Lock ”了啦！无法登陆喔！
[root@study ~]# grep vbird2 /etc/shadow
vbird2:!!$6$iWWO6T46$uYStdkB7QjcUpJaCLB.OOp...:16636:0:60:7:10::
# 其实只是在这里加上 !! 而已！

[root@study ~]# passwd -u vbird2
[root@study ~]# grep vbird2 /etc/shadow
vbird2:$6$iWWO6T46$uYStdkB7QjcUpJaCLB.OOp...:16636:0:60:7:10::
# 密码字段恢复正常！
```

是否很有趣啊！您可以自行管理一下你的帐号的密码相关参数喔！接下来让我们用更简单的方法来查阅密码参数喔！

- chage

除了使用 passwd -S 之外，有没有更详细的密码参数显示功能呢？有的！那就是 chage 了！他的用法如下：

```
[root@study ~]# chage [-ldEImMW] 帐号名
选项与参数：
-l ：列出该帐号的详细密码参数；
-d ：后面接日期，修改 shadow 第三字段（最近一次更改密码的日期），格式 YYYY-MM-DD
-E ：后面接日期，修改 shadow 第八字段（帐号失效日），格式 YYYY-MM-DD
-I ：后面接天数，修改 shadow 第七字段（密码失效日期）
-m ：后面接天数，修改 shadow 第四字段（密码最短保留天数）
-M ：后面接天数，修改 shadow 第五字段（密码多久需要进行变更）
-W ：后面接天数，修改 shadow 第六字段（密码过期前警告日期）

范例一：列出 vbird2 的详细密码参数
[root@study ~]# chage -l vbird2
Last password change                                    : Jul 20, 2015
Password expires                                        : Sep 18, 2015
Password inactive                                       : Sep 28, 2015
Account expires                                         : never
Minimum number of days between password change          : 0
Maximum number of days between password change          : 60
Number of days of warning before password expires       : 7
```

我们在 [passwd](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#passwd) 的介绍中谈到了处理 vbird2 这个帐号的密码属性流程，使用 passwd -S 却无法看到很清楚的说明。如果使用 chage 那可就明白多了！如上表所示，我们可以清楚的知道 vbird2 的详细参数呢！ 如果想要修改其他的设置值，就自己参考上面的选项，或者自行 man chage 一下吧！^_^

chage 有一个功能很不错喔！如果你想要让“使用者在第一次登陆时， 强制她们一定要更改密码后才能够使用系统资源”，可以利用如下的方法来处理的！

```
范例二：创建一个名为 agetest 的帐号，该帐号第一次登陆后使用默认密码，但必须要更改过密码后，
        使用新密码才能够登陆系统使用 bash 环境
[root@study ~]# useradd agetest
[root@study ~]# echo "agetest" &#124; passwd --stdin agetest
[root@study ~]# chage -d 0 agetest
[root@study ~]# chage -l agetest &#124; head -n 3
Last password change                : password must be changed
Password expires                    : password must be changed
Password inactive                   : password must be changed
# 此时此帐号的密码创建时间会被改为 1970/1/1 ，所以会有问题！

范例三：尝试以 agetest 登陆的情况
You are required to change your password immediately （root enforced）
WARNING: Your password has expired.
You must change your password now and login again!
Changing password for user agetest.
Changing password for agetest
（current） UNIX password:  &lt;==这个帐号被强制要求必须要改密码！
```

非常有趣吧！你会发现 agetest 这个帐号在第一次登陆时可以使用与帐号同名的密码登陆， 但登陆时就会被要求立刻更改密码，更改密码完成后就会被踢出系统。再次登陆时就能够使用新密码登陆了！ 这个功能对学校老师非常有帮助！因为我们不想要知道学生的密码，那么在初次上课时就使用与学号相同的帐号/密码给学生， 让她们登陆时自行设置她们的密码，如此一来就能够避免其他同学随意使用别人的帐号，也能够保证学生知道如何更改自己的密码！

- usermod

所谓这“人有失手，马有乱蹄”，您说是吧？所以啰，当然有的时候会“不小心手滑了一下”在 useradd 的时候加入了错误的设置数据。或者是，在使用 useradd 后，发现某些地方还可以进行细部修改。 此时，当然我们可以直接到 /etc/passwd 或 /etc/shadow 去修改相对应字段的数据， 不过，Linux 也有提供相关的指令让大家来进行帐号相关数据的微调呢～那就是 usermod 啰～

```
[root@study ~]# usermod [-cdegGlsuLU] username
选项与参数：
-c  ：后面接帐号的说明，即 /etc/passwd 第五栏的说明栏，可以加入一些帐号的说明。
-d  ：后面接帐号的主文件夹，即修改 /etc/passwd 的第六栏；
-e  ：后面接日期，格式是 YYYY-MM-DD 也就是在 /etc/shadow 内的第八个字段数据啦！
-f  ：后面接天数，为 shadow 的第七字段。
-g  ：后面接初始群组，修改 /etc/passwd 的第四个字段，亦即是 GID 的字段！
-G  ：后面接次要群组，修改这个使用者能够支持的群组，修改的是 /etc/group 啰～
-a  ：与 -G 合用，可“增加次要群组的支持”而非“设置”喔！
-l  ：后面接帐号名称。亦即是修改帐号名称， /etc/passwd 的第一栏！
-s  ：后面接 Shell 的实际文件，例如 /bin/bash 或 /bin/csh 等等。
-u  ：后面接 UID 数字啦！即 /etc/passwd 第三栏的数据；
-L  ：暂时将使用者的密码冻结，让他无法登陆。其实仅改 /etc/shadow 的密码栏。
-U  ：将 /etc/shadow 密码栏的 ! 拿掉，解冻啦！
```

如果你仔细的比对，会发现 usermod 的选项与 [useradd](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#useradd) 非常类似！ 这是因为 usermod 也是用来微调 useradd 增加的使用者参数嘛！不过 usermod 还是有新增的选项， 那就是 -L 与 -U ，不过这两个选项其实与 passwd 的 -l, -u 是相同的！而且也不见得会存在所有的 distribution 当中！接下来，让我们谈谈一些变更参数的实例吧！

```
范例一：修改使用者 vbird2 的说明栏，加上“VBird's test”的说明。
[root@study ~]# usermod -c "VBird's test" vbird2
[root@study ~]# grep vbird2 /etc/passwd
vbird2:x:1500:100:VBird's test:/home/vbird2:/bin/bash

范例二：使用者 vbird2 这个帐号在 2015/12/31 失效。
[root@study ~]# usermod -e "2015-12-31" vbird2
[root@study ~]# chage -l vbird2 &#124; grep 'Account expires'
Account expires                     : Dec 31, 2015

范例三：我们创建 vbird3 这个系统帐号时并没有给予主文件夹，请创建他的主文件夹
[root@study ~]# ll -d ~vbird3
ls: cannot access /home/vbird3: No such file or directory  &lt;==确认一下，确实没有主文件夹的存在！
[root@study ~]# cp -a /etc/skel /home/vbird3
[root@study ~]# chown -R vbird3:vbird3 /home/vbird3
[root@study ~]# chmod 700 /home/vbird3
[root@study ~]# ll -a ~vbird3
drwx------.  3 vbird3 vbird3   74 May  4 17:51 .   &lt;==使用者主文件夹权限
drwxr-xr-x. 10 root   root   4096 Jul 20 22:51 ..
-rw-r--r--.  1 vbird3 vbird3   18 Mar  6 06:06 .bash_logout
-rw-r--r--.  1 vbird3 vbird3  193 Mar  6 06:06 .bash_profile
-rw-r--r--.  1 vbird3 vbird3  231 Mar  6 06:06 .bashrc
drwxr-xr-x.  4 vbird3 vbird3   37 May  4 17:51 .mozilla
# 使用 chown -R 是为了连同主文件夹下面的使用者/群组属性都一起变更的意思；
# 使用 chmod 没有 -R ，是因为我们仅要修改目录的权限而非内部文件的权限！
```

- userdel

这个功能就太简单了，目的在删除使用者的相关数据，而使用者的数据有：

- 使用者帐号/密码相关参数：/etc/passwd, /etc/shadow
- 使用者群组相关参数：/etc/group, /etc/gshadow
- 使用者个人文件数据： /home/username, /var/spool/mail/username..

整个指令的语法非常简单：

```
[root@study ~]# userdel [-r] username
选项与参数：
-r  ：连同使用者的主文件夹也一起删除

范例一：删除 vbird2 ，连同主文件夹一起删除
[root@study ~]# userdel -r vbird2
```

这个指令下达的时候要小心了！通常我们要移除一个帐号的时候，你可以手动的将 /etc/passwd 与 /etc/shadow 里头的该帐号取消即可！一般而言，如果该帐号只是“暂时不启用”的话，那么将 /etc/shadow 里头帐号失效日期 （第八字段） 设置为 0 就可以让该帐号无法使用，但是所有跟该帐号相关的数据都会留下来！ 使用 userdel 的时机通常是“你真的确定不要让该用户在主机上面使用任何数据了！”

另外，其实使用者如果在系统上面操作过一阵子了，那么该使用者其实在系统内可能会含有其他文件的。 举例来说，他的邮件信箱 （mailbox） 或者是[例行性工作调度 （crontab, 十五章）](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 之类的文件。 所以，如果想要完整的将某个帐号完整的移除，最好可以在下达 userdel -r username 之前， 先以“ find / -user username ”查出整个系统内属于 username 的文件，然后再加以删除吧！



### 13.2.2 使用者功能

一般用户可以使用以下命令查询和修改自己的信息。

#### id

查询用户 UID/GID 信息。

语法：

```
id [username]
```

**示例：查询 root 的 UID/GID 信息**

```
id
```

#### finger

查询用户信息。

语法：

```
finger [-s] username
```

**示例：查询 vbird1 的信息**

```
finger vbird1
```

#### chfn

修改用户信息。

语法：

```
chfn [-foph] [帐号名]
```

常用选项：

- `-f`：全名。
- `-o`：办公室房间号码。
- `-p`：办公室电话。
- `-h`：家里电话。

**示例：修改 vbird1 的信息**

```
chfn
```

#### chsh

修改用户的 shell。

语法：

```
chsh [-ls]
```

常用选项：

- `-l`：列出合法的 shell。
- `-s`：设置 shell。

**示例：设置 vbird1 的 shell 为 csh**

```
chsh -s /bin/csh vbird1
```

#### 原文：

不论是 useradd/usermod/userdel ，那都是系统管理员所能够使用的指令， 如果我是一般身份使用者，那么我是否除了密码之外，就无法更改其他的数据呢？ 当然不是啦！这里我们介绍几个一般身份使用者常用的帐号数据变更与查询指令啰！

- id

id 这个指令则可以查询某人或自己的相关 UID/GID 等等的信息，他的参数也不少，不过，都不需要记～反正使用 id 就全部都列出啰！ 另外，也回想一下，我们在前一章谈到的循环时，就有用过这个指令喔！ ^_^

```
[root@study ~]# id [username]

范例一：查阅 root 自己的相关 ID 信息！
[root@study ~]# id
uid=0（root） gid=0（root） groups=0（root） context=unconfined_u:unconfined_r:unconfined_t:
s0-s0:c0.c1023
# 上面信息其实是同一行的数据！包括会显示 UID/GID 以及支持的所有群组！
# 至于后面那个 context=... 则是 SELinux 的内容，先不要理会他！

范例二：查阅一下 vbird1 吧～
[root@study ~]# id vbird1
uid=1003（vbird1） gid=1004（vbird1） groups=1004（vbird1）

[root@study ~]# id vbird100
id: vbird100: No such user  &lt;== id 这个指令也可以用来判断系统上面有无某帐号！
```

- finger

finger 的中文字面意义是：“手指”或者是“指纹”的意思。这个 finger 可以查阅很多使用者相关的信息喔！ 大部分都是在 /etc/passwd 这个文件里面的信息啦！不过，这个指令有点危险，所以新的版本中已经默认不安装这个软件！ 好啦！现在继续来安装软件先～记得[第九章 dos2unix](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#tips_dos) 的安装方式！ 假设你已经将光驱或光盘镜像文件挂载在 /mnt 下面了，所以：

```
[root@study ~]# df -hT /mnt
Filesystem     Type     Size  Used Avail Use% Mounted on
/dev/sr0       iso9660  7.1G  7.1G     0 100% /mnt    # 先确定是有挂载光盘的啦！

[root@study ~]# rpm -ivh /mnt/Packages/finger-[0-9]*
```

我们就先来检查检查使用者信息吧！

```
[root@study ~]# finger [-s] username
选项与参数：
-s  ：仅列出使用者的帐号、全名、终端机代号与登陆时间等等；
-m  ：列出与后面接的帐号相同者，而不是利用部分比对 （包括全名部分）

范例一：观察 vbird1 的使用者相关帐号属性
[root@study ~]# finger vbird1
Login: vbird1                           Name:
Directory: /home/vbird1                 Shell: /bin/bash
Never logged in.
No mail.
No Plan.
```

由于 finger 类似指纹的功能，他会将使用者的相关属性列出来！如上表所示，其实他列出来的几乎都是 /etc/passwd 文件里面的东西。列出的信息说明如下：

- Login：为使用者帐号，亦即 /etc/passwd 内的第一字段；
- Name：为全名，亦即 /etc/passwd 内的第五字段（或称为注解）；
- Directory：就是主文件夹了；
- Shell：就是使用的 Shell 文件所在；
- Never logged in.：figner 还会调查使用者登陆主机的情况喔！
- No mail.：调查 /var/spool/mail 当中的信箱数据；
- No Plan.：调查 ~vbird1/.plan 文件，并将该文件取出来说明！

不过是否能够查阅到 Mail 与 Plan 则与权限有关了！因为 Mail / Plan 都是与使用者自己的权限设置有关， root 当然可以查阅到使用者的这些信息，但是 vbird1 就不见得能够查到 vbird3 的信息， 因为 /var/spool/mail/vbird3 与 /home/vbird3/ 的权限分别是 660, 700 ，那 vbird1 当然就无法查阅的到！ 这样解释可以理解吧？此外，我们可以创建自己想要执行的预定计划，当然，最多是给自己看的！可以这样做：

```
范例二：利用 vbird1 创建自己的计划档
[vbird1@study ~]$ echo "I will study Linux during this year." &gt; ~/.plan
[vbird1@study ~]$ finger vbird1
Login: vbird1                           Name:
Directory: /home/vbird1                 Shell: /bin/bash
Last login Mon Jul 20 23:06 （CST） on pts/0
No mail.
Plan:
I will study Linux during this year.

范例三：找出目前在系统上面登陆的使用者与登陆时间
[vbird1@study ~]$ finger
Login     Name       Tty      Idle  Login Time   Office     Office Phone   Host
dmtsai    dmtsai     tty2      11d  Jul  7 23:07
dmtsai    dmtsai     pts/0          Jul 20 17:59
```

在范例三当中，我们发现输出的信息还会有 Office, Office Phone 等信息，那这些信息要如何记录呢？ 下面我们会介绍 chfn 这个指令！来看看如何修改使用者的 finger 数据吧！

- chfn

chfn 有点像是： change finger 的意思！这玩意的使用方法如下：

```
[root@study ~]# chfn [-foph] [帐号名]
选项与参数：
-f  ：后面接完整的大名；
-o  ：您办公室的房间号码；
-p  ：办公室的电话号码；
-h  ：家里的电话号码！

范例一：vbird1 自己更改一下自己的相关信息！
[vbird1@study ~]$ chfn
Changing finger information for vbird1.
Name []: VBird Tsai test         &lt;==输入你想要呈现的全名
Office []: DIC in KSU            &lt;==办公室号码
Office Phone []: 06-2727175#356  &lt;==办公室电话
Home Phone []: 06-1234567        &lt;==家里电话号码

Password:  &lt;==确认身份，所以输入自己的密码
Finger information changed.

[vbird1@study ~]$ grep vbird1 /etc/passwd
vbird1:x:1003:1004:VBird Tsai test,DIC in KSU,06-2727175#356,06-1234567:/home/vbird1:/bin/bash
# 其实就是改到第五个字段，该字段里面用多个“ , ”分隔就是了！

[vbird1@study ~]$ finger vbird1
Login: vbird1                           Name: VBird Tsai test
Directory: /home/vbird1                 Shell: /bin/bash
Office: DIC in KSU, 06-2727175#356      Home Phone: 06-1234567
Last login Mon Jul 20 23:12 （CST） on pts/0
No mail.
Plan:
I will study Linux during this year.
# 就是上面特殊字体呈现的那些地方是由 chfn 所修改出来的！
```

这个指令说实在的，除非是你的主机有很多的用户，否则倒真是用不着这个程序！这就有点像是 bbs 里头更改你“个人属性”的那一个数据啦！不过还是可以自己玩一玩！尤其是用来提醒自己相关数据啦！ ^_^

- chsh

这就是 change shell 的简写！使用方法就更简单了！

```
[vbird1@study ~]$ chsh [-ls]
选项与参数：
-l  ：列出目前系统上面可用的 shell ，其实就是 /etc/shells 的内容！
-s  ：设置修改自己的 Shell 啰

范例一：用 vbird1 的身份列出系统上所有合法的 shell，并且指定 csh 为自己的 shell
[vbird1@study ~]$ chsh -l
/bin/sh
/bin/bash
/sbin/nologin   &lt;==所谓：合法不可登陆的 Shell 就是这玩意！
/usr/bin/sh
/usr/bin/bash
/usr/sbin/nologin
/bin/tcsh
/bin/csh        &lt;==这就是 C shell 啦！
# 其实上面的信息就是我们在 [bash](../Text/index.html) 中谈到的 /etc/shells 啦！

[vbird1@study ~]$ chsh -s /bin/csh; grep vbird1 /etc/passwd
Changing shell for vbird1.
Password:  &lt;==确认身份，请输入 vbird1 的密码
Shell changed.
vbird1:x:1003:1004:VBird Tsai test,DIC in KSU,06-2727175#356,06-1234567:/home/vbird1:/bin/csh

[vbird1@study ~]$ chsh -s /bin/bash
# 测试完毕后，立刻改回来！

[vbird1@study ~]$ ll $（which chsh）
-rws--x--x. 1 root root 23856 Mar  6 13:59 /bin/chsh
```

不论是 chfn 与 chsh ，都是能够让一般使用者修改 /etc/passwd 这个系统文件的！所以你猜猜，这两个文件的权限是什么？ 一定是 [SUID](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#suid) 的功能啦！看到这里，想到前面！ 这就是 Linux 的学习方法～ ^_^



### 13.2.3 新增与移除群组

管理群组包括新增、修改和删除群组。

#### groupadd

新增群组。

语法：

```
groupadd [-g gid] [-r] 群组名称
```

常用选项：

- `-g`：指定 GID。
- `-r`：创建系统群组。

**示例：新增群组 group1**

```
groupadd group1
```

#### groupmod

修改群组。

语法：

```
groupmod [-g gid] [-n group_name] 群组名
```

常用选项：

- `-g`：修改 GID。
- `-n`：修改群组名称。

**示例：修改群组名为 mygroup，GID 为 201**

```
groupmod -g 201 -n mygroup group1
```

#### groupdel

删除群组。

语法：

```
groupdel 群组名称
```

**示例：删除群组 mygroup**

```
groupdel mygroup
```

#### gpasswd

设置群组管理员和管理群组成员。

语法（管理员）：

```
gpasswd [-A user1,...] [-M user3,...] 群组名称
gpasswd [-rR] 群组名称
```

常用选项：

- `-A`：设置群组管理员。
- `-M`：添加群组成员。
- `-r`：移除群组密码。
- `-R`：使群组密码失效。

语法（群组管理员）：

```
gpasswd [-ad] user 群组名称
```

常用选项：

- `-a`：添加群组成员。
- `-d`：移除群组成员。

**示例：设置 vbird1 为群组管理员，添加成员 vbird1 和 vbird3**

```
bash复制代码groupadd testgroup
gpasswd testgroup
gpasswd -A vbird1 testgroup
gpasswd -a vbird1 testgroup
gpasswd -a vbird3 testgroup
```

#### 原文：

OK！了解了帐号的新增、删除、更动与查询后，再来我们可以聊一聊群组的相关内容了。 基本上，群组的内容都与这两个文件有关：/etc/group, /etc/gshadow。 群组的内容其实很简单，都是上面两个文件的新增、修改与移除而已， 不过，如果再加上有效群组的概念，那么 newgrp 与 gpasswd 则不可不知呢！

- groupadd

```
[root@study ~]# groupadd [-g gid] [-r] 群组名称
选项与参数：
-g  ：后面接某个特定的 GID ，用来直接给予某个 GID ～
-r  ：创建系统群组啦！与 /etc/login.defs 内的 GID_MIN 有关。

范例一：新建一个群组，名称为 group1
[root@study ~]# groupadd group1
[root@study ~]# grep group1 /etc/group /etc/gshadow
/etc/group:group1:x:1503:
/etc/gshadow:group1:!::
# 群组的 GID 也是会由 1000 以上最大 GID+1 来决定！
```

曾经有某些版本的教育训练手册谈到，为了让使用者的 UID/GID 成对，她们建议新建的与使用者私有群组无关的其他群组时，使用小于 1000 以下的 GID 为宜。 也就是说，如果要创建群组的话，最好能够使用“ groupadd -r 群组名”的方式来创建啦！ 不过，这见仁见智啦！看你自己的抉择啰！

- groupmod

跟 [usermod](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#usermod) 类似的，这个指令仅是在进行 group 相关参数的修改而已。

```
[root@study ~]# groupmod [-g gid] [-n group_name] 群组名
选项与参数：
-g  ：修改既有的 GID 数字；
-n  ：修改既有的群组名称

范例一：将刚刚上个指令创建的 group1 名称改为 mygroup ， GID 为 201
[root@study ~]# groupmod -g 201 -n mygroup group1
[root@study ~]# grep mygroup /etc/group /etc/gshadow
/etc/group:mygroup:x:201:
/etc/gshadow:mygroup:!::
```

不过，还是那句老话，不要随意的更动 GID ，容易造成系统资源的错乱喔！

- groupdel

呼呼！ groupdel 自然就是在删除群组的啰～用法很简单：

```
[root@study ~]# groupdel [groupname]

范例一：将刚刚的 mygroup 删除！
[root@study ~]# groupdel mygroup

范例二：若要删除 vbird1 这个群组的话？
[root@study ~]# groupdel vbird1
groupdel: cannot remove the primary group of user 'vbird1'
```

为什么 mygroup 可以删除，但是 vbird1 就不能删除呢？原因很简单，“有某个帐号 （/etc/passwd） 的 initial group 使用该群组！” 如果查阅一下，你会发现在 /etc/passwd 内的 vbird1 第四栏的 GID 就是 /etc/group 内的 vbird1 那个群组的 GID ，所以啰，当然无法删除～否则 vbird1 这个使用者登陆系统后， 就会找不到 GID ，那可是会造成很大的困扰的！那么如果硬要删除 vbird1 这个群组呢？ 你“必须要确认 /etc/passwd 内的帐号没有任何人使用该群组作为 initial group ”才行喔！所以，你可以：

- 修改 vbird1 的 GID ，或者是：
- 删除 vbird1 这个使用者。
- gpasswd：群组管理员功能

如果系统管理员太忙碌了，导致某些帐号想要加入某个专案时找不到人帮忙！这个时候可以创建“群组管理员”喔！ 什么是群组管理员呢？就是让某个群组具有一个管理员，这个群组管理员可以管理哪些帐号可以加入/移出该群组！ 那要如何“创建一个群组管理员”呢？就得要通过 gpasswd 啰！

```
# 关于系统管理员（root）做的动作：
[root@study ~]# gpasswd groupname
[root@study ~]# gpasswd [-A user1,...] [-M user3,...] groupname
[root@study ~]# gpasswd [-rR] groupname
选项与参数：
    ：若没有任何参数时，表示给予 groupname 一个密码（/etc/gshadow）
-A  ：将 groupname 的主控权交由后面的使用者管理（该群组的管理员）
-M  ：将某些帐号加入这个群组当中！
-r  ：将 groupname 的密码移除
-R  ：让 groupname 的密码栏失效

# 关于群组管理员（Group administrator）做的动作：
[someone@study ~]$ gpasswd [-ad] user groupname
选项与参数：
-a  ：将某位使用者加入到 groupname 这个群组当中！
-d  ：将某位使用者移除出 groupname 这个群组当中。

范例一：创建一个新群组，名称为 testgroup 且群组交由 vbird1 管理：
[root@study ~]# groupadd testgroup  &lt;==先创建群组
[root@study ~]# gpasswd testgroup   &lt;==给这个群组一个密码吧！
Changing the password for group testgroup
New Password:
Re-enter new password:
# 输入两次密码就对了！
[root@study ~]# gpasswd -A vbird1 testgroup  &lt;==加入群组管理员为 vbird1
[root@study ~]# grep testgroup /etc/group /etc/gshadow
/etc/group:testgroup:x:1503:
/etc/gshadow:testgroup:$6$MnmChP3D$mrUn.Vo.buDjObMm8F2emTkvGSeuWikhRzaKHxpJ...:vbird1:
# 很有趣吧！此时 vbird1 则拥有 testgroup 的主控权喔！身份有点像板主啦！

范例二：以 vbird1 登陆系统，并且让他加入 vbird1, vbird3 成为 testgroup 成员
[vbird1@study ~]$ id
uid=1003（vbird1） gid=1004（vbird1） groups=1004（vbird1） ...
# 看得出来，vbird1 尚未加入 testgroup 群组喔！

[vbird1@study ~]$ gpasswd -a vbird1 testgroup
[vbird1@study ~]$ gpasswd -a vbird3 testgroup
[vbird1@study ~]$ grep testgroup /etc/group
testgroup:x:1503:vbird1,vbird3
```

很有趣的一个小实验吧！我们可以让 testgroup 成为一个可以公开的群组，然后创建起群组管理员， 群组管理员可以有多个。在这个案例中，我将 vbird1 设置为 testgroup 的群组管理员，所以 vbird1 就可以自行增加群组成员啰～呼呼！然后，该群组成员就能够使用 [newgrp](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#newgrp) 啰～



### 13.2.5 使用外部身份认证系统（原文）

在谈 ACL 之前，我们再来谈一个概念性的操作～因为我们目前没有服务器可供练习....

有时候，除了本机的帐号之外，我们可能还会使用到其他外部的身份验证服务器所提供的验证身份的功能！举例来说， windows 下面有个很有名的身份验证系统，称为 Active Directory （AD）的东西，还有 Linux 为了提供不同主机使用同一组帐号密码， 也会使用到 LDAP, NIS 等服务器提供的身份验证等等！

如果你的 Linux 主机要使用到上面提到的这些外部身份验证系统时，可能就得要额外的设置一些数据了！ 为了简化使用者的操作流程，所以 CentOS 提供一只名为 authconfig-tui 的指令给我们参考，这个指令的执行结果如下：

（图片不存在）

图13.2.1、使用外部身份验证服务器的方式

你可以在该画面中使用 [tab] 按钮在各个项目中间切换，不过，因为我们没有适用的服务器可以测试，因此这里仅是提供一个参考的依据， 未来如果谈到服务器章节时，你要如果谈到服务器章节时，服器有印象，处理外部身份验证的方式可以通过 authconfig-tui 就好了！ 上图中最多可供操作的，大概仅有支持 MD5 这个早期的密码格式就是了！此外，不要随便将已经启用的项目 （上头有星号 ＊ 的项目） 取消喔！ 可能某些帐号会失效...



### 13.2.4 帐号管理实例

#### 任务一：创建多个用户

要求：

- `myuser1`，1st user，次要群组 mygroup1，可登录，密码 password
- `myuser2`，2nd user，次要群组 mygroup1，可登录，密码 password
- `myuser3`，3rd user，无额外支持，不可登录，密码 password

处理方法：

```
groupadd mygroup1
useradd -G mygroup1 -c "1st user" myuser1
useradd -G mygroup1 -c "2nd user" myuser2
useradd -c "3rd user" -s /sbin/nologin myuser3
echo "password" | passwd --stdin myuser1
echo "password" | passwd --stdin myuser2
echo "password" | passwd --stdin myuser3
```

#### 任务二：项目组用户管理

要求：创建项目组用户 pro1, pro2, pro3，共享项目目录 /srv/projecta

处理方法：

```
groupadd projecta
useradd -G projecta -c "projecta user" pro1
useradd -G projecta -c "projecta user" pro2
useradd -G projecta -c "projecta user" pro3
echo "password" | passwd --stdin pro1
echo "password" | passwd --stdin pro2
echo "password" | passwd --stdin pro3
mkdir /srv/projecta
chgrp projecta /srv/projecta
chmod 2770 /srv/projecta
```

以上是 Linux 系统中帐号和群组管理的详细说明和实例。通过这些命令和配置文件，可以灵活地管理系统中的用户和群组，确保系统的安全性和灵活性。

## 13.3 主机的细部权限规划：ACL 的使用

从第五章开始，我们就一直强调 Linux 的权限概念是非常重要的！ 但是传统的权限仅有三种身份（owner, group, others）搭配三种权限（r, w, x）而已，并没有办法单纯地针对某一个使用者或某一个群组来设置特定的权限需求，例如前一小节最后的那个任务！此时就得要使用 ACL 这个机制啦！这玩意挺有趣的，下面我们就来谈一谈：

### 13.3.1 什么是 ACL 与如何支持启动 ACL

ACL 是 Access Control List 的缩写，主要目的是在提供传统的 owner, group, others 的 read, write, execute 权限之外的细部权限设置。ACL 可以针对单一使用者、单一文件或目录进行 r, w, x 的权限规范，对于需要特殊权限的使用状况非常有帮助。

#### ACL 的控制项目

ACL 主要可以针对以下几个项目来控制权限：

- **使用者（user）**：可以针对使用者来设置权限；
- **群组（group）**：针对群组为对象来设置其权限；
- **默认属性（mask）**：针对在该目录下创建新文件/目录时，规范新数据的默认权限。

传统的 Linux 权限只能针对一个用户、一个群组及非此群组的其他人设置权限，无法针对单一用户或个人来设计权限。ACL 就是为了改变这个问题而出现的。

#### 启动 ACL

近年以来，Linux 系统对权限细部设置的需求增加，因此目前 ACL 几乎已经默认加入在所有常见的 Linux 文件系统的挂载参数中（ext2/ext3/ext4/xfs 等等）。可以通过以下命令检查系统是否支持 ACL：

```
dmesg | grep -i acl
```



#### 原文：

ACL 是 Access Control List 的缩写，主要的目的是在提供传统的 owner,group,others 的 read,write,execute 权限之外的细部权限设置。ACL 可以针对单一使用者，单一文件或目录来进行 r,w,x 的权限规范，对于需要特殊权限的使用状况非常有帮助。

那 ACL 主要可以针对哪些方面来控制权限呢？他主要可以针对几个项目：

- 使用者 （user）：可以针对使用者来设置权限；
- 群组 （group）：针对群组为对象来设置其权限；
- 默认属性 （mask）：还可以针对在该目录下在创建新文件/目录时，规范新数据的默认权限；

也就是说，如果你有一个目录，需要给一堆人使用，每个人或每个群组所需要的权限并不相同时，在过去，传统的 Linux 三种身份的三种权限是无法达到的， 因为基本上，传统的 Linux 权限只能针对一个用户、一个群组及非此群组的其他人设置权限而已，无法针对单一用户或个人来设计权限。 而 ACL 就是为了要改变这个问题啊！好了，稍微了解之后，再来看看如何让你的文件系统可以支持 ACL 吧！

- 如何启动 ACL

事实上，原本 ACL 是 unix-like 操作系统的额外支持项目，但因为近年以来 Linux 系统对权限细部设置的热切需求， 因此目前 ACL 几乎已经默认加入在所有常见的 Linux 文件系统的挂载参数中 （ext2/ext3/ext4/xfs等等）！所以你无须进行任何动作， ACL 就可以被你使用啰！不过，如果你不放心系统是否真的有支持 ACL 的话，那么就来检查一下核心挂载时显示的信息吧！

```
[root@study ~]# dmesg &#124; grep -i acl
[    0.330377] systemd[1]: systemd 208 running in system mode. （+PAM +LIBWRAP +AUDIT
+SELINUX +IMA +SYSVINIT +LIBCRYPTSETUP +GCRYPT +ACL +XZ）
[    0.878265] SGI XFS with ACLs, security attributes, large block/inode numbers, no
debug enabled
```

瞧！至少 xfs 已经支持这个 ACL 的功能啰！



#### 补充：

使用 `dmesg | grep -i acl` 命令可以过滤并查看内核消息中包含 "acl" 字样的所有行。这种操作通常用于检查与 ACL（访问控制列表）相关的系统消息。

##### 详细解释

- **`dmesg` 命令**： `dmesg` 命令用于显示内核环缓冲区（kernel ring buffer）中的消息，这些消息包括系统启动时生成的硬件检测信息、设备驱动加载信息和其他内核日志。
- **`|` 管道符**： 管道符号 `|` 将前一个命令的输出作为下一个命令的输入。在这个例子中，`dmesg` 的输出被传递给 `grep` 命令。
- **`grep` 命令**： `grep` 是一个强大的文本搜索工具，用于在输入文本中查找匹配的模式。
- **`-i` 选项**： `-i` 选项告诉 `grep` 忽略大小写。这意味着 `grep` 会匹配 "acl"、"ACL"、"Acl" 等不同大小写的形式。
- **`acl`**： 这是要搜索的关键字。在内核消息中，"acl" 可能与访问控制列表（Access Control List）相关。

##### 命令的作用

当你运行 `dmesg | grep -i acl` 时，系统会执行以下操作：

1. **`dmesg`**：生成当前内核环缓冲区中的所有消息。
2. **`|`**：将 `dmesg` 的输出传递给 `grep`。
3. **`grep -i acl`**：从 `dmesg` 的输出中过滤出所有包含 "acl"（不区分大小写）的行。

##### 示例

假设你的系统内核消息中包含以下几行：

```
[    0.000000] Linux version 5.4.0-42-generic (buildd@lgw01-amd64-021) (gcc version 9.3.0 (Ubuntu 9.3.0-10ubuntu2)) #46-Ubuntu SMP Fri Jul 10 00:24:02 UTC 2020 (Ubuntu 5.4.0-42.46-generic 5.4.44)
[    1.234567] EXT4-fs (sda1): mounted filesystem with ordered data mode. Opts: acl,user_xattr
[    2.345678] audit: type=1400 audit(1598232345.234:2): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/sbin/ntpd" pid=432 comm="apparmor_parser"
[    3.456789] EXT4-fs (sdb1): mounted filesystem with ordered data mode. Opts: user_xattr,acl
```

运行 `dmesg | grep -i acl` 命令后，输出将是：

```
[    1.234567] EXT4-fs (sda1): mounted filesystem with ordered data mode. Opts: acl,user_xattr
[    3.456789] EXT4-fs (sdb1): mounted filesystem with ordered data mode. Opts: user_xattr,acl
```

##### 可能的用途

1. **检查文件系统选项**： 当挂载文件系统时，如果启用了 ACL 支持，相关消息会记录在内核日志中。你可以使用 `dmesg | grep -i acl` 检查文件系统是否启用了 ACL 选项。
2. **调试 ACL 相关问题**： 如果你在配置或使用 ACL 时遇到问题，查看内核日志可能会提供有用的信息。
3. **系统审计**： 在审计和安全检查中，查看 ACL 相关的内核消息有助于确认系统的安全配置。

##### 总结

- **`dmesg | grep -i acl`** 命令用于过滤并显示内核消息中包含 "acl" 字样的所有行。
- 这可以帮助你检查与 ACL 相关的系统日志消息。
- 使用 `grep` 的 `-i` 选项可以忽略大小写，使搜索更灵活。





### 13.3.2 ACL 的设置技巧：getfacl, setfacl

#### setfacl 指令

`setfacl` 用于设置 ACL，语法如下：

```
setfacl [-bkRd] [{-m|-x} acl参数] 目标文件名
```

常用选项：

- `-m`：设置 ACL 参数给文件使用，不可与 `-x` 合用；
- `-x`：删除 ACL 参数，不可与 `-m` 合用；
- `-b`：移除所有 ACL 设置参数；
- `-k`：移除默认的 ACL 参数；
- `-R`：递归设置 ACL，包括次目录；
- `-d`：设置默认 ACL 参数，只对目录有效。

**示例：针对特定使用者设置 ACL**

```
touch acl_test1
setfacl -m u:vbird1:rx acl_test1
ll acl_test1
```

权限部分会多出一个 `+` 号，表示该文件有 ACL 设置。

#### getfacl 指令

`getfacl` 用于查看文件/目录的 ACL 设置，语法如下：

```
getfacl filename
```

**示例：查看 acl_test1 的 ACL 设置**

```
getfacl acl_test1
```

显示内容：

```
# file: acl_test1
# owner: root
# group: root
user::rwx
user:vbird1:r-x
group::r--
mask::r-x
other::r--
```

#### 设置特定群组的权限

**示例：针对群组设置 ACL**

```
setfacl -m g:mygroup1:rx acl_test1
getfacl acl_test1
```

#### 设置有效权限（mask）

**示例：设置有效权限**

```
setfacl -m m:r acl_test1
getfacl acl_test1
```

显示内容中，`vbird1` 的权限会变为 `r`，因为有效权限仅为 `r`。

#### 原文：

好了，既然知道我们的 filesystem 有支持 ACL 之后，接下来该如何设置与观察 ACL 呢？ 很简单，利用这两个指令就可以了：

- getfacl：取得某个文件/目录的 ACL 设置项目；
- setfacl：设置某个目录/文件的 ACL 规范。

先让我们来瞧一瞧 setfacl 如何使用吧！

- setfacl 指令用法介绍及最简单的“ u:帐号:权限 ”设置

```
[root@study ~]# setfacl [-bkRd] [{-m&#124;-x} acl参数] 目标文件名
选项与参数：
-m ：设置后续的 acl 参数给文件使用，不可与 -x 合用；
-x ：删除后续的 acl 参数，不可与 -m 合用；
-b ：移除“所有的” ACL 设置参数；
-k ：移除“默认的” ACL 参数，关于所谓的“默认”参数于后续范例中介绍；
-R ：递回设置 acl ，亦即包括次目录都会被设置起来；
-d ：设置“默认 acl 参数”的意思！只对目录有效，在该目录新建的数据会引用此默认值
```

上面谈到的是 acl 的选项功能，那么如何设置 ACL 的特殊权限呢？特殊权限的设置方法有很多， 我们先来谈谈最常见的，就是针对单一使用者的设置方式：

```
# 1\. 针对特定使用者的方式：
# 设置规范：“ u:[使用者帐号列表]:[rwx] ”，例如针对 vbird1 的权限规范 rx ：
[root@study ~]# touch acl_test1
[root@study ~]# ll acl_test1
-rw-r--r--. 1 root root 0 Jul 21 17:33 acl_test1
[root@study ~]# setfacl -m u:vbird1:rx acl_test1
[root@study ~]# ll acl_test1
-rw-r-xr--+ 1 root root 0 Jul 21 17:33 acl_test1
# 权限部分多了个 + ，且与原本的权限 （644） 看起来差异很大！但要如何查阅呢？

[root@study ~]# setfacl -m u::rwx acl_test1
[root@study ~]# ll acl_test1
-rwxr-xr--+ 1 root root 0 Jul 21 17:33 acl_test1
# 设置值中的 u 后面无使用者列表，代表设置该文件拥有者，所以上面显示 root 的权限成为 rwx 了！
```

上述动作为最简单的 ACL 设置，利用“ u:使用者:权限 ”的方式来设置的啦！设置前请加上 -m 这个选项。 如果一个文件设置了 ACL 参数后，他的权限部分就会多出一个 + 号了！但是此时你看到的权限与实际权限可能就会有点误差！ 那要如何观察呢？就通过 getfacl 吧！

- getfacl 指令用法

```
[root@study ~]# getfacl filename
选项与参数：
getfacl 的选项几乎与 setfacl 相同！所以鸟哥这里就免去了选项的说明啊！

# 请列出刚刚我们设置的 acl_test1 的权限内容：
[root@study ~]# getfacl acl_test1
# file: acl_test1   &lt;==说明文档名而已！
# owner: root       &lt;==说明此文件的拥有者，亦即 ls -l 看到的第三使用者字段
# group: root       &lt;==此文件的所属群组，亦即 ls -l 看到的第四群组字段
user::rwx           &lt;==使用者列表栏是空的，代表文件拥有者的权限
user:vbird1:r-x     &lt;==针对 vbird1 的权限设置为 rx ，与拥有者并不同！
group::r--          &lt;==针对文件群组的权限设置仅有 r
mask::r-x           &lt;==此文件默认的有效权限 （mask）
other::r--          &lt;==其他人拥有的权限啰！
```

上面的数据非常容易查阅吧？显示的数据前面加上 # 的，代表这个文件的默认属性，包括文件名、文件拥有者与文件所属群组。 下面出现的 user, group, mask, other 则是属于不同使用者、群组与有效权限（mask）的设置值。 以上面的结果来看，我们刚刚设置的 vbird1 对于这个文件具有 r 与 x 的权限啦！这样看的懂吗？ 如果看的懂的话，接下来让我们在测试其他类型的 setfacl 设置吧！

- 特定的单一群组的权限设置：“ g:群组名:权限 ”

```
# 2\. 针对特定群组的方式：
# 设置规范：“ g:[群组列表]:[rwx] ”，例如针对 mygroup1 的权限规范 rx ：
[root@study ~]# setfacl -m g:mygroup1:rx acl_test1
[root@study ~]# getfacl acl_test1
# file: acl_test1
# owner: root
# group: root
user::rwx
user:vbird1:r-x
group::r--
group:mygroup1:r-x  &lt;==这里就是新增的部分！多了这个群组的权限设置！
mask::r-x
other::r--
```

- 针对有效权限设置：“ m:权限 ”

基本上，群组与使用者的设置并没有什么太大的差异啦！如上表所示，非常容易了解意义。不过，你应该会觉得奇怪的是， 那个 mask 是什么东西啊？其实他有点像是“有效权限”的意思！他的意义是： 使用者或群组所设置的权限必须要存在于 mask 的权限设置范围内才会生效，此即“有效权限 （effective permission）” 我们举个例子来看，如下所示：

```
# 3\. 针对有效权限 mask 的设置方式：
# 设置规范：“ m:[rwx] ”，例如针对刚刚的文件规范为仅有 r ：
[root@study ~]# setfacl -m m:r acl_test1
[root@study ~]# getfacl acl_test1
# file: acl_test1
# owner: root
# group: root
user::rwx
user:vbird1:r-x        #effective:r-- &lt;==vbird1+mask均存在者，仅有 r 而已，x 不会生效
group::r--
group:mygroup1:r-x     #effective:r--
mask::r--
other::r--
```

您瞧，vbird1 与 mask 的集合发现仅有 r 存在，因此 vbird1 仅具有 r 的权限而已，并不存在 x 权限！这就是 mask 的功能了！我们可以通过使用 mask 来规范最大允许的权限，就能够避免不小心开放某些权限给其他使用者或群组了。 不过，通常鸟哥都是将 mask 设置为 rwx 啦！然后再分别依据不同的使用者/群组去规范她们的权限就是了。

例题：将前一小节任务二中 /srv/projecta 这个目录，让 myuser1 可以进入查阅，但 myuser1 不具有修改的权力。答：由于 myuser1 是独立的使用者与群组，因此无法使用传统的 Linux 权限设置。此时使用 ACL 的设置如下：

```
# 1\. 先测试看看，使用 myuser1 能否进入该目录？
[myuser1@study ~]$ cd /srv/projecta
-bash: cd: /srv/projecta: Permission denied  &lt;==确实不可进入！

# 2\. 开始用 root 的身份来设置一下该目录的权限吧！
[root@study ~]# setfacl -m u:myuser1:rx /srv/projecta
[root@study ~]# getfacl /srv/projecta
# file: srv/projecta
# owner: root
# group: projecta
# flags: -s-
user::rwx
user:myuser1:r-x  &lt;==还是要看看有没有设置成功喔！
group::rwx
mask::rwx
other::---

# 3\. 还是得要使用 myuser1 去测试看看结果！
[myuser1@study ~]$ cd /srv/projecta
[myuser1@study projecta]$ ll -a
drwxrws---+ 2 root projecta 4096 Feb 27 11:29 .  &lt;==确实可以查询文件名
drwxr-xr-x  4 root root     4096 Feb 27 11:29 ..

[myuser1@study projecta]$ touch testing
touch: cannot touch `testing': Permission denied &lt;==确实不可以写入！
```

请注意，上述的 1, 3 步骤使用 myuser1 的身份，2步骤才是使用 root 去设置的！

上面的设置我们就完成了之前任务二的后续需求喔！这么简单呢！接下来让我们来测试一下，如果我用 root 或者是 pro1 的身份去 /srv/projecta 增加文件或目录时，该文件或目录是否能够具有 ACL 的设置？ 意思就是说，ACL 的权限设置是否能够被次目录所“继承？”先试看看：

```
[root@study ~]# cd /srv/projecta
[root@study ~]# touch abc1
[root@study ~]# mkdir abc2
[root@study ~]# ll -d abc*
-rw-r--r--. 1 root projecta 0 Jul 21 17:49 abc1
drwxr-sr-x. 2 root projecta 6 Jul 21 17:49 abc2
```

你可以明显的发现，权限后面都没有 + ，代表这个 acl 属性并没有继承喔！如果你想要让 acl 在目录下面的数据都有继承的功能，那就得如下这样做了！

- 使用默认权限设置目录未来文件的 ACL 权限继承“ d:[u|g]:[user|group]:权限 ”

```
# 4\. 针对默认权限的设置方式：
# 设置规范：“ d:[ug]:使用者列表:[rwx] ”

# 让 myuser1 在 /srv/projecta 下面一直具有 rx 的默认权限！
[root@study ~]# setfacl -m d:u:myuser1:rx /srv/projecta
[root@study ~]# getfacl /srv/projecta
# file: srv/projecta
# owner: root
# group: projecta
# flags: -s-
user::rwx
user:myuser1:r-x
group::rwx
mask::rwx
other::---
default:user::rwx
default:user:myuser1:r-x
default:group::rwx
default:mask::rwx
default:other::---

[root@study ~]# cd /srv/projecta
[root@study projecta]# touch zzz1
[root@study projecta]# mkdir zzz2
[root@study projecta]# ll -d zzz*
-rw-rw----+ 1 root projecta 0 Jul 21 17:50 zzz1
drwxrws---+ 2 root projecta 6 Jul 21 17:51 zzz2
# 看吧！确实有继承喔！然后我们使用 getfacl 再次确认看看！

[root@study projecta]# getfacl zzz2
# file: zzz2
# owner: root
# group: projecta
# flags: -s-
user::rwx
user:myuser1:r-x
group::rwx
mask::rwx
other::---
default:user::rwx
default:user:myuser1:r-x
default:group::rwx
default:mask::rwx
default:other::---
```

通过这个“针对目录来设置的默认 ACL 权限设置值”的项目，我们可以让这些属性继承到次目录下面呢！ 非常方便啊！那如果想要让 ACL 的属性全部消失又要如何处理？通过“ setfacl -b 文件名 ”即可啦！ 太简单了！鸟哥就不另外介绍了！请自行测试测试吧！

问：针对刚刚的 /srv/projecta 目录的权限设置中，我需要 1）取消 myuser1 的设置（连同默认值），以及 2）我不能让 pro3 这个用户使用该目录，亦即 pro3 在该目录下无任何权限， 该如何设置？答：取消全部的 ACL 设置可以使用 -b 来处理，但单一设置值的取消，就得要通过 -x 才行了！所以你应该这样作：

```
# 1.1 找到针对 myuser1 的设置值
[root@study ~]# getfacl /srv/projecta &#124; grep myuser1
user:myuser1:r-x
default:user:myuser1:r-x

# 1.2 针对每个设置值来处理，注意，取消某个帐号的 ACL 时，不需要加上权限项目！
[root@study ~]# setfacl -x u:myuser1 /srv/projecta
[root@study ~]# setfacl -x d:u:myuser1 /srv/projecta

# 2.1 开始让 pro3 这个用户无法使用该目录啰！
[root@study ~]# setfacl -m u:pro3:- /srv/projecta
```

只需要留意，当设置一个用户/群组没有任何权限的 ACL 语法中，在权限的字段不可留白，而是应该加上一个减号 （-） 才是正确的作法！



#### 原文：

好了，既然知道我们的 filesystem 有支持 ACL 之后，接下来该如何设置与观察 ACL 呢？ 很简单，利用这两个指令就可以了：

- getfacl：取得某个文件/目录的 ACL 设置项目；
- setfacl：设置某个目录/文件的 ACL 规范。

先让我们来瞧一瞧 setfacl 如何使用吧！

- setfacl 指令用法介绍及最简单的“ u:帐号:权限 ”设置

```
[root@study ~]# setfacl [-bkRd] [{-m&#124;-x} acl参数] 目标文件名
选项与参数：
-m ：设置后续的 acl 参数给文件使用，不可与 -x 合用；
-x ：删除后续的 acl 参数，不可与 -m 合用；
-b ：移除“所有的” ACL 设置参数；
-k ：移除“默认的” ACL 参数，关于所谓的“默认”参数于后续范例中介绍；
-R ：递回设置 acl ，亦即包括次目录都会被设置起来；
-d ：设置“默认 acl 参数”的意思！只对目录有效，在该目录新建的数据会引用此默认值
```

上面谈到的是 acl 的选项功能，那么如何设置 ACL 的特殊权限呢？特殊权限的设置方法有很多， 我们先来谈谈最常见的，就是针对单一使用者的设置方式：

```
# 1\. 针对特定使用者的方式：
# 设置规范：“ u:[使用者帐号列表]:[rwx] ”，例如针对 vbird1 的权限规范 rx ：
[root@study ~]# touch acl_test1
[root@study ~]# ll acl_test1
-rw-r--r--. 1 root root 0 Jul 21 17:33 acl_test1
[root@study ~]# setfacl -m u:vbird1:rx acl_test1
[root@study ~]# ll acl_test1
-rw-r-xr--+ 1 root root 0 Jul 21 17:33 acl_test1
# 权限部分多了个 + ，且与原本的权限 （644） 看起来差异很大！但要如何查阅呢？

[root@study ~]# setfacl -m u::rwx acl_test1
[root@study ~]# ll acl_test1
-rwxr-xr--+ 1 root root 0 Jul 21 17:33 acl_test1
# 设置值中的 u 后面无使用者列表，代表设置该文件拥有者，所以上面显示 root 的权限成为 rwx 了！
```

上述动作为最简单的 ACL 设置，利用“ u:使用者:权限 ”的方式来设置的啦！设置前请加上 -m 这个选项。 如果一个文件设置了 ACL 参数后，他的权限部分就会多出一个 + 号了！但是此时你看到的权限与实际权限可能就会有点误差！ 那要如何观察呢？就通过 getfacl 吧！

- getfacl 指令用法

```
[root@study ~]# getfacl filename
选项与参数：
getfacl 的选项几乎与 setfacl 相同！所以鸟哥这里就免去了选项的说明啊！

# 请列出刚刚我们设置的 acl_test1 的权限内容：
[root@study ~]# getfacl acl_test1
# file: acl_test1   &lt;==说明文档名而已！
# owner: root       &lt;==说明此文件的拥有者，亦即 ls -l 看到的第三使用者字段
# group: root       &lt;==此文件的所属群组，亦即 ls -l 看到的第四群组字段
user::rwx           &lt;==使用者列表栏是空的，代表文件拥有者的权限
user:vbird1:r-x     &lt;==针对 vbird1 的权限设置为 rx ，与拥有者并不同！
group::r--          &lt;==针对文件群组的权限设置仅有 r
mask::r-x           &lt;==此文件默认的有效权限 （mask）
other::r--          &lt;==其他人拥有的权限啰！
```

上面的数据非常容易查阅吧？显示的数据前面加上 # 的，代表这个文件的默认属性，包括文件名、文件拥有者与文件所属群组。 下面出现的 user, group, mask, other 则是属于不同使用者、群组与有效权限（mask）的设置值。 以上面的结果来看，我们刚刚设置的 vbird1 对于这个文件具有 r 与 x 的权限啦！这样看的懂吗？ 如果看的懂的话，接下来让我们在测试其他类型的 setfacl 设置吧！

- 特定的单一群组的权限设置：“ g:群组名:权限 ”

```
# 2\. 针对特定群组的方式：
# 设置规范：“ g:[群组列表]:[rwx] ”，例如针对 mygroup1 的权限规范 rx ：
[root@study ~]# setfacl -m g:mygroup1:rx acl_test1
[root@study ~]# getfacl acl_test1
# file: acl_test1
# owner: root
# group: root
user::rwx
user:vbird1:r-x
group::r--
group:mygroup1:r-x  &lt;==这里就是新增的部分！多了这个群组的权限设置！
mask::r-x
other::r--
```

- 针对有效权限设置：“ m:权限 ”

基本上，群组与使用者的设置并没有什么太大的差异啦！如上表所示，非常容易了解意义。不过，你应该会觉得奇怪的是， 那个 mask 是什么东西啊？其实他有点像是“有效权限”的意思！他的意义是： 使用者或群组所设置的权限必须要存在于 mask 的权限设置范围内才会生效，此即“有效权限 （effective permission）” 我们举个例子来看，如下所示：

```
# 3\. 针对有效权限 mask 的设置方式：
# 设置规范：“ m:[rwx] ”，例如针对刚刚的文件规范为仅有 r ：
[root@study ~]# setfacl -m m:r acl_test1
[root@study ~]# getfacl acl_test1
# file: acl_test1
# owner: root
# group: root
user::rwx
user:vbird1:r-x        #effective:r-- &lt;==vbird1+mask均存在者，仅有 r 而已，x 不会生效
group::r--
group:mygroup1:r-x     #effective:r--
mask::r--
other::r--
```

您瞧，vbird1 与 mask 的集合发现仅有 r 存在，因此 vbird1 仅具有 r 的权限而已，并不存在 x 权限！这就是 mask 的功能了！我们可以通过使用 mask 来规范最大允许的权限，就能够避免不小心开放某些权限给其他使用者或群组了。 不过，通常鸟哥都是将 mask 设置为 rwx 啦！然后再分别依据不同的使用者/群组去规范她们的权限就是了。

例题：将前一小节任务二中 /srv/projecta 这个目录，让 myuser1 可以进入查阅，但 myuser1 不具有修改的权力。答：由于 myuser1 是独立的使用者与群组，因此无法使用传统的 Linux 权限设置。此时使用 ACL 的设置如下：

```
# 1\. 先测试看看，使用 myuser1 能否进入该目录？
[myuser1@study ~]$ cd /srv/projecta
-bash: cd: /srv/projecta: Permission denied  &lt;==确实不可进入！

# 2\. 开始用 root 的身份来设置一下该目录的权限吧！
[root@study ~]# setfacl -m u:myuser1:rx /srv/projecta
[root@study ~]# getfacl /srv/projecta
# file: srv/projecta
# owner: root
# group: projecta
# flags: -s-
user::rwx
user:myuser1:r-x  &lt;==还是要看看有没有设置成功喔！
group::rwx
mask::rwx
other::---

# 3\. 还是得要使用 myuser1 去测试看看结果！
[myuser1@study ~]$ cd /srv/projecta
[myuser1@study projecta]$ ll -a
drwxrws---+ 2 root projecta 4096 Feb 27 11:29 .  &lt;==确实可以查询文件名
drwxr-xr-x  4 root root     4096 Feb 27 11:29 ..

[myuser1@study projecta]$ touch testing
touch: cannot touch `testing': Permission denied &lt;==确实不可以写入！
```

请注意，上述的 1, 3 步骤使用 myuser1 的身份，2步骤才是使用 root 去设置的！

上面的设置我们就完成了之前任务二的后续需求喔！这么简单呢！接下来让我们来测试一下，如果我用 root 或者是 pro1 的身份去 /srv/projecta 增加文件或目录时，该文件或目录是否能够具有 ACL 的设置？ 意思就是说，ACL 的权限设置是否能够被次目录所“继承？”先试看看：

```
[root@study ~]# cd /srv/projecta
[root@study ~]# touch abc1
[root@study ~]# mkdir abc2
[root@study ~]# ll -d abc*
-rw-r--r--. 1 root projecta 0 Jul 21 17:49 abc1
drwxr-sr-x. 2 root projecta 6 Jul 21 17:49 abc2
```

你可以明显的发现，权限后面都没有 + ，代表这个 acl 属性并没有继承喔！如果你想要让 acl 在目录下面的数据都有继承的功能，那就得如下这样做了！

- 使用默认权限设置目录未来文件的 ACL 权限继承“ d:[u|g]:[user|group]:权限 ”

```
# 4\. 针对默认权限的设置方式：
# 设置规范：“ d:[ug]:使用者列表:[rwx] ”

# 让 myuser1 在 /srv/projecta 下面一直具有 rx 的默认权限！
[root@study ~]# setfacl -m d:u:myuser1:rx /srv/projecta
[root@study ~]# getfacl /srv/projecta
# file: srv/projecta
# owner: root
# group: projecta
# flags: -s-
user::rwx
user:myuser1:r-x
group::rwx
mask::rwx
other::---
default:user::rwx
default:user:myuser1:r-x
default:group::rwx
default:mask::rwx
default:other::---

[root@study ~]# cd /srv/projecta
[root@study projecta]# touch zzz1
[root@study projecta]# mkdir zzz2
[root@study projecta]# ll -d zzz*
-rw-rw----+ 1 root projecta 0 Jul 21 17:50 zzz1
drwxrws---+ 2 root projecta 6 Jul 21 17:51 zzz2
# 看吧！确实有继承喔！然后我们使用 getfacl 再次确认看看！

[root@study projecta]# getfacl zzz2
# file: zzz2
# owner: root
# group: projecta
# flags: -s-
user::rwx
user:myuser1:r-x
group::rwx
mask::rwx
other::---
default:user::rwx
default:user:myuser1:r-x
default:group::rwx
default:mask::rwx
default:other::---
```

通过这个“针对目录来设置的默认 ACL 权限设置值”的项目，我们可以让这些属性继承到次目录下面呢！ 非常方便啊！那如果想要让 ACL 的属性全部消失又要如何处理？通过“ setfacl -b 文件名 ”即可啦！ 太简单了！鸟哥就不另外介绍了！请自行测试测试吧！

问：针对刚刚的 /srv/projecta 目录的权限设置中，我需要 1）取消 myuser1 的设置（连同默认值），以及 2）我不能让 pro3 这个用户使用该目录，亦即 pro3 在该目录下无任何权限， 该如何设置？答：取消全部的 ACL 设置可以使用 -b 来处理，但单一设置值的取消，就得要通过 -x 才行了！所以你应该这样作：

```
# 1.1 找到针对 myuser1 的设置值
[root@study ~]# getfacl /srv/projecta &#124; grep myuser1
user:myuser1:r-x
default:user:myuser1:r-x

# 1.2 针对每个设置值来处理，注意，取消某个帐号的 ACL 时，不需要加上权限项目！
[root@study ~]# setfacl -x u:myuser1 /srv/projecta
[root@study ~]# setfacl -x d:u:myuser1 /srv/projecta

# 2.1 开始让 pro3 这个用户无法使用该目录啰！
[root@study ~]# setfacl -m u:pro3:- /srv/projecta
```

只需要留意，当设置一个用户/群组没有任何权限的 ACL 语法中，在权限的字段不可留白，而是应该加上一个减号 （-） 才是正确的作法！





### 13.3.3 ACL 权限继承与设置

#### 继承 ACL 权限

默认情况下，ACL 权限不会自动继承。要设置继承权限，可以使用 `d` 参数。

**示例：设置目录的默认 ACL 权限**

```
setfacl -m d:u:myuser1:rx /srv/projecta
getfacl /srv/projecta
```

新建文件或目录会继承默认 ACL 权限。

#### 取消 ACL 设置

**示例：取消 myuser1 的 ACL 设置**

```
setfacl -x u:myuser1 /srv/projecta
setfacl -x d:u:myuser1 /srv/projecta
```

**示例：让 pro3 无法使用该目录**

```
setfacl -m u:pro3:- /srv/projecta
```

通过 `-` 来表示用户或群组无任何权限。

#### 总结

ACL 是一种灵活的权限管理机制，可以精确控制文件和目录的访问权限。通过 `setfacl` 和 `getfacl` 命令，可以轻松设置和查看 ACL。默认 ACL 的设置还可以实现权限的继承，方便管理复杂的权限需求。在实际操作中，合理使用 ACL 可以大大提高系统的安全性和灵活性。







## 13.4 使用者身份切换

在 Linux 系统中进行身份切换有几个原因：

1. **使用一般帐号**：为了安全，一般建议以普通用户身份操作日常任务，只有在需要时才切换到 root，避免误操作。
2. **用较低权限启动系统服务**：例如，使用 apache 用户启动 Apache 服务，这样即使服务被攻破，系统也不至于受影响。
3. **软件本身的限制**：例如，telnet 和 ssh 可以设置拒绝 root 直接登录，这时需要先登录普通用户再切换身份。

主要有两种身份切换方式：

1. **su**：直接切换身份，需要输入目标用户（如 root）的密码。
2. **sudo**：执行特定命令时临时获得高权限，需提前配置，输入当前用户密码。

#### 原文：

什么？在 Linux 系统当中还要作身份的变换？这是为啥？可能有下面几个原因啦！

- 使用一般帐号：系统平日操作的好习惯

事实上，为了安全的缘故，一些老人家都会建议你，尽量以一般身份使用者来操作 Linux 的日常作业！等到需要设置系统环境时， 才变换身份成为 root 来进行系统管理，相对比较安全啦！避免作错一些严重的指令，例如恐怖的“ rm -rf / ”（千万作不得！）

- 用较低权限启动系统服务

相对于系统安全，有的时候，我们必须要以某些系统帐号来进行程序的执行。 举例来说， Linux 主机上面的一套软件，名称为 apache ，我们可以额外创建一个名为 apache 的使用者来启动 apache 软件啊，如此一来，如果这个程序被攻破，至少系统还不至于就损毁了～

- 软件本身的限制

在远古时代的 [telnet](http://linux.vbird.org/linux_server/0310telnetssh.php#telnet) 程序中，该程序默认是不许使用 root 的身份登陆的，telnet 会判断登陆者的 UID， 若 UID 为 0 的话，那就直接拒绝登陆了。所以，你只能使用一般使用者来登陆 Linux 服务器。 此外， [ssh](http://linux.vbird.org/linux_server/0310telnetssh.php#ssh) [[3\]](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/117.html#ps3) 也可以设置拒绝 root 登陆喔！那如果你有系统设置需求该如何是好啊？就变换身份啊！

由于上述考虑，所以我们都是使用一般帐号登陆系统的，等有需要进行系统维护或软件更新时才转为 root 的身份来动作。 那如何让一般使用者转变身份成为 root 呢？主要有两种方式喔：

- 以“ su - ”直接将身份变成 root 即可，但是这个指令却需要 root 的密码，也就是说，如果你要以 su 变成 root 的话，你的一般使用者就必须要有 root 的密码才行；
- 以“ sudo 指令 ”执行 root 的指令串，由于 sudo 需要事先设置妥当，且 sudo 需要输入使用者自己的密码， 因此多人共管同一部主机时， sudo 要比 su 来的好喔！至少 root 密码不会流出去！

下面我们就来说一说 su 跟 sudo 的用法啦！

### 13.4.1 su

`su` 是最简单的身份切换命令，用法如下：

```
su [-lm] [-c 指令] [username]
```

选项与参数：

- `-`：使用 login-shell 的方式登录，读取新用户的环境变量。
- `-l`：同 `-`。
- `-m`：保留当前环境设置，不读取新用户的配置文件。
- `-c`：执行一次指令后返回原身份。

**示例：**

```
# 切换到 root，不读取新用户环境变量
[dmtsai@study ~]$ su
Password: 
[root@study dmtsai]# env | grep 'dmtsai'

# 使用 login shell 的方式切换到 root
[dmtsai@study ~]$ su -
Password: 
[root@study ~]# env | grep root

# 执行一次 root 权限命令后返回原身份
[dmtsai@study ~]$ su - -c "head -n 3 /etc/shadow"
Password:
```



#### 原文：

su 是最简单的身份切换指令了，他可以进行任何身份的切换唷！方法如下：

```
[root@study ~]# su [-lm] [-c 指令] [username]
选项与参数：
-   ：单纯使用 - 如“ su - ”代表使用 login-shell 的变量文件读取方式来登陆系统；
      若使用者名称没有加上去，则代表切换为 root 的身份。
-l  ：与 - 类似，但后面需要加欲切换的使用者帐号！也是 login-shell 的方式。
-m  ：-m 与 -p 是一样的，表示“使用目前的环境设置，而不读取新使用者的配置文件”
-c  ：仅进行一次指令，所以 -c 后面可以加上指令喔！
```

上表的解释当中有出现之前[第十章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)谈过的 [login-shell](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#settings_bashrc_shell) 配置文件读取方式，如果你忘记那是啥东西， 请先回去第十章瞧瞧再回来吧！这个 su 的用法当中，有没有加上那个减号“ - ”差很多喔！ 因为涉及 login-shell 与 non-login shell 的变量读取方法。这里让我们以一个小例子来说明吧！

```
范例一：假设你原本是 dmtsai 的身份，想要使用 non-login shell 的方式变成 root
[dmtsai@study ~]$ su       &lt;==注意提示字符，是 dmtsai 的身份喔！
Password:                  &lt;==这里输入 root 的密码喔！
[root@study dmtsai]# id    &lt;==提示字符的目录是 dmtsai 喔！
uid=0（root） gid=0（root） groups=0（root） context=unconf....  &lt;==确实是 root 的身份！
[root@study dmtsai]# env &#124; grep 'dmtsai'
USER=dmtsai                                         &lt;==竟然还是 dmtsai 这家伙！
PATH=...:/home/dmtsai/.local/bin:/home/dmtsai/bin   &lt;==这个影响最大！ 
MAIL=/var/spool/mail/dmtsai                         &lt;==收到的 mailbox 是 vbird1
PWD=/home/dmtsai                                    &lt;==并非 root 的主文件夹
LOGNAME=dmtsai
# 虽然你的 UID 已经是具有 root 的身份，但是看到上面的输出讯息吗？
# 还是有一堆变量为原本 dmtsai 的身份，所以很多数据还是无法直接利用。
[root@study dmtsai]# exit   &lt;==这样可以离开 su 的环境！
```

单纯使用“ su ”切换成为 root 的身份，读取的变量设置方式为 non-login shell 的方式，这种方式很多原本的变量不会被改变， 尤其是我们之前谈过很多次的 PATH 这个变量，由于没有改变成为 root 的环境， 因此很多 root 惯用的指令就只能使用绝对路径来执行咯。其他的还有 MAIL 这个变量，你输入 mail 时， 收到的邮件竟然还是 dmtsai 的，而不是 root 本身的邮件！是否觉得很奇怪啊！所以切换身份时，请务必使用如下的范例二：

```
范例二：使用 login shell 的方式切换为 root 的身份并观察变量
[dmtsai@study ~]$ su -
Password:   &lt;==这里输入 root 的密码喔！
[root@study ~]# env &#124; grep root
USER=root
MAIL=/var/spool/mail/root
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
PWD=/root
HOME=/root
LOGNAME=root
# 了解差异了吧？下次变换成为 root 时，记得最好使用 su - 喔！
[root@study ~]# exit   &lt;==这样可以离开 su 的环境！
```

上述的作法是让使用者的身份变成 root 并开始操作系统，如果想要离开 root 的身份则得要利用 exit 离开才行。 那我如果只是想要执行“一个只有 root 才能进行的指令，且执行完毕就恢复原本的身份”呢？那就可以加上 -c 这个选项啰！ 请参考下面范例三！

```
范例三：dmtsai 想要执行“ head -n 3 /etc/shadow ”一次，且已知 root 密码
[dmtsai@study ~]$ head -n 3 /etc/shadow
head: cannot open `/etc/shadow' for reading: Permission denied
[dmtsai@study ~]$ su - -c "head -n 3 /etc/shadow"
Password: &lt;==这里输入 root 的密码喔！
root:$6$wtbCCce/PxMeE5wm$KE2IfSJr.YLP7Rcai6oa/T7KFhOYO62vDnqfLw85...:16559:0:99999:7:::
bin:*:16372:0:99999:7:::
daemon:*:16372:0:99999:7:::
[dmtsai@study ~]$ &lt;==注意看，身份还是 dmtsai 喔！继续使用旧的身份进行系统操作！
```

由于 /etc/shadow 权限的关系，该文件仅有 root 可以查阅。为了查阅该文件，所以我们必须要使用 root 的身份工作。 但我只想要进行一次该指令而已，此时就使用类似上面的语法吧！好，那接下来，如果我是 root 或者是其他人， 想要变更成为某些特殊帐号，可以使用如下的方法来切换喔！

```
范例四：原本是 dmtsai 这个使用者，想要变换身份成为 vbird1 时？
[dmtsai@study ~]$ su -l vbird1
Password: &lt;==这里输入 vbird1 的密码喔！
[vbird1@study ~]$ su -
Password: &lt;==这里输入 root 的密码喔！
[root@study ~]# id sshd
uid=74（sshd） gid=74（sshd） groups=74（sshd） ... &lt;==确实有存在此人
[root@study ~]# su -l sshd
This account is currently not available.      &lt;==竟然说此人无法切换？
[root@study ~]# finger sshd
Login: sshd                             Name: Privilege-separated SSH
Directory: /var/empty/sshd              Shell: /sbin/nologin
[root@study ~]# exit    &lt;==离开第二次的 su 
[vbird1@study ~]$ exit  &lt;==离开第一次的 su 
[dmtsai@study ~]$ exit  &lt;==这才是最初的环境！
```

su 就这样简单的介绍完毕，总结一下他的用法是这样的：

- 若要完整的切换到新使用者的环境，必须要使用“ su - username ”或“ su -l username ”， 才会连同 PATH/USER/MAIL 等变量都转成新使用者的环境；
- 如果仅想要执行一次 root 的指令，可以利用“ su - -c "指令串" ”的方式来处理；
- 使用 root 切换成为任何使用者时，并不需要输入新使用者的密码；

虽然使用 su 很方便啦，不过缺点是，当我的主机是多人共管的环境时，如果大家都要使用 su 来切换成为 root 的身份，那么不就每个人都得要知道 root 的密码，这样密码太多人知道可能会流出去， 很不妥当呢！怎办？通过 sudo 来处理即可！



### 13.4.2 sudo

`sudo` 可以让用户在不需要知道 root 密码的情况下，临时获得高权限。用法如下：

```
sudo [-b] [-u 新使用者帐号] 命令
```

选项与参数：

- `-b`：将命令放到后台执行。
- `-u`：切换到指定用户，默认切换到 root。

**示例：**

```
# 以 sshd 用户身份创建文件
[root@study ~]# sudo -u sshd touch /tmp/mysshd
[root@study ~]# ll /tmp/mysshd

# 以 vbird1 用户身份执行一串命令
[root@study ~]# sudo -u vbird1 sh -c "mkdir ~vbird1/www; cd ~vbird1/www; echo 'This is index.html file' > index.html"
[root@study ~]# ll -a ~vbird1/www
```

### 配置 sudo

`sudo` 的配置文件是 `/etc/sudoers`，建议使用 `visudo` 编辑，以确保语法正确。

**示例：**

```
# 允许 vbird1 使用 sudo
[root@study ~]# visudo
root    ALL=(ALL)       ALL
vbird1  ALL=(ALL)       ALL

# 使用 sudo 测试
[vbird1@study ~]$ sudo tail -n 1 /etc/shadow
[sudo] password for vbird1:
```



### 使用群组管理 sudo 权限

**示例：**

```
# 允许 wheel 群组的用户使用 sudo
[root@study ~]# visudo
%wheel    ALL=(ALL)    ALL

# 将用户添加到 wheel 群组
[root@study ~]# usermod -a -G wheel pro1

# 测试
[pro1@study ~]$ sudo tail -n 1 /etc/shadow
[sudo] password for pro1:
```

### 限制特定命令

**示例：**

```
# 允许 myuser1 使用 sudo 修改密码，但不能修改 root 密码
[root@study ~]# visudo
myuser1  ALL=(root)    /usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd root

# 测试
[myuser1@study ~]$ sudo passwd myuser3
[sudo] password for myuser1:
[myuser1@study ~]$ sudo passwd
Changing password for user root.
```

### 使用别名管理 sudo 权限

**示例：**

```
# 创建别名并应用
[root@study ~]# visudo
User_Alias ADMPW = pro1, pro2, pro3, myuser1, myuser2
Cmnd_Alias ADMPWCOM = /usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd root
ADMPW ALL=(root) ADMPWCOM
```

### 配置 sudo 不需要密码

**示例：**

```
# 配置 wheel 群组用户使用 sudo 不需要密码
[root@study ~]# visudo
%wheel    ALL=(ALL)    NOPASSWD: ALL
```

### 使用 sudo 搭配 su

**示例：**

```
# 配置允许用户通过 sudo 使用 su 切换到 root
[root@study ~]# visudo
User_Alias ADMINS = pro1, pro2, pro3, myuser1
ADMINS ALL=(root) /bin/su -
```

使用 `sudo` 和 `su` 时，请确保用户的可信度，以避免潜在的安全风险。

#### **原文：**

相对于 su 需要了解新切换的使用者密码 （常常是需要 root 的密码）， sudo 的执行则仅需要自己的密码即可！ 甚至可以设置不需要密码即可执行 sudo 呢！由于 sudo 可以让你以其他用户的身份执行指令 （通常是使用 root 的身份来执行指令），因此并非所有人都能够执行 sudo ， 而是仅有规范到 /etc/sudoers 内的用户才能够执行 sudo 这个指令喔！说的这么神奇，下面就来瞧瞧那 sudo 如何使用？

![鸟哥的图示](image/vbird_face-172241485295213.gif)

**Tips** 事实上，一般用户能够具有 sudo 的使用权，就是管理员事先审核通过后，才开放 sudo 的使用权的！因此，除非是信任用户，否则一般用户默认是不能操作 sudo 的喔！

- sudo 的指令用法

由于一开始系统默认仅有 root 可以执行 sudo ，因此下面的范例我们先以 root 的身份来执行，等到谈到 visudo 时，再以一般使用者来讨论其他 sudo 的用法吧！ sudo 的语法如下：

![鸟哥的图示](image/vbird_face-172241485295213.gif)

**Tips** 还记得在安装 CentOS 7 的第三章时，在设置一般帐号的项目中，有个“让这位使用者成为管理员”的选项吧？如果你有勾选该选项的话， 那除了 root 之外，该一般用户确实是可以使用 sudo 的喔（以鸟哥的例子来说， dmtsai 默认竟然可以使用 sudo 了！）！这是因为创建帐号的时候，默认将此用户加入 sudo 的支持中了！详情本章稍后告知！

```
[root@study ~]# sudo [-b] [-u 新使用者帐号]
选项与参数：
-b  ：将后续的指令放到背景中让系统自行执行，而不与目前的 shell 产生影响
-u  ：后面可以接欲切换的使用者，若无此项则代表切换身份为 root 。

范例一：你想要以 sshd 的身份在 /tmp 下面创建一个名为 mysshd 的文件
[root@study ~]# sudo -u sshd touch /tmp/mysshd
[root@study ~]# ll /tmp/mysshd
-rw-r--r--. 1 sshd sshd 0 Jul 21 23:37 /tmp/mysshd
# 特别留意，这个文件的权限是由 sshd 所创建的情况喔！

范例二：你想要以 vbird1 的身份创建 ~vbird1/www 并于其中创建 index.html 文件
[root@study ~]# sudo -u vbird1 sh -c &lt;u&gt;"mkdir ~vbird1/www; cd ~vbird1/www; \&lt;/u&gt;
&gt;  &lt;u&gt;echo 'This is index.html file' &gt; index.html"&lt;/u&gt;
[root@study ~]# ll -a ~vbird1/www
drwxr-xr-x. 2 vbird1 vbird1   23 Jul 21 23:38 .
drwx------. 6 vbird1 vbird1 4096 Jul 21 23:38 ..
-rw-r--r--. 1 vbird1 vbird1   24 Jul 21 23:38 index.html
# 要注意，创建者的身份是 vbird1 ，且我们使用 sh -c "一串指令" 来执行的！
```

sudo 可以让你切换身份来进行某项任务，例如上面的两个范例。范例一中，我们的 root 使用 sshd 的权限去进行某项任务！ 要注意，因为我们无法使用“ su - sshd ”去切换系统帐号 （因为系统帐号的 shell 是 /sbin/nologin）， 这个时候 sudo 真是他 X 的好用了！立刻以 sshd 的权限在 /tmp 下面创建文件！查阅一下文件权限你就了解意义啦！ 至于范例二则更使用多重指令串 （通过分号 ; 来延续指令进行），使用 sh -c 的方法来执行一连串的指令， 如此真是好方便！

但是 sudo 默认仅有 root 能使用啊！为什么呢？因为 sudo 的执行是这样的流程：

1. 当使用者执行 sudo 时，系统于 /etc/sudoers 文件中搜寻该使用者是否有执行 sudo 的权限；
2. 若使用者具有可执行 sudo 的权限后，便让使用者“输入使用者自己的密码”来确认；
3. 若密码输入成功，便开始进行 sudo 后续接的指令（但 root 执行 sudo 时，不需要输入密码）；
4. 若欲切换的身份与执行者身份相同，那也不需要输入密码。

所以说，sudo 执行的重点是：“能否使用 sudo 必须要看 /etc/sudoers 的设置值， 而可使用 sudo 者是通过输入使用者自己的密码来执行后续的指令串”喔！由于能否使用与 /etc/sudoers 有关， 所以我们当然要去编辑 sudoers 文件啦！不过，因为该文件的内容是有一定的规范的，因此直接使用 vi 去编辑是不好的。 此时，我们得要通过 visudo 去修改这个文件喔！

- visudo 与 /etc/sudoers

从上面的说明我们可以知道，除了 root 之外的其他帐号，若想要使用 sudo 执行属于 root 的权限指令，则 root 需要先使用 visudo 去修改 /etc/sudoers ，让该帐号能够使用全部或部分的 root 指令功能。为什么要使用 visudo 呢？这是因为 /etc/sudoers 是有设置语法的，如果设置错误那会造成无法使用 sudo 指令的不良后果。因此才会使用 visudo 去修改， 并在结束离开修改画面时，系统会去检验 /etc/sudoers 的语法就是了。

一般来说，visudo 的设置方式有几种简单的方法喔，下面我们以几个简单的例子来分别说明：

- I. 单一使用者可进行 root 所有指令，与 sudoers 文件语法：

假如我们要让 vbird1 这个帐号可以使用 root 的任何指令，基本上有两种作法，第一种是直接通过修改 /etc/sudoers ，方法如下：

```
[root@study ~]# visudo
....（前面省略）....
root    ALL=（ALL）       ALL  &lt;==找到这一行，大约在 98 行左右
vbird1  ALL=（ALL）       ALL &lt;==这一行是你要新增的！
....（下面省略）....
```

有趣吧！其实 visudo 只是利用 vi 将 /etc/sudoers 文件调用出来进行修改而已，所以这个文件就是 /etc/sudoers 啦！ 这个文件的设置其实很简单，如上面所示，如果你找到 98 行 （有 root 设置的那行） 左右，看到的数据就是：

```
使用者帐号  登陆者的来源主机名称=（可切换的身份）  可下达的指令
root                         ALL=（ALL）           ALL   &lt;==这是默认值
```

上面这一行的四个元件意义是：

1. “使用者帐号”：系统的哪个帐号可以使用 sudo 这个指令的意思；
2. “登陆者的来源主机名称”：当这个帐号由哪部主机连线到本 Linux 主机，意思是这个帐号可能是由哪一部网络主机连线过来的， 这个设置值可以指定用户端计算机（信任的来源的意思）。默认值 root 可来自任何一部网络主机
3. “（可切换的身份）”：这个帐号可以切换成什么身份来下达后续的指令，默认 root 可以切换成任何人；
4. “可下达的指令”：可用该身份下达什么指令？这个指令请务必使用绝对路径撰写。 默认 root 可以切换任何身份且进行任何指令之意。

那个 ALL 是特殊的关键字，代表任何身份、主机或指令的意思。所以，我想让 vbird1 可以进行任何身份的任何指令， 就如同上表特殊字体写的那样，其实就是复制上述默认值那一行，再将 root 改成 vbird1 即可啊！ 此时“vbird1 不论来自哪部主机登陆，他可以变换身份成为任何人，且可以进行系统上面的任何指令”之意。 修改完请储存后离开 vi，并以 vbird1 登陆系统后，进行如下的测试看看：

```
[vbird1@study ~]$ tail -n 1 /etc/shadow  &lt;==注意！身份是 vbird1
tail: cannot open `/etc/shadow' for reading: Permission denied
# 因为不是 root 嘛！所以当然不能查询 /etc/shadow

[vbird1@study ~]$ sudo tail -n 1 /etc/shadow &lt;==通过 sudo

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1） Respect the privacy of others.  &lt;==这里仅是一些说明与警示项目
    #2） Think before you type.
    #3） With great power comes great responsibility.

[sudo] password for vbird1: &lt;==注意啊！这里输入的是“ vbird1 自己的密码 ”
pro3:$6$DMilzaKr$OeHeTDQPHzDOz/u5Cyhq1Q1dy...:16636:0:99999:7:::
# 看！vbird1 竟然可以查询 shadow ！
```

注意到了吧！vbird1 输入自己的密码就能够执行 root 的指令！所以，系统管理员当然要了解 vbird1 这个用户的“操守”才行！否则随便设置一个使用者，他恶搞系统怎办？另外，一个一个设置太麻烦了， 能不能使用群组的方式来设置呢？参考下面的第二种方式吧。

- II. 利用 wheel 群组以及免密码的功能处理 visudo

我们在本章前面曾经创建过 pro1, pro2, pro3 ，这三个用户能否通过群组的功能让这三个人可以管理系统？ 可以的，而且很简单！同样我们使用实际案例来说明：

```
[root@study ~]# visudo  &lt;==同样的，请使用 root 先设置
....（前面省略）....
%wheel     ALL=（ALL）    ALL &lt;==大约在 106 行左右，请将这行的 # 拿掉！
# 在最左边加上 % ，代表后面接的是一个“群组”之意！改完请储存后离开

[root@study ~]# usermod -a -G wheel pro1 &lt;==将 pro1 加入 wheel 的支持
```

上面的设置值会造成“任何加入 wheel 这个群组的使用者，就能够使用 sudo 切换任何身份来操作任何指令”的意思。 你当然可以将 wheel 换成你自己想要的群组名。接下来，请分别切换身份成为 pro1 及 pro2 试看看 sudo 的运行。

```
[pro1@study ~]$ sudo tail -n 1 /etc/shadow &lt;==注意身份是 pro1
....（前面省略）....
[sudo] password for pro1:  &lt;==输入 pro1 的密码喔！
pro3:$6$DMilzaKr$OeHeTDQPHzDOz/u5Cyhq1Q1dy...:16636:0:99999:7:::

[pro2@study ~]$ sudo tail -n 1 /etc/shadow &lt;==注意身份是 pro2
[sudo] password for pro2:  &lt;==输入 pro2 的密码喔！
pro2 is not in the sudoers file.  This incident will be reported.
# 仔细看错误讯息他是说这个 pro2 不在 /etc/sudoers 的设置中！
```

这样理解群组了吧？如果你想要让 pro3 也支持这个 sudo 的话，不需要重新使用 visudo ，只要利用 [usermod](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#usermod) 去修改 pro3 的群组支持，让 pro3 用户加入 wheel 群组当中，那他就能够进行 sudo 啰！ 好了！那么现在你知道为啥在安装时创建的用户，就是那个 dmstai 默认可以使用 sudo 了吗？请使用“ id dmtsai ”看看， 这个用户是否有加入 wheel 群组呢？嘿嘿！了解乎？

![鸟哥的图示](image/vbird_face-172241485295213.gif)

**Tips** 从 CentOS 7 开始，在 sudoers 文件中，默认已经开放 %wheel 那一行啰！以前的 CentOS 旧版本都是没有启用的呢！

简单吧！不过，既然我们都信任这些 sudo 的用户了，能否提供“不需要密码即可使用 sudo ”呢？ 就通过如下的方式：

```
[root@study ~]# visudo  &lt;==同样的，请使用 root 先设置
....（前面省略）....
%wheel     ALL=（ALL）   NOPASSWD: ALL &lt;==大约在 109 行左右，请将 # 拿掉！
# 在最左边加上 % ，代表后面接的是一个“群组”之意！改完请储存后离开
```

重点是那个 NOPASSWD 啦！该关键字是免除密码输入的意思喔！

- III. 有限制的指令操作：

上面两点都会让使用者能够利用 root 的身份进行任何事情！这样总是不太好～如果我想要让使用者仅能够进行部分系统任务， 比方说，系统上面的 myuser1 仅能够帮 root 修改其他使用者的密码时，亦即“当使用者仅能使用 passwd 这个指令帮忙 root 修改其他用户的密码”时，你该如何撰写呢？可以这样做：

```
[root@study ~]# visudo  &lt;==注意是 root 身份
myuser1      ALL=（root）    /usr/bin/passwd  &lt;==最后指令务必用绝对路径
```

上面的设置值指的是“myuser1 可以切换成为 root 使用 passwd 这个指令”的意思。其中要注意的是： 指令字段必须要填写绝对路径才行！否则 visudo 会出现语法错误的状况发生！ 此外，上面的设置是有问题的！我们使用下面的指令操作来让您了解：

```
[myuser1@study ~]$ sudo passwd myuser3  &lt;==注意，身份是 myuser1
[sudo] password for myuser1:  &lt;==输入 myuser1 的密码
Changing password for user myuser3\. &lt;==下面改的是 myuser3 的密码喔！这样是正确的
New password:
Retype new password:
passwd: all authentication tokens updated successfully.

[myuser1@study ~]$ sudo passwd
Changing password for user root.  &lt;==见鬼！怎么会去改 root 的密码？
```

恐怖啊！我们竟然让 root 的密码被 myuser1 给改变了！下次 root 回来竟无法登陆系统...欲哭无泪～怎办？ 所以我们必须要限制使用者的指令参数！修改的方法为将上述的那行改一改先：

```
[root@study ~]# visudo  &lt;==注意是 root 身份
myuser1    ALL=（root）  !/usr/bin/passwd, /usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd root
```

在设置值中加上惊叹号“ ! ”代表“不可执行”的意思。因此上面这一行会变成：可以执行“ passwd 任意字符”，但是“ passwd ”与“ passwd root ”这两个指令例外！ 如此一来 myuser1 就无法改变 root 的密码了！这样这位使用者可以具有 root 的能力帮助你修改其他用户的密码， 而且也不能随意改变 root 的密码！很有用处的！

- IV. 通过别名创建 visudo：

如上述第三点，如果我有 15 个用户需要加入刚刚的管理员行列，那么我是否要将上述那长长的设置写入 15 行啊？ 而且如果想要修改命令或者是新增命令时，那我每行都需要重新设置，很麻烦ㄟ！有没有更简单的方式？ 是有的！通过别名即可！我们 visudo 的别名可以是“指令别名、帐号别名、主机别名”等。不过这里我们仅介绍帐号别名， 其他的设置值有兴趣的话，可以自行玩玩！

假设我的 pro1, pro2, pro3 与 myuser1, myuser2 要加入上述的密码管理员的 sudo 列表中， 那我可以创立一个帐号别名称为 ADMPW 的名称，然后将这个名称处理一下即可。处理的方式如下：

```
[root@study ~]# visudo  &lt;==注意是 root 身份
User_Alias ADMPW = pro1, pro2, pro3, myuser1, myuser2
Cmnd_Alias ADMPWCOM = !/usr/bin/passwd, /usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd root
ADMPW   ALL=（root）  ADMPWCOM
```

我通过 User_Alias 创建出一个新帐号，这个帐号名称一定要使用大写字符来处理，包括 Cmnd_Alias（命令别名）、Host_Alias（来源主机名称别名） 都需要使用大写字符的！这个 ADMPW 代表后面接的那些实际帐号。 而该帐号能够进行的指令就如同 ADMPWCOM 后面所指定的那样！上表最后一行则写入这两个别名 （帐号与指令别名）， 未来要修改时，我只要修改 User_Alias 以及 Cmnd_Alias 这两行即可！设置方面会比较简单有弹性喔！

- V. sudo 的时间间隔问题：

或许您已经发现了，那就是，如果我使用同一个帐号在短时间内重复操作 sudo 来运行指令的话， 在第二次执行 sudo 时，并不需要输入自己的密码！sudo 还是会正确的运行喔！为什么呢？ 第一次执行 sudo 需要输入密码，是担心由于使用者暂时离开座位，但有人跑来你的座位使用你的帐号操作系统之故。 所以需要你输入一次密码重新确认一次身份。

两次执行 sudo 的间隔在五分钟内，那么再次执行 sudo 时就不需要再次输入密码了， 这是因为系统相信你在五分钟内不会离开你的作业，所以执行 sudo 的是同一个人！呼呼！真是很人性化的设计啊～ ^_^。不过如果两次 sudo 操作的间隔超过 5 分钟，那就得要重新输入一次你的密码了 [[4\]](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/117.html#ps4)

- VI. sudo 搭配 su 的使用方式：

很多时候我们需要大量执行很多 root 的工作，所以一直使用 sudo 觉得很烦ㄟ！那有没有办法使用 sudo 搭配 su ， 一口气将身份转为 root ，而且还用使用者自己的密码来变成 root 呢？是有的！而且方法简单的会让你想笑！ 我们创建一个 ADMINS 帐号别名，然后这样做：

```
[root@study ~]# visudo
User_Alias  ADMINS = pro1, pro2, pro3, myuser1
ADMINS ALL=（root）  /bin/su -
```

接下来，上述的 pro1, pro2, pro3, myuser1 这四个人，只要输入“ sudo su - ”并且输入“自己的密码”后， 立刻变成 root 的身份！不但 root 密码不会外流，使用者的管理也变的非常方便！ 这也是实务上面多人共管一部主机时常常使用的技巧呢！这样管理确实方便，不过还是要强调一下大前提， 那就是“这些你加入的使用者，全部都是你能够信任的用户”！





## 13.5 使用者的特殊 shell 与 PAM 模块

本节讨论如何创建仅能使用特定服务（如邮件服务）但不能登录系统的账户，以及 PAM（Pluggable Authentication Modules）的概念和应用。

#### 原文：

我们前面一直谈到的大多是一般身份使用者与系统管理员 （root） 的相关操作， 而且大多是讨论关于可登陆系统的帐号来说。那么换个角度想，**如果我今天想要创建的， 是一个“仅能使用 mail server 相关邮件服务的帐号，而该帐号并不能登陆 Linux 主机”呢？**如果不能给予该帐号一个密码，那么该帐号就无法使用系统的各项资源，当然也包括 mail 的资源， 而如果给予一个密码，那么该帐号就可能可以登陆 Linux 主机啊！呵呵～伤脑筋吧～ 所以，下面让我们来谈一谈这些有趣的话题啰！

另外，在本章之前谈到过 [/etc/login.defs](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#login.defs) 文件中，关于密码长度应该默认是 5 个字串长度，但是我们上面也谈到，该设置值已经被 PAM 模块所取代了，**那么 PAM 是什么？为什么他可以影响我们使用者的登陆呢？**这里也要来谈谈的！

### 13.5.1 特殊的 shell, /sbin/nologin

为了防止某些用户登录系统但仍能使用系统资源，可以给这些用户设置一个特殊的 shell：`/sbin/nologin`。这样，即使这些用户有密码，也无法登录系统，会看到如下信息：

```
This account is currently not available.
```

这种方法常用于系统帐号或仅使用特定服务（如邮件服务）的帐号。例如，为了让使用者知道他们不能登录系统，可以创建 `/etc/nologin.txt` 文件，并在其中说明原因。用户尝试登录时，会显示该文件的内容。

**例子：**

编辑 `/etc/nologin.txt`：

```
[root@study ~]# vim /etc/nologin.txt
This account is system account or mail account.
Please DO NOT use this account to login my Linux server.
```

测试：

```
[root@study ~]# su - myuser3
This account is system account or mail account.
Please DO NOT use this account to login my Linux server.
```

**原文：**

在本章一开头的 [passwd 文件结构](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#passwd_file)里面我们就谈过系统帐号这玩意儿，这玩意儿的 shell 就是使用 /sbin/nologin ，重点在于系统帐号是不需要登陆的！所以我们就给他这个无法登陆的合法 shell。 使用了这个 shell 的用户即使有了密码，你想要登陆时他也无法登陆，因为会出现如下的讯息喔：

```
This account is currently not available.
```

我们所谓的“无法登陆”指的仅是：“这个使用者无法使用 bash 或其他 shell 来登陆系统”而已， 并不是说这个帐号就无法使用其他的系统资源喔！ 举例来说，各个系统帐号，打印工作由 lp 这个帐号在管理， WWW 服务由 apache 这个帐号在管理， 他们都可以进行系统程序的工作，但是“就是无法登陆主机取得互动的 shell”而已啦！^_^

换个角度来想，如果我的 Linux 主机提供的是邮件服务，所以说，在这部 Linux 主机上面的帐号， 其实大部分都是用来收受主机的信件而已，并不需要登陆主机的呢！ 这个时候，我们就可以考虑让单纯使用 mail 的帐号以 /sbin/nologin 做为他们的 shell ， 这样，最起码当我的主机被尝试想要登陆系统以取得 shell 环境时，可以拒绝该帐号呢！

另外，如果我想要让某个具有 /sbin/nologin 的使用者知道，他们不能登陆主机时， 其实我可以创建“ /etc/nologin.txt ”这个文件， 并且在这个文件内说明不能登陆的原因，那么下次当这个使用者想要登陆系统时， 屏幕上出现的就会是 /etc/nologin.txt 这个文件的内容，而不是默认的内容了！

例题：当使用者尝试利用纯 mail 帐号 （例如 myuser3） 时，利用 /etc/nologin.txt 告知使用者不要利用该帐号登陆系统。答：直接以 vim 编辑该文件，内容可以是这样：

```
[root@study ~]# vim /etc/nologin.txt
This account is system account or mail account.
Please DO NOT use this account to login my Linux server.
```

想要测试时，可以使用 myuser3 （此帐号的 shell 是 /sbin/nologin） 来测试看看！

```
[root@study ~]# su - myuser3
This account is system account or mail account.
Please DO NOT use this account to login my Linux server.
```

结果会发现与原本的默认讯息不一样喔！ ^_^

### 13.5.2 PAM 模块简介

PAM 是嵌入式认证模块，可以统一管理用户认证过程，解决多种认证机制并存导致的不同步问题。PAM 提供一套 API，通过模块化的方式进行认证。

PAM 的工作流程如下：

1. 用户执行程序（如 `passwd`），并输入密码。
2. 程序调用 PAM 模块进行认证。
3. PAM 模块根据配置文件执行认证。
4. PAM 返回认证结果给程序。
5. 程序根据结果执行相应操作。

PAM 模块的设置文件位于 `/etc/pam.d/` 目录中，文件名与程序名相同。

**原文：**

在过去，我们想要对一个使用者进行认证 （authentication），得要要求使用者输入帐号密码， 然后通过自行撰写的程序来判断该帐号密码是否正确。也因为如此，我们常常得使用不同的机制来判断帐号密码， 所以搞的一部主机上面拥有多个各别的认证系统，也造成帐号密码可能不同步的验证问题！ 为了解决这个问题因此有了 PAM （Pluggable Authentication Modules, 嵌入式模块） 的机制！

**PAM 可以说是一套应用程序接口 （Application Programming Interface, API），他提供了一连串的验证机制，只要使用者将验证阶段的需求告知 PAM 后， PAM 就能够回报使用者验证的结果 （成功或失败）。**由于 PAM 仅是一套验证的机制，又可以提供给其他程序所调用引用，因此不论你使用什么程序，都可以使用 PAM 来进行验证，如此一来，就能够让帐号密码或者是其他方式的验证具有一致的结果！也让程序设计师方便处理验证的问题喔！ [[5\]](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/118.html#ps5)

![img](image/D(@20T)CEDQ310UH_ETQI.png)

图13.5.1、PAM 模块与其他程序的相关性

如上述的图示， PAM 是一个独立的 API 存在，只要任何程序有需求时，可以向 PAM 发出验证要求的通知， PAM 经过一连串的验证后，将验证的结果回报给该程序，然后该程序就能够利用验证的结果来进行可登陆或显示其他无法使用的讯息。 这也就是说，你可以在写程序的时候将 PAM 模块的功能加入，就能够利用 PAM 的验证功能啰。 因此目前很多程序都会利用 PAM 喔！所以我们才要来学习他啊！

PAM 用来进行验证的数据称为模块 （Modules），每个 PAM 模块的功能都不太相同。举例来说， 还记得我们在本章使用 [passwd](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#passwd) 指令时，如果随便输入字典上面找的到的字串， passwd 就会回报错误信息了！这是为什么呢？这就是 PAM 的 pam_cracklib.so 模块的功能！他能够判断该密码是否在字典里面！ 并回报给密码修改程序，此时就能够了解你的密码强度了。

所以，当你有任何需要判断是否在字典当中的密码字串时，就可以使用 pam_cracklib.so 这个模块来验证！ 并根据验证的回报结果来撰写你的程序呢！这样说，可以理解 PAM 的功能了吧？

### 13.5.3 PAM 模块设置语法

PAM 配置文件包含以下三部分：

1. 验证类别（Type）：四种类型：
   - `auth`：身份验证。
   - `account`：权限验证。
   - `session`：会话管理。
   - `password`：密码管理。
2. 控制旗标（Control Flag）：四种控制方式：
   - `required`：验证失败则继续，成功继续。
   - `requisite`：验证失败则终止，成功继续。
   - `sufficient`：验证成功则终止，失败继续。
   - `optional`：仅显示信息。
3. PAM 模块与参数：实际执行的模块及其参数。

**例子：`/etc/pam.d/passwd` 文件**

```
[root@study ~]# cat /etc/pam.d/passwd
#%PAM-1.0
auth       include      system-auth
account    include      system-auth
password   substack     system-auth
-password  optional     pam_gnome_keyring.so use_authtok
password   substack     postlogin
```

每一行表示一个认证过程，`include` 表示调用其他配置文件。

**原文：**

PAM 借由一个与程序相同文件名的配置文件来进行一连串的认证分析需求。我们同样以 passwd 这个指令的调用 PAM 来说明好了。 当你执行 passwd 后，这支程序调用 PAM 的流程是：

1. 使用者开始执行 /usr/bin/passwd 这支程序，并输入密码；
2. passwd 调用 PAM 模块进行验证；
3. PAM 模块会到 /etc/pam.d/ 找寻与程序 （passwd） 同名的配置文件；
4. 依据 /etc/pam.d/passwd 内的设置，引用相关的 PAM 模块逐步进行验证分析；
5. 将验证结果 （成功、失败以及其他讯息） 回传给 passwd 这支程序；
6. passwd 这支程序会根据 PAM 回传的结果决定下一个动作 （重新输入新密码或者通过验证！）

从上头的说明，我们会知道重点其实是 /etc/pam.d/ 里面的配置文件，以及配置文件所调用的 PAM 模块进行的验证工作！ 既然一直谈到 passwd 这个密码修改指令，那我们就来看看 /etc/pam.d/passwd 这个配置文件的内容是怎样吧！

```
[root@study ~]# cat /etc/pam.d/passwd
#%PAM-1.0  &lt;==PAM版本的说明而已！
auth       include      system-auth   &lt;==每一行都是一个验证的过程
account    include      system-auth
password   substack     system-auth
-password   optional    pam_gnome_keyring.so use_authtok
password   substack     postlogin
验证类别   控制标准     PAM 模块与该模块的参数
```

在这个配置文件当中，除了第一行宣告 PAM 版本之外，其他任何“ # ”开头的都是注解，而每一行都是一个独立的验证流程， 每一行可以区分为三个字段，分别是验证类别（type）、控制标准（flag）、PAM的模块与该模块的参数。 下面我们先来谈谈验证类别与控制标准这两项数据吧！

![鸟哥的图示](image/vbird_face-172248638646917.gif)

**Tips** 你会发现在我们上面的表格当中出现的是“ include （包括） ”这个关键字，他代表的是“请调用后面的文件来作为这个类别的验证”， 所以，上述的每一行都要重复调用 /etc/pam.d/system-auth 那个文件来进行验证的意思！

- 第一个字段：验证类别 （Type）

验证类别主要分为四种，分别说明如下：

- auth 是 authentication （认证） 的缩写，所以这种类别主要用来检验使用者的身份验证，这种类别通常是需要密码来检验的， 所以后续接的模块是用来检验使用者的身份。
- account account （帐号） 则大部分是在进行 authorization （授权），这种类别则主要在检验使用者是否具有正确的使用权限， 举例来说，当你使用一个过期的密码来登陆时，当然就无法正确的登陆了。
- session session 是会议期间的意思，所以 session 管理的就是使用者在这次登陆 （或使用这个指令） 期间，PAM 所给予的环境设置。 这个类别通常用在记录使用者登陆与登出时的信息！例如，如果你常常使用 su 或者是 sudo 指令的话， 那么应该可以在 /var/log/secure 里面发现很多关于 pam 的说明，而且记载的数据是“session open, session close”的信息！
- password password 就是密码嘛！所以这种类别主要在提供验证的修订工作，举例来说，就是修改/变更密码啦！

这四个验证的类型通常是有顺序的，不过也有例外就是了。 会有顺序的原因是，（1）我们总是得要先验证身份 （auth） 后， （2）系统才能够借由使用者的身份给予适当的授权与权限设置 （account），而且（3）登陆与登出期间的环境才需要设置， 也才需要记录登陆与登出的信息 （session）。如果在运行期间需要密码修订时，（4）才给予 password 的类别。这样说起来， 自然是需要有点顺序吧！

- 第二个字段：验证的控制旗标 （control flag）

那么“验证的控制旗标（control flag）”又是什么？简单的说，他就是“验证通过的标准”啦！ 这个字段在管控该验证的放行方式，主要也分为四种控制方式：

- required 此验证若成功则带有 success （成功） 的标志，若失败则带有 failure 的标志，但不论成功或失败都会继续后续的验证流程。 由于后续的验证流程可以继续进行，因此相当有利于数据的登录 （log） ，这也是 PAM 最常使用 required 的原因。
- requisite 若验证失败则立刻回报原程序 failure 的标志，并终止后续的验证流程。若验证成功则带有 success 的标志并继续后续的验证流程。 这个项目与 required 最大的差异，就在于失败的时候还要不要继续验证下去？由于 requisite 是失败就终止， 因此失败时所产生的 PAM 信息就无法通过后续的模块来记录了。
- sufficient 若验证成功则立刻回传 success 给原程序，并终止后续的验证流程；若验证失败则带有 failure 标志并继续后续的验证流程。 这玩意儿与 requisits 刚好相反！
- optional 这个模块控制项目大多是在显示讯息而已，并不是用在验证方面的。

如果将这些控制旗标以图示的方式配合成功与否的条件绘图，会有点像下面这样：

![PAM 控制旗标所造成的回报流程](image/pam-2.gif)图13.5.2、PAM 控制旗标所造成的回报流程

程序运行过程中遇到验证时才会去调用 PAM ，而 PAM 验证又分很多类型与控制，不同的控制旗标所回报的讯息并不相同。 如上图所示， requisite 失败就回报了并不会继续，而 sufficient 则是成功就回报了也不会继续。 至于验证结束后所回报的信息通常是“succes 或 failure ”而已，后续的流程还需要该程序的判断来继续执行才行。

### 13.5.4 常用模块简介

常用 PAM 模块包括：

1. **pam_securetty.so**：限制 root 只能从安全终端登录。
2. **pam_nologin.so**：当 `/etc/nologin` 存在时，禁止普通用户登录。
3. **pam_selinux.so**：管理 SELinux 权限。
4. **pam_console.so**：处理控制台登录。
5. **pam_loginuid.so**：验证用户 UID。
6. **pam_env.so**：设置环境变量。
7. **pam_unix.so**：多功能模块，管理认证、授权、会话记录等。
8. **pam_pwquality.so**：检验密码强度。
9. **pam_limits.so**：管理用户资源限制。

**例子：`/etc/pam.d/login` 文件**

```
[root@study ~]# cat /etc/pam.d/login
#%PAM-1.0
auth       include      system-auth
account    required     pam_nologin.so
password   include      system-auth
session    required     pam_selinux.so close
session    required     pam_loginuid.so
session    include      system-auth
session    include      postlogin
```

**原文：**

谈完了配置文件的语法后，现在让我们来查阅一下 CentOS 5.x 提供的 PAM 默认文件的内容是啥吧！ 由于我们常常需要通过各种方式登陆 （login） 系统，因此就来看看登陆所需要的 PAM 流程为何：

```
[root@study ~]# cat /etc/pam.d/login
#%PAM-1.0
auth [user_unknown=ignore success=ok ignore=ignore default=bad] pam_securetty.so
auth       substack     system-auth
auth       include      postlogin
account    required     pam_nologin.so
account    include      system-auth
password   include      system-auth
# pam_selinux.so close should be the first session rule
session    required     pam_selinux.so close
session    required     pam_loginuid.so
session    optional     pam_console.so
# pam_selinux.so open should only be followed by sessions to be executed in the user context
session    required     pam_selinux.so open
session    required     pam_namespace.so
session    optional     pam_keyinit.so force revoke
session    include      system-auth
session    include      postlogin
-session   optional     pam_ck_connector.so
# 我们可以看到，其实 login 也调用多次的 system-auth ，所以下面列出该配置文件

[root@study ~]# cat /etc/pam.d/system-auth
#%PAM-1.0
# This file is auto-generated.
# User changes will be destroyed the next time authconfig is run.
auth        required      pam_env.so
auth        sufficient    pam_fprintd.so
auth        sufficient    pam_unix.so nullok try_first_pass
auth        requisite     pam_succeed_if.so uid &gt;= 1000 quiet_success
auth        required      pam_deny.so

account     required      pam_unix.so
account     sufficient    pam_localuser.so
account     sufficient    pam_succeed_if.so uid &lt; 1000 quiet
account     required      pam_permit.so

password    requisite     pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=
password    sufficient    pam_unix.so sha512 shadow nullok try_first_pass use_authtok
password    required      pam_deny.so

session     optional      pam_keyinit.so revoke
session     required      pam_limits.so
-session     optional      pam_systemd.so
session     [success=1 default=ignore] pam_succeed_if.so service in crond quiet use_uid
session     required      pam_unix.so
```

上面这个表格当中使用到非常多的 PAM 模块，每个模块的功能都不太相同，详细的模块情报可以在你的系统中找到：

- /etc/pam.d/*：每个程序个别的 PAM 配置文件；
- /lib64/security/*：PAM 模块文件的实际放置目录；
- /etc/security/*：其他 PAM 环境的配置文件；
- /usr/share/doc/pam-*/：详细的 PAM 说明文档。

例如鸟哥使用未 update 过的 CentOS 7.1 ，pam*nologin 说明文档在： /usr/share/doc/pam-1.1.8/txts/README.pam_nologin。你可以自行查阅一下该模块的功能。 鸟哥这里仅简单介绍几个较常使用的模块，详细的信息还得要您努力查阅参考书呢！ ^*^

- pam_securetty.so： 限制系统管理员 （root） 只能够从安全的 （secure） 终端机登陆；那什么是终端机？例如 tty1, tty2 等就是传统的终端机设备名称。那么安全的终端机设置呢？ 就写在 /etc/securetty 这个文件中。你可以查阅一下该文件， 就知道为什么 root 可以从 tty1~tty7 登陆，但却无法通过 telnet 登陆 Linux 主机了！
- pam_nologin.so： 这个模块可以限制一般使用者是否能够登陆主机之用。当 /etc/nologin 这个文件存在时，则所有一般使用者均无法再登陆系统了！若 /etc/nologin 存在，则一般使用者在登陆时， 在他们的终端机上会将该文件的内容显示出来！所以，正常的情况下，这个文件应该是不能存在系统中的。 但这个模块对 root 以及已经登陆系统中的一般帐号并没有影响。 （注意喔！这与 /etc/nologin.txt 并不相同！）
- pam_selinux.so： SELinux 是个针对程序来进行细部管理权限的功能，SELinux 这玩意儿我们会在[第十六章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)的时候再来详细谈论。由于 SELinux 会影响到使用者执行程序的权限，因此我们利用 PAM 模块，将 SELinux 暂时关闭，等到验证通过后， 再予以启动！
- pam_console.so： 当系统出现某些问题，或者是某些时刻你需要使用特殊的终端接口 （例如 RS232 之类的终端连线设备） 登陆主机时， 这个模块可以帮助处理一些文件权限的问题，让使用者可以通过特殊终端接口 （console） 顺利的登陆系统。
- pam_loginuid.so： 我们知道系统帐号与一般帐号的 UID 是不同的！一般帐号 UID 均大于 1000 才合理。 因此，为了验证使用者的 UID 真的是我们所需要的数值，可以使用这个模块来进行规范！
- pam_env.so： 用来设置环境变量的一个模块，如果你有需要额外的环境变量设置，可以参考 /etc/security/pam_env.conf 这个文件的详细说明。
- pam_unix.so： 这是个很复杂且重要的模块，这个模块可以用在验证阶段的认证功能，可以用在授权阶段的帐号授权管理， 可以用在会议阶段的登录文件记录等，甚至也可以用在密码更新阶段的检验！非常丰富的功能！ 这个模块在早期使用得相当频繁喔！
- pam_pwquality.so： 可以用来检验密码的强度！包括密码是否在字典中，密码输入几次都失败就断掉此次连线等功能，都是这模块提供的！ 最早之前其实使用的是 pam_cracklib.so 这个模块，后来改成 pam_pwquality.so 这个模块，但此模块完全相容于 pam_cracklib.so， 同时提供了 /etc/security/pwquality.conf 这个文件可以额外指定默认值！比较容易处理修改！
- pam_limits.so： 还记得我们在[第十章谈到的 ulimit](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#variable_ulimit) 吗？ 其实那就是这个模块提供的能力！还有更多细部的设置可以参考： /etc/security/limits.conf 内的说明。

了解了这些模块的大致功能后，言归正传，讨论一下 login 的 PAM 验证机制流程是这样的：

1. 验证阶段 （auth）：首先，（a）会先经过 pam_securetty.so 判断，如果使用者是 root 时，则会参考 /etc/securetty 的设置； 接下来（b）经过 pam_env.so 设置额外的环境变量；再（c）通过 pam_unix.so 检验密码，若通过则回报 login 程序；若不通过则（d）继续往下以 pam_succeed_if.so 判断 UID 是否大于 1000 ，若小于 1000则回报失败，否则再往下 （e）以 pam_deny.so 拒绝连线。
2. 授权阶段 （account）：（a）先以 pam_nologin.so 判断 /etc/nologin 是否存在，若存在则不许一般使用者登陆； （b）接下来以 pam_unix.so 及 pam_localuser.so 进行帐号管理，再以 （c） pam_succeed_if.so 判断 UID 是否小于 1000 ，若小于 1000 则不记录登录信息。（d）最后以 pam_permit.so 允许该帐号登陆。
3. 密码阶段 （password）：（a）先以 pam_pwquality.so 设置密码仅能尝试错误 3 次；（b）接下来以 pam_unix.so 通过 sha512, shadow 等功能进行密码检验，若通过则回报 login 程序，若不通过则 （c）以 pam_deny.so 拒绝登陆。
4. 会议阶段 （session）：（a）先以 pam_selinux.so 暂时关闭 SELinux；（b）使用 pam_limits.so 设置好使用者能够操作的系统资源； （c）登陆成功后开始记录相关信息在登录文件中； （d）以 pam_loginuid.so 规范不同的 UID 权限；（e）打开 pam_selinux.so 的功能。

总之，就是依据验证类别 （type） 来看，然后先由 login 的设置值去查阅，如果出现“ include system-auth ” 就转到 system-auth 文件中的相同类别，去取得额外的验证流程就是了。然后再到下一个验证类别，最终将所有的验证跑完！ 就结束这次的 PAM 验证啦！

经过这样的验证流程，现在你知道为啥 /etc/nologin 存在会有问题，也会知道为何你使用一些远端连线机制时， 老是无法使用 root 登陆的问题了吧？没错！这都是 PAM 模块提供的功能啦！

例题：为什么 root 无法以 telnet 直接登陆系统，但是却能够使用 ssh 直接登陆？答：一般来说， telnet 会引用 login 的 PAM 模块，而 login 的验证阶段会有 /etc/securetty 的限制！ 由于远端连线属于 pts/n （n 为数字） 的动态终端机接口设备名称，并没有写入到 /etc/securetty ， 因此 root 无法以 telnet 登陆远端主机。至于 ssh 使用的是 /etc/pam.d/sshd 这个模块， 你可以查阅一下该模块，由于该模块的验证阶段并没有加入 pam_securetty ，因此就没有 /etc/securetty 的限制！故可以从远端直接连线到服务器端。

另外，关于 telnet 与 ssh 的细部说明，请参考[鸟哥的 Linux 私房菜服务器篇](http://linux.vbird.org/linux_server)

### 13.5.5 其他相关文件

除了 PAM 配置文件外，还有其他相关文件：

1. **/etc/securetty**：限制 root 可登录的终端。
2. **/etc/nologin**：存在时禁止普通用户登录。
3. **/etc/security/limits.conf**：设置用户资源限制。
4. **/var/log/secure** 和 **/var/log/messages**：记录登录和认证相关的信息。

**例子：设置用户资源限制**

```
[root@study ~]# vim /etc/security/limits.conf
vbird1    soft    fsize    90000
vbird1    hard    fsize    100000
```

以上设置限制 vbird1 用户最大文件大小为 100MB，超过 90MB 时警告。

PAM 是一个强大的工具，可以灵活管理用户认证和资源限制，确保系统安全。

## 13.6 Linux 主机上的使用者讯息传递

在 Linux 系统中，了解如何查询用户信息以及在用户之间传递消息是非常重要的。以下将介绍几种常用的查询和通信方法。

### 13.6.1 查询使用者： w, who, last, lastlog

这些命令用于查询用户的登录信息和状态。

- **w**：显示当前登录用户及其活动。

  ```
  [root@study ~]# w
   01:49:18 up 25 days,  3:34,  3 users,  load average: 0.00, 0.01, 0.05
  USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
  dmtsai   tty2                      07Jul15 12days  0.03s  0.03s -bash
  dmtsai   pts/0    172.16.200.254   00:18    6.00s  0.31s  0.11s sshd: dmtsai [priv]
  ```

- **who**：显示当前登录用户的信息。

  ```
  [root@study ~]# who
  dmtsai   tty2         2015-07-07 23:07
  dmtsai   pts/0        2015-07-22 00:18 （192.168.1.100）
  ```

- **last**：显示用户的历史登录信息。

  ```
  [root@study ~]# last
  ```
  
- **lastlog**：显示每个用户的最近登录时间。

  ```
  [root@study ~]# lastlog
  Username         Port     From             Latest
  root             pts/0                     Wed Jul 22 00:26:08 +0800 2015
  bin                                        **Never logged in**
  dmtsai           pts/1    192.168.1.100    Wed Jul 22 01:08:07 +0800 2015
  ```

#### 原文：

如何查询一个使用者的相关数据呢？这还不简单，我们之前就提过了 [id](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#id), [finger](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#finger) 等指令了，都可以让您了解到一个使用者的相关信息啦！那么想要知道使用者到底啥时候登陆呢？ 最简单可以使用 last 检查啊！这个玩意儿我们也在 [第十章 bash](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#last) 提过了， 您可以自行前往参考啊！简单的很。

![鸟哥的图示](image/vbird_face-17225808755081.gif)

**Tips** 早期的 Red Hat 系统的版本中， last 仅会列出当月的登陆者信息，不过在我们的 CentOS 5.x 版以后， last 可以列出从系统创建之后到目前为止的所有登陆者信息！这是因为登录文件轮替的设置不同所致。 详细的说明可以参考后续的[第十八章登录文件简介](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)。

那如果你想要知道目前已登陆在系统上面的使用者呢？可以通过 w 或 who 来查询喔！如下范例所示：

```
[root@study ~]# w
 01:49:18 up 25 days,  3:34,  3 users,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
dmtsai   tty2                      07Jul15 12days  0.03s  0.03s -bash
dmtsai   pts/0    172.16.200.254   00:18    6.00s  0.31s  0.11s sshd: dmtsai [priv]
# 第一行显示目前的时间、开机 （up） 多久，几个使用者在系统上平均负载等；
# 第二行只是各个项目的说明，
# 第三行以后，每行代表一个使用者。如上所示，dmtsai 登陆并取得终端机名 tty2 之意。

[root@study ~]# who
dmtsai   tty2         2015-07-07 23:07
dmtsai   pts/0        2015-07-22 00:18 （192.168.1.100）
```

另外，如果您想要知道每个帐号的最近登陆的时间，则可以使用 lastlog 这个指令喔！ lastlog 会去读取 /var/log/lastlog 文件，结果将数据输出如下表：

```
[root@study ~]# lastlog
Username         Port     From             Latest
root             pts/0                     Wed Jul 22 00:26:08 +0800 2015
bin                                        **Never logged in**
....（中间省略）....
dmtsai           pts/1    192.168.1.100    Wed Jul 22 01:08:07 +0800 2015
vbird1           pts/0                     Wed Jul 22 01:32:17 +0800 2015
pro3                                       **Never logged in**
....（以下省略）....
```

这样就能够知道每个帐号的最近登陆的时间啰～ ^_^



### 13.6.2 使用者对谈： write, mesg, wall

在系统中，可以使用 `write` 命令与其他用户对话，`mesg` 命令控制是否接受消息，`wall` 命令向所有用户广播消息。

- **write**：发送消息给特定用户。

  ```
  [root@study ~]# write vbird1 pts/2
  Hello, there:
  Please don't do anything wrong...
  ```

- **mesg**：控制是否接受消息。

  ```
  [vbird1@study ~]$ mesg n  # 禁止接受消息
  [vbird1@study ~]$ mesg y  # 允许接受消息
  ```

- **wall**：向所有用户广播消息。

  ```
  [root@study ~]# wall "I will shutdown my linux server..."
  ```

#### 原文：

如何查询一个使用者的相关数据呢？这还不简单，我们之前就提过了 [id](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#id), [finger](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#finger) 等指令了，都可以让您了解到一个使用者的相关信息啦！那么想要知道使用者到底啥时候登陆呢？ 最简单可以使用 last 检查啊！这个玩意儿我们也在 [第十章 bash](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#last) 提过了， 您可以自行前往参考啊！简单的很。

![鸟哥的图示](image/vbird_face-17225812396703.gif)

**Tips** 早期的 Red Hat 系统的版本中， last 仅会列出当月的登陆者信息，不过在我们的 CentOS 5.x 版以后， last 可以列出从系统创建之后到目前为止的所有登陆者信息！这是因为登录文件轮替的设置不同所致。 详细的说明可以参考后续的[第十八章登录文件简介](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)。

那如果你想要知道目前已登陆在系统上面的使用者呢？可以通过 w 或 who 来查询喔！如下范例所示：

```
[root@study ~]# w
 01:49:18 up 25 days,  3:34,  3 users,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
dmtsai   tty2                      07Jul15 12days  0.03s  0.03s -bash
dmtsai   pts/0    172.16.200.254   00:18    6.00s  0.31s  0.11s sshd: dmtsai [priv]
# 第一行显示目前的时间、开机 （up） 多久，几个使用者在系统上平均负载等；
# 第二行只是各个项目的说明，
# 第三行以后，每行代表一个使用者。如上所示，dmtsai 登陆并取得终端机名 tty2 之意。

[root@study ~]# who
dmtsai   tty2         2015-07-07 23:07
dmtsai   pts/0        2015-07-22 00:18 （192.168.1.100）
```

另外，如果您想要知道每个帐号的最近登陆的时间，则可以使用 lastlog 这个指令喔！ lastlog 会去读取 /var/log/lastlog 文件，结果将数据输出如下表：

```
[root@study ~]# lastlog
Username         Port     From             Latest
root             pts/0                     Wed Jul 22 00:26:08 +0800 2015
bin                                        **Never logged in**
....（中间省略）....
dmtsai           pts/1    192.168.1.100    Wed Jul 22 01:08:07 +0800 2015
vbird1           pts/0                     Wed Jul 22 01:32:17 +0800 2015
pro3                                       **Never logged in**
....（以下省略）....
```

这样就能够知道每个帐号的最近登陆的时间啰～ ^_^



### 13.6.3 使用者邮件信箱： mail

使用 `mail` 命令可以发送和接收邮件。系统中的每个用户都有一个邮箱文件，通常位于 `/var/spool/mail` 目录下。

- **发送邮件**：

  ```
  [root@study ~]# mail -s "nice to meet you" vbird1
  Hello, D.M. Tsai
  Nice to meet you in the network.
  You are so nice.  byebye!
  .  # 输入 . 表示结束邮件内容
  ```

- **使用文件内容发送邮件**：

  ```
  [root@study ~]# mail -s "bashrc file content" dmtsai < ~/.bashrc
  [root@study ~]# ls -al ~ | mail -s "myfile" root
  ```

- **查看和处理邮件**：

  ```
  [vbird1@study ~]$ mail
  Heirloom Mail version 12.5 7/5/10.  Type ? for help.
  "/var/spool/mail/vbird1": 1 message 1 new
  >N  1 root                  Wed Jul 22 02:09  20/671   "nice to meet you"
  & ?  # 输入 ? 查看命令帮助
  ```

常用 `mail` 命令：

- **h**：列出邮件标头。
- **d**：删除指定邮件。
- **s**：保存邮件到文件。
- **x**：退出 `mail` 程序，不做任何更改。
- **q**：退出 `mail` 程序，应用所有更改。

#### 总结

通过这些命令，可以有效地管理和与系统上的用户进行通信。`w`、`who`、`last` 和 `lastlog` 用于查询用户信息，`write`、`mesg` 和 `wall` 用于用户之间的通信，`mail` 用于发送和接收邮件。这些工具对于系统管理员和普通用户都非常有用，能够帮助他们更好地了解和管理系统。

#### **原文：**

使用 wall, write 毕竟要等到使用者在线上才能够进行，有没有其他方式来联络啊？ 不是说每个 Linux 主机上面的使用者都具有一个 mailbox 吗？ 我们可否寄信给使用者啊！呵呵！当然可以啊！我们可以寄、收 mailbox 内的信件呢！ 一般来说， mailbox 都会放置在 /var/spool/mail 里面，一个帐号一个 mailbox （文件）。 举例来说，我的 vbird1 就具有 /var/spool/mail/vbird1 这个 mailbox 喔！

那么我该如何寄出信件呢？就直接使用 mail 这个指令即可！这个指令的用法很简单的，直接这样下达：“ mail -s "邮件标题" username@localhost ”即可！ 一般来说，如果是寄给本机上的使用者，基本上，连“ @localhost ”都不用写啦！ 举例来说，我以 root 寄信给 vbird1 ，信件标题是“ nice to meet you ”，则：

```
[root@study ~]# mail -s "nice to meet you" vbird1
Hello, D.M. Tsai
Nice to meet you in the network.
You are so nice.  byebye!
.    &lt;==这里很重要喔，结束时，最后一行输入小数点 . 即可！
EOT
[root@study ~]#  &lt;==出现提示字符，表示输入完毕了！
```

如此一来，你就已经寄出一封信给 vbird1 这位使用者啰，而且，该信件标题为： nice to meet you，信件内容就如同上面提到的。不过，你或许会觉得 mail 这个程序不好用～ 因为在信件编写的过程中，如果写错字而按下 Enter 进入次行，前一行的数据很难删除ㄟ！ 那怎么办？没关系啦！我们使用数据流重导向啊！呵呵！利用那个小于的符号 （ < ） 就可以达到取代键盘输入的要求了。也就是说，你可以先用 vi 将信件内容编好， 然后再以 mail -s "nice to meet you" vbird1 < filename 来将文件内容传输即可。

例题：请将你的主文件夹下的环境变量文件 （~/.bashrc） 寄给自己！答：mail -s "bashrc file content" dmtsai < ~/.bashrc例题：通过管线命令直接将 ls -al ~ 的内容传给 root 自己！答：ls -al ~ | mail -s "myfile" root

刚刚上面提到的是关于“寄信”的问题，那么如果是要收信呢？呵呵！同样的使用 mail 啊！ 假设我以 vbird1 的身份登陆主机，然后输入 mail 后，会得到什么？

```
[vbird1@study ~]$ mail
Heirloom Mail version 12.5 7/5/10\.  Type ? for help.
"/var/spool/mail/vbird1": 1 message 1 new
&gt;N  1 root                  Wed Jul 22 02:09  20/671   "nice to meet you"
&  &lt;==这里可以输入很多的指令，如果要查阅，输入 ? 即可！
```

在 mail 当中的提示字符是 & 符号喔，别搞错了～输入 mail 之后，我可以看到我有一封信件， 这封信件的前面那个 > 代表目前处理的信件，而在大于符号的右边那个 N 代表该封信件尚未读过， 如果我想要知道这个 mail 内部的指令有哪些，可以在 & 之后输入“ ? ”，就可以看到如下的画面：

```
& ?
               mail commands
type &lt;message list&gt;             type messages
next                            goto and type next message
from &lt;message list&gt;             give head lines of messages
headers                         print out active message headers
delete &lt;message list&gt;           delete messages
undelete &lt;message list&gt;         undelete messages
save &lt;message list&gt; folder      append messages to folder and mark as saved
copy &lt;message list&gt; folder      append messages to folder without marking them
write &lt;message list&gt; file       append message texts to file, save attachments
preserve &lt;message list&gt;         keep incoming messages in mailbox even if saved
Reply &lt;message list&gt;            reply to message senders
reply &lt;message list&gt;            reply to message senders and all recipients
mail addresses                  mail to specific recipients
file folder                     change to another folder
quit                            quit and apply changes to folder
xit                             quit and discard changes made to folder
!                               shell escape
cd &lt;directory&gt;                  chdir to directory or home if none given
list                            list names of all available commands
```

<message list> 指的是每封邮件的左边那个数字啦！而几个比较常见的指令是：

| 指令 | 意义                                                         |
| ---- | ------------------------------------------------------------ |
| h    | 列出信件标头；如果要查阅 40 封信件左右的信件标头，可以输入“ h 40 ” |
| d    | 删除后续接的信件号码，删除单封是“ d10 ”，删除 20~40 封则为“ d20-40 ”。 不过，这个动作要生效的话，必须要配合 q 这个指令才行（参考下面说明）！ |
| s    | 将信件储存成文件。例如我要将第 5 封信件的内容存成 ~/mail.file:“s 5 ~/mail.file” |
| x    | 或者输入 exit 都可以。这个是“不作任何动作离开 mail 程序”的意思。 不论你刚刚删除了什么信件，或者读过什么，使用 exit 都会直接离开 mail，所以刚刚进行的删除与阅读工作都会无效。 如果您只是查阅一下邮件而已的话，一般来说，建议使用这个离开啦！除非你真的要删除某些信件。 |
| q    | 相对于 exit 是不动作离开， q 则会实际进行你刚刚所执行的任何动作 （尤其是删除！） |

旧版的 CentOS 在使用 mail 读信后，通过 q 离开始，会将已读信件移动到 ~/mbox 中，不过目前 CentOS 7 已经不这么做了！ 所以离开 mail 可以轻松愉快的使用 q 了呢！



## 13.7 CentOS 7 环境下大量创建帐号的方法

在管理大量用户帐号时，使用脚本和相关工具可以显著提高效率。下面介绍几种常用的帐号检查工具和一个批量创建用户的脚本。

### 13.7.1 一些帐号相关的检查工具

#### pwck

`pwck` 用于检查 `/etc/passwd` 文件中的信息是否正确，以及对应的主文件夹是否存在。

```
[root@study ~]# pwck
user 'ftp': directory '/var/ftp' does not exist
user 'avahi-autoipd': directory '/var/lib/avahi-autoipd' does not exist
user 'pulse': directory '/var/run/pulse' does not exist
pwck: no changes
```

#### pwconv

`pwconv` 用于将 `/etc/passwd` 文件中的帐号和密码移动到 `/etc/shadow` 中。

- 比对 `/etc/passwd` 和 `/etc/shadow`，若存在差异，则进行修正。
- 将 `/etc/passwd` 中的加密密码移动到 `/etc/shadow`，并将 `/etc/passwd` 中的密码字段替换为 `x`。

#### pwunconv

`pwunconv` 将 `/etc/shadow` 中的密码数据写回 `/etc/passwd`，并删除 `/etc/shadow` 文件。此操作具有风险，建议谨慎使用。

#### chpasswd

`chpasswd` 用于批量更新用户密码。输入格式为 `username:password`。

```
[root@study ~]# echo "vbird3:abcdefg" | chpasswd
```

#### 原文：

先来检查看看使用者的主文件夹、密码等数据有没有问题？这时会使用到的主要有 pwck 以及 pwconv / pwuconv 等，让我们来了解一下先！

- pwck

pwck 这个指令在检查 /etc/passwd 这个帐号配置文件内的信息，与实际的主文件夹是否存在等信息， 还可以比对 /etc/passwd /etc/shadow 的信息是否一致，另外，如果 /etc/passwd 内的数据字段错误时，会提示使用者修订。 一般来说，我只是利用这个玩意儿来检查我的输入是否正确就是了。

```
[root@study ~]# pwck
user 'ftp': directory '/var/ftp' does not exist
user 'avahi-autoipd': directory '/var/lib/avahi-autoipd' does not exist
user 'pulse': directory '/var/run/pulse' does not exist
pwck: no changes
```

瞧！上面仅是告知我，这些帐号并没有主文件夹，由于那些帐号绝大部分都是系统帐号，确实也不需要主文件夹的，所以，那是“正常的错误！”呵呵！不理他。 ^_^。 相对应的群组检查可以使用 grpck 这个指令的啦！

- pwconv

这个指令主要的目的是在“将 /etc/passwd 内的帐号与密码，移动到 /etc/shadow 当中！” 早期的 Unix 系统当中并没有 /etc/shadow 呢，所以，使用者的登陆密码早期是在 /etc/passwd 的第二栏，后来为了系统安全，才将密码数据移动到 /etc/shadow 内的。使用 pwconv 后，可以：

- 比对 /etc/passwd 及 /etc/shadow ，若 /etc/passwd 内存在的帐号并没有对应的 /etc/shadow 密码时，则 pwconv 会去 /etc/login.defs 取用相关的密码数据，并创建该帐号的 /etc/shadow 数据；
- 若 /etc/passwd 内存在加密后的密码数据时，则 pwconv 会将该密码栏移动到 /etc/shadow 内，并将原本的 /etc/passwd 内相对应的密码栏变成 x ！

一般来说，如果您正常使用 useradd 增加使用者时，使用 pwconv 并不会有任何的动作，因为 /etc/passwd 与 /etc/shadow 并不会有上述两点问题啊！ ^_^。不过，如果手动设置帐号，这个 pwconv 就很重要啰！

- pwunconv

相对于 pwconv ， pwunconv 则是“将 /etc/shadow 内的密码栏数据写回 /etc/passwd 当中， 并且删除 /etc/shadow 文件。”这个指令说实在的，最好不要使用啦！ 因为他会将你的 /etc/shadow 删除喔！如果你忘记备份，又不会使用 pwconv 的话，粉严重呢！

- chpasswd

chpasswd 是个挺有趣的指令，他可以“读入未加密前的密码，并且经过加密后， 将加密后的密码写入 /etc/shadow 当中。”这个指令很常被使用在大量创建帐号的情况中喔！ 他可以由 Standard input 读入数据，每笔数据的格式是“ username:password ”。 举例来说，我的系统当中有个使用者帐号为 vbird3 ，我想要更新他的密码 （update） ， 假如他的密码是 abcdefg 的话，那么我可以这样做：

```
[root@study ~]# echo "vbird3:abcdefg" &#124; chpasswd
```

神奇吧！这样就可以更新了呢！在默认的情况中， chpasswd 会去读取 /etc/login.defs 文件内的加密机制，我们 CentOS 7.x 用的是 SHA512， 因此 chpasswd 就默认会使用 SHA512 来加密！如果你想要使用不同的加密机制，那就得要使用 -c 以及 -e 等方式来处理了！ 不过从 CentOS 5.x 开始之后，passwd 已经默认加入了 --stdin 的选项，因此这个 chpasswd 就变得英雄无用武之地了！ 不过，在其他非 Red Hat 衍生的 Linux 版本中，或许还是可以参考这个指令功能来大量创建帐号喔！



### 13.7.2 大量创建帐号范本（适用 passwd --stdin 选项）

利用 `passwd --stdin` 选项，可以批量创建用户并设置密码。以下是一个创建用户的脚本示例：

```
[root@study ~]# vim accountadd.sh
#!/bin/bash
# This shell script will create amount of linux login accounts for you.
# 1. check the "accountadd.txt" file exist? you must create that file manually.
#    one account name one line in the "accountadd.txt" file.
# 2. use openssl to create users password.
# 3. User must change his password in his first login.
# 4. more options check the following url:
# 0410accountmanager.html#manual_amount
# 2015/07/22    VBird
export PATH=/bin:/sbin:/usr/bin:/usr/sbin

# 0. userinput
usergroup=""                   # if your account need secondary group, add here.
pwmech="openssl"               # "openssl" or "account" is needed.
homeperm="no"                  # if "yes" then I will modify home dir permission to 711

# 1. check the accountadd.txt file
action="${1}"                  # "create" is useradd and "delete" is userdel.
if [ ! -f accountadd.txt ]; then
    echo "There is no accountadd.txt file, stop here."
    exit 1
fi

[ "${usergroup}" != "" ] && groupadd -r ${usergroup}
rm -f outputpw.txt
usernames=$(cat accountadd.txt)

for username in ${usernames}
do
    case ${action} in
        "create")
            [ "${usergroup}" != "" ] && usegrp=" -G ${usergroup} " || usegrp=""
            useradd ${usegrp} ${username}               # 新增帐号
            [ "${pwmech}" == "openssl" ] && usepw=$(openssl rand -base64 6) || usepw=${username}
            echo ${usepw} | passwd --stdin ${username}  # 创建密码
            chage -d 0 ${username}                      # 强制登陆修改密码
            [ "${homeperm}" == "yes" ] && chmod 711 /home/${username}
            echo "username=${username}, password=${usepw}" >> outputpw.txt
            ;;
        "delete")
            echo "deleting ${username}"
            userdel -r ${username}
            ;;
        *)
            echo "Usage: $0 [create|delete]"
            ;;
    esac
done
```

### 创建用户示例

创建一个包含用户列表的文件 `accountadd.txt`，每行一个用户名。

```
[root@study ~]# vim accountadd.txt
std01
std02
std03
std04
std05
```

运行脚本创建用户：

```
[root@study ~]# sh accountadd.sh create
Changing password for user std01.
passwd: all authentication tokens updated successfully.
...
```

此脚本会根据 `accountadd.txt` 文件中的用户列表创建用户，并生成密码。生成的密码会保存到 `outputpw.txt` 文件中，供后续查阅。

通过上述脚本和工具，可以有效地管理和批量创建用户，提高工作效率。

#### 原文：

由于 CentOS 7.x 的 passwd 已经提供了 --stdin 的功能，因此如果我们可以提供帐号密码的话， 那么就能够很简单的创建起我们的帐号密码了。下面鸟哥制作一个简单的 script 来执行新增用户的功能喔！

```
[root@study ~]# vim accountadd.sh
#!/bin/bash
# This shell script will create amount of linux login accounts for you.
# 1\. check the "accountadd.txt" file exist? you must create that file manually.
#    one account name one line in the "accountadd.txt" file.
# 2\. use openssl to create users password.
# 3\. User must change his password in his first login.
# 4\. more options check the following url:
# 0410accountmanager.html#manual_amount
# 2015/07/22    VBird
export PATH=/bin:/sbin:/usr/bin:/usr/sbin

# 0\. userinput
usergroup=""                   # if your account need secondary group, add here.
pwmech="openssl"               # "openssl" or "account" is needed.
homeperm="no"                  # if "yes" then I will modify home dir permission to 711

# 1\. check the accountadd.txt file
action="${1}"                  # "create" is useradd and "delete" is userdel.
if [ ! -f accountadd.txt ]; then
    echo "There is no accountadd.txt file, stop here."
        exit 1
fi

[ "${usergroup}" != "" ] && groupadd -r ${usergroup}
rm -f outputpw.txt
usernames=$（cat accountadd.txt）

for username in ${usernames}
do
    case ${action} in
        "create"）
            [ "${usergroup}" != "" ] && usegrp=" -G ${usergroup} " &#124;&#124; usegrp=""
            useradd ${usegrp} ${username}               # 新增帐号
            [ "${pwmech}" == "openssl" ] && usepw=$（openssl rand -base64 6） &#124;&#124; usepw=${username}
            echo ${usepw} &#124; passwd --stdin ${username}  # 创建密码
            chage -d 0 ${username}                      # 强制登陆修改密码
            [ "${homeperm}" == "yes" ] && chmod 711 /home/${username}
        echo "username=${username}, password=${usepw}" &gt;&gt; outputpw.txt
            ;;
        "delete"）
            echo "deleting ${username}"
            userdel -r ${username}
            ;;
        *）
            echo "Usage: $0 [create&#124;delete]"
            ;;
    esac
done
```

接下来只要创建 accountadd.txt 这个文件即可！鸟哥创建这个文件里面共有 5 行，你可以自行创建该文件！内容每一行一个帐号。 而是否需要修改密码？是否与帐号相同的信息等等，你可以自由选择！若使用 openssl 自动猜密码时，使用者的密码请由 outputpw.txt 去捞～鸟哥最常作的方法，就是将该文件打印出来，用裁纸机一个帐号一条，交给同学即可！

```
[root@study ~]# vim accountadd.txt
std01
std02
std03
std04
std05

[root@study ~]# sh accountadd.sh create
Changing password for user std01.
passwd: all authentication tokens updated successfully.
....（后面省略）....
```



## 13.8 重点回顾

- Linux 操作系统上面，关于帐号与群组，其实记录的是 UID/GID 的数字而已；
- 使用者的帐号/群组与 UID/GID 的对应，参考 /etc/passwd 及 /etc/group 两个文件
- /etc/passwd 文件结构以冒号隔开，共分为七个字段，分别是“帐号名称、密码、UID、GID、全名、主文件夹、shell”
- UID 只有 0 与非为 0 两种，非为 0 则为一般帐号。一般帐号又分为系统帐号 （1~999） 及可登陆者帐号 （大于 1000）
- 帐号的密码已经移动到 /etc/shadow 文件中，该文件权限为仅有 root 可以更动。该文件分为九个字段，内容为“ 帐号名称、加密密码、密码更动日期、密码最小可变动日期、密码最大需变动日期、密码过期前警告日数、密码失效天数、 帐号失效日、保留未使用”
- 使用者可以支持多个群组，其中在新建文件时会影响新文件群组者，为有效群组。而写入 /etc/passwd 的第四个字段者， 称为初始群组。
- 与使用者创建、更改参数、删除有关的指令为：useradd, usermod, userdel等，密码创建则为 passwd；
- 与群组创建、修改、删除有关的指令为：groupadd, groupmod, groupdel 等；
- 群组的观察与有效群组的切换分别为：groups 及 newgrp 指令；
- useradd 指令作用参考的文件有： /etc/default/useradd, /etc/login.defs, /etc/skel/ 等等
- 观察使用者详细的密码参数，可以使用“ chage -l 帐号 ”来处理；
- 使用者自行修改参数的指令有： chsh, chfn 等，观察指令则有： id, finger 等
- ACL 的功能需要文件系统有支持，CentOS 7 默认的 XFS 确实有支持 ACL 功能！
- ACL 可进行单一个人或群组的权限管理，但 ACL 的启动需要有文件系统的支持；
- ACL 的设置可使用 setfacl ，查阅则使用 getfacl ；
- 身份切换可使用 su ，亦可使用 sudo ，但使用 sudo 者，必须先以 visudo 设置可使用的指令；
- PAM 模块可进行某些程序的验证程序！与 PAM 模块有关的配置文件位于 /etc/pam.d/ *及 /etc/security/*
- 系统上面帐号登陆情况的查询，可使用 w, who, last, lastlog 等；
- 线上与使用者交谈可使用 write, wall，离线状态下可使用 mail 传送邮件！