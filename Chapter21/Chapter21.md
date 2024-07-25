# 第二十一章、软件安装：源代码与 Tarball

最近更新日期：20//

我们在[第一章、Linux是什么](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)当中提到了 GNU 计划与 GPL 授权所产生的自由软件与开放源码等咚咚。不过，前面的章节都还没有提到真正的开放源码是什么的讯息！在这一章当中，我们将借由 Linux 操作系统里面的可执行文件，来理解什么是可执行的程序，以及了解什么是编译器。另外，与程序息息相关的函数库 （library） 的信息也需要了解一番！不过，在这个章节当中，鸟哥并不是要你成为一个开放源码的程序设计师， 而是希望你可以了解如何将开放源码的程序设计、加入函数库的原理、通过编译而成为可以执行 的 binary program，最后该可执行文件可被我们所使用的一连串过程！

了解上面的咚咚有什么好处呢？因为在 Linux 的世界里面，由于客制化的关系，有时候我们需要自行安装软件在自己的 Linux 系统上面，所以如果你有简单的程序编译概念，那么将很容易进行软件的安装。 甚至在发生软件编译过程中的错误时，你也可以自行作一些简易的修订呢！而最传统的软件安装过程， 自然就是由源代码编译而来的啰！所以，在这里我们将介绍最原始的软件管理方式：使用 Tarball 来安装与升级管理我们的软件喔！

## 21.1 开放源码的软件安装与升级简介

为了在 Linux 服务器上运行网页服务器 (WWW server) 这一服务，我们需要安装相应的软件。如果服务器上没有这个软件，自然无法启用该服务。学习如何安装软件是非常重要的。

### 21.1.1 什么是开放源码、编译器与可执行文件

在 Linux 系统上，一个文件是否可以执行，主要看它是否有执行权限 (x permission)。Linux 系统实际认识的是二进制文件 (binary program)，例如 `/usr/bin/passwd`、`/bin/touch` 等。这些文件通过编译器将源代码转换成机器可以识别的二进制格式。

#### 开放源码

开放源码指的是写给人类看的程序语言，但机器并不认识，需要通过编译器转译成机器可识别的二进制程序。

#### 编译器

编译器是将源代码转换成机器语言的工具。常见的编译器如 gcc，可以将 C 语言源代码编译成可执行文件。

#### 可执行文件

可执行文件是经过编译器处理后的二进制程序，机器可以直接执行。

### 21.1.2 什么是函数库

函数库 (library) 是一组可以被调用来执行特定功能的函数。函数库分为动态和静态两种，动态函数库在程序运行时加载，静态函数库在编译时直接链接到程序中。函数库使得程序可以重用现有的代码，而不必重新编写相同的功能。

### 21.1.3 什么是 make 与 configure

make 是一个自动化编译工具，利用 Makefile 文件定义的规则进行编译。configure 是一个自动化配置脚本，用于检测系统环境并生成适用于该环境的 Makefile。make 和 configure 共同作用，使得编译和安装软件变得简单高效。

### 21.1.4 什么是 Tarball 的软件

Tarball 是经过打包和压缩的源代码文件，通常扩展名为 `.tar.gz` 或 `.tgz`，也可能是 `.tar.bz2` 或 `.tar.xz`。Tarball 文件中包含原始程序码、配置脚本 (如 configure) 以及安装说明 (如 INSTALL 或 README)。

### 21.1.5 如何安装与升级软件

安装和升级软件的原因可能包括需要新功能、修补安全漏洞或提高性能。安装和升级软件的方法主要有两种：

1. **通过源代码编译安装**：下载 Tarball 文件，解压后通过配置和编译生成可执行文件，并进行安装。
2. **通过预编译的二进制程序安装**：使用软件包管理工具 (如 RPM 或 dpkg) 安装预先编译好的软件包，省略了配置和编译的步骤。

#### 安装 Tarball 软件的步骤

1. 下载 Tarball 文件。
2. 解压 Tarball 文件，生成源代码文件。
3. 运行 `./configure` 配置脚本生成 Makefile。
4. 运行 `make` 编译源代码，生成目标文件和可执行文件。
5. 运行 `make install` 安装可执行文件和相关配置文件。

