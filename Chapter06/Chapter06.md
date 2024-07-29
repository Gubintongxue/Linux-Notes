# 第六章、Linux 文件与目录管理

最近更新日期：20//

在前一章我们认识了Linux系统下的文件权限概念以及目录的配置说明。 在这个章节当中，我们就直接来进一步的操作与管理文件及目录吧！包括在不同的目录间变换、 创建与删除目录、创建与删除文件，还有寻找文件、查阅文件内容等等，都会在这个章节作个简单的介绍啊！

### 6.1 目录与路径

在本章中，**主要讨论Linux系统中的路径问题，特别是绝对路径与相对路径的概念，以及如何操作目录**。

#### 6.1.1 相对路径与绝对路径

**绝对路径**：从根目录（/）开始的路径。例如：`/usr/share/doc`。 

**相对路径**：相对于当前工作目录的路径。例如：从`/usr/share/doc`到`/usr/share/man`可以写成`cd ../man`。

**用途**：

- **绝对路径**：在写程序（如shell脚本）时，推荐使用绝对路径，因为其写法虽然繁琐，但可以保证路径的正确性，不会因工作环境变化而出错。
- **相对路径**：在软件开发中，使用相对路径可以使软件更加灵活，不同用户可以将软件安装在不同的目录中而不影响使用。

因为绝对路径的写法虽然比较麻烦，但是可以肯定这个写法绝对不会有问题。 如果使用相对路径在程序当中，则可能由于你执行的工作环境不同，导致一些问题的发生。 这个问题在[工作调度（at, cron, 第十五章）](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)当中尤其重要！这个现象我们在[十二章、shell script](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)时，会再次的提醒你喔！ ^_^



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
     cd ~dmtsai  # 切换到dmtsai用户的主目录
     cd ~        # 切换到当前用户的主目录
     cd          # 切换到当前用户的主目录
     cd ..       # 切换到上一级目录
     cd -        # 切换到前一个工作目录
     cd /var/spool/mail  # 绝对路径
     cd ../postfix       # 相对路径
     ```
     
     **原文：**
     
     我们知道dmtsai这个使用者的主文件夹是/home/dmtsai/，而root主文件夹则是/root/，假设我以root身份在 Linux系统中，那么简单的说明一下这几个特殊的目录的意义是：
     
     ```
     [dmtsai@study ~]$ su -  # 先切换身份成为 root 看看！
     [root@study ~]# cd [相对路径或绝对路径]
     # 最重要的就是目录的绝对路径与相对路径，还有一些特殊目录的符号啰！
     [root@study ~]# cd ~dmtsai
     # 代表去到 dmtsai 这个使用者的主文件夹，亦即 /home/dmtsai
     [root@study dmtsai]# cd ~
     # 表示回到自己的主文件夹，亦即是 /root 这个目录
     [root@study ~]# cd
     # 没有加上任何路径，也还是代表回到自己主文件夹的意思喔！
     [root@study ~]# cd ..
     # 表示去到目前的上层目录，亦即是 /root 的上层目录的意思；
     [root@study /]# cd -
     # 表示回到刚刚的那个目录，也就是 /root 啰～
     [root@study ~]# cd /var/spool/mail
     # 这个就是绝对路径的写法！直接指定要去的完整路径名称！
     [root@study mail]# cd ../postfix
     # 这个是相对路径的写法，我们由/var/spool/mail 去到/var/spool/postfix 就这样写！
     ```
     
     cd是Change Directory的缩写，这是用来变换工作目录的指令。注意，目录名称与cd指令之间存在一个空格。 一登陆Linux系统后，每个帐号都会在自己帐号的主文件夹中。那回到上一层目录可以用“ cd .. ”。 利用相对路径的写法必须要确认你目前的路径才能正确的去到想要去的目录。例如上表当中最后一个例子， 你必须要确认你是在/var/spool/mail当中，并且知道在/var/spool当中有个mqueue的目录才行啊～ 这样才能使用cd ../postfix 去到正确的目录说，否则就要直接输入cd /var/spool/postfix 啰～
     
     其实，我们的提示字符，亦即那个 [root@study ~]# 当中，就已经有指出目前的目录了， 刚登陆时会到自己的主文件夹，而主文件夹还有一个代码，那就是“ ~ ”符号！ 例如上面的例子可以发现，使用“ cd ~ ”可以回到个人的主文件夹里头去呢！ 另外，针对 cd 的使用方法，如果仅输入 cd 时，代表的就是“ cd ~ ”的意思喔～ 亦即是会回到自己的主文件夹啦！而那个“ cd - ”比较难以理解，请自行多做几次练习，就会比较明白了。
     
     

   **Tips** 还是要一再地提醒，**我们的 Linux 的默认命令行界面 （bash shell） 具有文件补齐功能， 你要常常利用 [tab] 按键来达成你的目录完整性啊**！这可是个好习惯啊～可以避免你按错键盘输入错字说～ ^_^

   

2. **pwd（显示当前目录）**：

   - 选项与参数：

     - `-P`：显示真实路径，而非链接路径。

   - 例子：

     ```
     pwd        # 显示当前目录
     pwd -P     # 显示真实路径
     ```

   **原文：**

   ```
   [root@study ~]# pwd [-P]
   选项与参数：
   -P  ：显示出确实的路径，而非使用链接 （link） 路径。
   
   范例：单纯显示出目前的工作目录：
   [root@study ~]# pwd
   /root   &lt;== 显示出目录啦～
   
   范例：显示出实际的工作目录，而非链接文件本身的目录名而已
   [root@study ~]# cd /var/mail   &lt;==注意，/var/mail是一个链接文件
   [root@study mail]# pwd
   /var/mail         &lt;==列出目前的工作目录
   [root@study mail]# pwd -P
   /var/spool/mail   &lt;==怎么回事？有没有加 -P 差很多～
   [root@study mail]# ls -ld /var/mail
   lrwxrwxrwx. 1 root root 10 May  4 17:51 /var/mail -&gt; spool/mail
   # 看到这里应该知道为啥了吧？因为 /var/mail 是链接文件，链接到 /var/spool/mail
   # 所以，加上 pwd -P 的选项后，会不以链接文件的数据显示，而是显示正确的完整路径啊！
   ```

   pwd是Print Working Directory的缩写，也就是显示目前所在目录的指令， 例如在上个表格最后的目录是/var/mail这个目录，但是提示字符仅显示mail， 如果你想要知道目前所在的目录，可以输入pwd即可。此外，由于很多的套件所使用的目录名称都相同，例如 /usr/local/etc还有/etc，但是通常Linux仅列出最后面那一个目录而已，这个时候你就可以使用pwd 来知道你的所在目录啰！免得搞错目录，结果...

   其实有趣的是那个 -P 的选项啦！他可以让我们取得正确的目录名称，而不是以链接文件的路径来显示的。 如果你使用的是CentOS 7.x的话，刚刚好/var/mail是/var/spool/mail的链接文件， 所以，通过到/var/mail下达pwd -P就能够知道这个选项的意义啰～ ^_^

3. **mkdir（创建新目录）**：

   - 选项与参数：

     - `-m`：设置目录权限。
     - `-p`：递归创建所需目录。

   - 例子：

     ```
     mkdir test                       # 创建名为test的目录
     mkdir -p test1/test2/test3/test4 # 递归创建多层目录
     mkdir -m 711 test2               # 创建权限为rwx--x--x的目录
     ```

   **原文：**

   ```
   [root@study ~]# mkdir [-mp] 目录名称
   选项与参数：
   -m ：设置文件的权限喔！直接设置，不需要看默认权限 （umask） 的脸色～
   -p ：帮助你直接将所需要的目录（包含上层目录）递回创建起来！
   
   范例：请到/tmp下面尝试创建数个新目录看看：
   [root@study ~]# cd /tmp
   [root@study tmp]# mkdir test    &lt;==创建一名为 test 的新目录
   [root@study tmp]# mkdir test1/test2/test3/test4
   mkdir: cannot create directory ‘test1/test2/test3/test4’: No such file or directory
   # 话说，系统告诉我们，没可能创建这个目录啊！就是没有目录才要创建的！见鬼嘛？
   [root@study tmp]# mkdir -p test1/test2/test3/test4
   # 原来是要建 test4 上层没先建 test3 之故！加了这个 -p 的选项，可以自行帮你创建多层目录！
   
   范例：创建权限为rwx--x--x的目录
   [root@study tmp]# mkdir -m 711 test2
   [root@study tmp]# ls -ld test*
   drwxr-xr-x. 2 root   root  6 Jun  4 19:03 test
   drwxr-xr-x. 3 root   root 18 Jun  4 19:04 test1
   drwx--x--x. 2 root   root  6 Jun  4 19:05 test2
   # 仔细看上面的权限部分，如果没有加上 -m 来强制设置属性，系统会使用默认属性。
   # 那么你的默认属性为何？这要通过下面介绍的 [umask](../Text/index.html#umask) 才能了解喔！ ^_^
   ```

   如果想要创建新的目录的话，那么就使用mkdir （make directory）吧！ 不过，在默认的情况下， 你所需要的目录得一层一层的创建才行！例如：假如你要创建一个目录为 /home/bird/testing/test1，那么首先必须要有 /home 然后 /home/bird ，再来 /home/bird/testing 都必须要存在，才可以创建 /home/bird/testing/test1 这个目录！假如没有 /home/bird/testing 时，就没有办法创建 test1 的目录啰！

   不过，现在有个更简单有效的方法啦！那就是加上 -p 这个选项喔！你可以直接下达：“ mkdir -p /home/bird/testing/test1 ” 则系统会自动的帮你将 /home, /home/bird, /home/bird/testing 依序的创建起目录！并且， 如果该目录本来就已经存在时，系统也不会显示错误讯息喔！挺快乐的吧！ ^_^。 不过鸟哥不建议常用-p这个选项，因为担心如果你打错字，那么目录名称就会变的乱七八糟的！

   另外，有个地方你必须要先有概念，那就是“默认权限”的地方。我们可以利用 -m 来强制给予一个新的目录相关的权限， 例如上表当中，我们给予 -m 711 来给予新的目录 drwx--x--x 的权限。不过，如果没有给予 -m 选项时， **那么默认的新建目录权限又是什么呢？这个跟 [umask](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#umask) 有关，我们在本章后头会加以介绍的。**

4. **rmdir（删除空目录）**：

   - 选项与参数：

     - `-p`：递归删除空目录。

   - 例子：

     ```
     rmdir test                       # 删除名为test的空目录
     rmdir -p test1/test2/test3/test4 # 递归删除多层空目录
     ```
   
   **原文：**
   
   ```
   [root@study ~]# rmdir [-p] 目录名称
   选项与参数：
   -p ：连同“上层”“空的”目录也一起删除
   
   范例：将于mkdir范例中创建的目录（/tmp下面）删除掉！
   [root@study tmp]# ls -ld test*   &lt;==看看有多少目录存在？
   drwxr-xr-x. 2 root   root  6 Jun  4 19:03 test
   drwxr-xr-x. 3 root   root 18 Jun  4 19:04 test1
   drwx--x--x. 2 root   root  6 Jun  4 19:05 test2
   [root@study tmp]# rmdir test   &lt;==可直接删除掉，没问题
   [root@study tmp]# rmdir test1  &lt;==因为尚有内容，所以无法删除！
   rmdir: failed to remove ‘test1’: Directory not empty
   [root@study tmp]# rmdir -p test1/test2/test3/test4
   [root@study tmp]# ls -ld test*    &lt;==您看看，下面的输出中test与test1不见了！
   drwx--x--x. 2 root   root  6 Jun  4 19:05 test2
   # 瞧！利用 -p 这个选项，立刻就可以将 test1/test2/test3/test4 一次删除～
   # 不过要注意的是，这个 rmdir 仅能“删除空的目录”喔！
   ```
   
   **如果想要删除旧有的目录时，就使用rmdir吧！**例如将刚刚创建的test杀掉，使用“ rmdir test ”即可！请注意呦！目录需要一层一层的删除才行！而且被删除的目录里面必定不能存在其他的目录或文件！ 这也是所谓的空的目录（empty directory）的意思啊！那如果要将所有目录下的东西都杀掉呢？！ 这个时候就必须使用“ rm -r test ”啰！不过，还是使用 rmdir 比较不危险！你也可以尝试以 -p 的选项加入，来删除上层的目录喔！

#### 6.1.3 关于可执行文件路径的变量：$PATH

**$PATH**：环境变量PATH定义了系统查找可执行文件的目录。执行命令时，系统会依照PATH的设置依次查找目录中的可执行文件。

**查看PATH**：

```
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

     

     ```
     /root/ls
     ./ls
     ```

  3. 将

     ```
     /root
     ```

     加入到PATH中：

     ```
     PATH="${PATH}:/root"
     ```
  
- 例题：在PATH中包含多个同名文件时，优先执行先找到的文件。

**注意**：

- 不建议将“.”加入PATH中，因为这会使得当前目录中的文件容易被误执行，尤其是在公共目录（如/tmp）中。

总结：

- 绝对路径和相对路径各有用途，需灵活使用。
- 熟练掌握常见目录操作命令（cd、pwd、mkdir、rmdir）。
- 理解并合理设置$PATH，以确保系统能正确查找并执行命令。



**原文：**

经过前一章FHS的说明后，我们知道查阅文件属性的指令ls完整文件名为：/bin/ls（这是绝对路径）， 那你会不会觉得很奇怪：“为什么我可以在任何地方执行/bin/ls这个指令呢？ ” 为什么我在任何目录下输入 ls 就一定可以显示出一些讯息而不会说找不到该 /bin/ls 指令呢？ 这是因为环境变量 PATH 的帮助所致呀！

当我们在执行一个指令的时候，举例来说“ls”好了，系统会依照PATH的设置去每个PATH定义的目录下搜寻文件名为ls的可可执行文件， 如果在PATH定义的目录中含有多个文件名为ls的可可执行文件，那么先搜寻到的同名指令先被执行！

现在，请下达“echo $PATH”来看看到底有哪些目录被定义出来了？ echo有“显示、印出”的意思，而 PATH 前面加的 $ 表示后面接的是变量，所以会显示出目前的 PATH ！

```
范例：先用root的身份列出搜寻的路径为何？
[root@study ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin

范例：用dmtsai的身份列出搜寻的路径为何？
[root@study ~]# exit    # 由之前的 su - 离开，变回原本的帐号！或再取得一个终端机皆可！
[dmtsai@study ~]$ echo $PATH
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/dmtsai/.local/bin:/home/dmtsai/bin
# 记不记得我们前一章说过，目前 /bin 是链接到 /usr/bin 当中的喔！
```

