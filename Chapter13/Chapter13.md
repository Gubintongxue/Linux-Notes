# 第十三章、Linux 帐号管理与 ACL 权限设置

最近更新日期：20//

要登陆 Linux 系统一定要有帐号与密码才行，否则怎么登陆，您说是吧？不过， 不同的使用者应该要拥有不同的权限才行吧？我们还可以通过 user/group 的特殊权限设置， 来规范出不同的群组开发专案呢～在 Linux 的环境下，我们可以通过很多方式来限制使用者能够使用的系统资源， 包括 [第十章、bash](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html) 提到的 [ulimit](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#variable_ulimit) 限制、还有特殊权限限制，如 [umask](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#umask) 等等。 通过这些举动，我们可以规范出不同使用者的使用资源。另外，还记得系统管理员的帐号吗？对！ 就是 root 。请问一下，除了 root 之外，是否可以有其他的系统管理员帐号？ 为什么大家都要尽量避免使用数字体态的帐号？如何修改使用者相关的信息呢？这些我们都得要了解了解的！

## 13.1 Linux 的帐号与群组

管理员的工作中，相当重要的一环就是“管理帐号”。Linux 系统通过 UID 和 GID 来识别每一个用户和群组。了解如何管理好一个服务器主机的帐号对于系统管理员来说至关重要。

### 13.1.1 使用者识别码：UID 与 GID

尽管我们登录 Linux 主机时输入的是帐号名称，但实际上，Linux 主机识别的是与帐号对应的 UID（用户识别码）和 GID（群组识别码）。这些信息保存在 /etc/passwd 文件中。

**示例：**

1. 确认系统中有名为 dmtsai 的用户。

   ```
   bash
   复制代码
   id dmtsai
   ```

2. 修改 dmtsai 的 UID。

   ```
   bash复制代码vim /etc/passwd
   # 将 dmtsai 的 UID 从 1000 改为 2000
   ```

**注意：** 在正常运行的系统中，不建议随意修改 UID，因为可能导致系统权限问题。

### 13.1.2 使用者帐号

当用户登录 Linux 系统时，系统会进行以下步骤：

1. 在 /etc/passwd 中查找帐号，获取 UID、GID、主文件夹和 shell 设置。
2. 在 /etc/shadow 中核对密码。
3. 若核对通过，进入 Shell 控管阶段。

关键文件包括：

- /etc/passwd：管理用户 UID/GID 和其他重要参数。
- /etc/shadow：管理密码相关数据。

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
plaintext
复制代码
dmtsai:$6$M4IphgNP2TmlXaSS$B418YFroYxxmm....:16559:5:60:7:5:16679:
```

这表示 dmtsai 的密码在 2015/05/04 修改过，密码需要 5 天后才能再次修改，60 天内必须更改密码，密码到期前 7 天会有警告，密码到期后有 5 天的宽限期，帐号将在 2015/09/01 失效。

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
bash复制代码groups
# 输出：dmtsai wheel users
```

表示 dmtsai 支持 dmtsai、wheel 和 users 这三个群组，其中 dmtsai 为有效群组。

**切换有效群组：**

```
bash复制代码newgrp users
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

## 13.2 帐号管理

管理帐号包括新增、移除与更改使用者的相关信息。下面我们详细介绍如何新增、移除与更改使用者的相关信息。

### 13.2.1 新增与移除使用者： useradd, 相关配置文件, passwd, usermod, userdel

要在 Linux 系统新增一个使用者，可以使用 `useradd` 创建帐号，使用 `passwd` 设置密码。

#### useradd

语法：

```
bash
复制代码
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
bash
复制代码
useradd vbird1
```

会创建 `/home/vbird1` 主文件夹，权限为 700。

**示例二：创建一个指定群组和 UID 的使用者 vbird2**

```
bash
复制代码
useradd -u 1500 -g users vbird2
```

**示例三：创建一个系统帐号 vbird3**

```
bash
复制代码
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
bash
复制代码
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
bash
复制代码
passwd vbird2
```

**示例二：使用 stdin 设置密码**

```
bash
复制代码
echo "abc543CC" | passwd --stdin vbird2
```

#### chage

显示或更改用户密码的过期信息。

语法：

```
bash
复制代码
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
bash
复制代码
chage -l vbird2
```

**示例二：强制用户在首次登录时更改密码**

```
bash
复制代码
chage -d 0 agetest
```

#### usermod

修改现有用户的属性。

语法：

```
bash
复制代码
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
bash
复制代码
usermod -c "VBird's test" vbird2
```

**示例二：设置 vbird2 的帐号失效日期**

```
bash
复制代码
usermod -e "2015-12-31" vbird2
```

#### userdel

删除用户。

语法：

```
bash
复制代码
userdel [-r] username
```

常用选项：

- `-r`：删除用户的主文件夹。

**示例：删除 vbird2，包括主文件夹**

```
bash
复制代码
userdel -r vbird2
```

### 13.2.2 使用者功能

一般用户可以使用以下命令查询和修改自己的信息。

#### id

查询用户 UID/GID 信息。

语法：

```
bash
复制代码
id [username]
```

**示例：查询 root 的 UID/GID 信息**

```
bash
复制代码
id
```

#### finger

查询用户信息。

语法：

```
bash
复制代码
finger [-s] username
```

**示例：查询 vbird1 的信息**

```
bash
复制代码
finger vbird1
```

#### chfn

修改用户信息。

语法：

```
bash
复制代码
chfn [-foph] [帐号名]
```

常用选项：

- `-f`：全名。
- `-o`：办公室房间号码。
- `-p`：办公室电话。
- `-h`：家里电话。

**示例：修改 vbird1 的信息**

```
bash
复制代码
chfn
```

#### chsh

修改用户的 shell。

语法：

```
bash
复制代码
chsh [-ls]
```

常用选项：

- `-l`：列出合法的 shell。
- `-s`：设置 shell。

**示例：设置 vbird1 的 shell 为 csh**

```
bash
复制代码
chsh -s /bin/csh vbird1
```

### 13.2.3 新增与移除群组

管理群组包括新增、修改和删除群组。

#### groupadd

新增群组。

语法：

```
bash
复制代码
groupadd [-g gid] [-r] 群组名称
```

常用选项：

- `-g`：指定 GID。
- `-r`：创建系统群组。

**示例：新增群组 group1**

```
bash
复制代码
groupadd group1
```

#### groupmod

修改群组。

语法：

```
bash
复制代码
groupmod [-g gid] [-n group_name] 群组名
```

常用选项：

- `-g`：修改 GID。
- `-n`：修改群组名称。

**示例：修改群组名为 mygroup，GID 为 201**

```
bash
复制代码
groupmod -g 201 -n mygroup group1
```

#### groupdel

删除群组。

语法：

```
bash
复制代码
groupdel 群组名称
```

**示例：删除群组 mygroup**

```
bash
复制代码
groupdel mygroup
```

#### gpasswd

设置群组管理员和管理群组成员。

语法（管理员）：

```
bash复制代码gpasswd [-A user1,...] [-M user3,...] 群组名称
gpasswd [-rR] 群组名称
```

常用选项：

- `-A`：设置群组管理员。
- `-M`：添加群组成员。
- `-r`：移除群组密码。
- `-R`：使群组密码失效。

语法（群组管理员）：

```
bash
复制代码
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

### 13.2.4 帐号管理实例

#### 任务一：创建多个用户

要求：

- `myuser1`，1st user，次要群组 mygroup1，可登录，密码 password
- `myuser2`，2nd user，次要群组 mygroup1，可登录，密码 password
- `myuser3`，3rd user，无额外支持，不可登录，密码 password

处理方法：

```
bash复制代码groupadd mygroup1
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
bash复制代码groupadd projecta
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
bash
复制代码
dmesg | grep -i acl
```

### 13.3.2 ACL 的设置技巧：getfacl, setfacl

#### setfacl 指令

`setfacl` 用于设置 ACL，语法如下：

```
bash
复制代码
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
bash复制代码touch acl_test1
setfacl -m u:vbird1:rx acl_test1
ll acl_test1
```

权限部分会多出一个 `+` 号，表示该文件有 ACL 设置。

#### getfacl 指令

`getfacl` 用于查看文件/目录的 ACL 设置，语法如下：

```
bash
复制代码
getfacl filename
```

**示例：查看 acl_test1 的 ACL 设置**

```
bash
复制代码
getfacl acl_test1
```

显示内容：

```
makefile复制代码# file: acl_test1
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
bash复制代码setfacl -m g:mygroup1:rx acl_test1
getfacl acl_test1
```

#### 设置有效权限（mask）

**示例：设置有效权限**

```
bash复制代码setfacl -m m:r acl_test1
getfacl acl_test1
```

显示内容中，`vbird1` 的权限会变为 `r`，因为有效权限仅为 `r`。

### 13.3.3 ACL 权限继承与设置

#### 继承 ACL 权限

默认情况下，ACL 权限不会自动继承。要设置继承权限，可以使用 `d` 参数。

**示例：设置目录的默认 ACL 权限**

```
bash复制代码setfacl -m d:u:myuser1:rx /srv/projecta
getfacl /srv/projecta
```

新建文件或目录会继承默认 ACL 权限。

#### 取消 ACL 设置

**示例：取消 myuser1 的 ACL 设置**

```
bash复制代码setfacl -x u:myuser1 /srv/projecta
setfacl -x d:u:myuser1 /srv/projecta
```

**示例：让 pro3 无法使用该目录**

```
bash
复制代码
setfacl -m u:pro3:- /srv/projecta
```

通过 `-` 来表示用户或群组无任何权限。

### 总结

ACL 是一种灵活的权限管理机制，可以精确控制文件和目录的访问权限。通过 `setfacl` 和 `getfacl` 命令，可以轻松设置和查看 ACL。默认 ACL 的设置还可以实现权限的继承，方便管理复杂的权限需求。在实际操作中，合理使用 ACL 可以大大提高系统的安全性和灵活性。

## 13.4 使用者身份切换

在 Linux 系统中进行身份切换有几个原因：

1. **使用一般帐号**：为了安全，一般建议以普通用户身份操作日常任务，只有在需要时才切换到 root，避免误操作。
2. **用较低权限启动系统服务**：例如，使用 apache 用户启动 Apache 服务，这样即使服务被攻破，系统也不至于受影响。
3. **软件本身的限制**：例如，telnet 和 ssh 可以设置拒绝 root 直接登录，这时需要先登录普通用户再切换身份。

主要有两种身份切换方式：

1. **su**：直接切换身份，需要输入目标用户（如 root）的密码。
2. **sudo**：执行特定命令时临时获得高权限，需提前配置，输入当前用户密码。

### 13.4.1 su

`su` 是最简单的身份切换命令，用法如下：

```
bash
复制代码
su [-lm] [-c 指令] [username]
```

选项与参数：

- `-`：使用 login-shell 的方式登录，读取新用户的环境变量。
- `-l`：同 `-`。
- `-m`：保留当前环境设置，不读取新用户的配置文件。
- `-c`：执行一次指令后返回原身份。

**示例：**

```
bash复制代码# 切换到 root，不读取新用户环境变量
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

### 13.4.2 sudo

`sudo` 可以让用户在不需要知道 root 密码的情况下，临时获得高权限。用法如下：

```
bash
复制代码
sudo [-b] [-u 新使用者帐号] 命令
```

选项与参数：

- `-b`：将命令放到后台执行。
- `-u`：切换到指定用户，默认切换到 root。

**示例：**

```
bash复制代码# 以 sshd 用户身份创建文件
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
bash复制代码# 允许 vbird1 使用 sudo
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
bash复制代码# 允许 wheel 群组的用户使用 sudo
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
bash复制代码# 允许 myuser1 使用 sudo 修改密码，但不能修改 root 密码
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
bash复制代码# 创建别名并应用
[root@study ~]# visudo
User_Alias ADMPW = pro1, pro2, pro3, myuser1, myuser2
Cmnd_Alias ADMPWCOM = /usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd root
ADMPW ALL=(root) ADMPWCOM
```

### 配置 sudo 不需要密码

**示例：**

```
bash复制代码# 配置 wheel 群组用户使用 sudo 不需要密码
[root@study ~]# visudo
%wheel    ALL=(ALL)    NOPASSWD: ALL
```

### 使用 sudo 搭配 su

**示例：**

```
bash复制代码# 配置允许用户通过 sudo 使用 su 切换到 root
[root@study ~]# visudo
User_Alias ADMINS = pro1, pro2, pro3, myuser1
ADMINS ALL=(root) /bin/su -
```

使用 `sudo` 和 `su` 时，请确保用户的可信度，以避免潜在的安全风险。

## 13.5 使用者的特殊 shell 与 PAM 模块

本节讨论如何创建仅能使用特定服务（如邮件服务）但不能登录系统的账户，以及 PAM（Pluggable Authentication Modules）的概念和应用。

### 13.5.1 特殊的 shell, /sbin/nologin

为了防止某些用户登录系统但仍能使用系统资源，可以给这些用户设置一个特殊的 shell：`/sbin/nologin`。这样，即使这些用户有密码，也无法登录系统，会看到如下信息：

```
arduino
复制代码
This account is currently not available.
```

这种方法常用于系统帐号或仅使用特定服务（如邮件服务）的帐号。例如，为了让使用者知道他们不能登录系统，可以创建 `/etc/nologin.txt` 文件，并在其中说明原因。用户尝试登录时，会显示该文件的内容。

**例子：**

编辑 `/etc/nologin.txt`：

```
bash复制代码[root@study ~]# vim /etc/nologin.txt
This account is system account or mail account.
Please DO NOT use this account to login my Linux server.
```

测试：

```
bash复制代码[root@study ~]# su - myuser3
This account is system account or mail account.
Please DO NOT use this account to login my Linux server.
```

### 13.5.2 PAM 模块简介

PAM 是嵌入式认证模块，可以统一管理用户认证过程，解决多种认证机制并存导致的不同步问题。PAM 提供一套 API，通过模块化的方式进行认证。

PAM 的工作流程如下：

1. 用户执行程序（如 `passwd`），并输入密码。
2. 程序调用 PAM 模块进行认证。
3. PAM 模块根据配置文件执行认证。
4. PAM 返回认证结果给程序。
5. 程序根据结果执行相应操作。

PAM 模块的设置文件位于 `/etc/pam.d/` 目录中，文件名与程序名相同。

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
bash复制代码[root@study ~]# cat /etc/pam.d/passwd
#%PAM-1.0
auth       include      system-auth
account    include      system-auth
password   substack     system-auth
-password  optional     pam_gnome_keyring.so use_authtok
password   substack     postlogin
```

每一行表示一个认证过程，`include` 表示调用其他配置文件。

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
bash复制代码[root@study ~]# cat /etc/pam.d/login
#%PAM-1.0
auth       include      system-auth
account    required     pam_nologin.so
password   include      system-auth
session    required     pam_selinux.so close
session    required     pam_loginuid.so
session    include      system-auth
session    include      postlogin
```

### 13.5.5 其他相关文件

除了 PAM 配置文件外，还有其他相关文件：

1. **/etc/securetty**：限制 root 可登录的终端。
2. **/etc/nologin**：存在时禁止普通用户登录。
3. **/etc/security/limits.conf**：设置用户资源限制。
4. **/var/log/secure** 和 **/var/log/messages**：记录登录和认证相关的信息。

**例子：设置用户资源限制**

```
bash复制代码[root@study ~]# vim /etc/security/limits.conf
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
  bash复制代码[root@study ~]# w
   01:49:18 up 25 days,  3:34,  3 users,  load average: 0.00, 0.01, 0.05
  USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
  dmtsai   tty2                      07Jul15 12days  0.03s  0.03s -bash
  dmtsai   pts/0    172.16.200.254   00:18    6.00s  0.31s  0.11s sshd: dmtsai [priv]
  ```

- **who**：显示当前登录用户的信息。

  ```
  bash复制代码[root@study ~]# who
  dmtsai   tty2         2015-07-07 23:07
  dmtsai   pts/0        2015-07-22 00:18 （192.168.1.100）
  ```

- **last**：显示用户的历史登录信息。

  ```
  bash
  复制代码
  [root@study ~]# last
  ```

- **lastlog**：显示每个用户的最近登录时间。

  ```
  bash复制代码[root@study ~]# lastlog
  Username         Port     From             Latest
  root             pts/0                     Wed Jul 22 00:26:08 +0800 2015
  bin                                        **Never logged in**
  dmtsai           pts/1    192.168.1.100    Wed Jul 22 01:08:07 +0800 2015
  ```

### 13.6.2 使用者对谈： write, mesg, wall

在系统中，可以使用 `write` 命令与其他用户对话，`mesg` 命令控制是否接受消息，`wall` 命令向所有用户广播消息。

- **write**：发送消息给特定用户。

  ```
  bash复制代码[root@study ~]# write vbird1 pts/2
  Hello, there:
  Please don't do anything wrong...
  ```

- **mesg**：控制是否接受消息。

  ```
  bash复制代码[vbird1@study ~]$ mesg n  # 禁止接受消息
  [vbird1@study ~]$ mesg y  # 允许接受消息
  ```

- **wall**：向所有用户广播消息。

  ```
  bash
  复制代码
  [root@study ~]# wall "I will shutdown my linux server..."
  ```

### 13.6.3 使用者邮件信箱： mail

使用 `mail` 命令可以发送和接收邮件。系统中的每个用户都有一个邮箱文件，通常位于 `/var/spool/mail` 目录下。

- **发送邮件**：

  ```
  bash复制代码[root@study ~]# mail -s "nice to meet you" vbird1
  Hello, D.M. Tsai
  Nice to meet you in the network.
  You are so nice.  byebye!
  .  # 输入 . 表示结束邮件内容
  ```

- **使用文件内容发送邮件**：

  ```
  bash复制代码[root@study ~]# mail -s "bashrc file content" dmtsai < ~/.bashrc
  [root@study ~]# ls -al ~ | mail -s "myfile" root
  ```

- **查看和处理邮件**：

  ```
  bash复制代码[vbird1@study ~]$ mail
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

### 总结

通过这些命令，可以有效地管理和与系统上的用户进行通信。`w`、`who`、`last` 和 `lastlog` 用于查询用户信息，`write`、`mesg` 和 `wall` 用于用户之间的通信，`mail` 用于发送和接收邮件。这些工具对于系统管理员和普通用户都非常有用，能够帮助他们更好地了解和管理系统。

## 13.7 CentOS 7 环境下大量创建帐号的方法

在管理大量用户帐号时，使用脚本和相关工具可以显著提高效率。下面介绍几种常用的帐号检查工具和一个批量创建用户的脚本。

### 13.7.1 一些帐号相关的检查工具

#### pwck

`pwck` 用于检查 `/etc/passwd` 文件中的信息是否正确，以及对应的主文件夹是否存在。

```
bash复制代码[root@study ~]# pwck
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
bash
复制代码
[root@study ~]# echo "vbird3:abcdefg" | chpasswd
```

### 13.7.2 大量创建帐号范本（适用 passwd --stdin 选项）

利用 `passwd --stdin` 选项，可以批量创建用户并设置密码。以下是一个创建用户的脚本示例：

```
bash复制代码[root@study ~]# vim accountadd.sh
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
bash复制代码[root@study ~]# vim accountadd.txt
std01
std02
std03
std04
std05
```

运行脚本创建用户：

```
bash复制代码[root@study ~]# sh accountadd.sh create
Changing password for user std01.
passwd: all authentication tokens updated successfully.
...
```

此脚本会根据 `accountadd.txt` 文件中的用户列表创建用户，并生成密码。生成的密码会保存到 `outputpw.txt` 文件中，供后续查阅。

通过上述脚本和工具，可以有效地管理和批量创建用户，提高工作效率。

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