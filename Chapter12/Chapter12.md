# 第十二章、学习 Shell Scripts

最近更新日期：20//

如果你真的很想要走信息这条路，并且想要管理好属于你的主机，那么，别说鸟哥不告诉你， 可以自动管理系统的好工具： **Shell scripts！这家伙真的是得要好好学习学习的！ 基本上， shell script 有点像是早期的批处理文件，亦即是将一些指令汇整起来一次执行，但是 Shell script 拥有更强大的功能，那就是他可以进行类似程序 （program） 的撰写，并且不需要经过编译 （compile） 就能够执行， 真的很方便。加上我们可通过 shell script 来简化我们日常的工作管理**， 而且，整个 Linux 环境中，**一些服务 （services） 的启动都是通过 shell script 的， 如果你对于 script 不了解，嘿嘿！发生问题时，可真是会求助无门喔！**所以，好好的学一学他吧！

## 12.1 什么是 Shell Scripts

Shell script 是一种利用 shell 的功能所编写的程序，它将一些 shell 的语法与指令写在纯文本文件中，搭配正则表达式、管线命令与数据流重导向等功能，以实现特定的处理目的。简单来说，shell script 就像是早期 DOS 的批处理文件 (.bat)，能够将多条指令汇集在一个文件中，便于一次性执行多个指令。Shell script 提供了阵列、循环、条件与逻辑判断等功能，使其可以作为一种编程语言使用，帮助系统管理员高效地管理主机。

### 12.1.1 为什么要学习 Shell Scripts

学习 shell script 有以下几个好处：

1. **自动化管理**：通过编写 shell script，可以自动化完成一些日常管理任务，如查询登录文件、监控主机状态、更新软件等，提高工作效率。
2. **系统追踪与管理**：许多系统服务的启动与管理都依赖 shell script，如 CentOS 的 `/etc/init.d/` 目录下的服务启动脚本。
3. **入侵检测**：通过 shell script 分析系统日志，可以实现简单的入侵检测与防护。
4. **简化复杂操作**：将一系列复杂的命令写入 script，可以通过执行一个文件来完成多步操作。
5. **数据处理**：shell script 可以处理简单的数据操作，如文本处理、数据比对等，快速且高效。
6. **跨平台支持**：shell script 可以在几乎所有 Unix-like 系统上运行，甚至在 Windows 上也有类似的仿真器。

### 12.1.2 第一支 Shell Script 的编写与执行

编写 shell script 就是创建一个纯文本文件，将需要执行的命令写入其中。要执行这个文件，可以通过以下几种方法：

1. **直接执行**：
   - 使用绝对路径：`/home/user/shell.sh`
   - 使用相对路径：`./shell.sh`
   - 将脚本放在 PATH 变量指定的目录中：`~/bin/shell.sh`
2. **通过 shell 解释器执行**：
   - `sh shell.sh`
   - `bash shell.sh`

#### 示例：Hello World

编写一个简单的 Hello World 脚本：

```
#!/bin/bash
# Program:
#       This program shows "Hello World!" in your screen.
# History:
# 2024/07/23    User    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo -e "Hello World! \a \n"
exit 0
```

保存为 `hello.sh`，然后通过以下命令执行：

```
chmod a+x hello.sh  # 赋予执行权限
./hello.sh
```

**原文：**

