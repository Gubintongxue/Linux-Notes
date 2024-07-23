# 第六章、Linux 文件与目录管理

最近更新日期：20//

在前一章我们认识了Linux系统下的文件权限概念以及目录的配置说明。 在这个章节当中，我们就直接来进一步的操作与管理文件及目录吧！包括在不同的目录间变换、 创建与删除目录、创建与删除文件，还有寻找文件、查阅文件内容等等，都会在这个章节作个简单的介绍啊！

### 6.1 目录与路径

在本章中，主要讨论Linux系统中的路径问题，特别是绝对路径与相对路径的概念，以及如何操作目录。

#### 6.1.1 相对路径与绝对路径

**绝对路径**：从根目录（/）开始的路径。例如：`/usr/share/doc`。 **相对路径**：相对于当前工作目录的路径。例如：从`/usr/share/doc`到`/usr/share/man`可以写成`cd ../man`。

**用途**：

- **绝对路径**：在写程序（如shell脚本）时，推荐使用绝对路径，因为其写法虽然繁琐，但可以保证路径的正确性，不会因工作环境变化而出错。
- **相对路径**：在软件开发中，使用相对路径可以使软件更加灵活，不同用户可以将软件安装在不同的目录中而不影响使用。

#### 6.1.2 目录的相关操作

**特殊目录符号**：

- `.`：代表当前目录。
- `..`：代表上一级目录。
- `-`：代表前一个工作目录。
- `~`：代表当前用户的主目录。
- `~account`：代表指定用户的主目录。

**常见操作命令**：

- `cd`：变换目录。
- `pwd`：显示当前目录。
- `mkdir`：创建新目录。
- `rmdir`：删除空目录。

**详细说明**：

1. **cd（变换目录）**：

   - 例子：

     ```
     bash复制代码cd ~dmtsai  # 切换到dmtsai用户的主目录
     cd ~        # 切换到当前用户的主目录
     cd          # 切换到当前用户的主目录
     cd ..       # 切换到上一级目录
     cd -        # 切换到前一个工作目录
     cd /var/spool/mail  # 绝对路径
     cd ../postfix       # 相对路径
     ```

2. **pwd（显示当前目录）**：

   - 选项与参数：

     - `-P`：显示真实路径，而非链接路径。

   - 例子：

     ```
     bash复制代码pwd        # 显示当前目录
     pwd -P     # 显示真实路径
     ```

3. **mkdir（创建新目录）**：

   - 选项与参数：

     - `-m`：设置目录权限。
     - `-p`：递归创建所需目录。

   - 例子：

     ```
     bash复制代码mkdir test                       # 创建名为test的目录
     mkdir -p test1/test2/test3/test4 # 递归创建多层目录
     mkdir -m 711 test2               # 创建权限为rwx--x--x的目录
     ```

4. **rmdir（删除空目录）**：

   - 选项与参数：

     - `-p`：递归删除空目录。

   - 例子：

     ```
     bash复制代码rmdir test                       # 删除名为test的空目录
     rmdir -p test1/test2/test3/test4 # 递归删除多层空目录
     ```

#### 6.1.3 关于可执行文件路径的变量：$PATH

**$PATH**：环境变量PATH定义了系统查找可执行文件的目录。执行命令时，系统会依照PATH的设置依次查找目录中的可执行文件。

**查看PATH**：

```
bash
复制代码
echo $PATH
```

**示例**：

- 例题：如果将

  ```
  ls
  ```

  移动到

  ```
  /root
  ```

  目录，会发生什么？

  1. 不能直接输入`ls`来执行。

  2. 可以通过绝对路径或相对路径执行

     ```
     ls
     ```

     ：

     ```
     bash复制代码/root/ls
     ./ls
     ```

  3. 将

     ```
     /root
     ```

     加入到PATH中：

     ```
     bash
     复制代码
     PATH="${PATH}:/root"
     ```

