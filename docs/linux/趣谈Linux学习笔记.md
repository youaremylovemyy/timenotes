[TOC]

## 01 开篇介绍

开篇词就是总结了接下来要学习的东西。总的来说，这门课有以下亮点：

- 有深度（其中涉及到 Linux 内核源码解析）
- 有广度（从 Linux 基础，谈到内核，到虚拟化，到容器、集群、数据中心操作系统等）
- 有趣味（类比于一家公司，操作系统是这家公司的老板）
- 图文并茂

开篇放出了一张 Linux 操作系统全景图（深入到代码），如下：

![alllinux](images/alllinux.jpg)



这张图展示了进程、文件、文件系统、硬盘之间的一个关系（Linux的哲学之一就是一切皆文件），这里面涉及到内核代码结构，其实就是一个个的数据结构拼接起来，想象一下，操作系统那么多组件，怎么样很好地管理这些组件，就是把他们定义成一个个的数据结构，然后有关系的就连接起来。

## 03 学习路径

一图胜千言

![](images/linuxpath.jpeg)

## 04 认识 Linux

从电脑组装说起。一台电脑硬件上来说至少应该包括以下组件，才能满足我们个人的使用需求。

![](images/hw.png)

光有这些硬件设备也玩不转一台电脑，必须要有一个类似于大脑的东西来指挥这些硬件才行，这个大脑就是操作系统。

操作系统要管理不同的硬件设备，就需要有不同的模块组件，就像一个组织需要设定几个不同的负责人一样。从功能和管理对象上说，可以分为以下几大类（操作系统体系结构图）：

![](images/linuxstruct.jpg)

上面这幅图关注点在于内核态，对于完整的操作系统，还应该包括用户态，以及用户态和内核态的交互模块，下面这幅图包含了这一切，而且还包括了硬件部分，非常完整：

![](images/linuxmodule.png)

---

### 内核代码

**如何找 Linux 内核代码？**

Linux 内核源码链接：

https://www.kernel.org/

*在里面找到 `kernel/git/torvalds/linux.git` 即可下载*

推荐一个 **在线阅读源码** 的网站（非常赞）：

https://elixir.bootlin.com/linux/latest/source



**内核代码结构如何体现出上面的几大子系统的？**

以 Linux 3.0 为例看看它的代码结构：

| 目录          | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| arch          | 包含和硬件体系结构相关的代码，每种平台占一个相应的目录，如 i386、 ARM、PowerPC、MIPS 等。 |
| block         | 块设备驱动程序 I/O 模块。                                    |
| crypto        | 常用加密和散列算法（如 AES、SHA 等），还有一些压缩和 CRC 校 验算法。 |
| Documentation | 内核源码文档                                                 |
| drivers       | 设备驱动程序，每个不同的驱动占用一个子目录，如 char、block、net、 mtd、i2c 等。 |
| firmware      | 使用某些驱动程序而需要的设备固件                             |
| fs            | vfs 和 支持的各种文件系统，如 EXT、FAT、NTFS、JFFS2 等。     |
| include       | 内核 头文件，与系统相关的头文件被放置在 include/linux 子目录下 |
| init          | 内核引导和初始化代码                                         |
| ipc           | 进程间通信的代码                                             |
| kernel        | 内核的最核心部分，包括进程调度、定时器等，而和平台相关的一 部分代码放在 arch/*/kernel 目录下 |
| lib           | 库文件代码，通用内核函数                                     |
| mm            | 内存管理子系统代码，和平台相关的一部分代码放在 arch/*/mm 目录下 |
| net           | 网络子系统相关代码，实现了各种常见的网络协议                 |
| samples       | 示例，示范代码                                               |
| scripts       | 编译内核所用的脚本文件                                       |
| security      | Linux 安全模块， 主要包含 SELinux 模块。                     |
| sound         | 音频子系统， ALSA、OSS 音频设备的驱动核心代码和常用设备驱动。 |
| usr           | 早期用户空间代码（所谓的initramfs）， 实现了用于打包和压缩的 cpio 等 |
| tools         | 在 Linux 开发中有用的工具                                    |
| virt          | 虚拟化基础结构                                               |



## 05 快速上手 Linux 命令

### 用户名和密码

- 配置 root 密码：`passwd` 或 `passwd root` （`passwd <user>`）
- 创建一个新的用户：`useradd jay`
- 创建一个新的用户并加到某个组里：`useradd jay -g <gid>`
- `/etc/passwd` 和 `/etc/group` 中能够看到用户密码和用户组的信息

### 浏览文件

```sh
ls -l
drwxr-xr-x 6 root root    4096 Oct 20  2017 apt
-rw-r--r-- 1 root root     211 Oct 20  2017 hosts
```

- 第一个字段中的第一个字符 `-` 表示普通文件，`d` 表示目录文件，`l` 表示连接文件
- 接着后面 9 个字符表示模式（权限位），前面 3 个是所属用户的读写执行权限，中间 3 个是用户所属组的，后面 3 个是其他用户的。改变权限使用 `chmod 711 <file>` 
- 第二个字段是硬链接数目
- 第三个字段是所属用户
- 第四个字段是所属用户组
- 第五个字段是文件大小
- 第六个字段是文件被修改的日期
- 最后是文件名

`chmod` 改变文件权限，`chown` 改变所属用户，`chgrp` 改变所属组。

```sh
# 将文件 file.txt 的拥有者改为 root，这个命令需要有超级用户权限才能执行，所以我们在命令之前加上sudo
sudo chown root file.txt 
```

```sh
# 改变文件的拥有组为root组
sudo chgrp root file.txt

# 将文件的所属组由 root 改为 tim
chgrp -v tim file.txt
```



### 安装软件

相对于 Windows 的 `exe`、`zip` 等包，Linux 常用有 `rpm`（Centos 系）、`deb`（Ubuntu 系） 和 `tar.gz` 等包。

- `rpm` 和 `deb`

  

```sh
# centos rpm
rpm -i jdk-XXX_linux-x64_bin.rpm

# ubuntu deb
dpkg -i jdk-xxx_linux-x64_bin.deb

# 查看安装软件列表
rpm -qa
dpkg -l

# 翻页
rpm -qa | more
rpm -qa | less

# 加搜索
rpm -qa | grep jdk
dpkg -l | grep jdk

# 删除
rpm -e 
dpkg -r

```



- Linux 软件管家：`yum` 和 `apt-get`



```sh
# 搜索软件
yum search jdk
apt-cache search jdk

# 安装
yum install java-11-openjdk.x86_64
apt-get install openjdk-9-jdk
```



- 配置软件源：国内常用是 `阿里源` 和 `163源`。（用到时搜索即可）

centos：`/etc/yum.repos.d/CentOS-Base.repo`



ubuntu: `/etc/apt/sources.list`



- 包下载和压缩解压缩



```sh
# 下载
wget

# 压缩解压缩
yum install zip.x86_64 unzip.x86_64
apt-get install zip unzip

unzip <xx.zip>
tar zxvf jdk-XXX_linux-x64_bin.tar.gz
```



- 运行程序



```sh
# 01 直接运行
<command> 或者 <./command>

# 02 后台运行
nohup <command> >out.file 2>&1 &

# 后台进程关闭
ps -ef |grep 关键字  |awk '{print $2}'|xargs kill -9

# 03 以服务的方式运行 /lib/systemd/system
# 比如 mysql
apt-get install mysql-server # 安装
systemctl start mysql # 启动
systemctl enable mysql # 设置开机启动

# centos 中 MySQL 是 MariaDB。/usr/lib/systemd/system/
```



### 开关机

```sh
# 关机
shutdown -h now
poweroff

# 重启
reboot
```



### 一图总结之