如同前面讲到的，shell script 其实就是纯文本文件，我们可以编辑这个文件，然后让这个文件来帮我们一次执行多个指令， 或者是利用一些运算与逻辑判断来帮我们达成某些功能。所以啦，要编辑这个文件的内容时，当然就需要具备有 bash 指令下达的相关认识。下达指令需要注意的事项在[第四章的开始下达指令](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#cmd_cmd)小节内已经提过，有疑问请自行回去翻阅。 在 shell script 的撰写中还需要用到下面的注意事项：

1. 指令的执行是从上而下、从左而右的分析与执行；
2. 指令的下达就如同[第四章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#cmd_cmd)内提到的： 指令、选项与参数间的多个空白都会被忽略掉；
3. 空白行也将被忽略掉，并且 [tab] 按键所推开的空白同样视为空白键；
4. 如果读取到一个 Enter 符号 （CR） ，就尝试开始执行该行 （或该串） 命令；
5. 至于如果一行的内容太多，则可以使用“ [Enter] ”来延伸至下一行；
6. “ # ”可做为注解！任何加在 # 后面的数据将全部被视为注解文字而被忽略！

如此一来，我们在 script 内所撰写的程序，就会被一行一行的执行。现在我们假设你写的这个程序文件名是 /home/dmtsai/shell.sh 好了，那如何执行这个文件？很简单，可以有下面几个方法：

- 直接指令下达： shell.sh 文件必须要具备可读与可执行 （rx） 的权限，然后：
  - 绝对路径：使用 /home/dmtsai/shell.sh 来下达指令；
  - 相对路径：假设工作目录在 /home/dmtsai/ ，则使用 ./shell.sh 来执行
  - 变量“PATH”功能：将 shell.sh 放在 PATH 指定的目录内，例如： ~/bin/
- 以 bash 程序来执行：通过“ bash shell.sh ”或“ sh shell.sh ”来执行

反正重点就是要让那个 shell.sh 内的指令可以被执行的意思啦！ 咦！那我为何需要使用 “./shell.sh ”来下达指令？忘记了吗？回去[第十章内的指令搜寻顺序](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#settings_path)察看一下， 你就会知道原因了！同时，由于 CentOS 默认使用者主文件夹下的 ~/bin 目录会被设置到 ${PATH} 内，所以你也可以将 shell.sh 创建在 /home/dmtsai/bin/ 下面 （ ~/bin 目录需要自行设置） 。此时，若 shell.sh 在 ~/bin 内且具有 rx 的权限，那就直接输入 shell.sh 即可执行该脚本程序！

那为何“ sh shell.sh ”也可以执行呢？这是因为 /bin/sh 其实就是 /bin/bash （链接文件），使用 sh shell.sh 亦即告诉系统，我想要直接以 bash 的功能来执行 shell.sh 这个文件内的相关指令的意思，所以此时你的 shell.sh 只要有 r 的权限即可被执行喔！而我们也可以利用 sh 的参数，如 -n 及 -x 来检查与追踪 shell.sh 的语法是否正确呢！ ^_^

- 撰写第一支 script

在武侠世界中，不论是那个门派，要学武功要从扫地与蹲马步做起，那么要学程序呢？呵呵，肯定是由“秀出 Hello World！” 这个字眼开始的！OK！那么鸟哥就先写一支 script 给大家瞧一瞧：

```
[dmtsai@study ~]$ mkdir bin; cd bin
[dmtsai@study bin]$ vim hello.sh
#!/bin/bash
# Program:
#       This program shows "Hello World!" in your screen.
# History:
# 2015/07/16    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo -e "Hello World! \a \n"
exit 0
```

在本章当中，请将所有撰写的 script 放置到你主文件夹的 ~/bin 这个目录内，未来比较好管理啦！上面的写法当中，鸟哥主要将整个程序的撰写分成数段，大致是这样：

1. 第一行 #!/bin/bash 在宣告这个 script 使用的 shell 名称： 因为我们使用的是 bash ，所以，必须要以“ **#!/bin/bash** ”来宣告这个文件内的语法使用 bash 的语法！那么当这个程序被执行时，他就能够载入 bash 的相关环境配置文件 （一般来说就是 [non-login shell 的 ~/.bashrc](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#settings_bashrc_shell)）， 并且执行 bash 来使我们下面的指令能够执行！这很重要的！（在很多状况中，如果没有设置好这一行， 那么该程序很可能会无法执行，因为系统可能无法判断该程序需要使用什么 shell 来执行啊！）
2. 程序内容的说明： 整个 script 当中，除了第一行的“ #! ”是用来宣告 shell 的之外，其他的 # 都是“注解”用途！ 所以上面的程序当中，第二行以下就是用来说明整个程序的基本数据。一般来说， 建议你一定要养成说明该 script 的：1. 内容与功能； 2. 版本信息； 3. 作者与联络方式； 4. 创建日期；5. 历史纪录 等等。这将有助于未来程序的改写与 debug 呢！
3. 主要环境变量的宣告： 建议务必要将一些重要的环境变量设置好，鸟哥个人认为， PATH 与 LANG （如果有使用到输出相关的信息时） 是当中最重要的！ 如此一来，则可让我们这支程序在进行时，可以直接下达一些外部指令，而不必写绝对路径呢！比较方便啦！
4. 主要程序部分 就将主要的程序写好即可！在这个例子当中，就是 echo 那一行啦！
5. 执行成果告知 （定义回传值） 是否记得我们在[第十章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)里面要讨论一个指令的执行成功与否，可以使用 [$?](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#redirect_com) 这个变量来观察～ 那么我们也可以利用 exit 这个指令来让程序中断，并且回传一个数值给系统。 在我们这个例子当中，鸟哥使用 exit 0 ，这代表离开 script 并且回传一个 0 给系统， 所以我执行完这个 script 后，若接着下达 echo $? 则可得到 0 的值喔！ 更聪明的读者应该也知道了，呵呵！利用这个 exit n （n 是数字） 的功能，我们还可以自订错误讯息， 让这支程序变得更加的 smart 呢！

接下来通过刚刚上头介绍的执行方法来执行看看结果吧！

```
[dmtsai@study bin]$ sh hello.sh
Hello World !
```

你会看到屏幕是这样，而且应该还会听到“咚”的一声，为什么呢？还记得前一章提到的 [printf](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#printf) 吧？用 echo 接着那些特殊的按键也可以发生同样的事情～ 不过， echo 必须要加上 -e 的选项才行！呵呵！在你写完这个小 script 之后，你就可以大声的说：“我也会写程序了”！哈哈！ 很简单有趣吧～ ^_^

另外，你也可以利用：“chmod a+x hello.sh; ./hello.sh”来执行这个 script 的呢！





### 12.1.3 编写 Shell Script 的良好习惯

良好的编写习惯可以提高代码的可读性和可维护性：

1. **注释说明**：
   - 脚本功能
   - 版本信息
   - 作者与联系方式
   - 版权声明
   - 历史记录
2. **环境变量设置**：
   - 定义重要的环境变量，如 PATH
3. **代码注释**：
   - 在代码中添加注释，解释复杂的逻辑和功能
4. **结构清晰**：
   - 使用缩进和空行，使代码结构清晰，便于阅读和调试
5. **使用绝对路径**：
   - 在脚本中使用绝对路径，以避免路径问题
6. **使用 Vim 编写**：
   - 使用 Vim 而不是 Vi，因为 Vim 提供了语法高亮和语法检查功能，有助于发现语法错误

**原文：**

一个良好习惯的养成是很重要的～大家在刚开始撰写程序的时候，最容易忽略这部分， 认为程序写出来就好了，其他的不重要。其实，如果程序的说明能够更清楚，那么对你自己是有很大的帮助的。

举例来说，鸟哥自己为了自己的需求，曾经撰写了不少的 script 来帮我进行主机 IP 的侦测啊、 登录文件分析与管理啊、自动上传下载重要配置文件啊等等的，不过，早期就是因为太懒了， 管理的主机又太多了，常常同一个程序在不同的主机上面进行更改，到最后，到底哪一支才是最新的都记不起来， 而且，重点是，我到底是改了哪里？为什么做那样的修改？都忘的一干二净～真要命～

所以，后来鸟哥在写程序的时候，通常会比较仔细的将程序的设计过程给他记录下来，而且还会记录一些历史纪录， 如此一来，好多了～至少很容易知道我修改了哪些数据，以及程序修改的理念与逻辑概念等等， 在维护上面是轻松很多很多的喔！

另外，在一些环境的设置上面，毕竟每个人的环境都不相同，为了取得较佳的执行环境， 我都会自行先定义好一些一定会被用到的环境变量，例如 PATH 这个玩意儿！ 这样比较好啦～所以说，建议你一定要养成良好的 script 撰写习惯，在每个 script 的文件开始处记录好：

- script 的功能；
- script 的版本信息；
- script 的作者与联络方式；
- script 的版权宣告方式；
- script 的 History （历史纪录）；
- script 内较特殊的指令，使用“绝对路径”的方式来下达；
- script 运行时需要的环境变量预先宣告与设置。

除了记录这些信息之外，在较为特殊的程序码部分，个人建议务必要加上注解说明，可以帮助你非常非常多！ 此外，程序码的撰写最好使用巢状方式，在包覆的内部程序码最好能以 [tab] 按键的空格向后推， 这样你的程序码会显的非常的漂亮与有条理！在查阅与 debug 上较为轻松愉快喔！ 另外，使用撰写 script 的工具最好使用 vim 而不是 vi ，因为 vim 会有额外的语法检验机制，能够在第一阶段撰写时就发现语法方面的问题喔！

## 12.2 简单的 Shell Script 练习

在编写完第一支 shell script 之后，现在可以进行一些简单的练习。这些练习可以帮助你更好地理解 shell script 的基本概念和用法。

### 12.2.1 简单范例

以下是一些常见的脚本范例，这些脚本在实际使用中非常有用。

#### 对谈式脚本：变量内容由使用者决定

此脚本将提示用户输入他们的名字，并显示完整名字。

```
#!/bin/bash
# Program:
#    User inputs his first name and last name. Program shows his full name.
# History:
# 2024/07/23    User    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "Please input your first name: " firstname      # 提示用户输入
read -p "Please input your last name:  " lastname       # 提示用户输入
echo -e "\nYour full name is: ${firstname} ${lastname}" # 显示结果
```

#### 随日期变化：利用 date 进行文件的创建

此脚本会创建三个文件，文件名包含使用者输入的前缀和前天、昨天、今天的日期。

```
#!/bin/bash
# Program:
#    Program creates three files, which named by user's input and date command.
# History:
# 2024/07/23    User    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo -e "I will use 'touch' command to create 3 files." # 显示信息
read -p "Please input your filename: " fileuser         # 提示用户输入

filename=${fileuser:-"filename"}           # 判断是否设置文件名

date1=$(date --date='2 days ago' +%Y%m%d)  # 前两天的日期
date2=$(date --date='1 days ago' +%Y%m%d)  # 前一天的日期
date3=$(date +%Y%m%d)                      # 今天的日期
file1=${filename}${date1}                  # 设置文件名
file2=${filename}${date2}
file3=${filename}${date3}

touch "${file1}"                           # 创建文件
touch "${file2}"
touch "${file3}"
```

#### 数值运算：简单的加减乘除

此脚本提示用户输入两个整数，并显示它们的乘积。

```
#!/bin/bash
# Program:
#    User inputs 2 integer numbers; program will multiply these two numbers.
# History:
# 2024/07/23    User    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo -e "You SHOULD input 2 numbers, I will multiply them! \n"
read -p "first number:  " firstnu
read -p "second number: " secnu
total=$((firstnu * secnu))
echo -e "\nThe result of ${firstnu} x ${secnu} is ==> ${total}"
```

#### 数值运算：通过 bc 计算 pi

此脚本提示用户输入一个精度值，并使用 bc 计算 pi 的值。

```
#!/bin/bash
# Program:
#    User inputs a scale number to calculate pi value.
# History:
# 2024/07/23    User    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo -e "This program will calculate pi value. \n"
echo -e "You should input a float number to calculate pi value.\n"
read -p "The scale number (10~10000)? " checking
num=${checking:-"10"}           # 判断是否输入数值
echo -e "Starting to calculate pi value. Be patient."
time echo "scale=${num}; 4*a(1)" | bc -lq
```

**原文：**

- 对谈式脚本：变量内容由使用者决定

很多时候我们需要使用者输入一些内容，好让程序可以顺利运行。 简单的来说，大家应该都有安装过软件的经验，安装的时候，他不是会问你“要安装到那个目录去”吗？ 那个让使用者输入数据的动作，就是让使用者输入变量内容啦。

你应该还记得在[十章 bash](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 的时候，我们有学到一个 [read](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#read) 指令吧？现在，请你以 read 指令的用途，撰写一个 script ，他可以让使用者输入：1. first name 与 2. last name， 最后并且在屏幕上显示：“Your full name is: ”的内容：

```
[dmtsai@study bin]$ vim showname.sh
#!/bin/bash
# Program:
#    User inputs his first name and last name.  Program shows his full name.
# History:
# 2015/07/16    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "Please input your first name: " firstname      # 提示使用者输入
read -p "Please input your last name:  " lastname       # 提示使用者输入
echo -e "\nYour full name is: ${firstname} ${lastname}" # 结果由屏幕输出
```

将上面这个 showname.sh 执行一下，你就能够发现使用者自己输入的变量可以让程序所取用，并且将他显示到屏幕上！ 接下来，如果想要制作一个每次执行都会依据不同的日期而变化结果的脚本呢？

- 随日期变化：利用 date 进行文件的创建

想像一个状况，假设我的服务器内有数据库，数据库每天的数据都不太一样，因此当我备份时，希望将每天的数据都备份成不同的文件名， 这样才能够让旧的数据也能够保存下来不被覆盖。哇！不同文件名呢！这真困扰啊？难道要我每天去修改 script ？

不需要啊！考虑每天的“日期”并不相同，所以我可以将文件名取成类似： backup.2015-07-16.data ， 不就可以每天一个不同文件名了吗？呵呵！确实如此。那个 2015-07-16 怎么来的？那就是重点啦！接下来出个相关的例子： 假设我想要创建三个空的文件 （通过 [touch](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#touch)） ，文件名最开头由使用者输入决定，假设使用者输入 filename 好了，那今天的日期是 2015/07/16 ， 我想要以前天、昨天、今天的日期来创建这些文件，亦即 filename_20150714, filename_20150715, filename_20150716 ，该如何是好？

```
[dmtsai@study bin]$ vim create_3_filename.sh
#!/bin/bash
# Program:
#    Program creates three files, which named by user's input and date command.
# History:
# 2015/07/16    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

# 1\. 让使用者输入文件名称，并取得 fileuser 这个变量；
echo -e "I will use 'touch' command to create 3 files." # 纯粹显示信息
read -p "Please input your filename: " fileuser         # 提示使用者输入

# 2\. 为了避免使用者随意按 Enter ，利用[变量功能](../Text/index.html#variable_other_re)分析文件名是否有设置？
filename=${fileuser:-"filename"}           # 开始判断有否配置文件名

# 3\. 开始利用 date 指令来取得所需要的文件名了；
date1=$（date --date='2 days ago' +%Y%m%d）  # 前两天的日期
date2=$（date --date='1 days ago' +%Y%m%d）  # 前一天的日期
date3=$（date +%Y%m%d）                      # 今天的日期
file1=${filename}${date1}                  # 下面三行在配置文件名
file2=${filename}${date2}
file3=${filename}${date3}

# 4\. 将文件名创建吧！
touch "${file1}"                           # 下面三行在创建文件
touch "${file2}"
touch "${file3}"
```

上面的范例鸟哥使用了很多在[第十章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)介绍过的概念： 包括小指令“ $（command） ”的取得讯息、变量的设置功能、变量的累加以及利用 touch 指令辅助！ 如果你开始执行这个 create_3_filename.sh 之后，你可以进行两次执行：一次直接按 [Enter] 来查阅文件名是啥？ 一次可以输入一些字符，这样可以判断你的脚本是否设计正确喔！

- 数值运算：简单的加减乘除

各位看官应该还记得，我们可以使用 [declare](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#declare) 来定义变量的类型吧？ 当变量定义成为整数后才能够进行加减运算啊！此外，我们也可以利用“ $（（计算式）） ”来进行数值运算的。 可惜的是， bash shell 里头默认仅支持到整数的数据而已。OK！那我们来玩玩看，如果我们要使用者输入两个变量， 然后将两个变量的内容相乘，最后输出相乘的结果，那可以怎么做？

```
[dmtsai@study bin]$ vim multiplying.sh
#!/bin/bash
# Program:
#    User inputs 2 integer numbers; program will cross these two numbers.
# History:
# 2015/07/16    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo -e "You SHOULD input 2 numbers, I will multiplying them! \n"
read -p "first number:  " firstnu
read -p "second number: " secnu
total=$（（${firstnu}*${secnu}））
echo -e "\nThe result of ${firstnu} x ${secnu} is ==&gt; ${total}"
```

在数值的运算上，我们可以使用“ declare -i total=${firstnu}*${secnu} ” 也可以使用上面的方式来进行！基本上，鸟哥比较建议使用这样的方式来进行运算：

> var=$（（运算内容））

不但容易记忆，而且也比较方便的多，因为两个小括号内可以加上空白字符喔！ 未来你可以使用这种方式来计算的呀！至于数值运算上的处理，则有：“ +, -, *, /, % ”等等。 那个 % 是取余数啦～举例来说， 13 对 3 取余数，结果是 13=4*3+1，所以余数是 1 啊！就是：

```
[dmtsai@study bin]$ echo $（（ 13 % 3 ））
1
```

这样了解了吧？另外，如果你想要计算含有小数点的数据时，其实可以通过 [bc](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#bc) 这个指令的协助喔！ 例如可以这样做：

```
[dmtsai@study bin]$ echo "123.123*55.9" &#124; bc
6882.575
```

了解了 bc 的妙用之后，来让我们测试一下如何计算 pi 这个东西呢？

- 数值运算：通过 bc 计算 pi

其实计算 pi 时，小数点以下位数可以无限制的延伸下去！而 bc 有提供一个运算 pi 的函数，只是想要使用该函数必须要使用 bc -l 来调用才行。 也因为这个小数点以下位数可以无线延伸运算的特性存在，所以我们可以通过下面这只小脚本来让使用者输入一个“小数点为数值”， 以让 pi 能够更准确！

```
[dmtsai@study bin]$ vim cal_pi.sh
#!/bin/bash
# Program:
#    User input a scale number to calculate pi number.
# History:
# 2015/07/16    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo -e "This program will calculate pi value. \n"
echo -e "You should input a float number to calculate pi value.\n"
read -p "The scale number （10~10000） ? " checking
num=${checking:-"10"}           # 开始判断有否有输入数值
echo -e "Starting calcuate pi value.  Be patient."
time echo "scale=${num}; 4*a（1）" &#124; bc -lq
```

上述数据中，那个 4*a（1） 是 bc 主动提供的一个计算 pi 的函数，至于 scale 就是要 bc 计算几个小数点下位数的意思。当 scale 的数值越大， 代表 pi 要被计算的越精确，当然用掉的时间就会越多！因此，你可以尝试输入不同的数值看看！不过，最好是不要超过 5000 啦！因为会算很久！ 如果你要让你的 CPU 随时保持在高负载，这个程序算下去你就会知道有多操 CPU 啰！ ^_^

![鸟哥的图示](image/vbird_face.gif)

**Tips** 鸟哥的实验室中，为了要确认虚拟机的效率问题，所以很多时候需要保持虚拟机在忙碌的状态～鸟哥的学生就是丢这只程序进去系统跑！ 但是将 scale 调高一些，那计算就得要花比较多时间～用以达到我们需要 CPU 忙碌的状态喔！

### 12.2.2 Script 的执行方式差异（source, sh script, ./script）

不同的 script 执行方式会影响脚本的执行结果，特别是对 bash 环境变量的影响很大。脚本的执行方式包括：

#### 利用直接执行的方式来执行 script

当使用绝对路径、相对路径或 PATH 环境变量中的路径来执行脚本时，脚本会在子进程中运行，父进程中的变量不会受到影响。

```
#!/bin/bash
# showname.sh
# Program:
#    User inputs his first name and last name. Program shows his full name.
# History:
# 2024/07/23    User    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "Please input your first name: " firstname
read -p "Please input your last name:  " lastname
echo -e "\nYour full name is: ${firstname} ${lastname}"
```

执行脚本：

```
sh showname.sh
```

此时，脚本中的变量不会在父进程中生效。

#### 利用 source 来执行脚本

使用 source 或 .（点号）来执行脚本时，脚本会在当前 shell 环境中执行，变量在父进程中生效。

```
source showname.sh
```

此时，脚本中的变量会在父进程中生效，可以在当前 shell 中使用。

通过这些练习，你可以更好地理解 shell script 的基本用法和执行方式。继续进行更复杂的练习，你会发现 shell script 是管理和自动化 Linux 系统的强大工具。



补充：

```
shell 中我们可以在目录使用hello.sh之前最好要指定下环境变量，要不然我们就可以使用./hello.sh，按照上面叙述，可以指定设置好环境变量，直接使用sh hello.sh或. hello.sh
如果执行C语言可执行文件的话，直接打hello可执行命令会报错，找不到路径可能，也是使用. hello会好点
```



**原文:**

不同的 script 执行方式会造成不一样的结果喔！尤其影响 bash 的环境很大呢！脚本的执行方式除了[前面小节谈到的方式](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#script_run)之外，还可以利用 [source](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#source) 或小数点 （.） 来执行喔！那么这种执行方式有何不同呢？当然是不同的啦！让我们来说说！

- 利用直接执行的方式来执行 script

当使用前一小节提到的直接指令下达 （不论是绝对路径/相对路径还是 ${PATH} 内），或者是利用 bash （或 sh） 来下达脚本时， 该 script 都会使用一个新的 bash 环境来执行脚本内的指令！也就是说，使用这种执行方式时， 其实 script 是在子程序的 bash 内执行的！我们在[第十章 BASH](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 内谈到 [export](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#export) 的功能时，曾经就父程序/子程序谈过一些概念性的问题， 重点在于：“当子程序完成后，在子程序内的各项变量或动作将会结束而不会传回到父程序中”！ 这是什么意思呢？

我们举刚刚提到过的 showname.sh 这个脚本来说明好了，这个脚本可以让使用者自行设置两个变量，分别是 firstname 与 lastname，想一想，如果你直接执行该指令时，该指令帮你设置的 firstname 会不会生效？看一下下面的执行结果：

```
[dmtsai@study bin]$ echo ${firstname} ${lastname}
    &lt;==确认了，这两个变量并不存在喔！
[dmtsai@study bin]$ sh showname.sh
Please input your first name: VBird &lt;==这个名字是鸟哥自己输入的
Please input your last name:  Tsai 

Your full name is: VBird Tsai      &lt;==看吧！在 script 运行中，这两个变量有生效
[dmtsai@study bin]$ echo ${firstname} ${lastname}
    &lt;==事实上，这两个变量在父程序的 bash 中还是不存在的！
```

上面的结果你应该会觉得很奇怪，怎么我已经利用 showname.sh 设置好的变量竟然在 bash 环境下面无效！怎么回事呢？ 如果将程序相关性绘制成图的话，我们以下图来说明。当你使用直接执行的方法来处理时，系统会给予一支新的 bash 让我们来执行 showname.sh 里面的指令，因此你的 firstname, lastname 等变量其实是在下图中的子程序 bash 内执行的。 当 showname.sh 执行完毕后，子程序 bash 内的所有数据便被移除，因此上表的练习中，在父程序下面 echo ${firstname} 时， 就看不到任何东西了！这样可以理解吗？

![showname.sh 在子程序当中运行的示意图](image/centos7_non-source.gif)图12.2.1、showname.sh 在子程序当中运行的示意图

- 利用 source 来执行脚本：在父程序中执行

如果你使用 source 来执行指令那就不一样了！同样的脚本我们来执行看看：

```
[dmtsai@study bin]$ source showname.sh
Please input your first name: VBird
Please input your last name:  Tsai

Your full name is: VBird Tsai
[dmtsai@study bin]$ echo ${firstname} ${lastname}
VBird Tsai  &lt;==嘿嘿！有数据产生喔！
```

竟然生效了！没错啊！因为 source 对 script 的执行方式可以使用下面的图示来说明！ showname.sh 会在父程序中执行的，因此各项动作都会在原本的 bash 内生效！这也是为啥你不登出系统而要让某些写入 ~/.bashrc 的设置生效时，需要使用“ source ~/.bashrc ”而不能使用“ bash ~/.bashrc ”是一样的啊！

![showname.sh 在父程序当中运行的示意图](image/centos7_source.gif)

图12.2.2、showname.sh 在父程序当中运行的示意图

**补充：**

使用 `. hello.sh` 或 `source hello.sh` 来执行脚本所做的环境变量修改只会影响当前的 shell 会话，并不会在 shell 会话结束后永久保留。这些修改仅在当前 shell 会话中有效，当你关闭这个 shell 会话或者打开一个新的 shell 会话时，这些修改都不会再存在。

### 具体说明

1. **当前 shell 会话的修改**： 使用 `. hello.sh` 或 `source hello.sh` 来执行脚本，会在当前 shell 会话中运行脚本中的命令，所有的环境变量修改都会立即生效并影响当前的 shell 会话。

   示例：

   ```
   echo $PATH
   # 输出: /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
   
   . hello.sh
   # 期间输入名字和姓氏
   
   echo $PATH
   # 输出: /bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
   ```

2. **关闭 shell 会话后**： 当你关闭当前的 shell 会话，所有的环境变量修改都会丢失。新的 shell 会话将会使用系统默认的环境变量设置，或者从用户的配置文件中读取。

   示例：

   ```
   exit  # 关闭当前 shell 会话
   
   # 打开一个新的 shell 会话
   echo $PATH
   # 输出: /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
   ```

### 如何永久修改环境变量

如果你希望永久修改 `PATH` 环境变量，你需要将修改写入到用户的 shell 配置文件中，例如 `~/.bashrc`、`~/.bash_profile`、`~/.profile` 或者 `~/.zshrc`（取决于你使用的 shell）。这些文件会在每次启动一个新的 shell 会话时被读取和执行，从而确保环境变量的修改是持久的。

### 示例：永久修改 `PATH`

1. **编辑配置文件**： 假设你使用的是 Bash，可以编辑 `~/.bashrc` 文件：

   ```
   nano ~/.bashrc
   ```

2. **添加 `PATH` 修改**： 在文件末尾添加如下内容：

   ```
   export PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
   ```

3. **使修改生效**： 保存文件并退出，然后运行以下命令使修改立即生效：

   ```
   source ~/.bashrc
   ```

4. **验证修改**： 现在，无论何时启动新的 shell 会话，`PATH` 环境变量都将包含你添加的路径：

   ```
   echo $PATH
   # 输出: /bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
   ```

### 总结

- 使用 `. hello.sh` 或 `source hello.sh` 只会在当前 shell 会话中修改环境变量，关闭会话后这些修改不会保留。
- 要永久修改环境变量，需要将修改写入用户的 shell 配置文件，如 `~/.bashrc` 或 `~/.bash_profile`。
- 通过编辑配置文件并使其生效，可以确保环境变量的修改在每次新的 shell 会话中都会生效。



## 12.3 善用判断式

在第十章中，我们提到过 `$?` 这个变量所代表的意义。此外，也通过 `&&` 及 `||` 来作为前一个指令执行回传值对于后一个指令是否要进行的依据。如果想要判断一个目录是否存在，当时我们使用的是 `ls` 这个指令搭配数据流重导向，最后配合 `$?` 来决定后续的指令进行与否。但是，是否有更简单的方式可以来进行“条件判断”呢？有的，那就是 `test` 这个指令。

### 12.3.1 利用 test 指令的测试功能

当我要检测系统上面某些文件或者是相关的属性时，利用 `test` 这个指令来工作非常方便。例如我要检查 `/dmtsai` 是否存在时，使用：

```
[dmtsai@study ~]$ test -e /dmtsai
```

执行结果并不会显示任何讯息，但最后我们可以通过 `$?` 或 `&&` 及 `||` 来展现结果。例如：

```
[dmtsai@study ~]$ test -e /dmtsai && echo "exist" || echo "Not exist"
Not exist  # 结果显示不存在
```

这里我们知道 `-e` 是测试一个“东西”是否存在，如果还想要测试该文件名是什么类型，还有其他标志可以使用：

#### 文件类型判断

- **`-e`：该“文件名”是否存在（常用）。**
- **`-f`：该“文件名”是否存在且为文件（常用）。**
- **`-d`：该“文件名”是否存在且为目录（常用）。**
- `-b`：该“文件名”是否存在且为一个块设备。
- `-c`：该“文件名”是否存在且为一个字符设备。
- `-S`：该“文件名”是否存在且为一个套接字文件。
- `-p`：该“文件名”是否存在且为一个 FIFO（命名管道）。
- `-L`：该“文件名”是否存在且为一个符号链接文件。

#### 文件权限侦测

- `-r`：侦测该文件名是否存在且具有“可读”的权限。
- `-w`：侦测该文件名是否存在且具有“可写”的权限。
- `-x`：侦测该文件名是否存在且具有“可执行”的权限。
- `-u`：侦测该文件名是否存在且具有“SUID”的属性。
- `-g`：侦测该文件名是否存在且具有“SGID”的属性。
- `-k`：侦测该文件名是否存在且具有“粘着位”的属性。
- `-s`：侦测该文件名是否存在且为“非空白文件”。

#### 两个文件之间的比较

- `-nt`：判断 `file1` 是否比 `file2` 新。
- `-ot`：判断 `file1` 是否比 `file2` 旧。
- `-ef`：判断 `file1` 与 `file2` 是否为同一文件（硬链接）。

#### 两个整数之间的判定

- `-eq`：两数值相等（equal）。
- `-ne`：两数值不等（not equal）。
- `-gt`：`n1` 大于 `n2`（greater than）。
- `-lt`：`n1` 小于 `n2`（less than）。
- `-ge`：`n1` 大于等于 `n2`（greater than or equal）。
- `-le`：`n1` 小于等于 `n2`（less than or equal）。

#### 字符串数据的判定

- `-z string`：判定字符串是否为空，为空返回 true。
- `-n string`：判定字符串是否非空，为非空返回 true。`-n` 可以省略。
- `str1 == str2`：判定 `str1` 是否等于 `str2`，相等返回 true。
- `str1 != str2`：判定 `str1` 是否不等于 `str2`，不等返回 true。

#### 多重条件判定

- `-a`：（and）两状况同时成立。
- `-o`：（or）两状况任何一个成立。
- `!`：反相状态，如 `test ! -x file`，当 `file` 不具有 `x` 时，返回 true。

利用 `test` 来写几个简单的例子。首先，判断一下，让用户输入一个文件名，判断：

1. 这个文件是否存在，若不存在则给予一个“Filename does not exist”的讯息，并中断程序。
2. 若这个文件存在，则判断是文件还是目录，输出“Filename is regular file”或“Filename is directory”。
3. 判断执行者的身份对这个文件或目录所拥有的权限，并输出权限数据。

```
[dmtsai@study bin]$ vim file_perm.sh
#!/bin/bash
# Program:
#    User inputs a filename, program will check the following:
#    1.) exist? 2.) file/directory? 3.) file permissions
# History:
# 2024/07/23    User    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

# 1. 让用户输入文件名，并判断是否有输入
echo -e "Please input a filename, I will check the filename's type and permission.\n\n"
read -p "Input a filename : " filename
test -z ${filename} && echo "You MUST input a filename." && exit 0
# 2. 判断文件是否存在
test ! -e ${filename} && echo "The filename '${filename}' DO NOT exist" && exit 0
# 3. 判断文件类型与属性
test -f ${filename} && filetype="regular file"
test -d ${filename} && filetype="directory"
test -r ${filename} && perm="readable"
test -w ${filename} && perm="${perm} writable"
test -x ${filename} && perm="${perm} executable"
# 4. 输出信息
echo "The filename: ${filename} is a ${filetype}"
echo "And the permissions for you are : ${perm}"
```

**原文：**

当我要检测系统上面某些文件或者是相关的属性时，利用 test 这个指令来工作真是好用得不得了， 举例来说，我要检查 /dmtsai 是否存在时，使用：

```
[dmtsai@study ~]$ test -e /dmtsai
```

执行结果并不会显示任何讯息，但最后我们可以通过 $? 或 && 及 || 来展现整个结果呢！ 例如我们在将上面的例子改写成这样：

```
[dmtsai@study ~]$ test -e /dmtsai && echo "exist" &#124;&#124; echo "Not exist"
Not exist  &lt;==结果显示不存在啊！
```

最终的结果可以告知我们是“exist”还是“Not exist”呢！那我知道 -e 是测试一个“东西”在不在， 如果还想要测试一下该文件名是啥玩意儿时，还有哪些标志可以来判断的呢？呵呵！有下面这些东西喔！

| 测试的标志                                                   | 代表意义                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1. 关于某个文件名的“文件类型”判断，如 test -e filename 表示存在否 |                                                              |
| -e                                                           | 该“文件名”是否存在？（常用）                                 |
| -f                                                           | 该“文件名”是否存在且为文件（file）？（常用）                 |
| -d                                                           | 该“文件名”是否存在且为目录（directory）？（常用）            |
| -b                                                           | 该“文件名”是否存在且为一个 block device 设备？               |
| -c                                                           | 该“文件名”是否存在且为一个 character device 设备？           |
| -S                                                           | 该“文件名”是否存在且为一个 Socket 文件？                     |
| -p                                                           | 该“文件名”是否存在且为一个 FIFO （pipe） 文件？              |
| -L                                                           | 该“文件名”是否存在且为一个链接文件？                         |
| 2. 关于文件的权限侦测，如 test -r filename 表示可读否 （但 root 权限常有例外） |                                                              |
| -r                                                           | 侦测该文件名是否存在且具有“可读”的权限？                     |
| -w                                                           | 侦测该文件名是否存在且具有“可写”的权限？                     |
| -x                                                           | 侦测该文件名是否存在且具有“可执行”的权限？                   |
| -u                                                           | 侦测该文件名是否存在且具有“SUID”的属性？                     |
| -g                                                           | 侦测该文件名是否存在且具有“SGID”的属性？                     |
| -k                                                           | 侦测该文件名是否存在且具有“Sticky bit”的属性？               |
| -s                                                           | 侦测该文件名是否存在且为“非空白文件”？                       |
| 3. 两个文件之间的比较，如： test file1 -nt file2             |                                                              |
| -nt                                                          | （newer than）判断 file1 是否比 file2 新                     |
| -ot                                                          | （older than）判断 file1 是否比 file2 旧                     |
| -ef                                                          | 判断 file1 与 file2 是否为同一文件，可用在判断 hard link 的判定上。 主要意义在判定，两个文件是否均指向同一个 inode 哩！ |
| 4. 关于两个整数之间的判定，例如 test n1 -eq n2               |                                                              |
| -eq                                                          | 两数值相等 （equal）                                         |
| -ne                                                          | 两数值不等 （not equal）                                     |
| -gt                                                          | n1 大于 n2 （greater than）                                  |
| -lt                                                          | n1 小于 n2 （less than）                                     |
| -ge                                                          | n1 大于等于 n2 （greater than or equal）                     |
| -le                                                          | n1 小于等于 n2 （less than or equal）                        |
| 5. 判定字串的数据                                            |                                                              |
| test -z string                                               | 判定字串是否为 0 ？若 string 为空字串，则为 true             |
| test -n string                                               | 判定字串是否非为 0 ？若 string 为空字串，则为 false。 -n 亦可省略 |
| test str1 == str2                                            | 判定 str1 是否等于 str2 ，若相等，则回传 true                |
| test str1 != str2                                            | 判定 str1 是否不等于 str2 ，若相等，则回传 false             |
| 6. 多重条件判定，例如： test -r filename -a -x filename      |                                                              |
| -a                                                           | （and）两状况同时成立！例如 test -r file -a -x file，则 file 同时具有 r 与 x 权限时，才回传 true。 |
| -o                                                           | （or）两状况任何一个成立！例如 test -r file -o -x file，则 file 具有 r 或 x 权限时，就可回传 true。 |
| !                                                            | 反相状态，如 test ! -x file ，当 file 不具有 x 时，回传 true |

OK！现在我们就利用 test 来帮我们写几个简单的例子。首先，判断一下，让使用者输入一个文件名，我们判断：

1. 这个文件是否存在，若不存在则给予一个“Filename does not exist”的讯息，并中断程序；
2. 若这个文件存在，则判断他是个文件或目录，结果输出“Filename is regular file”或 “Filename is directory”
3. 判断一下，执行者的身份对这个文件或目录所拥有的权限，并输出权限数据！

你可以先自行创作看看，然后再跟下面的结果讨论讨论。注意利用 test 与 && 还有 || 等标志！

```
[dmtsai@study bin]$ vim file_perm.sh
#!/bin/bash
# Program:
#    User input a filename, program will check the flowing:
#    1.） exist? 2.） file/directory? 3.） file permissions
# History:
# 2015/07/16    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

# 1\. 让使用者输入文件名，并且判断使用者是否真的有输入字串？
echo -e "Please input a filename, I will check the filename's type and permission. \n\n"
read -p "Input a filename : " filename
test -z ${filename} && echo "You MUST input a filename." && exit 0
# 2\. 判断文件是否存在？若不存在则显示讯息并结束脚本
test ! -e ${filename} && echo "The filename '${filename}' DO NOT exist" && exit 0
# 3\. 开始判断文件类型与属性
test -f ${filename} && filetype="regulare file"
test -d ${filename} && filetype="directory"
test -r ${filename} && perm="readable"
test -w ${filename} && perm="${perm} writable"
test -x ${filename} && perm="${perm} executable"
# 4\. 开始输出信息！
echo "The filename: ${filename} is a ${filetype}"
echo "And the permissions for you are : ${perm}"
```

如果你执行这个脚本后，他会依据你输入的文件名来进行检查喔！先看是否存在，再看为文件或目录类型，最后判断权限。 但是你必须要注意的是，由于 root 在很多权限的限制上面都是无效的，所以使用 root 执行这个脚本时， 常常会发现与 ls -l 观察到的结果并不相同！所以，建议使用一般使用者来执行这个脚本试看看。



### 12.3.2 利用判断符号 `[` `]`

除了 `test` 之外，还可以利用判断符号 `[` `]` 来进行数据的判断。例如，要判断 `${HOME}` 这个变量是否为空：

```
[dmtsai@study ~]$ [ -z "${HOME}" ] ; echo $?
```

使用中括号必须特别注意，==**因为中括号用在很多地方，包括万用字符与正则表达式等。如果要在 bash 的语法中使用中括号作为 shell 的判断式时，必须确保中括号的两端有空格字符来分隔**==。例如：

```
[ "${HOME}" == "${MAIL}" ]
```

必须确保每个元素之间有空格分隔。此外，变量最好都以双引号括起来，常数最好都以单或双引号括起来。比如：

```
[ "${name}" == "VBird" ]
```

**如果没有空格分隔，会出现错误信息。因此要特别注意这些细节。**

利用中括号的判断来做一个小案例：

1. 当执行一个程序时，程序会让用户选择 Y 或 N。
2. 如果用户输入 Y 或 y，则显示“OK, continue”。
3. 如果用户输入 N 或 n，则显示“Oh, interrupt!”。
4. 如果不是 Y/y/N/n 之内的其他字符，则显示“I don't know what your choice is”。

```
[dmtsai@study bin]$ vim ans_yn.sh
#!/bin/bash
# Program:
#    This program shows the user's choice
# History:
# 2024/07/23    User    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "Please input (Y/N): " yn
[ "${yn}" == "Y" -o "${yn}" == "y" ] && echo "OK, continue" && exit 0
[ "${yn}" == "N" -o "${yn}" == "n" ] && echo "Oh, interrupt!" && exit 0
echo "I don't know what your choice is" && exit 0
```

**原文：**

除了我们很喜欢使用的 test 之外，其实，我们还可以利用判断符号“ [ ] ”（就是中括号啦） 来进行数据的判断呢！ 举例来说，如果我想要知道 ${HOME} 这个变量是否为空的，可以这样做：

```
[dmtsai@study ~]$ [ -z "${HOME}" ] ; echo $?
```

使用中括号必须要特别注意，因为中括号用在很多地方，包括万用字符与正则表达式等等，所以如果要在 bash 的语法当中使用中括号作为 shell 的判断式时，必须要注意中括号的两端需要有空白字符来分隔喔！ 假设我空白键使用“□”符号来表示，那么，在这些地方你都需要有空白键：

```
[  "$HOME"  ==  "$MAIL"  ]
[□"$HOME"□==□"$MAIL"□]
 ↑       ↑  ↑       ↑
```

![鸟哥的图示](image/vbird_face-17224065389024.gif)

**Tips** 你会发现鸟哥在上面的判断式当中使用了两个等号“ == ”。其实在 bash 当中使用一个等号与两个等号的结果是一样的！ 不过在一般惯用程序的写法中，一个等号代表“变量的设置”，两个等号则是代表“逻辑判断 （是与否之意）”。 由于我们在中括号内重点在于“判断”而非“设置变量”，因此鸟哥建议您还是使用两个等号较佳！

上面的例子在说明，两个字串 ${HOME} 与 ${MAIL} 是否相同的意思，相当于 test ${HOME} == ${MAIL} 的意思啦！ 而如果没有空白分隔，例如 [${HOME}==${MAIL}] 时，我们的 bash 就会显示错误讯息了！这可要很注意啊！ 所以说，你最好要注意：

- 在中括号 [] 内的每个元件都需要有空白键来分隔；
- 在中括号内的变量，最好都以双引号括号起来；
- 在中括号内的常数，最好都以单或双引号括号起来。

为什么要这么麻烦啊？直接举例来说，假如我设置了 name="VBird Tsai" ，然后这样判定：

```
[dmtsai@study ~]$ name="VBird Tsai"
[dmtsai@study ~]$ [ ${name} == "VBird" ]
bash: [: too many arguments
```

见鬼了！怎么会发生错误啊？bash 还跟我说错误是由于“太多参数 （arguments）”所致！ 为什么呢？因为 ${name} 如果没有使用双引号刮起来，那么上面的判定式会变成：

> [ VBird Tsai == "VBird" ]

上面肯定不对嘛！因为一个判断式仅能有两个数据的比对，上面 VBird 与 Tsai 还有 "VBird" 就有三个数据！ 这不是我们要的！我们要的应该是下面这个样子：

> [ "VBird Tsai" == "VBird" ]

这可是差很多的喔！另外，中括号的使用方法与 test 几乎一模一样啊～ 只是中括号比较常用在[条件判断式 if ..... then ..... fi](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#rule) 的情况中就是了。 好，那我们也使用中括号的判断来做一个小案例好了，案例设置如下：

1. 当执行一个程序的时候，这个程序会让使用者选择 Y 或 N ，
2. 如果使用者输入 Y 或 y 时，就显示“ OK, continue ”
3. 如果使用者输入 n 或 N 时，就显示“ Oh, interrupt ！”
4. 如果不是 Y/y/N/n 之内的其他字符，就显示“ I don't know what your choice is ”

利用中括号、 && 与 || 来继续吧！

```
[dmtsai@study bin]$ vim ans_yn.sh
#!/bin/bash
# Program:
#     This program shows the user's choice
# History:
# 2015/07/16    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "Please input （Y/N）: " yn
[ "${yn}" == "Y" -o "${yn}" == "y" ] && echo "OK, continue" && exit 0
[ "${yn}" == "N" -o "${yn}" == "n" ] && echo "Oh, interrupt!" && exit 0
echo "I don't know what your choice is" && exit 0
```

由于输入正确 （Yes） 的方法有大小写之分，不论输入大写 Y 或小写 y 都是可以的，此时判断式内就得要有两个判断才行！ 由于是任何一个成立即可 （大写或小写的 y） ，所以这里使用 -o （或） 链接两个判断喔！ 很有趣吧！利用这个字串判别的方法，我们就可以很轻松的将使用者想要进行的工作分门别类呢！ 接下来，我们再来谈一些其他有的没有的东西吧！



### 12.3.3 Shell script 的默认变量（$0, $1...）

指令可以带有选项与参数，例如 `ls -la` 可以查看包含隐藏文件的所有属性与权限。那么 shell script 能不能在脚本文件名后面带有参数呢？可以。我们来举个例子：

```
/path/to/scriptname opt1 opt2 opt3 opt4
       $0          $1  $2  $3  $4
```

这样执行时，脚本文件名为 `$0`，第一个参数为 `$1`。

通过特殊变量来调用这些参数，例如：

- `$#`：代表后接的参数个数，以上例中显示为 4。
- `$@`：代表 `"${1}" "${2}" "${3}" "${4}"`，每个变量是独立的（用双引号括起来）。
- `$*`：代表 `"${1}${IFS}${2}${IFS}${3}${IFS}${4}"`，其中 IFS 为分隔字符，默认为空白键。

这些变量可以用来处理不同的参数传递情况。让我们通过一个例子来展示这些变量的使用：

假设我们有一个脚本 `how_paras.sh`，执行该脚本后会输出以下内容：

- 程序的文件名
- 共有几个参数
- 如果参数的个数小于 2，则告知用户参数数量太少
- 全部的参数内容
- 第一个参数
- 第二个参数

```
[dmtsai@study bin]$ vim how_paras.sh
#!/bin/bash
# Program:
#    Program shows the script name, parameters...
# History:
# 2024/07/23    User    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo "The script name is        ==> ${0}"
echo "Total parameter number is ==> $#"
[ "$#" -lt 2 ] && echo "The number of parameter is less than 2. Stop here." && exit 0
echo "Your whole parameter is   ==> '$@'"
echo "The 1st parameter         ==> ${1}"
echo "The 2nd parameter         ==> ${2}"
```

执行结果如下：

```
[dmtsai@study bin]$ sh how_paras.sh theone haha quot
The script name is        ==> how_paras.sh       # 文件名
Total parameter number is ==> 3                  # 参数个数
Your whole parameter is   ==> 'theone haha quot' # 参数内容
The 1st parameter         ==> theone             # 第一个参数
The 2nd parameter         ==> haha               # 第二个参数
```

#### shift：造成参数变量号码偏移

除了上述的变量之外，还有一个 `shift` 命令可以进行参数偏移。以下是一个示例，展示了参数偏移的效果：

```
[dmtsai@study bin]$ vim shift_paras.sh
#!/bin/bash
# Program:
#    Program shows the effect of shift function.
# History:
# 2024/07/23    User    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo "Total parameter number is ==> $#"
echo "Your whole parameter is   ==> '$@'"
shift   # 进行第一次“一个变量的 shift ”
echo "Total parameter number is ==> $#"
echo "Your whole parameter is   ==> '$@'"
shift 3 # 进行第二次“三个变量的 shift ”
echo "Total parameter number is ==> $#"
echo "Your whole parameter is   ==> '$@'"
```

执行结果如下：

```
[dmtsai@study bin]$ sh shift_paras.sh one two three four five six
Total parameter number is ==> 6   # 最原始的参数变量情况
Your whole parameter is   ==> 'one two three four five six'
Total parameter number is ==> 5   # 第一次偏移，看下面发现第一个 one 不见了
Your whole parameter is   ==> 'two three four five six'
Total parameter number is ==> 2   # 第二次偏移掉三个，two three four 不见了
Your whole parameter is   ==> 'five six'
```

通过以上示例，你可以看到 `shift` 命令如何影响参数变量。第一次执行 `shift` 后，参数变量的个数减少一个，第二次执行 `shift 3` 后，参数变量的个数减少三个。这样可以方便地处理参数列表中的参数。

**原文：**

我们知道指令可以带有选项与参数，例如 ls -la 可以察看包含隐藏文件的所有属性与权限。那么 shell script 能不能在脚本文件名后面带有参数呢？很有趣喔！举例来说，如果你想要重新启动系统的网络，可以这样做：

```
[dmtsai@study ~]$ file /etc/init.d/network
/etc/init.d/network: Bourne-Again shell script, ASCII text executable
# 使用 file 来查询后，系统告知这个文件是个 bash 的可执行 script 喔！
[dmtsai@study ~]$ /etc/init.d/network restart
```

restart 是重新启动的意思，上面的指令可以“重新启动 /etc/init.d/network 这支程序”的意思！ 唔！那么如果你在 /etc/init.d/network 后面加上 stop 呢？没错！就可以直接关闭该服务了！这么神奇啊？ 没错啊！如果你要依据程序的执行给予一些变量去进行不同的任务时，本章一开始是使用 [read](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#ex_read) 的功能！但 read 功能的问题是你得要手动由键盘输入一些判断式。如果通过指令后面接参数， 那么一个指令就能够处理完毕而不需要手动再次输入一些变量行为！这样下达指令会比较简单方便啦！

script 是怎么达成这个功能的呢？其实 script 针对参数已经有设置好一些变量名称了！对应如下：

```
/path/to/scriptname  opt1  opt2  opt3  opt4
       $0             $1    $2    $3    $4
```

这样够清楚了吧？执行的脚本文件名为 $0 这个变量，第一个接的参数就是 $1 啊～ 所以，只要我们在 script 里面善用 $1 的话，就可以很简单的立即下达某些指令功能了！除了这些数字的变量之外， 我们还有一些较为特殊的变量可以在 script 内使用来调用这些参数喔！

- $# ：代表后接的参数“个数”，以上表为例这里显示为“ 4 ”；
- $@ ：代表“ "$1" "$2" "$3" "$4" ”之意，每个变量是独立的（用双引号括起来）；
- $* ：代表“ "$1<u>c</u>$2<u>c</u>$3<u>c</u>$4" ”，其中 <u>c</u> 为分隔字符，默认为空白键， 所以本例中代表“ "$1 $2 $3 $4" ”之意。

那个 $@ 与 $* 基本上还是有所不同啦！不过，一般使用情况下可以直接记忆 $@ 即可！ 好了，来做个例子吧～假设我要执行一个可以携带参数的 script ，执行该脚本后屏幕会显示如下的数据：

- 程序的文件名为何？
- 共有几个参数？
- 若参数的个数小于 2 则告知使用者参数数量太少
- 全部的参数内容为何？
- 第一个参数为何？
- 第二个参数为何

```
[dmtsai@study bin]$ vim how_paras.sh
#!/bin/bash
# Program:
#    Program shows the script name, parameters...
# History:
# 2015/07/16    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo "The script name is        ==&gt; ${0}"
echo "Total parameter number is ==&gt; $#"
[ "$#" -lt 2 ] && echo "The number of parameter is less than 2\.  Stop here." && exit 0
echo "Your whole parameter is   ==&gt; '$@'"
echo "The 1st parameter         ==&gt; ${1}"
echo "The 2nd parameter         ==&gt; ${2}"
```

执行结果如下：

```
[dmtsai@study bin]$ sh how_paras.sh theone haha quot
The script name is        ==&gt; how_paras.sh       &lt;==文件名
Total parameter number is ==&gt; 3                  &lt;==果然有三个参数
Your whole parameter is   ==&gt; 'theone haha quot' &lt;==参数的内容全部
The 1st parameter         ==&gt; theone             &lt;==第一个参数
The 2nd parameter         ==&gt; haha               &lt;==第二个参数
```

- shift：造成参数变量号码偏移

除此之外，脚本后面所接的变量是否能够进行偏移 （shift） 呢？什么是偏移啊？我们直接以下面的范例来说明好了， 用范例说明比较好解释！我们将 how_paras.sh 的内容稍作变化一下，用来显示每次偏移后参数的变化情况：

```
[dmtsai@study bin]$ vim shift_paras.sh
#!/bin/bash
# Program:
#    Program shows the effect of shift function.
# History:
# 2009/02/17    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo "Total parameter number is ==&gt; $#"
echo "Your whole parameter is   ==&gt; '$@'"
shift   # 进行第一次“一个变量的 shift ”
echo "Total parameter number is ==&gt; $#"
echo "Your whole parameter is   ==&gt; '$@'"
shift 3 # 进行第二次“三个变量的 shift ”
echo "Total parameter number is ==&gt; $#"
echo "Your whole parameter is   ==&gt; '$@'"
```

这玩意的执行成果如下：

```
[dmtsai@study bin]$ sh shift_paras.sh one two three four five six &lt;==给予六个参数
Total parameter number is ==&gt; 6   &lt;==最原始的参数变量情况
Your whole parameter is   ==&gt; 'one two three four five six'
Total parameter number is ==&gt; 5   &lt;==第一次偏移，看下面发现第一个 one 不见了
Your whole parameter is   ==&gt; 'two three four five six'
Total parameter number is ==&gt; 2   &lt;==第二次偏移掉三个，two three four 不见了
Your whole parameter is   ==&gt; 'five six'
```

光看结果你就可以知道啦，那个 shift 会移动变量，而且 shift 后面可以接数字，代表拿掉最前面的几个参数的意思。 上面的执行结果中，第一次进行 shift 后他的显示情况是“ one two three four five six”，所以就剩下五个啦！第二次直接拿掉三个，就变成“ two three four five six ”啦！ 这样这个案例可以了解了吗？理解了 shift 的功能了吗？

上面这几个例子都很简单吧？几乎都是利用 bash 的相关功能而已～ 不难啦～下面我们就要使用条件判断式来进行一些分别功能的设置了，好好瞧一瞧先～



### 12.4 条件判断式

在编写程序时，条件判断式是非常重要的工具。它可以根据某些数据判断程序的执行流程。下面，我们将介绍如何使用条件判断式来编写更复杂的 shell 脚本。

#### 12.4.1 利用 if .... then

`if .... then` 是最常见的条件判断式，用于根据条件来执行不同的指令。基本语法如下：

```
if [ 条件判断式 ]; then
    # 当条件判断式成立时，可以进行的指令工作内容；
fi  # 将 if 反过来写，就成为 fi 啦！结束 if 之意！
```

可以通过多个 `if` 语句进行多次判断：

```
if [ 条件判断式一 ]; then
    # 当条件判断式一成立时，可以进行的指令工作内容；
elif [ 条件判断式二 ]; then
    # 当条件判断式二成立时，可以进行的指令工作内容；
else
    # 当条件判断式一与二均不成立时，可以进行的指令工作内容；
fi
```

#### 案例：用户输入判断

我们可以使用 `if .... then` 重写前面的 ans_yn.sh 脚本，以实现更复杂的条件判断：

```
[dmtsai@study bin]$ vim ans_yn-3.sh
#!/bin/bash
# Program:
#       This program shows the user's choice
# History:
# 2015/07/16    VBird   First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "Please input （Y/N）: " yn

if [ "${yn}" == "Y" ] || [ "${yn}" == "y" ]; then
    echo "OK, continue"
elif [ "${yn}" == "N" ] || [ "${yn}" == "n" ]; then
    echo "Oh, interrupt!"
else
    echo "I don't know what your choice is"
fi
```

#### 案例：参数判断

现在我们想让用户输入 `hello` 作为参数，如果没有输入参数或输入错误的参数，则给出相应提示：

```
[dmtsai@study bin]$ vim hello-2.sh
#!/bin/bash
# Program:
#    Check $1 is equal to "hello"
# History:
# 2015/07/16    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

if [ "${1}" == "hello" ]; then
    echo "Hello, how are you ?"
elif [ "${1}" == "" ]; then
    echo "You MUST input parameters, ex> {${0} someword}"
else
    echo "The only parameter is 'hello', ex> {${0} hello}"
fi
```

原文：

这个 if .... then 是最常见的条件判断式了～简单的说，就是当符合某个条件判断的时候， 就予以进行某项工作就是了。这个 if ... then 的判断还有多层次的情况！我们分别介绍如下：

- 单层、简单条件判断式

如果你只有一个判断式要进行，那么我们可以简单的这样看：

```
if [ 条件判断式 ]; then
    当条件判断式成立时，可以进行的指令工作内容；
fi   &lt;==将 if 反过来写，就成为 fi 啦！结束 if 之意！
```

至于条件判断式的判断方法，与前一小节的介绍相同啊！较特别的是，如果我有多个条件要判别时， 除了 [ans_yn.sh](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#sh06) 那个案例所写的，也就是“将多个条件写入一个中括号内的情况”之外， 我还可以有多个中括号来隔开喔！而括号与括号之间，则以 && 或 || 来隔开，他们的意义是：

- && 代表 AND ；
- || 代表 or ；

所以，在使用中括号的判断式中， && 及 || 就与指令下达的状态不同了。举例来说， ans_yn.sh 里面的判断式可以这样修改：

> [ "${yn}" == "Y" -o "${yn}" == "y" ] 上式可替换为 [ "${yn}" == "Y" ] || [ "${yn}" == "y" ]

之所以这样改，很多人是习惯问题！很多人则是喜欢一个中括号仅有一个判别式的原因。好了， 现在我们来将 ans_yn.sh 这个脚本修改成为 if ... then 的样式来看看：

```
[dmtsai@study bin]$ cp ans_yn.sh ans_yn-2.sh  &lt;==用复制来修改的比较快！
[dmtsai@study bin]$ vim ans_yn-2.sh
#!/bin/bash
# Program:
#       This program shows the user's choice
# History:
# 2015/07/16    VBird   First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "Please input （Y/N）: " yn

if [ "${yn}" == "Y" ] &#124;&#124; [ "${yn}" == "y" ]; then
    echo "OK, continue"
    exit 0
fi
if [ "${yn}" == "N" ] &#124;&#124; [ "${yn}" == "n" ]; then
    echo "Oh, interrupt!"
    exit 0
fi
echo "I don't know what your choice is" && exit 0
```

不过，由这个例子看起来，似乎也没有什么了不起吧？原本的 ans_yn.sh 还比较简单呢～ 但是如果以逻辑概念来看，其实上面的范例中，我们使用了两个条件判断呢！明明仅有一个 ${yn} 的变量，为何需要进行两次比对呢？ 此时，多重条件判断就能够来测试测试啰！

- 多重、复杂条件判断式

在同一个数据的判断中，如果该数据需要进行多种不同的判断时，应该怎么作？举例来说，上面的 [ans_yn.sh](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#sh06) 脚本中，我们只要进行一次 ${yn} 的判断就好 （仅进行一次 if ），不想要作多次 if 的判断。 此时你就得要知道下面的语法了：

```
# 一个条件判断，分成功进行与失败进行 （else）
if [ 条件判断式 ]; then
    当条件判断式成立时，可以进行的指令工作内容；
else
    当条件判断式不成立时，可以进行的指令工作内容；
fi
```

如果考虑更复杂的情况，则可以使用这个语法：

```
# 多个条件判断 （if ... elif ... elif ... else） 分多种不同情况执行
if [ 条件判断式一 ]; then
    当条件判断式一成立时，可以进行的指令工作内容；
elif [ 条件判断式二 ]; then
    当条件判断式二成立时，可以进行的指令工作内容；
else
    当条件判断式一与二均不成立时，可以进行的指令工作内容；
fi
```

你得要注意的是， elif 也是个判断式，因此出现 elif 后面都要接 then 来处理！但是 else 已经是最后的没有成立的结果了， 所以 else 后面并没有 then 喔！好！我们来将 ans_yn-2.sh 改写成这样：

```
[dmtsai@study bin]$ cp ans_yn-2.sh ans_yn-3.sh
[dmtsai@study bin]$ vim ans_yn-3.sh
#!/bin/bash
# Program:
#       This program shows the user's choice
# History:
# 2015/07/16    VBird   First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "Please input （Y/N）: " yn

if [ "${yn}" == "Y" ] &#124;&#124; [ "${yn}" == "y" ]; then
    echo "OK, continue"
elif [ "${yn}" == "N" ] &#124;&#124; [ "${yn}" == "n" ]; then
    echo "Oh, interrupt!"
else
    echo "I don't know what your choice is"
fi
```

是否程序变得很简单，而且依序判断，可以避免掉重复判断的状况，这样真的很容易设计程序的啦！ ^_^！ 好了，让我们再来进行另外一个案例的设计。一般来说，如果你不希望使用者由键盘输入额外的数据时， 可以使用[上一节提到的参数功能 （$1）](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#dis3)！让使用者在下达指令时就将参数带进去！ 现在我们想让使用者输入“ hello ”这个关键字时，利用参数的方法可以这样依序设计：

1. 判断 $1 是否为 hello，如果是的话，就显示 "Hello, how are you ?"；
2. 如果没有加任何参数，就提示使用者必须要使用的参数下达法；
3. 而如果加入的参数不是 hello ，就提醒使用者仅能使用 hello 为参数。

整个程序的撰写可以是这样的：

```
[dmtsai@study bin]$ vim hello-2.sh
#!/bin/bash
# Program:
#    Check $1 is equal to "hello"
# History:
# 2015/07/16    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

if [ "${1}" == "hello" ]; then
    echo "Hello, how are you ?"
elif [ "${1}" == "" ]; then
    echo "You MUST input parameters, ex&gt; {${0} someword}"
else
    echo "The only parameter is 'hello', ex&gt; {${0} hello}"
fi
```

然后你可以执行这支程序，分别在 $1 的位置输入 hello, 没有输入与随意输入， 就可以看到不同的输出啰～是否还觉得挺简单的啊！ ^_^。事实上， 学到这里，也真的很厉害了～好了，下面我们继续来玩一些比较大一点的计划啰～

我们在第十章已经学会了 [grep](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#grep) 这个好用的玩意儿，那么多学一个叫做 netstat 的指令，这个指令可以查询到目前主机有打开的网络服务端口 （service ports）， 相关的功能我们会在[服务器架设篇](http://linux.vbird.org/linux_server)继续介绍，这里你只要知道，我可以利用“ netstat -tuln ”来取得目前主机有启动的服务， 而且取得的信息有点像这样：

```
[dmtsai@study ~]$ netstat -tuln
Active Internet connections （only servers）
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN
tcp6       0      0 :::22                   :::*                    LISTEN
tcp6       0      0 ::1:25                  :::*                    LISTEN
udp        0      0 0.0.0.0:123             0.0.0.0:*
udp        0      0 0.0.0.0:5353            0.0.0.0:*
udp        0      0 0.0.0.0:44326           0.0.0.0:*
udp        0      0 127.0.0.1:323           0.0.0.0:*
udp6       0      0 :::123                  :::*
udp6       0      0 ::1:323                 :::*
#封包格式           本地IP:端口             远端IP:端口             是否监听
```

上面的重点是“Local Address （本地主机的IP与端口对应）”那个字段，他代表的是本机所启动的网络服务！ IP的部分说明的是该服务位于那个接口上，若为 127.0.0.1 则是仅针对本机开放，若是 0.0.0.0 或 ::: 则代表对整个 Internet 开放 （更多信息请参考服务器架设篇的介绍）。 每个端口 （port） 都有其特定的网络服务，几个常见的 port 与相关网络服务的关系是：

- 80: WWW
- 22: ssh
- 21: ftp
- 25: mail
- 111: RPC（远端程序调用）
- 631: CUPS（打印服务功能）

假设我的主机有兴趣要侦测的是比较常见的 port 21, 22, 25及 80 时，那我如何通过 netstat 去侦测我的主机是否有打开这四个主要的网络服务端口呢？由于每个服务的关键字都是接在冒号“ : ”后面， 所以可以借由撷取类似“ :80 ”来侦测的！那我就可以简单的这样去写这个程序喔：

```
[dmtsai@study bin]$ vim netstat.sh
#!/bin/bash
# Program:
#     Using netstat and grep to detect WWW,SSH,FTP and Mail services.
# History:
# 2015/07/16    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

# 1\. 先作一些告知的动作而已～
echo "Now, I will detect your Linux server's services!"
echo -e "The www, ftp, ssh, and mail（smtp） will be detect! \n"

# 2\. 开始进行一些测试的工作，并且也输出一些信息啰！
testfile=/dev/shm/netstat_checking.txt
netstat -tuln &gt; ${testfile}          # 先转存数据到内存当中！不用一直执行 netstat
testing=$（grep ":80 " ${testfile}）   # 侦测看 port 80 在否？
if [ "${testing}" != "" ]; then
    echo "WWW is running in your system."
fi
testing=$（grep ":22 " ${testfile}）   # 侦测看 port 22 在否？
if [ "${testing}" != "" ]; then
    echo "SSH is running in your system."
fi
testing=$（grep ":21 " ${testfile}）   # 侦测看 port 21 在否？
if [ "${testing}" != "" ]; then
    echo "FTP is running in your system."
fi
testing=$（grep ":25 " ${testfile}）   # 侦测看 port 25 在否？
if [ "${testing}" != "" ]; then
    echo "Mail is running in your system."
fi
```

实际执行这支程序你就可以看到你的主机有没有启动这些服务啦！是否很有趣呢？ 条件判断式还可以搞的更复杂！举例来说，在台湾当兵是国民应尽的义务，不过，在当兵的时候总是很想要退伍的！ 那你能不能写个脚本程序来跑，让使用者输入他的退伍日期，让你去帮他计算还有几天才退伍？

由于日期是要用相减的方式来处置，所以我们可以通过使用 date 显示日期与时间，将他转为由 1970-01-01 累积而来的秒数， 通过秒数相减来取得剩余的秒数后，再换算为日数即可。整个脚本的制作流程有点像这样：

1. 先让使用者输入他们的退伍日期；
2. 再由现在日期比对退伍日期；
3. 由两个日期的比较来显示“还需要几天”才能够退伍的字样。

似乎挺难的样子？其实也不会啦，利用“ date --date="YYYYMMDD" +%s ”转成秒数后，接下来的动作就容易的多了！如果你已经写完了程序，对照下面的写法试看看：

```
[dmtsai@study bin]$ vim cal_retired.sh
#!/bin/bash
# Program:
#    You input your demobilization date, I calculate how many days before you demobilize.
# History:
# 2015/07/16    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

# 1\. 告知使用者这支程序的用途，并且告知应该如何输入日期格式？
echo "This program will try to calculate :"
echo "How many days before your demobilization date..."
read -p "Please input your demobilization date （YYYYMMDD ex&gt;20150716）: " date2

# 2\. 测试一下，这个输入的内容是否正确？利用正则表达式啰～
date_d=$（echo ${date2} &#124;grep '[0-9]\{8\}'）   # 看看是否有八个数字
if [ "${date_d}" == "" ]; then
    echo "You input the wrong date format...."
    exit 1
fi

# 3\. 开始计算日期啰～
declare -i date_dem=$（date --date="${date2}" +%s）      # 退伍日期秒数
declare -i date_now=$（date +%s）                        # 现在日期秒数
declare -i date_total_s=$（（${date_dem}-${date_now}））   # 剩余秒数统计
declare -i date_d=$（（${date_total_s}/60/60/24））        # 转为日数
if [ "${date_total_s}" -lt "0" ]; then                 # 判断是否已退伍
    echo "You had been demobilization before: " $（（-1*${date_d}）） " ago"
else
    declare -i date_h=$（（$（（${date_total_s}-${date_d}*60*60*24））/60/60））
    echo "You will demobilize after ${date_d} days and ${date_h} hours."
fi
```

瞧一瞧，这支程序可以帮你计算退伍日期呢～如果是已经退伍的朋友， 还可以知道已经退伍多久了～哈哈！很可爱吧～脚本中的 date*d 变量宣告那个 /60/60/24 是来自于一天的总秒数 （24小时\*60分\*60秒） 。瞧～全部的动作都没有超出我们所学的范围吧～ ^*^ 还能够避免使用者输入错误的数字，所以多了一个正则表达式的判断式呢～ 这个例子比较难，有兴趣想要一探究竟的朋友，可以作一下[课后练习题](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#ex) 关于计算生日的那一题喔！～加油！



### 12.4.2 利用 case ..... esac 判断

`case .... in .... esac` 是另一种条件判断语法，特别适用于需要处理固定值的情况。语法如下：

```
case $变量名称 in
  "第一个变量内容"）
    # 程序段
    ;;
  "第二个变量内容"）
    # 程序段
    ;;
  *）
    # 其他情况的处理程序
    ;;
esac
```

#### 案例：变量匹配

我们可以使用 `case` 语法重写 hello-2.sh 脚本：

```
[dmtsai@study bin]$ vim hello-3.sh
#!/bin/bash
# Program:
#     Show "Hello" from $1.... by using case .... esac
# History:
# 2015/07/16    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

case ${1} in
  "hello"）
    echo "Hello, how are you ?"
    ;;
  ""）
    echo "You MUST input parameters, ex> {${0} someword}"
    ;;
  *）
    echo "Usage ${0} {hello}"
    ;;
esac
```

**原文：**

上个小节提到的“ if .... then .... fi ”对于变量的判断是以“比对”的方式来分辨的， 如果符合状态就进行某些行为，并且通过较多层次 （就是 elif ...） 的方式来进行多个变量的程序码撰写，譬如 [hello-2.sh](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#check_1) 那个小程序，就是用这样的方式来撰写的啰。 好，那么万一我有多个既定的变量内容，例如 hello-2.sh 当中，我所需要的变量就是 "hello" 及空字串两个， 那么我只要针对这两个变量来设置状况就好了，对吧？那么可以使用什么方式来设计呢？呵呵～就用 case ... in .... esac 吧～，他的语法如下：

```
case  $变量名称 in   &lt;==关键字为 case ，还有变量前有钱字号
  "第一个变量内容"）   &lt;==每个变量内容建议用双引号括起来，关键字则为小括号 ）
    程序段
    ;;            &lt;==每个类别结尾使用两个连续的分号来处理！
  "第二个变量内容"）
    程序段
    ;;
  *）                  &lt;==最后一个变量内容都会用 * 来代表所有其他值
    不包含第一个变量内容与第二个变量内容的其他程序执行段
    exit 1
    ;;
esac                  &lt;==最终的 case 结尾！“反过来写”思考一下！
```

要注意的是，这个语法以 case （实际案例之意） 为开头，结尾自然就是将 case 的英文反过来写！就成为 esac 啰！ 不会很难背啦！另外，每一个变量内容的程序段最后都需要两个分号 （;;） 来代表该程序段落的结束，这挺重要的喔！ 至于为何需要有 * 这个变量内容在最后呢？这是因为，如果使用者不是输入变量内容一或二时， 我们可以告知使用者相关的信息啊！废话少说，我们拿 hello-2.sh 的案例来修改一下，他应该会变成这样喔：

```
[dmtsai@study bin]$ vim hello-3.sh
#!/bin/bash
# Program:
#     Show "Hello" from $1.... by using case .... esac
# History:
# 2015/07/16    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

case ${1} in
  "hello"）
    echo "Hello, how are you ?"
    ;;
  ""）
    echo "You MUST input parameters, ex&gt; {${0} someword}"
    ;;
  *）   # 其实就相当于万用字符，0~无穷多个任意字符之意！
    echo "Usage ${0} {hello}"
    ;;
esac
```

在上面这个 hello-3.sh 的案例当中，如果你输入“ sh hello-3.sh test ”来执行， 那么屏幕上就会出现“Usage hello-3.sh {hello}”的字样，告知执行者仅能够使用 hello 喔～ 这样的方式对于需要某些固定字串来执行的变量内容就显的更加的方便呢！ 这种方式你真的要熟悉喔！这是因为早期系统的很多服务的启动 scripts 都是使用这种写法的 （CentOS 6.x 以前）。 虽然 CentOS 7 已经使用 systemd，不过仍有数个服务是放在 /etc/init.d/ 目录下喔！例如有个名为 netconsole 的服务在该目录下， 那么你想要重新启动该服务，是可以这样做的 （请注意，要成功执行，还是得要具有 root 身份才行！一般帐号能执行，但不会成功！）：

> /etc/init.d/netconsole restart

重点是那个 restart 啦！如果你使用“ less /etc/init.d/netconsole ”去查阅一下，就会看到他使用的是 case 语法， 并且会规定某些既定的变量内容，你可以直接下达 /etc/init.d/netconsole ， 该 script 就会告知你有哪些后续接的变量可以使用啰～方便吧！ ^_^

一般来说，使用“ case $变量 in ”这个语法中，当中的那个“ $变量 ”大致有两种取得的方式：

- 直接下达式：例如上面提到的，利用“ script.sh variable ” 的方式来直接给予 $1 这个变量的内容，这也是在 /etc/init.d 目录下大多数程序的设计方式。
- 互动式：通过 read 这个指令来让使用者输入变量的内容。

这么说或许你的感受性还不高，好，我们直接写个程序来玩玩：让使用者能够输入 one, two, three ， 并且将使用者的变量显示到屏幕上，如果不是 one, two, three 时，就告知使用者仅有这三种选择。

```
[dmtsai@study bin]$ vim show123.sh
#!/bin/bash
# Program:
#    This script only accepts the flowing parameter: one, two or three.
# History:
# 2015/07/17    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo "This program will print your selection !"
# read -p "Input your choice: " choice   # 暂时取消，可以替换！
# case ${choice} in                      # 暂时取消，可以替换！
case ${1} in                             # 现在使用，可以用上面两行替换！
  "one"）
    echo "Your choice is ONE"
    ;;
  "two"）
    echo "Your choice is TWO"
    ;;
  "three"）
    echo "Your choice is THREE"
    ;;
  *）
    echo "Usage ${0} {one&#124;two&#124;three}"
    ;;
esac
```

此时，你可以使用“ sh show123.sh two ”的方式来下达指令，就可以收到相对应的回应了。 上面使用的是直接下达的方式，而如果使用的是互动式时，那么将上面第 10, 11 行的 "#" 拿掉， 并将 12 行加上注解 （#），就可以让使用者输入参数啰～这样是否很有趣啊？

#### 补充：

在 Unix/Linux Shell 脚本中，`case` 语句用于执行多分支选择，类似于其他编程语言中的 `switch` 语句。以下是对 `case` 语句的详细解释，包括每个关键部分的作用，以及为什么需要使用双分号 `;;` 来结束每个分支。

##### `case` 语句的结构

`case` 语句的基本结构如下：

```
bash复制代码case $变量名称 in
  "第一个变量内容")
    # 程序段
    ;;
  "第二个变量内容")
    # 程序段
    ;;
  *)
    # 不包含第一个变量内容与第二个变量内容的其他程序执行段
    exit 1
    ;;
esac
```

##### 关键部分解释：

1. **`case $变量名称 in`**： 这是 `case` 语句的开始。`$变量名称` 是你要匹配的变量，它的值会被拿来与每个分支的值进行比较。
2. **`"第一个变量内容")`**： 这是第一个匹配分支。`"第一个变量内容"` 是你要比较的具体值。每个分支以右括号 `)` 结尾。
3. **程序段**： 如果 `$变量名称` 的值匹配 `"第一个变量内容"`，那么这个程序段会被执行。
4. **`;;`**： 每个分支的结尾都需要用两个连续的分号 `;;` 来结束。双分号的作用是告诉 `case` 语句，这个分支的处理结束，可以继续检查下一个分支。没有这个标记，Shell 不知道分支何时结束，会报语法错误。
5. **`\*)`**： 星号 `*` 是一个通配符，表示所有其他未被明确列出的值。如果没有其他分支匹配，执行这里的程序段。它相当于 `default` 分支。
6. **`exit 1`**： 这个例子中，`exit 1` 命令会退出脚本并返回状态码 `1`，表示失败或错误。
7. **`esac`**： 这是 `case` 语句的结尾。`esac` 是 `case` 的反写，用于标识 `case` 语句的结束。

##### 为什么需要使用双分号 `;;`

双分号 `;;` 是 `case` 语句中的分隔符，用于结束每个分支的程序段。其作用类似于 C 语言中的 `break` 语句，标识当前分支处理结束，防止意外执行后续的分支内容。如果没有双分号 `;;`，`case` 语句将无法正确解析分支的结束，导致语法错误。

##### 示例

以下是一个完整的 `case` 语句示例，用于打印输入的颜色名称：

```
#!/bin/bash

read -p "Please enter a color (red, green, blue): " color

case $color in
  "red")
    echo "You chose red."
    ;;
  "green")
    echo "You chose green."
    ;;
  "blue")
    echo "You chose blue."
    ;;
  *)
    echo "Invalid color choice."
    exit 1
    ;;
esac
```

##### 执行流程

1. 用户输入颜色名称。
2. `case` 语句匹配用户输入的颜色名称。
3. 如果匹配成功，执行相应的程序段，并用双分号 `;;` 结束该分支。
4. 如果没有匹配到任何分支，执行 `*` 分支中的程序段。

##### 总结

- `case` 语句用于多分支选择，`$变量名称` 是要匹配的变量。
- 每个分支用具体值和右括号 `)` 表示，程序段用双分号 `;;` 结束。
- 星号 `*` 表示所有其他未被明确列出的值，相当于 `default` 分支。
- `esac` 用于结束 `case` 语句。
- 双分号 `;;` 必须用于标识每个分支的结束，以防止意外执行后续分支内容。



### 12.4.3 利用 function 功能

函数（function）是将一段代码封装成一个独立的模块，可以多次调用。它的基本语法如下：

```
function fname（） {
    # 程序段
}
```

#### 案例：函数使用

我们可以将前面的脚本重构为使用函数的版本：

```
[dmtsai@study bin]$ vim show123-2.sh
#!/bin/bash
# Program:
#    Use function to repeat information.
# History:
# 2015/07/17    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

function printit（）{
    echo -n "Your choice is "  # 加上 -n 可以不断行继续在同一行显示
}

echo "This program will print your selection !"
case ${1} in
  "one"）
    printit; echo ${1} | tr 'a-z' 'A-Z'  # 将参数做大小写转换！
    ;;
  "two"）
    printit; echo ${1} | tr 'a-z' 'A-Z'
    ;;
  "three"）
    printit; echo ${1} | tr 'a-z' 'A-Z'
    ;;
  *）
    echo "Usage ${0} {one|two|three}"
    ;;
esac
```

通过使用函数，我们可以简化代码，并使其更具可读性和可维护性。函数也可以接受参数，并在函数体内使用这些参数。

#### **原文：**

什么是“函数 （function）”功能啊？简单的说，其实， 函数可以在 shell script 当中做出一个类似自订执行指令的东西，最大的功能是， 可以简化我们很多的程序码～举例来说，上面的 show123.sh 当中，每个输入结果 one, two, three 其实输出的内容都一样啊～那么我就可以使用 function 来简化了！ function 的语法是这样的：

```
function fname（） {
    程序段
}
```

那个 fname 就是我们的自订的执行指令名称～而程序段就是我们要他执行的内容了。 要注意的是，因为 shell script 的执行方式是由上而下，由左而右， 因此在 shell script 当中的 function 的设置一定要在程序的最前面， 这样才能够在执行时被找到可用的程序段喔 （这一点与传统程序语言差异相当大！初次接触的朋友要小心！）！ 好～我们将 show123.sh 改写一下，自订一个名为 printit 的函数来使用喔：

```
[dmtsai@study bin]$ vim show123-2.sh
#!/bin/bash
# Program:
#    Use function to repeat information.
# History:
# 2015/07/17    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

function printit（）{
    echo -n "Your choice is "     # 加上 -n 可以不断行继续在同一行显示
}

echo "This program will print your selection !"
case ${1} in
  "one"）
    **printit**; echo ${1} &#124; tr 'a-z' 'A-Z'  # 将参数做大小写转换！
    ;;
  "two"）
    **printit**; echo ${1} &#124; tr 'a-z' 'A-Z'
    ;;
  "three"）
    **printit**; echo ${1} &#124; tr 'a-z' 'A-Z'
    ;;
  *）
    echo "Usage ${0} {one&#124;two&#124;three}"
    ;;
esac
```

以上面的例子来说，鸟哥做了一个函数名称为 printit ，所以，当我在后续的程序段里面， 只要执行 printit 的话，就表示我的 shell script 要去执行“ function printit .... ” 里面的那几个程序段落啰！当然啰，上面这个例子举得太简单了，所以你不会觉得 function 有什么好厉害的， 不过，如果某些程序码一再地在 script 当中重复时，这个 function 可就重要的多啰～ 不但可以简化程序码，而且可以做成类似“模块”的玩意儿，真的很棒啦！

![鸟哥的图示](image/vbird_face-17224082521816.gif)

**Tips** 建议读者可以使用类似 vim 的编辑器到 /etc/init.d/ 目录下去查阅一下你所看到的文件， 并且自行追踪一下每个文件的执行情况，相信会更有心得！

另外， function 也是拥有内置变量的～他的内置变量与 shell script 很类似， 函数名称代表示 $0 ，而后续接的变量也是以 $1, $2... 来取代的～ 这里很容易搞错喔～因为“ function fname（） { 程序段 } ”内的 $0, $1... 等等与 shell script 的 $0 是不同的。以上面 show123-2.sh 来说，假如我下达：“ sh show123-2.sh one ” 这表示在 shell script 内的 $1 为 "one" 这个字串。但是在 printit（） 内的 $1 则与这个 one 无关。 我们将上面的例子再次的改写一下，让你更清楚！

```
[dmtsai@study bin]$ vim show123-3.sh
#!/bin/bash
# Program:
#    Use function to repeat information.
# History:
# 2015/07/17    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

function printit（）{
    echo "Your choice is ${1}"   # 这个 $1 必须要参考下面指令的下达
}

echo "This program will print your selection !"
case ${1} in
  "one"）
    **printit 1**  # 请注意， printit 指令后面还有接参数！
    ;;
  "two"）
    **printit 2**
    ;;
  "three"）
    **printit 3**
    ;;
  *）
    echo "Usage ${0} {one&#124;two&#124;three}"
    ;;
esac
```

在上面的例子当中，如果你输入“ sh show123-3.sh one ”就会出现“ Your choice is 1 ”的字样～ 为什么是 1 呢？因为在程序段落当中，我们是写了“ printit 1 ”那个 1 就会成为 function 当中的 $1 喔～ 这样是否理解呢？ function 本身其实比较困难一点，如果你还想要进行其他的撰写的话。 不过，我们仅是想要更加了解 shell script 而已，所以，这里看看即可～了解原理就好啰～ ^_^



### 12.5 循环（loop）

在编写脚本时，循环语句是非常重要的。循环语句允许我们不断地执行某个程序段，直到满足特定的条件。主要有两种类型的循环：不定循环和固定循环。

### 12.5.1 while do done 和 until do done（不定循环）

**while 循环**

`while` 循环在条件成立时执行循环，直到条件不成立时停止。基本语法如下：

```
while [ condition ]
do
    程序段落
done
```

**until 循环**

`until` 循环在条件不成立时执行循环，直到条件成立时停止。基本语法如下：

```
until [ condition ]
do
    程序段落
done
```

#### 案例：用户输入控制循环

让用户输入 `yes` 或 `YES` 才结束程序，否则继续提示用户输入：

```
[dmtsai@study bin]$ vim yes_to_stop.sh
#!/bin/bash
# Program:
#    Repeat question until user input correct answer.
# History:
# 2015/07/17    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

while [ "${yn}" != "yes" -a "${yn}" != "YES" ]
do
    read -p "Please input yes/YES to stop this program: " yn
done
echo "OK! you input the correct answer."
```

使用 `until` 循环的版本：

```
[dmtsai@study bin]$ vim yes_to_stop-2.sh
#!/bin/bash
# Program:
#    Repeat question until user input correct answer.
# History:
# 2015/07/17    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

until [ "${yn}" == "yes" -o "${yn}" == "YES" ]
do
    read -p "Please input yes/YES to stop this program: " yn
done
echo "OK! you input the correct answer."
```

#### 案例：计算 1 到 100 的和

利用 `while` 循环计算 1+2+...+100 的值：

```
[dmtsai@study bin]$ vim cal_1_100.sh
#!/bin/bash
# Program:
#    Use loop to calculate "1+2+3+...+100" result.
# History:
# 2015/07/17    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

s=0  # 这是加总的数值变量
i=0  # 这是累计的数值，亦即是 1, 2, 3....
while [ "${i}" != "100" ]
do
    i=$((i+1))   # 每次 i 都会增加 1 
    s=$((s+i))   # 每次都会加总一次！
done
echo "The result of '1+2+3+...+100' is ==> $s"
```

### 12.5.2 for...do...done（固定循环）

`for` 循环用于固定次数的循环。基本语法如下：

```
for var in con1 con2 con3 ...
do
    程序段
done
```

#### 案例：输出动物名称

输出三种动物的名称：

```
[dmtsai@study bin]$ vim show_animal.sh
#!/bin/bash
# Program:
#     Using for .... loop to print 3 animals
# History:
# 2015/07/17    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

for animal in dog cat elephant
do
    echo "There are ${animal}s.... "
done
```

#### 案例：检查系统帐号信息

通过管道命令和 `for` 循环检查系统的帐号信息：

```
[dmtsai@study bin]$ vim userid.sh
#!/bin/bash
# Program
#       Use id, finger command to check system account's information.
# History
# 2015/07/17    VBird   first release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
users=$(cut -d ':' -f1 /etc/passwd)    # 撷取帐号名称
for username in ${users}               # 开始循环进行！
do
    id ${username}
done
```

#### 案例：网络状态检查

通过 `ping` 命令检查网络中 192.168.1.1 到 192.168.1.100 的主机状态：

```
[dmtsai@study bin]$ vim pingip.sh
#!/bin/bash
# Program
#       Use ping command to check the network's PC state.
# History
# 2015/07/17    VBird   first release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
network="192.168.1"              # 先定义一个网域的前面部分！
for sitenu in $(seq 1 100)       # seq 为 sequence（连续） 的缩写之意
do
    ping -c 1 -w 1 ${network}.${sitenu} &> /dev/null && result=0 || result=1
    if [ "${result}" == 0 ]; then
        echo "Server ${network}.${sitenu} is UP."
    else
        echo "Server ${network}.${sitenu} is DOWN."
    fi
done
```

### 12.5.3 for...do...done 的数值处理

for 循环也可以用于数值处理。基本语法如下：

```
for (( 初始值; 限制值; 执行步阶 ))
do
    程序段
done
```

#### 案例：计算 1 到用户输入值的和

```
[dmtsai@study bin]$ vim cal_1_100-2.sh
#!/bin/bash
# Program:
#     Try do calculate 1+2+....+${your_input}
# History:
# 2015/07/17    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "Please input a number, I will count for 1+2+...+your_input: " nu

s=0
for (( i=1; i<=${nu}; i=i+1 ))
do
    s=$((s+i))
done
echo "The result of '1+2+3+...+${nu}' is ==> ${s}"
```

### 12.5.4 搭配乱数与阵列的实验

结合数组和随机数编写一个脚本，随机选择吃饭的地方：

```
[dmtsai@study bin]$ vim what_to_eat.sh
#!/bin/bash
# Program:
#     Try do tell you what you may eat.
# History:
# 2015/07/17    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

eat[1]="卖当当漢堡包"
eat[2]="肯爷爷炸鸡"
eat[3]="彩虹日式便当"
eat[4]="越油越好吃大雅"
eat[5]="想不出吃啥学餐"
eat[6]="太师父便当"
eat[7]="池上便当"
eat[8]="怀念火车便当"
eat[9]="一起吃方便面"
eatnum=9

check=$(( ${RANDOM} * ${eatnum} / 32767 + 1 ))
echo "you may eat ${eat[${check}]}"
```

如果想要每次随机选择三个不同的地方：

```
[dmtsai@study bin]$ vim what_to_eat-2.sh
#!/bin/bash
# Program:
#     Try do tell you what you may eat.
# History:
# 2015/07/17    VBird    First release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

eat[1]="卖当当漢堡包"
eat[2]="肯爷爷炸鸡"
eat[3]="彩虹日式便当"
eat[4]="越油越好吃大雅"
eat[5]="想不出吃啥学餐"
eat[6]="太师父便当"
eat[7]="池上便当"
eat[8]="怀念火车便当"
eat[9]="一起吃方便面"
eatnum=9

eated=0
while [ "${eated}" -lt 3 ]; do
    check=$(( ${RANDOM} * ${eatnum} / 32767 + 1 ))
    mycheck=0
    if [ "${eated}" -ge 1 ]; then
        for i in $(seq 1 ${eated} )
        do
            if [ ${eatedcon[$i]} == $check ]; then
                mycheck=1
            fi
        done
    fi
    if [ ${mycheck} == 0 ]; then
        echo "you may eat ${eat[${check}]}"
        eated=$(( eated + 1 ))
        eatedcon[${eated}]=${check}
    fi
done
```

通过结合乱数、数组、循环与条件判断，你可以编写出更加复杂和有趣的脚本，简化日常任务，提高效率。

## 12.6 shell script 的追踪与 debug

scripts 在执行之前，最怕的就是出现语法错误的问题了！那么我们如何 debug 呢？有没有办法不需要通过直接执行该 scripts 就可以来判断是否有问题呢？呵呵！当然是有的！我们就直接以 bash 的相关参数来进行判断吧！

```
[dmtsai@study ~]$ sh [-nvx] scripts.sh
选项与参数：
-n  ：不要执行 script，仅查询语法的问题；
-v  ：再执行 sccript 前，先将 scripts 的内容输出到屏幕上；
-x  ：将使用到的 script 内容显示到屏幕上，这是很有用的参数！

范例一：测试 dir_perm.sh 有无语法的问题？
[dmtsai@study ~]$ sh -n dir_perm.sh 
# 若语法没有问题，则不会显示任何信息！

范例二：将 show_animal.sh 的执行过程全部列出来～
[dmtsai@study ~]$ sh -x show_animal.sh 
+ PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:/root/bin
+ export PATH
+ for animal in dog cat elephant
+ echo 'There are dogs.... '
There are dogs....
+ for animal in dog cat elephant
+ echo 'There are cats.... '
There are cats....
+ for animal in dog cat elephant
+ echo 'There are elephants.... '
There are elephants....
```

请注意，上面范例二中执行的结果并不会有颜色的显示！鸟哥为了方便说明所以在 + 号之后的数据都加上颜色了！ 在输出的讯息中，在加号后面的数据其实都是指令串，由于 sh -x 的方式来将指令执行过程也显示出来， 如此使用者可以判断程序码执行到哪一段时会出现相关的信息！这个功能非常的棒！通过显示完整的指令串， 你就能够依据输出的错误信息来订正你的脚本了！

熟悉 sh 的用法，将可以使你在管理 Linux 的过程中得心应手！至于在 Shell scripts 的学习方法上面，需要“多看、多模仿、并加以修改成自己的样式！” 是最快的学习手段了！网络上有相当多的朋友在开发一些相当有用的 scripts ，若是你可以将对方的 scripts 拿来，并且改成适合自己主机的样子！那么学习的效果会是最快的呢！

另外，我们 Linux 系统本来就有很多的服务启动脚本，如果你想要知道每个 script 所代表的功能是什么？ 可以直接以 vim 进入该 script 去查阅一下，通常立刻就知道该 script 的目的了。 举例来说，我们之前一直提到的 /etc/init.d/netconsole ，这个 script 是干嘛用的？ 利用 vim 去查阅最前面的几行字，他出现如下信息：

```
# netconsole    This loads the netconsole module with the configured parameters.
# chkconfig: - 50 50
# description: Initializes network console logging
# config: /etc/sysconfig/netconsole
```

意思是说，这个脚本在设置网络终端机来应付登陆的意思，且配置文件在 /etc/sysconfig/netconsole 设置内！ 所以，你写的脚本如果也能够很清楚的交待，那就太棒了！

另外，本章所有的范例都可以在 http://linux.vbird.org/linux_basic/0340bashshell-scripts/scripts-20150717.tar.bz2 里头找到喔！加油～

## 12.7 重点回顾

- shell script 是利用 shell 的功能所写的一个“程序 （program）”，这个程序是使用纯文本文件，将一些 shell 的语法与指令（含外部指令）写在里面， 搭配正则表达式、管线命令与数据流重导向等功能，以达到我们所想要的处理目的
- shell script 用在系统管理上面是很好的一项工具，但是用在处理大量数值运算上， 就不够好了，因为 Shell scripts 的速度较慢，且使用的 CPU 资源较多，造成主机资源的分配不良。
- **在 Shell script 的文件中，指令的执行是从上而下、从左而右的分析与执行；**
- **shell script 的执行，至少需要有 r 的权限，若需要直接指令下达，则需要拥有 r 与 x 的权限；**
- 良好的程序撰写习惯中，第一行要宣告 shell （#!/bin/bash） ，第二行以后则宣告程序用途、版本、作者等
- 对谈式脚本可用 read 指令达成；
- 要创建每次执行脚本都有不同结果的数据，可使用 date 指令利用日期达成；
- **script 的执行若以 source 来执行时，代表在父程序的 bash 内执行之意！**
- 若需要进行判断式，可使用 test 或中括号 （ [] ） 来处理；
- **在 script 内，$0, $1, $2..., $@ 是有特殊意义的！**
- 条件判断式可使用 if...then 来判断，若是固定变量内容的情况下，可使用 case $var in ... esac 来处理
- **循环主要分为不定循环 （while, until） 以及固定循环 （for） ，配合 do, done 来达成所需任务**！
- **我们可使用 sh -x script.sh 来进行程序的 debug**

