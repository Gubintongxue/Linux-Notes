# 第十一章、正则表达式与文件格式化处理

最近更新日期：20//

正则表达式 （Regular Expression, RE, 或称为常规表达式）是通过一些特殊字符的排列，用以“搜寻/取代/删除”一列或多列文字字串， 简单的说，正则表达式就是用在字串的处理上面的一项“表示式”。正则表达式并不是一个工具程序， 而是一个字串处理的标准依据，如果您想要以正则表达式的方式处理字串，就得要使用支持正则表达式的工具程序才行， 这类的工具程序很多，例如 vi, sed, awk 等等。

正则表达式对于系统管理员来说实在是很重要！因为系统会产生很多的讯息，这些讯息有的重要有的仅是告知， 此时，管理员可以通过正则表达式的功能来将重要讯息撷取出来，并产生便于查阅的报表来简化管理流程。此外， 很多的套装软件也都支持正则表达式的分析，例如邮件服务器的过滤机制（过滤垃圾信件）就是很重要的一个例子。 所以，您最好要了解正则表达式的相关技能，在未来管理主机时，才能够更精简处理您的日常事务！

本章节使用者需要多加练习，因为目前很多的套件都是使用正则表达式来达成其“过滤、分析”的目的， 为了未来主机管理的便利性，使用者至少要能看的懂正则表达式的意义！



## 11.1 开始之前：什么是正则表达式

在深入学习 Linux 的基本指令（BASH）并且熟悉了 vim 之后，接下来要介绍一个非常重要的概念，即正则表达式（Regular Expression）。

### 什么是正则表达式

正则表达式是一种用于处理字符串的方法，通过使用特殊符号，可以轻松实现字符串的搜索、删除和替换操作。例如，你想要找到 "VBird" 或 "Vbird" 这样的字符串，而不希望匹配到 "VBIRD" 或 "vbird"。正则表达式可以帮助你准确匹配到你想要的结果。

### 正则表达式的用途

正则表达式在系统管理中有广泛的应用。例如，当系统在启动时遇到邮件程序的错误，你可以使用正则表达式来快速找到包含错误信息的文件。在 `/lib/systemd/system/` 目录下搜索包含 "mail" 的文件，只需使用以下命令：

```
bash
复制代码
grep 'mail' /lib/systemd/system/*
```

`grep` 是支持正则表达式的工具程序之一，使用它可以轻松完成字符串的搜索。

### 正则表达式对系统管理员的重要性

对于系统管理员来说，正则表达式是不可或缺的工具。系统每天会生成大量的日志信息，通过正则表达式，可以快速提取出重要的错误信息，极大地提高工作效率。例如，可以使用正则表达式从大量的系统日志中提取出异常信息，帮助快速定位问题。

### 正则表达式的广泛应用

除了系统管理，正则表达式在邮件服务器等软件中也有广泛应用。例如，邮件服务器可以使用正则表达式来过滤垃圾邮件，根据特定的关键词来筛选并剔除不需要的邮件，从而减少网络带宽的浪费。

### 正则表达式与 Shell 在 Linux 中的角色定位

正则表达式在 Linux 中的地位类似于数学中的九九乘法表，是基础中的基础。尽管学习起来可能有些困难，但一旦掌握，将极大地提升你的工作效率和操作技能。

### 延伸的正则表达式

正则表达式根据不同的严格程度分为基础正则表达式和延伸正则表达式。延伸正则表达式支持更复杂的字符串处理，如使用括号和竖线进行分组和或操作。虽然本章主要介绍基础正则表达式，但了解延伸正则表达式的存在和用途也很重要。

![鸟哥的图示](image/vbird_face-17222688759852.gif)

**Tips** 正则表达式与万用字符（wildcard）是完全不同的概念。万用字符是 BASH 操作接口的功能，而正则表达式是一种字符串处理表示方式。学习本章时，请先暂时忘记万用字符的意义，专注于正则表达式的学习。

------

