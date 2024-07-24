# 第十章、认识与学习BASH

最近更新日期：20//

在 Linux 的环境下，如果你不懂 bash 是什么，那么其他的东西就不用学了！因为前面几章我们使用终端机下达指令的方式， 就是通过 bash 的环境来处理的喔！所以说，他很重要吧！bash 的东西非常的多，包括变量的设置与使用、 bash 操作环境的创建、数据流重导向的功能，还有那好用的管线命令！好好清一清脑门，准备用功去啰～ ^_^ 这个章节几乎是所有命令行界面 （command line） 与未来主机维护与管理的重要基础，一定要好好仔细的阅读喔！

## 10.1 认识 BASH 这个 Shell

我们在[第一章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)提到：管理计算机硬件的是操作系统的核心（kernel）。核心通过shell与用户沟通，使用户输入的指令被核心执行。那么系统中有多少种shell呢？为什么我们要使用bash？下面详细介绍。

### 10.1.1 硬件、核心与 Shell

**什么是 Shell**？

Shell 是操作系统的用户接口，它将用户的指令传递给核心执行。以下是计算机运行的基本步骤：

1. 硬件：计算机需要硬件支持，例如声卡芯片。
2. 核心管理：操作系统的核心支持硬件，并提供驱动程序。
3. 应用程序：用户输入指令，通过应用程序与核心沟通。

![硬件、核心与使用者的相关性图示](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/img/0320bash_1.jpg)