- 例题：在PATH中包含多个同名文件时，优先执行先找到的文件。

**注意**：

- 不建议将“.”加入PATH中，因为这会使得当前目录中的文件容易被误执行，尤其是在公共目录（如/tmp）中。

总结：

- 绝对路径和相对路径各有用途，需灵活使用。
- 熟练掌握常见目录操作命令（cd、pwd、mkdir、rmdir）。
- 理解并合理设置$PATH，以确保系统能正确查找并执行命令。

### 6.2 文件与目录管理

本节讨论了关于文件和目录的一些基本管理操作，包括“显示属性”、“拷贝”、“删除文件”和“移动文件或目录”等。

#### 6.2.1 文件与目录的检视：`ls`

`ls` 是 Linux 系统中最常用的指令之一，用于显示文件和目录的信息。

**常用选项**：

- `-a`：显示所有文件，包括隐藏文件。
- `-A`：显示所有文件，但不包括 `.` 和 `..`。
- `-d`：仅显示目录本身，而不是目录中的文件。
- `-h`：以人类易读的方式显示文件大小（如 GB, KB）。
- `-l`：显示详细信息，包括权限、所有者、大小等。
- `-r`：反向排序。
- `-R`：递归显示目录及其子目录。
- `-S`：按文件大小排序。
- `-t`：按时间排序。

**示例**：

1. 显示主目录下所有文件及其详细信息，包括隐藏文件：

   ```
   bash
   复制代码
   ls -al ~
   ```

2. 显示文件类型而不显示颜色：

   ```
   bash
   复制代码
   ls -alF --color=never ~
   ```

3. 显示文件的完整修改时间：

   ```
   bash
   复制代码
   ls -al --full-time ~
   ```

#### 6.2.2 复制、删除与移动：`cp`, `rm`, `mv`

**复制文件或目录：`cp`**

**常用选项**：

- `-a`：相当于 `-dr --preserve=all`，复制所有属性。
- `-d`：复制符号链接而非文件本身。
- `-f`：强制覆盖目标文件。
- `-i`：在覆盖目标文件前询问用户。
- `-p`：连同文件的属性一起复制。
- `-r`：递归复制目录。
- `-s`：复制为符号链接。
- `-u`：仅在源文件比目标文件新或目标文件不存在时才复制。

**示例**：

1. 将 `.bashrc` 复制到 `/tmp` 并更名为 `bashrc`：

   ```
   bash
   复制代码
   cp ~/.bashrc /tmp/bashrc
   ```

2. 复制 `/var/log/wtmp` 到当前目录，并保留所有属性：

   ```
   bash
   复制代码
   cp -a /var/log/wtmp .
   ```

3. 创建符号链接和硬链接：

   ```
   bash复制代码cp -s bashrc bashrc_slink
   cp -l bashrc bashrc_hlink
   ```

**删除文件或目录：`rm`**

**常用选项**：

- `-f`：强制删除，忽略不存在的文件。
- `-i`：在删除前询问用户。
- `-r`：递归删除目录。

**示例**：

1. 删除 `/tmp` 下的 `bashrc` 文件：

   ```
   bash
   复制代码
   rm -i /tmp/bashrc
   ```

2. 递归删除 `/tmp/etc` 目录：

   ```
   bash
   复制代码
   rm -r /tmp/etc
   ```

3. 删除以 `-` 开头的文件：

   ```
   bash
   复制代码
   rm ./-aaa-
   ```

**移动文件与目录，或更名：`mv`**

**常用选项**：

- `-f`：强制覆盖目标文件。
- `-i`：在覆盖目标文件前询问用户。
- `-u`：仅在源文件比目标文件新或目标文件不存在时才移动。

**示例**：

1. 将 `bashrc` 文件移动到 `mvtest` 目录：

   ```
   bash
   复制代码
   mv bashrc mvtest
   ```