PATH（一定是大写）这个变量的内容是由一堆目录所组成的，每个目录中间用冒号（:）来隔开， 每个目录是有“顺序”之分的。仔细看一下上面的输出，你可以发现到无论是root还是dmtsai都有 /bin 或 /usr/bin 这个目录在PATH变量内，所以当然就能够在任何地方执行ls来找到/bin/ls可执行文件啰！因为 /bin 在 CentOS 7 当中，就是链接到 /usr/bin 去的！ 所以这两个目录内容会一模一样！

我们用几个范例来让你了解一下，为什么PATH是那么重要的项目！

例题：假设你是root，如果你将ls由/bin/ls移动成为/root/ls（可用“mv /bin/ls /root”指令达成），然后你自己本身也在/root目录下， 请问（1）你能不能直接输入ls来执行？（2）若不能，你该如何执行ls这个指令？（3）若要直接输入ls即可执行，又该如何进行？答：由于这个例题的重点是将某个可执行文件移动到非正规目录去，所以我们先要进行下面的动作才行：（务必先使用 su - 切换成为root的身份）

```
[root@study ~]# mv /bin/ls /root
# mv 为移动，可将文件在不同的目录间进行移动作业
```

（1）接下来不论你在那个目录下面输入任何与ls相关的指令，都没有办法顺利的执行ls了！ 也就是说，你不能直接输入ls来执行，因为/root这个目录并不在PATH指定的目录中， 所以，即使你在/root目录下，也不能够搜寻到ls这个指令！

（2）因为这个ls确实存在于/root下面，并不是被删除了！所以我们可以通过使用绝对路径或者是相对路径直接指定这个可执行文件文件名， 下面的两个方法都能够执行ls这个指令：

```
[root@study ~]# /root/ls  &lt;==直接用绝对路径指定该文件名
[root@study ~]# ./ls      &lt;==因为在 /root 目录下，就用./ls来指定
```

（3）**如果想要让root在任何目录均可执行/root下面的ls，那么就将/root加入PATH当中即可。 加入的方法很简单，就像下面这样：**

```
[root@study ~]# PATH="${PATH}:/root"
```

上面这个作法就能够将/root加入到可执行文件搜寻路径PATH中了！不相信的话请您自行使用“echo $PATH”去查看吧！ 另外，除了 $PATH 之外，如果想要更明确的定义出变量的名称，可以使用大括号 ${PATH} 来处理变量的调用喔！ 如果确定这个例题进行没有问题了，请将ls搬回/bin下面，不然系统会挂点的！

```
[root@study ~]# mv /root/ls /bin
```

某些情况下，即使你已经将 ls 搬回 /bin 了，不过系统还是会告知你无法处理 /root/ls 喔！很可能是因为指令参数被高速缓存的关系。 不要紧张，只要登出 （exit） 再登陆 （su -） 就可以继续快乐的使用 ls 了！

例题：如果我有两个ls指令在不同的目录中，例如/usr/local/bin/ls与/bin/ls那么当我下达 ls 的时候，哪个ls会被执行？答：那还用说，就找出 ${PATH} 里面哪个目录先被查询，则那个目录下的指令就会被先执行了！所以用 dmtsai 帐号为例，他最先搜寻的是 /usr/local/bin， 所以 /usr/local/bin/ls 会先被执行喔！

例题：为什么 ${PATH} 搜寻的目录不加入本目录（.）？加入本目录的搜寻不是也不错？答：如果在PATH中加入本目录（.）后，确实我们就能够在指令所在目录进行指令的执行了。 **但是由于你的工作目录并非固定（常常会使用cd来切换到不同的目录）， 因此能够执行的指令会有变动（因为每个目录下面的可可执行文件都不相同嘛！），这对使用者来说并非好事。**

另外，如果有个坏心使用者在/tmp下面做了一个指令，因为/tmp是大家都能够写入的环境，所以他当然可以这样做。 假设该指令可能会窃取使用者的一些数据，如果你使用root的身份来执行这个指令，那不是很糟糕？ 如果这个指令的名称又是经常会被用到的ls时，那“中标”的概率就更高了！

**所以，为了安全起见，不建议将“.”加入PATH的搜寻目录中。**

而由上面的几个例题我们也可以知道几件事情：

- 不同身份使用者默认的PATH不同，默认能够随意执行的指令也不同（如root与dmtsai）；
- PATH是可以修改的；
- 使用绝对路径或相对路径直接指定某个指令的文件名来执行，会比搜寻PATH来的正确；
- 指令应该要放置到正确的目录下，执行才会比较方便；
- **本目录（.）最好不要放到PATH当中。**

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
   ls -al ~
   ```
   
2. 显示文件类型而不显示颜色：

   ```
   ls -alF --color=never ~
   ```
   
3. 显示文件的完整修改时间：

   ```
   ls -al --full-time ~
   ```

**原文：**

```
[root@study ~]# ls [-aAdfFhilnrRSt] 文件名或目录名称..
[root@study ~]# ls [--color={never,auto,always}] 文件名或目录名称..
[root@study ~]# ls [--full-time] 文件名或目录名称..
选项与参数：
-a  ：全部的文件，连同隐藏文件（ 开头为 . 的文件） 一起列出来（常用）
-A  ：全部的文件，连同隐藏文件，但不包括 . 与 .. 这两个目录
-d  ：仅列出目录本身，而不是列出目录内的文件数据（常用）
-f  ：直接列出结果，而不进行排序 （ls 默认会以文件名排序！）
-F  ：根据文件、目录等信息，给予附加数据结构，例如：
      *:代表可可执行文件； /:代表目录； =:代表 socket 文件； &#124;:代表 FIFO 文件；
-h  ：将文件大小以人类较易读的方式（例如 GB, KB 等等）列出来；
-i  ：列出 inode 号码，inode 的意义下一章将会介绍；
-l  ：长数据串行出，包含文件的属性与权限等等数据；（常用）
-n  ：列出 UID 与 GID 而非使用者与群组的名称 （UID与GID会在帐号管理提到！）
-r  ：将排序结果反向输出，例如：原本文件名由小到大，反向则为由大到小；
-R  ：连同子目录内容一起列出来，等于该目录下的所有文件都会显示出来；
-S  ：以文件大小大小排序，而不是用文件名排序；
-t  ：依时间排序，而不是用文件名。
--color=never  ：不要依据文件特性给予颜色显示；
--color=always ：显示颜色
--color=auto   ：让系统自行依据设置来判断是否给予颜色
--full-time    ：以完整时间模式 （包含年、月、日、时、分） 输出
--time={atime,ctime} ：输出 access 时间或改变权限属性时间 （ctime）
                       而非内容变更时间 （modification time）
```

在Linux系统当中，这个 ls 指令可能是最常被执行的吧！因为我们随时都要知道文件或者是目录的相关信息啊～ 不过，我们Linux的文件所记录的信息实在是太多了，ls 没有需要全部都列出来呢～ 所以，当你只有下达 ls 时，默认显示的只有：非隐藏文件的文件名、 以文件名进行排序及文件名代表的颜色显示如此而已。举例来说， 你下达“ ls /etc ”之后，只有经过排序的文件名以及以蓝色显示目录及白色显示一般文件，如此而已。

那如果我还想要加入其他的显示信息时，可以加入上头提到的那些有用的选项呢～ 举例来说，我们之前一直用到的 -l 这个长串显示数据内容，以及将隐藏文件也一起列示出来的 -a 选项等等。 下面则是一些常用的范例，实际试做看看：

```
范例一：将主文件夹下的所有文件列出来（含属性与隐藏文件）
[root@study ~]# ls -al ~
total 56
dr-xr-x---.  5 root root 4096 Jun  4 19:49 .
dr-xr-xr-x. 17 root root 4096 May  4 17:56 ..
-rw-------.  1 root root 1816 May  4 17:57 anaconda-ks.cfg
-rw-------.  1 root root 6798 Jun  4 19:53 .bash_history
-rw-r--r--.  1 root root   18 Dec 29  2013 .bash_logout
-rw-r--r--.  1 root root  176 Dec 29  2013 .bash_profile
-rw-rw-rw-.  1 root root  176 Dec 29  2013 .bashrc
-rw-r--r--.  1 root root  176 Jun  3 00:04 .bashrc_test
drwx------.  4 root root   29 May  6 00:14 .cache
drwxr-xr-x.  3 root root   17 May  6 00:14 .config
# 这个时候你会看到以 . 为开头的几个文件，以及目录档 （.） （..） .config 等等，
# 不过，目录档文件名都是以深蓝色显示，有点不容易看清楚就是了。

范例二：承上题，不显示颜色，但在文件名末显示出该文件名代表的类型（type）
[root@study ~]# ls -alF --color=never  ~
total 56
dr-xr-x---.  5 root root 4096 Jun  4 19:49 ./
dr-xr-xr-x. 17 root root 4096 May  4 17:56 ../
-rw-------.  1 root root 1816 May  4 17:57 anaconda-ks.cfg
-rw-------.  1 root root 6798 Jun  4 19:53 .bash_history
-rw-r--r--.  1 root root   18 Dec 29  2013 .bash_logout
-rw-r--r--.  1 root root  176 Dec 29  2013 .bash_profile
-rw-rw-rw-.  1 root root  176 Dec 29  2013 .bashrc
-rw-r--r--.  1 root root  176 Jun  3 00:04 .bashrc_test
drwx------.  4 root root   29 May  6 00:14 .cache/
drwxr-xr-x.  3 root root   17 May  6 00:14 .config/
# 注意看到显示结果的第一行，嘿嘿～知道为何我们会下达类似 ./command
# 之类的指令了吧？因为 ./ 代表的是“目前目录下”的意思啊！至于什么是 FIFO/Socket ？
# 请参考前一章节的介绍啊！另外，那个.bashrc 时间仅写2013，能否知道详细时间？

范例三：完整的呈现文件的修改时间 （modification time）
[root@study ~]# ls -al --full-time  ~
total 56
dr-xr-x---.  5 root root 4096 2015-06-04 19:49:54.520684829 +0800 .
dr-xr-xr-x. 17 root root 4096 2015-05-04 17:56:38.888000000 +0800 ..
-rw-------.  1 root root 1816 2015-05-04 17:57:02.326000000 +0800 anaconda-ks.cfg
-rw-------.  1 root root 6798 2015-06-04 19:53:41.451684829 +0800 .bash_history
-rw-r--r--.  1 root root   18 2013-12-29 10:26:31.000000000 +0800 .bash_logout
-rw-r--r--.  1 root root  176 2013-12-29 10:26:31.000000000 +0800 .bash_profile
-rw-rw-rw-.  1 root root  176 2013-12-29 10:26:31.000000000 +0800 .bashrc
-rw-r--r--.  1 root root  176 2015-06-03 00:04:16.916684829 +0800 .bashrc_test
drwx------.  4 root root   29 2015-05-06 00:14:56.960764950 +0800 .cache
drwxr-xr-x.  3 root root   17 2015-05-06 00:14:56.975764950 +0800 .config
# 请仔细看，上面的“时间”字段变了喔！变成较为完整的格式。
# 一般来说， ls -al 仅列出目前短格式的时间，有时不会列出年份，
# 借由 --full-time 可以查阅到比较正确的完整时间格式啊！
```

**其实 ls 的用法还有很多，包括查阅文件所在 i-node 号码的 ls -i 选项，以及用来进行文件排序的 -S 选项，还有用来查阅不同时间的动作的 --time=atime 等选项（更多时间说明请参考本章后面[touch](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#touch)的说明）**。而这些选项的存在都是因为 Linux 文件系统记录了很多有用的信息的缘故。那么 Linux 的文件系统中，这些与权限、属性有关的数据放在哪里呢？ 放在 i-node 里面。关于这部分，我们会在下一章继续为你作比较深入的介绍啊！

无论如何， **ls 最常被使用到的功能还是那个 -l 的选项，为此，很多 distribution 在默认的情况中， 已经将 ll （L 的小写） 设置成为 ls -l 的意思了！其实，那个功能是 [Bash shell](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 的 [alias](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#alias) 功能呢**～也就是说，我们直接输入 ll 就等于是输入 ls -l 是一样的～关于这部分，我们会在后续 bash shell 时再次的强调滴～





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
   cp ~/.bashrc /tmp/bashrc
   ```
   
2. 复制 `/var/log/wtmp` 到当前目录，并保留所有属性：

   ```
   cp -a /var/log/wtmp .
   ```
   
3. 创建符号链接和硬链接：

   ```
   cp -s bashrc bashrc_slink
   cp -l bashrc bashrc_hlink
   ```

**原文：**

要复制文件，请使用 cp （copy） 这个指令即可～不过， cp 这个指令的用途可多了～ 除了单纯的复制之外，还可以创建链接文件 （就是捷径啰），比对两文件的新旧而予以更新， 以及复制整个目录等等的功能呢！至于移动目录与文件，则使用 mv （move）， 这个指令也可以直接拿来作更名 （rename） 的动作喔！至于移除吗？那就是 rm （remove） 这个指令啰～下面我们就来瞧一瞧先～

- cp （复制文件或目录）

```
[root@study ~]# cp [-adfilprsu] 来源文件（source） 目标文件（destination）
[root@study ~]# cp [options] source1 source2 source3 .... directory
选项与参数：
-a  ：相当于 -dr --preserve=all 的意思，至于 dr 请参考下列说明；（常用）
-d  ：若来源文件为链接文件的属性（link file），则复制链接文件属性而非文件本身；
-f  ：为强制（force）的意思，若目标文件已经存在且无法打开，则移除后再尝试一次；
-i  ：若目标文件（destination）已经存在时，在覆盖时会先询问动作的进行（常用）
-l  ：进行硬式链接（hard link）的链接文件创建，而非复制文件本身；
-p  ：连同文件的属性（权限、用户、时间）一起复制过去，而非使用默认属性（备份常用）；
-r  ：递回持续复制，用于目录的复制行为；（常用）
-s  ：复制成为符号链接文件 （symbolic link），亦即“捷径”文件；
-u  ：destination 比 source 旧才更新 destination，或 destination 不存在的情况下才复制。
--preserve=all ：除了 -p 的权限相关参数外，还加入 SELinux 的属性, links, xattr 等也复制了。
最后需要注意的，如果来源文件有两个以上，则最后一个目的文件一定要是“目录”才行！
```

复制（cp）这个指令是非常重要的，不同身份者执行这个指令会有不同的结果产生，尤其是那个-a, -p的选项， 对于不同身份来说，差异则非常的大！下面的练习中，有的身份为root有的身份为一般帐号 （在我这里用 dmtsai 这个帐号）， 练习时请特别注意身份的差别喔！好！开始来做复制的练习与观察：

```
范例一：用root身份，将主文件夹下的 .bashrc 复制到 /tmp 下，并更名为 bashrc
[root@study ~]# cp ~/.bashrc /tmp/bashrc
[root@study ~]# cp -i ~/.bashrc /tmp/bashrc
cp: overwrite `/tmp/bashrc'? n  &lt;==n不覆盖，y为覆盖
# 重复作两次动作，由于 /tmp 下面已经存在 bashrc 了，加上 -i 选项后，
# 则在覆盖前会询问使用者是否确定！可以按下 n 或者 y 来二次确认呢！