Shell 是操作系统的一部分，负责将用户指令传递给核心执行。在[第零章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#program_os)中提到，操作系统管理硬件和系统活动，用户通过应用程序与核心交互。Shell 是用户与核心之间的桥梁，通过它用户可以执行各种指令。

### 10.1.2 为什么要学命令行的 Shell？

虽然命令行的 Shell 学习起来较难，但它有许多优点：

- **一致性**：几乎所有的 Linux distributions 都使用相同的 bash Shell，使得在不同系统间迁移变得容易。
- **远程管理**：命令行的传输速度更快，更适合远程管理。
- **高效管理**：通过 Shell，系统管理员可以快速高效地管理系统。

学习 Shell 可以让你更深入地理解和操作 Linux 系统，提高效率。

### 10.1.3 系统的合法 Shell 与 /etc/shells 功能

Linux 系统中有多个可用的 Shell，如 sh、bash、tcsh 和 csh。你可以通过查看 /etc/shells 文件来了解系统中有哪些可用的 Shell。这个文件列出了系统中合法的 Shell，例如：

- /bin/sh
- /bin/bash
- /bin/tcsh
- /bin/csh

默认情况下，Linux 使用 bash 作为 Shell。合法的 Shell 列在 /etc/shells 文件中，这些 Shell 可以供用户选择和使用。

### 10.1.4 Bash Shell 的功能

bash 是 GNU 计划中的一个重要工具，也是 Linux distributions 的标准 Shell。bash 相容于 sh，并根据用户需求进行了增强。它的主要优点包括：

- **命令历史记录**：bash 能记忆使用过的指令，通过上下键可以查看历史指令。
- **命令与文件补全功能**：按 Tab 键可以自动补全命令或文件名。
- **命令别名设置功能**：通过 alias 设置命令别名，例如 alias lm='ls -al'。
- **工作控制**：支持前景和背景任务的控制。
- **程序化脚本**：支持编写 Shell 脚本，实现自动化任务。
- **万用字符**：支持使用万用字符（如 * 和 ?）进行文件名匹配。

### 10.1.5 查询指令是否为 Bash Shell 的内置命令：type

通过 type 命令可以查询某个命令是否为 bash 的内置命令。type 命令的使用方式如下：

```
bash复制代码[root@study ~]$ type [-tpa] name
选项与参数：
    不加任何选项与参数时，type 会显示出 name 是外部指令还是 bash 内置指令
    -t  ：显示 name 的类型（file、alias、builtin）
    -p  ：如果 name 为外部指令，显示完整文件名
    -a  ：显示所有包含 name 的指令，包括 alias
```

### 10.1.6 指令的下达与快速编辑按钮

在 Shell 环境中，可以使用 CtrlCtrlCtrl+u 和 CtrlCtrlCtrl+k 删除指令行中的字符，使用 CtrlCtrlCtrl+a 和 CtrlCtrlCtrl+e 移动光标到指令行的开头或结尾。

通过学习 bash Shell，你可以高效地管理 Linux 系统，提高工作效率。掌握 Shell 的使用，是成为一名优秀系统管理员的重要一步。

## 10.2 Shell 的变量功能

在 Linux 的多用户多任务环境中，每个用户登录系统后都会获得一个 bash shell，每个人可以使用 shell 下达各种指令。这其中，变量扮演了非常重要的角色。下面我们将介绍环境变量、变量的取用与设置等内容。

### 10.2.1 什么是变量？

变量是一个特定的字符串，用来代表不固定的内容。简单来说，就是用一个简单的“字眼”来取代另一个比较复杂或容易变动的数据，这样做的最大好处就是方便。例如，每个用户的邮件信箱路径可以用变量 MAIL 来表示，不同用户登录时，MAIL 的内容会指向各自的邮件信箱路径。这样程序只需读取 MAIL 变量，就能处理不同用户的邮件，而无需更改程序代码。

### 10.2.2 变量的取用与设置

**变量的取用**

使用 `echo` 命令来取用变量的内容，变量名称前需要加上 `$` 符号。例如：

```
bash复制代码echo $PATH
echo ${PATH}
```

两者都可以读取 PATH 变量的内容。

**变量的设置**

设置变量时，使用等号（=）连接变量名和内容。例如：

```
bash复制代码myname=VBird
echo ${myname}
```

设置变量时需要遵守以下规则：

1. 等号两边不能有空格。
2. 变量名只能包含字母和数字，但开头不能是数字。
3. 变量内容如果有空格，可以使用双引号或单引号包裹。

例如：

```
bash复制代码name="VBird's name"
name='VBird\'s name'
```

变量可以累加内容，例如：

```
bash
复制代码
PATH="$PATH:/home/dmtsai/bin"
```

**导出变量**

如果希望变量在子程序中也能使用，需要使用 `export` 命令将变量变成环境变量：

```
bash
复制代码
export myname
```

### 10.2.3 环境变量的功能

环境变量可以影响 bash 环境的操作，例如 PATH 变量决定了系统查找可执行文件的路径。可以使用 `env` 命令查看当前 shell 环境下的所有环境变量，也可以使用 `set` 命令查看所有变量（包括环境变量和自定义变量）。

一些常见的环境变量包括：

- **HOME**：用户的主文件夹。
- **SHELL**：当前使用的 shell 程序。
- **HISTSIZE**：历史命令记录的条数。
- **MAIL**：用户的邮件信箱路径。
- **PATH**：可执行文件的查找路径。
- **LANG**：系统的语言环境。
- **RANDOM**：随机数生成器的变量。

### 10.2.4 影响显示结果的语系变量（locale）

Linux 支持多种语言环境，可以通过 `locale` 命令查看系统支持的语言编码。常见的环境变量包括：

- **LANG**：主语言环境。
- **LC_ALL**：整体语言环境。
- **LC_CTYPE**：字符集编码。
- **LC_TIME**：时间格式。

通过设置这些变量，可以更改系统的语言显示。例如：

```
bash复制代码LANG=en_US.utf8
export LC_ALL=en_US.utf8
```

### 10.2.5 变量的有效范围

变量的有效范围与 `export` 命令有关。未被 `export` 的变量在子程序中不可见，而被 `export` 的变量则会在子程序中继承。可以使用 `declare` 命令查看变量的属性，并将变量声明为只读、整数或数组等类型。

### 10.2.6 变量键盘读取、数组与声明

可以使用 `read` 命令从键盘读取输入，将其赋值给变量：

```
bash复制代码read -p "Please enter your name: " name
echo $name
```

使用 `declare` 命令声明变量类型，例如声明整数或只读变量：

```
bash复制代码declare -i number
declare -r readonly_var
```

数组变量可以通过 `declare -a` 声明，例如：

```
bash复制代码declare -a array
array[0]="element1"
array[1]="element2"
```

### 10.2.7 与文件系统及程序的限制关系：ulimit

`ulimit` 命令用于限制用户的系统资源使用，如最大文件大小、最大内存使用量等。例如：

```
bash
复制代码
ulimit -f 10240  # 限制最大文件大小为 10MB
```

### 10.2.8 变量内容的删除、取代与替换

可以使用特定的语法删除或替换变量内容。例如：

```
bash复制代码path=${PATH}
echo ${path#/*local/bin:}  # 删除前缀
echo ${path/sbin/SBIN}  # 替换第一个匹配的字符串
echo ${path//sbin/SBIN}  # 替换所有匹配的字符串
```

还可以通过判断变量是否存在或为空来设置变量的值。例如：

```
bash
复制代码
username=${username:-root}
```

以上是 Shell 中变量的基本使用和管理方法，通过灵活运用变量，可以大大提高命令行操作的效率和灵活性。

## 10.3 命令别名与历史命令

### 10.3.1 命令别名设置：alias, unalias

命令别名是一个非常有趣且实用的功能，特别是当你需要频繁使用长指令时。例如，清除屏幕信息在 Linux 中使用 `clear`，如果你习惯使用 DOS 的 `cls`，可以通过设置命令别名实现：

```
bash
复制代码
alias cls='clear'
```

这样，每次输入 `cls` 就相当于执行了 `clear`。同样地，如果你希望简化一些长指令，例如：

```
bash
复制代码
alias lm='ls -al | more'
```

以后只需要输入 `lm` 就能执行 `ls -al | more` 这条命令。

**查看当前命令别名**

```
bash
复制代码
alias
```

**取消命令别名**

```
bash
复制代码
unalias lm
```

**命令别名与变量的区别**

命令别名是创建一个新的指令，而变量则需要通过其他指令调用其内容。

### 10.3.2 历史命令：history

Bash 提供了历史命令功能，可以查看和重新执行之前输入过的命令。使用 `history` 命令可以查看历史记录：

```
bash
复制代码
history [n]
```

`n` 是要显示的最近命令的数量。常用选项：

- `-c`：清除当前 shell 会话的历史记录。
- `-a`：将当前新增的历史记录写入历史文件（默认是 `~/.bash_history`）。
- `-r`：从历史文件读取历史记录到当前 shell 会话。
- `-w`：将当前 shell 会话的历史记录写入历史文件。

**示例**

```
bash
复制代码
history 3
```

显示最近的 3 条命令。

```
bash
复制代码
history -w
```

将当前历史记录写入 `~/.bash_history` 文件。

**重新执行历史命令**

- `!number`：执行指定编号的命令。
- `!command`：执行最近一次以 `command` 开头的命令。
- `!!`：执行上一条命令。

**示例**

```
bash
复制代码
!66
```

执行编号为 66 的命令。

```
bash
复制代码
!!
```

执行上一条命令。

```
bash
复制代码
!al
```

执行最近一次以 `al` 开头的命令。

### 同一帐号多次登录的历史记录写入问题

当同一用户同时登录多个 bash 会话时，每个会话都有自己的历史记录，只有最后一个退出的会话会将历史记录写入 `~/.bash_history` 文件。这会导致其他会话的历史记录被覆盖。

### 无法记录时间

默认情况下，历史命令不会记录命令执行的时间。如果需要记录时间，可以在 `~/.bash_logout` 文件中通过 `date` 命令来记录历史命令的时间。

**示例**

```
bash
复制代码
echo "$(date): $(history -w)" >> ~/.bash_history
```

这样可以在每次退出时记录命令执行的时间。

通过以上的命令别名和历史命令的使用，可以大大提高命令行操作的效率和便利性。在实际工作中，这些功能能够帮助你更快地完成任务并避免误操作。

## 10.4 Bash Shell 的操作环境

Bash Shell 提供了多种灵活的操作环境设置，包括命令别名、历史命令以及各种配置文件，帮助用户更高效地使用命令行界面。

### 10.4.1 路径与指令搜寻顺序

在执行命令时，系统会按照以下顺序进行搜寻和执行：

1. 以相对路径或绝对路径执行指令，例如 `/bin/ls` 或 `./ls`。
2. 查找 alias（别名）来执行指令。
3. 使用 bash 内置指令（builtin）。
4. 根据 $PATH 变量的顺序搜寻到的第一个指令来执行。

**示例**

```
bash复制代码alias echo='echo -n'
type -a echo
```

输出结果显示了命令的查找顺序：先是 alias，然后是内置指令，最后是系统路径中的指令。

### 10.4.2 Bash 的进站与欢迎讯息： /etc/issue, /etc/motd

Bash 提供了进站画面和欢迎讯息的功能。在终端登陆时会显示 `/etc/issue` 文件的内容，而登陆后显示 `/etc/motd` 文件的内容。可以通过编辑这些文件来定制登陆和欢迎信息。

**示例**

修改 `/etc/issue` 文件：

```
bash复制代码\S (terminal: \l)
Date: \d \t
Kernel \r on an \m
Welcome!
```

修改 `/etc/motd` 文件：

```
bash复制代码[root@study ~]# vim /etc/motd
Hello everyone,
Our server will be maintained at 2015/07/10 0:00 ~ 24:00.
Please don't login server at that time. ^_^
```

### 10.4.3 Bash 的环境配置文件

Bash 启动时会读取一系列的配置文件来设置操作环境，这些文件可以分为系统整体配置和用户个人偏好配置。根据登陆方式不同，读取的配置文件也有所区别：

- **Login shell** 读取 `/etc/profile` 和 `~/.bash_profile`（或 `~/.bash_login` 或 `~/.profile`）。
- **Non-login shell** 读取 `~/.bashrc`。

#### `/etc/profile`（系统整体设置）

```
bash复制代码PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
MAIL=/var/spool/mail/$USER
USER=`id -un`
HISTSIZE=1000
umask 022
```

#### `~/.bash_profile`（用户个人设置）

```
bash复制代码# .bash_profile
if [ -f ~/.bashrc ]; then
   . ~/.bashrc
fi
PATH=$PATH:$HOME/bin
export PATH
```

#### `~/.bashrc`（用户个人设置）

```
bash复制代码# .bashrc
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

if [ -f /etc/bashrc ]; then
   . /etc/bashrc
fi
```

### source 指令

可以使用 `source` 指令或小数点（.）来读取配置文件的内容，立即生效，而无需重新登陆。

```
bash复制代码source ~/.bashrc
. ~/.bashrc
```

### 10.4.4 终端机的环境设置：stty, set

#### stty

`stty` 用于设置终端的输入输出环境。

**示例**

```
bash复制代码stty -a
stty erase ^H
```

#### set

`set` 用于设置 shell 的环境参数。

**常用选项**

- `-u`：使用未定义变量时报错。
- `-v`：输出前显示原始内容。
- `-x`：执行前显示命令内容。
- `-h`：与历史命令相关。

**示例**

```
bash复制代码set -u
set -x
```

### 10.4.5 万用字符与特殊符号

Bash 中有一些非常有用的万用字符和特殊符号，可以用于文件名匹配和各种操作。

#### 万用字符

- `*`：匹配零个或多个字符。
- `?`：匹配一个任意字符。
- `[ ]`：匹配方括号中的任意一个字符。
- `[ - ]`：匹配字符范围。
- `[^ ]`：匹配不在方括号中的字符。

#### 特殊符号

- `#`：注释。
- `\`：转义字符。
- `|`：管道。
- `;`：连续命令分隔符。
- `~`：用户主目录。
- `$`：变量前缀。
- `&`：后台运行。
- `!`：逻辑非。
- `>`，`>>`：输出重定向。
- `<`，`<<`：输入重定向。
- `' '`：单引号，不解析变量。
- `" "`：双引号，解析变量。
- `` ``：命令替换。
- `( )`：子 shell。
- `{ }`：命令块。

**示例**

```
bash复制代码ll -d /etc/cron*
ll -d /etc/?????
ll -d /etc/*[0-9]*
ll -d /etc/[^a-z]*
mkdir /tmp/upper; cp -a /etc/[^a-z]* /tmp/upper
```

以上内容涵盖了 Bash Shell 操作环境的关键点，通过合理设置和使用这些功能，可以极大地提升工作效率和操作体验。

## 10.5 数据流重导向

数据流重导向 (redirect) 在 Linux 中是一个非常重要的功能，它允许将指令执行后的数据输出到其他地方，例如文件或设备，而不是默认的屏幕显示。

### 10.5.1 什么是数据流重导向

在 Linux 中，指令的输出可以分为标准输出 (STDOUT) 和标准错误输出 (STDERR)，默认情况下，它们都输出到屏幕上。数据流重导向可以将这些输出重定向到文件或其他设备。

- **标准输入 (stdin)**：代码为 0，使用 `<` 或 `<<`。
- **标准输出 (stdout)**：代码为 1，使用 `>` 或 `>>`。
- **标准错误输出 (stderr)**：代码为 2，使用 `2>` 或 `2>>`。

#### 示例和用法

1. **标准输出重定向**

   ```
   bash
   复制代码
   ll / > ~/rootfile  # 将标准输出重定向到文件 rootfile
   ```

2. **标准错误输出重定向**

   ```
   bash
   复制代码
   find /home -name .bashrc 2> errorfile  # 将错误输出重定向到文件 errorfile
   ```

3. **将标准输出和错误输出重定向到同一文件**

   ```
   bash复制代码find /home -name .bashrc > outputfile 2>&1  # 使用 2>&1 将错误输出重定向到标准输出
   find /home -name .bashrc &> outputfile      # 使用 &> 重定向所有输出
   ```

4. **使用 /dev/null 忽略输出**

   ```
   bash
   复制代码
   find /home -name .bashrc 2> /dev/null  # 忽略错误输出
   ```

5. **标准输入重定向**

   ```
   bash
   复制代码
   cat > newfile < oldfile  # 使用 oldfile 的内容替代键盘输入
   ```

6. **使用 << 定义多行输入**

   ```
   bash复制代码cat > newfile << "EOF"
   This is a test.
   OK now stop
   EOF
   ```

### 10.5.2 命令执行的判断依据： ; , &&, ||

在 Linux 中，可以通过 `;`, `&&`, 和 `||` 来一次输入并执行多个命令。

#### 分号 `;`：连续执行命令，不考虑前一个命令的执行结果

```
bash
复制代码
sync; sync; shutdown -h now
```

#### `&&` 和 `||`：根据前一个命令的执行结果来决定是否执行下一个命令

- `cmd1 && cmd2`：如果 cmd1 执行成功 (返回值为 0)，则执行 cmd2；否则不执行 cmd2。
- `cmd1 || cmd2`：如果 cmd1 执行失败 (返回值非 0)，则执行 cmd2；否则不执行 cmd2。

**示例**

1. **使用 `&&` 执行条件命令**

   ```
   bash
   复制代码
   ls /tmp/abc && touch /tmp/abc/hehe  # 如果 /tmp/abc 存在，则创建文件 hehe
   ```

2. **使用 `||` 执行条件命令**

   ```
   bash
   复制代码
   ls /tmp/abc || mkdir /tmp/abc  # 如果 /tmp/abc 不存在，则创建目录 abc
   ```

3. **综合使用 `&&` 和 `||`**

   ```
   bash
   复制代码
   ls /tmp/abc || mkdir /tmp/abc && touch /tmp/abc/hehe  # 无论 /tmp/abc 是否存在，都会创建文件 hehe
   ```

### 示例练习

1. **判断目录是否存在并显示信息**

   ```
   bash
   复制代码
   ls /tmp/vbirding && echo "exist" || echo "not exist"
   ```

2. **错误示例**

   ```
   bash
   复制代码
   ls /tmp/vbirding || echo "not exist" && echo "exist"
   ```

通过上述命令的练习，可以理解如何利用数据流重导向和命令判断来实现复杂的操作需求。这些技巧对于编写高效的 shell 脚本和执行复杂的命令序列非常有用。

## 10.6 ==未完成==