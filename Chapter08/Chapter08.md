# 第八章、文件与文件系统的压缩,打包与备份

在 Linux 下面有相当多的压缩指令可以运行喔！这些压缩指令可以让我们更方便从网络上面下载容量较大的文件呢！ 此外，我们知道在 Linux 下面的扩展名是没有什么很特殊的意义的，不过，针对这些压缩指令所做出来的压缩文件， 为了方便记忆，还是会有一些特殊的命名方式啦！就让我们来看看吧！

### 8.1 压缩文件的用途与技术总结

在本节中，我们探讨了文件压缩技术的用途、基本原理以及带来的好处。以下是关键点的回顾：

#### 压缩文件的用途

1. **减少文件大小**：
   - 便于通过电子邮件发送大文件。
   - 适合于容量受限的存储介质（如CD或DVD）。
   - 节省磁盘空间，用于备份大量数据。
2. **便于文件传输**：
   - 通过减少数据量，提高网络传输效率。
   - 压缩后的文件在网络传输中占用更少带宽，提高网站的响应速度。

#### 文件压缩的技术原理

1. **位填充压缩**：
   - 利用计算机文件中的空闲空间，通过复杂计算方式将未使用的位压缩掉，从而减少文件大小。
2. **重复数据压缩**：
   - 对文件中的重复数据进行统计记录，例如将连续的100个“1”记录为“100个1”。

举例：

**Tips** 由于 1 Byte = 8 bits ，所以每个 Byte 当中会有 8 个空格，而每个空格可以是 0, 1 ，这里仅是做为一个约略的介绍， 更多的详细数据请参考[第零章的计算机概论](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)吧！

由于我们记录数字是 1 ，考虑计算机所谓的二进制喔，如此一来， 1 会在最右边占据 1 个 bit ，而其他的 7 个 bits 将会自动的被填上 0 啰！你看看，其实在这样的例子中，那 7 个 bits 应该是“空的”才对！不过，**为了要满足目前我们的操作系统数据的存取，所以就会将该数据转为 Byte 的型态来记录了！而一些聪明的计算机工程师就利用一些复杂的计算方式， 将这些没有使用到的空间“丢”出来，以让文件占用的空间变小！这就是压缩的技术啦！**

另外一种压缩技术也很有趣，他是将重复的数据进行统计记录的。**举例来说，如果你的数据为“111....”共有100个1时， 那么压缩技术会记录为“100个1”而不是真的有100个1的位存在**！这样也能够精简文件记录的容量呢！ 非常有趣吧！

简单的说，你可以将他想成，其实文件里面有相当多的“空间”存在，并不是完全填满的， 而“压缩”的技术就是将这些“空间”填满，以让整个文件占用的容量下降！ 不过，这些“压缩过的文件”并无法直接被我们的操作系统所使用的，因此， 若要使用这些被压缩过的文件数据，则必须将他“还原”回来未压缩前的模样， 那就是所谓的“解压缩”啰！而**至于压缩后与压缩的文件所占用的磁盘空间大小， 就可以被称为是“压缩比”啰**！更多的技术文件或许你可以参考一下：