范例二：变换目录到/tmp，并将/var/log/wtmp复制到/tmp且观察属性：
[root@study ~]# cd /tmp
[root@study tmp]# cp /var/log/wtmp . &lt;==想要复制到目前的目录，最后的 . 不要忘
[root@study tmp]# ls -l /var/log/wtmp wtmp
-rw-rw-r--. 1 root utmp 28416 Jun 11 18:56 /var/log/wtmp
-rw-r--r--. 1 root root 28416 Jun 11 19:01 wtmp
# 注意上面的特殊字体，在不加任何选项的情况下，文件的某些属性/权限会改变；
# 这是个很重要的特性！要注意喔！还有，连文件创建的时间也不一样了！
# 那如果你想要将文件的所有特性都一起复制过来该怎办？可以加上 -a 喔！如下所示：

[root@study tmp]# cp -a /var/log/wtmp wtmp_2
[root@study tmp]# ls -l /var/log/wtmp wtmp_2
-rw-rw-r--. 1 root utmp 28416 Jun 11 18:56 /var/log/wtmp
-rw-rw-r--. 1 root utmp 28416 Jun 11 18:56 wtmp_2
# 瞭了吧！整个数据特性完全一模一样ㄟ！真是不赖～这就是 -a 的特性！
```

这个 cp 的功能很多，由于我们常常会进行一些数据的复制，所以也会常常用到这个指令的。 一般来说，我们如果去复制别人的数据 （当然，该文件你必须要有 read 的权限才行啊！ ^*^） 时， 总是希望复制到的数据最后是我们自己的，所以，在默认的条件中， cp 的来源文件与目的文件的权限是不同的，目的文件的拥有者通常会是指令操作者本身。举例来说， 上面的范例二中，由于我是 root 的身份，因此复制过来的文件拥有者与群组就改变成为 root 所有了！ 这样说，可以明白吗？^*^

由于具有这个特性，因此当我们在进行备份的时候，某些需要特别注意的特殊权限文件， 例如密码档 （/etc/shadow） 以及一些配置文件，就不能直接以 cp 来复制，而必须要加上 -a 或者是 -p 等等可以完整复制文件权限的选项才行！另外，如果你想要复制文件给其他的使用者， 也必须要注意到文件的权限（包含读、写、执行以及文件拥有者等等）， 否则，其他人还是无法针对你给予的文件进行修订的动作喔！注意注意！

```
范例三：复制 /etc/ 这个目录下的所有内容到 /tmp 下面
[root@study tmp]# cp /etc/ /tmp
cp: omitting directory `/etc'   &lt;== 如果是目录则不能直接复制，要加上 -r 的选项
[root@study tmp]# cp -r /etc/ /tmp
# 还是要再次的强调喔！ -r 是可以复制目录，但是，文件与目录的权限可能会被改变
# 所以，也可以利用“ cp -a /etc /tmp ”来下达指令喔！尤其是在备份的情况下！

范例四：将范例一复制的 bashrc 创建一个链接文件 （symbolic link）
[root@study tmp]# ls -l bashrc
-rw-r--r--. 1 root root 176 Jun 11 19:01 bashrc  &lt;==先观察一下文件情况
[root@study tmp]# cp -s bashrc bashrc_slink
[root@study tmp]# cp -l bashrc bashrc_hlink
[root@study tmp]# ls -l bashrc*
-rw-r--r--. 2 root root 176 Jun 11 19:01 bashrc         &lt;==与原始文件不太一样了！
-rw-r--r--. 2 root root 176 Jun 11 19:01 bashrc_hlink
lrwxrwxrwx. 1 root root   6 Jun 11 19:06 bashrc_slink -&gt; bashrc
```

范例四可有趣了！使用 -l 及 -s 都会创建所谓的链接文件（link file），但是这两种链接文件却有不一样的情况。这是怎么一回事啊？ 那个 -l 就是所谓的实体链接（hard link），至于 -s 则是符号链接（symbolic link）， 简单来说，bashrc_slink 是一个“捷径”，这个捷径会链接到bashrc去！所以你会看到文件名右侧会有个指向（->）的符号！

至于bashrc_hlink文件与bashrc的属性与权限完全一模一样，与尚未进行链接前的差异则是第二栏的link数由1变成2了！ 鸟哥这里先不介绍实体链接，因为实体链接涉及 i-node 的相关知识，我们下一章谈到文件系统（filesystem）时再来讨论这个问题。

```
范例五：若 ~/.bashrc 比 /tmp/bashrc 新才复制过来
[root@study tmp]# cp -u ~/.bashrc /tmp/bashrc
# 这个 -u 的特性，是在目标文件与来源文件有差异时，才会复制的。
# 所以，比较常被用于“备份”的工作当中喔！ ^_^

范例六：将范例四造成的 bashrc_slink 复制成为 bashrc_slink_1 与bashrc_slink_2
[root@study tmp]# cp bashrc_slink bashrc_slink_1
[root@study tmp]# cp -d bashrc_slink bashrc_slink_2
[root@study tmp]# ls -l bashrc bashrc_slink*
-rw-r--r--. 2 root root 176 Jun 11 19:01 bashrc
lrwxrwxrwx. 1 root root   6 Jun 11 19:06 bashrc_slink -&gt; bashrc
-rw-r--r--. 1 root root 176 Jun 11 19:09 bashrc_slink_1            &lt;==与原始文件相同
lrwxrwxrwx. 1 root root   6 Jun 11 19:10 bashrc_slink_2 -&gt; bashrc  &lt;==是链接文件！
# 这个例子也是很有趣喔！原本复制的是链接文件，但是却将链接文件的实际文件复制过来了
# 也就是说，如果没有加上任何选项时，cp复制的是原始文件，而非链接文件的属性！
# 若要复制链接文件的属性，就得要使用 -d 的选项了！如 bashrc_slink_2 所示。

范例七：将主文件夹的 .bashrc 及 .bash_history 复制到 /tmp 下面
[root@study tmp]# cp ~/.bashrc ~/.bash_history /tmp
# 可以将多个数据一次复制到同一个目录去！最后面一定是目录！
```

例题：你能否使用 dmtsai 的身份，完整的复制/var/log/wtmp文件到/tmp下面，并更名为dmtsai_wtmp呢？答：实际做看看的结果如下：

```
[dmtsai@study ~]$ cp -a /var/log/wtmp /tmp/dmtsai_wtmp
[dmtsai@study ~]$ ls -l /var/log/wtmp /tmp/dmtsai_wtmp
-rw-rw-r--. 1 dmtsai dmtsai 28416  6月 11 18:56 /tmp/dmtsai_wtmp
-rw-rw-r--. 1 root   utmp   28416  6月 11 18:56 /var/log/wtmp
```

由于 dmtsai 的身份并不能随意修改文件的拥有者与群组，因此虽然能够复制wtmp的相关权限与时间等属性， 但是与拥有者、群组相关的，原本 dmtsai 身份无法进行的动作，即使加上 -a 选项，也是无法达成完整复制权限的！

总之，由于 cp 有种种的文件属性与权限的特性，所以，在复制时，你必须要清楚的了解到：

- 是否需要完整的保留来源文件的信息？
- 来源文件是否为链接文件 （symbolic link file）？
- 来源文件是否为特殊的文件，例如 FIFO, socket 等？
- 来源文件是否为目录？





**删除文件或目录：`rm`**

**常用选项**：

- `-f`：强制删除，忽略不存在的文件。
- `-i`：在删除前询问用户。
- `-r`：递归删除目录。

**示例**：

1. 删除 `/tmp` 下的 `bashrc` 文件：

   ```
   rm -i /tmp/bashrc
   ```
   
2. 递归删除 `/tmp/etc` 目录：

   ```
   rm -r /tmp/etc
   ```
   
3. 删除以 `-` 开头的文件：

   ```
   rm ./-aaa-
   ```

**原文：**

- rm （移除文件或目录）

```
[root@study ~]# rm [-fir] 文件或目录
选项与参数：
-f  ：就是 force 的意思，忽略不存在的文件，不会出现警告讯息；
-i  ：互动模式，在删除前会询问使用者是否动作
-r  ：递回删除啊！最常用在目录的删除了！这是非常危险的选项！！！

范例一：将刚刚在 cp 的范例中创建的 bashrc 删除掉！
[root@study ~]# cd /tmp
[root@study tmp]# rm -i bashrc
rm: remove regular file `bashrc'? y
# 如果加上 -i 的选项就会主动询问喔，避免你删除到错误的文件名！

范例二：通过万用字符*的帮忙，将/tmp下面开头为bashrc的文件名通通删除：
[root@study tmp]# rm -i bashrc*
# 注意那个星号，代表的是 0 到无穷多个任意字符喔！很好用的东西！

范例三：将 cp 范例中所创建的 /tmp/etc/ 这个目录删除掉！
[root@study tmp]# rmdir /tmp/etc
rmdir: failed to remove '/tmp/etc': Directory not empty   &lt;== 删不掉啊！因为这不是空的目录！
[root@study tmp]# rm -r /tmp/etc
rm: descend into directory `/tmp/etc'? y
rm: remove regular file `/tmp/etc/fstab'? y
rm: remove regular empty file `/tmp/etc/crypttab'? ^C  &lt;== 按下 [crtl]+c 中断
.....（中间省略）.....
# 因为身份是 root ，默认已经加入了 -i 的选项，所以你要一直按 y 才会删除！
# 如果不想要继续按 y ，可以按下“ [ctrl]-c ”来结束 rm 的工作。
# 这是一种保护的动作，如果确定要删除掉此目录而不要询问，可以这样做：
[root@study tmp]# \rm -r /tmp/etc
# 在指令前加上反斜线，可以忽略掉 alias 的指定选项喔！至于 alias 我们在bash再谈！
# 拜托！这个范例很可怕！你不要删错了！删除 /etc 系统是会挂掉的！

范例四：删除一个带有 - 开头的文件
[root@study tmp]# touch ./-aaa-  &lt;==[touch](../Text/index.html#touch)这个指令可以创建空文件！
[root@study tmp]# ls -l
-rw-r--r--. 1 root   root       0 Jun 11 19:22 -aaa-  &lt;==文件大小为0，所以是空文件
[root@study tmp]# rm -aaa-
rm: invalid option -- 'a'                    &lt;== 因为 "-" 是选项嘛！所以系统误判了！
Try 'rm ./-aaa-' to remove the file `-aaa-'. &lt;== 新的 bash 有给建议的
Try 'rm --help' for more information.
[root@study tmp]# rm ./-aaa-
```

这是移除的指令（remove），要注意的是，通常在Linux系统下，为了怕文件被 root 误杀，所以很多 distributions 都已经默认加入 -i 这个选项了！而如果要连目录下的东西都一起杀掉的话， 例如子目录里面还有子目录时，那就要使用 -r 这个选项了！不过，使用“ rm -r ”这个指令之前，请千万注意了，因为该目录或文件“肯定”会被 root 杀掉！因为系统不会再次询问你是否要砍掉呦！所以那是个超级严重的指令下达呦！ 得特别注意！不过，如果你确定该目录不要了，那么使用 rm -r 来循环杀掉是不错的方式！

另外，范例四也是很有趣的例子，我们在之前就谈过，文件名最好不要使用 "-" 号开头， 因为 "-" 后面接的是选项，因此，单纯的使用“ rm -aaa- ”系统的指令就会误判啦！ 那如果使用后面会谈到的正则表达式时，还是会出问题的！所以，只能用避过首位字符是 "-" 的方法啦！ 就是加上本目录“ ./ ”即可！如果 man rm 的话，其实还有一种方法，那就是“ rm -- -aaa- ”也可以啊！



**移动文件与目录，或更名：`mv`**

**常用选项**：

- `-f`：强制覆盖目标文件。
- `-i`：在覆盖目标文件前询问用户。
- `-u`：仅在源文件比目标文件新或目标文件不存在时才移动。

**示例**：

1. 将 `bashrc` 文件移动到 `mvtest` 目录：

   ```
   mv bashrc mvtest
   ```
   
2. 将 `mvtest` 目录更名为 `mvtest2`：

   ```
   mv mvtest mvtest2
   ```
   
3. 将多个文件移动到 `mvtest2` 目录：

   ```
   mv bashrc1 bashrc2 mvtest2
   ```

**原文：**

```
[root@study ~]# mv [-fiu] source destination
[root@study ~]# mv [options] source1 source2 source3 .... directory
选项与参数：
-f  ：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；
-i  ：若目标文件 （destination） 已经存在时，就会询问是否覆盖！
-u  ：若目标文件已经存在，且 source 比较新，才会更新 （update）

范例一：复制一文件，创建一目录，将文件移动到目录中
[root@study ~]# cd /tmp
[root@study tmp]# cp ~/.bashrc bashrc
[root@study tmp]# mkdir mvtest
[root@study tmp]# mv bashrc mvtest
# 将某个文件移动到某个目录去，就是这样做！

范例二：将刚刚的目录名称更名为 mvtest2
[root@study tmp]# mv mvtest mvtest2 &lt;== 这样就更名了！简单～
# 其实在 Linux 下面还有个有趣的指令，名称为 rename ，
# 该指令专职进行多个文件名的同时更名，并非针对单一文件名变更，与mv不同。请man rename。