2. 将 `mvtest` 目录更名为 `mvtest2`：

   ```
   bash
   复制代码
   mv mvtest mvtest2
   ```

3. 将多个文件移动到 `mvtest2` 目录：

   ```
   bash
   复制代码
   mv bashrc1 bashrc2 mvtest2
   ```

#### 6.2.3 取得路径的文件名称与目录名称

`basename` 和 `dirname` 用于分别获取路径中的文件名和目录名。

**示例**：

1. 获取文件名：

   ```
   bash复制代码basename /etc/sysconfig/network
   # 输出：network
   ```

2. 获取目录名：

   ```
   bash复制代码dirname /etc/sysconfig/network
   # 输出：/etc/sysconfig
   ```

这些命令和操作是 Linux 文件与目录管理的基础，掌握它们对管理系统和开发应用都非常重要。

### 6.3 文件内容查阅

在 Linux 系统中，有多种命令可以用来查阅文件内容，包括 `cat`、`more`、`less`、`head`、`tail` 等。根据需求不同，这些命令可以显示文件的全部内容或部分内容，适合处理小型和大型文件。

#### 6.3.1 直接检视文件内容

**常用命令**：

- **cat**（由第一行开始显示文件内容）

  ```
  bash
  复制代码
  cat [-AbEnTv] 文件名
  ```

  常用选项：

  - `-A`：显示所有不可见字符。
  - `-b`：对非空白行编号。
  - `-n`：对所有行编号。
  - `-T`：将 `tab` 显示为 `^I`。
  - `-v`：显示非打印字符。

  示例：

  ```
  bash
  复制代码
  cat -n /etc/issue
  ```

- **tac**（从最后一行开始显示）

  ```
  bash
  复制代码
  tac /etc/issue
  ```

- **nl**（显示时添加行号）

  ```
  bash
  复制代码
  nl [-bnw] 文件名
  ```

  常用选项：

  - `-b a`：对所有行编号。
  - `-b t`：对非空行编号。
  - `-n ln`：行号在左侧。
  - `-n rn`：行号在右侧，不加 0。
  - `-n rz`：行号在右侧，补齐 0。
  - `-w`：行号字段宽度。

  示例：

  ```
  bash
  复制代码
  nl -b a -n rz -w 3 /etc/issue
  ```

#### 6.3.2 可翻页检视

**常用命令**：

- **more**（一页一页显示）

  ```
  bash
  复制代码
  more /etc/man_db.conf
  ```

  操作：

  - 空格键：向下翻一页。
  - Enter：向下翻一行。
  - `/字符串`：向下搜索字符串。
  - `:f`：显示文件名和当前行数。
  - `q`：退出。
  - `b` 或 `[ctrl]-b`：向上翻页。

- **less**（比 `more` 功能更强）

  ```
  bash
  复制代码
  less /etc/man_db.conf
  ```

  操作：

  - 空格键：向下翻一页。
  - `[pagedown]`：向下翻一页。
  - `[pageup]`：向上翻一页。
  - `/字符串`：向下搜索字符串。
  - `?字符串`：向上搜索字符串。
  - `n`：重复前一个搜索。
  - `N`：反向重复前一个搜索。
  - `g`：跳到文件头。
  - `G`：跳到文件尾。
  - `q`：退出。

#### 6.3.3 数据撷取

**常用命令**：

- **head**（取出前几行）

  ```
  bash
  复制代码
  head [-n number] 文件名
  ```

  示例：

  ```
  bash
  复制代码
  head -n 20 /etc/man_db.conf
  ```

- **tail**（取出后几行）

  ```
  bash
  复制代码
  tail [-n number] 文件名
  ```

  示例：

  ```
  bash
  复制代码
  tail -n 20 /etc/man_db.conf
  ```

  持续监视文件：

  ```
  bash
  复制代码
  tail -f /var/log/messages
  ```

  例题：显示 `/etc/man_db.conf` 的第 11 到第 20 行

  ```
  bash
  复制代码
  head -n 20 /etc/man_db.conf | tail -n 10
  ```