通过这些步骤，可以将源代码编译安装到 Linux 系统中，实现软件的功能和更新。

### 小结

通过了解开放源码、编译器、可执行文件、函数库、make 和 configure 工具，以及 Tarball 软件的概念和安装步骤，我们可以更好地管理和升级 Linux 系统上的软件。这不仅提高了系统的安全性和性能，也使我们能够灵活地定制软件功能，满足特定需求。

## 21.2 使用传统程序语言进行编译的简单范例

通过上面的介绍，我们对源代码、编译器、函数库与可执行文件之间的相关性有了初步了解。接下来，通过实际的操作范例，更清楚地理解整个编译的过程。

### 21.2.1 单一程序：打印 Hello World

我们从一个简单的 C 语言程序开始，它会在屏幕上打印“Hello World！”。

#### 编写源代码

```
c复制代码#include <stdio.h>
int main(void)
{
    printf("Hello World\n");
}
```

将上述代码保存到 `hello.c` 文件中。可以从以下链接下载该文件：

- [hello.c](http://linux.vbird.org/linux_basic/0520source/hello.c)

#### 编译与执行

```
bash复制代码[root@study ~]# gcc hello.c
[root@study ~]# ll hello.c a.out
-rwxr-xr-x. 1 root root 8503 Sep  4 11:33 a.out
-rw-r--r--. 1 root root   71 Sep  4 11:32 hello.c

[root@study ~]# ./a.out
Hello World
```

默认情况下，`gcc` 会将编译后的可执行文件命名为 `a.out`。通过执行 `./a.out` 可以看到输出的“Hello World”。

#### 创建目标文件并指定输出文件名

```
bash复制代码[root@study ~]# gcc -c hello.c
[root@study ~]# ll hello*
-rw-r--r--. 1 root root   71 Sep  4 11:32 hello.c
-rw-r--r--. 1 root root 1496 Sep  4 11:34 hello.o

[root@study ~]# gcc -o hello hello.o
[root@study ~]# ll hello*
-rwxr-xr-x. 1 root root 8503 Sep  4 11:35 hello
-rw-r--r--. 1 root root   71 Sep  4 11:32 hello.c
-rw-r--r--. 1 root root 1496 Sep  4 11:34 hello.o

[root@study ~]# ./hello
Hello World
```

这样我们创建了目标文件 `hello.o`，并将其链接成可执行文件 `hello`。

### 21.2.2 主、副程序链接：副程序的编译

如果我们在主程序中调用了另一个副程序呢？下面通过一个例子演示如何编译和链接主程序和副程序。

#### 编写主、副程序

**主程序 `thanks.c`：**

```
c复制代码#include <stdio.h>
int main(void)
{
    printf("Hello World\n");
    thanks_2();
}
```

**副程序 `thanks_2.c`：**

```
c复制代码#include <stdio.h>
void thanks_2(void)
{
    printf("Thank you!\n");
}
```

可以从以下链接下载这些文件：

- [thanks.c](http://linux.vbird.org/linux_basic/0520source/thanks.c)
- [thanks_2.c](http://linux.vbird.org/linux_basic/0520source/thanks_2.c)

#### 编译与链接

```
bash复制代码[root@study ~]# gcc -c thanks.c thanks_2.c
[root@study ~]# ll thanks*
-rw-r--r--. 1 root root   75 Sep  4 11:43 thanks_2.c
-rw-r--r--. 1 root root 1496 Sep  4 11:43 thanks_2.o
-rw-r--r--. 1 root root   91 Sep  4 11:42 thanks.c
-rw-r--r--. 1 root root 1560 Sep  4 11:43 thanks.o

[root@study ~]# gcc -o thanks thanks.o thanks_2.o
[root@study ~]# ll thanks*
-rwxr-xr-x. 1 root root 8572 Sep  4 11:44 thanks

[root@study ~]# ./thanks
Hello World
Thank you!
```

通过编译生成目标文件 `thanks.o` 和 `thanks_2.o`，再将它们链接成可执行文件 `thanks`。

### 21.2.3 调用外部函数库：加入链接的函数库

编写一个程序来计算数学公式，例如计算三角函数的 sin（90度）。

#### 编写源代码

```
c复制代码#include <stdio.h>
#include <math.h>
int main(void)
{
    float value;
    value = sin(3.14 / 2);
    printf("%f\n", value);
}
```

可以从以下链接下载该文件：

- [sin.c](http://linux.vbird.org/linux_basic/0520source/sin.c)

#### 编译时加入额外函数库

```
bash复制代码[root@study ~]# gcc sin.c -lm
[root@study ~]# ./a.out
1.000000
```

在编译时加入 `-lm` 参数，表示链接数学函数库 `libm.so`。

### 21.2.4 gcc 的简易用法

以下是 `gcc` 的一些常见用法：

- **编译源代码生成目标文件**：

  ```
  bash
  复制代码
  [root@study ~]# gcc -c hello.c
  ```

- **进行最优化编译**：

  ```
  bash
  复制代码
  [root@study ~]# gcc -O hello.c -c
  ```

- **链接函数库和相关路径**：

  ```
  bash
  复制代码
  [root@study ~]# gcc sin.c -lm -L/lib -I/usr/include
  ```

- **指定输出文件名**：

  ```
  bash
  复制代码
  [root@study ~]# gcc -o hello hello.c
  ```

- **输出详细编译信息**：

  ```
  bash
  复制代码
  [root@study ~]# gcc -o hello hello.c -Wall
  ```

通过这些命令，我们可以灵活地使用 `gcc` 编译和优化我们的 C 程序。

通过上述几个简单的范例，我们对使用 `gcc` 编译源代码、生成目标文件、链接函数库以及生成可执行文件的过程有了更深入的理解。接下来，我们可以继续学习更复杂的编译和链接过程，以及如何处理更多的编译选项和优化参数。

## 21.3 用 make 进行宏编译

### 21.3.1 为什么要用 make

假设我们有四个源代码文件，分别是 `main.c`、`haha.c`、`sin_value.c` 和 `cos_value.c`。这些文件的功能如下：

-   `main.c`：处理用户输入角度数据并调用其他三个副程序；
-   `haha.c`：输出一些信息；
-   `sin_value.c`：计算输入角度（360度）的正弦值；
-   `cos_value.c`：计算输入角度（360度）的余弦值。

可以从[这里](http://linux.vbird.org/linux_basic/0520source/main.tgz)下载这些文件。

我们需要以下步骤来编译这些文件：

1.  编译目标文件：
    
    bash
    
    复制代码
    
    `[root@study ~]# gcc -c main.c [root@study ~]# gcc -c haha.c [root@study ~]# gcc -c sin_value.c [root@study ~]# gcc -c cos_value.c`
    
2.  链接成可执行文件：
    
    bash
    
    复制代码
    
    `[root@study ~]# gcc -o main main.o haha.o sin_value.o cos_value.o -lm`
    
3.  执行程序：
    
    bash
    
    复制代码
    
    `[root@study ~]# ./main Please input your name: VBird  <==输入名字 Please enter the degree angle （ex> 90）: 30   <==输入角度 Hi, Dear VBird, nice to meet you. The Sin is:  0.50 The Cos is:  0.87`
    

通过 `make` 可以简化这些步骤。创建一个名为 `makefile` 的文件，内容如下：

makefile

复制代码

`main: main.o haha.o sin_value.o cos_value.o     gcc -o main main.o haha.o sin_value.o cos_value.o -lm`

注意：第二行的 `gcc` 前面必须有 `<tab>`。

执行 `make` 命令：

bash

复制代码

`[root@study ~]# rm -f main *.o [root@study ~]# make`

`make` 会自动读取 `makefile` 并执行编译。再次执行 `make` 命令：

bash

复制代码

``[root@study ~]# make make: `main' is up to date.``

如果创建一个 shell script 来包含这些命令，虽然可以实现同样的功能，但 `make` 会根据文件的更新情况自动判断是否需要重新编译，这点更为高效。

### 21.3.2 makefile 的基本语法与变量

基本的 `makefile` 规则如下：

makefile

复制代码

`目标: 依赖文件 <tab>命令`

例如：

makefile

复制代码

`main: main.o haha.o sin_value.o cos_value.o     gcc -o main main.o haha.o sin_value.o cos_value.o -lm`

#### 使用变量简化 makefile

我们可以使用变量来简化 `makefile`：

makefile

复制代码

`LIBS = -lm OBJS = main.o haha.o sin_value.o cos_value.o main: ${OBJS}     gcc -o main ${OBJS} ${LIBS} clean:     rm -f main ${OBJS}`

使用变量的语法：

1.  变量与变量内容以 `=` 隔开，两边可以有空格；
2.  变量左边不可以有 `<tab>`；
3.  变量与变量内容在 `=` 两边不能有 `:`；
4.  变量名最好使用大写字母；
5.  运用变量时，以 `${变量}` 或 `$（变量）` 使用；
6.  Shell 环境变量可以被使用，例如 `CFLAGS`；
7.  可以在命令行界面给予变量。

#### 特殊变量

-   `$@`：代表当前的目标。

例如：

makefile

复制代码

`LIBS = -lm OBJS = main.o haha.o sin_value.o cos_value.o CFLAGS = -Wall main: ${OBJS}     gcc -o $@ ${OBJS} ${LIBS} clean:     rm -f main ${OBJS}`

通过这些步骤，我们可以更好地利用 `make` 和 `makefile` 来管理和简化我们的编译过程。

## 21.4 Tarball 的管理与建议

### 21.4.1 使用源代码管理软件所需要的基础软件

为了管理和编译源代码，需要安装以下基础软件：

- **gcc 或 cc 等 C 语言编译器**：没有编译器无法进行编译，通常使用 GNU 的 gcc。
- **make 及 autoconfig 等软件**：用于简化编译过程，自动生成 Makefile。
- **Kernel 提供的 Library 及相关的 Include 文件**：核心提供的函数库和 include 文件对软件相容性非常重要，特别是在安装驱动程序时。

许多 Linux 发行版默认没有安装这些软件，用户需要手动安装。可以通过包管理器如 yum 来安装，例如：

```
bash复制代码yum groupinstall "Development Tools"
yum groupinstall "X Software Development"
yum groupinstall "Legacy Software Development"
```

### 21.4.2 Tarball 安装的基本步骤

Tarball 的安装通常包含以下步骤：

1. **取得原始文件**：将 Tarball 文件解压缩到 /usr/local/src 目录。
2. **查阅文档**：阅读 INSTALL 和 README 文件，了解安装步骤和要求。
3. **安装依赖软件**：根据文档安装必要的依赖软件。
4. **创建 Makefile**：使用 configure 或 config 生成 Makefile。
5. **编译**：运行 make 编译源代码。
6. **安装**：运行 make install 安装编译后的文件。

示例：

```
bash复制代码cd /usr/local/src
tar -zxvf /path/to/your/tarball.tar.gz
cd extracted_directory
./configure --prefix=/desired/install/path
make clean
make
make install
```

### 21.4.3 一般 Tarball 软件安装的建议事项

- **解压缩到 /usr/local/src**：保持源代码在一个统一的位置，方便管理。
- **安装到 /usr/local**：将软件安装到 /usr/local 下，可以与系统默认的软件区分开。
- **单独目录安装**：每个软件安装在独立的目录，如 /usr/local/software_name，这样便于管理和移除。
- **man page 设置**：将 man page 路径加入到系统的 man path 配置中。

示例：

```
bash复制代码vi /etc/man_db.conf
# 在合适的位置添加以下行：
MANPATH_MAP /usr/local/software/bin /usr/local/software/man
```

### 21.4.4 一个简单的范例：利用 ntp 来示范

以下是安装 ntp 软件的示例：

1. **解压缩**：

   ```
   bash复制代码cd /usr/local/src
   tar -zxvf /root/ntp-4.2.8p3.tar.gz
   cd ntp-4.2.8p3
   ```

2. **查阅文档**：

   ```
   bash
   复制代码
   less INSTALL
   ```

3. **检查 configure 支持参数**：

   ```
   bash
   复制代码
   ./configure --help | more
   ```

4. **创建 Makefile**：

   ```
   bash
   复制代码
   ./configure --prefix=/usr/local/ntp --enable-all-clocks --enable-parse-clocks
   ```

5. **编译与安装**：

   ```
   bash复制代码make clean
   make
   make install
   ```

### 21.4.5 利用 patch 更新源代码

更新源代码可以使用 patch 文件，只需更改有修订的文件部分。以下是一个简单的示例：

1. **下载和解压缩旧版程序**：

   ```
   bash复制代码tar -zxvf main-0.1.tgz
   cd main-0.1
   ```

2. **查看 patch 文件内容**：

   ```
   bash
   复制代码
   vim ~/main_0.1_to_0.2.patch
   ```

3. **应用 patch 文件**：

   ```
   bash
   复制代码
   patch -p1 < ../main_0.1_to_0.2.patch
   ```

4. **重新编译和测试**：

   ```
   bash复制代码make clean main
   ./main
   ```

使用 patch 可以节省下载和编译时间，仅需更新更改的部分，保持其他部分不变。

通过这些步骤，您可以有效地管理和安装 Tarball 软件，简化升级和维护过程。

## 21.5 函数库管理

在 Linux 操作系统中，函数库是非常重要的组件。很多软件相互依赖函数库来实现特定功能。例如，许多需要身份验证的程序使用 PAM 模块，网络连接则常常使用 SSL 函数库来实现加密。因此，函数库的管理和使用非常关键。函数库分为动态函数库和静态函数库，它们各有特点和用途。

### 21.5.1 动态与静态函数库

#### 静态函数库

- **扩展名**：通常为 `.a`，例如 `libxxx.a`。
- **编译行为**：编译时，静态函数库会直接整合到可执行文件中，因此可执行文件较大。
- **独立执行状态**：可执行文件可以独立执行，无需外部函数库。
- **升级难易度**：由于函数库被直接整合到可执行文件中，函数库升级时，需要重新编译所有相关的可执行文件。

#### 动态函数库

- **扩展名**：通常为 `.so`，例如 `libxxx.so`。
- **编译行为**：编译时，可执行文件中只包含一个指向动态函数库的指针，函数库内容未被整合到可执行文件中。
- **独立执行状态**：可执行文件不能独立执行，需要动态函数库文件存在且路径正确。
- **升级难易度**：动态函数库升级后，可执行文件无需重新编译，前提是新旧版本的函数库文件名相同。

### 21.5.2 函数库路径和缓存配置

#### 函数库路径

大部分函数库放置在 `/lib64` 和 `/lib` 目录下。内核提供的函数库放置在 `/lib/modules` 下。

#### 使用 ldconfig 和 /etc/ld.so.conf

为了提高函数库的读取性能，可以使用 `ldconfig` 将常用的动态函数库加载到内存中（高速缓存）。配置步骤如下：

1. **配置 /etc/ld.so.conf**：在此文件中添加动态函数库所在的目录。
2. **运行 ldconfig**：将配置文件中指定的函数库目录加载到缓存中，并记录到 `/etc/ld.so.cache`。

示例：

```
bash复制代码[root@study ~]# vim /etc/ld.so.conf.d/vbird.conf
/usr/lib64/mysql   # 添加这一行

[root@study ~]# ldconfig  # 执行后不会有输出
[root@study ~]# ldconfig -p  # 查看已缓存的函数库
```

### 21.5.3 程序的动态函数库解析： ldd

`ldd` 命令用于查看可执行文件所依赖的动态函数库。使用示例如下：

```
bash复制代码[root@study ~]# ldd /usr/bin/passwd
        libpam.so.0 => /lib64/libpam.so.0 (0x00007f5e683dd000)
        libpam_misc.so.0 => /lib64/libpam_misc.so.0 (0x00007f5e681d8000)
        libaudit.so.1 => /lib64/libaudit.so.1 (0x00007f5e67fb1000)
        libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f5e67d8c000)
```

使用 `ldd` 可以方便地查看可执行文件依赖的所有动态函数库，帮助解决函数库依赖问题。

## 21.6 检验软件正确性

在安装和升级软件的过程中，我们常常需要从 Linux 发行版或软件开发商的网站下载最新且较安全的软件。然而，下载文件可能存在被篡改的风险，因此我们需要验证文件的正确性。这时，可以利用每个文件独特的指纹验证数据，如 MD5、SHA1 或更严密的 SHA256 等指纹验证机制来判断文件是否被修改。

### 21.6.1 md5sum / sha1sum / sha256sum

MD5、SHA1 和 SHA256 是广泛使用的文件指纹验证机制。它们通过计算文件的指纹码来确认文件的完整性。以下是如何使用这些工具来验证文件的正确性。

#### 示例：验证 NTP 软件的指纹

我们以之前下载的 NTP 软件 `ntp-4.2.8p3.tar.gz` 为例，官网提供的 MD5 数据如下：

```
复制代码
b98b0cbb72f6df04608e1dd5f313808b  ntp-4.2.8p3.tar.gz
```

验证下载的文件：

```
bash复制代码[root@study ~]# md5sum ntp-4.2.8p3.tar.gz
b98b0cbb72f6df04608e1dd5f313808b  ntp-4.2.8p3.tar.gz
```

显示的编码应与官网提供的相同，表示文件没有被修改。

#### 用法

```
bash复制代码[root@study ~]# md5sum/sha1sum/sha256sum [-bct] filename
[root@study ~]# md5sum/sha1sum/sha256sum [--status|--warn] --check filename
选项与参数：
-b ：使用 binary 的读档方式，默认为 Windows/DOS 文件型态的读取方式；
-c ：检验文件指纹；
-t ：以文字体态来读取文件指纹。
```

#### 创建指纹数据库

为了保护系统中的重要文件，可以为这些文件创建指纹数据库，常见的文件包括：

- `/etc/passwd`
- `/etc/shadow`
- `/etc/group`
- `/usr/bin/passwd`
- `/sbin/rpcbind`
- `/bin/login`
- `/bin/ls`
- `/bin/ps`
- `/bin/top`

创建指纹数据库后，可以定期使用 shell 脚本检查这些文件的指纹是否发生变化，以确保文件系统的安全。

#### 示例：创建指纹数据库并进行验证

1. **创建指纹数据库**

```
bash
复制代码
[root@study ~]# md5sum /etc/passwd /etc/shadow /etc/group /usr/bin/passwd /sbin/rpcbind /bin/login /bin/ls /bin/ps /bin/top > /root/file_check.md5
```

1. **验证文件完整性**

```
bash复制代码[root@study ~]# md5sum -c /root/file_check.md5
/etc/passwd: OK
/etc/shadow: OK
/etc/group: OK
/usr/bin/passwd: OK
/sbin/rpcbind: OK
/bin/login: OK
/bin/ls: OK
/bin/ps: OK
/bin/top: OK
```

通过以上步骤，可以确保重要文件没有被篡改，从而增强系统的安全性。

## 21.7 重点回顾

- 源代码其实大多是纯文本文件，需要通过编译器的编译动作后，才能够制作出 Linux 系统能够认识的可执行的 binary file ；
- 开放源代码可以加速软件的更新速度，让软件性能更快、漏洞修补更实时；
- 在 Linux 系统当中，最标准的 C 语言编译器为 gcc ；
- 在编译的过程当中，可以借由其他软件提供的函数库来使用该软件的相关机制与功能；
- 为了简化编译过程当中的复杂的指令输入，可以借由 make 与 makefile 规则定义，来简化程序的更新、编译与链接等动作；
- Tarball 为使用 tar 与 gzip/bzip2/xz 压缩功能所打包与压缩的，具有源代码的文件；
- 一般而言，要使用 Tarball 管理 Linux 系统上的软件，最好需要 gcc, make, autoconfig, kernel source, kernel header 等前驱软件才行，所以在安装 Linux 之初，最好就能够选择 Software development 以及 kernel development 之类的群组；
- 函数库有动态函数库与静态函数库，动态函数库在升级上具有较佳的优势。动态函数库的扩展名为 *.so 而静态则是* .a ；
- patch 的主要功能在更新源代码，所以更新源代码之后，还需要进行重新编译的动作才行；
- 可以利用 ldconfig 与 /etc/ld.so.conf /etc/ld.so.conf.d/*.conf 来制作动态函数库的链接与高速缓存！
- 通过 MD5/SHA1/SHA256 的编码可以判断下载的文件是否为原本厂商所释出的文件。