范例三：再创建两个文件，再全部移动到 /tmp/mvtest2 当中
[root@study tmp]# cp ~/.bashrc bashrc1
[root@study tmp]# cp ~/.bashrc bashrc2
[root@study tmp]# mv bashrc1 bashrc2 mvtest2
# 注意到这边，如果有多个来源文件或目录，则最后一个目标文件一定是“目录！”
# 意思是说，将所有的数据移动到该目录的意思！
```

这是搬移 （move） 的意思！当你要移动文件或目录的时后，呵呵！这个指令就很重要啦！ 同样的，你也可以使用 -u （ update ）来测试新旧文件，看看是否需要搬移啰！ 另外一个用途就是“变更文件名！”，我们可以很轻易的使用 mv 来变更一个文件的文件名呢！不过，在 Linux 才有的指令当中，有个 rename ， 可以用来更改大量文件的文件名，你可以利用 man rename 来查阅一下，也是挺有趣的指令喔！





#### 6.2.3 取得路径的文件名称与目录名称

`basename` 和 `dirname` 用于分别获取路径中的文件名和目录名。

**示例**：

1. 获取文件名：

   ```
   basename /etc/sysconfig/network
   # 输出：network
   ```

2. 获取目录名：

   ```
   dirname /etc/sysconfig/network
   # 输出：/etc/sysconfig
   ```

**原文：**

每个文件的完整文件名包含了前面的目录与最终的文件名，而每个文件名的长度都可以到达 255 个字符耶！ 那么你怎么知道那个是文件名？那个是目录名？嘿嘿！就是利用斜线 （/） 来分辨啊！ 其实，取得文件名或者是目录名称，一般的用途应该是在写程序的时候用来判断之用的啦～ 所以，这部分的指令可以用在第三篇内的 shell scripts 里头喔！ 下面我们简单的以几个范例来谈一谈 basename 与 dirname 的用途！

```
[root@study ~]# basename /etc/sysconfig/network
network         &lt;== 很简单！就取得最后的文件名～
[root@study ~]# dirname /etc/sysconfig/network
/etc/sysconfig  &lt;== 取得的变成目录名了！
```



### 6.3 文件内容查阅

在 Linux 系统中，有多种命令可以用来查阅文件内容，包括 `cat`、`more`、`less`、`head`、`tail` 等。根据需求不同，这些命令可以显示文件的全部内容或部分内容，适合处理小型和大型文件。



- cat 由第一行开始显示文件内容
- tac 从最后一行开始显示，可以看出 tac 是 cat 的倒着写！
- nl 显示的时候，顺道输出行号！
- more 一页一页的显示文件内容
- less 与 more 类似，但是比 more 更好的是，他可以往前翻页！
- head 只看头几行
- tail 只看尾巴几行
- od 以二进制的方式读取文件内容！



#### 6.3.1 直接检视文件内容

**常用命令**：

- **cat**（由第一行开始显示文件内容）

  ```
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
  cat -n /etc/issue
  ```
  
- **tac**（从最后一行开始显示）

  ```
  tac /etc/issue
  ```
  
- **nl**（显示时添加行号）

  ```
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
  nl -b a -n rz -w 3 /etc/issue
  ```

**原文：**

- cat （concatenate）

```
[root@study ~]# cat [-AbEnTv]
选项与参数：
-A  ：相当于 -vET 的整合选项，可列出一些特殊字符而不是空白而已；
-b  ：列出行号，仅针对非空白行做行号显示，空白行不标行号！
-E  ：将结尾的断行字符 $ 显示出来；
-n  ：打印出行号，连同空白行也会有行号，与 -b 的选项不同；
-T  ：将 [tab] 按键以 ^I 显示出来；
-v  ：列出一些看不出来的特殊字符

范例一：检阅 /etc/issue 这个文件的内容
[root@study ~]# cat /etc/issue
\S
Kernel \r on an \m

范例二：承上题，如果还要加印行号呢？
[root@study ~]# cat -n /etc/issue
     1  \S
     2  Kernel \r on an \m
     3
# 所以这个文件有三行！看到了吧！可以印出行号呢！这对于大文件要找某个特定的行时，有点用处！
# 如果不想要编排空白行的行号，可以使用“cat -b /etc/issue”，自己测试看看：

范例三：将 /etc/man_db.conf 的内容完整的显示出来（包含特殊字符）
[root@study ~]# cat -A /etc/man_db.conf
# $
....（中间省略）....
MANPATH_MAP^I/bin^I^I^I/usr/share/man$
MANPATH_MAP^I/usr/bin^I^I/usr/share/man$
MANPATH_MAP^I/sbin^I^I^I/usr/share/man$
MANPATH_MAP^I/usr/sbin^I^I/usr/share/man$
.....（下面省略）.....
# 上面的结果限于篇幅，鸟哥删除掉很多数据了。另外，输出的结果并不会有特殊字体，
# 鸟哥上面的特殊字体是要让您发现差异点在哪里就是了。基本上，在一般的环境中，
# 使用 [tab] 与空白键的效果差不多，都是一堆空白啊！我们无法知道两者的差别。
# 此时使用 cat -A 就能够发现那些空白的地方是啥鬼东西了！[tab]会以 ^I 表示，
# 断行字符则是以 $ 表示，所以你可以发现每一行后面都是 $ 啊！不过断行字符
# 在Windows/Linux则不太相同，Windows的断行字符是 ^M$ 啰。
# 这部分我们会在[第九章 vim 软件](../Text/index.html#tips_dos)的介绍时，再次的说明到喔！
```

嘿嘿！Linux 里面有“猫”指令？喔！不是的， cat 是 Concatenate （连续） 的简写， 主要的功能是将一个文件的内容连续的印出在屏幕上面！例如上面的例子中，我们将 /etc/issue 印出来！如果加上 -n 或 -b 的话，则每一行前面还会加上行号呦！

鸟哥个人是比较少用 cat 啦！毕竟当你的文件内容的行数超过 40 行以上，嘿嘿！根本来不及在屏幕上看到结果！ 所以，配合等一下要介绍的 more 或者是 less 来执行比较好！此外，如果是一般的 DOS 文件时，就需要特别留意一些奇奇怪怪的符号了， 例如断行与 [tab] 等，要显示出来，就得加入 -A 之类的选项了！

- tac （反向列示）

```
[root@study ~]# tac /etc/issue

Kernel \r on an \m
\S
# 嘿嘿！与刚刚上面的范例一比较，是由最后一行先显示喔！
```

tac 这个好玩了！怎么说呢？详细的看一下， cat 与 tac ，有没有发现呀！对啦！ tac 刚好是将 cat 反写过来，所以他的功能就跟 cat 相反啦， cat 是由“第一行到最后一行连续显示在屏幕上”，而 tac 则是“ 由最后一行到第一行反向在屏幕上显示出来 ”，很好玩吧！

- nl （添加行号打印）

```
[root@study ~]# nl [-bnw] 文件
选项与参数：
-b  ：指定行号指定的方式，主要有两种：
      -b a ：表示不论是否为空行，也同样列出行号（类似 cat -n）；
      -b t ：如果有空行，空的那一行不要列出行号（默认值）；
-n  ：列出行号表示的方法，主要有三种：
      -n ln ：行号在屏幕的最左方显示；
      -n rn ：行号在自己字段的最右方显示，且不加 0 ；
      -n rz ：行号在自己字段的最右方显示，且加 0 ；
-w  ：行号字段的占用的字符数。

范例一：用 nl 列出 /etc/issue 的内容
[root@study ~]# nl /etc/issue
     1  \S
     2  Kernel \r on an \m

# 注意看，这个文件其实有三行，第三行为空白（没有任何字符），
# 因为他是空白行，所以 nl 不会加上行号喔！如果确定要加上行号，可以这样做：

[root@study ~]# nl -b a /etc/issue
     1  \S
     2  Kernel \r on an \m
     3
# 呵呵！行号加上来啰～那么如果要让行号前面自动补上 0 呢？可这样

[root@study ~]# nl -b a -n rz /etc/issue
000001  \S
000002  Kernel \r on an \m
000003
# 嘿嘿！自动在自己字段的地方补上 0 了～默认字段是六位数，如果想要改成 3 位数？

[root@study ~]# nl -b a -n rz -w 3 /etc/issue
001     \S
002     Kernel \r on an \m
003
# 变成仅有 3 位数啰～
```

nl 可以将输出的文件内容自动的加上行号！其默认的结果与 cat -n 有点不太一样， nl 可以将行号做比较多的显示设计，包括位数与是否自动补齐 0 等等的功能呢。





#### 6.3.2 可翻页检视

**常用命令**：

- **more**（一页一页显示）

  ```
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

**原文：**

前面提到的 nl 与 cat, tac 等等，都是一次性的将数据一口气显示到屏幕上面，那有没有可以进行一页一页翻动的指令啊？ 让我们可以一页一页的观察，才不会前面的数据看不到啊～呵呵！有的！那就是 more 与 less 啰～

- more （一页一页翻动）

```
[root@study ~]# more /etc/man_db.conf
#
#
# This file is used by the man-db package to configure the man and cat paths.
# It is also used to provide a manpath for those without one by examining
# their PATH environment variable. For details see the manpath（5） man page.
#
.....（中间省略）.....
--More--（28%）  &lt;== 重点在这一行喔！你的光标也会在这里等待你的指令
```

仔细的给他看到上面的范例，如果 more 后面接的文件内容行数大于屏幕输出的行数时， 就会出现类似上面的图示。重点在最后一行，最后一行会显示出目前显示的百分比， 而且还可以在最后一行输入一些有用的指令喔！在 more 这个程序的运行过程中，你有几个按键可以按的：

- 空白键 （space）：代表向下翻一页；
- Enter ：代表向下翻“一行”；
- /字串 ：代表在这个显示的内容当中，向下搜寻“字串”这个关键字；
- :f ：立刻显示出文件名以及目前显示的行数；
- q ：代表立刻离开 more ，不再显示该文件内容。
- b 或 [ctrl]-b ：代表往回翻页，不过这动作只对文件有用，对管线无用。

要离开 more 这个指令的显示工作，可以按下 q 就能够离开了。而要向下翻页，就使用空白键即可。 比较有用的是搜寻字串的功能，举例来说，我们使用“ more /etc/man_db.conf ”来观察该文件， 若想要在该文件内搜寻 MANPATH 这个字串时，可以这样做：

```
[root@study ~]# more /etc/man_db.conf
#
#
# This file is used by the man-db package to configure the man and cat paths.
# It is also used to provide a manpath for those without one by examining
# their PATH environment variable. For details see the manpath（5） man page.
#
....（中间省略）....
/MANPATH   &lt;== 输入了 / 之后，光标就会自动跑到最下面一行等待输入！
```

如同上面的说明，输入了 / 之后，光标就会跑到最下面一行，并且等待你的输入， 你输入了字串并按下[enter]之后，嘿嘿！ more 就会开始向下搜寻该字串啰～而重复搜寻同一个字串， 可以直接按下 n 即可啊！最后，不想要看了，就按下 q 即可离开 more 啦！

- less （一页一页翻动）

```
[root@study ~]# less /etc/man_db.conf
#
#
# This file is used by the man-db package to configure the man and cat paths.
# It is also used to provide a manpath for those without one by examining
# their PATH environment variable. For details see the manpath（5） man page.
#
.....（中间省略）.....
:   &lt;== 这里可以等待你输入指令！
```

less 的用法比起 more 又更加的有弹性，怎么说呢？在 more 的时候，我们并没有办法向前面翻， 只能往后面看，但若使用了 less 时，呵呵！就可以使用 [pageup] [pagedown] 等按键的功能来往前往后翻看文件，你瞧，是不是更容易使用来观看一个文件的内容了呢！

除此之外，在 less 里头可以拥有更多的“搜寻”功能喔！不止可以向下搜寻，也可以向上搜寻～ 实在是很不错用～基本上，可以输入的指令有：

- 空白键 ：向下翻动一页；
- [pagedown]：向下翻动一页；
- [pageup] ：向上翻动一页；
- /字串 ：向下搜寻“字串”的功能；
- ?字串 ：向上搜寻“字串”的功能；
- n ：重复前一个搜寻 （与 / 或 ? 有关！）
- N ：反向的重复前一个搜寻 （与 / 或 ? 有关！）
- g ：前进到这个数据的第一行去；
- G ：前进到这个数据的最后一行去 （注意大小写）；
- q ：离开 less 这个程序；

查阅文件内容还可以进行搜寻的动作～瞧～ less 是否很不错用啊！ 其实 less 还有很多的功能喔！详细的使用方式请使用 man less 查询一下啊！ ^_^