#### 6.3.4 非纯文本文件：od

**od** 命令用于查看二进制文件内容。

```
bash
复制代码
od [-t TYPE] 文件名
```

常用选项：

- `-t c`：以 ASCII 字符显示。
- `-t oCc`：以 8 进制显示字符和 ASCII 对照。

示例：

```
bash
复制代码
od -t c /usr/bin/passwd
```

#### 6.3.5 修改文件时间或创建新文件：touch

**touch** 命令用于修改文件时间或创建新文件。

```
bash
复制代码
touch [-acdmt] 文件名
```

常用选项：

- `-a`：仅修改 access time。
- `-c`：仅修改时间，文件不存在则不创建新文件。
- `-d`：指定日期。
- `-m`：仅修改 modification time。
- `-t`：指定时间。

示例：

1. 创建一个空文件：

   ```
   bash
   复制代码
   touch testfile
   ```

2. 修改文件的日期和时间：

   ```
   bash
   复制代码
   touch -d "2 days ago" testfile
   ```

3. 将文件时间设为特定日期：

   ```
   bash
   复制代码
   touch -t 201406150202 testfile
   ```

通过掌握这些命令和选项，可以有效地管理和查阅文件内容，提高工作效率。

### 6.4 文件与目录的默认权限与隐藏权限

#### 6.4.1 文件默认权限：umask

在 Linux 系统中，创建文件或目录时，默认的权限由 `umask` 决定。`umask` 指定了新创建文件或目录的默认权限。

**如何查看和设置 umask**：

```
bash复制代码[root@study ~]# umask
0022  # 与一般权限有关的是后面三个数字！
[root@study ~]# umask -S
u=rwx,g=rx,o=rx  # 以符号方式显示权限
```

**默认权限**：

- 新创建文件的最大权限为 `666`（即 `-rw-rw-rw-`）。
- 新创建目录的最大权限为 `777`（即 `drwxrwxrwx`）。

`umask` 的值是减去的权限，因此：

- 如果 `umask` 为 `022`，文件的默认权限是 `666 - 022 = 644`（即 `-rw-r--r--`）。
- 如果 `umask` 为 `022`，目录的默认权限是 `777 - 022 = 755`（即 `drwxr-xr-x`）。

**示例**：

```
bash复制代码[root@study ~]# umask
0022
[root@study ~]# touch test1
[root@study ~]# mkdir test2
[root@study ~]# ll -d test*
-rw-r--r--. 1 root root 0 Jun 16 01:11 test1
drwxr-xr-x. 2 root root 6 Jun 16 01:11 test2
```

**设置 umask**：

```
bash复制代码[root@study ~]# umask 002
[root@study ~]# touch test3
[root@study ~]# mkdir test4
[root@study ~]# ll -d test[34]
-rw-rw-r--. 1 root root 0 Jun 16 01:12 test3
drwxrwxr-x. 2 root root 6 Jun 16 01:12 test4
```

#### 6.4.2 文件隐藏属性

Linux 文件系统中还有一些隐藏属性，通过 `chattr` 设置，通过 `lsattr` 查看。这些属性主要用于系统安全。

**常见隐藏属性**：

- `A`：不修改访问时间（`atime`）。
- `S`：同步更新文件。
- `a`：只能追加数据。
- `c`：自动压缩文件。
- `d`：不进行 dump 备份。
- `i`：文件不可修改。
- `s`：彻底删除文件。
- `u`：可恢复删除文件。

**设置和查看隐藏属性**：

```
bash复制代码[root@study ~]# chattr +i attrtest  # 设置不可修改属性
[root@study ~]# lsattr attrtest
----i---------- attrtest

[root@study ~]# chattr -i attrtest  # 取消不可修改属性
```

#### 6.4.3 文件特殊权限：SUID, SGID, SBIT

