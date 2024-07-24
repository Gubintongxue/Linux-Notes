# 第十二章、学习 Shell Scripts

最近更新日期：20//

如果你真的很想要走信息这条路，并且想要管理好属于你的主机，那么，别说鸟哥不告诉你， 可以自动管理系统的好工具： Shell scripts！这家伙真的是得要好好学习学习的！ 基本上， shell script 有点像是早期的批处理文件，亦即是将一些指令汇整起来一次执行，但是 Shell script 拥有更强大的功能，那就是他可以进行类似程序 （program） 的撰写，并且不需要经过编译 （compile） 就能够执行， 真的很方便。加上我们可通过 shell script 来简化我们日常的工作管理， 而且，整个 Linux 环境中，一些服务 （services） 的启动都是通过 shell script 的， 如果你对于 script 不了解，嘿嘿！发生问题时，可真是会求助无门喔！所以，好好的学一学他吧！

## 12.1 什么是 Shell Scripts

Shell script 是一种利用 shell 的功能所编写的程序，它将一些 shell 的语法与指令写在纯文本文件中，搭配正则表达式、管线命令与数据流重导向等功能，以实现特定的处理目的。简单来说，shell script 就像是早期 DOS 的批处理文件 (.

bat)，能够将多条指令汇集在一个文件中，便于一次性执行多个指令。Shell script 提供了阵列、循环、条件与逻辑判断等功能，使其可以作为一种编程语言使用，帮助系统管理员高效地管理主机。

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
bash复制代码#!/bin/bash
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
bash复制代码chmod a+x hello.sh  # 赋予执行权限
./hello.sh
```

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

通过以上方法，可以编写出高质量、易维护的 shell script，提高工作效率。

## 12.2 简单的 Shell Script 练习

在编写完第一支 shell script 之后，现在可以进行一些简单的练习。这些练习可以帮助你更好地理解 shell script 的基本概念和用法。

### 12.2.1 简单范例

以下是一些常见的脚本范例，这些脚本在实际使用中非常有用。

#### 对谈式脚本：变量内容由使用者决定

此脚本将提示用户输入他们的名字，并显示完整名字。

```
bash复制代码#!/bin/bash
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
bash复制代码#!/bin/bash
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
bash复制代码#!/bin/bash
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
bash复制代码#!/bin/bash
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

### 12.2.2 Script 的执行方式差异（source, sh script, ./script）

不同的 script 执行方式会影响脚本的执行结果，特别是对 bash 环境变量的影响很大。脚本的执行方式包括：

#### 利用直接执行的方式来执行 script

当使用绝对路径、相对路径或 PATH 环境变量中的路径来执行脚本时，脚本会在子进程中运行，父进程中的变量不会受到影响。

```
bash复制代码#!/bin/bash
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
bash
复制代码
sh showname.sh
```

此时，脚本中的变量不会在父进程中生效。

#### 利用 source 来执行脚本

使用 source 或 .（点号）来执行脚本时，脚本会在当前 shell 环境中执行，变量在父进程中生效。

```
bash
复制代码
source showname.sh
```

此时，脚本中的变量会在父进程中生效，可以在当前 shell 中使用。

通过这些练习，你可以更好地理解 shell script 的基本用法和执行方式。继续进行更复杂的练习，你会发现 shell script 是管理和自动化 Linux 系统的强大工具。

## 12.3 善用判断式

在第十章中，我们提到过 `$?` 这个变量所代表的意义。此外，也通过 `&&` 及 `||` 来作为前一个指令执行回传值对于后一个指令是否要进行的依据。如果想要判断一个目录是否存在，当时我们使用的是 `ls` 这个指令搭配数据流重导向，最后配合 `$?` 来决定后续的指令进行与否。但是，是否有更简单的方式可以来进行“条件判断”呢？有的，那就是 `test` 这个指令。

### 12.3.1 利用 test 指令的测试功能

当我要检测系统上面某些文件或者是相关的属性时，利用 `test` 这个指令来工作非常方便。例如我要检查 `/dmtsai` 是否存在时，使用：

```
bash
复制代码
[dmtsai@study ~]$ test -e /dmtsai
```

执行结果并不会显示任何讯息，但最后我们可以通过 `$?` 或 `&&` 及 `||` 来展现结果。例如：