**你是否会觉得 less 使用的画面与环境与 [man page](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#manual_man) 非常的类似呢？没错啦！因为man这个指令就是调用 less 来显示说明文档的内容的！ 现在你是否觉得 less 很重要呢？** ^_^



#### 6.3.3 数据撷取

**常用命令**：

- **head**（取出前几行）

  ```
  head [-n number] 文件名
  ```
  
  示例：

  ```
  head -n 20 /etc/man_db.conf
  ```
  
- **tail**（取出后几行）

  ```
   tail [-n number] 文件名
  ```
  
  示例：

  ```
  tail -n 20 /etc/man_db.conf
  ```
  
  持续监视文件：
  
  ```
  tail -f /var/log/messages
  ```

  例题：显示 `/etc/man_db.conf` 的第 11 到第 20 行
  
  ```
  head -n 20 /etc/man_db.conf | tail -n 10
  ```

**原文：**

- head （取出前面几行）

```
[root@study ~]# head [-n number] 文件
选项与参数：
-n  ：后面接数字，代表显示几行的意思

[root@study ~]# head /etc/man_db.conf
# 默认的情况中，显示前面十行！若要显示前 20 行，就得要这样：
[root@study ~]# head -n 20 /etc/man_db.conf

范例：如果后面100行的数据都不打印，只打印/etc/man_db.conf的前面几行，该如何是好？
[root@study ~]# head -n -100 /etc/man_db.conf
```

head 的英文意思就是“头”啦，那么这个东西的用法自然就是显示出一个文件的前几行啰！ 没错！就是这样！若没有加上 -n 这个选项时，默认只显示十行，若只要一行呢？那就加入“ head -n 1 filename ”即可！

另外那个 -n 选项后面的参数较有趣，如果接的是负数，例如上面范例的-n -100时，代表列前的所有行数， 但不包括后面100行。举例来说 CentOS 7.1 的 /etc/man*db.conf 共有131行，则上述的指令“head -n -100 /etc/man_db.conf” 就会列出前面31行，后面100行不会打印出来了。这样说，比较容易懂了吧？ ^*^

- tail （取出后面几行）

```
[root@study ~]# tail [-n number] 文件
选项与参数：
-n  ：后面接数字，代表显示几行的意思
-f  ：表示持续侦测后面所接的文件名，要等到按下[ctrl]-c才会结束tail的侦测

[root@study ~]# tail /etc/man_db.conf
# 默认的情况中，显示最后的十行！若要显示最后的 20 行，就得要这样：
[root@study ~]# tail -n 20 /etc/man_db.conf

范例一：如果不知道/etc/man_db.conf有几行，却只想列出100行以后的数据时？
[root@study ~]# tail -n +100 /etc/man_db.conf

范例二：持续侦测/var/log/messages的内容
[root@study ~]# tail -f /var/log/messages
  &lt;==要等到输入[crtl]-c之后才会离开tail这个指令的侦测！
```

有 head 自然就有 tail （ 尾巴 ） 啰！没错！这个 tail 的用法跟 head 的用法差不多类似，只是显示的是后面几行就是了！默认也是显示十行，若要显示非十行，就加 -n number 的选项即可。

范例一的内容就有趣啦！其实与head -n -xx有异曲同工之妙。当下达“tail -n +100 /etc/man_db.conf” 代表该文件从100行以后都会被列出来，同样的，在man_db.conf共有131行，因此第100~131行就会被列出来啦！ 前面的99行都不会被显示出来喔！

至于范例二中，由于/var/log/messages随时会有数据写入，你想要让该文件有数据写入时就立刻显示到屏幕上， 就利用 -f 这个选项，他可以一直侦测/var/log/messages这个文件，新加入的数据都会被显示到屏幕上。 直到你按下[crtl]-c才会离开tail的侦测喔！由于 messages 必须要 root 权限才能看，所以该范例得要使用 root 来查询喔！

例题：假如我想要显示 /etc/man_db.conf 的第 11 到第 20 行呢？答：这个应该不算难，想一想，在第 11 到第 20 行，那么我取前 20 行，再取后十行，所以结果就是：“ head -n 20 /etc/man_db.conf | tail -n 10 ”，这样就可以得到第 11 到第 20 行之间的内容了！

这两个指令中间有个管线 （|） 的符号存在，这个管线的意思是：“前面的指令所输出的讯息，请通过管线交由后续的指令继续使用”的意思。 所以， head -n 20 /etc/man_db.conf 会将文件内的 20 行取出来，但不输出到屏幕上，而是转交给后续的 tail 指令继续处理。 因此 tail “不需要接文件名”，因为 tail 所需要的数据是来自于 head 处理后的结果！这样说，有没有理解？

更多的管线命令，我们会在第三篇继续解释的！

例题：承上一题，那如果我想要列出正确的行号呢？就是屏幕上仅列出 /etc/man_db.conf 的第 11 到第 20 行，且有行号存在？答：我们可以通过 cat -n 来带出行号，然后再通过 head/tail 来撷取数据即可！所以就变成如下的模样了： cat -n /etc/man_db.conf | head -n 20 | tail -n 10



#### 6.3.4 非纯文本文件：od

**od** 命令用于查看二进制文件内容。

```
od [-t TYPE] 文件名
```

常用选项：

- `-t c`：以 ASCII 字符显示。
- `-t oCc`：以 8 进制显示字符和 ASCII 对照。

示例：

```
od -t c /usr/bin/passwd
```

原文：

我们上面提到的，都是在查阅纯文本文件的内容。那么万一我们想要查阅非文本文件，举例来说，例如 /usr/bin/passwd 这个可执行文件的内容时， 又该如何去读出信息呢？**事实上，由于可执行文件通常是 binary file ，使用上头提到的指令来读取他的内容时， 确实会产生类似乱码的数据啊！那怎么办？没关系，我们可以利用 od 这个指令来读取喔！**

```
[root@study ~]# od [-t TYPE] 文件
选项或参数：
-t  ：后面可以接各种“类型 （TYPE）”的输出，例如：
      a       ：利用默认的字符来输出；
      c       ：使用 ASCII 字符来输出
      d[size] ：利用十进制（decimal）来输出数据，每个整数占用 size Bytes ；
      f[size] ：利用浮点数值（floating）来输出数据，每个数占用 size Bytes ；
      o[size] ：利用八进位（octal）来输出数据，每个整数占用 size Bytes ；
      x[size] ：利用十六进制（hexadecimal）来输出数据，每个整数占用 size Bytes ；

范例一：请将/usr/bin/passwd的内容使用ASCII方式来展现！
[root@study ~]# od -t c /usr/bin/passwd
0000000 177   E   L   F 002 001 001  \0  \0  \0  \0  \0  \0  \0  \0  \0
0000020 003  \0   &gt;  \0 001  \0  \0  \0 364   3  \0  \0  \0  \0  \0  \0
0000040   @  \0  \0  \0  \0  \0  \0  \0   x   e  \0  \0  \0  \0  \0  \0
0000060  \0  \0  \0  \0   @  \0   8  \0  \t  \0   @  \0 035  \0 034  \0
0000100 006  \0  \0  \0 005  \0  \0  \0   @  \0  \0  \0  \0  \0  \0  \0
.....（后面省略）....
# 最左边第一栏是以 8 进位来表示Bytes数。以上面范例来说，第二栏0000020代表开头是
# 第 16 个 byes （2x8） 的内容之意。

范例二：请将/etc/issue这个文件的内容以8进位列出储存值与ASCII的对照表
[root@study ~]# od -t oCc /etc/issue
0000000 134 123 012 113 145 162 156 145 154 040 134 162 040 157 156 040
          \   S  \n   K   e   r   n   e   l       \   r       o   n
0000020 141 156 040 134 155 012 012
          a   n       \   m  \n  \n
0000027
# 如上所示，可以发现每个字符可以对应到的数值为何！要注意的是，该数值是 8 进位喔！
# 例如 S 对应的记录数值为 123 ，转成十进制：1x8^2+2x8+3=83。
```

利用这个指令，可以将 data file 或者是 binary file 的内容数据给他读出来喔！ 虽然读出的来数值默认是使用非文本文件，亦即是 16 进位的数值来显示的， 不过，我们还是可以通过 -t c 的选项与参数来将数据内的字符以 ASCII 类型的字符来显示， **虽然对于一般使用者来说，这个指令的用处可能不大，但是对于工程师来说， 这个指令可以将 binary file 的内容作一个大致的输出，他们可以看得出东西的啦～ ^_^**

如果对纯文本文件使用这个指令，你甚至可以发现到 ASCII 与字符的对照表！非常有趣！ 例如上述的范例二，你可以发现到每个英文字 S 对照到的数字都是 123，转成十进制你就能够发现那是 83 啰！ 如果你有任何程序语言的书，拿出来对照一下 ASCII 的对照表，就能够发现真是正确啊！呵呵！

例题：我不想找 google，想要立刻找到 password 这几个字的 ASCII 对照，该如何通过 od 来判断？答：其实可以通过刚刚上一个小节谈到的管线命令来处理！如下所示： echo password | od -t oCc echo 可以在屏幕上面显示任何信息，而这个信息不由屏幕输出，而是传给 od 去继续处理！就可以得到 ASCII code 对照啰！



#### 6.3.5 修改文件时间或创建新文件：touch

**touch** 命令用于修改文件时间或创建新文件。

```
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
   touch testfile
   ```
   
2. 修改文件的日期和时间：

   ```
   touch -d "2 days ago" testfile
   ```
   
3. 将文件时间设为特定日期：

   ```
   touch -t 201406150202 testfile
   ```

**原文：**

我们在 [ls 这个指令的介绍](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#ls)时，有稍微提到每个文件在linux下面都会记录许多的时间参数， 其实是有三个主要的变动时间，那么三个时间的意义是什么呢？

- **modification time （mtime）**： **当该文件的“内容数据”变更时，就会更新这个时间！内容数据指的是文件的内容，而不是文件的属性或权限喔！**
- **status time （ctime）**： 当该文件的“状态 （status）”改变时，就会更新这个时间，举例来说，像是权限与属性被更改了，都会更新这个时间啊。
- **access time （atime）**： 当“该文件的内容被取用”时，就会更新这个读取时间 （access）。举例来说，我们使用 cat 去读取 /etc/man_db.conf ， 就会更新该文件的 atime 了。

这是个挺有趣的现象，举例来说，我们来看一看你自己的 /etc/man_db.conf 这个文件的时间吧！

```
[root@study ~]# date; ls -l /etc/man_db.conf ; ls -l --time=atime /etc/man_db.conf ; \
&gt; ls -l --time=ctime /etc/man_db.conf # 这两行其实是同一行喔！用分号隔开
Tue Jun 16 00:43:17 CST 2015  # 目前的时间啊！
-rw-r--r--. 1 root root 5171 Jun 10  2014 /etc/man_db.conf  # 在 2014/06/10 创建的内容（mtime）
-rw-r--r--. 1 root root 5171 Jun 15 23:46 /etc/man_db.conf  # 在 2015/06/15 读取过内容（atime）
-rw-r--r--. 1 root root 5171 May  4 17:54 /etc/man_db.conf  # 在 2015/05/04 更新过状态（ctime）
# 为了要让数据输出比较好看，所以鸟哥将三个指令同时依序执行，三个指令中间用分号 （;） 隔开即可。
```

**看到了吗？在默认的情况下，ls 显示出来的是该文件的 mtime ，也就是这个文件的内容上次被更动的时间。** 至于鸟哥的系统是在 5 月 4 号的时候安装的，因此，这个文件被产生导致状态被更动的时间就回溯到那个时间点了（ctime）！ 而还记得刚刚我们使用的范例当中，**有使用到man_db.conf这个文件啊，所以啊，他的 atime 就会变成刚刚使用的时间了！**

文件的时间是很重要的，因为，如果文件的时间误判的话，可能会造成某些程序无法顺利的运行。 OK！那么万一我发现了一个文件来自未来，该如何让该文件的时间变成“现在”的时刻呢？ 很简单啊！就用“touch”这个指令即可！



**Tips** 嘿嘿！不要怀疑系统时间会“来自未来”喔！很多时候会有这个问题的！举例来说在安装过后系统时间可能会被改变！ 因为台湾时区在国际标准时间“格林威治时间, GMT”的右边，所以会比较早看到阳光，也就是说，台湾时间比GMT时间快了八小时！ 如果安装行为不当，我们的系统可能会有八小时快转，你的文件就有可能来自八小时后了。

至于某些情况下，由于BIOS的设置错误，导致系统时间跑到未来时间，并且你又创建了某些文件。 等你将时间改回正确的时间时，该文件不就变成来自未来了？^_^

```
[root@study ~]# touch [-acdmt] 文件
选项与参数：
-a  ：仅修订 access time；
-c  ：仅修改文件的时间，若该文件不存在则不创建新文件；
-d  ：后面可以接欲修订的日期而不用目前的日期，也可以使用 --date="日期或时间"
-m  ：仅修改 mtime ；
-t  ：后面可以接欲修订的时间而不用目前的时间，格式为[YYYYMMDDhhmm]

范例一：新建一个空的文件并观察时间
[dmtsai@study ~]# cd /tmp
[dmtsai@study tmp]# touch testtouch
[dmtsai@study tmp]# ls -l testtouch
-rw-rw-r--. 1 dmtsai dmtsai 0 Jun 16 00:45 testtouch
# 注意到，这个文件的大小是 0 呢！在默认的状态下，如果 touch 后面有接文件，
# 则该文件的三个时间 （atime/ctime/mtime） 都会更新为目前的时间。若该文件不存在，
# 则会主动的创建一个新的空的文件喔！例如上面这个例子！

范例二：将 ~/.bashrc 复制成为 bashrc，假设复制完全的属性，检查其日期
[dmtsai@study tmp]# cp -a ~/.bashrc bashrc
[dmtsai@study tmp]# date; ll bashrc; ll --time=atime bashrc; ll --time=ctime bashrc
Tue Jun 16 00:49:24 CST 2015                         &lt;==这是目前的时间
-rw-r--r--. 1 dmtsai dmtsai 231 Mar  6 06:06 bashrc  &lt;==这是 mtime
-rw-r--r--. 1 dmtsai dmtsai 231 Jun 15 23:44 bashrc  &lt;==这是 atime
-rw-r--r--. 1 dmtsai dmtsai 231 Jun 16 00:47 bashrc  &lt;==这是 ctime
```

在上面这个案例当中我们使用了“ll”这个指令（两个英文L的小写），这个指令其实就是“ls -l”的意思， ll本身不存在，是被“做出来”的一个命令别名。相关的[命令别名我们会在bash章节](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#alias)当中详谈的，这里先知道ll="ls -l"即可。 至于分号“ ; ”则代表连续指令的下达啦！你可以在一行指令当中写入多重指令， 这些指令可以“依序”执行。由上面的指令我们会知道ll那一行有三个指令被下达在同一行中。

至于执行的结果当中，我们可以发现数据的内容与属性是被复制过来的，因此文件内容时间（mtime）与原本文件相同。 但是由于这个文件是刚刚被创建的，因此状态（ctime）就变成现在的时间啦！那如果你想要变更这个文件的时间呢？可以这样做：

```
范例三：修改案例二的 bashrc 文件，将日期调整为两天前
[dmtsai@study tmp]# touch -d "2 days ago" bashrc
[dmtsai@study tmp]# date; ll bashrc; ll --time=atime bashrc; ll --time=ctime bashrc
Tue Jun 16 00:51:52 CST 2015
-rw-r--r--. 1 dmtsai dmtsai 231 Jun 14 00:51 bashrc
-rw-r--r--. 1 dmtsai dmtsai 231 Jun 14 00:51 bashrc
-rw-r--r--. 1 dmtsai dmtsai 231 Jun 16 00:51 bashrc
# 跟上个范例比较看看，本来是 16 日变成 14 日了 （atime/mtime）～不过， ctime 并没有跟着改变喔！

范例四：将上个范例的 bashrc 日期改为 2014/06/15 2:02
[dmtsai@study tmp]# touch -t 201406150202 bashrc
[dmtsai@study tmp]# date; ll bashrc; ll --time=atime bashrc; ll --time=ctime bashrc
Tue Jun 16 00:54:07 CST 2015
-rw-r--r--. 1 dmtsai dmtsai 231 Jun 15  2014 bashrc
-rw-r--r--. 1 dmtsai dmtsai 231 Jun 15  2014 bashrc
-rw-r--r--. 1 dmtsai dmtsai 231 Jun 16 00:54 bashrc
# 注意看看，日期在 atime 与 mtime 都改变了，但是 ctime 则是记录目前的时间！
```

通过 touch 这个指令，我们可以轻易的修订文件的日期与时间。并且也可以创建一个空的文件喔！ 不过，要注意的是，即使我们复制一个文件时，复制所有的属性，但也没有办法复制 ctime 这个属性的。 ctime 可以记录这个文件最近的状态 （status） 被改变的时间。无论如何，还是要告知大家， 我们平时看的文件属性中，比较重要的还是属于那个 mtime 啊！我们关心的常常是这个文件的“内容” 是什么时候被更动的说～瞭乎？

无论如何， touch 这个指令最常被使用的情况是：

- 创建一个空的文件；
- 将某个文件日期修订为目前 （mtime 与 atime）



### 6.4 文件与目录的默认权限与隐藏权限

#### 6.4.1 文件默认权限：umask

在 Linux 系统中，创建文件或目录时，默认的权限由 `umask` 决定。`umask` 指定了新创建文件或目录的默认权限。

**如何查看和设置 umask**：

```
[root@study ~]# umask
0022  # 与一般权限有关的是后面三个数字！
[root@study ~]# umask -S
u=rwx,g=rx,o=rx  # 以符号方式显示权限
```

**默认权限**：

- 新创建文件的最大权限为 `666`（即 `-rw-rw-rw-`）。
- 新创建目录的最大权限为 `777`（即 `drwxrwxrwx`）。

**`umask` 的值是减去的权限**，因此：

- ==如果 `umask` 为 `022`，文件的默认权限是 `666 - 022 = 644`（即 `-rw-r--r--`）。==
- ==如果 `umask` 为 `022`，目录的默认权限是 `777 - 022 = 755`（即 `drwxr-xr-x`）。==

**示例**：

```
[root@study ~]# umask
0022
[root@study ~]# touch test1
[root@study ~]# mkdir test2
[root@study ~]# ll -d test*
-rw-r--r--. 1 root root 0 Jun 16 01:11 test1
drwxr-xr-x. 2 root root 6 Jun 16 01:11 test2
```

**设置 umask**：

```
[root@study ~]# umask 002
[root@study ~]# touch test3
[root@study ~]# mkdir test4
[root@study ~]# ll -d test[34]
-rw-rw-r--. 1 root root 0 Jun 16 01:12 test3
drwxrwxr-x. 2 root root 6 Jun 16 01:12 test4
```

原文：

OK！那么现在我们知道如何创建或者是改变一个目录或文件的属性了，不过， 你知道当你创建一个新的文件或目录时，他的默认权限会是什么吗？呵呵！那就与 umask 这个玩意儿有关了！那么 umask 是在搞什么呢？基本上， umask 就是指定 “目前使用者在创建文件或目录时候的权限默认值”， 那么如何得知或设置 umask 呢？他的指定条件以下面的方式来指定：

```
[root@study ~]# umask
0022             &lt;==与一般权限有关的是后面三个数字！
[root@study ~]# umask -S
u=rwx,g=rx,o=rx
```

查阅的方式有两种，一种可以直接输入 umask ，就可以看到数字体态的权限设置分数， 一种则是加入 -S （Symbolic） 这个选项，就会以符号类型的方式来显示出权限了！ 奇怪的是，怎么 umask 会有四组数字啊？不是只有三组吗？是没错啦。 第一组是特殊权限用的，我们先不要理他，所以先看后面三组即可。

在默认权限的属性上，目录与文件是不一样的。从第五章我们知道 x 权限对于目录是非常重要的！ 但是一般文件的创建则不应该有执行的权限，因为一般文件通常是用在于数据的记录嘛！当然不需要执行的权限了。 因此，默认的情况如下：

- 若使用者创建为“文件”则默认“没有可执行（ x ）权限”，亦即只有 rw 这两个项目，也就是最大为 666 分，默认权限如下： -rw-rw-rw-
- 若使用者创建为“目录”，则由于 x 与是否可以进入此目录有关，因此默认为所有权限均开放，亦即为 777 分，默认权限如下： drwxrwxrwx

要注意的是，umask 的分数指的是“该默认值需要减掉的权限！”因为 r、w、x 分别是 4、2、1 分，所以啰！也就是说，当要拿掉能写的权限，就是输入 2 分，而如果要拿掉能读的权限，也就是 4 分，那么要拿掉读与写的权限，也就是 6 分，而要拿掉执行与写入的权限，也就是 3 分，这样了解吗？请问你， 5 分是什么？呵呵！ 就是读与执行的权限啦！

如果以上面的例子来说明的话，因为 umask 为 022 ，所以 user 并没有被拿掉任何权限，不过 group 与 others 的权限被拿掉了 2 （也就是 w 这个权限），那么当使用者：

- 创建文件时：（-rw-rw-rw-） - （-----w--w-） ==> -rw-r--r--
- 创建目录时：（drwxrwxrwx） - （d----w--w-） ==> drwxr-xr-x

不相信吗？我们就来测试看看吧！

```
[root@study ~]# umask
0022
[root@study ~]# touch test1
[root@study ~]# mkdir test2
[root@study ~]# ll -d test*
-rw-r--r--. 1 root root 0  6月 16 01:11 test1
drwxr-xr-x. 2 root root 6  6月 16 01:11 test2
```

呵呵！瞧见了吧！确定新建文件的权限是没有错的。

- umask的利用与重要性：专题制作

想像一个状况，如果你跟你的同学在同一部主机里面工作时，因为你们两个正在进行同一个专题， 老师也帮你们两个的帐号创建好了相同群组的状态，并且将 /home/class/ 目录做为你们两个人的专题目录。 想像一下，有没有可能你所制作的文件你的同学无法编辑？果真如此的话，那就伤脑筋了！

这个问题很常发生啊！举上面的案例来看就好了，你看一下 test1 的权限是几分？ 644 呢！意思是“如果 umask 订定为 022 ，那新建的数据只有使用者自己具有 w 的权限， 同群组的人只有 r 这个可读的权限而已，并无法修改喔！”这样要怎么共同制作专题啊！您说是吧！

所以，当我们需要新建文件给同群组的使用者共同编辑时，那么 umask 的群组就不能拿掉 2 这个 w 的权限！ 所以啰， umask 就得要是 002 之类的才可以！这样新建的文件才能够是 -rw-rw-r-- 的权限模样喔！ 那么如何设置 umask 呢？简单的很，直接在 umask 后面输入 002 就好了！

```
[root@study ~]# umask 002
[root@study ~]# touch test3
[root@study ~]# mkdir test4
[root@study ~]# ll -d test[34]   # 中括号 [ ] 代表中间有个指定的字符，而不是任意字符的意思
-rw-rw-r--. 1 root root 0  6月 16 01:12 test3
drwxrwxr-x. 2 root root 6  6月 16 01:12 test4
```

所以说，这个 umask 对于新建文件与目录的默认权限是很有关系的！这个概念可以用在任何服务器上面， 尤其是未来在你架设文件服务器 （file server） ，举例来说， [SAMBA Server](http://linux.vbird.org/linux_server/0370samba.php) 或者是 [FTP server](http://linux.vbird.org/linux_server/0410vsftpd.php) 时， 都是很重要的观念！这牵涉到你的使用者是否能够将文件进一步利用的问题喔！不要等闲视之！

例题：假设你的 umask 为 003 ，请问该 umask 情况下，创建的文件与目录权限为？答：umask 为 003 ，所以拿掉的权限为 --------wx，因此： 文件： （-rw-rw-rw-） - （--------wx） = -rw-rw-r-- 目录： （drwxrwxrwx） - （d-------wx） = drwxrwxr--

![鸟哥的图示](image/vbird_face.gif)

**Tips** 关于 umask 与权限的计算方式中，教科书喜欢使用二进制的方式来进行 AND 与 NOT 的计算， 不过，鸟哥还是比较喜欢使用符号方式来计算～联想上面比较容易一点～

但是，有的书籍或者是 BBS 上面的朋友，喜欢使用文件默认属性 666 与目录默认属性 777 来与 umask 进行相减的计算～这是不好的喔！以上面例题来看， 如果使用默认属性相加减，则文件变成：666-003=663，亦即是 -rw-rw--wx ，这可是完全不对的喔！ 想想看，原本文件就已经去除 x 的默认属性了，怎么可能突然间冒出来了？ 所以，这个地方得要特别小心喔！

在默认的情况中， root 的 umask 会拿掉比较多的属性，root 的 umask 默认是 022 ， 这是基于安全的考虑啦～至于一般身份使用者，通常他们的 umask 为 002 ，亦即保留同群组的写入权力！ 其实，关于默认 umask 的设置可以参考 /etc/bashrc 这个文件的内容，不过，不建议修改该文件， 你可以参考[第十章 bash shell 提到的环境参数配置文件](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#settings_bashrc_bashrc) （~/.bashrc） 的说明！



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
[root@study ~]# chattr +i attrtest  # 设置不可修改属性
[root@study ~]# lsattr attrtest
----i---------- attrtest

[root@study ~]# chattr -i attrtest  # 取消不可修改属性
```

**原文：**

什么？文件还有隐藏属性？光是那九个权限就快要疯掉了，竟然还有隐藏属性，真是要命～ 但是没办法，就是有文件的隐藏属性存在啊！不过，这些隐藏的属性确实对于系统有很大的帮助的～ 尤其是在系统安全 （Security） 上面，重要的紧呢！不过要先强调的是，下面的chattr指令只能在Ext2/Ext3/Ext4的 Linux 传统文件系统上面完整生效， 其他的文件系统可能就无法完整的支持这个指令了，例如 xfs 仅支持部份参数而已。下面我们就来谈一谈如何设置与检查这些隐藏的属性吧！

- chattr （设置文件隐藏属性）

```
[root@study ~]# chattr [+-=][ASacdistu] 文件或目录名称
选项与参数：
+   ：增加某一个特殊参数，其他原本存在参数则不动。
-   ：移除某一个特殊参数，其他原本存在参数则不动。
=   ：设置一定，且仅有后面接的参数

A  ：当设置了 A 这个属性时，若你有存取此文件（或目录）时，他的存取时间 atime 将不会被修改，
     可避免 I/O 较慢的机器过度的存取磁盘。（目前建议使用文件系统挂载参数处理这个项目）
S  ：一般文件是非同步写入磁盘的（原理请参考[前一章sync](../Text/index.html#sync)的说明），如果加上 S 这个属性时，
     当你进行任何文件的修改，该更动会“同步”写入磁盘中。
a  ：当设置 a 之后，这个文件将只能增加数据，而不能删除也不能修改数据，只有root 才能设置这属性
c  ：这个属性设置之后，将会自动的将此文件“压缩”，在读取的时候将会自动解压缩，
     但是在储存的时候，将会先进行压缩后再储存（看来对于大文件似乎蛮有用的！）
d  ：当 dump 程序被执行的时候，设置 d 属性将可使该文件（或目录）不会被 dump 备份
i  ：这个 i 可就很厉害了！他可以让一个文件“不能被删除、改名、设置链接也无法写入或新增数据！”
     对于系统安全性有相当大的助益！只有 root 能设置此属性
s  ：当文件设置了 s 属性时，如果这个文件被删除，他将会被完全的移除出这个硬盘空间，
     所以如果误删了，完全无法救回来了喔！
u  ：与 s 相反的，当使用 u 来设置文件时，如果该文件被删除了，则数据内容其实还存在磁盘中，
     可以使用来救援该文件喔！
注意1：属性设置常见的是 a 与 i 的设置值，而且很多设置值必须要身为 root 才能设置
注意2：xfs 文件系统仅支持 AadiS 而已

范例：请尝试到/tmp下面创建文件，并加入 i 的参数，尝试删除看看。
[root@study ~]# cd /tmp
[root@study tmp]# touch attrtest     &lt;==创建一个空文件
[root@study tmp]# chattr +i attrtest &lt;==给予 i 的属性
[root@study tmp]# rm attrtest        &lt;==尝试删除看看
rm: remove regular empty file `attrtest'? y
rm: cannot remove `attrtest': Operation not permitted
# 看到了吗？呼呼！连 root 也没有办法将这个文件删除呢！赶紧解除设置！

范例：请将该文件的 i 属性取消！
[root@study tmp]# chattr -i attrtest
```

这个指令是很重要的，尤其是在系统的数据安全上面！由于这些属性是隐藏的性质，所以需要以 [lsattr](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#lsattr) 才能看到该属性呦！其中，个人认为最重要的当属 +i 与 +a 这个属性了。+i 可以让一个文件无法被更动，对于需要强烈的系统安全的人来说， 真是相当的重要的！里头还有相当多的属性是需要 root 才能设置的呢！

此外，如果是 log file 这种的登录文件，就更需要 +a 这个可以增加，但是不能修改旧有的数据与删除的参数了！怎样？很棒吧！ 未来提到[登录文件 （十八章）](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 的认知时，我们再来聊一聊如何设置他吧！

- lsattr （显示文件隐藏属性）

```
[root@study ~]# lsattr [-adR] 文件或目录
选项与参数：
-a ：将隐藏文件的属性也秀出来；
-d ：如果接的是目录，仅列出目录本身的属性而非目录内的文件名；
-R ：连同子目录的数据也一并列出来！

[root@study tmp]# chattr +aiS attrtest
[root@study tmp]# lsattr attrtest
--S-ia---------- attrtest
```

使用 chattr 设置后，可以利用 lsattr 来查阅隐藏的属性。不过， 这两个指令在使用上必须要特别小心，否则会造成很大的困扰。例如：某天你心情好，突然将 /etc/shadow 这个重要的密码记录文件给他设置成为具有 i 的属性，那么过了若干天之后， 你突然要新增使用者，却一直无法新增！别怀疑，赶快去将 i 的属性拿掉吧！



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
[root@study tmp]# touch test
[root@study tmp]# chmod 4755 test; ls -l test
-rwsr-xr-x 1 root root 0 Jun 16 02:53 test

[root@study tmp]# chmod 6755 test; ls -l test
-rwsr-sr-x 1 root root 0 Jun 16 02:53 test

[root@study tmp]# chmod 1755 test; ls -l test
-rwxr-xr-t 1 root root 0 Jun 16 02:53 test

[root@study tmp]# chmod 7666 test; ls -l test
-rwSrwSrwT 1 root root 0 Jun 16 02:53 test
```

**原文：**

我们前面一直提到关于文件的重要权限，那就是 rwx 这三个读、写、执行的权限。 但是，眼尖的朋友们在[第五章的目录树章节](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#dir_tree)中， 一定注意到了一件事，那就是，怎么我们的 /tmp 权限怪怪的？ 还有，那个 /usr/bin/passwd 也怪怪的？怎么回事啊？看看先：

```
[root@study ~]# ls -ld /tmp ; ls -l /usr/bin/passwd
drwxrwxrwt. 14 root root 4096 Jun 16 01:27 /tmp
-rwsr-xr-x. 1 root root 27832 Jun 10  2014 /usr/bin/passwd
```

不是应该只有 rwx 吗？还有其他的特殊权限（ s 跟 t ）啊？啊.....头又开始昏了～ @_@ 因为 s 与 t 这两个权限的意义与[系统的帐号 （第十三章）](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)及[系统的程序（process, 第十六章）](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)较为相关， 所以等到后面的章节谈完后你才会比较有概念！下面的说明先看看就好，如果看不懂也没有关系， 先知道s放在哪里称为SUID/SGID以及如何设置即可，等系统程序章节读完后，再回来看看喔！

- Set UID

当 s 这个标志出现在文件拥有者的 x 权限上时，例如刚刚提到的 /usr/bin/passwd 这个文件的权限状态：“-rw**s**r-xr-x”，此时就被称为 Set UID，简称为 SUID 的特殊权限。 那么SUID的权限对于一个文件的特殊功能是什么呢？基本上SUID有这样的限制与功能：

- SUID 权限仅对二进制程序（binary program）有效；
- 执行者对于该程序需要具有 x 的可执行权限；
- 本权限仅在执行该程序的过程中有效 （run-time）；
- 执行者将具有该程序拥有者 （owner） 的权限。

讲这么硬的东西你可能对于 SUID 还是没有概念，没关系，我们举个例子来说明好了。 我们的 Linux 系统中，所有帐号的密码都记录在 /etc/shadow 这个文件里面，这个文件的权限为：“---------- 1 root root”，意思是这个文件仅有root可读且仅有root可以强制写入而已。 既然这个文件仅有 root 可以修改，那么鸟哥的 dmtsai 这个一般帐号使用者能否自行修改自己的密码呢？ 你可以使用你自己的帐号输入“passwd”这个指令来看看，嘿嘿！一般使用者当然可以修改自己的密码了！

唔！有没有冲突啊！明明 /etc/shadow 就不能让 dmtsai 这个一般帐户去存取的，为什么 dmtsai 还能够修改这个文件内的密码呢？ 这就是 SUID 的功能啦！借由上述的功能说明，我们可以知道

1. dmtsai 对于 /usr/bin/passwd 这个程序来说是具有 x 权限的，表示 dmtsai 能执行 passwd；
2. passwd 的拥有者是 root 这个帐号；
3. dmtsai 执行 passwd 的过程中，会“暂时”获得 root 的权限；
4. /etc/shadow 就可以被 dmtsai 所执行的 passwd 所修改。

但如果 dmtsai 使用 cat 去读取 /etc/shadow 时，他能够读取吗？因为 cat 不具有 SUID 的权限，所以 dmtsai 执行 “cat /etc/shadow” 时，是不能读取 /etc/shadow 的。我们用一张示意图来说明如下：

![SUID程序执行的过程示意图](image/centos7_suid.gif)图6.4.1、SUID程序执行的过程示意图

另外，SUID 仅可用在binary program 上， 不能够用在 shell script 上面！这是因为 shell script 只是将很多的 binary 可执行文件叫进来执行而已！所以 SUID 的权限部分，还是得要看 shell script 调用进来的程序的设置， 而不是 shell script 本身。当然，SUID 对于目录也是无效的～这点要特别留意。

- Set GID

当 s 标志在文件拥有者的 x 项目为 SUID，那 s 在群组的 x 时则称为 Set GID, SGID 啰！是这样没错！^_^。 举例来说，你可以用下面的指令来观察到具有 SGID 权限的文件喔：

```
[root@study ~]# ls -l /usr/bin/locate
-rwx--s--x. 1 root slocate 40496 Jun 10  2014 /usr/bin/locate
```

与 SUID 不同的是，SGID 可以针对文件或目录来设置！如果是对文件来说， SGID 有如下的功能：

- SGID 对二进制程序有用；
- 程序执行者对于该程序来说，需具备 x 的权限；
- 执行者在执行的过程中将会获得该程序群组的支持！

举例来说，上面的 /usr/bin/locate 这个程序可以去搜寻 /var/lib/mlocate/mlocate.db 这个文件的内容 （详细说明会在下节讲述）， mlocate.db 的权限如下：

```
[root@study ~]# ll /usr/bin/locate /var/lib/mlocate/mlocate.db
-rwx--s--x. 1 root slocate   40496 Jun 10  2014 /usr/bin/locate
-rw-r-----. 1 root slocate 2349055 Jun 15 03:44 /var/lib/mlocate/mlocate.db
```

与 SUID 非常的类似，若我使用 dmtsai 这个帐号去执行 locate 时，那 dmtsai 将会取得 slocate 群组的支持， 因此就能够去读取 mlocate.db 啦！非常有趣吧！

除了 binary program 之外，事实上 SGID 也能够用在目录上，这也是非常常见的一种用途！ 当一个目录设置了 SGID 的权限后，他将具有如下的功能：

- 使用者若对于此目录具有 r 与 x 的权限时，该使用者能够进入此目录；
- 使用者在此目录下的有效群组（effective group）将会变成该目录的群组；
- 用途：若使用者在此目录下具有 w 的权限（可以新建文件），则使用者所创建的新文件，该新文件的群组与此目录的群组相同。

SGID 对于专案开发来说是非常重要的！因为这涉及群组权限的问题，您可以参考一下本章后续[情境仿真的案例](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#situation1)，应该就能够对于 SGID 有一些了解的！^_^

- Sticky Bit

这个 Sticky Bit, SBIT 目前只针对目录有效，对于文件已经没有效果了。SBIT 对于目录的作用是：

- 当使用者对于此目录具有 w, x 权限，亦即具有写入的权限时；
- 当使用者在该目录下创建文件或目录时，仅有自己与 root 才有权力删除该文件

换句话说：当甲这个使用者于 A 目录是具有群组或其他人的身份，并且拥有该目录 w 的权限， 这表示“甲使用者对该目录内任何人创建的目录或文件均可进行 "删除/更名/搬移" 等动作。” 不过，如果将 A 目录加上了 SBIT 的权限项目时， 则甲只能够针对自己创建的文件或目录进行删除/更名/移动等动作，而无法删除他人的文件。

举例来说，我们的 /tmp 本身的权限是“drwxrwxrwt”， 在这样的权限内容下，任何人都可以在 /tmp 内新增、修改文件，但仅有该文件/目录创建者与 root 能够删除自己的目录或文件。这个特性也是挺重要的啊！你可以这样做个简单的测试：

1. 以 root 登陆系统，并且进入 /tmp 当中；
2. touch test，并且更改 test 权限成为 777 ；
3. 以一般使用者登陆，并进入 /tmp；
4. 尝试删除 test 这个文件！

由于 SUID/SGID/SBIT 牵涉到程序的概念，因此再次强调，这部份的数据在您读完[第十六章关于程序方面](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)的知识后，要再次的回来瞧瞧喔！ 目前，你先有个简单的基础概念就好了！文末的参考数据也建议阅读一番喔！

- SUID/SGID/SBIT 权限设置

前面介绍过 SUID 与 SGID 的功能，那么如何设置文件使成为具有 SUID 与 SGID 的权限呢？ 这就需要[第五章的数字更改权限](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)的方法了！ 现在你应该已经知道数字体态更改权限的方式为“三个数字”的组合， 那么如果在这三个数字之前再加上一个数字的话，最前面的那个数字就代表这几个权限了！

- 4 为 SUID
- 2 为 SGID
- 1 为 SBIT

假设要将一个文件权限改为“-rwsr-xr-x”时，由于 s 在使用者权限中，所以是 SUID ，因此， 在原先的 755 之前还要加上 4 ，也就是：“ chmod 4755 filename ”来设置！此外，还有大 S 与大 T 的产生喔！参考下面的范例啦！

![鸟哥的图示](image/vbird_face-17222396743093.gif)

**Tips** 注意：下面的范例只是练习而已，所以鸟哥使用同一个文件来设置，你必须了解 SUID 不是用在目录上，而 SBIT 不是用在文件上的喔！

```
[root@study ~]# cd /tmp
[root@study tmp]# touch test                  &lt;==创建一个测试用空档
[root@study tmp]# chmod 4755 test; ls -l test &lt;==加入具有 SUID 的权限
-rwsr-xr-x 1 root root 0 Jun 16 02:53 test
[root@study tmp]# chmod 6755 test; ls -l test &lt;==加入具有 SUID/SGID 的权限
-rwsr-sr-x 1 root root 0 Jun 16 02:53 test
[root@study tmp]# chmod 1755 test; ls -l test &lt;==加入 SBIT 的功能！
-rwxr-xr-t 1 root root 0 Jun 16 02:53 test
[root@study tmp]# chmod 7666 test; ls -l test &lt;==具有空的 SUID/SGID 权限
-rwSrwSrwT 1 root root 0 Jun 16 02:53 test
```

最后一个例子就要特别小心啦！怎么会出现大写的 S 与 T 呢？不都是小写的吗？ 因为 s 与 t 都是取代 x 这个权限的，但是你有没有发现阿，我们是下达 7666 喔！也就是说， user, group 以及 others 都没有 x 这个可执行的标志（ 因为 666 嘛 ），所以，这个 S, T 代表的就是“空的”啦！怎么说？ SUID 是表示“该文件在执行的时候，具有文件拥有者的权限”，但是文件 拥有者都无法执行了，哪里来的权限给其他人使用？当然就是空的啦！ ^_^

而除了数字法之外，你也可以通过符号法来处理喔！其中 SUID 为 u+s ，而 SGID 为 g+s ，SBIT 则是 o+t 啰！来看看如下的范例：

```
# 设置权限成为 -rws--x--x 的模样：
[root@study tmp]# chmod u=rwxs,go=x test; ls -l test
-rws--x--x 1 root root 0 Jun 16 02:53 test

# 承上，加上 SGID 与 SBIT 在上述的文件权限中！
[root@study tmp]# chmod g+s,o+t test; ls -l test
-rws--s--t 1 root root 0 Jun 16 02:53 test
```





#### 6.4.4 观察文件类型：file

使用 `file` 命令可以查看文件的基本类型信息，如 ASCII 文件、二进制文件等。

**示例**：

```
[root@study ~]# file ~/.bashrc
/root/.bashrc: ASCII text

[root@study ~]# file /usr/bin/passwd
/usr/bin/passwd: setuid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=0xbf35571e607e317bf107b9bcf65199988d0ed5ab, stripped

[root@study ~]# file /var/lib/mlocate/mlocate.db
/var/lib/mlocate/mlocate.db: data
```

**原文：**

如果你想要知道某个文件的基本数据，例如是属于 ASCII 或者是 data 文件，或者是 binary ， 且其中有没有使用到动态函数库 （share library） 等等的信息，就可以利用 file 这个指令来检阅喔！举例来说：

```
[root@study ~]# file ~/.bashrc
/root/.bashrc: ASCII text  &lt;==告诉我们是 ASCII 的纯文本文件啊！
[root@study ~]# file /usr/bin/passwd
/usr/bin/passwd: setuid ELF 64-bit LSB shared object, x86-64, version 1 （SYSV）, dynamically
linked （uses shared libs）, for GNU/Linux 2.6.32,
BuildID[sha1]=0xbf35571e607e317bf107b9bcf65199988d0ed5ab, stripped
# 可执行文件的数据可就多的不得了！包括这个文件的 suid 权限、相容于 Intel x86-64 等级的硬件平台
# 使用的是 Linux 核心 2.6.32 的动态函数库链接等等。
[root@study ~]# file /var/lib/mlocate/mlocate.db
/var/lib/mlocate/mlocate.db: data  &lt;== 这是 data 文件！
```

通过这个指令，我们可以简单的先判断这个文件的格式为何喔！包括未来你也可以用来判断使用 tar 包裹时，该 tarball 文件是使用哪一种压缩功能哩！



### 6.5 指令与文件的搜寻

#### 6.5.1 指令文件名的搜寻

在终端机中，我们可以通过 `which` 和 `type` 命令来找到指令的完整文件路径。

**which**（寻找“可执行文件”）

```
[root@study ~]# which [-a] command
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
[root@study ~]# type history
history is a shell builtin
```

**原文：**

我们知道在终端机模式当中，连续输入两次[tab]按键就能够知道使用者有多少指令可以下达。 那你知不知道这些指令的完整文件名放在哪里？举例来说，ls 这个常用的指令放在哪里呢？ 就通过 which 或 type 来找寻吧！

- which （寻找“可执行文件”）

```
[root@study ~]# which [-a] command
选项或参数：
-a ：将所有由 PATH 目录中可以找到的指令均列出，而不止第一个被找到的指令名称

范例一：搜寻 ifconfig 这个指令的完整文件名
[root@study ~]# which ifconfig
/sbin/ifconfig 

范例二：用 which 去找出 which 的文件名为何？
[root@study ~]# which which
alias which='alias &#124; /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
        /bin/alias
        /usr/bin/which
# 竟然会有两个 which ，其中一个是 alias 这玩意儿呢！那是啥？
# 那就是所谓的“命令别名”，意思是输入 which 会等于后面接的那串指令啦！
# 更多的数据我们会在 bash 章节中再来谈的！

范例三：请找出 history 这个指令的完整文件名
[root@study ~]# which history
/usr/bin/which: no history in （/usr/local/sbin:/usr/local/bin:/sbin:/bin:
/usr/sbin:/usr/bin:/root/bin）

[root@study ~]# history --help
-bash: history: --: invalid option
history: usage: history [-c] [-d offset] [n] or history -anrw [filename] or history -ps arg 
# 瞎密？怎么可能没有 history ，我明明就能够用 root 执行 history 的啊！
```

这个指令是根据“[PATH](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#dir_path)”这个环境变量所规范的路径，去搜寻“可执行文件”的文件名～ 所以，重点是找出“可执行文件”而已！且 which 后面接的是“完整文件名”喔！若加上 -a 选项，则可以列出所有的可以找到的同名可执行文件，而非仅显示第一个而已！

最后一个范例最有趣，怎么 history 这个常用的指令竟然找不到啊！为什么呢？这是因为 history 是“bash 内置的指令”啦！ 但是 which 默认是找 PATH 内所规范的目录，所以当然一定找不到的啊（有 bash 就有 history！）！那怎办？没关系！我们可以通过 type 这个指令喔！ 关于 type 的用法我们将在 [第十章的 bash](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#bash_type) 再来谈！





#### 6.5.2 文件文件名的搜寻

Linux 系统提供了多个文件搜索命令，包括 `whereis`、`locate` 和 `find`。

**whereis**（从特定目录中寻找文件文件名）

```
[root@study ~]# whereis [-bmsu] 文件或目录名
选项与参数：
-l ：列出 whereis 会去查询的几个主要目录
-b ：只找 binary 格式的文件
-m ：只找说明文档（manual）路径下的文件
-s ：只找 source 来源文件
-u ：搜寻不在上述三个项目中的其他特殊文件
```

**示例**：

```
[root@study ~]# whereis ifconfig
ifconfig: /sbin/ifconfig /usr/share/man/man8/ifconfig.8.gz

[root@study ~]# whereis -m passwd
passwd: /usr/share/man/man1/passwd.1.gz /usr/share/man/man5/passwd.5.gz
```

**locate / updatedb**

```
[root@study ~]# locate [-ir] keyword
选项与参数：
-i  ：忽略大小写差异
-c  ：不输出文件名，仅计算找到的文件数量
-l  ：仅输出几行，例如输出五行则是 -l 5
-S  ：输出 locate 所使用的数据库文件的相关信息
-r  ：接正则表达式显示方式
```

**示例**：

```
[root@study ~]# locate -l 5 passwd
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
[root@study ~]# updatedb
```

**find**

```
[root@study ~]# find [PATH] [option] [action]
```

**示例**：

```
# 搜寻过去24小时内有更动过内容的文件
[root@study ~]# find / -mtime 0

# 搜寻比 /etc/passwd 更新的文件
[root@study ~]# find /etc -newer /etc/passwd
```

**其他常用选项**：

```
# 按用户、群组搜寻
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

**原文：**

再来谈一谈怎么搜寻文件吧！在 Linux 下面也有相当优异的搜寻指令呦！通常 find 不很常用的！因为速度慢之外， 也很操硬盘！一般我们都是先使用 whereis 或者是 locate 来检查，如果真的找不到了，才以 find 来搜寻呦！ 为什么呢？因为 whereis 只找系统中某些特定目录下面的文件而已，locate 则是利用数据库来搜寻文件名，当然两者就相当的快速， 并且没有实际的搜寻硬盘内的文件系统状态，比较省时间啦！

- whereis （由一些特定的目录中寻找文件文件名）

```
[root@study ~]# whereis [-bmsu] 文件或目录名
选项与参数：
-l    :可以列出 whereis 会去查询的几个主要目录而已
-b    :只找 binary 格式的文件
-m    :只找在说明文档 manual 路径下的文件
-s    :只找 source 来源文件
-u    :搜寻不在上述三个项目当中的其他特殊文件

范例一：请找出 ifconfig 这个文件名
[root@study ~]# whereis ifconfig 
ifconfig: /sbin/ifconfig /usr/share/man/man8/ifconfig.8.gz

范例二：只找出跟 passwd 有关的“说明文档”文件名（man page）
[root@study ~]# whereis passwd     # 全部的文件名通通列出来！
passwd: /usr/bin/passwd /etc/passwd /usr/share/man/man1/passwd.1.gz /usr/share/man/man5/passwd.5.gz
[root@study ~]# whereis -m passwd  # 只有在 man 里面的文件名才抓出来！
passwd: /usr/share/man/man1/passwd.1.gz /usr/share/man/man5/passwd.5.gz
```

等一下我们会提到 find 这个搜寻指令， find 是很强大的搜寻指令，但时间花用的很大！（因为 find 是直接搜寻硬盘，为如果你的硬盘比较老旧的话，嘿嘿！有的等！） 这个时候 whereis 就相当的好用了！另外， whereis 可以加入选项来找寻相关的数据，例如，如果你是要找可可执行文件 （binary） 那么加上 -b 就可以啦！ 如果不加任何选项的话，那么就将所有的数据列出来啰！

那么 whereis 到底是使用什么咚咚呢？为何搜寻的速度会比 find 快这么多？ 其实那也没有什么，只是因为 whereis 只找几个特定的目录而已～并没有全系统去查询之故。所以说，whereis 主要是针对 /bin /sbin 下面的可执行文件， 以及 /usr/share/man 下面的 man page 文件，跟几个比较特定的目录来处理而已。所以速度当然快的多！不过，就有某些文件是你找不到的啦！ 想要知道 whereis 到底查了多少目录？可以使用 whereis -l 来确认一下即可！

- locate / updatedb

```
[root@study ~]# locate [-ir] keyword
选项与参数：
-i  ：忽略大小写的差异；
-c  ：不输出文件名，仅计算找到的文件数量
-l  ：仅输出几行的意思，例如输出五行则是 -l 5
-S  ：输出 locate 所使用的数据库文件的相关信息，包括该数据库纪录的文件/目录数量等
-r  ：后面可接正则表达式的显示方式

范例一：找出系统中所有与 passwd 相关的文件名，且只列出 5 个
[root@study ~]# locate -l 5 passwd
/etc/passwd
/etc/passwd-
/etc/pam.d/passwd
/etc/security/opasswd
/usr/bin/gpasswd

范例二：列出 locate 查询所使用的数据库文件之文件名与各数据数量
[root@study ~]# locate -S
Database /var/lib/mlocate/mlocate.db:
        8,086 directories     # 总纪录目录数
        109,605 files         # 总纪录文件数
        5,190,295 Bytes in file names
        2,349,150 Bytes used to store database
```

这个 locate 的使用更简单，直接在后面输入“文件的部分名称”后，就能够得到结果。 举上面的例子来说，我输入 locate passwd ，那么在完整文件名 （包含路径名称） 当中，只要有 passwd 在其中， 就会被显示出来的！这也是个很方便好用的指令，如果你忘记某个文件的完整文件名时～～

但是，这个东西还是有使用上的限制呦！为什么呢？你会发现使用 locate 来寻找数据的时候特别的快， 这是因为 locate 寻找的数据是由“已创建的数据库 /var/lib/mlocate/” 里面的数据所搜寻到的，所以不用直接在去硬盘当中存取数据，呵呵！当然是很快速啰！

那么有什么限制呢？就是因为他是经由数据库来搜寻的，而数据库的创建默认是在每天执行一次 （每个 distribution 都不同，CentOS 7.x 是每天更新数据库一次！），所以当你新创建起来的文件， 却还在数据库更新之前搜寻该文件，那么 locate 会告诉你“找不到！”呵呵！因为必须要更新数据库呀！

那能否手动更新数据库哪？当然可以啊！更新 locate 数据库的方法非常简单，直接输入“ updatedb ”就可以了！ updatedb 指令会去读取 /etc/updatedb.conf 这个配置文件的设置，然后再去硬盘里面进行搜寻文件名的动作， 最后就更新整个数据库文件啰！因为 updatedb 会去搜寻硬盘，所以当你执行 updatedb 时，可能会等待数分钟的时间喔！

- updatedb：根据 /etc/updatedb.conf 的设置去搜寻系统硬盘内的文件名，并更新 /var/lib/mlocate 内的数据库文件；
- locate：依据 /var/lib/mlocate 内的数据库记载，找出使用者输入的关键字文件名。
- find

```
[root@study ~]# find [PATH] [option] [action]
选项与参数：
1\. 与时间有关的选项：共有 -atime, -ctime 与 -mtime ，以 -mtime 说明
   -mtime  n ：n 为数字，意义为在 n 天之前的“一天之内”被更动过内容的文件；
   -mtime +n ：列出在 n 天之前（不含 n 天本身）被更动过内容的文件文件名；
   -mtime -n ：列出在 n 天之内（含 n 天本身）被更动过内容的文件文件名。
   -newer file ：file 为一个存在的文件，列出比 file 还要新的文件文件名

范例一：将过去系统上面 24 小时内有更动过内容 （mtime） 的文件列出
[root@study ~]# find / -mtime 0
# 那个 0 是重点！0 代表目前的时间，所以，从现在开始到 24 小时前，
# 有变动过内容的文件都会被列出来！那如果是三天前的 24 小时内？
# find / -mtime 3 有变动过的文件都被列出的意思！

范例二：寻找 /etc 下面的文件，如果文件日期比 /etc/passwd 新就列出
[root@study ~]# find /etc -newer /etc/passwd
# -newer 用在分辨两个文件之间的新旧关系是很有用的！
```

时间参数真是挺有意思的！我们现在知道 atime, ctime 与 mtime 的意义，如果你想要找出一天内被更动过的文件名称， 可以使用上述范例一的作法。但如果我想要找出“4天内被更动过的文件文件名”呢？那可以使用“ find /var -mtime -4 ”。那如果是“4天前的那一天”就用“ find /var -mtime 4 ”。有没有加上“+, -”差别很大喔！我们可以用简单的图示来说明一下：

![find 相关的时间参数意义](image/find_time.gif)图6.5.1、find 相关的时间参数意义

图中最右边为目前的时间，越往左边则代表越早之前的时间轴啦。由图 6.5.1 我们可以清楚的知道：

- +4代表大于等于5天前的文件名：ex> find /var -mtime +4
- -4代表小于等于4天内的文件文件名：ex> find /var -mtime -4
- 4则是代表4-5那一天的文件文件名：ex> find /var -mtime 4

非常有趣吧！你可以在 /var/ 目录下搜寻一下，感受一下输出文件的差异喔！再来看看其他 find 的用法吧！

```
选项与参数：
2\. 与使用者或群组名称有关的参数：
   -uid n ：n 为数字，这个数字是使用者的帐号 ID，亦即 UID ，这个 UID 是记录在
            /etc/passwd 里面与帐号名称对应的数字。这方面我们会在第四篇介绍。
   -gid n ：n 为数字，这个数字是群组名称的 ID，亦即 GID，这个 GID 记录在
            /etc/group，相关的介绍我们会第四篇说明～
   -user name ：name 为使用者帐号名称喔！例如 dmtsai 
   -group name：name 为群组名称喔，例如 users ；
   -nouser    ：寻找文件的拥有者不存在 /etc/passwd 的人！
   -nogroup   ：寻找文件的拥有群组不存在于 /etc/group 的文件！
                当你自行安装软件时，很可能该软件的属性当中并没有文件拥有者，
                这是可能的！在这个时候，就可以使用 -nouser 与 -nogroup 搜寻。

范例三：搜寻 /home 下面属于 dmtsai 的文件
[root@study ~]# find /home -user dmtsai
# 这个东西也很有用的～当我们要找出任何一个使用者在系统当中的所有文件时，
# 就可以利用这个指令将属于某个使用者的所有文件都找出来喔！

范例四：搜寻系统中不属于任何人的文件
[root@study ~]# find / -nouser
# 通过这个指令，可以轻易的就找出那些不太正常的文件。如果有找到不属于系统任何人的文件时，
# 不要太紧张，那有时候是正常的～尤其是你曾经以源代码自行编译软件时。
```

如果你想要找出某个使用者在系统下面创建了啥咚咚，使用上述的选项与参数，就能够找出来啦！ 至于那个 -nouser 或 -nogroup 的选项功能中，除了你自行由网络上面下载文件时会发生之外， 如果你将系统里面某个帐号删除了，但是该帐号已经在系统内创建很多文件时，就可能会发生无主孤魂的文件存在！ 此时你就得使用这个 -nouser 来找出该类型的文件啰！

```
选项与参数：
3\. 与文件权限及名称有关的参数：
   -name filename：搜寻文件名称为 filename 的文件；
   -size [+-]SIZE：搜寻比 SIZE 还要大（+）或小（-）的文件。这个 SIZE 的规格有：
                   c: 代表 Byte， k: 代表 1024Bytes。所以，要找比 50KB
                   还要大的文件，就是“ -size +50k ”
   -type TYPE    ：搜寻文件的类型为 TYPE 的，类型主要有：一般正规文件 （f）, 设备文件 （b, c）,
                   目录 （d）, 链接文件 （l）, socket （s）, 及 FIFO （p） 等属性。
   -perm mode  ：搜寻文件权限“刚好等于” mode 的文件，这个 mode 为类似 chmod
                 的属性值，举例来说， -rwsr-xr-x 的属性为 4755 ！
   -perm -mode ：搜寻文件权限“必须要全部囊括 mode 的权限”的文件，举例来说，
                 我们要搜寻 -rwxr--r-- ，亦即 0744 的文件，使用 -perm -0744，
                 当一个文件的权限为 -rwsr-xr-x ，亦即 4755 时，也会被列出来，
                 因为 -rwsr-xr-x 的属性已经囊括了 -rwxr--r-- 的属性了。
   -perm /mode ：搜寻文件权限“包含任一 mode 的权限”的文件，举例来说，我们搜寻
                 -rwxr-xr-x ，亦即 -perm /755 时，但一个文件属性为 -rw-------
                 也会被列出来，因为他有 -rw.... 的属性存在！

范例五：找出文件名为 passwd 这个文件
[root@study ~]# find / -name passwd

范例五-1：找出文件名包含了 passwd 这个关键字的文件
[root@study ~]# find / -name "*passwd*"
# 利用这个 -name 可以搜寻文件名啊！默认是完整文件名，如果想要找关键字，
# 可以使用类似 * 的任意字符来处理

范例六：找出 /run 目录下，文件类型为 Socket 的文件名有哪些？
[root@study ~]# find /run -type s
# 这个 -type 的属性也很有帮助喔！尤其是要找出那些怪异的文件，
# 例如 socket 与 FIFO 文件，可以用 find /run -type p 或 -type s 来找！

范例七：搜寻文件当中含有 SGID 或 SUID 或 SBIT 的属性
[root@study ~]# find / -perm /7000 
# 所谓的 7000 就是 ---s--s--t ，那么只要含有 s 或 t 的就列出，所以当然要使用 /7000，
# 使用 -7000 表示要同时含有 ---s--s--t 的所有三个权限。而只需要任意一个，就是 /7000 ～瞭乎？
```

上述范例中比较有趣的就属 -perm 这个选项啦！他的重点在找出特殊权限的文件啰！ 我们知道 SUID 与 SGID 都可以设置在二进制程序上，假设我想要找出来 /usr/bin, /usr/sbin 这两个目录下， 只要具有 SUID 或 SGID 就列出来该文件，你可以这样做：

```
[root@study ~]# find /usr/bin /usr/sbin -perm /6000
```

因为 SUID 是 4 分，SGID 2 分，总共为 6 分，因此可用 /6000 来处理这个权限！ 至于 find 后面可以接多个目录来进行搜寻！另外， find 本来就会搜寻次目录，这个特色也要特别注意喔！ 最后，我们再来看一下 find 还有什么特殊功能吧！

```
选项与参数：
4\. 额外可进行的动作：
   -exec command ：command 为其他指令，-exec 后面可再接额外的指令来处理搜寻到的结果。
   -print        ：将结果打印到屏幕上，这个动作是默认动作！

范例八：将上个范例找到的文件使用 ls -l 列出来～
[root@study ~]# find /usr/bin /usr/sbin -perm /7000 -exec ls -l {} \;
# 注意到，那个 -exec 后面的 ls -l 就是额外的指令，指令不支持命令别名，
# 所以仅能使用 ls -l 不可以使用 ll 喔！注意注意！

范例九：找出系统中，大于 1MB 的文件
[root@study ~]# find / -size +1M
```

find 的特殊功能就是能够进行额外的动作（action）。我们将范例八的例子以图解来说明如下：

![find 相关的额外动作](image/centos7_find_exec.gif)图6.5.2、find 相关的额外动作

该范例中特殊的地方有 {} 以及 \; 还有 -exec 这个关键字，这些东西的意义为：

- {} 代表的是“由 find 找到的内容”，如上图所示，find 的结果会被放置到 {} 位置中；
- -exec 一直到 \; 是关键字，代表 find 额外动作的开始 （-exec） 到结束 （\;） ，在这中间的就是 find 指令内的额外动作。 在本例中就是“ ls -l {} ”啰！
- 因为“ ; ”在 bash 环境下是有特殊意义的，因此利用反斜线来跳脱。

通过图 6.5.2 你应该就比较容易了解 -exec 到 \; 之间的意义了吧！

如果你要找的文件是具有特殊属性的，例如 SUID 、文件拥有者、文件大小等等， 那么利用 locate 是没有办法达成你的搜寻的！此时 find 就显的很重要啦！ 另外，find 还可以利用万用字符来找寻文件名呢！举例来说，你想要找出 /etc 下面文件名包含 httpd 的文件， 那么你就可以这样做：

```
[root@study ~]# find /etc -name '*httpd*'
```

不但可以指定搜寻的目录（连同次目录），并且可以利用额外的选项与参数来找到最正确的文件名！真是好好用！ 不过由于 find 在寻找数据的时后相当的操硬盘！所以没事情不要使用 find 啦！有更棒的指令可以取代呦！那就是上面提到的 [whereis](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#whereis) 与 [locate](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#locate) 啰！

### 总结

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