除了基本权限外，Linux 还有一些特殊权限，如 SUID、SGID 和 SBIT。

**SUID**：

- 作用于二进制文件。
- 执行者在执行该程序时，获得程序拥有者的权限。
- 用途：如 `passwd` 命令。

**SGID**：

- 作用于二进制文件和目录。
- 执行者在执行该程序时，获得程序群组的权限。
- 用途：如 `locate` 命令。

**SBIT**：

- 作用于目录。
- 在目录中创建文件的用户，只有自己和 root 能删除该文件。
- 用途：如 `/tmp` 目录。

**设置特殊权限**：

- 使用数字方式：`chmod 4755 filename`（设置 SUID）。
- 使用符号方式：`chmod u+s filename`。

**示例**：

```
bash复制代码[root@study tmp]# touch test
[root@study tmp]# chmod 4755 test; ls -l test
-rwsr-xr-x 1 root root 0 Jun 16 02:53 test

[root@study tmp]# chmod 6755 test; ls -l test
-rwsr-sr-x 1 root root 0 Jun 16 02:53 test

[root@study tmp]# chmod 1755 test; ls -l test
-rwxr-xr-t 1 root root 0 Jun 16 02:53 test

[root@study tmp]# chmod 7666 test; ls -l test
-rwSrwSrwT 1 root root 0 Jun 16 02:53 test
```

#### 6.4.4 观察文件类型：file

使用 `file` 命令可以查看文件的基本类型信息，如 ASCII 文件、二进制文件等。

**示例**：

```
bash复制代码[root@study ~]# file ~/.bashrc
/root/.bashrc: ASCII text

[root@study ~]# file /usr/bin/passwd
/usr/bin/passwd: setuid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=0xbf35571e607e317bf107b9bcf65199988d0ed5ab, stripped

[root@study ~]# file /var/lib/mlocate/mlocate.db
/var/lib/mlocate/mlocate.db: data
```

通过这些命令，用户可以详细了解文件的权限设置、隐藏属性和基本类型，从而更好地管理和使用文件系统。

### 6.5 指令与文件的搜寻

#### 6.5.1 指令文件名的搜寻

在终端机中，我们可以通过 `which` 和 `type` 命令来找到指令的完整文件路径。

**which**（寻找“可执行文件”）

```
bash复制代码[root@study ~]# which [-a] command
选项与参数：
-a ：列出 PATH 目录中所有能找到的同名指令，而不止第一个被找到的指令名称
```

**示例**：

```
bash复制代码[root@study ~]# which ifconfig
/sbin/ifconfig 

[root@study ~]# which which
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
        /bin/alias
        /usr/bin/which

[root@study ~]# which history
/usr/bin/which: no history in (/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin)
```

**type**（查看指令类型）

```
bash复制代码[root@study ~]# type history
history is a shell builtin
```

#### 6.5.2 文件文件名的搜寻

Linux 系统提供了多个文件搜索命令，包括 `whereis`、`locate` 和 `find`。

**whereis**（从特定目录中寻找文件文件名）

```
bash复制代码[root@study ~]# whereis [-bmsu] 文件或目录名
选项与参数：
-l ：列出 whereis 会去查询的几个主要目录
-b ：只找 binary 格式的文件
-m ：只找说明文档（manual）路径下的文件
-s ：只找 source 来源文件
-u ：搜寻不在上述三个项目中的其他特殊文件
```

**示例**：

```
bash复制代码[root@study ~]# whereis ifconfig
ifconfig: /sbin/ifconfig /usr/share/man/man8/ifconfig.8.gz

[root@study ~]# whereis -m passwd
passwd: /usr/share/man/man1/passwd.1.gz /usr/share/man/man5/passwd.5.gz
```

**locate / updatedb**

```
bash复制代码[root@study ~]# locate [-ir] keyword
选项与参数：
-i  ：忽略大小写差异
-c  ：不输出文件名，仅计算找到的文件数量
-l  ：仅输出几行，例如输出五行则是 -l 5
-S  ：输出 locate 所使用的数据库文件的相关信息
-r  ：接正则表达式显示方式
```