```
bash复制代码[dmtsai@study ~]$ test -e /dmtsai && echo "exist" || echo "Not exist"
Not exist  # 结果显示不存在
```

这里我们知道 `-e` 是测试一个“东西”是否存在，如果还想要测试该文件名是什么类型，还有其他标志可以使用：

#### 文件类型判断

- `-e`：该“文件名”是否存在（常用）。
- `-f`：该“文件名”是否存在且为文件（常用）。
- `-d`：该“文件名”是否存在且为目录（常用）。
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
bash复制代码[dmtsai@study bin]$ vim file_perm.sh
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

### 12.3.2 利用判断符号 `[` `]`

除了 `test` 之外，还可以利用判断符号 `[` `]` 来进行数据的判断。例如，要判断 `${HOME}` 这个变量是否为空：

```
bash
复制代码
[dmtsai@study ~]$ [ -z "${HOME}" ] ; echo $?
```

使用中括号必须特别注意，因为中括号用在很多地方，包括万用字符与正则表达式等。如果要在 bash 的语法中使用中括号作为 shell 的判断式时，必须确保中括号的两端有空格字符来分隔。例如：

```
bash
复制代码
[ "${HOME}" == "${MAIL}" ]
```

必须确保每个元素之间有空格分隔。此外，变量最好都以双引号括起来，常数最好都以单或双引号括起来。比如：

```
bash
复制代码
[ "${name}" == "VBird" ]
```

如果没有空格分隔，会出现错误信息。因此要特别注意这些细节。

利用中括号的判断来做一个小案例：

1. 当执行一个程序时，程序会让用户选择 Y 或 N。
2. 如果用户输入 Y 或 y，则显示“OK, continue”。
3. 如果用户输入 N 或 n，则显示“Oh, interrupt!”。
4. 如果不是 Y/y/N/n 之内的其他字符，则显示“I don't know what your choice is”。

```
bash复制代码[dmtsai@study bin]$ vim ans_yn.sh
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

### 12.3.3 Shell script 的默认变量（$0, $1...）

指令可以带有选项与参数，例如 `ls -la` 可以查看包含隐藏文件的所有属性与权限。那么 shell script 能不能在脚本文件名后面带有参数呢？可以。我们来举个例子：

```
bash复制代码/path/to/scriptname opt1 opt2 opt3 opt4
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
bash复制代码[dmtsai@study bin]$ vim how_paras.sh
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
bash复制代码[dmtsai@study bin]$ sh how_paras.sh theone haha quot
The script name is        ==> how_paras.sh       # 文件名
Total parameter number is ==> 3                  # 参数个数
Your whole parameter is   ==> 'theone haha quot' # 参数内容
The 1st parameter         ==> theone             # 第一个参数
The 2nd parameter         ==> haha               # 第二个参数
```

#### shift：造成参数变量号码偏移

除了上述的变量之外，还有一个 `shift` 命令可以进行参数偏移。以下是一个示例，展示了参数偏移的效果：

```
bash复制代码[dmtsai@study bin]$ vim shift_paras.sh
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
bash复制代码[dmtsai@study bin]$ sh shift_paras.sh one two three four five six
Total parameter number is ==> 6   # 最原始的参数变量情况
Your whole parameter is   ==> 'one two three four five six'
Total parameter number is ==> 5   # 第一次偏移，看下面发现第一个 one 不见了
Your whole parameter is   ==> 'two three four five six'
Total parameter number is ==> 2   # 第二次偏移掉三个，two three four 不见了
Your whole parameter is   ==> 'five six'
```

通过以上示例，你可以看到 `shift` 命令如何影响参数变量。第一次执行 `shift` 后，参数变量的个数减少一个，第二次执行 `shift 3` 后，参数变量的个数减少三个。这样可以方便地处理参数列表中的参数。

了解了这些基本的 shell script 技巧后，接下来我们将讨论更复杂的条件判断和流程控制，以便编写更强大的脚本程序。

### 12.4 条件判断式

在编写程序时，条件判断式是非常重要的工具。它可以根据某些数据判断程序的执行流程。下面，我们将介绍如何使用条件判断式来编写更复杂的 shell 脚本。

#### 12.4.1 利用 if .... then

`if .... then` 是最常见的条件判断式，用于根据条件来执行不同的指令。基本语法如下：

```
bash复制代码if [ 条件判断式 ]; then
    # 当条件判断式成立时，可以进行的指令工作内容；