正则表达式是 Linux 系统管理中的重要工具，通过学习正则表达式，可以显著提高系统管理和文本处理的效率。掌握正则表达式，将使你的 Linux 技能更上一层楼。

**原文**：

约略了解了 Linux 的基本指令 （[BASH](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)） 并且熟悉了 [vim](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 之后，相信你对于敲击键盘的打字与指令下达比较不陌生了吧？ 接下来，下面要开始介绍一个很重要的观念，那就是所谓的“正则表达式 （Regular Expression）”啰！

- 什么是正则表达式

任何一个有经验的系统管理员，都会告诉你：“正则表达式真是挺重要的！” 为什么很重要呢？因为日常生活就使用的到啊！举个例子来说， 在你日常使用 [vim](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 作文书处理或程序撰写时使用到的“搜寻/取代”等等的功能， 这些举动要作的漂亮，就得要配合正则表达式来处理啰！

简单的说，正则表达式就是处理字串的方法，他是以行为单位来进行字串的处理行为， 正则表达式通过一些特殊符号的辅助，可以让使用者轻易的达到“搜寻/删除/取代”某特定字串的处理程序！

举例来说，我只想找到 VBird（前面两个大写字符） 或 Vbird（仅有一个大写字符） 这个字样，但是不要其他的字串 （例如 VBIRD, vbird 等不需要），该如何办理？如果在没有正则表达式的环境中（例如 MS word），你或许就得要使用忽略大小写的办法， 或者是分别以 VBird 及 Vbird 搜寻两遍。但是，忽略大小写可能会搜寻到 VBIRD/vbird/VbIrD 等等的不需要的字串而造成困扰。

再举个系统常见的例子好了，假设你发现系统在开机的时候，老是会出现一个关于 mail 程序的错误， 而开机过程的相关程序都是在 /lib/systemd/system/ 下面，也就是说，在该目录下面的某个文件内具有 mail 这个关键字，你想要将该文件捉出来进行查询修改的动作。此时你怎么找出来含有这个关键字的文件？ 你当然可以一个文件一个文件的打开，然后去搜寻 mail 这个关键字，只是.....该目录下面的文件可能不止 100 个说～ 如果了解正则表达式的相关技巧，那么只要一行指令就找出来啦：“grep 'mail' /lib/systemd/system/*” 那个 grep 就是支持正则表达式的工具程序之一！如何～很简单吧！

谈到这里就得要进一步说明了，正则表达式基本上是一种“表达式”， 只要工具程序支持这种表达式，那么该工具程序就可以用来作为正则表达式的字串处理之用。 例如 vi, grep, awk ,sed 等等工具，因为她们有支持正则表达式， 所以，这些工具就可以使用正则表达式的特殊字符来进行字串的处理。但例如 cp, ls 等指令并未支持正则表达式， 所以就只能使用 [bash 自己本身的万用字符](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#settings_wildcard)而已。

- 正则表达式对于系统管理员的用途

那么为何我需要学习正则表达式呢？对于一般使用者来说，由于使用到正则表达式的机会可能不怎么多， 因此感受不到他的魅力，不过，对于身为系统管理员的你来说，正则表达式则是一个“不可不学的好东西！” 怎么说呢？由于系统如果在繁忙的情况之下，每天产生的讯息信息会多到你无法想像的地步， 而我们也都知道，系统的“[错误讯息登录文件 （第十八章）](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html)” 的内容记载了系统产生的所有讯息，当然，这包含你的系统是否被“入侵”的记录数据。

但是系统的数据量太大了，要身为系统管理员的你每天去看这么多的讯息数据， 从千百行的数据里面找出一行有问题的讯息，呵呵～光是用肉眼去看，想不疯掉都很难！ 这个时候，我们就可以通过“正则表达式”的功能，将这些登录的信息进行处理， 仅取出“有问题”的信息来进行分析，哈哈！如此一来，你的系统管理工作将会 “快乐得不得了”啊！当然，正则表达式的优点还不止于此，等你有一定程度的了解之后，你会爱上他喔！

- 正则表达式的广泛用途

正则表达式除了可以让系统管理员管理主机更为便利之外，事实上，由于正则表达式强大的字串处理能力， 目前一堆软件都支持正则表达式呢！最常见的就是“邮件服务器”啦！

如果你留意网际网络上的消息，那么应该不难发现，目前造成网络大塞车的主因之一就是“垃圾/广告信件”了， 而如果我们可以在服务器端，就将这些问题邮件剔除的话，用户端就会减少很多不必要的带宽耗损了。 那么如何剔除广告信件呢？由于广告信件几乎都有一定的标题或者是内容，因此， 只要每次有来信时，都先将来信的标题与内容进行特殊字串的比对，发现有不良信件就予以剔除！ 嘿！这个工作怎么达到啊？就使用正则表达式啊！目前两大邮件服务器软件 sendmail 与 postfix 以及支持邮件服务器的相关分析软件，都支持正则表达式的比对功能！

当然还不止于此啦，很多的服务器软件都支持正则表达式呢！当然， 虽然各家软件都支持他，不过，这些“字串”的比对还是需要系统管理员来加入比对规则的， 所以啦！身为系统管理员的你，为了自身的工作以及用户端的需求， 正则表达式实在是很需要也很值得学习的一项工具呢！

- 正则表达式与 Shell 在 Linux 当中的角色定位

说实在的，我们在学数学的时候，一个很重要、但是粉难的东西是一定要“背”的， 那就是九九乘法表，背成功了之后，未来在数学应用的路途上，真是一帆风顺啊！ 这个九九乘法表我们在小学的时候几乎背了一整年才背下来，并不是这么好背的呢！ 但他却是基础当中的基础！你现在一定受惠相当的多呢 ^_^！

而我们谈到的这个正则表达式，与前一章的 [BASH](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 就有点像是数学的九九乘法表一样，是 Linux 基础当中的基础，虽然也是最难的部分， 不过，如果学成了之后，一定是“大大的有帮助”的！这就好像是金庸小说里面的学武难关：任督二脉！ 打通任督二脉之后，武功立刻成倍成长！所以啦， 不论是对于系统的认识与系统的管理部分，他都有很棒的辅助啊！请好好的学习这个基础吧！ ^_^

- 延伸的正则表达式

唔！正则表达式还有分喔？没错喔！正则表达式的字串表示方式依照不同的严谨度而分为： 基础正则表达式与延伸正则表达式。延伸型正则表达式除了简单的一组字串处理之外，还可以作群组的字串处理， 例如进行搜寻 VBird 或 netman 或 lman 的搜寻，注意，是“或（or）”而不是“和（and）”的处理， 此时就需要延伸正则表达式的帮助啦！借由特殊的“ （ ”与“ | ”等字符的协助， 就能够达到这样的目的！不过，我们在这里主力仅是介绍最基础的基础正则表达式而已啦！好啦！清清脑门，咱们用功去啰！

![鸟哥的图示](image/vbird_face.gif)

**Tips** 有一点要向大家报告的，那就是：“**正则表达式与万用字符是完全不一样的东西！**” 这很重要喔！因为“万用字符 （wildcard） 代表的是 bash 操作接口的一个功能”，但正则表达式则是一种字串处理的表示方式！ 这两者要分的很清楚才行喔！所以，学习本章，请将前一章 bash 的万用字符意义先忘掉吧！

老实说，鸟哥以前刚接触正则表达式时，老想着要将这两者归纳在一起，结果就是...错误认知一大堆～ 所以才会建议您学习本章先忘记万用字符再来学习吧！

### 11.2 基础正则表达式

正则表达式是一种处理字符串的表示方式，它依赖于字符排序的语系数据，因此不同的语系会影响正则表达式的结果。此外，正则表达式需要工具程序的支持。这里我们先介绍一个简单的字符串提取工具程序——`grep`。前一章已经介绍了 `grep` 的基本选项和参数，这里着重介绍较进阶的 `grep` 选项。

#### 11.2.1 语系对正则表达式的影响

语系数据会影响正则表达式的输出结果，因为文件记录的仅有 0 和 1，通过编码表转换成我们看到的字符。不同语系的编码数据不同，导致数据提取结果差异。例如，英文大小写的编码顺序在 `zh_TW.big5` 和 `C` 这两种语系的输出结果不同：

- `LANG=C` 时：0 1 2 3 4 ... A B C D ... Z a b c d ... z
- `LANG=zh_TW` 时：0 1 2 3 4 ... a A b B c C d D ... z Z

为了避免编码问题带来的影响，正则表达式中有一些特殊符号需要了解：

- `[:alnum:]`：代表英文大小写字符及数字，即 0-9, A-Z, a-z
- `[:alpha:]`：代表任何英文大小写字符，即 A-Z, a-z
- `[:blank:]`：代表空白键与 Tab 按键
- `[:digit:]`：代表数字，即 0-9
- `[:lower:]`：代表小写字符，即 a-z
- `[:upper:]`：代表大写字符，即 A-Z
- `[:space:]`：任何会产生空白的字符，包括空白键, Tab, CR 等
- `[:xdigit:]`：代表 16 进位的数字类型，包括 0-9, A-F, a-f

#### 11.2.2 grep 的一些进阶选项

`grep` 的一些进阶用法如下：

```
bash
复制代码
grep [-A] [-B] [--color=auto] '搜索字符串' 文件名
```

- `-A`：后面可加数字，为 `after` 的意思，除了列出该行外，后续的 n 行也列出来。
- `-B`：后面可加数字，为 `before` 的意思，除了列出该行外，前面的 n 行也列出来。
- `--color=auto`：将正确的撷取数据列出颜色。

例子：

```
bash复制代码# 列出包含 qxl 的行
dmesg | grep 'qxl'
# 列出包含 qxl 的行，并加上行号和颜色
dmesg | grep -n --color=auto 'qxl'
# 列出包含 qxl 的行，前两行和后三行也显示出来
dmesg | grep -n -A3 -B2 --color=auto 'qxl'
```

#### 11.2.3 基础正则表达式练习

正则表达式的学习可以通过实际练习来感受，这里我们使用一个示例文件进行练习。假设文件内容如下：

```
text复制代码"Open Source" is a good mechanism to develop programs.
apple is my favorite food.
Football game is not use feet only.
this dress doesn't fit me.
However, this dress is about $ 3183 dollars.
GNU is free air not free beer.
Her hair is very beauty.
I can't finish the test.
Oh! The soup taste good.
motorcycle is cheap than car.
This window is clear.
the symbol '*' is represented as start.
Oh!     My god!
The gd software is a library for drafting programs.
You are the best is mean you are the no. 1.
The world <Happy> is the same with "glad".
I like dog.
google is the best tools for search keyword.
goooooogle yes!
go! go! Let's go.
# I am VBird
```

例题一：搜寻特定字串

```
bash复制代码# 搜寻包含 'the' 的行
grep -n 'the' regular_express.txt
# 反向选择，显示不包含 'the' 的行
grep -vn 'the' regular_express.txt
# 不区分大小写地搜索包含 'the' 的行
grep -in 'the' regular_express.txt
```

例题二：利用中括号 [] 来搜寻集合字符

```
bash复制代码# 搜寻包含 'test' 或 'taste' 的行
grep -n 't[ae]st' regular_express.txt
# 搜寻包含 'oo' 的行
grep -n 'oo' regular_express.txt
# 搜寻 'oo' 前面不为 'g' 的行
grep -n '[^g]oo' regular_express.txt
# 搜寻包含数字的行
grep -n '[0-9]' regular_express.txt
```

例题三：行首与行尾字符 ^ $

```
bash复制代码# 搜寻行首为 'the' 的行
grep -n '^the' regular_express.txt
# 搜寻行尾为 '.' 的行
grep -n '\.$' regular_express.txt
# 搜寻空白行
grep -n '^$' regular_express.txt
```

例题四：任意一个字符 . 与重复字符 *

```
bash复制代码# 搜寻 'g??d' 的字串，表示 g 和 d 之间有两个任意字符
grep -n 'g..d' regular_express.txt
# 搜寻至少两个 'o' 的字串
grep -n 'oo*' regular_express.txt
# 搜寻开头和结尾都是 'g' 且中间有至少一个 'o' 的字串
grep -n 'go\{2,\}g' regular_express.txt
```

例题五：限定连续 RE 字符范围 {}

```
bash复制代码# 搜寻 'g' 和 'g' 之间有 2 到 5 个 'o' 的字串
grep -n 'go\{2,5\}g' regular_express.txt
# 搜寻 'g' 和 'g' 之间有至少 2 个 'o' 的字串
grep -n 'go\{2,\}g' regular_express.txt
```

### 11.2.4 基础正则表达式字符汇整

以下是基础正则表达式的特殊字符：

- `^word`：待搜寻的字串在行首。例如：`grep -n '^#' regular_express.txt`
- `word$`：待搜寻的字串在行尾。例如：`grep -n '!$' regular_express.txt`
- `.`：代表“一定有一个任意字符”的字符。例如：`grep -n 'e.e' regular_express.txt`
- `\`：跳脱字符，去除特殊符号的特殊意义。例如：`grep -n \' regular_express.txt`
- `*`：重复零个到无穷多个的前一个 RE 字符。例如：`grep -n 'ess*' regular_express.txt`
- `[list]`：字符集合的 RE 字符。例如：`grep -n 'g[ld]' regular_express.txt`
- `[n1-n2]`：字符集合的 RE 字符范围。例如：`grep -n '[A-Z]' regular_express.txt`
- `[^list]`：字符集合的反向选择。例如：`grep -n 'oo[^t]' regular_express.txt`
- `{n,m}`：连续 n 到 m 个的前一个 RE 字符。例如：`grep -n 'go\{2,3\}g' regular_express.txt`

### 11.2.5 sed 工具

`sed` 是一个强大的流编辑器，可以分析标准输入的数据，并进行取代、删除、新增、提取特定行等操作。`sed` 的基本用法如下：

```
bash
复制代码
sed [-nefr] [动作]
```

选项与参数：

- `-n`：使用安静模式，只输出经过 `sed` 处理的行。
- `-e`：直接在命令行上编辑 `sed` 的动作。
- `-f`：从文件中读取 `sed` 的动作。
- `-r`：支持延伸型正则表达式的语法。
- `-i`：直接修改读取的文件内容。

常见动作：

- `a`：新增。
- `c`：取代。
- `d`：删除。
- `i`：插入。
- `p`：打印。
- `s`：取代。

例子：

```
bash复制代码# 删除第 2 到第 5 行
nl /etc/passwd | sed '2,5d'
# 在第二行后新增 "drink tea?"
nl /etc/passwd | sed '2a drink tea'
# 将第 2 到第 5 行替换成 "No 2-5 number"
nl /etc/passwd | sed '2,5c No 2-5 number'
# 仅列出第 5 到第 7 行
nl /etc/passwd | sed -n '5,7p'
# 搜寻并取代，将行尾的 '.' 换成 '!'
sed -i 's/\.$/\!/g' regular_express.txt
# 在最后一行新增 "# This is a test"
sed -i '$a # This is a test' regular_express.txt
```

通过这些示例，可以更好地理解正则表达式和 `sed` 的强大功能。正则表达式和 `sed` 是 Linux 系统管理中的重要工具，掌握它们可以极大地提高工作效率。

### 11.3 延伸正则表达式

一般情况下，基础正则表达式已经足够使用了，但为了简化操作，了解延伸型正则表达式的表示式会更方便。例如，去除空白行与行首为 # 的行列，基础正则表达式需要使用两次 `grep` 命令：

```
bash
复制代码
grep -v '^$' regular_express.txt | grep -v '^#'
```

而使用延伸型正则表达式可以简化为一次操作：

```
bash
复制代码
egrep -v '^$|^#' regular_express.txt
```

延伸型正则表达式通过群组功能 `|` 来进行一次搜寻。该符号在单引号内的意义为“或 (or)”。使用 `egrep` 代替 `grep -E` 更直观，虽然两者功能相同。下面是延伸型正则表达式的几个重要符号及其示例。

#### RE 字符及其意义与范例

1. `+` ：重复“一个或一个以上”的前一个 RE 字符。

   - 范例：搜寻 `(god)` `(good)` `(goood)` 等等字串。`o+` 代表“一个以上的 `o`”，所以执行结果会列出第 1、9、13 行。

   - 命令：

     ```
     bash
     复制代码
     egrep -n 'go+d' regular_express.txt
     ```

2. `?` ：“零个或一个”的前一个 RE 字符。

   - 范例：搜寻 `(gd)` `(god)` 两个字串。`o?` 代表“空的或 1 个 `o`”，所以执行结果会列出第 13、14 行。这两个案例（`go+d` 与 `go?d`）的结果集合与 `go*d` 相同。

   - 命令：

     ```
     bash
     复制代码
     egrep -n 'go?d' regular_express.txt
     ```

3. `|` ：用或（or）的方式找出数个字串。

   - 范例：搜寻 `(gd)` 或 `(good)` 两个字串，所以第 1、9、14 行都可以被打印出来。要找出 `(dog)` 也可以加上。

   - 命令：

     ```
     bash复制代码egrep -n 'gd|good' regular_express.txt
     egrep -n 'gd|good|dog' regular_express.txt
     ```

4. `()` ：找出“群组”字串。

   - 范例：搜寻 `(glad)` 或 `(good)` 两个字串。因为 `g` 与 `d` 是重复的，可以将 `la` 与 `oo` 列于 `()` 当中，并以 `|` 分隔开来。

   - 命令：

     ```
     bash
     复制代码
     egrep -n 'g(la|oo)d' regular_express.txt
     ```

5. `()+` ：多个重复群组的判别。

   - 范例：将 `AxyzxyzxyzxyzC` 用 `echo` 叫出，然后使用如下的方法搜寻。

   - 命令：

     ```
     bash
     复制代码
     echo 'AxyzxyzxyzxyzC' | egrep 'A(xyz)+C'
     ```

上面的例子表示要找开头是 `A` 结尾是 `C`，中间有一个以上的 "xyz" 字串。

此外，要注意，正则表达式中的 `!` 并不是特殊字符，所以如果要查找文件中含有 `!` 与 `>` 的字行，可以这样做：

```
bash
复制代码
grep -n '[!>]' regular_express.txt
```

常见的陷阱题目：“反向选择这样对否？`[!a-z]`？”是错误的，正确的是 `[a-z]`。

延伸型正则表达式的这些符号与基础正则表达式结合使用，可以大大提高字符串处理的效率。更多关于正则表达式的进阶文章，请参考相关文献和资源。

## 11.4 文件的格式化与相关处理

接下来让我们来将文件进行一些简单的编排吧！下面这些动作可以将你的信息进行排版，而无需重新用 vim 编辑。通过数据流重导向配合 printf 和 awk 指令，可以让你的信息以你想要的方式输出。

### 11.4.1 格式化打印：printf

在很多时候，我们可能需要将自己的数据格式化输出。例如，考试成绩的输出，可以按照一定的格式排列。以下是一个示例：

```
plaintext复制代码Name     Chinese   English   Math    Average
DmTsai        80        60     92      77.33
VBird         75        55     80      70.00
Ken           60        90     70      73.33
```

上表的数据主要分成五个字段，各字段之间可以使用 tab 或空白键进行分隔。请将上表的数据保存为 printf.txt 文件，我们会利用这个文件进行几个小练习。

```
bash
复制代码
printf '打印格式' 实际内容
```

格式化方面的几个特殊样式：

- `\a` 警告声音输出
- `\b` 倒退键（backspace）
- `\f` 清除屏幕（form feed）
- `\n` 输出新的一行
- `\r` 亦即 Enter 按键
- `\t` 水平的 [tab] 按键
- `\v` 垂直的 [tab] 按键
- `\xNN` NN 为两位数的数字，可以转换数字成为字符

C 程序语言内常见的变量格式：

- `%ns` n 是数字，s 代表 string ，亦即多少个字符
- `%ni` n 是数字，i 代表 integer ，亦即多少整数码数
- `%N.nf` N 和 n 都是数字，f 代表 floating（浮点），如果有小数码数，假设我共要十个位数，但小数点有两位，即为 `%10.2f`

#### 范例练习

**范例一：将 printf.txt 的内容以 [tab] 分隔**

```
bash
复制代码
printf '%s\t %s\t %s\t %s\t %s\t \n' $(cat printf.txt)
```

输出：

```
plaintext复制代码Name     Chinese         English         Math    Average
DmTsai   80      60      92      77.33
VBird    75      55      80      70.00
Ken      60      90      70      73.33
```

**范例二：将数据固定字段长度的显示**

```
bash
复制代码
printf '%10s %5i %5i %5i %8.2f \n' $(cat printf.txt | grep -v Name)
```

输出：

```
plaintext复制代码    DmTsai    80    60    92    77.33
     VBird    75    55    80    70.00
       Ken    60    90    70    73.33
```

**范例三：将 16 进位数值 45 代表的字符显示出来**

```
bash
复制代码
printf '\x45\n'
```

输出：

```
plaintext
复制代码
E
```

### 11.4.2 awk：好用的数据处理工具

awk 是一个非常棒的数据处理工具！相较于 sed 常用于处理整行数据，awk 更倾向于处理每行数据的字段。awk 的基本运行模式如下：

```
bash
复制代码
awk '条件类型1{动作1} 条件类型2{动作2} ...' filename
```

awk 的分隔符默认是空白键或 [tab] 键。以下是一些练习示例：

**范例：取出登陆者的帐号与 IP**

```
bash
复制代码
last -n 5 | awk '{print $1 "\t" $3}'
```

输出：

```
plaintext复制代码dmtsai  192.168.1.100
dmtsai  192.168.1.100
dmtsai  192.168.1.100
dmtsai  192.168.1.100
dmtsai  Fri
```

**awk 的内置变量：**

- `NF`：每一行拥有的字段总数
- `NR`：目前 awk 处理的是第几行数据
- `FS`：当前的分隔字符，默认是空白键

**范例：列出每一行的帐号、行数和字段数**

```
bash
复制代码
last -n 5 | awk '{print $1 "\t lines: " NR "\t columns: " NF}'
```

输出：

```
plaintext复制代码dmtsai   lines: 1        columns: 10
dmtsai   lines: 2        columns: 10
dmtsai   lines: 3        columns: 10
dmtsai   lines: 4        columns: 10
dmtsai   lines: 5        columns: 9
```

**逻辑运算符：**

- `>`：大于
- `<`：小于
- `>=`：大于或等于
- `<=`：小于或等于
- `==`：等于
- `!=`：不等于

**范例：查找 /etc/passwd 中 UID 小于 10 的账户**

```
bash
复制代码
cat /etc/passwd | awk 'BEGIN {FS=":"} $3 < 10 {print $1 "\t " $3}'
```

输出：

```
plaintext复制代码root     0
bin      1
daemon   2
...
```

**计算薪资总额**

```
bash复制代码cat pay.txt | \
awk 'NR==1{printf "%10s %10s %10s %10s %10s\n",$1,$2,$3,$4,"Total" }
NR>=2{total = $2 + $3 + $4
printf "%10s %10d %10d %10d %10.2f\n", $1, $2, $3, $4, total}'
```

输出：

```
plaintext复制代码      Name        1st        2nd        3th      Total
     VBird      23000      24000      25000   72000.00
    DMTsai      21000      20000      23000   64000.00
     Bird2      43000      42000      41000  126000.00
```

### 11.4.3 文件比对工具

文件比对工具常用来比较同一个软件不同版本的配置文件和原始文件的差异。最常见的文件比对工具有 `diff` 和 `cmp`。

#### diff

diff 用于比对两个文件之间的差异，以行为单位。

**范例：比对 passwd.old 与 passwd.new 的差异**

```
bash
复制代码
diff passwd.old passwd.new
```

输出：

```
plaintext复制代码4d3
< adm:x:3:4:adm:/var/adm:/sbin/nologin
6c5
< sync:x:5:0:sync:/sbin:/bin/sync
---
> no six line
```

#### cmp

cmp 用于比对两个文件的字节差异。

**范例：用 cmp 比较 passwd.old 和 passwd.new**

```
bash
复制代码
cmp passwd.old passwd.new
```

输出：

```
plaintext
复制代码
passwd.old passwd.new differ: char 106, line 4
```

#### patch

patch 指令与 diff 密不可分。它通过补丁文件来更新旧文件。

**范例：制作并应用补丁文件**

制作补丁文件：

```
bash
复制代码
diff -Naur passwd.old passwd.new > passwd.patch
```

应用补丁文件：

```
bash
复制代码
patch -p0 < passwd.patch
```

恢复旧文件：

```
bash
复制代码
patch -R -p0 < passwd.patch
```

### 11.4.4 文件打印准备：pr

pr 用于格式化文本文件，添加标题和页码。

**范例：打印 /etc/man_db.conf 文件**

```
bash
复制代码
pr /etc/man_db.conf
```

输出带有标题和页码的文件内容：

```
plaintext复制代码2014-06-10 05:35                 /etc/man_db.conf                 Page 1

#
#
# This file is used by the man-db package to configure the man and cat paths.
# It is also used to provide a manpath for those without one by examining
# configure script.
...
```

## 11.5 重点回顾

- 正则表达式就是处理字串的方法，他是以行为单位来进行字串的处理行为；
- 正则表达式通过一些特殊符号的辅助，可以让使用者轻易的达到“搜寻/删除/取代”某特定字串的处理程序；
- 只要工具程序支持正则表达式，那么该工具程序就可以用来作为正则表达式的字串处理之用；
- 正则表达式与万用字符是完全不一样的东西！万用字符 （wildcard） 代表的是 bash 操作接口的一个功能， 但正则表达式则是一种字串处理的表示方式！
- 使用 grep 或其他工具进行正则表达式的字串比对时，因为编码的问题会有不同的状态，因此， 你最好将 LANG 等变量设置为 C 或者是 en 等英文语系！
- grep 与 egrep 在正则表达式里面是很常见的两支程序，其中， egrep 支持更严谨的正则表达式的语法；
- 由于编码系统的不同，不同的语系 （LANG） 会造成正则表达式撷取数据的差异。因此可利用特殊符号如 [:upper:] 来替代编码范围较佳；
- 由于严谨度的不同，正则表达式之上还有更严谨的延伸正则表达式；
- 基础正则表达式的特殊字符有： *, ., [], [-], , ^, $ 等！
- 常见的支持正则表达式的工具软件有： grep , sed, vim 等等
- printf 可以通过一些特殊符号来将数据进行格式化输出；
- awk 可以使用“字段”为依据，进行数据的重新整理与输出；
- 文件的比对中，可利用 diff 及 cmp 进行比对，其中 diff 主要用在纯文本方面的新旧版本比对
- patch 指令可以将旧版数据更新到新版 （主要亦由 diff 创建 patch 的补丁来源文件）