**示例**：

```
bash复制代码[root@study ~]# locate -l 5 passwd
/etc/passwd
/etc/passwd-
/etc/pam.d/passwd
/etc/security/opasswd
/usr/bin/gpasswd

[root@study ~]# locate -S
Database /var/lib/mlocate/mlocate.db:
        8,086 directories
        109,605 files
        5,190,295 Bytes in file names
        2,349,150 Bytes used to store database
```

**更新数据库**：

```
bash
复制代码
[root@study ~]# updatedb
```

**find**

```
bash
复制代码
[root@study ~]# find [PATH] [option] [action]
```

**示例**：

```
bash复制代码# 搜寻过去24小时内有更动过内容的文件
[root@study ~]# find / -mtime 0

# 搜寻比 /etc/passwd 更新的文件
[root@study ~]# find /etc -newer /etc/passwd
```

**其他常用选项**：

```
bash复制代码# 按用户、群组搜寻
[root@study ~]# find /home -user dmtsai
[root@study ~]# find / -nouser

# 按文件名、文件类型、权限搜寻
[root@study ~]# find / -name passwd
[root@study ~]# find /run -type s
[root@study ~]# find / -perm /7000

# 结合其他指令进行操作
[root@study ~]# find /usr/bin /usr/sbin -perm /7000 -exec ls -l {} \;
[root@study ~]# find / -size +1M
```

### 6.5 总结

1. **指令文件名的搜寻**：
   - `which`：查找可执行文件路径。
   - `type`：查看指令类型。
2. **文件文件名的搜寻**：
   - `whereis`：从特定目录中查找文件文件名。
   - `locate`：利用数据库快速查找文件文件名。
   - `updatedb`：更新 locate 使用的数据库。
   - `find`：强大的文件搜索工具，可以结合多种条件和操作。

通过这些命令，可以有效地查找系统中的文件和指令，方便进行配置和维护。

## 6.6 极重要的复习！权限与指令间的关系（原文内容）

我们知道权限对于使用者帐号来说是非常重要的，因为他可以限制使用者能不能读取/创建/删除/修改文件或目录！ 在这一章我们介绍了很多文件系统的管理指令，第五章则介绍了很多文件权限的意义。在这个小节当中， 我们就将这两者结合起来，说明一下什么指令在什么样的权限下才能够运行吧！^_^

一、让使用者能进入某目录成为“可工作目录”的基本权限为何：

- 可使用的指令：例如 cd 等变换工作目录的指令；
- 目录所需权限：使用者对这个目录至少需要具有 x 的权限
- 额外需求：如果使用者想要在这个目录内利用 ls 查阅文件名，则使用者对此目录还需要 r 的权限。

二、使用者在某个目录内读取一个文件的基本权限为何？

- 可使用的指令：例如本章谈到的 cat, more, less等等
- 目录所需权限：使用者对这个目录至少需要具有 x 权限；
- 文件所需权限：使用者对文件至少需要具有 r 的权限才行！

三、让使用者可以修改一个文件的基本权限为何？