fi  # 将 if 反过来写，就成为 fi 啦！结束 if 之意！
```

可以通过多个 `if` 语句进行多次判断：

```
bash复制代码if [ 条件判断式一 ]; then
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
bash复制代码[dmtsai@study bin]$ vim ans_yn-3.sh
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
bash复制代码[dmtsai@study bin]$ vim hello-2.sh
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

### 12.4.2 利用 case ..... esac 判断

`case .... in .... esac` 是另一种条件判断语法，特别适用于需要处理固定值的情况。语法如下：

```
bash复制代码case $变量名称 in
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
bash复制代码[dmtsai@study bin]$ vim hello-3.sh
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

### 12.4.3 利用 function 功能

函数（function）是将一段代码封装成一个独立的模块，可以多次调用。它的基本语法如下：

```
bash复制代码function fname（） {
    # 程序段
}
```

#### 案例：函数使用

我们可以将前面的脚本重构为使用函数的版本：

```
bash复制代码[dmtsai@study bin]$ vim show123-2.sh
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

### 总结

通过本章的学习，你已经掌握了如何使用条件判断和函数来编写更复杂和高效的 shell 脚本。继续练习这些技能，并尝试编写更复杂的脚本，以提高你的脚本编写能力。

### 12.5 循环（loop）

在编写脚本时，循环语句是非常重要的。循环语句允许我们不断地执行某个程序段，直到满足特定的条件。主要有两种类型的循环：不定循环和固定循环。

### 12.5.1 while do done 和 until do done（不定循环）

**while 循环**

`while` 循环在条件成立时执行循环，直到条件不成立时停止。基本语法如下：

```
bash复制代码while [ condition ]
do
    程序段落
done
```

**until 循环**

`until` 循环在条件不成立时执行循环，直到条件成立时停止。基本语法如下：

```
bash复制代码until [ condition ]
do
    程序段落
done
```

#### 案例：用户输入控制循环

让用户输入 `yes` 或 `YES` 才结束程序，否则继续提示用户输入：

```
bash复制代码[dmtsai@study bin]$ vim yes_to_stop.sh
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
bash复制代码[dmtsai@study bin]$ vim yes_to_stop-2.sh
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
bash复制代码[dmtsai@study bin]$ vim cal_1_100.sh
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
bash复制代码for var in con1 con2 con3 ...
do
    程序段
done
```

#### 案例：输出动物名称

输出三种动物的名称：

```
bash复制代码[dmtsai@study bin]$ vim show_animal.sh
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
bash复制代码[dmtsai@study bin]$ vim userid.sh
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
bash复制代码[dmtsai@study bin]$ vim pingip.sh
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
bash复制代码for (( 初始值; 限制值; 执行步阶 ))
do
    程序段
done
```

#### 案例：计算 1 到用户输入值的和

```
bash复制代码[dmtsai@study bin]$ vim cal_1_100-2.sh
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
bash复制代码[dmtsai@study bin]$ vim what_to_eat.sh
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
bash复制代码[dmtsai@study bin]$ vim what_to_eat-2.sh
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
- 在 Shell script 的文件中，指令的执行是从上而下、从左而右的分析与执行；
- shell script 的执行，至少需要有 r 的权限，若需要直接指令下达，则需要拥有 r 与 x 的权限；
- 良好的程序撰写习惯中，第一行要宣告 shell （#!/bin/bash） ，第二行以后则宣告程序用途、版本、作者等
- 对谈式脚本可用 read 指令达成；
- 要创建每次执行脚本都有不同结果的数据，可使用 date 指令利用日期达成；
- script 的执行若以 source 来执行时，代表在父程序的 bash 内执行之意！
- 若需要进行判断式，可使用 test 或中括号 （ [] ） 来处理；
- 在 script 内，$0, $1, $2..., $@ 是有特殊意义的！
- 条件判断式可使用 if...then 来判断，若是固定变量内容的情况下，可使用 case $var in ... esac 来处理
- 循环主要分为不定循环 （while, until） 以及固定循环 （for） ，配合 do, done 来达成所需任务！
- 我们可使用 sh -x script.sh 来进行程序的 debug