![img](https://static001.geekbang.org/resource/image/88/e5/8855bb645d8ecc35c80aa89cde5d16e5.jpg)



## 05 系统调用

**进程相关系统调用：** 



- fork：创建子进程，返回两个值，如果是 0，表示子进程，如果 >0 表示父进程，值是子进程的 ID。
- execve：创建出的子进程执行的系统调用（有一套函数集）。
- waitpid：父进程监听子进程的执行情况。

通过如下方式查询更加详细的信息:

```sh
$ps -o pid,pgid,ppid,sid,tty,comm
```

(tty表示控制终端）



进程地址空间：

![](images/process.png)



**进程内存空间分配系统调用：**



- brk：当分配内存量小的时候（具体是 128K）使用；
- mmap：当分配内存量大于 128K 时使用，这么做的目的是避免 **内存碎片** 的问题。参考[这里](<https://blog.csdn.net/yusiguyuan/article/details/39496057>)



**异常与信号处理相关系统调用：**



- kill：用户进程发送一个退出信号给另一个进程
- sigaction：信号处理函数（无视(ignore)信号、默认(default)操作、自定义操作）



`$man 7 signal` 查阅更多的信号。一些常用的信号：

- `SIGINT`   当键盘按下 `CTRL+C` 从 shell 中发出信号，信号被传递给shell中前台运行的进程，对应该信号的默认操作是中断 (INTERRUPT) 该进程。
- `SIGQUIT`  当键盘按下 `CTRL+\` 从 shell 中发出信号，信号被传递给shell中前台运行的进程，对应该信号的默认操作是退出 (QUIT) 该进程。
- `SIGTSTP`  当键盘按下 `CTRL+Z` 从 shell 中发出信号，信号被传递给shell中前台运行的进程，对应该信号的默认操作是暂停 (STOP) 该进程。
- `SIGCONT`  用于通知暂停的进程继续。
- `SIGALRM`  起到定时器的作用，通常是程序在一定的时间之后才生成该信号。



**进程间通信系统调用：**



- 消息队列：msgget（创建）、msgsnd（发送）、msgrcv（接收）
- 共享内存（通信量大）：shmget（创建）、shmat（映射共享内存）
- 信号量（资源访问竞争）：sem_wait（占用资源）、sem_post（释放资源）
- 管道（无名管道和命名管道）
- Socket
- 参考[这里](<https://songlee24.github.io/2015/04/21/linux-IPC/>)





**C 函数库（glibc）和系统调用的关系：**



C 函数库是对相关系统调用函数的进一步封装，目的是以一种更友好的方式供上层用户的调用。系统调用更像是组成一个汉字的 **笔画** ，而库函数则像 **偏旁部首** 。比如 glibc 中的 printf 函数就会调用 `sys_open`、`sys_mmap`、`sys_write`、`sys_close` 等系统调用函数。malloc 函数也会进一步调用到 `sys_brk` 等函数。



**Tips：**



`$man 2 syscalls` 可以查看所有的系统调用，`$man 2 read` 查看系统调用 read 的详细说明。



### 一图总结

![img](https://static001.geekbang.org/resource/image/ff/f0/ffb6847b94cb0fd086095ac263ac4ff0.jpg)



## 06 x86 体系架构

硬件图结合计算机的逻辑图：

![img](https://static001.geekbang.org/resource/image/fa/9b/fa6c2b6166d02ac37637d7da4e4b579b.jpeg)

CPU 和 内存如何配合工作：

CPU 包括：

- 运算单元：如加减乘除
- 数据单元：CPU 内部的缓存和寄存器组
- 控制单元：指令获取、控制
  - 指令指针寄存器：执行下一条指令在内存中的地址
  - 指令寄存器



![img](https://static001.geekbang.org/resource/image/3a/23/3afda18fc38e7e53604e9ebf9cb42023.jpeg)



x86 CPU 组件放大图：

![img](https://static001.geekbang.org/resource/image/2d/1c/2dc8237e996e699a0361a6b5ffd4871c.jpeg)



运算单元：

- 通用寄存器

控制单元：

- IP：指令指针寄存器
- CS： 代码寄存器
- DS：数据寄存器
- SS：栈寄存器



一图总结之：

![img](https://static001.geekbang.org/resource/image/e2/76/e2e92f2239fe9b4c024d300046536d76.jpeg)



## 07 Linux 启动过程

1 打开电源，从主板上的 BIOS 读取其中存储的程序，这个程序知道主板连接哪些硬件（硬盘、网络接口、键盘、串口、并口），BIOS 会检查以哪种方式启动（软盘、光盘、硬盘）。（BIOS 存储在 ROM 只读区）

![img](https://static001.geekbang.org/resource/image/5f/fc/5f364ef5c9d1a3b1d9bb7153bd166bfc.jpeg)



2 读取启动盘中的前 512 个字节，也就是 MBR （主引导扇区）

3 MBR 会从设备的某一个分区来转载 **引导加载程序** （BootLoader） ，BootLoader 含有 OS 相关的信息，比如 OS 内核，OS 内核所在的位置等。常用的 BootLoader 程序有 **GRUB2** 和 **LILO** 。

可以通过 `grub2-mkconfig -o /boot/grub2/grub.cfg` 来配置系统启动的选项。

4 BootLoader 接着会加载内核程序

具体地，它会安装 `boot.img` 、`core.img` 等镜像程序。其中 `kernel.img` 就是存放的 OS 内核程序。一般会有多个程序（多操作系统），我们选择其中一个。

![img](https://static001.geekbang.org/resource/image/2b/6a/2b8573bbbf31fc0cb0420e32d07b196a.jpeg)

5 假如选择 Linux内核，内核会预留一部分自己的内存空间，然后通过驱动程序检测哪些计算机硬件可用，然后会启动一个 `init` 进程，它是 Linux 内核中的 1 号进程（Linux 没有 0 号进程），至此，便完成启动阶段做的事，接下来就交给 `init` 进程来管理。

6 `init` 进程会运行一系列的初始脚本（shell脚本）：

设置计算机名称，时区，检测文件系统，挂载硬盘，清空临时文件，设置网络……

至此，整个 OS 便准备好了，`init` 启动登录程序，输入用户名、密码便可自行玩耍了。



**附：**

grub2 命令的使用手册见：

<http://www.cnblogs.com/f-ck-need-u/p/7094693.html>



## 08 内核初始化

从源码上来讲，内核初始化从 函数 `start_kernel` 开始的，相当于内核的 `main` 函数，在文件 `init/main.c` 中。它总的要进行几件事：

- 任务初始化
- 中断初始化
- 内存管理初始化
- 调度器初始化
- 其他（如进程线程）初始化



![img](https://static001.geekbang.org/resource/image/cd/01/cdfc33db2fe1e07b6acf8faa3959cb01.jpeg)



`init_task` 产生 0 号进程，这是唯一一个没有通过 `fork` 或者其他 `kernel_thread` 产生的进程，是进程列表的第一个。



`rest_init` 会产生两个进程，一号进程和二号进程来对用户态和内核进行初始化。



CPU 的分层权限限制，导致用户态和内核态的访问需要采用系统调用的手段：

![img](https://static001.geekbang.org/resource/image/2b/42/2b53b470673cde8f9d8e2573f7d07242.jpg)



系统调用总的一个过程基本上是：

![img](https://static001.geekbang.org/resource/image/b1/99/b16beba7a40011cab8e54f938fefeb99.jpg)



## 09 系统调用

用户态访问内核：

- 系统调用
- C 函数库 glibc（最终也是调用系统调用函数）



在 glibc 源代码中，有个文件 `syscallls.list`，里面列着所有 glibc 的函数对应的系统调用。

```C
# File name Caller  Syscall name    Args    Strong name Weak names
open		-	open		Ci:siv	__libc_open __open open
```



Glibc 还有一个文件 `syscall-tmplate.S` ，使用 宏，定义了系统调用的调用方式：

```C
T_PSEUDO (SYSCALL_SYMBOL, SYSCALL_NAME, SYSCALL_NARGS)
    ret
T_PSEUDO_END (SYSCALL_SYMBOL)

#define T_PSEUDO(SYMBOL, NAME, N)		PSEUDO (SYMBOL, NAME, N)
```



这里有一个 宏 `PSEUDO`，对于任何一个系统调用，都会调用到这个 宏：

```C
#define PSEUDO(name, syscall_name, args)                      \
  .text;                                      \
  ENTRY (name)                                    \
    DO_CALL (syscall_name, args);                         \
    cmpl $-4095, %eax;                               \
    jae SYSCALL_ERROR_LABEL
```



32 位系统调用过程和 64 位还有些许不同，我们直接看 64 位的：

接着，开始陷入内核，OS 会将系统调用名称转换为系统调用号，放到寄存器 rax 中，然后执行 `syscall`，然后通过一种特殊模块寄存器（MSR）来完成系统调用的功能，它会将系统调用相关的指令压入内核：

![img](https://static001.geekbang.org/resource/image/86/a5/868db3f559ad08659ddc74db07a9a0a5.jpg)

然后通过一个 **系统调用表** 找到相应的函数，进行执行。

系统调用表存放着所有的系统调用函数，对于 64位的系统，是定义在文件 `arch/x86/entry/syscalls/syscall_64.tbl` 中的（比如 `open` 是这样定义的）:

```
2	common	open			sys_open
```



通过寄存器 rax 中的系统调用号，就可以找到系统调用表中对应的系统调用函数，从而执行真正的内核函数，比如 `open` 对应内核函数为 `sys_open`。



## 10 进程

安装开发套件：(centos 7)

```sh
yum -y groupinstall "Development Tools"
```



程序文件具有 **严格的格式** ：ELF（Executeable and Linkable Format）

ELF 是一种用于二进制文件、可执行文件、目标代码、共享库和核心转储的标准文件格式。

目标文件有三类（可通过 file 命令查看）：

- 可重定位的目标文件（.o 文件）
- 可执行的目标文件（.exe 文件）
- 可被共享的目标文件（.so 动态库文件）



从文本文件到二进制可执行文件的过程：

![img](https://static001.geekbang.org/resource/image/85/de/85320245cd80ce61e69c8391958240de.jpeg)



1、.o 文件时 ELF 的第一种文件类型，格式如下：

![img](https://static001.geekbang.org/resource/image/e9/d6/e9c2b4c67f8784a8eec7392628ce6cd6.jpg)



将 .o 归档为 .a 的静态库文件，使用命令 ar：

```sh
ar cr libstaticprocess.a process.o
```

- .text：存放编译好的二进制可执行代码
- .rodata：只读数据，例如字符串常量、const 的变量
- .data：已经初始化好的全局变量
- .bss：未初始化的全局变量
- .symtab：符号表，记录的事函数和变量
- .strtab：字符串表、字符串常量和变量名



编译的时候使用静态链接库：

```sh
gcc -o staticcreateprocess createprocess.o -L. -lstaticprocess
```



2、生成的二进制可执行文件为 ELF 的第二种格式。



静态链接库如果更新，那个多个引用的程序都要随之更新，效率低，因为出现动态链接库，可被多个程序共享。



3、动态链接库是 ELF 的第三种格式。共享对象文件。

```sh
gcc -shared -fPIC -o libdynamicprocess.so process.o
```



```sh
gcc -o dynamiccreateprocess createprocess.o -L. -ldynamicprocess
```



> 当一个动态链接库被链接到一个程序文件中的时候，最后的程序文件并不包括动态链接库中的代码，而仅仅包括对动态链接库的引用，并且不保存动态链接库的全路径，仅仅保存动态链接库的名称。



系统默认加载的路径是 /lib 和 /usr/lib，可以使用 `LD_LIBRARY_PATH` 来自定义动态库链接的路径。

```sh
export LD_LIBRARY_PATH = .
```





进程调用 exec -> load_elf_binary，最终将动态库加载的内存。

exec 函数：

- 包含 p 的函数（execvp, execlp）会在 PATH 路径下面寻找程序； 
- 不包含 p 的函数需要输入程序的全路径； 
- 包含 v 的函数（execv, execvp,execve）以数组的形式接收参数； 
- 包含 l 的函数（execl, execlp, execle）以列表的形式接收参数； 包含 e 的函数（execve, execle）以数组的形式接收环境变量。



![img](https://static001.geekbang.org/resource/image/46/f6/465b740b86ccc6ad3f8e38de25336bf6.jpg)

进程树：

所有进程都是由父进程 fork 过来的，往前追溯，会找到祖宗进程，也就是系统启动的 init 进程。

![img](https://static001.geekbang.org/resource/image/4d/16/4de740c10670a92bbaa58348e66b7b16.jpeg)

有些系统中，比如 centos ，/sbin/init 会被链接到 systemd。

一图总结之：

![img](https://static001.geekbang.org/resource/image/db/a9/dbd8785da6c3ce3fe1abb7bb5934b7a9.jpeg)



Tips:

查看 ELF 文件的工具：

- readelf：分析 ELF 文件的信息
- objdump：显示二进制文件信息
- hexdump：查看文件的 16进制编码
- nm：显示关于指定文件中符号的信息



## 11 线程

多线程程序的编译依赖于 `libpthread.so`

```sh
gcc thread.c -lpthread
```



普通线程的创建和运行过程：

![img](https://static001.geekbang.org/resource/image/e3/bd/e38c28b0972581d009ef16f1ebdee2bd.jpg)



多线程可访问的数据：

- 线程栈上的本地数据，比如函数执行过程中的局部变量。（`ulimit -a` 查看栈的大小）
- 整个进程里共享的全局数据，比如全局变量
- 线程的私有数据



线程共享数据的保护问题：

- Mutex（互斥变量）
- 条件变量（一般和互斥锁配合使用）



![img](https://static001.geekbang.org/resource/image/02/58/02a774d7c0f83bb69fec4662622d6d58.png)



## 12 进程数据结构

在内核中，进程和线程都是由一个数据结构 `task_struct` 来统一管理。

![img](https://static001.geekbang.org/resource/image/01/e8/016ae7fb63f8b3fd0ca072cb9964e3e8.jpeg)



每个进程或线程定义一个这个结构，多个进程用链表连接起来。

其中，

任务 ID：

- pid：进程 id
- tgid：多个线程中主线程对应的进程 ID
- group_leader：多线程中主线程



信号处理：

```c
/* Signal handlers: */
struct signal_struct		*signal;
struct sighand_struct		*sighand;
sigset_t			blocked;
sigset_t			real_blocked;
sigset_t			saved_sigmask;
struct sigpending		pending;
unsigned long			sas_ss_sp;
size_t				sas_ss_size;
unsigned int			sas_ss_flags;
```



包括，

- blocked：阻塞暂不处理
- pending：尚等待处理
- sighand：正在被信号处理函数处理



任务状态：

值定义在 `include/linux/sched.h` 中，

```c
/* Used in tsk->state: */
#define TASK_RUNNING                    0
#define TASK_INTERRUPTIBLE              1
#define TASK_UNINTERRUPTIBLE            2
#define __TASK_STOPPED                  4
#define __TASK_TRACED                   8
/* Used in tsk->exit_state: */
#define EXIT_DEAD                       16
#define EXIT_ZOMBIE                     32
#define EXIT_TRACE                      (EXIT_ZOMBIE | EXIT_DEAD)
/* Used in tsk->state again: */
#define TASK_DEAD                       64
#define TASK_WAKEKILL                   128
#define TASK_WAKING                     256
#define TASK_PARKED                     512
#define TASK_NOLOAD                     1024
#define TASK_NEW                        2048
#define TASK_STATE_MAX                  4096
```



所有定义的状态如下图所示：

![img](https://static001.geekbang.org/resource/image/e2/88/e2fa348c67ce41ef730048ff9ca4c988.jpeg)

其中，

- TASK_RUNNING：表示进程在时刻准备运行（并不是正在运行，当获得 CPU 时间片的时候进入运行）
- TASK_INTERRUPTIBLE：可中断的睡眠状态（浅睡眠），当信号来的时候，进程可被唤醒
- TASK_UNINTERRUPTIBLE：不可中断的睡眠状态（深睡眠），不可被信号唤醒，只能死等 I/O 操作完成，kill 也不行
- TASK_KILLABLE：可以终止的睡眠状态，上面的改良，可以响应致命信号
- TASK_WAKEKILL：在接收致命信号时唤醒进程
- TASK_STOPPED：进程收到 SIGSTOP、SIGINT、SIGTSTP 或者 SIGTTOU 后进入的状态
- TASK_TRACED：进程被 debugger 等进程监视，进程执行被调试程序所停止
- EXIT_ZOMBIE：进程结束首先进入的状态，如果父进程没有调用 waitpid() 函数的话，就成为 僵尸进程
- EXIT_DEAD 进程的最终状态



## 13 进程数据结构（中）

进程运行过程中一些统计量，可以更好地了解进程的工作情况：

```C
u64				utime;// 用户态消耗的 CPU 时间
u64				stime;// 内核态消耗的 CPU 时间
unsigned long			nvcsw;// 自愿 (voluntary) 上下文切换计数
unsigned long			nivcsw;// 非自愿 (involuntary) 上下文切换计数
u64				start_time;// 进程启动时间，不包含睡眠时间
u64				real_start_time;// 进程启动时间，包含睡眠时间
```



进程的亲缘关系：

```C
struct task_struct __rcu *real_parent; /* real parent process */
struct task_struct __rcu *parent; /* recipient of SIGCHLD, wait4() reports */
struct list_head children;      /* list of my children */
struct list_head sibling;       /* linkage in my parent's children list */
```



- parent：指向其父进程
- children：表示链表头部
- sibling：表示兄弟链表



整个关系如下图所示：

![img](https://static001.geekbang.org/resource/image/92/04/92711107d8dcdf2c19e8fe4ee3965304.jpeg)

进程权限控制：

```C
/* Objective and real subjective task credentials (COW): */
const struct cred __rcu         *real_cred;
/* Effective (overridable) subjective task credentials (COW): */
const struct cred __rcu         *cred;
```



- real_cred：谁能操作这个进程
- cred：这个进程能够操作谁



内存管理：

每个进程有自己的独立虚拟内存空间，用 `mm_struct` 表示：

```C
struct mm_struct                *mm;
struct mm_struct                *active_mm;
```



文件与文件系统：

每个进程有一个文件系统的数据结构，和一个打开文件的数据结构：

```C
/* Filesystem information: */
struct fs_struct                *fs;
/* Open file information: */
struct files_struct             *files;
```



## 加餐（一）学习攻略 

三遍学习法+PPT笔记

第一遍学习：先读薄，不要在乎一城一池的得失，梳理主次矛盾，记录所有问题

第二遍学习：再读厚，把握主线，攻克各个问题（用 PPT 笔记法记录这个过程）

第三遍学习：再读薄，变成自己的东西，总体梳理知识点，可以用xmind思维导图，融汇贯通



## 14 进程数据结构（下）

用户态函数栈：

函数调用时的栈如下所示（32位）：从高地址到低地址，往下增长，上面是栈底，下面是栈顶。

![img](https://static001.geekbang.org/resource/image/ae/2e/aec865abccf0308155f4138cc905972e.jpg)



在 64位的环境，EBP 变成了 RBP，ESP 变成了 RSP。



内核态函数栈：

进程用户态和内核态的联系用到如下结构：

```c
struct thread_info		thread_info;
void  *stack;
```



其中，stack 即维护内核函数栈。

![img](https://static001.geekbang.org/resource/image/31/2d/31d15bcd2a053235b5590977d12ffa2d.jpeg)

当用户态切换到内核态后，需要将用户态运行过程中的 CPU 上下文保存起来，其实是保存的 `pt_regs` 这个结构，



通过 `task_struct` 的 `stack` 指针，就可以找到内核栈：

```c
static inline void *task_stack_page(const struct task_struct *task)
{
	return task->stack;
}
```



通过 `stack` 就能很方便找到 `pt_regs`：

```c
/*
 * TOP_OF_KERNEL_STACK_PADDING reserves 8 bytes on top of the ring0 stack.
 * This is necessary to guarantee that the entire "struct pt_regs"
 * is accessible even if the CPU haven't stored the SS/ESP registers
 * on the stack (interrupt gate does not save these registers
 * when switching to the same priv ring).
 * Therefore beware: accessing the ss/esp fields of the
 * "struct pt_regs" is possible, but they may contain the
 * completely wrong values.
 */
#define task_pt_regs(task) \
({									\
	unsigned long __ptr = (unsigned long)task_stack_page(task);	\
	__ptr += THREAD_SIZE - TOP_OF_KERNEL_STACK_PADDING;		\
	((struct pt_regs *)__ptr) - 1;					\
})
```



反过来，内核栈通过 `thread_info` 这个结构来找 `task_struct`，

```c
struct thread_info {
	struct task_struct	*task;		/* main task structure */
	__u32			flags;		/* low level flags */
	__u32			status;		/* thread synchronous flags */
	__u32			cpu;		/* current CPU */
	mm_segment_t		addr_limit;
	unsigned int		sig_on_uaccess_error:1;
	unsigned int		uaccess_err:1;	/* uaccess failed */
};
```



其中 `struct task_struct *task;` 即是指向 `task_struct`，通常 `current_thread_info()->task` 来获取 `task_struct`。





一图总结：

![img](https://static001.geekbang.org/resource/image/82/5c/82ba663aad4f6bd946d48424196e515c.jpeg)

## 15 调度（上）

指的是进程数量远超过 CPU 的数目，需要进行 CPU 调度执行。

进程包括两种：

- 实时进程
- 普通进程



实时进程的调度优先级比普通进程要高。优先级数值越低，优先级越高。

实时进程优先级范围：0-99

普通进程优先级范围：100-139



`task_struct` 中有一个成员变量，叫 **调度策略** ：

```c
unsigned int policy;
```

包括以下策略：

```c
#define SCHED_NORMAL		0
#define SCHED_FIFO		1
#define SCHED_RR		2
#define SCHED_BATCH		3
#define SCHED_IDLE		5
#define SCHED_DEADLINE		6
```



其中，`SCHED_FIFO（先来先服务）` 、`SCHED_RR（时间片轮流调度）` 、`SCHED_DEADLINE（根据 deadline 选择调度）` 是实时进程调度策略，



剩余，`SCHED_NORMAL（普通进程）` 、`SCHED_BATCH（后台进程）`、`SCHED_IDLE（空闲进程）` 是普通进程调度策略。



通常最常用的调度算法是 普通进程使用的 **完全公平调度算法：**

用球装进口袋的例子来理解，球少的袋子多放点球，这样经过几轮，虽不能保证所有口袋球完全一样，但也差不多公平。

这个算法的底层实现是通过 **红黑树** 来实现的。

红黑树的每个节点记录一个 vruntime，vruntime 是虚拟运行时间，也就是进程时钟 tick 数，用来记录每个进程占用 CPU 的时间滴答数，以此保证公平。

![img](https://static001.geekbang.org/resource/image/c2/93/c2b86e79f19d811ce10774688fc0c093.jpeg)



总结：

一个 CPU 上有一个队列，即 CFS（公平调度队列），这实现上是一颗红黑树，树的每一个节点都是一个 `sched_entity（调度实体）`，每个  ``sched_entity` 都属于一个 `task_struct`，`task_struct` 里有指针指向这个进程属于哪个调度类。在调度的时候，依次调用调度类的函数，从 CPU 的队列中取出下一个进程。

![img](https://static001.geekbang.org/resource/image/10/af/10381dbafe0f78d80beb87560a9506af.jpeg)



## 16 调度（中）

分为：主动调度和被动调度

进程上下文切换：

主要干两件事：一是切换进程空间，也即虚拟内存，二是切换寄存器和 CPU 上下文



主动调度的过程：

一个运行中的进程主动调用 `__schedule` 让出 CPU，在 `__schedule` 里会做两件事情，第一是选取下一个进程，第二是进行上下文切换，而上下文切换又分为用户进程空间切换和内核态的切换

![img](https://static001.geekbang.org/resource/image/9f/64/9f4433e82c78ed5cd4399b4b116a9064.png)



## 17 调度（下） 

抢占式调度：一个进程执行时间太长了，是时候切换到另一个进程了。

![img](https://static001.geekbang.org/resource/image/93/7f/93588d71abd7f007397979f0ba7def7f.png)



## 18 进程的创建 

```
首先调用 fork 系统调用

--> sys_call_table --> sys_fork
	--> _do_fork
		--> copy_process # 第一件大事
			--> dup_task_struct
				--> alloc_task_struct_node # 分配 task_struct
				--> alloc_thread_stack_node # 创建内核栈
				--> arch_dup_task_struct # 调用 memcpy 将task_struct 进行复制
				--> setup_thread_stack # 设置 thread_info
			--> copy_creds
				--> prepare_creds # memcpy 复制一份父进程的 cred
				--> 设置进程运行统计量
				--> sched_fork # 设置调度相关的变量
					--> __sched_fork # 设置包括运行时间，进程状态，优先级，调度类
				--> copy_files # 复制一个进程打开的文件信息
					--> dup_fd # 复制文件描述符
					--> file_fs # 复制一个进程的目录信息
				--> copy_sighand # 初始化信号相关的变量
				--> copy_mm # 复制内存空间，进程空间用 mm_struct 结构表示
		--> wake_up_new_task # 第二件大事,试图唤醒创建的子进程
			--> activate_task 
				--> enqueue_task # 激活进程
			--> enqueue_task_fair # 如果是 CFS 的调度类，执行相应调度类
```



以上第一阶段的过程如以下图示所示：

![img](https://static001.geekbang.org/resource/image/9d/58/9d9c5779436da40cabf8e8599eb85558.jpeg)



## 19 线程的创建

和进程一样，线程的创建是由用户态和内核态共同完成的。

它们都要依托于一个数据结构 `task_struct`，下面看看线程具体创建就的过程：

从用户调用 `pthread_create` 开始，

```c
1、设置线程属性参数
2、pthread 结构用户态维护线程
3、创建线程栈(ALLOCATE_STACK)
    -
    -
4、进入内核 调用 create_thread
5、调用 __clone
	--> sys_call
		--> _do_fork # 和进程不同，这里主要是对相关的 结构 做引用计数 +1，而不是复制。
```



下图总结了进程和线程创建的区别：

![img](https://static001.geekbang.org/resource/image/14/4b/14635b1613d04df9f217c3508ae8524b.jpeg)



## 20 内存管理（上）：规划进程内存空间布局

操作系统管理的 三个方面：

- 物理内存的管理：物理内存地址只有内存管理模块能够使用
- 虚拟内存的管理：每个进程看到的都是独立的，互不干扰的虚拟地址空间
- 虚拟地址和物理的地址的映射管理：虚拟地址和物理地址通过页表结构关联起来

打印进程内存空间的布局的命令：

```c
cat /proc/$pid/map
```



## 21 内存管理（下）

虚拟内存转到物理内存，需要用到两种机制：分段机制和分页机制。

分段机制：

![img](https://static001.geekbang.org/resource/image/96/eb/9697ae17b9f561e78514890f9d58d4eb.jpg)

大体思路是通过段基地址和段内偏移，将虚拟地址转换为物理地址。



分页机制：

![img](https://static001.geekbang.org/resource/image/ab/40/abbcafe962d93fac976aa26b7fcb7440.jpg)

通过页表目录项和页内偏移量，将虚拟地址转换为物理地址。

对于 64位，则页表目录更多，如下：

![img](https://static001.geekbang.org/resource/image/42/0b/42eff3e7574ac8ce2501210e25cd2c0b.jpg)

内存映射：将虚拟内存和物理内存映射起来，并且在内存紧张的时候可以换出到硬盘中。

![img](https://static001.geekbang.org/resource/image/7d/91/7dd9039e4ad2f6433aa09c14ede92991.jpg)



## 22 进程空间管理

进程的内存空间管理来自于一个结构：

```c
struct mm_struct **mm
```

其中，一个成员变量:

```c
unsigned long task_size
```

定义了用户空间和内核空间的大小分界线。

对于 32 位系统，最大能够寻址 2^32=4G，其中用户态虚拟地址空间是 3G，内核态是 1G。

对于 64 位系统，虚拟地址只使用了 48 位。用户空间和内核空间大小都是 128T。

如下：

![img](https://static001.geekbang.org/resource/image/89/59/89723dc967b59f6f49419082f6ab7659.jpg)



内存空间布局：

![img](https://static001.geekbang.org/resource/image/7a/4c/7af58012466c7d006511a7e16143314c.jpeg)



以上这些区域通过下面的结构描述：

```c
struct vm_area_struct *mmap;		/* list of VMAs */
struct rb_root mm_rb;
```



64位的内核空间布局：

![img](https://static001.geekbang.org/resource/image/7e/f6/7eaf620768c62ff53e5ea2b11b4940f6.jpg)

整个过程，可以用下面的两幅图表示：

32位：

![img](https://static001.geekbang.org/resource/image/28/e8/2861968d1907bc314b82c34c221aace8.jpeg)

64位：

![img](https://static001.geekbang.org/resource/image/2a/ce/2ad275ff8fdf6aafced4a7aeea4ca0ce.jpeg)



## 23 物理内存管理（上）

物理内存的组织方式，有两种：

- SMP：对称多处理器
- NUMA ：非一致内存访问

![img](https://static001.geekbang.org/resource/image/8f/49/8f158f58dda94ec04b26200073e15449.jpeg)

在 NUMA 结构中，把内存分成节点，把节点分成区域zone，区域又分为页，

页是组成物理内存的基本单位，页的数据结构是 `struct page`。

在Linux中，如果要分配比较大的内存，例如分配页级别的，可以使用伙伴系统。

几者之间的关系如下所示：

![img](https://static001.geekbang.org/resource/image/3f/4f/3fa8123990e5ae2c86859f70a8351f4f.jpeg)



## 24 物理内存管理（下）

物理内存分 NUMA 节点，分别进行管理

伙伴系统将多个连续的页面作为一个大的内存块分配给上层。

小内存的分配使用 slub 分配器。它会从伙伴系统中申请大内存块切分成小块，分配给其他系统。

内存页面的换入换出会使用一个内核线程 `kswapd`，它会循环检查内存的使用情况，当内存充足的时候，它什么也不会做，当内存吃紧了，它会检查是否应该换出一些内存页。

常用的一个内存换出算法是 LRU，也就是最近最少使用。

用下面这个图总结：伙伴系统和 Slub 分配器：

![img](https://static001.geekbang.org/resource/image/52/54/527e5c861fd06c6eb61a761e4214ba54.jpeg)



## 25 用户态内存映射

内存映射：

- 物理内存和虚拟内存之间的映射
- 将文件中的内容映射到虚拟内存空间

内存映射通过一个 系统调用 mmap 来完成，内存映射区在内存结构中的位置如下所示：

![img](https://static001.geekbang.org/resource/image/f0/45/f0dcb83fcaa4f185a8e36c9d28f12345.jpg)



当我们在申请内存时，如果申请的是小块内存，就用 brk，如果申请大块内存，就用 mmap。

mmap 依赖于页表项，页表由于一般都较大，所以只能放在内存中，这会导致访存的性能下降。

为了加快映射速度，引入 TLB 块表，TLB 存储在 Cache 中，存储的是页表项，可以直接通过虚拟地址找到物理地址。内存映射的时候，会首先查 TLB 表，块表中有映射关系，就直接转换为物理地址，如果没有，才会到内存中查询页表。

如果用户态访问没有映射的内存会引发缺页异常，分配物理页表、补齐页表。如果是 swap，则将 swap 文件读入，如果是文件映射，则将文件读入。

![img](https://static001.geekbang.org/resource/image/78/44/78d351d0105c8e5bf0e49c685a2c1a44.jpg)





## 26 内核态内存映射

- vmalloc 和 kmap_atomic
    - 内核的虚拟地址空间 vmalloc 区域用于映射
    - kmap_atomic 临时映射
        - 32 位, 调用 set_pte 通过内核页表临时映射
        - 64 位, 调用 page_address→lowmem_page_address 进行映射
- 内核态缺页异常
    - kmap_atomic 直接创建页表进行映射
    - vmalloc 只分配内核虚拟地址, 访问时触发缺页中断, 调用 

物理内存管理：

![](images/phymem.png)



用户态内存分配：

![](images/usermem.png)



内核态内存分配：

![](images/kernelmem.png)



内存分配体系总图：

![img](https://static001.geekbang.org/resource/image/27/9a/274e22b3f5196a4c68bb6813fb643f9a.png)



## 27 文件系统

文件系统的设计原则：

- 严格的组织形式，以块为单位进行存储
- 要有索引区
- 应该有缓存层，存放经常被读写的文件
- 以文件夹的形式组织，方便管理和查询
- linux 维护一套数据结构，保存哪些文件被哪些进程打开和使用



文件系统相关的命令：

```sh
fdisk -l
# 查看格式化和没有格式化的分区
```



```sh
mkfs.ext4 /dev/vdc
# 对磁盘进行格式化，vdc会建立一个分区，格式化为 ext4 文件系统的格式。
```



```sh
mount /dev/vdc1 / 根目录 / 用户 A 目录 / 目录 1 
# 格式化的分区需要挂到某个目录下面，才能作为普通的文件系统进行访问
```



```sh
umount / 根目录 / 用户 A 目录 / 目录 1
# 卸载挂载
```



文件系统相关的系统调用：

文件相关：open, write, read, lseek, close

目录相关：opendir，readdir，closedir

文件属性由结构体 struct stat 来维护，由函数 stat，fstat，lstat来操作：

```c
int stat(const char *pathname, struct stat *statbuf);
int fstat(int fd, struct stat *statbuf);
int lstat(const char *pathname, struct stat *statbuf);
 
 
struct stat {
  dev_t     st_dev;         /* ID of device containing file */
  ino_t     st_ino;         /* Inode number */
  mode_t    st_mode;        /* File type and mode */
  nlink_t   st_nlink;       /* Number of hard links */
  uid_t     st_uid;         /* User ID of owner */
  gid_t     st_gid;         /* Group ID of owner */
  dev_t     st_rdev;        /* Device ID (if special file) */
  off_t     st_size;        /* Total size, in bytes */
  blksize_t st_blksize;     /* Block size for filesystem I/O */
  blkcnt_t  st_blocks;      /* Number of 512B blocks allocated */
  struct timespec st_atim;  /* Time of last access */
  struct timespec st_mtim;  /* Time of last modification */
  struct timespec st_ctim;  /* Time of last status change */
};
```

![](images/stat.png)







最后，用一幅图来总结之：

![](images/fsys.png)





## 28 硬盘文件系统

硬盘结构：

https://static001.geekbang.org/resource/image/2e/d2/2ea68b40d928e6469233fcb4948c7cd2.jpg

Linux 主流文件系统格式：ext 系列，ext3, ext4

硬盘分成相同大小的块，一块的大小是扇区大小的整数倍，默认是 4K，格式化的时候，可以设定。

为了更好的管理文件，使用一个结构 inode 来维护，inode 保存一个文件的基本信息，提供索引机制，方便查找和管理。

```c
struct ext4_inode {
	__le16	i_mode;		/* File mode */
	__le16	i_uid;		/* Low 16 bits of Owner Uid */
	__le32	i_size_lo;	/* Size in bytes */
	__le32	i_atime;	/* Access time */
	__le32	i_ctime;	/* Inode Change time */
	__le32	i_mtime;	/* Modification time */
	__le32	i_dtime;	/* Deletion Time */
	__le16	i_gid;		/* Low 16 bits of Group Id */
	__le16	i_links_count;	/* Links count */
	__le32	i_blocks_lo;	/* Blocks count */
	__le32	i_flags;	/* File flags */
......
	__le32	i_block[EXT4_N_BLOCKS];/* Pointers to blocks */
	__le32	i_generation;	/* File version (for NFS) */
	__le32	i_file_acl_lo;	/* File ACL */
	__le32	i_size_high;
......
};
```

其中，`i_block[]`  定义了一个文件被划分了多少块，每一块在哪里这些信息。



**文件在文件系统中使用 inode 来描述, 每个 inode 中都含有一些列的数据块**

- 对于普通文件: 数据块中保存的是文件数据
- 对于文件夹: 数据块中保存的是子文件的文件项
  - 为了加快文件夹中文件的搜索速度, Linux 通过索引模式, 可以使用文件名的散列快速定位到文件的位置





**文件的组织**

- Linux 中将磁盘的块分成一个个的块组, 每个块组由一个块组的位图和对应的数据块描述, 最大可描述 128M 的空间
- 每个块组有其对应的块组描述符, 描述符之间形成一个链表
- 为了更好地管理块组, Linux 通过元块组来描述块组
  - 一个元块组中由 64 个块组组成, 其中的块组描述符一共 64 项, 分别在 0, 1, 63 的位置的块组上备份三份
- 整个文件系统通过超级块来描述



**软链接与硬链接**

- 软链接: 在链接目标文件之前, 会创建自己的 inode 和数据块, 然后其数据块中的内容指向目标文件
  - 可跨系统链接
- 硬链接: 即为一个文件创建了一个别名, 这个别名会指向真实文件的 inode 结构体
  - 因为直接指向源文件结构体, 因此他无法跨文件系统进行链接



## 29 虚拟文件系统

虚拟文件系统是为 Linux 访问底层系统提供一个统一的接口，Linux 可以支持多达数十种不同的文件系统。

Linux 一个文件操作的系统调用总的需要经历以下的几个层次：

![img](https://static001.geekbang.org/resource/image/3c/73/3c506edf93b15341da3db658e9970773.jpg)

挂载文件系统（mount）：

1. 挂载之前先注册：`register_filesystem`
2. mount 调用链：`do_mount->do_new_mount->vfs_kern_mount`
3. `mount_fs` 挂载文件系统，调用 `ext4_fs_type` 的 `ext4_mount` 函数挂载 `ext4` 文件系统

打开文件（open）：

1. open->do_sys_open -> `get_unused_fd_flags` 得到一个未使用的文件描述符
2. `path_init` 对文件路径进行查找，并逐层处理
3. `do_last` 实现目录项高速缓存，Linux为了提高目录项对象的处理效率，设计和实现了目录项高速缓存 dcache。
4. `vfs_open` 真正打开文件



对于每一个打开的文件，都有一个 dentry 对应，叫做 directory entry，不仅仅表示文件夹，也表示文件，指向文件对应的 inode。dentry 是放在一个 dentry cache 里，文件关闭了，它依然存在。inode 结构表示硬盘上的 inode，包括块设备号等。



## 30 文件缓存

脏页：写入到缓存，但是还没有写入到硬盘的页面。

缓存页：缓存以页为单位存在内存里，并以 `radix tree` 这种数据结构来存放。



read 或 write 系统调用：

1. 首先进入 VFS：`read->vfs_read->__vfs_read`
2. 每个文件有一个结构 `struct file` ，其中 `file_operations` 定义了文件系统层的操作，对于 ext4 文件系统来说，调用 `ext4_file_read_iter`
3. 这一步有两种方式，一种是直接读写硬盘，叫 直接 I/O，一种是通过缓存间接读写，叫 缓存 I/O
4. 如果是缓存 I/O ，会触发数据的预读和回写

具体过程用下面这张图来总结：

![img](https://static001.geekbang.org/resource/image/0c/65/0c49a870b9e6441381fec8d9bf3dee65.png)





## 31 输入输出设备

用户-》设备控制器-》硬件设备

比如中断控制器，DMA 控制器等待。



输入输出设备：

- 块设备：将信息存在固定大小的块中，每个块都有自己的地址，比如硬盘
- 字符设备：发送或接收字节流，不能寻址，比如鼠标



和各个设备控制器对接的对象又是设备驱动程序。

![img](https://static001.geekbang.org/resource/image/7b/68/7bf96d3c8e3a82cdac9c7629b81fa368.png)



设备驱动程序实现中断处理的入口：中断处理函数 handler



读取设备文件的数据流并以十六进制查看：

```sh
cat /dev/urandom | od -x
```



Linux内核驱动程序可以看成是一个标准的内核模块，在linux中，安装驱动程序，就是加载一个内核模块。用 `lsmod` 查看没有加载的内核模块。用 `insmod` 安装内核模块。

有了驱动，就可以用 `mknod` 在 /dev 文件夹下创建设备文件：

```sh
mknod filename type major minor
# filename 为 /dev 下的设备名 
# type 是 c 为字符设备，b 为 块设备
# major 是主设备号
# minor 是次设备号
```



之后就可以通过操作新创建的设备文件来操作相应的驱动程序了。



也可以通过 `sysfs` 文件系统下 `/sys` 查看设备文件：

在 /sys 路径下有下列的文件夹：

- `/sys/devices` 是内核对系统中所有设备的分层次的表示； 
- `/sys/dev` 目录下一个 char 文件夹，一个 block 文件夹，分别维护一个按字符设备和块设备的主次号码 (major:minor）连接到真是设备(/sys/devices) 的符号连接文件
- `/sys/block` 是系统中当前所有的块设备
- `/sys/module` 有系统中所有模块的信息



有了 sysfs 之后，还需要一个守护进程`udev`，当一个设备新插入系统的时候，内核会检查到这个设备，并向 `udevd` 发送热插拔消息，`udev` 接着在 /dev/ 中创建对于文件。

![img](https://static001.geekbang.org/resource/image/62/90/6234738aac8d5897449e1a541d557090.jpg)

最后，用下面一幅图来总结整个输入输出的管理层次关系：

![img](https://static001.geekbang.org/resource/image/80/7f/80e152fe768e3cb4c84be62ad8d6d07f.jpg)



## 32 字符设备(上)

打开一个字符设备，需要经历以下的一些过程：

1. 把写好的内核模块 （.ko 结尾），通过 insmod 加载进内核，`module_init`
2. 通过 mknod 在 /dev 下创建一个设备文件，并和相应的设备号进行关联，最后就可以通过文件系统的接口，对这个设备文件进行操作。
3. open 打开设备文件



![img](https://static001.geekbang.org/resource/image/2e/e6/2e29767e84b299324ea7fc524a3dcee6.jpeg)

打开设备文件之后，就可以对文件进行读写了。

通过 read write 调用到内核中的 `sys_read` 和 `sys_write` 进行操作。

除此之外，还可以调用 `ioctl` 进行一些特殊的 I/O 操作。



## 33 字符设备(下)

中断的大体处理流程：

从外部设备发起，形成外部中断信号，中断信号到达中断控制器，中断控制器接着发送中断向量 interrupt vector 给 CPU。

`do_IRQ` ：统一处理硬件中断的处理函数，通过中断向量  vector_irq 映射为 irq_desc，irq_desc 是一个用于描述用户注册的中断处理函数结构，为了能够根据中断向量得到 irq_desc 结构，会把这些结构放在一个基数树里面，irq_desc 里有一个成员 irqaction，指向设备驱动程序里面注册的中断处理函数。大体过程如下：

![img](https://static001.geekbang.org/resource/image/26/8f/26bde4fa2279f66098856c5b2b6d308f.png)



## 34 块设备(上)

mknod 创建块设备文件，创建在 /dev 路径下，属于 devtmpfs 文件系统。

insmod 加载块设备文件

mount 块设备文件

下面一幅图展示了这三个系统调用的底层实现细节：

![img](https://static001.geekbang.org/resource/image/62/20/6290b73283063f99d6eb728c26339620.png)



## 35 块设备(下)

从文件系统到块设备的调用层次关系如下：



![img](https://static001.geekbang.org/resource/image/3c/0e/3c473d163b6e90985d7301f115ab660e.jpeg)

块设备IO 和字符设备IO一样，IO操作都分为两种：直接IO和缓存IO。

无论哪种IO，最终都会调用submit_bio 提交块设备的 IO 请求。

每一种块设备，都有一个 gendisk 表示这个设备，它有一个请求队列，这个请求队列是一系列的request对象，每个request对象里面包含多个BIO对象，指向page cache，所谓写入块设备，就是将 page cache 里的数据写入硬盘。

![img](https://static001.geekbang.org/resource/image/c9/3c/c9f6a08075ba4eae3314523fa258363c.png)



## 36 进程间通信

进程间通信的 几种方式：

- 管道：瀑布模型
- 消息队列：发邮件
- 共享内存+信号量：拉到会议室讨论
- 信号

### 管道

管道类似于软件开发中的瀑布模型，只有当上一下步骤完成才会开始下一个步骤，上个步骤的输出是下一个步骤的输入。

```sh
ps -ef | grep 关键字 | awk '{print $2}' | xargs kill -9
```



管道分为命名管道和匿名管道

创建命名管道：

```sh
mkfifo hello
```



查看管道：

```sh
# ls -l
prw-r--r--  1 root root         0 May 21 23:29 hello

# p 表示这个文件时管道文件
```



向管道一端输入东西：

```sh
# echo "hello world" > hello
```



从管道另一端查看东西：

```sh
# cat < hello 
hello world
```



### 消息队列

创建消息队列：

```sh
messagequeueid = msgget(key, IPC_CREAT | 0777)
```



每个消息队列都有一个唯一的 key，使用 ftok 生成。

```sh
key = ftok("/root/messagequeue/messagequeuekey", 1024)
```



发送消息：

```sh
msgsnd(messagequeueid, &buffer, len, IPC_NOWAIT)
# messagequeueid 指定发送消息的消息队列 ID
# buffer 消息结构体
# len 消息长度
# flag 消息标志 IPC_NOWAIT 表示发送消息时候不阻塞
```



接收消息：

```sh
msgrcv(messagequeueid, &buffer, 1024, type, IPC_NOWAIT)
# 1024 表示消息可接受的最大长度
# type 消息类型

```



使用命令行工具创建 IPC 对象：ipcmk，ipcs 和 ipcrm 分别用于创建、查看和删除 IPC 对象。



创建消息队列：

```sh
# ipcs -q
------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages    
0x00016978 32768      root       777        0            0

```



### 共享内存

说的是多个进程共享同一块虚拟地址空间，映射到相同的物理空间。一个进程写入，另外的进程立马就能看到了。



创建共享内存：

```sh
int shmget(key_t key, size_t size, int flag);
# key 共享内存唯一的key
# size 共享内存的大小
# flag IPC_CREAT 创建一个新的共享内存

```



查看共享内存：

```sh
#ipcs --shmems
------ Shared Memory Segments ------ ­­­­­­­­
key        shmid    owner perms    bytes nattch status
0x00000000 19398656 marc  600    1048576 2      dest

```



将共享内存 attach 到虚拟地址空间的某个位置：

```sh
void *shmat(int shm_id, const void *addr, int flag);
# addr 表示 attach 的地址，通常设为 NULL，表示让内核来选一个合适的地址

```



删除共享内存(先解绑，再删除)：

```sh
# 解绑
int shmdt(void *addr); 

# 删除
int shmctl(int shm_id, int cmd, struct shmid_ds *buf);

```



### 信号量

保护共享内存中数据读写的冲突。

定义了两种原子操作：

- P 操作：申请资源，-N
- V 操作：归还资源，+N



创建信号量：

```sh
 int semget(key_t key, int num_sems, int sem_flags);
 # num_sems 表示创建多少个信号量，如果有多个资源需要管理，就可以创建一个信号量组

```



初始化信号量的总的资源数量：

```sh
int semctl(int semid, int semnum, int cmd, union semun args);
# semnum 信号量组中某个信号的 ID

union semun
{
  int val;
  struct semid_ds *buf;
  unsigned short int *array;
  struct seminfo *__buf;
};

```



无论P操作还是 V 操作，都使用 semop 函数：

```sh
int semop(int semid, struct sembuf semoparray[], size_t numops);


struct sembuf 
{
  short sem_num; // 信号量组中对应的序号，0～sem_nums-1
  short sem_op;  // 信号量值在一次操作中的改变量
  short sem_flg; // IPC_NOWAIT, SEM_UNDO
}

```



### 信号

异常情况下的进程间通信方式，比如线上系统故障等。

信号可以在任何时候发送给进程，进程需要为这个信号设置信号处理函数。相当于一个应急手册。



## 35 块设备(下)

从文件系统到块设备的调用层次关系如下：



![img](https://static001.geekbang.org/resource/image/3c/0e/3c473d163b6e90985d7301f115ab660e.jpeg)

块设备IO 和字符设备IO一样，IO操作都分为两种：直接IO和缓存IO。

无论哪种IO，最终都会调用submit_bio 提交块设备的 IO 请求。

每一种块设备，都有一个 gendisk 表示这个设备，它有一个请求队列，这个请求队列是一系列的request对象，每个request对象里面包含多个BIO对象，指向page cache，所谓写入块设备，就是将 page cache 里的数据写入硬盘。

![img](https://static001.geekbang.org/resource/image/c9/3c/c9f6a08075ba4eae3314523fa258363c.png)



## 36 进程间通信

进程间通信的 几种方式：

- 管道：瀑布模型
- 消息队列：发邮件
- 共享内存+信号量：拉到会议室讨论
- 信号

### 管道

管道类似于软件开发中的瀑布模型，只有当上一下步骤完成才会开始下一个步骤，上个步骤的输出是下一个步骤的输入。

```sh
ps -ef | grep 关键字 | awk '{print $2}' | xargs kill -9
```



管道分为命名管道和匿名管道

创建命名管道：

```sh
mkfifo hello
```



查看管道：

```sh
# ls -l
prw-r--r--  1 root root         0 May 21 23:29 hello

# p 表示这个文件时管道文件
```



向管道一端输入东西：

```sh
# echo "hello world" > hello
```



从管道另一端查看东西：

```sh
# cat < hello 
hello world
```



### 消息队列

创建消息队列：

```sh
messagequeueid = msgget(key, IPC_CREAT | 0777)
```



每个消息队列都有一个唯一的 key，使用 ftok 生成。

```sh
key = ftok("/root/messagequeue/messagequeuekey", 1024)
```



发送消息：

```sh
msgsnd(messagequeueid, &buffer, len, IPC_NOWAIT)
# messagequeueid 指定发送消息的消息队列 ID
# buffer 消息结构体
# len 消息长度
# flag 消息标志 IPC_NOWAIT 表示发送消息时候不阻塞
```



接收消息：

```sh
msgrcv(messagequeueid, &buffer, 1024, type, IPC_NOWAIT)
# 1024 表示消息可接受的最大长度
# type 消息类型
```



使用命令行工具创建 IPC 对象：ipcmk，ipcs 和 ipcrm 分别用于创建、查看和删除 IPC 对象。



创建消息队列：

```sh
# ipcs -q
------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages    
0x00016978 32768      root       777        0            0
```



### 共享内存

说的是多个进程共享同一块虚拟地址空间，映射到相同的物理空间。一个进程写入，另外的进程立马就能看到了。



创建共享内存：

```sh
int shmget(key_t key, size_t size, int flag);
# key 共享内存唯一的key
# size 共享内存的大小
# flag IPC_CREAT 创建一个新的共享内存
```



查看共享内存：

```sh
#ipcs --shmems
------ Shared Memory Segments ------ ­­­­­­­­
key        shmid    owner perms    bytes nattch status
0x00000000 19398656 marc  600    1048576 2      dest
```



将共享内存 attach 到虚拟地址空间的某个位置：

```sh
void *shmat(int shm_id, const void *addr, int flag);
# addr 表示 attach 的地址，通常设为 NULL，表示让内核来选一个合适的地址
```



删除共享内存(先解绑，再删除)：

```sh
# 解绑
int shmdt(void *addr); 

# 删除
int shmctl(int shm_id, int cmd, struct shmid_ds *buf);
```



### 信号量

保护共享内存中数据读写的冲突。

定义了两种原子操作：

- P 操作：申请资源，-N
- V 操作：归还资源，+N



创建信号量：

```sh
 int semget(key_t key, int num_sems, int sem_flags);
 # num_sems 表示创建多少个信号量，如果有多个资源需要管理，就可以创建一个信号量组
```



初始化信号量的总的资源数量：

```sh
int semctl(int semid, int semnum, int cmd, union semun args);
# semnum 信号量组中某个信号的 ID

union semun
{
  int val;
  struct semid_ds *buf;
  unsigned short int *array;
  struct seminfo *__buf;
};
```



无论P操作还是 V 操作，都使用 semop 函数：

```sh
int semop(int semid, struct sembuf semoparray[], size_t numops);


struct sembuf 
{
  short sem_num; // 信号量组中对应的序号，0～sem_nums-1
  short sem_op;  // 信号量值在一次操作中的改变量
  short sem_flg; // IPC_NOWAIT, SEM_UNDO
}
```



### 信号

异常情况下的进程间通信方式，比如线上系统故障等。

信号可以在任何时候发送给进程，进程需要为这个信号设置信号处理函数。相当于一个应急手册。



## 37 信号

Linux定义了非常多的信号，`kill -l` 可以查看所有的信号。

`man 7 signal` 查看每个信号的意思：

```sh
Signal     Value     Action   Comment
──────────────────────────────────────────────────────────────────────
SIGHUP        1       Term    Hangup detected on controlling terminal
                              or death of controlling process
SIGINT        2       Term    Interrupt from keyboard
SIGQUIT       3       Core    Quit from keyboard
SIGILL        4       Core    Illegal Instruction


SIGABRT       6       Core    Abort signal from abort(3)
SIGFPE        8       Core    Floating point exception
SIGKILL       9       Term    Kill signal
SIGSEGV      11       Core    Invalid memory reference
SIGPIPE      13       Term    Broken pipe: write to pipe with no
                              readers
SIGALRM      14       Term    Timer signal from alarm(2)
SIGTERM      15       Term    Termination signal
SIGUSR1   30,10,16    Term    User-defined signal 1
SIGUSR2   31,12,17    Term    User-defined signal 2
……
```



进程对信号的处理方式：

- 执行默认操作：比如 Core Dump，程序种植户，将进程状态保存到文件中，方式程序员事后分析问题
- 捕捉信号：执行信号处理函数
- 忽略信号



信号处理常见流程：

1. 注册信号处理函数
2. 发送信号



注册信号处理函数：

```sh
typedef void (*sighandler_t)(int);
int sigaction(int signum, const struct sigaction *act,
                     struct sigaction *oldact);
```



信号处理函数执行的动作保存在结构体 `struct sigaction` 中：

```sh
struct sigaction {
	__sighandler_t sa_handler;
	unsigned long sa_flags;
	__sigrestore_t sa_restorer;
	sigset_t sa_mask;		/* mask last for extensibility */
};
```



sigaction 的调用过程：

```sh
sigaction-> __sigaction -> __libc_sigaction -> rt_sigaction(内核) -> do_sigaction
```



大体的流程如下所示：

![img](https://static001.geekbang.org/resource/image/7c/28/7cb86c73b9e73893e6b0e0433d476928.png)



## 38 信号(下)

中断与信号的区别：

- 中断在内核驱动里注册中断处理函数
- 信号在用户态进程中注册信号处理函数

发送信号的系统调用：

- kill 或 sigqueue 发送信号给某个进程
- tkill 或 tgkill 发送信号给某个线程

最终都会调用到 `do_send_sig_info` 函数。



信号的发送和处理：

![img](https://static001.geekbang.org/resource/image/3d/fb/3dcb3366b11a3594b00805896b7731fb.png)



## 39 管道

管道分为匿名管道和命名管道

两个进程之间通过匿名管道进行通信：

```c
int pipe(int fd[2])
```



![img](https://static001.geekbang.org/resource/image/71/b6/71eb7b4d026d04e4093daad7e24feab6.png)

通过 mkfifo 创建命名管道

无论是匿名管道，还是命名管道，在内核都是一个文件。

写入一个 pipe 就是从 struct file 结构中找到缓存写入，读取一个 pipe 就是从 struct file 结构中找到缓存读出。



## 40 IPC 共享内存和信号量

共享内存和信号量都通过 ftok 得到 key，通过 xxxget 创建对象并生成 id；

生产者和消费者通过 shmat 将共享内存映射到各自的内存空间，在不同进程里面映射的位置不同。同时，共享内存需要通过信号量来进行保护。

如下图所示：



![img](https://static001.geekbang.org/resource/image/46/0b/469552bffe601d594c432d4fad97490b.png)



## 41 IPC(中) 共享内存的创建和映射

共享内存的创建和映射过程：

1. 调用 shmget 创建共享内存，这会调用 shm_ops 结构的 newseg 方法，创建共享内存对象 shmid_kernel
2. 在 shmem 文件系统中创建一个文件 file1，共享内存对象 shmid_kernel 会指向这个文件
3. 调用 shmat，将共享内存映射到虚拟地址空间
4. 创建用于内存映射到文件的 file2 和 shm_file_data
5. 关联内存区域 vm_area_struct 和 file2，并调用 file2 的 mmap 函数
6. file2 的 mmap 函数 shm_mmap 会调用 file1 的 mmap 函数 shmem_mmap，完成内存映射，大体过程如下：

![img](https://static001.geekbang.org/resource/image/20/51/20e8f4e69d47b7469f374bc9fbcf7251.png)



## 42 IPC(下) 信号量的内核机制

信号量机制：

1. 调用 semget 创建信号量集合，进而调用 sem_ops 的 newary 方法，创建一个信号量集合 sem_array
2. 调用 semctl 初始化信号量
3. 再调用 semop 操作信号量，创建信号量操作结果 sem_queue，放入队列
4. 创建 undo 结构，放入链表

如下图所示：

![img](https://static001.geekbang.org/resource/image/60/7c/6028c83b0aa00e65916988911aa01b7c.png)



## 43 预习：Socket通信之网络协议基本原理

两种协议模型：OSI 七层模型和 TCP/IP 模型

![img](https://static001.geekbang.org/resource/image/92/0e/92f8e85f7b9a9f764c71081b56286e0e.png)



数据包的发送过程，这里有一张神图：

![img](https://static001.geekbang.org/resource/image/98/28/98a4496fff94eb02d1b1b8ae88f8dc28.jpeg)



## 43 Socket 通信



socket 通信的网络子系统：

![img](https://static001.geekbang.org/resource/image/d3/5c/d34e667d1c3340deb8c82a2d44f2a65c.png)

四层及以下都属于内核层面的东西。



## 44 Socket 的内核数据结构

TCP 三次握手的底层实现流程：

![img](https://static001.geekbang.org/resource/image/ab/df/ab92c2afb4aafb53143c471293ccb2df.png)



整个 socket 调用过程如下：

![img](https://static001.geekbang.org/resource/image/c0/d8/c028381cf45d65d3f148e57408d26bd8.png)



socket 系统调用有三个级别的参数 family、type、protocal，通过这三级参数，分别在 net_proto_family 表中找到 type 链表，在 type 链表中找到 protocal 对应的操作，这个操作分为两层，对于 TCP 协议来讲，第一层是 inet_stream_ops 层，第二层是 tcp_prot 层。



## 45 发送网络数据包(一)

数据包的发送需要经历这样的几个层次：

- **VFS层：** write 找到 struct file，根据里面的 file_operations 定义，调用 sock_write_iter 函数，接着调用 sock_sendmsg 函数
- **Socket 层：** 从 struct file 里面的 private_data 得到 struct socket，根据里面的 ops 定义，调用 inet_sendmsg 函数
- **Sock 层：** 从 struct socket 里面的 sk_buff 得到 struct sock ，根据里面 sk_prot 定义，调用 tcp_sendmsg 函数
- **TCP 层：** tcp_sendmsg 函数会调用 tcp_write_xmit 函数，tcp_write_xmit 函数会调用 tcp_transmit_skb，这里实现了 TCP 层面向连接的逻辑。
- **IP 层：** 扩展 struct sock，得到 struct inet_connection_sock，根据里面 icsk_af_ops 的定义，调用 ip_queue_xmit 函数

如下图所示：

![img](https://static001.geekbang.org/resource/image/dc/44/dc66535fa7e1a10fd6d728865f6c9344.png)



其中，sk_buff 的结构如下所示：

![img](https://static001.geekbang.org/resource/image/9a/b8/9ad34c3c748978f915027d5085a858b8.png)



## 46 发送网络包(下)

接着上面：

- **IP 层：** 扩展 struct sock，得到 struct inet_connection_sock，根据里面 icsk_af_ops 的定义，调用 ip_queue_xmit 函数
- **IP 层：** ip_route_output_ports 函数里面会调用 fib_lookup 查找路由表，FIB 全称是 Forwarding Information Base，转发信息表，也就是路由表
- 在 IP 层里面做的另一件事是填写 IP 层的头，以及 通过 iptables 规则
- **Mac 层：** IP 层调用 ip_finish_output 进行 MAC 层，MAC 层需要 ARP 得到 MAC 地址，调用 `__neigh_lookup_noref` 查找属于同一个网段的邻居，它会调用 neigh_probe 发送 ARP，有了 MAC 地址，就可以调用 dev_queue_xmit 发送二层网络包了，接着调用 `__dev_xmit_skb` 将请求放入队列
- **设备层：** 网络包的发送会触发一个软中断 NET_TX_SOFTIRQ 来处理队列中的数据，这个软中断的处理函数是 net_tx_action，在该函数中，会将网络包从队列上拿下来，调用网络设备的传输函数 `ixgb_xmit_frame` 将网络包发到设备的队列上去

如下图所示：

![img](https://static001.geekbang.org/resource/image/79/6f/79cc42f3163d159a66e163c006d9f36f.png)

其中，iptables 的调用关系图如下：

![img](https://static001.geekbang.org/resource/image/75/4d/75c8257049eed99499e802fcc2eacf4d.png)

Iptables 有表和链的概念，最终是两个表：

filter 表处理过滤功能，主要包含三个链：

- INPUT 链：过滤所有目标地址是本机的数据包
- FORWARD 链：过滤所有路过本机的数据包
- OUTPUT 链：过滤所有由本机产生的数据包

nat 表主要处理网络地址转换，进行 SNAT 和 DNAT，含三个链：

- PREROUTING 链：可以在数据包达到时改变目标地址
- OUTPUT 链：可以改变本地产生的数据包的目标地址
- POSTROUTING链：在数据包离开时改变数据包的源地址



![img](https://static001.geekbang.org/resource/image/76/da/765e5431fe4b17f62b1b5712cc82abda.png)



## 47 接收网络包(上)

网络数据包的接收也分以下几个层次：

- **硬件层：** 网卡收到包之后，通过 DMA 技术，拷贝到 Ring Buffer 中。接着通过中断通知 CPU 有新的包到来
- **设备驱动层：** 网卡驱动程序注册中断处理函数 `ixgb_intr`，中断处理函数分前半段和后半段，前半段处理完需要暂时屏蔽中断的核心流程之后，通过软中断 `NET_RX_SOFTIRQ` 触发接下来的处理过程，软中断处理函数是 `net_rx_action`，调用 `napi_poll` 从 Ring Buffer 中读取数据到内核 struct sk_buff。
- **数据链路层：** 调用 `netif_receive_skb` 进入内核网络协议栈，进行一些关于 VLAN 的二层处理
- **网络层：** 调用 `ip_rcv` 进入 IP 层，在 IP 层，会处理 iptables 规则，调用 `ip_local_deliver` 交给 TCP 层
- **传输层：** 调用 `tcp_v4_rcv` 层

![img](https://static001.geekbang.org/resource/image/a5/37/a51af8ada1135101e252271626669337.png)



## 48 接收网络包(下)

用户态从内核协议栈读取数据包的过程也可以分为以下几个层次：

- **VFS 层：** read 系统调用找到 struct file，根据里面的 file_operations 的定义，调用 sock_read_iter 函数，接着调用 sock_recvmsg 函数
- **Socket 层：** 从 struct file 里面的 private_data 得到 struct socket，根据里面 ops 的定义，调用 inet_recvmsg 函数
- **Sock 层：** 从 struct socket 里面的 sk 得到 struct sock，根据里面 sk_prot 定义，调用 tcp_recvmsg 函数
- **TCP 层：** tcp_recvmsg 依次读取 receive_queue 队列，prequeue 队列和 backlog 队列

![img](https://static001.geekbang.org/resource/image/20/52/20df32a842495d0f629ca5da53e47152.png)



## 49 虚拟机

qemu-kvm 的半虚拟化模型如下所示：

![img](https://static001.geekbang.org/resource/image/f7/22/f748fd6b6b84fa90a1044a92443c3522.png)



虚拟机之间的通信模型：

![img](https://static001.geekbang.org/resource/image/da/a7/da83bb01b7ed63ac0062b5cc835099a7.png)

## 50 计算虚拟化之 CPU

本节来看下用户态的 Qemu 和 内核态的 kvm 如何一起协作，来创建虚拟机，实现 CPU 的虚拟化。

Qemu 启动的一般命令：

```sh
qemu-system-x86_64 -enable-kvm -name ubuntutest  -m 2048 -hda ubuntutest.qcow2 -vnc :19 -net nic,model=virtio -nettap,ifname=tap0,script=no,downscript=no
```



qemu 的 main 函数在 vl.c 下面，第一步初始化所有的 Module，调用：

```
module_call_init(MODULE_INIT_QOM);
```

qemu 为了模拟各种各样的设备，需要管理各种各样的设备，定义一个 qemu 模块会调用 type_init，每个模块会顶一个 TypeInfo，会通过 type_init 变为全局的 TypeImpl。整个关系图如下所示：

![](E:/%E4%B8%AA%E4%BA%BA/wiki/timenotes/docs/linux/images/qemu.png)



## 51 计算虚拟化之 CPU （下）

CPU虚拟化的大体过程：

- 定义 CPU 类型的 TypeInfo 和 Typelmpl，继承关系，并且声明它的类型初始化函数
- 在 qemu 的 main 函数中调用 MachineClass 的 init 函数，这个函数既会初始化 CPU 也会初始化内存。
- CPU 初始化的时候，会调用 pc_new_cpu 创建一个 CPU，他会调用 CPU 类的初始化函数
- 每个 VCPU 会调用 qemu_thread_create 创建一个线程，线程的执行函数为 qemu_kvm_cpu_thread_fn
- 其中会调用 kvm_vm_ioctl(KVM_CREATE_VCPU)，在内核的 KVM 里面，创建一个结构 struct vcpu_vmx，表示这个虚拟 CPU。
- 接着调用 kvm_vcpu_ioctl(KVM_RUN)，在内核的 KVM 里面运行这个虚拟机 CPU。

## 52 计算虚拟化之 内存虚拟化

虚拟机内存包括：

- 虚拟机里面的虚拟内存（GVA）：这是虚拟机里的进程看到的内存空间
- 虚拟机里的物理内存（GPA）：这是虚拟机里面的操作系统看到内存，虚拟机物理内存
- 物理机的虚拟内存（HVA）：这是物理机的 qemu 进程看到的内存空间
- 物理机的物理内存（HPA）：这是物理机上的操作系统看到的内存



虚拟机的内存管理也需要用户态的qemu和内核态的 KVM 共同完成，为了加速内存映射，需要借助硬件的 EPT 技术。

![](E:/%E4%B8%AA%E4%BA%BA/wiki/timenotes/docs/linux/images/qemumem.jpg)



## 53 存储虚拟化(上)

virtio是对虚拟化IO设备的一种标准，加载虚拟机操作系统的驱动，统一加载 virtio 即可。



![img](https://static001.geekbang.org/resource/image/1e/33/1e13ffd5ac846c52739291cb489d0233.png)



virtio 的架构可以分为四层：

virtio 前端驱动层(包括各种针对不同硬件的设备驱动，如硬盘 virtio_blk，网络 virtio_net)，中间是 virtio 层和 virtio-ring 层(主要实现虚拟队列接口)，最低端是virtio 后端驱动层。

![img](https://static001.geekbang.org/resource/image/2e/f3/2e9ef612f7b80ec9fcd91e200f4946f3.png)

整个前后端通信的流程如下图所示：

![img](https://static001.geekbang.org/resource/image/1f/4b/1f0c3043a11d6ea1a802f7d0f3b0b34b.jpg)



## 54 存储虚拟化(下)

virtio vring 的队列结构：

![img](https://static001.geekbang.org/resource/image/49/db/49414d5acc81933b66410bbba102b0db.jpg)

vring 的三个成员：

- Viring_desc 指向分配的内存块，用于存放 guest 和 qemu 之间传输的数据
- avail->ring[] 是发送端维护的环形队列，指向需要接收端处理的 ring_desc
- used->ring[] 是接收端维护的环形队列，执行已经处理过了的 vring_desc



Virtio_blk 调用的整个过程如下所示：

![img](https://static001.geekbang.org/resource/image/79/0c/79ad143a3149ea36bc80219940d7d00c.jpg)





## 55 网络虚拟化

网络虚拟化模型：

![img](https://static001.geekbang.org/resource/image/24/d3/243e93913b18c3ab00be5676bef334d3.png)

整个虚拟网络通信过程如下所示：

![img](https://static001.geekbang.org/resource/image/e3/44/e329505cfcd367612f8ae47054ec8e44.jpg)



## 56 容器

容器三大底层技术：

- Namespace
- CGroup
- 镜像



容器带来的很多便捷功能：

- 持续集成，build once，run everywhere
- 弹性伸缩
- 跨云迁移
- 。。。



Docker 限制资源的使用参数：

- `-c 或者 --cpu-shares` 限定 CPU 占用比例
- `--cpus` 限定容器能使用的 CPU 核数
- `--cpuset` 让容器运行在某些核上
- `-m 或者 --memory` 容器能使用的最大内存大小
- `-memory-swap` 容器能使用的 swap 大小
- `-memory-swappiness` 设置容器使用的匿名页 swap 的比例
- `-memory-reservation` 设置一个内存使用的 soft limit，docker 发现主机内存不足，会执行 OOM 操作，该值小于 `-m` 设置的值。
- `-kernel-memory` 容器能够使用的 kernel memory 大小
- `-oom-kill-disable` 是否允许 OOM 的时候杀死容器



整个 Docker 运行模型如下所示：

![img](https://static001.geekbang.org/resource/image/5a/c5/5a499cb50a1b214a39ddf19cbb63dcc5.jpg)



## 57 Namespace 技术

Namespace 隔离的资源包括：

- UTS
- User
- Mount
- PID
- Network



操作 Namespace 的常用指令：

- nsenter：用来运行一个进程，进入指定的 Namespace，比如
  - `nsenter --target 58212 --mount --uts --ipc --net --pid -- env --ignore-environment-- /bin/bash`
- unshare：离开当前的 Namespace，创建且加入新的 Namespace，然后执行参数中指定的命令。
  - `unshare --mount --ipc --pid --net --mount-proc=/proc --fork /bin/bash`



还可以通过函数来操作 Namespace

- clone：创建一个新的进程，并把它放到新的 Namespace 中。

```
int clone(int (*fn)(void *), void *child_stack, int flags, void *arg);
```

- setns：将当前进程加入到已有的 Namespace 中

  ```
  int setns(int fd, int nstype);
  ```

- unshare：当前进程退出当前的 Namespace，并加入到新创建的 Namespace。

```sh
int unshare(int flags);
```



![img](https://static001.geekbang.org/resource/image/56/d7/56bb9502b58628ff3d1bee83b6f53cd7.png)



## 58 CGroup

CGroup 对资源的限制包括以下几类：

- CPU 子系统，主要限制进程的 CPU 使用率
- cpuacct子系统，可以统计cgroups 中的进程的 CPU 使用报告
- cpuset 子系统，可以为 CGroups中的进程分配单独的 CPU 节点或者内存节点
- memory 子系统，可以限制进程的 memory 使用量
- blkio 子系统，可以限制进程的块设备 io
- devices子系统，可以控制进程能够访问某些设备
- net_cls 子系统，可以标记 CGroups中进程的网络数据包，然后可以使用 tc模块对数据包进行控制
- freezer 子系统，可以挂起或者恢复 CGroups 中的进程



总结下来，CGroup 对于 docker 资源的控制，在用户态的表现如下图所示：

```
docker run -d --cpu-shares 513 --cpus 2 --cpuset-cpus 1,3 --memory 1024M --memory-swap 1234M --memory-swappiness 7 -p 8081:80 testnginx:1

# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                  NAMES
3dc0601189dd        testnginx:1         "/bin/sh -c 'nginx -…"   About a minute ago   Up About a minute   0.0.0.0:8081->80/tcp   boring_cohen
```



![img](https://static001.geekbang.org/resource/image/1c/0f/1c762a6283429ff3587a7fc370fc090f.png)



对于内核中 CGroup 的工作机制，总结如下：

![img](https://static001.geekbang.org/resource/image/c9/c4/c9cc56d20e6a4bac0f9657e6380a96c4.png)