- 可使用的指令：例如 [nano](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#nano) 或未来要介绍的 [vi](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 编辑器等；
- 目录所需权限：使用者在该文件所在的目录至少要有 x 权限；
- 文件所需权限：使用者对该文件至少要有 r, w 权限

四、让一个使用者可以创建一个文件的基本权限为何？

- 目录所需权限：使用者在该目录要具有 w,x 的权限，重点在 w 啦！

五、让使用者进入某目录并执行该目录下的某个指令之基本权限为何？

- 目录所需权限：使用者在该目录至少要有 x 的权限；
- 文件所需权限：使用者在该文件至少需要有 x 的权限

例题：让一个使用者 dmtsai 能够进行“cp /dir1/file1 /dir2”的指令时，请说明 dir1, file1, dir2 的最小所需权限为何？答：执行 cp 时， dmtsai 要“能够读取来源文件，并且写入目标文件！”所以应参考上述第二点与第四点的说明！ 因此各文件/目录的最小权限应该是：

- dir1 ：至少需要有 x 权限；
- file1：至少需要有 r 权限；
- dir2 ：至少需要有 w, x 权限。

例题：有一个文件全名为 /home/student/www/index.html ，各相关文件/目录的权限如下：

```
drwxr-xr-x 23 root    root    4096 Sep 22 12:09 /
drwxr-xr-x  6 root    root    4096 Sep 29 02:21 /home
drwx------  6 student student 4096 Sep 29 02:23 /home/student
drwxr-xr-x  6 student student 4096 Sep 29 02:24 /home/student/www
-rwxr--r--  6 student student  369 Sep 29 02:27 /home/student/www/index.html
```

请问 vbird 这个帐号（不属于student群组）能否读取 index.html 这个文件呢？答：虽然 www 与 index.html 是可以让 vbird 读取的权限，但是因为目录结构是由根目录一层一层读取的， 因此 vbird 可进入 /home 但是却不可进入 /home/student/ ，既然连进入 /home/student 都不许了， 当然就读不到 index.html 了！所以答案是“vbird不会读取到 index.html 的内容”喔！

那要如何修改权限呢？其实只要将 /home/student 的权限修改为最小 711 ，或者直接给予 755 就可以啰！ 这可是很重要的概念喔！

## 6.7 重点回顾

- 绝对路径：“一定由根目录 / 写起”；相对路径：“不由 / 写起，而是由相对当前目录写起”
- 特殊目录有：., .., -, ~, ~account需要注意；
- 与目录相关的指令有：cd, mkdir, rmdir, pwd 等重要指令；
- rmdir 仅能删除空目录，要删除非空目录需使用“ rm -r ”指令；
- 使用者能使用的指令是依据 PATH 变量所规定的目录去搜寻的；
- ls 可以检视文件的属性，尤其 -d, -a, -l 等选项特别重要！
- 文件的复制、删除、移动可以分别使用：cp, rm , mv等指令来操作；
- 检查文件的内容（读档）可使用的指令包括有：cat, tac, nl, more, less, head, tail, od 等
- cat -n 与 nl 均可显示行号，但默认的情况下，空白行会不会编号并不相同；
- touch 的目的在修改文件的时间参数，但亦可用来创建空文件；
- 一个文件记录的时间参数有三种，分别是 access time（atime）, status time （ctime）, modification time（mtime），ls 默认显示的是 mtime。
- 除了传统的rwx权限之外，在Ext2/Ext3/Ext4/xfs文件系统中，还可以使用chattr与lsattr设置及观察隐藏属性。 常见的包括只能新增数据的 +a 与完全不能更动文件的 +i 属性。
- 新建文件/目录时，新文件的默认权限使用 umask 来规范。默认目录完全权限为drwxrwxrwx， 文件则为-rw-rw-rw-。
- 文件具有SUID的特殊权限时，代表当使用者执行此一binary程序时，在执行过程中使用者会暂时具有程序拥有者的权限
- 目录具有SGID的特殊权限时，代表使用者在这个目录下面新建的文件之群组都会与该目录的群组名称相同。
- 目录具有SBIT的特殊权限时，代表在该目录下使用者创建的文件只有自己与root能够删除！
- 观察文件的类型可以使用 file 指令来观察；
- 搜寻指令的完整文件名可用 which 或 type ，这两个指令都是通过 PATH 变量来搜寻文件名；
- 搜寻文件的完整文件名可以使用 whereis 找特定目录或 locate 到数据库去搜寻，而不实际搜寻文件系统；
- 利用 find 可以加入许多选项来直接查询文件系统，以获得自己想要知道的文件名。