- [RFC 1952 文件：http://www.ietf.org/rfc/rfc1952.txt](http://www.ietf.org/rfc/rfc1952.txt)
- 鸟哥站上的备份：[http://linux.vbird.org/linux_basic/0240tarcompress/0240tarcompress_gzip.php](http://linux.vbird.org/linux_basic/0240tarcompress//0240tarcompress_gzip.php)

#### 文件压缩的好处

1. **节省存储空间**：
   - 压缩后的文件占用更少的磁盘空间，从而可以存储更多的数据。
2. **提高传输效率**：
   - 压缩后的数据在网络上传输时，占用带宽更少，使得传输速度更快。
3. **应用于网站优化**：
   - 网站的数据传输可以通过压缩技术进行优化，提高带宽利用率和页面加载速度。

文件压缩技术通过有效地减少文件的实际存储空间和传输数据量，为我们日常的数据管理和传输带来了极大的便利。了解这些基本概念后，可以更好地应用于实际操作中，提高工作效率。

**原文：**

这个“压缩”与“解压缩”的动作有什么好处呢？最大的好处就是压缩过的文件大小变小了， 所以你的硬盘容量无形之中就可以容纳更多的数据。此外，在一些网络数据的传输中，也会由于数据量的降低， 好让网络带宽可以用来作更多的工作！而不是老是卡在一些大型的文件传输上面呢！目前很多的 WWW 网站也是利用文件压缩的技术来进行数据的传送，好让网站带宽的可利用率上升喔！

**Tips** 上述的WWW网站压缩技术蛮有趣的！他让你网站上面“看的到的数据”在经过网络传输时，使用的是“压缩过的数据”， 等到这些压缩过的数据到达你的计算机主机时，再进行解压缩，由于目前的计算机运算速度相当的快速， 因此其实在网页浏览的时候，时间都是花在“数据的传输”上面，而不是 CPU 的运算啦！如此一来，由于压缩过的数据量降低了，自然传送的速度就会增快不少！



### 8.2 Linux 系统常见的压缩指令总结

在本节中，我们详细探讨了Linux系统中的几种常见压缩指令，包括它们的用途、基本用法和优缺点。以下是对各个压缩指令的详细总结。

#### 常见压缩文件的扩展名

- **.Z**：由 compress 程序压缩的文件。

- **.zip**：由 zip 程序压缩的文件。

- **.gz**：由 gzip 程序压缩的文件。

- **.bz2**：由 bzip2 程序压缩的文件。

- **.xz**：由 xz 程序压缩的文件。

- **.tar**：由 tar 程序打包的文件（未压缩）。

- **.tar.gz**：由 tar 程序打包并经过 gzip 压缩的文件。

- **.tar.bz2**：由 tar 程序打包并经过 bzip2 压缩的文件。

- **.tar.xz**：由 tar 程序打包并经过 xz 压缩的文件。

  Linux上常见的压缩指令就是 gzip, bzip2 以及最新的 xz ，至于 compress 已经退流行了。

  为了支持 windows 常见的 zip，其实 Linux 也早就有 zip 指令了！ gzip 是由 [GNU 计划](http://www.gnu.org/)所开发出来的压缩指令，该指令已经取代了 compress 。 后来 GNU 又开发出 bzip2 及 xz 这几个压缩比更好的压缩指令！不过，这些指令通常仅能针对一个文件来压缩与解压缩，如此一来， 每次压缩与解压缩都要一大堆文件，岂不烦人？此时，那个所谓的“打包软件, tar”就显的很重要啦！

  这个 tar 可以将很多文件“打包”成为一个文件！甚至是目录也可以这么玩。不过，单纯的 tar 功能仅是“打包”而已，亦即是将很多文件集结成为一个文件， 事实上，他并没有提供压缩的功能，后来，[GNU 计划](http://www.gnu.org/)中，将整个 tar 与压缩的功能结合在一起，如此一来提供使用者更方便并且更强大的压缩与打包功能！ 

  下面我们就来谈一谈这些在 Linux 下面基本的压缩指令吧！

#### 8.2.1 gzip, zcat/zmore/zless/zgrep

- **用途**：gzip 是最广泛使用的压缩指令，可以解压缩 compress、zip 和 gzip 创建的文件。

- 基本用法：

  - `gzip [-cdtv#] 文件名`：压缩文件。
  - `zcat 文件名.gz`：查看压缩文件内容。
  - `gzip -d 文件名.gz`：解压缩文件。
  
- **优点**：压缩速度快，支持解压缩多种格式。

- **缺点**：压缩比不如 bzip2 和 xz。

- 示例：

  - 压缩文件：`gzip -v services`
  - 解压缩文件：`gzip -d services.gz`
  - 查看压缩文件内容：`zcat services.gz`
  
  ```
  [dmtsai@study ~]$ gzip [-cdtv#] 文件名
  [dmtsai@study ~]$ zcat 文件名.gz
  选项与参数：
  -c  ：将压缩的数据输出到屏幕上，可通过数据流重导向来处理；
  -d  ：解压缩的参数；
  -t  ：可以用来检验一个压缩文件的一致性～看看文件有无错误；
  -v  ：可以显示出原文件/压缩文件的压缩比等信息；
  -#  ：# 为数字的意思，代表压缩等级，-1 最快，但是压缩比最差、-9 最慢，但是压缩比最好！默认是 -6
  
  范例一：找出 /etc 下面 （不含子目录） 容量最大的文件，并将它复制到 /tmp ，然后以 gzip 压缩
  [dmtsai@study ~]$ ls -ldSr /etc/*   # 忘记选项意义？请自行 man 啰！
  .....（前面省略）.....
  -rw-r--r--.  1 root root    25213 Jun 10  2014 /etc/dnsmasq.conf
  -rw-r--r--.  1 root root    69768 May  4 17:55 /etc/ld.so.cache
  -rw-r--r--.  1 root root   670293 Jun  7  2013 /etc/services
  
  [dmtsai@study ~]$ cd /tmp 
  [dmtsai@study tmp]$ cp /etc/services .
  [dmtsai@study tmp]$ gzip -v services
  services:        79.7% -- replaced with services.gz
  [dmtsai@study tmp]$ ll /etc/services /tmp/services*
  -rw-r--r--. 1 root   root   670293 Jun  7  2013 /etc/services
  -rw-r--r--. 1 dmtsai dmtsai 136088 Jun 30 18:40 /tmp/services.gz
  ```
  
  **当你使用 gzip 进行压缩时，在默认的状态下原本的文件会被压缩成为 .gz 的文件名，原始文件就不再存在了**。 这点与一般习惯使用 windows 做压缩的朋友所熟悉的情况不同喔！要注意！要注意！ 此外，使用 gzip 压缩的文件在 Windows 系统中，竟然可以被 WinRAR/7zip 这个软件解压缩呢！很好用吧！至于其他的用法如下：
  
  ```
  范例二：由于 services 是文本文件，请将范例一的压缩文件的内容读出来！
  [dmtsai@study tmp]$ zcat services.gz
  # 由于 services 这个原本的文件是是文本文件，因此我们可以尝试使用 zcat/zmore/zless 去读取！
  # 此时屏幕上会显示 servcies.gz 解压缩之后的原始文件内容！
  
  范例三：将范例一的文件解压缩
  [dmtsai@study tmp]$ gzip -d services.gz
  # 鸟哥不要使用 gunzip 这个指令，不好背！使用 gzip -d 来进行解压缩！
  # 与 gzip 相反， gzip -d 会将原本的 .gz 删除，回复到原本的 services 文件。
  
  范例四：将范例三解开的 services 用最佳的压缩比压缩，并保留原本的文件
  [dmtsai@study tmp]$ gzip -9 -c services &gt; services.gz
  
  范例五：由范例四再次创建的 services.gz 中，找出 http 这个关键字在哪几行？
  [dmtsai@study tmp]$ zgrep -n 'http' services.gz
  14:#       http://www.iana.org/assignments/port-numbers
  89:http            80/tcp          www www-http    # WorldWideWeb HTTP
  90:http            80/udp          www www-http    # HyperText Transfer Protocol
  .....（下面省略）.....
  ```
  
  其实 gzip 的压缩已经最优化过了，所以虽然 gzip 提供 1~9 的压缩等级，不过使用默认的 6 就非常好用了！ 因此上述的范例四可以不要加入那个 -9 的选项。范例四的重点在那个 -c 与 > 的使用啰！-c 可以将原本要转成压缩文件的数据内容，将它变成文字类型从屏幕输出， 然后我们可以通过大于 （>） 这个符号，将原本应该由屏幕输出的数据，转成输出到文件而不是屏幕，所以就能够创建出压缩挡了。只是文件名也要自己写， 当然最好还是遵循 gzip 的压缩文件名要求较佳喔！！更多的 > 这个符号的应用，我们会在 bash 章节再次提及！
  
  cat/more/less 可以使用不同的方式来读取纯文本文件，那个 zcat/zmore/zless 则可以对应于 cat/more/less 的方式来读取纯文本文件被压缩后的压缩文件！ 由于 gzip 这个压缩指令主要想要用来取代 compress 的，所以不但 compress 的压缩文件可以使用 gzip 来解开，同时 zcat 这个指令可以同时读取 compress 与 gzip 的压缩文件呦！
  
  另外，如果你还想要从文字压缩文件当中找数据的话，可以通过 egrep 来搜寻关键字喔！而不需要将压缩文件解开才以 grep 进行！ 这对查询备份中的文本文件数据相当有用！

#### 8.2.2 bzip2, bzcat/bzmore/bzless/bzgrep

- **用途**：bzip2 提供比 gzip 更高的压缩比。
- 基本用法：

  - `bzip2 [-cdkzv#] 文件名`：压缩文件。
  - `bzcat 文件名.bz2`：查看压缩文件内容。
  - `bzip2 -d 文件名.bz2`：解压缩文件。
- **优点**：压缩比优于 gzip。
- **缺点**：压缩和解压缩速度较慢。
- 示例：

  - 压缩文件：`bzip2 -v services`
  - 解压缩文件：`bzip2 -d services.bz2`
  - 查看压缩文件内容：`bzcat services.bz2`

```
[dmtsai@study ~]$ bzip2 [-cdkzv#] 文件名
[dmtsai@study ~]$ bzcat 文件名.bz2
选项与参数：
-c  ：将压缩的过程产生的数据输出到屏幕上！
-d  ：解压缩的参数
-k  ：保留原始文件，而不会删除原始的文件喔！
-z  ：压缩的参数 （默认值，可以不加）
-v  ：可以显示出原文件/压缩文件的压缩比等信息；
-#  ：与 gzip 同样的，都是在计算压缩比的参数， -9 最佳， -1 最快！

范例一：将刚刚 gzip 范例留下来的 /tmp/services 以 bzip2 压缩
[dmtsai@study tmp]$ bzip2 -v services
  services:  5.409:1,  1.479 bits/Byte, 81.51% saved, 670293 in, 123932 out.
[dmtsai@study tmp]$ ls -l services*
-rw-r--r--. 1 dmtsai dmtsai 123932 Jun 30 18:40 services.bz2
-rw-rw-r--. 1 dmtsai dmtsai 135489 Jun 30 18:46 services.gz
# 此时 services 会变成 services.bz2 之外，你也可以发现 bzip2 的压缩比要较 gzip 好喔！！
# 压缩率由 gzip 的 79% 提升到 bzip2 的 81% 哩！

范例二：将范例一的文件内容读出来！
[dmtsai@study tmp]$ bzcat services.bz2

范例三：将范例一的文件解压缩
[dmtsai@study tmp]$ bzip2 -d services.bz2

范例四：将范例三解开的 services 用最佳的压缩比压缩，并保留原本的文件
[dmtsai@study tmp]$ bzip2 -9 -c services &gt; services.bz2
```

看上面的范例，你会发现到 bzip2 连选项与参数都跟 gzip 一模一样！只是扩展名由 .gz 变成 .bz2 而已！其他的用法都大同小异，所以鸟哥就不一一介绍了！ 你也可以发现到 bzip2 的压缩率确实比 gzip 要好些！不过，对于大容量文件来说，bzip2 压缩时间会花比较久喔！至少比 gzip 要久的多！ 这没办法～要有更多可用容量，就得要花费相对应的时间！



#### 8.2.3 xz, xzcat/xzmore/xzless/xzgrep

- **用途**：xz 提供更高的压缩比，适用于需要最大化压缩的场景。
- 基本用法：

  - `xz [-dtlkc#] 文件名`：压缩文件。
  - `xzcat 文件名.xz`：查看压缩文件内容。
  - `xz -d 文件名.xz`：解压缩文件。
- **优点**：压缩比最高，适合大文件压缩。
- **缺点**：压缩和解压缩速度最慢。
- 示例：

  - 压缩文件：`xz -v services`
  - 解压缩文件：`xz -d services.xz`
  - 查看压缩文件内容：`xzcat services.xz`

```
[dmtsai@study ~]$ xz [-dtlkc#] 文件名
[dmtsai@study ~]$ xcat 文件名.xz
选项与参数：
-d  ：就是解压缩啊！
-t  ：测试压缩文件的完整性，看有没有错误
-l  ：列出压缩文件的相关信息
-k  ：保留原本的文件不删除～
-c  ：同样的，就是将数据由屏幕上输出的意思！
-#  ：同样的，也有较佳的压缩比的意思！

范例一：将刚刚由 bzip2 所遗留下来的 /tmp/services 通过 xz 来压缩！
[dmtsai@study tmp]$ xz -v services
services （1/1）
  100 %        97.3 KiB / 654.6 KiB = 0.149

[dmtsai@study tmp]$ ls -l services*
-rw-rw-r--. 1 dmtsai dmtsai 123932 Jun 30 19:09 services.bz2
-rw-rw-r--. 1 dmtsai dmtsai 135489 Jun 30 18:46 services.gz
-rw-r--r--. 1 dmtsai dmtsai  99608 Jun 30 18:40 services.xz
# 各位观众！看到没有啊！！容量又进一步下降的更多耶！好棒的压缩比！

范例二：列出这个压缩文件的信息，然后读出这个压缩文件的内容
[dmtsai@study tmp]$ xz -l services.xz
Strms  Blocks   Compressed Uncompressed  Ratio  Check   Filename
    1       1     97.3 KiB    654.6 KiB  0.149  CRC64   services.xz
# 竟然可以列出这个文件的压缩前后的容量，真是太人性化了！这样观察就方便多了！

[dmtsai@study tmp]$ xzcat services.xz

范例三：将他解压缩吧！
[dmtsai@study tmp]$ xz -d services.xz

范例四：保留原文件的文件名，并且创建压缩文件！
[dmtsai@study tmp]$ xz -k services
```

虽然 xz 这个压缩比真的好太多太多了！以鸟哥选择的这个 services 文件为范例，他可以将 gzip 压缩比 （压缩后/压缩前） 的 21% 更进一步优化到 15% 耶！ 差非常非常多！不过， xz 最大的问题是...时间花太久了！如果你曾经使用过 xz 的话，应该会有发现，他的运算时间真的比 gzip 久很多喔！



#### tar 指令及其应用

- **用途**：tar 主要用于打包多个文件或目录，常与 gzip、bzip2 和 xz 结合使用。

- 基本用法：

  - 打包并压缩：`tar -zcvf archive.tar.gz 文件/目录`
  - 解压缩并解包：`tar -zxvf archive.tar.gz`
  - 查看压缩包内容：`tar -ztvf archive.tar.gz`
  - 指定解压目录：`tar -zxvf archive.tar.gz -C 目标目录`
  
- 示例：

  - 打包并压缩：`tar -jcv -f /root/etc.tar.bz2 /etc`
  - 解压缩并解包：`tar -jxv -f /root/etc.tar.bz2 -C /tmp`
  - 仅解压特定文件：`tar -jxv -f /root/etc.tar.bz2 etc/shadow`
  - 打包时排除特定文件：`tar -jcv -f /root/system.tar.bz2 --exclude=/root/etc* --exclude=/root/system.tar.bz2 /etc /root`

#### 其他注意事项

- **SELinux 问题**：在解压缩系统文件时，可能需要修复 SELinux 标签，可以使用 `restorecon -Rv /etc` 或创建 `/.autorelabel` 文件后重启系统。
- **时间成本**：不同压缩工具的压缩比和压缩时间有所不同，需要根据具体情况选择合适的工具。

通过了解这些压缩指令的用途和基本用法，可以根据具体需求选择合适的压缩工具，提高工作效率和数据管理能力。

### 8.3 打包指令： tar

在 Linux 中，gzip、bzip2、xz 等指令通常只能针对单个文件进行压缩，虽然它们也能压缩目录，但指的是“将目录内的所有文件分别进行压缩”的动作。tar（Tape Archive）可以将多个文件或目录打包成一个大文件，并通过 gzip、bzip2 或 xz 进行压缩。这种功能在需要将多个文件和目录打包成一个压缩包时非常有用。

### 8.3.1 tar

#### tar 的选项与参数

- **基本语法**：
  - 打包并压缩：`tar [-z|-j|-J] [cv] [-f 新文件名] 文件或目录`
  - 查看内容：`tar [-z|-j|-J] [tv] [-f 现有的 tar 文件名]`
  - 解压并解包：`tar [-z|-j|-J] [xv] [-f 现有的 tar 文件名] [-C 目录]`
- **常用选项**：
  - `-c`：创建新的打包文件。
  - `-t`：查看打包文件的内容。
  - `-x`：解打包或解压缩。
  - `-z`：通过 gzip 支持进行压缩/解压缩。
  - `-j`：通过 bzip2 支持进行压缩/解压缩。
  - `-J`：通过 xz 支持进行压缩/解压缩。
  - `-v`：显示详细信息。
  - `-f`：指定打包文件名。
  - `-C`：指定解包目录。
  - `-p`：保留原文件的权限和属性。
  - `-P`：保留绝对路径。
  - `--exclude`：排除指定的文件或目录。

```
[dmtsai@study ~]$ tar [-z&#124;-j&#124;-J] [cv] [-f 待创建的新文件名] filename... &lt;==打包与压缩
[dmtsai@study ~]$ tar [-z&#124;-j&#124;-J] [tv] [-f 既有的 tar文件名]             &lt;==察看文件名
[dmtsai@study ~]$ tar [-z&#124;-j&#124;-J] [xv] [-f 既有的 tar文件名] [-C 目录]   &lt;==解压缩
选项与参数：
-c  ：创建打包文件，可搭配 -v 来察看过程中被打包的文件名（filename）
-t  ：察看打包文件的内容含有哪些文件名，重点在察看“文件名”就是了；
-x  ：解打包或解压缩的功能，可以搭配 -C （大写） 在特定目录解开
      特别留意的是， -c, -t, -x 不可同时出现在一串命令行中。
-z  ：通过 gzip  的支持进行压缩/解压缩：此时文件名最好为 *.tar.gz
-j  ：通过 bzip2 的支持进行压缩/解压缩：此时文件名最好为 *.tar.bz2
-J  ：通过 xz    的支持进行压缩/解压缩：此时文件名最好为 *.tar.xz
      特别留意， -z, -j, -J 不可以同时出现在一串命令行中
-v  ：在压缩/解压缩的过程中，将正在处理的文件名显示出来！
-f filename：-f 后面要立刻接要被处理的文件名！建议 -f 单独写一个选项啰！（比较不会忘记）
-C 目录    ：这个选项用在解压缩，若要在特定目录解压缩，可以使用这个选项。

其他后续练习会使用到的选项介绍：
-p（小写） ：保留备份数据的原本权限与属性，常用于备份（-c）重要的配置文件
-P（大写） ：保留绝对路径，亦即允许备份数据中含有根目录存在之意；
--exclude=FILE：在压缩的过程中，不要将 FILE 打包！
```

其实最简单的使用 tar 就只要记忆下面的方式即可：

- 压　缩：tar -j<u>c</u>v -f filename.tar.bz2 要被压缩的文件或目录名称
- 查　询：tar -j<u>t</u>v -f filename.tar.bz2
- 解压缩：tar -j<u>x</u>v -f filename.tar.bz2 -C 欲解压缩的目录

那个 filename.tar.bz2 是我们自己取的文件名，tar 并不会主动的产生创建的文件名喔！我们要自订啦！ 所以扩展名就显的很重要了！如果不加 [-z|-j|-J] 的话，文件名最好取为 *.tar 即可。如果是 -j 选项，代表有 bzip2 的支持，因此文件名最好就取为* .tar.bz2 ，因为 bzip2 会产生 .bz2 的扩展名之故！ 至于如果是加上了 -z 的 gzip 的支持，那文件名最好取为 *.tar.gz 喔！了解乎？

另外，由于“ -f filename ”是紧接在一起的，过去很多文章常会写成“-jcvf filename”，这样是对的， 但由于选项的顺序理论上是可以变换的，所以很多读者会误认为“-jvfc filename”也可以～事实上这样会导致产生的文件名变成 c ！ 因为 -fc 嘛！所以啰，建议您在学习 tar 时，将“ -f filename ”与其他选项独立出来，会比较不容易发生问题。

### 示例操作

#### 示例 1：使用 tar 加入 -z, -j 或 -J 的参数备份 /etc/ 目录

```
[dmtsai@study ~]$ su -  # 因为备份 /etc 需要 root 的权限，否则会出现一堆错误
[root@study ~]# time tar -zpcv -f /root/etc.tar.gz /etc
tar: Removing leading `/' from member names  &lt;==注意这个警告讯息
/etc/
....（中间省略）....
/etc/hostname
/etc/aliases.db

real    0m0.799s   # 多了 time 会显示程序运行的时间！看 real 就好了！花去了 0.799s
user    0m0.767s
sys     0m0.046s
# 由于加上 -v 这个选项，因此正在作用中的文件名就会显示在屏幕上。
# 如果你可以翻到第一页，会发现出现上面的错误讯息！下面会讲解。
# 至于 -p 的选项，重点在于“保留原本文件的权限与属性”之意。

[root@study ~]# time tar -jpcv -f /root/etc.tar.bz2 /etc
....（前面省略）....
real    0m1.913s
user    0m1.881s
sys     0m0.038s
[root@study ~]# time tar -Jpcv -f /root/etc.tar.xz  /etc
....（前面省略）....
real    0m9.023s
user    0m8.984s
sys     0m0.086s
# 显示的讯息会跟上面一模一样啰！不过时间会花比较多！使用了 -J 时，会花更多时间

[root@study ~]# ll /root/etc*
-rw-r--r--. 1 root root 6721809 Jul  1 00:16 /root/etc.tar.bz2
-rw-r--r--. 1 root root 7758826 Jul  1 00:14 /root/etc.tar.gz
-rw-r--r--. 1 root root 5511500 Jul  1 00:16 /root/etc.tar.xz
[root@study ~]# du -sm /etc
28     /etc  # 实际目录约占有 28MB 的意思！
```

压缩比越好当然要花费的运算时间越多！我们从上面可以看到，虽然使用 gzip 的速度相当快，总时间花费不到 1 秒钟，但是压缩率最糟糕！ 如果使用 xz 的话，虽然压缩比最佳！不过竟然花了 9 秒钟的时间耶！这还仅是备份 28MBytes 的 /etc 而已，如果备份的数据是很大容量的， 那你真的要考虑时间成本才行！

至于加上“ -p ”这个选项的原因是为了保存原本文件的权限与属性！我们曾在[第六章的 cp 指令介绍](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#cp)时谈到权限与文件类型（例如链接文件）对复制的不同影响。 同样的，在备份重要的系统数据时，这些原本文件的权限需要做完整的备份比较好。此时 -p 这个选项就派的上用场了。 



#### 示例 2：查阅 tar 文件的数据内容 （可察看文件名），与备份文件名有否根目录的意义

```
[root@study ~]# tar -jtv -f /root/etc.tar.bz2
....（前面省略）....
-rw-r--r-- root/root       131 2015-05-25 17:48 etc/locale.conf
-rw-r--r-- root/root        19 2015-05-04 17:56 etc/hostname
-rw-r--r-- root/root     12288 2015-05-04 17:59 etc/aliases.db
```

如果加上 -v 这个选项时，详细的文件权限/属性都会被列出来！如果只是想要知道文件名而已， 那么就将 -v 拿掉即可。**从上面的数据我们可以发现一件很有趣的事情，那就是每个文件名都没了根目录了！**这也是上一个练习中出现的那个警告讯息“tar: Removing leading `/' from member names（移除了文件名开头的`/' ）”所告知的情况！

那为什么要拿掉根目录呢？**主要是为了安全！**我们使用 tar 备份的数据可能会需要解压缩回来使用， 在 tar 所记录的文件名 （就是我们刚刚使用 tar -jtvf 所察看到的文件名） 那就是解压缩后的实际文件名。 如果拿掉了根目录，假设你将备份数据在 /tmp 解开，那么解压缩的文件名就会变成“/tmp/<u>etc/xxx</u>”。 但“如果没有拿掉根目录，解压缩后的文件名就会是绝对路径， 亦即解压缩后的数据一定会被放置到 <u>/etc/xxx</u> 去！”如此一来，你的原本的 /etc/ 下面的数据， 就会被备份数据所覆盖过去了！



如果你确定你就是需要备份根目录到 tar 的文件中，那可以使用 -P （大写） 这个选项，请看下面的例子分析：

```
范例：将文件名中的（根）目录也备份下来，并察看一下备份文件的内容文件名
[root@study ~]# tar -jp&lt;u&gt;P&lt;/u&gt;cv -f /root/etc.and.root.tar.bz2 /etc

[root@study ~]# tar -jtf /root/etc.and.root.tar.bz2
/etc/locale.conf
/etc/hostname
/etc/aliases.db
# 这次查阅文件名不含 -v 选项，所以仅有文件名而已！没有详细属性/权限等参数。
```

有发现不同点了吧？如果加上 -P 选项，那么文件名内的根目录就会存在喔！不过，鸟哥个人建议，还是不要加上 -P 这个选项来备份！ 毕竟很多时候，我们备份是为了要未来追踪问题用的，倒不一定需要还原回原本的系统中！ 所以拿掉根目录后，备份数据的应用会比较有弹性！也比较安全呢！

#### 示例 3：将 tar 文件解压缩到指定目录

```
sh复制代码[root@study ~]# tar -jxv -f /root/etc.tar.bz2 -C /tmp
[root@study ~]# ll /tmp
drwxr-xr-x. 131 root root 8192 Jun 26 22:14 etc
```



#### 示例 4：仅解压单一文件

```
sh复制代码[root@study ~]# tar -jtv -f /root/etc.tar.bz2 | grep 'shadow'
---------- root/root       721 2015-06-17 00:20 etc/gshadow
---------- root/root      1183 2015-06-17 00:20 etc/shadow-
---------- root/root      1210 2015-06-17 00:20 etc/shadow
---------- root/root       707 2015-06-17 00:20 etc/gshadow-

[root@study ~]# tar -jxv -f /root/etc.tar.bz2 etc/shadow
etc/shadow
[root@study ~]# ll etc
total 4
----------. 1 root root 1210 Jun 17 00:20 shadow
```



#### 示例 5：打包目录但不包含某些文件

```
sh
复制代码
[root@study ~]# tar -jcv -f /root/system.tar.bz2 --exclude=/root/etc* --exclude=/root/system.tar.bz2 /etc /root
```

#### 示例 6：仅备份比某个时刻还要新的文件

```
sh复制代码[root@study ~]# find /etc -newer /etc/passwd
-rw-r--r--. 1 root root 2092 Jun 17 00:20 /etc/passwd

[root@study ~]# tar -jcv -f /root/etc.newer.tar.bz2 --newer-mtime="2015/06/17" /etc/*
tar: Removing leading `/' from member names
/etc/abrt/
/etc/alsa/
/etc/yum.repos.d/
tar: /etc/yum.repos.d/CentOS-fasttrack.repo: file is unchanged; not dumped

[root@study ~]# tar -jtv -f /root/etc.newer.tar.bz2 | grep -v '/$'
```

**原文：**

- 将备份的数据解压缩，并考虑特定目录的解压缩动作 （-C 选项的应用）

那如果想要解打包呢？很简单的动作就是直接进行解打包嘛！

```
[root@study ~]# tar -jxv -f /root/etc.tar.bz2
[root@study ~]# ll
....（前面省略）....
drwxr-xr-x. 131 root root    8192 Jun 26 22:14 etc
....（后面省略）....
```

此时该打包文件会在“本目录下进行解压缩”的动作！ 所以，你等一下就会在主文件夹下面发现一个名为 etc 的目录啰！所以啰，如果你想要将该文件在 /tmp 下面解开， 可以 cd /tmp 后，再下达上述的指令即可。不过，这样好像很麻烦呢～有没有更简单的方法可以“指定欲解开的目录”呢？ 有的，可以使用 -C 这个选项喔！举例来说：

```
[root@study ~]# tar -jxv -f /root/etc.tar.bz2 -C /tmp
[root@study ~]# ll /tmp
....（前面省略）....
drwxr-xr-x. 131 root   root     8192 Jun 26 22:14 etc
....（后面省略）....
```

这样一来，你就能够将该文件在不同的目录解开啰！鸟哥个人是认为，这个 -C 的选项务必要记忆一下的！ 好了，处理完毕后，请记得将这两个目录删除一下呢！

```
[root@study ~]# rm -rf /root/etc /tmp/etc
```

再次强调，这个“ rm -rf ”是很危险的指令！下达时请务必要确认一下后面接的文件名。我们要删除的是 /root/etc 与 /tmp/etc， 您可不要将 /etc/ 删除掉了！系统会死掉的～ ^_^

- 仅解开单一文件的方法

刚刚上头我们解压缩都是将整个打包文件的内容全部解开！想像一个情况，如果我只想要解开打包文件内的其中一个文件而已， 那该如何做呢？很简单的，你只要使用 -jtv 找到你要的文件名，然后将该文件名解开即可。 我们用下面的例子来说明一下：

```
# 1\. 先找到我们要的文件名，假设解开 shadow 文件好了：
[root@study ~]# tar -jtv -f /root/etc.tar.bz2 &#124; grep 'shadow'
---------- root/root       721 2015-06-17 00:20 etc/gshadow
---------- root/root      1183 2015-06-17 00:20 etc/shadow-
---------- root/root      1210 2015-06-17 00:20 etc/shadow  &lt;==这是我们要的！
---------- root/root       707 2015-06-17 00:20 etc/gshadow-
# 先搜寻重要的文件名！其中那个 grep 是“撷取”关键字的功能！我们会在第三篇说明！
# 这里您先有个概念即可！那个管线 &#124; 配合 grep 可以撷取关键字的意思！

# 2\. 将该文件解开！语法与实际作法如下：
[root@study ~]# tar -jxv -f 打包档.tar.bz2 待解开文件名
[root@study ~]# tar -jxv -f /root/etc.tar.bz2 etc/shadow
etc/shadow
[root@study ~]# ll etc
total 4
----------. 1 root root 1210 Jun 17 00:20 shadow
# 很有趣！此时只会解开一个文件而已！不过，重点是那个文件名！你要找到正确的文件名。
# 在本例中，你不能写成 /etc/shadow ！因为记录在 etc.tar.bz2 内的并没有 / 之故！
```



**Tips** 在这个练习之前，你可能要先将前面练习所产生的 /root/etc 删除才行！不然 /root/etc/shadow 会重复存在，而其他的前面实验的文件也会存在， 那就看不出什么鬼～

- 打包某目录，但不含该目录下的某些文件之作法

假设我们想要打包 /etc/ /root 这几个重要的目录，但却不想要打包 /root/etc* 开头的文件，因为该文件都是刚刚我们才创建的备份文件嘛！ 而且假设这个新的打包文件要放置成为 /root/system.tar.bz2 ，当然这个文件自己不要打包自己 （因为这个文件放置在 /root 下面啊！），此时我们可以通过 --exclude 的帮忙！ 那个 exclude 就是不包含的意思！所以你可以这样做：

```
[root@study ~]# tar -jcv  -f /root/system.tar.bz2 --exclude=/root/etc* \
&gt; --exclude=/root/system.tar.bz2  /etc /root
```

上面的指令是一整列的～其实你可以打成：“tar -jcv -f /root/system.tar.bz2 --exclude=/root/etc* --exclude=/root/system.tar.bz2 /etc /root”，如果想要两行输入时，最后面加上反斜线 （\） 并立刻按下 [enter] ， 就能够到第二行继续输入了。这个指令下达的方式我们会在第三章再仔细说明。 通过这个 --exclude="file" 的动作，我们可以将几个特殊的文件或目录移除在打包之列，让打包的动作变的更简便喔！^_^

- 仅备份比某个时刻还要新的文件

某些情况下你会想要备份新的文件而已，并不想要备份旧文件！此时 --newer-mtime 这个选项就粉重要啦！ 其实有两个选项啦，一个是“ --newer ”另一个就是“ --newer-mtime ”，这两个选项有何不同呢？ 我们在 [第六章的 touch](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#touch) 介绍中谈到过三种不同的时间参数， 当使用 --newer 时，表示后续的日期包含“ mtime 与 ctime ”，而 --newer-mtime 则仅是 mtime 而已！ 这样知道了吧！ ^_^ 。那就让我们来尝试处理一下啰！

```
# 1\. 先由 [find](../Text/index.html#find) 找出比 /etc/passwd 还要新的文件
[root@study ~]# find /etc -newer /etc/passwd
....（过程省略）....
# 此时会显示出比 /etc/passwd 这个文件的 mtime 还要新的文件名，
# 这个结果在每部主机都不相同！您先自行查阅自己的主机即可，不会跟鸟哥一样！

[root@study ~]# ll /etc/passwd
-rw-r--r--. 1 root root 2092  Jun 17 00:20 /etc/passwd

# 2\. 好了，那么使用 tar 来进行打包吧！日期为上面看到的 2015/06/17
[root@study ~]# tar -jcv -f /root/etc.newer.then.passwd.tar.bz2 \
&gt; --newer-mtime="2015/06/17" /etc/*
tar: Option --newer-mtime: Treating date `2015/06/17' as 2015-06-17 00:00:00
tar: Removing leading `/' from member names
/etc/abrt/
....（中间省略）....
/etc/alsa/
/etc/yum.repos.d/
....（中间省略）....
tar: /etc/yum.repos.d/CentOS-fasttrack.repo: file is unchanged; not dumped
# 最后行显示的是“没有被备份的”，亦即 not dumped 的意思！

# 3\. 显示出文件即可
[root@study ~]# tar -jtv -f /root/etc.newer.then.passwd.tar.bz2 &#124; grep -v '/$' 
# 通过这个指令可以调用出 tar.bz2 内的结尾非 / 的文件名！就是我们要的啦！
```

现在你知道这个指令的好用了吧！甚至可以进行差异文件的记录与备份呢～ 这样子的备份就会显的更容易啰！你可以这样想像，如果我在一个月前才进行过一次完整的数据备份， 那么这个月想要备份时，当然可以仅备份上个月进行备份的那个时间点之后的更新的文件即可！ 为什么呢？因为原本的文件已经有备份了嘛！干嘛还要进行一次？只要备份新数据即可。这样可以降低备份的容量啊！

- 基本名称： tarfile, tarball ？

另外值得一提的是，tar 打包出来的文件有没有进行压缩所得到文件称呼不同喔！ 如果仅是打包而已，就是“ tar -cv -f file.tar ”而已，这个文件我们称呼为 tarfile 。 如果还有进行压缩的支持，例如“ tar -jcv -f file.tar.bz2 ”时，我们就称呼为 tarball （tar 球？）！这只是一个基本的称谓而已，不过很多书籍与网络都会使用到这个 tarball 的名称！所以得要跟您介绍介绍。

此外，tar 除了可以将数据打包成为文件之外，还能够将文件打包到某些特别的设备去，举例来说， 磁带机 （tape） 就是一个常见的例子。磁带机由于是一次性读取/写入的设备，因此我们不能够使用类似 cp 等指令来复制的！ 那如果想要将 /home, /root, /etc 备份到磁带机 （/dev/st0） 时，就可以使用：“tar -cv -f /dev/st0 /home /root /etc”，很简单容易吧！ 磁带机用在备份 （尤其是企业应用） 是很常见的工作喔！

- 特殊应用：利用管线命令与数据流

在 tar 的使用中，有一种方式最特殊，那就是通过标准输入输出的数据流重导向（standard input/standard output）， 以及管线命令 （pipe） 的方式，将待处理的文件一边打包一边解压缩到目标目录去。 关于数据流重导向与管线命令更详细的数据我们会在[第十章 bash](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 再跟大家介绍， 下面先来看一个例子吧！

```
# 1\. 将 /etc 整个目录一边打包一边在 /tmp 解开
[root@study ~]# cd /tmp
[root@study tmp]# tar -cvf - /etc &#124; tar -xvf -
# 这个动作有点像是 cp -r /etc /tmp 啦～依旧是有其有用途的！
# 要注意的地方在于输出文件变成 - 而输入文件也变成 - ，又有一个 &#124; 存在～
# 这分别代表 standard output, standard input 与管线命令啦！
# 简单的想法中，你可以将 - 想成是在内存中的一个设备（缓冲区）。
# 更详细的数据流与管线命令，请翻到 [bash](../Text/index.html) 章节啰！
```

在上面的例子中，我们想要“将 /etc 下面的数据直接 copy 到目前所在的路径，也就是 /tmp 下面”，但是又觉得使用 cp -r 有点麻烦，那么就直接以这个打包的方式来打包，其中，指令里面的 - 就是表示那个被打包的文件啦！ 由于我们不想要让中间文件存在，所以就以这一个方式来进行复制的行为啦！

- 例题：系统备份范例

系统上有非常多的重要目录需要进行备份，而且其实我们也不建议你将备份数据放置到 /root 目录下！ 假设目前你已经知道重要的目录有下面这几个：

- /etc/ （配置文件）
- /home/ （使用者的主文件夹）
- /var/spool/mail/ （系统中，所有帐号的邮件信箱）
- /var/spool/cron/ （所有帐号的工作排成配置文件）
- /root （系统管理员的主文件夹）

然后我们也知道，由于[第七章](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#loop)曾经做过的练习的关系， /home/loop* 不需要备份，而且 /root 下面的压缩文件也不需要备份，另外假设你要将备份的数据放置到 /backups ，并且该目录仅有 root 有权限进入！ 此外，每次备份的文件名都希望不相同，例如使用：backup-system-20150701.tar.bz2 之类的文件名来处理。 那你该如何处理这个备份数据呢？（请先动手作看看，再来察看一下下面的参考解答！）

```
# 1\. 先处理要放置备份数据的目录与权限：
[root@study ~]# mkdir /backups
[root@study ~]# chmod 700 /backups
[root@study ~]# ll -d /backups
drwx------. 2 root root 6 Jul  1 17:25 /backups

# 2\. 假设今天是 2015/07/01 ，则创建备份的方式如下：
[root@study ~]# tar -jcv -f /backups/backup-system-20150701.tar.bz2 \
&gt; --exclude=/root/*.bz2 --exclude=/root/*.gz --exclude=/home/loop* \
&gt; /etc /home /var/spool/mail /var/spool/cron /root
....（过程省略）....

[root@study ~]# ll -h /backups/
-rw-r--r--. 1 root root 21M Jul  1 17:26 backup-system-20150701.tar.bz2
```



### 备份系统的例题

假设我们需要备份以下重要目录：

- /etc/
- /home/
- /var/spool/mail/
- /var/spool/cron/
- /root/

同时排除 /root 目录下的压缩文件以及 /home/loop* 文件，备份文件名要包含日期：

```
sh复制代码[root@study ~]# mkdir /backups
[root@study ~]# chmod 700 /backups
[root@study ~]# tar -jcv -f /backups/backup-system-20150701.tar.bz2 --exclude=/root/*.bz2 --exclude=/root/*.gz --exclude=/home/loop* /etc /home /var/spool/mail /var/spool/cron /root
[root@study ~]# ll -h /backups/
-rw-r--r--. 1 root root 21M Jul 1 17:26 backup-system-20150701.tar.bz2
```

### 解压缩后的 SELinux 问题

复原系统后，如果遇到 SELinux 导致的权限问题，可以通过以下方法解决：

1. 修改 /etc/selinux/config 文件，将 SELinux 改成 permissive 模式，并重新启动。
2. 使用 `restorecon -Rv /etc` 修复 SELinux 类型。
3. 创建 /.autorelabel 文件，并重新启动系统让 SELinux 自动修复类型。

通过以上方法，可以解决解压缩后可能遇到的 SELinux 问题



### 8.4 XFS 文件系统的备份与还原

使用 tar 通常是针对目录树系统进行备份，那么如果想要备份和还原整个文件系统，可以使用 CentOS 7 默认的 XFS 文件系统的工具 xfsdump 和 xfsrestore。下面详细介绍这些工具的使用方法。

### 8.4.1 XFS 文件系统备份 xfsdump

xfsdump 的功能强大，可以进行完整备份（full backup）和累积备份（Incremental backup）。完整备份被定义为 level 0，累积备份则是记录与之前备份所有差异的文件。

![xfsdump 运行时，完整备份与累积备份示意图](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/img/centos7_dump-1.gif)

#### 使用 xfsdump 的注意事项：

- 只能备份已挂载的文件系统
- 需要 root 权限
- 只能备份 XFS 文件系统
- 备份数据只能由 xfsrestore 解析
- 使用文件系统的 UUID 来分辨备份文件，不能备份两个具有相同 UUID 的文件系统

#### xfsdump 常用选项：

```
[root@study ~]# xfsdump [-L S_label] [-M M_label] [-l #] [-f 备份文件] 待备份数据
[root@study ~]# xfsdump -I
选项与参数：
-L  ：xfsdump 会纪录每次备份的 session 标头，这里可以填写针对此文件系统的简易说明
-M  ：xfsdump 可以纪录储存媒体的标头，这里可以填写此媒体的简易说明
-l  ：是 L 的小写，就是指定等级～有 0~9 共 10 个等级喔！ （默认为 0，即完整备份）
-f  ：有点类似 tar 啦！后面接产生的文件，亦可接例如 /dev/st0 设备文件名或其他一般文件文件名等
-I  ：从 /var/lib/xfsdump/inventory 列出目前备份的信息状态
```

- `-L`：记录每次备份的 session 标头
- `-M`：记录储存媒体的标头
- `-l`：指定等级，有 0~9 共 10 个等级（默认 0，即完整备份）
- `-f`：指定生成的备份文件或设备文件名
- `-I`：列出当前备份的信息状态

特别注意， xfsdump 默认仅支持文件系统的备份，并不支持特定目录的备份～所以你不能用 xfsdump 去备份 /etc ！ **因为 /etc 从来就不是一个独立的文件系统！**注意！注意！

#### 备份完整的文件系统：

1. 确认 /boot 是独立的文件系统：

```
[root@study ~]# df -h /boot
```

1. 备份文件系统：

```
[root@study ~]# xfsdump -l 0 -L boot_all -M boot_all -f /srv/boot.dump /boot
```

1. 查看备份文件和信息：

```
[root@study ~]# ll /srv/boot.dump
[root@study ~]# ll /var/lib/xfsdump/inventory
```

**原文：**

现在就让我们来做几个范例吧！假设你跟鸟哥一样有将 /boot 分区出自己的文件系统，要整个文件系统备份可以这样作：

```
# 1\. 先确定 /boot 是独立的文件系统喔！
[root@study ~]# df -h /boot
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda2      1014M  131M  884M  13% /boot      # 挂载 /boot 的是 /dev/vda 设备！
# 看！确实是独立的文件系统喔！ /boot 是挂载点！

# 2\. 将完整备份的文件名记录成为 /srv/boot.dump ：
[root@study ~]# xfsdump -l 0 -L boot_all -M boot_all -f /srv/boot.dump /boot
xfsdump -l 0 -L boot_all -M boot_all -f /srv/boot.dump /boot
xfsdump: using file dump （drive_simple） strategy
xfsdump: version 3.1.4 （dump format 3.0） - type ^C for status and control
xfsdump: level 0 dump of study.centos.vbird:/boot              # 开始备份本机/boot系统
xfsdump: dump date: Wed Jul  1 18:43:04 2015                   # 备份的时间
xfsdump: session id: 418b563f-26fa-4c9b-98b7-6f57ea0163b1      # 这次dump的ID
xfsdump: session label: "boot_all"                             # 简单给予一个名字记忆
xfsdump: ino map phase 1: constructing initial dump list       # 开始备份程序
xfsdump: ino map phase 2: skipping （no pruning necessary）
xfsdump: ino map phase 3: skipping （only one dump stream）
xfsdump: ino map construction complete
xfsdump: estimated dump size: 103188992 Bytes
xfsdump: creating dump session media file 0 （media 0, file 0）
xfsdump: dumping ino map
xfsdump: dumping directories
xfsdump: dumping non-directory files
xfsdump: ending media file
xfsdump: media file size 102872168 Bytes
xfsdump: dump size （non-dir files） : 102637296 Bytes
xfsdump: dump complete: 1 seconds elapsed
xfsdump: Dump Summary:
xfsdump:   stream 0 /srv/boot.dump OK （success）
xfsdump: Dump Status: SUCCESS
# 在指令的下达方面，你也可以不加 -L 及 -M 的，只是那就会进入互动模式，要求你 enter！
# 而执行 xfsdump 的过程中会出现如上的一些讯息，您可以自行仔细的观察！

[root@study ~]# ll /srv/boot.dump
-rw-r--r--. 1 root root 102872168 Jul  1 18:43 /srv/boot.dump

[root@study ~]# ll /var/lib/xfsdump/inventory
-rw-r--r--. 1 root root 5080 Jul  1 18:43 506425d2-396a-433d-9968-9b200db0c17c.StObj
-rw-r--r--. 1 root root  312 Jul  1 18:43 94ac5f77-cb8a-495e-a65b-2ef7442b837c.InvIndex
-rw-r--r--. 1 root root  576 Jul  1 18:43 fstab
# 使用了 xfsdump 之后才会有上述 /var/lib/xfsdump/inventory 内的文件产生喔！
```

这样很简单的就创建起来 /srv/boot.dump 文件，该文件将整个 /boot/ 文件系统都备份下来了！ 并且将备份的相关信息 （文件系统/时间/session ID等等） 写入 /var/lib/xfsdump/inventory 中，准备让下次备份时可以作为一个参考依据。 现在让我们来进行一个测试，检查看看能否真的创建 level 1 的备份呢？

#### 进行累积备份：

1. 确认已有完整备份：

```
[root@study ~]# xfsdump -I
```

1. 创建大约 10 MB 的文件：

```
[root@study ~]# dd if=/dev/zero of=/boot/testing.img bs=1M count=10
```

1. 创建差异备份文件：

```
[root@study ~]# xfsdump -l 1 -L boot_2 -M boot_2 -f /srv/boot.dump1 /boot
```

1. 查看差异备份文件：

```
[root@study ~]# ll /srv/boot*
[root@study ~]# xfsdump -I
```

**原文：**

你一定得要进行过完整备份后 （-l 0） 才能够继续有其他累积备份 （-l 1~9） 的能耐！所以，请确定上面的实做已经完成！ 接下来让我们来搞一搞累积备份功能吧！

```
# 0\. 看一下有没有任何文件系统被 xfsdump 过的数据？
[root@study ~]# xfsdump -I
file system 0:
    fs id:          94ac5f77-cb8a-495e-a65b-2ef7442b837c
    session 0:
        mount point:    study.centos.vbird:/boot
        device:         study.centos.vbird:/dev/vda2
        time:           Wed Jul  1 18:43:04 2015
        session label:  "boot_all"
        session id:     418b563f-26fa-4c9b-98b7-6f57ea0163b1
        level:          0
        resumed:        NO
        subtree:        NO
        streams:        1
        stream 0:
                pathname:       /srv/boot.dump
                start:          ino 132 offset 0
                end:            ino 2138243 offset 0
                interrupted:    NO
                media files:    1
                media file 0:
                        mfile index:    0
                        mfile type:     data
                        mfile size:     102872168
                        mfile start:    ino 132 offset 0
                        mfile end:      ino 2138243 offset 0
                        media label:    "boot_all"
                        media id:       a6168ea6-1ca8-44c1-8d88-95c863202eab
xfsdump: Dump Status: SUCCESS
# 我们可以看到目前仅有一个 session 0 的备份数据而已！而且是 level 0 喔！

# 1\. 先恶搞一下，创建一个大约 10 MB 的文件在 /boot 内：
[root@study ~]# dd if=/dev/zero of=/boot/testing.img bs=1M count=10
10+0 records in
10+0 records out
10485760 Bytes （10 MB） copied, 0.166128 seconds, 63.1 MB/s

# 2\. 开始创建差异备份文件，此时我们使用 level 1 吧：
[root@study ~]# xfsdump -l 1 -L boot_2 -M boot_2 -f /srv/boot.dump1 /boot
....（中间省略）....

[root@study ~]# ll /srv/boot*
-rw-r--r--. 1 root root 102872168 Jul  1 18:43 /srv/boot.dump
-rw-r--r--. 1 root root  10510952 Jul  1 18:46 /srv/boot.dump1
# 看看文件大小，岂不是就是刚刚我们所创建的那个大文件的容量吗？ ^_^

# 3\. 最后再看一下是否有记录 level 1 备份的时间点呢？
[root@study ~]# xfsdump -I
file system 0:
    fs id:          94ac5f77-cb8a-495e-a65b-2ef7442b837c
    session 0:
        mount point:    study.centos.vbird:/boot
        device:         study.centos.vbird:/dev/vda2
....（中间省略）....

    session 1:
        mount point:    study.centos.vbird:/boot
        device:         study.centos.vbird:/dev/vda2
        time:           Wed Jul  1 18:46:21 2015
        session label:  "boot_2"
        session id:     c71d1d41-b3bb-48ee-bed6-d77c939c5ee8
        level:          1
        resumed:        NO
        subtree:        NO
        streams:        1
        stream 0:
                pathname:       /srv/boot.dump1
                start:          ino 455518 offset 0
....（下面省略）....
```

通过这个简单的方式，我们就能够仅备份差异文件的部分啰！

### 8.4.2 XFS 文件系统还原 xfsrestore

xfsrestore 用于还原由 xfsdump 备份的数据。

#### xfsrestore 常用选项：

```
[root@study ~]# xfsrestore -I &lt;==用来察看备份文件数据
[root@study ~]# xfsrestore [-f 备份文件] [-L S_label] [-s] 待复原目录 &lt;==单一文件全系统复原
[root@study ~]# xfsrestore [-f 备份文件] -r 待复原目录 &lt;==通过累积备份文件来复原系统
[root@study ~]# xfsrestore [-f 备份文件] -i 待复原目录 &lt;==进入互动模式
选项与参数：
-I  ：跟 xfsdump 相同的输出！可查询备份数据，包括 Label 名称与备份时间等
-f  ：后面接的就是备份文件！企业界很有可能会接 /dev/st0 等磁带机！我们这里接文件名！
-L  ：就是 Session 的 Label name 喔！可用 -I 查询到的数据，在这个选项后输入！
-s  ：需要接某特定目录，亦即仅复原某一个文件或目录之意！
-r  ：如果是用文件来储存备份数据，那这个就不需要使用。如果是一个磁带内有多个文件，
      需要这东西来达成累积复原
-i  ：进入互动模式，进阶管理员使用的！一般我们不太需要操作它！
```

- `-I`：查询备份数据，包括 Label 名称与备份时间等
- `-f`：指定备份文件
- `-L`：指定 Session 的 Label name
- `-s`：仅复原某一个文件或目录
- `-r`：用于累积复原
- `-i`：进入互动模式

#### 观察备份数据内容：

```
[root@study ~]# xfsrestore -I
```



-  xfsrestore 观察 xfsdump 后的备份数据内容

要找出 xfsdump 的内容就使用 xfsrestore -I 来查阅即可！不需要加任何参数！因为 xfsdump 与 xfsrestore 都会到 /var/lib/xfsdump/inventory/ 里面去捞数据来显示的！因此两者输出是相同的！

```
[root@study ~]# xfsrestore -I 
file system 0:
    fs id:          94ac5f77-cb8a-495e-a65b-2ef7442b837c
    session 0:
        mount point:    study.centos.vbird:/boot
        device:         study.centos.vbird:/dev/vda2
        time:           Wed Jul  1 18:43:04 2015
        session label:  "boot_all"
        session id:     418b563f-26fa-4c9b-98b7-6f57ea0163b1
        level:          0
                pathname:       /srv/boot.dump
                        mfile size:     102872168
                        media label:    "boot_all"
    session 1:
        mount point:    study.centos.vbird:/boot
        device:         study.centos.vbird:/dev/vda2
        time:           Wed Jul  1 18:46:21 2015
        session label:  "boot_2"
        session id:     c71d1d41-b3bb-48ee-bed6-d77c939c5ee8
        level:          1
                pathname:       /srv/boot.dump1
                        mfile size:     10510952
                        media label:    "boot_2"
xfsrestore: Restore Status: SUCCESS
# 鸟哥已经将不重要的项目删除了，所以上面的输出是经过经简化的结果！
# 我们可以看到这个文件系统是 /boot 载点，然后有两个备份，一个 level 0 一个 level 1。
# 也看到这两个备份的数据他的内容大小！更重要的，就是那个 session label 喔！
```

这个查询重点是找出到底哪个文件是哪个挂载点？而该备份文件又是什么 level 等等的！接下来，让我们实做一下从备份还原系统吧！



#### 简单复原 level 0 的文件系统：

1.将数据覆盖回去：

```
[root@study ~]# xfsrestore -f /srv/boot.dump -L boot_all /boot
```

2.将备份数据在 /tmp/boot 下解开：

```
[root@study ~]# mkdir /tmp/boot
[root@study ~]# xfsrestore -f /srv/boot.dump -L boot_all /tmp/boot
```

3.检查文件差异：

```
[root@study ~]# diff -r /boot /tmp/boot
```

**原文：**

先来处理一个简单的任务，就是将 /boot 整个复原到最原本的状态～你该如何处理？其实很简单，我们只要知道想要被复原的那个文件， 以及该文件的 session label name，就可以复原啦！我们从上面的观察已经知道 level 0 的 session label 是“boot_all”啰！那整个流程是这样：

```
# 1\. 直接将数据给它覆盖回去即可！
[root@study ~]# xfsrestore -f /srv/boot.dump -L boot_all /boot
xfsrestore: using file dump （drive_simple） strategy
xfsrestore: version 3.1.4 （dump format 3.0） - type ^C for status and control
xfsrestore: using online session inventory
xfsrestore: searching media for directory dump
xfsrestore: examining media file 0
xfsrestore: reading directories
xfsrestore: 8 directories and 327 entries processed
xfsrestore: directory post-processing
xfsrestore: restoring non-directory files
xfsrestore: restore complete: 1 seconds elapsed
xfsrestore: Restore Summary:
xfsrestore:   stream 0 /srv/boot.dump OK （success）  # 是否是正确的文件啊？
xfsrestore: Restore Status: SUCCESS

# 2\. 将备份数据在 /tmp/boot 下面解开！
[root@study ~]# mkdir /tmp/boot
[root@study ~]# xfsrestore -f /srv/boot.dump -L boot_all /tmp/boot
[root@study ~]# du -sm /boot /tmp/boot
109     /boot
99      /tmp/boot
# 咦！两者怎么大小不一致呢？没关系！我们来检查看看！

[root@study ~]# diff -r /boot /tmp/boot
Only in /boot: testing.img
# 看吧！原来是 /boot 我们有增加过一个文件啦！
```

因为原本 /boot 里面的东西我们没有删除，直接复原的结果就是：“同名的文件会被覆盖，其他系统内新的文件会被保留”喔！所以， 那个 /boot/testing.img 就会一直在里头～如果备份的目的地是新的位置，当然就只有原本备份的数据而已啊！那个 diff -r 可以比较两个目录内的文件差异！ 通过该指令我们可以找到两个目录的差异处！



#### 仅复原备份文件内的特定目录：

```
# 3\. 仅复原备份文件内的 grub2 到 /tmp/boot2/ 里头去！
[root@study ~]# mkdir /tmp/boot2
[root@study ~]# xfsrestore -f /srv/boot.dump -L boot_all -s grub2 /tmp/boot2
```

如果只想要复原某一个目录或文件的话，直接加上“ -s 目录 ”这个选项与参数即可！相当简单好用！



- 复原累积备份数据

其实复原累积备份与复原单一文件系统相似耶！如果备份数据是由 level 0 -> level 1 -> level 2... 去进行的， 当然复原就得要相同的流程来复原！因此当我们复原了 level 0 之后，接下来当然就要复原 level 1 到系统内啊！ 我们可以前一个案例复原 /tmp/boot 的情况来继续往下处理：

```
# 继续复原 level 1 到 /tmp/boot 当中！
[root@study ~]# xfsrestore -f /srv/boot.dump1 /tmp/boot
```

- 仅还原部分文件的 xfsrestore 互动模式

刚刚的 -s 可以接部份数据来还原，但是...如果我就根本不知道备份文件里面有啥文件，那该如何选择啊？用猜的喔？ 又如果要复原的文件数量太多时，用 -s 似乎也是笨笨的～那怎办？有没有比较好的方式呢？有的，就通过 -i 这个互动界面吧！ 举例来说，我们想要知道 level 0 的备份数据里面有哪些东西，然后再少量的还原回来的话！

```
# 1\. 先进入备份文件内，准备找出需要备份的文件名数据，同时预计还原到 /tmp/boot3 当中！
[root@study ~]# mkdir /tmp/boot3
[root@study ~]# xfsrestore -f /srv/boot.dump -i /tmp/boot3
 ========================== subtree selection dialog ==========================

the following commands are available:
        pwd
        ls [ &lt;path&gt; ]
        cd [ &lt;path&gt; ]
        add [ &lt;path&gt; ]       # 可以加入复原文件列表中
        delete [ &lt;path&gt; ]    # 从复原列表拿掉文件名！并非删除喔！
        extract              # 开始复原动作！
        quit
        help

 -&gt; ls
          455517 initramfs-3.10.0-229.el7.x86_64kdump.img
             138 initramfs-3.10.0-229.el7.x86_64.img
             141 initrd-plymouth.img
             140 vmlinuz-0-rescue-309eb890d09f440681f596543d95ec7a
             139 initramfs-0-rescue-309eb890d09f440681f596543d95ec7a.img
             137 vmlinuz-3.10.0-229.el7.x86_64
             136 symvers-3.10.0-229.el7.x86_64.gz
             135 config-3.10.0-229.el7.x86_64
             134 System.map-3.10.0-229.el7.x86_64
             133 .vmlinuz-3.10.0-229.el7.x86_64.hmac
         1048704 grub2/
             131 grub/

 -&gt; add grub
 -&gt; add grub2
 -&gt; add config-3.10.0-229.el7.x86_64
 -&gt; extract

[root@study ~]# ls -l /tmp/boot3
-rw-r--r--. 1 root root 123838 Mar  6 19:45 config-3.10.0-229.el7.x86_64
drwxr-xr-x. 2 root root     26 May  4 17:52 grub
drwxr-xr-x. 6 root root    104 Jun 25 00:02 grub2
# 就只会有 3 个文件名被复原，当然，如果文件名是目录，那下面的子文件当然也会被还原回来的！
```

事实上，这个 -i 是很有帮助的一个项目！可以从备份文件里面找出你所需要的数据来复原！相当有趣！当然啦， 如果你已经知道文件名，使用 -s 不需要进入备份文件就能够处理掉这部份了！



### 8.5 光盘写入工具==未完成==

虽然企业仍然喜欢使用磁带进行备份，因为它们具有高容量、长储存时限和耐摔等优点，但 DVD/CD 等光盘由于存储速度慢和容量有限，已经逐渐被 U 盘和 USB 外接式硬盘所取代。不过，某些情况下还是需要使用光盘，所以了解创建光盘镜像文件和烧录软件还是很有必要的。

#### 8.5.1 mkisofs：创建镜像文件

mkisofs 用于将数据打包成 ISO 镜像文件，该镜像文件可以用于光盘烧录。基本使用方法如下：

```
[root@study ~]# mkisofs [-o 镜像文件] [-Jrv] [-V vol] [-m file] 待备份文件...
```

常用选项：

- `-o`：指定生成的镜像文件名
- `-J`：生成适用于 Windows 机器的文件名结构
- `-r`：支持 Unix/Linux 的文件数据，记录更多信息（如 UID/GID 等）
- `-v`：显示创建 ISO 文件的过程
- `-V`：创建卷标，类似于 Windows 中的 CD title
- `-m`：排除指定文件（支持 * 万用字符）
- `-graft-point`：定义镜像文件中的目录结构

示例：创建包含 /root、/home、/etc 目录的镜像文件

```
[root@study ~]# mkisofs -r -v -o /tmp/system.img /root /home /etc
```

如果文件名有冲突，使用 `-graft-point` 选项来指定目录结构：

```
[root@study ~]# mkisofs -r -V 'linux_file' -o /tmp/system.img -m /root/etc -graft-point /root=/root /home=/home /etc=/etc
```



#### 8.5.2 cdrecord：光盘烧录工具

新版 CentOS 7 使用 wodim 进行烧录，兼容旧版的 cdrecord 命令。常用选项：

```
[root@study ~]# wodim --devices dev=/dev/sr0...                  # 查询烧录机位置
[root@study ~]# wodim -v dev=/dev/sr0 blank=[fast|all]           # 抹除可重复读写光盘
[root@study ~]# wodim -v dev=/dev/sr0 -format                    # 格式化 DVD+RW
[root@study ~]# wodim -v dev=/dev/sr0 [可用选项功能] file.iso    # 烧录 ISO 文件
```

常用参数：

- `--devices`：扫描磁盘总线并找出可用烧录机
- `-v`：显示过程
- `dev=/dev/sr0`：指定光驱设备
- `blank=[fast|all]`：抹除可重复写入的光盘，fast 较快，all 较完整
- `-format`：格式化 DVD+RW
- `-data`：指定以数据格式写入
- `speed=X`：指定烧录速度
- `-eject`：烧录完毕后自动退出光盘
- `fs=Ym`：指定缓冲内存大小
- `driveropts=burnfree`：打开 Buffer Underrun Free 功能
- `-sao`：支持 DVD-RW 格式

#### 步骤：找到光驱设备并进行烧录

1. 查找光驱位置：

```
[root@study ~]# wodim --devices dev=/dev/sr0
```

1. 抹除光盘内容（若非可重复读写光盘可略过）：

```
[root@demo ~]# wodim -v dev=/dev/sr0 blank=fast
```

1. 烧录镜像文件：

```
[root@demo ~]# wodim -v dev=/dev/sr0 speed=4 -dummy -eject /tmp/system.img
```

1. 测试挂载烧录的光盘：

```
[root@demo ~]# mount /dev/sr0 /mnt
[root@demo ~]# df -h /mnt
[root@demo ~]# umount /mnt
```

使用文字模式进行光盘烧录较为复杂，但掌握基本命令可以应对一些特殊情况。对于服务器备份，选择合适的工具和方法至关重要。



### 8.6 其他常见的压缩与备份工具

除了前面介绍的工具，dd 和 cpio 也是非常有用的备份工具，特别是 dd。

#### 8.6.1 dd

dd 命令不仅可以制作文件，还可以直接读取和备份磁盘设备的内容。dd 的使用非常灵活，以下是一些常用选项：

```
[root@study ~]# dd if="input_file" of="output_file" bs="block_size" count="number"
```

- `if`：输入文件或设备
- `of`：输出文件或设备
- `bs`：每个块的大小，默认为 512 Bytes
- `count`：块的数量

**示例一：将 /etc/passwd 备份到 /tmp/passwd.back**

```
[root@study ~]# dd if=/etc/passwd of=/tmp/passwd.back
```

**示例二：将光驱内容备份为镜像文件**

```
[root@study ~]# dd if=/dev/sr0 of=/tmp/system.iso
```

**示例三：将镜像文件写入 USB 磁盘**

```
[root@study ~]# dd if=/tmp/system.iso of=/dev/sda
```

**示例四：备份 /boot 文件系统**

```
[root@study ~]# dd if=/dev/vda2 of=/tmp/vda2.img
```

原文：

```
[root@study ~]# dd if="input_file" of="output_file" bs="block_size" count="number"
选项与参数：
if   ：就是 input file 啰～也可以是设备喔！
of   ：就是 output file 喔～也可以是设备；
bs   ：规划的一个 block 的大小，若未指定则默认是 512 Bytes（一个 sector 的大小）
count：多少个 bs 的意思。

范例一：将 /etc/passwd 备份到 /tmp/passwd.back 当中
[root@study ~]# dd if=/etc/passwd of=/tmp/passwd.back
4+1 records in
4+1 records out
2092 Bytes （2.1 kB） copied, 0.000111657 s, 18.7 MB/s
[root@study ~]# ll /etc/passwd /tmp/passwd.back
-rw-r--r--. 1 root root 2092 Jun 17 00:20 /etc/passwd
-rw-r--r--. 1 root root 2092 Jul  2 23:27 /tmp/passwd.back
# 仔细的看一下，我的 /etc/passwd 文件大小为 2092 Bytes，因为我没有设置 bs ，
# 所以默认是 512 Bytes 为一个单位，因此，上面那个 4+1 表示有 4 个完整的 512 Bytes，
# 以及未满 512 Bytes 的另一个 block 的意思啦！事实上，感觉好像是 cp 这个指令啦～

范例二：将刚刚烧录的光驱的内容，再次的备份下来成为图像挡
[root@study ~]# dd if=/dev/sr0 of=/tmp/system.iso
177612+0 records in
177612+0 records out
90937344 Bytes （91 MB） copied, 22.111 s, 4.1 MB/s
# 要将数据抓下来用这个方法，如果是要将镜像文件写入 USB 磁盘，就会变如下一个范例啰！

范例三：假设你的 USB 是 /dev/sda 好了，请将刚刚范例二的 image 烧录到 USB 磁盘中
[root@study ~]# lsblk /dev/sda
NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda    8:0    0   2G  0 disk             # 确实是 disk 而且有 2GB 喔！

[root@study ~]# dd if=/tmp/system.iso of=/dev/sda
[root@study ~]# mount /dev/sda /mnt
[root@study ~]# ll /mnt
dr-xr-xr-x. 131 root root 34816 Jun 26 22:14 etc
dr-xr-xr-x.   5 root root  2048 Jun 17 00:20 home
dr-xr-xr-x.   8 root root  4096 Jul  2 18:48 root
# 如果你不想要使用 DVD 来作为开机媒体，那可以将镜像文件使用这个 dd 写入 USB 磁盘，
# 该磁盘就会变成跟可开机光盘一样的功能！可以让你用 USB 来安装 Linux 喔！速度快很多！

范例四：将你的 /boot 整个文件系统通过 dd 备份下来
[root@study ~]# df -h /boot
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda2      1014M  149M  866M  15% /boot       # 请注意！备份的容量会到 1G 喔！
[root@study ~]# dd if=/dev/vda2 of=/tmp/vda2.img
[root@study ~]# ll -h /tmp/vda2.img
-rw-r--r--. 1 root root 1.0G Jul  2 23:39 /tmp/vda2.img
# 等于是将整个 /dev/vda2 通通捉下来的意思～所以，文件大小会跟整颗磁盘的最大量一样大！
```

其实使用 dd 来备份是莫可奈何的情况，很笨耶！因为默认 dd 是一个一个扇区去读/写的，而且即使没有用到的扇区也会被写入备份文件中！ 因此这个文件会变得跟原本的磁盘一模一样大！不像使用 xfsdump 只备份文件系统中有使用到的部份。不过， dd 就是因为不理会文件系统， 单纯有啥纪录啥，因此不论该磁盘内的文件系统你是否认识，它都可以备份、还原的！所以，鸟哥认为，上述的第三个案例是比较重要的学习喔！



**原文：**

例题：你想要将你的 /dev/vda2 进行完整的复制到另一个 partition 上，请使用你的系统上面未分区完毕的容量再创建一个与 /dev/vda2 差不多大小的分区 （只能比 /dev/vda2 大，不能比他小！），然后将之进行完整的复制 （包括需要复制 boot sector 的区块）。答：因为我们的 /dev/sda 也是个测试的 USB 磁盘，可以随意恶搞！我们刚刚也才测试过将光盘镜像文件给它复制进去而已。 现在，请你分区 /dev/sda1 出来，然后将 /dev/vda2 完整的拷贝进去 /dev/sda1 吧！

```
# 1\. 先进行分区的动作
[root@study ~]# fdisk /dev/sda

Command （m for help）: n
Partition type:
   p   primary （0 primary, 0 extended, 4 free）
   e   extended
Select （default p）: p
Partition number （1-4, default 1）: 1
First sector （2048-4195455, default 2048）: Enter
Using default value 2048
Last sector, +sectors or +size{K,M,G} （2048-4195455, default 4195455）: Enter
Using default value 4195455
Partition 1 of type Linux and of size 2 GiB is set

Command （m for help）: p
   Device Boot      Start         End      Blocks   Id  System
/dev/sda1            2048     4195455     2096704   83  Linux

Command （m for help）: w

[root@study ~]# partprobe

# 2\. 不需要格式化，直接进行 sector 表面的复制！
[root@study ~]# dd if=/dev/vda2 of=/dev/sda1
2097152+0 records in
2097152+0 records out
1073741824 Bytes （1.1 GB） copied, 71.5395 s, 15.0 MB/s

[root@study ~]# xfs_repair -L /dev/sda1  # 一定要先清除一堆 log 才行！
[root@study ~]# uuidgen                  # 下面两行在给予一个新的 UUID
896c38d1-bcb5-475f-83f1-172ab38c9a0c
[root@study ~]# xfs_admin -U 896c38d1-bcb5-475f-83f1-172ab38c9a0c /dev/sda1
# 因为 XFS 文件系统主要使用 UUID 来分辨文件系统，但我们使用 dd 复制，连 UUID
# 也都复制成为相同！当然就得要使用上述的 xfs_repair 及 xfs_admin 来修订一下！

[root@study ~]# mount /dev/sda1 /mnt
[root@study ~]# df -h /boot /mnt
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda2      1014M  149M  866M  15% /boot
/dev/sda1      1014M  149M  866M  15% /mnt
# 这两个玩意儿会“一模一样”喔！

# 3\. 接下来！让我们将文件系统放大吧！！！
[root@study ~]# xfs_growfs /mnt
[root@study ~]# df -h /boot /mnt
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda2      1014M  149M  866M  15% /boot
/dev/sda1       2.0G  149M  1.9G   8% /mnt

[root@study ~]# umount /mnt
```

非常有趣的范例吧！新分区出来的 partition 不需要经过格式化，因为 dd 可以将原本旧的 partition 上面，将 sector 表面的数据整个复制过来！ 当然连同 superblock, boot sector, meta data 等等通通也会复制过来！是否很有趣呢？未来你想要创建两颗一模一样的磁盘时， 只要下达类似： dd if=/dev/sda of=/dev/sdb ，就能够让两颗磁盘一模一样，甚至 /dev/sdb 不需要分区与格式化， 因为该指令可以将 /dev/sda 内的所有数据，包括 MBR 与 partition table 也复制到 /dev/sdb 说！ ^_^

话说，用 dd 来处理这方面的事情真的是很方便，你也不需考虑到啥有的没的，通通是磁盘表面的复制而已！不过如果真的用在文件系统上面， 例如上面这个案例，那么再次挂载时，恐怕得要理解一下每种文件系统的挂载要求！以上面的案例来说，你就得要先清除 XFS 文件系统内的 log 之后， 重新给予一个跟原本不一样的 UUID 后，才能够顺利挂载！同时，为了让系统继续利用后续没有用到的磁盘空间，那个 xfs_growfs 就得要理解一下。 关于 xfs_growfs 我们会在后续第十四章继续强调！这里先理解即可。





#### 8.6.2 cpio

cpio 是一个功能强大的备份工具，可以备份包括设备文件在内的任何文件。但 cpio 需要其他工具（如 find）来提供要备份的文件列表。常用选项如下：

```
[root@study ~]# cpio -ovcB > [file|device]    # 备份
[root@study ~]# cpio -ivcdu < [file|device]   # 还原
[root@study ~]# cpio -ivct < [file|device]    # 察看
```

- `-o`：将数据输出到文件或设备
- `-B`：增加 Blocks 大小至 5120 Bytes，加快大文件储存速度
- `-i`：将数据从文件或设备恢复到系统
- `-d`：自动创建目录
- `-u`：覆盖较旧的文件
- `-t`：查看 cpio 创建的文件或设备内容
- `-v`：显示过程中的文件名称
- `-c`：使用新的 portable format 储存

**原文：**

这个指令挺有趣的，因为 cpio 可以备份任何东西，包括设备设备文件。不过 cpio 有个大问题， 那就是 cpio 不会主动的去找文件来备份！啊！那怎办？所以啰，一般来说， cpio 得要配合类似 [find](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#find) 等可以找到文件名的指令来告知 cpio 该被备份的数据在哪里啊！ 有点小麻烦啦～因为牵涉到我们在第三篇才会谈到的[数据流重导向](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#redirect)说～ 所以这里你就先背一下语法，等到第三篇讲完你就知道如何使用 cpio 啰！

```
[root@study ~]# cpio -ovcB  &gt; [file&#124;device] &lt;==备份
[root@study ~]# cpio -ivcdu &lt; [file&#124;device] &lt;==还原
[root@study ~]# cpio -ivct  &lt; [file&#124;device] &lt;==察看
备份会使用到的选项与参数：
  -o ：将数据 copy 输出到文件或设备上
  -B ：让默认的 Blocks 可以增加至 5120 Bytes ，默认是 512 Bytes ！
　  　 这样的好处是可以让大文件的储存速度加快（请参考 i-nodes 的观念）
还原会使用到的选项与参数：
  -i ：将数据自文件或设备 copy 出来系统当中
  -d ：自动创建目录！使用 cpio 所备份的数据内容不见得会在同一层目录中，因此我们
       必须要让 cpio 在还原时可以创建新目录，此时就得要 -d 选项的帮助！
  -u ：自动的将较新的文件覆盖较旧的文件！
  -t ：需配合 -i 选项，可用在"察看"以 cpio 创建的文件或设备的内容
一些可共享的选项与参数：
  -v ：让储存的过程中文件名称可以在屏幕上显示
  -c ：一种较新的 portable format 方式储存
```

你应该会发现一件事情，就是上述的选项与指令中怎么会没有指定需要备份的数据呢？还有那个大于 （>） 与小于 （<） 符号是怎么回事啊？因为 cpio 会将数据整个显示到屏幕上，因此我们可以通过将这些屏幕的数据重新导向 （>） 一个新的文件！ 至于还原呢？就是将备份文件读进来 cpio （<） 进行处理之意！我们来进行几个案例你就知道啥是啥了！

```
范例：找出 /boot 下面的所有文件，然后将他备份到 /tmp/boot.cpio 去！
[root@study ~]# cd /
[root@study /]# find boot -print
boot
boot/grub
boot/grub/splash.xpm.gz
....（以下省略）....
# 通过 find 我们可以找到 boot 下面应该要存在的文件名！包括文件与目录！但请千万不要是绝对路径！

[root@study /]# find boot &#124; cpio -ocvB &gt; /tmp/boot.cpio
[root@study /]# ll -h /tmp/boot.cpio
-rw-r--r--. 1 root root 108M Jul  3 00:05 /tmp/boot.cpio
[root@study ~]# file /tmp/boot.cpio
/tmp/boot.cpio: ASCII cpio archive （SVR4 with no CRC）
```

我们使用 find boot 可以找出文件名，然后通过那条管线 （|, 亦即键盘上的 shift+\ 的组合）， 就能将文件名传给 cpio 来进行处理！最终会得到 /tmp/boot.cpio 那个文件喔！你可能会觉得奇怪，为啥鸟哥要先转换目录到 / 再去找 boot 呢？ 为何不能直接找 /boot 呢？这是因为 cpio 很笨！它不会理会你给的是绝对路径还是相对路径的文件名，所以如果你加上绝对路径的 / 开头， 那么未来解开的时候，它就一定会覆盖掉原本的 /boot 耶！那就太危险了！这个我们在 tar 也稍微讲过那个 -P 的选项！！理解吧！ 好了，那接下来让我们来进行解压缩看看。

```
范例：将刚刚的文件给他在 /root/ 目录下解开
[root@study ~]# cd ~
[root@study ~]# cpio -idvc &lt; /tmp/boot.cpio
[root@study ~]# ll /root/boot
# 你可以自行比较一下 /root/boot 与 /boot 的内容是否一模一样！
```

事实上 cpio 可以将系统的数据完整的备份到磁带机上头去喔！如果你有磁带机的话！

- 备份：find / | cpio -ocvB > /dev/st0
- 还原：cpio -idvc < /dev/st0

这个 cpio 好像不怎么好用呦！但是，他可是备份的时候的一项利器呢！因为他可以备份任何的文件， 包括 /dev 下面的任何设备文件！所以他可是相当重要的呢！而由于 cpio 必需要配合其他的程序，例如 find 来创建文件名，所以 cpio 与管线命令及数据流重导向的相关性就相当的重要了！

其实系统里面已经含有一个使用 cpio 创建的文件喔！那就是 /boot/initramfs-xxx 这个文件啦！现在让我们来将这个文件解压缩看看，看你能不能发现该文件的内容为何？

```
# 1\. 我们先来看看该文件是属于什么文件格式，然后再加以处理：
[root@study ~]# file /boot/initramfs-3.10.0-229.el7.x86_64.img
/boot/initramfs-3.10.0-229.el7.x86_64.img: ASCII cpio archive （SVR4 with no CRC）

[root@study ~]# mkdir /tmp/initramfs
[root@study ~]# cd /tmp/initramfs
[root@study initramfs]# cpio -idvc &lt; /boot/initramfs-3.10.0-229.el7.x86_64.img
.
kernel
kernel/x86
kernel/x86/microcode
kernel/x86/microcode/GenuineIntel.bin
early_cpio
22 blocks
# 瞧！这样就将这个文件解开啰！这样了解乎？
```



**示例：备份 /boot 目录并还原**

1. 使用 find 找出所有文件并备份到 /tmp/boot.cpio：

```
[root@study ~]# cd /
[root@study /]# find boot | cpio -ocvB > /tmp/boot.cpio
```

1. 在 /root 目录下还原备份的文件：

```
[root@study ~]# cd ~
[root@study ~]# cpio -idvc < /tmp/boot.cpio
```

**示例：使用 cpio 备份和还原磁带机**

```
[root@study ~]# find / | cpio -ocvB > /dev/st0   # 备份
[root@study ~]# cpio -idvc < /dev/st0           # 还原
```

通过这些示例，可以看出 dd 和 cpio 是非常强大的备份工具，尤其适用于备份设备文件和进行系统恢复。掌握这些工具的使用，可以大大提高系统管理和数据保护的效率。



## 8.7 重点回顾

- 压缩指令为通过一些运算方法去将原本的文件进行压缩，以减少文件所占用的磁盘容量。 **压缩前与压缩后的文件所占用的磁盘容量比值， 就可以被称为是“压缩比”**
- 压缩的好处是可以减少磁盘容量的浪费，**在 WWW 网站也可以利用文件压缩的技术来进行数据的传送，好让网站带宽的可利用率上升喔**
- 压缩文件的扩展名大多是：“*.gz,* .bz2, *.xz,* .tar, *.tar.gz,* .tar.bz2, *.tar.xz”
- 常见的压缩指令有 gzip, bzip2, xz。压缩率最佳的是 xz，若可以不计时间成本，建议使用 xz 进行压缩。
- **tar 可以用来进行文件打包，并可支持 gzip, bzip2, xz 的压缩。**
- **压　缩：tar -Jcv -f filename.tar.xz 要被压缩的文件或目录名称**
- **查　询：tar -Jtv -f filename.tar.xz**
- **解压缩：tar -Jxv -f filename.tar.xz -C 欲解压缩的目录**
- xfsdump 指令可备份文件系统或单一目录
- xfsdump 的备份若针对文件系统时，可进行 0-9 的 level 差异备份！其中 level 0 为完整备份；
- xfsrestore 指令可还原被 xfsdump 创建的备份文件；
- 要创建光盘烧录数据时，可通过 mkisofs 指令来创建；
- 可通过 wodim 来写入 CD 或 DVD 烧录机
- dd 可备份完整的 partition 或 disk ，因为 dd 可读取磁盘的 sector 表面数据
- cpio 为相当优秀的备份指令，不过必须要搭配类似 find 指令来读入欲备份的文件名数据，方可进行备份动作。