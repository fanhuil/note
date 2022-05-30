# Linux 文件系统

在 Linux 中，最直观、最可见的部分就是 `文件系统(file system)`。下面我们就来一起探讨一下关于 Linux 中国的文件系统，系统调用以及文件系统实现背后的原理和思想。这些思想中有一些来源于 MULTICS，现在已经被 Windows 等其他操作系统使用。Linux 的设计理念就是 `小的就是好的(Small is Beautiful)` 。虽然 Linux 只是使用了最简单的机制和少量的系统调用，但是 Linux 却提供了强大而优雅的文件系统。

## Linux 文件系统基本概念

Linux 在最初的设计是 MINIX1 文件系统，它只支持 14 字节的文件名，它的最大文件只支持到 64 MB。在 MINIX 1 之后的文件系统是 ext 文件系统。ext 系统相较于 MINIX 1 来说，在支持字节大小和文件大小上均有很大提升，但是 ext 的速度仍没有 MINIX 1 快，于是，ext 2 被开发出来，它能够支持长文件名和大文件，而且具有比 MINIX 1 更好的性能。这使他成为 Linux 的主要文件系统。只不过 Linux 会使用 `VFS` 曾支持多种文件系统。在 Linux 链接时，用户可以动态的将不同的文件系统挂载倒 VFS 上。

Linux 中的文件是一个任意长度的字节序列，Linux 中的文件可以包含任意信息，比如 ASCII 码、二进制文件和其他类型的文件是不加区分的。

为了方便起见，文件可以被组织在一个目录中，目录存储成文件的形式在很大程度上可以作为文件处理。目录可以有子目录，这样形成有层次的文件系统，Linux 系统下面的根目录是 `/` ，它通常包含了多个子目录。字符 `/` 还用于对目录名进行区分，例如 **/usr/cxuan** 表示的就是根目录下面的 usr 目录，其中有一个叫做 cxuan 的子目录。

下面我们介绍一下 Linux 系统根目录下面的目录名

* `/bin`，它是重要的二进制应用程序，包含二进制文件，系统的所有用户使用的命令都在这里
* `/boot`，启动包含引导加载程序的相关文件
* `/dev`，包含设备文件，终端文件，USB 或者连接到系统的任何设备
* `/etc`，配置文件，启动脚本等，包含所有程序所需要的配置文件，也包含了启动/停止单个应用程序的启动和关闭 shell 脚本
* `/home`，本地主要路径，所有用户用 home 目录存储个人信息
* `/lib`，系统库文件，包含支持位于 /bin 和 /sbin 下的二进制库文件
* `/lost+found`，在根目录下提供一个遗失+查找系统，必须在 root 用户下才能查看当前目录下的内容
* `/media`，挂载可移动介质
* `/mnt`，挂载文件系统
* `/opt`，提供一个可选的应用程序安装目录
* `/proc`，特殊的动态目录，用于维护系统信息和状态，包括当前运行中进程信息
* `/root`，root 用户的主要目录文件夹
* `/sbin`，重要的二进制系统文件
* `/tmp`， 系统和用户创建的临时文件，系统重启时，这个目录下的文件都会被删除
* `/usr`，包含绝大多数用户都能访问的应用程序和文件
* `/var`，经常变化的文件，诸如日志文件或数据库等

在 Linux 中，有两种路径，一种是 `绝对路径(absolute path)` ，绝对路径告诉你从根目录下查找文件，绝对路径的缺点是太长而且不太方便。还有一种是 `相对路径(relative path)` ，相对路径所在的目录也叫做`工作目录(working directory)`。

如果 `/usr/local/books` 是工作目录，那么 shell 命令

```shell
cp books books-replica 
```

就表示的是相对路径，而

```shell
cp /usr/local/books/books /usr/local/books/books-replica
```

则表示的是绝对路径。

在 Linux 中经常出现一个用户使用另一个用户的文件或者使用文件树结构中的文件。两个用户共享同一个文件，这个文件位于某个用户的目录结构中，另一个用户需要使用这个文件时，必须通过绝对路径才能引用到他。如果绝对路径很长，那么每次输入起来会变的非常麻烦，所以 Linux 提供了一种 `链接(link)` 机制。

举个例子，下面是一个使用链接之前的图

<img src="https://s1.ax1x.com/2020/08/29/dTyinK.png" alt="21" border="0" style="zoom:50%;" >

以上所示，比如有两个工作账户 jianshe 和 cxuan，jianshe 想要使用 cxuan 账户下的 A 目录，那么它可能会输入 `/usr/cxuan/A` ，这是一种未使用链接之后的图。

使用链接后的示意如下

<img src="https://s1.ax1x.com/2020/08/29/dTy96x.png" alt="22" border="0" style="zoom:50%;" >

现在，jianshe 可以创建一个链接来使用 cxuan 下面的目录了。‘

当一个目录被创建出来后，有两个目录项也同时被创建出来，它们就是 `.` 和 `..` ，前者代表工作目录自身，后者代表该目录的父目录，也就是该目录所在的目录。这样一来，在 /usr/jianshe 中访问 cxuan 中的目录就是 `../cxuan/xxx`

Linux 文件系统不区分磁盘的，这是什么意思呢？一般来说，一个磁盘中的文件系统相互之间保持独立，如果一个文件系统目录想要访问另一个磁盘中的文件系统，在 Windows 中你可以像下面这样。

<img src="https://s1.ax1x.com/2020/08/29/dTyCX6.png" alt="23" border="0">

两个文件系统分别在不同的磁盘中，彼此保持独立。

而在 Linux 中，是支持`挂载`的，它允许一个磁盘挂在到另外一个磁盘上，那么上面的关系会变成下面这样

<img src="https://s1.ax1x.com/2020/08/29/dTsvtJ.png" alt="24" border="0">

挂在之后，两个文件系统就不再需要关心文件系统在哪个磁盘上了，两个文件系统彼此可见。

Linux 文件系统的另外一个特性是支持 `加锁(locking)`。在一些应用中会出现两个或者更多的进程同时使用同一个文件的情况，这样很可能会导致`竞争条件(race condition)`。一种解决方法是对其进行加不同粒度的锁，就是为了防止某一个进程只修改某一行记录从而导致整个文件都不能使用的情况。

POSIX 提供了一种灵活的、不同粒度级别的锁机制，允许一个进程使用一个不可分割的操作对一个字节或者整个文件进行加锁。加锁机制要求尝试加锁的进程指定其 **要加锁的文件，开始位置以及要加锁的字节**

Linux 系统提供了两种锁：**共享锁和互斥锁**。如果文件的一部分已经加上了共享锁，那么再加排他锁是不会成功的；如果文件系统的一部分已经被加了互斥锁，那么在互斥锁解除之前的任何加锁都不会成功。为了成功加锁、请求加锁的部分的所有字节都必须是可用的。

在加锁阶段，进程需要设计好加锁失败后的情况，也就是判断加锁失败后是否选择阻塞，如果选择阻塞式，那么当已经加锁的进程中的锁被删除时，这个进程会解除阻塞并替换锁。如果进程选择非阻塞式的，那么就不会替换这个锁，会立刻从系统调用中返回，标记状态码表示是否加锁成功，然后进程会选择下一个时间再次尝试。

加锁区域是可以重叠的。下面我们演示了三种不同条件的加锁区域。

<img src="https://s1.ax1x.com/2020/08/29/dTsjk4.png" alt="25" border="0">

如上图所示，A 的共享锁在第四字节到第八字节进行加锁

<img src="https://s1.ax1x.com/2020/08/29/dTsxh9.png" alt="26" border="0">

如上图所示，进程在 A 和 B 上同时加了共享锁，其中 6 - 8 字节是重叠锁

<img src="https://s1.ax1x.com/2020/08/29/dTySpR.png" alt="27" border="0">

如上图所示，进程 A 和 B 和 C 同时加了共享锁，那么第六字节和第七字节是共享锁。

如果此时一个进程尝试在第 6 个字节处加锁，此时会设置失败并阻塞，由于该区域被 A B C 同时加锁，那么只有等到 A B C 都释放锁后，进程才能加锁成功。

## Linux 文件系统调用

许多系统调用都会和文件与文件系统有关。我们首先先看一下对单个文件的系统调用，然后再来看一下对整个目录和文件的系统调用。

为了创建一个新的文件，会使用到 `creat` 方法，注意没有 `e`。

>这里说一个小插曲，曾经有人问 UNIX 创始人 Ken Thompson，如果有机会重新写 UNIX ，你会怎么办，他回答自己要把 creat 改成 create ，哈哈哈哈。

这个系统调用的两个参数是文件名和保护模式

```shell
fd = creat("aaa",mode);
```

这段命令会创建一个名为 aaa 的文件，并根据 mode 设置文件的保护位。这些位决定了哪个用户可能访问文件、如何访问。

creat 系统调用不仅仅创建了一个名为 aaa 的文件，还会打开这个文件。为了允许后续的系统调用访问这个文件，这个 creat 系统调用会返回一个 `非负整数`， 这个就叫做 `文件描述符(file descriptor)`，也就是上面的 fd。

如果在已经存在的文件上调用了 creat 系统调用，那么该文件中的内容会被清除，从 0 开始。通过设置合适的参数，`open` 系统调用也能够创建文件。

下面让我们看一看主要的系统调用，如下表所示

| 系统调用                             | 描述                     |
| ------------------------------------ | ------------------------ |
| fd = creat(name,mode)                | 一种创建一个新文件的方式 |
| fd = open(file, ...)                 | 打开文件读、写或者读写   |
| s = close(fd)                        | 关闭一个打开的文件       |
| n = read(fd, buffer, nbytes)         | 从文件中向缓存中读入数据 |
| n = write(fd, buffer, nbytes)        | 从缓存中向文件中写入数据 |
| position = lseek(fd, offset, whence) | 移动文件指针             |
| s = stat(name, &buf)                 | 获取文件信息             |
| s = fstat(fd, &buf)                  | 获取文件信息             |
| s = pipe(&fd[0])                     | 创建一个管道             |
| s = fcntl(fd,...)                    | 文件加锁等其他操作       |

为了对一个文件进行读写的前提是先需要打开文件，必须使用 creat 或者 open 打开，参数是打开文件的方式，是只读、可读写还是只写。open 系统调用也会返回文件描述符。打开文件后，需要使用 `close` 系统调用进行关闭。close 和 open 返回的 fd 总是未被使用的最小数量。

>什么是文件描述符？文件描述符就是一个数字，这个数字标示了计算机操作系统中打开的文件。它描述了数据资源，以及访问资源的方式。

当程序要求打开一个文件时，内核会进行如下操作

* 授予访问权限
* 在`全局文件表(global file table)`中创建一个`条目(entry)`
* 向软件提供条目的位置

文件描述符由唯一的非负整数组成，系统上每个打开的文件至少存在一个文件描述符。文件描述符最初在 Unix 中使用，并且被包括 Linux，macOS 和 BSD 在内的现代操作系统所使用。

当一个进程成功访问一个打开的文件时，内核会返回一个文件描述符，这个文件描述符指向全局文件表的 entry 项。这个文件表项包含文件的 inode 信息，字节位移，访问限制等。例如下图所示

<img src="https://s1.ax1x.com/2020/08/29/dTyp11.png" alt="28" border="0">

默认情况下，前三个文件描述符为 `STDIN(标准输入)`、`STDOUT(标准输出)`、`STDERR(标准错误)`。

标准输入的文件描述符是 0 ，在终端中，默认为用户的键盘输入

标准输出的文件描述符是 1 ，在终端中，默认为用户的屏幕

与错误有关的默认数据流是 2，在终端中，默认为用户的屏幕。

在简单聊了一下文件描述符后，我们继续回到文件系统调用的探讨。

在文件系统调用中，开销最大的就是 read 和 write 了。read 和 write 都有三个参数

* `文件描述符`：告诉需要对哪一个打开文件进行读取和写入
* `缓冲区地址`：告诉数据需要从哪里读取和写入哪里
* `统计`：告诉需要传输多少字节

这就是所有的参数了，这个设计非常简单轻巧。

虽然几乎所有程序都按顺序读取和写入文件，但是某些程序需要能够随机访问文件的任何部分。与每个文件相关联的是一个指针，该指针指示文件中的当前位置。顺序读取（或写入）时，它通常指向要读取（写入）的下一个字节。如果指针在读取 1024 个字节之前位于 4096 的位置，则它将在成功读取系统调用后自动移至 5120 的位置。

`Lseek` 系统调用会更改指针位置的值，以便后续对 read 或 write 的调用可以在文件中的任何位置开始，甚至可以超出文件末尾。

>lseek = Lseek ，段首大写。

lseek 避免叫做 seek 的原因就是 seek 已经在之前 16 位的计算机上用于搜素功能了。

`Lseek` 有三个参数：第一个是文件的文件描述符，第二个是文件的位置；第三个告诉文件位置是相对于文件的开头，当前位置还是文件的结尾

```c
lseek(int fildes, off_t offset, int whence);
```

lseek 的返回值是更改文件指针后文件中的绝对位置。lseek 是唯一从来不会造成真正磁盘查找的系统调用，它只是更新当前的文件位置，这个文件位置就是内存中的数字。

对于每个文件，Linux 都会跟踪文件模式（常规，目录，特殊文件），大小，最后修改时间以及其他信息。程序能够通过 `stat` 系统调用看到这些信息。第一个参数就是文件名，第二个是指向要放置请求信息结构的指针。这些结构的属性如下图所示。

| 存储文件的设备           |
| ------------------------ |
| 存储文件的设备           |
| i-node 编号              |
| 文件模式(包括保护位信息) |
| 文件链接的数量           |
| 文件所有者标识           |
| 文件所属的组             |
| 文件大小(字节)           |
| 创建时间                 |
| 最后一个修改/访问时间    |

`fstat` 调用和 `stat` 相同，只有一点区别，fstat 可以对打开文件进行操作，而 stat 只能对路径进行操作。

`pipe` 文件系统调用被用来创建 shell 管道。它会创建一系列的`伪文件`，来缓冲和管道组件之间的数据，并且返回读取或者写入缓冲区的文件描述符。在管道中，像是如下操作

```shell
sort <in | head –40
```

sort 进程将会输出到文件描述符1，也就是标准输出，写入管道中，而 head 进程将从管道中读入。在这种方式中，sort 只是从文件描述符 0 中读取并写入到文件描述符 1 （管道）中，甚至不知道它们已经被重定向了。如果没有重定向的话，sort 会自动的从键盘读入并输出到屏幕中。

最后一个系统调用是 `fcntl`，它用来锁定和解锁文件，应用共享锁和互斥锁，或者是执行一些文件相关的其他操作。

现在我们来关心一下和整体目录和文件系统相关的系统调用，而不是把精力放在单个的文件上，下面列出了这些系统调用，我们一起来看一下。

| 系统调用                  | 描述                   |
| ------------------------- | ---------------------- |
| s = mkdir(path,mode)      | 创建一个新的目录       |
| s = rmdir(path)           | 移除一个目录           |
| s = link(oldpath,newpath) | 创建指向已有文件的链接 |
| s = unlink(path)          | 取消文件的链接         |
| s = chdir(path)           | 改变工作目录           |
| dir = opendir(path)       | 打开一个目录读取       |
| s = closedir(dir)         | 关闭一个目录           |
| dirent = readdir(dir)     | 读取一个目录项         |
| rewinddir(dir)            | 回转目录使其在此使用   |

可以使用 mkdir 和 rmdir 创建和删除目录。但是需要注意，只有目录为空时才可以删除。

创建一个指向已有文件的链接时会创建一个`目录项(directory entry)`。系统调用 link 来创建链接，oldpath 代表已有的路径，newpath 代表需要链接的路径，使用 `unlink` 可以删除目录项。当文件的最后一个链接被删除时，这个文件会被自动删除。

使用 `chdir` 系统调用可以改变工作目录。

最后四个系统调用是用于读取目录的。和普通文件类似，他们可以被打开、关闭和读取。每次调用 `readdir` 都会以固定的格式返回一个目录项。用户不能对目录执行写操作，但是可以使用 creat 或者 link 在文件夹中创建一个目录，或使用 unlink 删除一个目录。用户不能在目录中查找某个特定文件，但是可以使用 `rewindir` 作用于一个打开的目录，使他能在此从头开始读取。

## Linux 文件系统的实现

下面我们主要讨论一下 `虚拟文件系统(Virtual File System)`。 VFS 对高层进程和应用程序隐藏了 Linux 支持的所有文件系统的区别，以及文件系统是存储在本地设备，还是需要通过网络访问远程设备。设备和其他特殊文件和 VFS 层相关联。接下来，我们就会探讨一下第一个 Linux 广泛传播的文件系统： `ext2`。随后，我们就会探讨 `ext4` 文件系统所做的改进。各种各样的其他文件系统也正在使用中。 所有 Linux 系统都可以处理多个磁盘分区，每个磁盘分区上都有不同的文件系统。

### Linux 虚拟文件系统

为了能够使应用程序能够在不同类型的本地或者远程设备上的文件系统进行交互，因为在 Linux 当中文件系统千奇百种，比较常见的有 EXT3、EXT4，还有基于内存的 ramfs、tmpfs 和基于网络的 nfs，和基于用户态的 fuse，当然 fuse 应该不能完全的文件系统，只能算是一个能把文件系统实现放到用户态的模块，满足了内核文件系统的接口，他们都是文件系统的一种实现。对于这些文件系统，Linux 做了一层抽象就是 `VFS `虚拟文件系统，

下表总结了 VFS 支持的四个主要的文件系统结构。

| 对象   | 描述                       |
| ------ | -------------------------- |
| 超级块 | 特定的文件系统             |
| Dentry | 目录项，路径的一个组成部分 |
| I-node | 特定的文件                 |
| File   | 跟一个进程相关联的打开文件 |

`超级块(superblock)` 包含了有关文件系统布局的重要信息，超级块如果遭到破坏那么就会导致整个文件系统不可读。

`i-node` 索引节点，包含了每一个文件的描述符。

>在 Linux 中，目录和设备也表示为文件，因为它们具有对应的 i-node

超级块和索引块所在的文件系统都在磁盘上有对应的结构。

为了便于某些目录操作和路径遍历，比如 /usr/local/cxuan，VFS 支持一个 `dentry` 数据结构，该数据结构代表着目录项。这个 dentry 数据结构有很多东西（http://books.gigatux.nl/mirror/kerneldevelopment/0672327201/ch12lev1sec7.html）这个数据结构由文件系统动态创建。

目录项被缓存在 `dentry_cache` 缓存中。例如，缓存条目会缓存 /usr 、 /usr/local 等条目。如果多个进程通过硬连接访问相同的文件，他们的文件对象将指向此缓存中的相同条目。

最后，文件数据结构是代表着打开的文件，也代表着内存表示，它根据 open 系统调用创建。它支持 **read、write、sendfile、lock** 和其他在我们之前描述的系统调用中。

在 VFS 下实现的实际文件系统不需要在内部使用完全相同的抽象和操作。 但是，它们必须在语义上实现与 VFS 对象指定的文件系统操作相同的文件系统操作。 四个 VFS 对象中每个对象的操作数据结构的元素都是指向基础文件系统中功能的指针。

### Linux Ext2 文件系统

现在我们一起看一下 Linux 中最流行的一个磁盘文件系统，那就是 `ext2` 。Linux 的第一个版本用于 `MINIX1` 文件系统，它的文件名大小被限制为最大 64 MB。MINIX 1 文件系统被永远的被它的扩展系统 ext 取代，因为 ext 允许更长的文件名和文件大小。由于 ext 的性能低下，ext 被其替代者 ext2 取代，ext2 目前仍在广泛使用。

一个 ext2 Linux 磁盘分区包含了一个文件系统，这个文件系统的布局如下所示

<img src="https://s1.ax1x.com/2020/09/29/0Z3k40.png" alt="linuxfilesystem009" border="0">

Boot 块也就是第 0 块不是让 Linux 使用的，而是用来加载和引导计算机启动代码的。在块 0 之后，磁盘分区被分成多个组，这些组与磁盘柱面边界所处的位置无关。

第一个块是 `超级块(superblock)`。它包含有关文件系统布局的信息，包括 i-node、磁盘块数量和以及空闲磁盘块列表的开始。下一个是 `组描述符(group descriptor)`，其中包含有关位图的位置，组中空闲块和 i-node 的数量以及组中的目录数量的信息。这些信息很重要，因为 ext2 会在磁盘上均匀分布目录。

图中的两个位图用来记录空闲块和空闲 i-node，这是从 MINIX 1文件系统继承的选择，大多数 UNIX 文件系统使用位图而不是空闲列表。每个位图的大小是一个块。如果一个块的大小是 1 KB，那么就限制了块组的数量是 8192 个块和 8192 个 i-node。块的大小是一个严格的限制，块组的数量不固定，在 4KB 的块中，块组的数量增大四倍。

在超级块之后分布的是 `i-node` 它们自己，i-node 取值范围是 1 - 某些最大值。每个 i-node 是 128 字节的 `long` ，这些字节恰好能够描述一个文件。i-node 包含了统计信息(包含了 `stat` 系统调用能获得的所有者信息，实际上 stat 就是从 i-node 中读取信息的)，以及足够的信息来查找保存文件数据的所有磁盘块。

在 i-node 之后的是 `数据块(data blocks)`。所有的文件和目录都保存在这。如果一个文件或者目录包含多个块，那么这些块在磁盘中的分布不一定是连续的，也有可能不连续。事实上，大文件块可能会被拆分成很多小块散布在整个磁盘上。

对应于目录的 i-node 分散在整个磁盘组上。如果有足够的空间，ext2 会把普通文件组织到与父目录相同的块组中，而把同一块上的数据文件组织成初始 `i-node` 节点。位图用来快速确定新文件系统数据的分配位置。在分配新的文件块时，ext2 也会给该文件预分配许多额外的数据块，这样可以减少将来向文件写入数据时产生的文件碎片。这种策略在整个磁盘上实现了文件系统的 `负载`，后续还有对文件碎片的排列和整理，而且性能也比较好。

为了达到访问的目的，需要首先使用 Linux 系统调用，例如 `open`，这个系统调用会确定打开文件的路径。路径分为两种，`相对路径` 和 `绝对路径`。如果使用相对路径，那么就会从当前目录开始查找，否则就会从根目录进行查找。

目录文件的文件名最高不能超过 255 个字符，它的分配如下图所示

<img src="https://s1.ax1x.com/2020/09/29/0Z3CHs.png" alt="linuxfilesystem010" border="0">

每一个目录都由整数个磁盘块组成，这样目录就可以整体的写入磁盘。在一个目录中，文件和子目录的目录项都是未经排序的，并且一个挨着一个。目录项不能跨越磁盘块，所以通常在每个磁盘块的尾部会有部分未使用的字节。

上图中每个目录项都由四个固定长度的属性和一个长度可变的属性组成。第一个属性是 `i-node` 节点数量，文件 first 的 i-node 编号是 19 ，文件 second 的编号是 42，目录 third 的 i-node 编号是 88。紧随其后的是 `rec_len` 域，表明目录项大小是多少字节，名称后面会有一些扩展，当名字以未知长度填充时，这个域被用来寻找下一个目录项，直至最后的未使用。这也是图中箭头的含义。紧随其后的是 `类型域`：F 表示的是文件，D 表示的是目录，最后是固定长度的文件名，上面的文件名的长度依次是 5、6、5，最后以文件名结束。

rec_len 域是如何扩展的呢？如下图所示

<img src="https://s1.ax1x.com/2020/09/29/0Z3iEn.png" alt="linuxfilesystem011" border="0">

我们可以看到，中间的 `second` 被移除了，所以将其所在的域变为第一个目录项的填充。当然，这个填充可以作为后续的目录项。

由于目录是按照线性的顺序进行查找的，因此可能需要很长时间才能在大文件末尾找到目录项。因此，系统会为近期的访问目录维护一个缓存。这个缓存用文件名来查找，如果缓存命中，那么就会避免线程搜索这样昂贵的开销。组成路径的每个部分都在目录缓存中保存一个 `dentry` 对象，并且通过 i-node 找到后续的路径元素的目录项，直到找到真正的文件 i - node。

比如说要使用绝对路径来寻找一个文件，我们暂定这个路径是 `/usr/local/file`，那么需要经过如下几个步骤：

* 首先，系统会确定根目录，它通常使用 2 号 i -node ，也就是索引 2 节点，因为索引节点 1 是 ext2 /3/4 文件系统上的`坏块`索引节点。系统会将一项放在 dentry 缓存中，以应对将来对根目录的查找。
* 然后，在根目录中查找字符串 `usr`，得到 /usr 目录的 i - node 节点号。/usr 的 i - node 同样也进入 dentry 缓存。然后节点被取出，并从中解析出磁盘块，这样就可以读取 /usr 目录并查找字符串 `local` 了。一旦找到这个目录项，目录 `/usr/local` 的 i - node 节点就可以从中获得。有了 /usr/local 的 i - node 节点号，就可以读取 i - node 并确定目录所在的磁盘块。最后，从 /usr/local 目录查找 file 并确定其 i - node 节点呢号。

如果文件存在，那么系统会提取 i - node 节点号并把它作为索引在 i - node 节点表中定位相应的 i - node 节点并装入内存。i - node 被存放在 i - node `节点表(i-node table)` 中，节点表是一个内核数据结构，它会持有当前打开文件和目录的 i - node 节点号。下面是一些 Linux 文件系统支持的 i - node 数据结构。

| 属性   | 字节 | 描述                                  |
| ------ | ---- | ------------------------------------- |
| Mode   | 2    | 文件属性、保护位、setuid 和 setgid 位 |
| Nlinks | 2    | 指向 i - node 节点目录项的数目        |
| Uid    | 2    | 文件所有者的 UID                      |
| Gid    | 2    | 文件所有者的 GID                      |
| Size   | 4    | 文件字节大小                          |
| Addr   | 60   | 12 个磁盘块以及后面 3 个间接块的地址  |
| Gen    | 1    | 每次重复使用 i - node 时增加的代号    |
| Atime  | 4    | 最近访问文件的时间                    |
| Mtime  | 4    | 最近修改文件的时间                    |
| Ctime  | 4    | 最近更改 i - node 的时间              |

现在我们来一起探讨一下文件读取过程，还记得 `read` 函数是如何调用的吗？

```c
n = read(fd,buffer,nbytes);
```

当内核接管后，它会从这三个参数以及内部表与用户有关的信息开始。内部表的其中一项是文件描述符数组。文件描述符数组用`文件描述符` 作为索引并为每一个打开文件保存一个表项。

文件是和 i - node 节点号相关的。那么如何通过一个文件描述符找到文件对应的 i - node 节点呢？

这里使用的一种设计思想是在文件描述符表和 i - node 节点表之间插入一个新的表，叫做 `打开文件描述符(open-file-description table)`。文件的读写位置会在打开文件描述符表中存在，如下图所示

<img src="https://s1.ax1x.com/2020/09/29/0Z3ZgU.png" alt="linuxfilesystem012" border="0">

我们使用 shell 、P1 和 P2 来描述一下父进程、子进程、子进程的关系。Shell 首先生成 P1，P1 的数据结构就是 Shell 的一个副本，因此两者都指向相同的打开文件描述符的表项。当 P1 运行完成后，Shell 的文件描述符仍会指向 P1 文件位置的打开文件描述。然后 Shell 生成了 P2，新的子进程自动继承文件的读写位置，甚至 P2 和 Shell 都不知道文件具体的读写位置。

上面描述的是父进程和子进程这两个 `相关` 进程，如果是一个不相关进程打开文件时，它将得到自己的打开文件描述符表项，以及自己的文件读写位置，这是我们需要的。

>因此，打开文件描述符相当于是给相关进程提供同一个读写位置，而给不相关进程提供各自私有的位置。

i - node 包含三个间接块的磁盘地址，它们每个指向磁盘块的地址所能够存储的大小不一样。

### Linux Ext4 文件系统

为了防止由于系统崩溃和电源故障造成的数据丢失，ext2 系统必须在每个数据块创建之后立即将其写入到磁盘上，磁盘磁头寻道操作导致的延迟是无法让人忍受的。为了增强文件系统的健壮性，Linux 依靠`日志文件系统`，ext3 是一个日志文件系统，它在 ext2 文件系统的基础之上做了改进，ext4 也是 ext3 的改进，ext4 也是一个日志文件系统。ext4 改变了 ext3 的块寻址方案，从而支持更大的文件和更大的文件系统大小。下面我们就来描述一下 ext4 文件系统的特性。

具有记录的文件系统最基本的功能就是`记录日志`，这个日志记录了按照顺序描述所有文件系统的操作。通过顺序写出文件系统数据或元数据的更改，操作不受磁盘访问期间磁盘头移动的开销。最终，这个变更会写入并提交到合适的磁盘位置上。如果这个变更在提交到磁盘前文件系统宕机了，那么在重启期间，系统会检测到文件系统未正确卸载，那么就会遍历日志并应用日志的记录来对文件系统进行更改。

Ext4 文件系统被设计用来高度匹配 ext2 和 ext3 文件系统的，尽管 ext4 文件系统在内核数据结构和磁盘布局上都做了变更。尽管如此，一个文件系统能够从 ext2 文件系统上卸载后成功的挂载到 ext4 文件系统上，并提供合适的日志记录。

日志是作为循环缓冲区管理的文件。日志可以存储在与主文件系统相同或者不同的设备上。日志记录的读写操作会由单独的 `JBD(Journaling Block Device)` 来扮演。

JBD 中有三个主要的数据结构，分别是 **log record(日志记录)、原子操作和事务**。一个日志记录描述了一个低级别的文件系统操作，这个操作通常导致块内的变化。因为像是 `write` 这种系统调用会包含多个地方的改动 --- i - node 节点，现有的文件块，新的文件块和空闲列表等。相关的日志记录会以原子性的方式分组。ext4 会通知系统调用进程的开始和结束，以此使 JBD 能够确保原子操作的记录都能被应用，或者一个也不被应用。最后，主要从效率方面考虑，JBD 会视原子操作的集合为事务。一个事务中的日志记录是连续存储的。只有在所有的变更一起应用到磁盘后，日志记录才能够被丢弃。

由于为每个磁盘写出日志的开销会很大，所以 ext4 可以配置为保留所有磁盘更改的日志，或者仅仅保留与文件系统元数据相关的日志更改。仅仅记录元数据可以减少系统开销，提升性能，但不能保证不会损坏文件数据。其他的几个日志系统维护着一系列元数据操作的日志，例如 SGI 的 XFS。

### /proc 文件系统

另外一个 Linux 文件系统是 `/proc` (process) 文件系统

>它的主要思想来源于贝尔实验室开发的第 8 版的 UNIX，后来被 BSD 和 System V 采用。

然而，Linux 在一些方面上对这个想法进行了扩充。它的基本概念是为系统中的每个进程在 `/proc` 中创建一个目录。目录的名字就是进程 PID，以十进制数进行表示。例如，`/proc/1024` 就是一个进程号为 1024 的目录。在该目录下是进程信息相关的文件，比如进程的命令行、环境变量和信号掩码等。事实上，这些文件在磁盘上并不存在磁盘中。当需要这些信息的时候，系统会按需从进程中读取，并以标准格式返回给用户。

许多 Linux 扩展与 `/proc` 中的其他文件和目录有关。它们包含各种各样的关于 CPU、磁盘分区、设备、中断向量、内核计数器、文件系统、已加载模块等信息。非特权用户可以读取很多这样的信息，于是就可以通过一种安全的方式了解系统情况。

### NFS 网络文件系统

从一开始，网络就在 Linux 中扮演了很重要的作用。下面我们会探讨一下 `NFS(Network File System)` 网络文件系统，它在现代 Linux 操作系统的作用是将不同计算机上的不同文件系统链接成一个逻辑整体。

#### NFS 架构

NFS 最基本的思想是允许任意选定的一些`客户端`和`服务器`共享一个公共文件系统。在许多情况下，所有的客户端和服务器都会在同一个 `LAN(Local Area Network)`  局域网内共享，但是这并不是必须的。也可能是下面这样的情况：如果客户端和服务器距离较远，那么它们也可以在广域网上运行。客户端可以是服务器，服务器可以是客户端，但是为了简单起见，我们说的客户端就是消费服务，而服务器就是提供服务的角度来聊。

每一个 NFS 服务都会导出一个或者多个目录供远程客户端访问。当一个目录可用时，它的所有子目录也可用。因此，通常整个目录树都会作为一个整体导出。服务器导出的目录列表会用一个文件来维护，这个文件是 `/etc/exports`，当服务器启动后，这些目录可以自动的被导出。客户端通过挂载这些导出的目录来访问它们。当一个客户端挂载了一个远程目录，这个目录就成为客户端目录层次的一部分，如下图所示。

<img src="https://s1.ax1x.com/2020/09/29/0Z3ECV.png" alt="linuxfilesystem013" border="0">

在这个示例中，一号客户机挂载到服务器的 bin 目录下，因此它现在可以使用 shell 访问 /bin/cat 或者其他任何一个目录。同样，客户机 1 也可以挂载到 二号服务器上从而访问 /usr/local/projects/proj1 或者其他目录。二号客户机同样可以挂载到二号服务器上，访问路径是 /mnt/projects/proj2。

从上面可以看到，由于不同的客户端将文件挂载到各自目录树的不同位置，同一个文件在不同的客户端有不同的访问路径和不同的名字。挂载点一般通常在客户端本地，服务器不知道任何一个挂载点的存在。

#### NFS 协议

由于 NFS 的协议之一是支持 `异构` 系统，客户端和服务器可能在不同的硬件上运行不同的操作系统，因此有必要在服务器和客户端之间进行接口定义。这样才能让任何写一个新客户端能够和现有的服务器一起正常工作，反之亦然。

NFS 就通过定义两个客户端 - 服务器协议从而实现了这个目标。协议就是客户端发送给服务器的一连串的请求，以及服务器发送回客户端的相应答复。

第一个 NFS 协议是处理挂载。客户端可以向服务器发送路径名并且请求服务器是否能够将服务器的目录挂载到自己目录层次上。因为服务器不关心挂载到哪里，因此请求不会包含挂载地址。如果路径名是合法的并且指定的目录已经被导出，那么服务器会将文件 `句柄` 返回给客户端。

>文件句柄包含唯一标识文件系统类型，磁盘，目录的i节点号和安全性信息的字段。

随后调用读取和写入已安装目录或其任何子目录中的文件，都将使用文件句柄。

当 Linux 启动时会在多用户之前运行 shell 脚本 /etc/rc 。可以将挂载远程文件系统的命令写入该脚本中，这样就可以在允许用户登陆之前自动挂载必要的远程文件系统。大部分 Linux 版本是支持`自动挂载`的。这个特性会支持将远程目录和本地目录进行关联。

相对于手动挂载到 /etc/rc 目录下，自动挂载具有以下优势

* 如果列出的 /etc/rc 目录下出现了某种故障，那么客户端将无法启动，或者启动会很困难、延迟或者伴随一些出错信息，如果客户根本不需要这个服务器，那么手动做了这些工作就白费了。
* 允许客户端并行的尝试一组服务器，可以实现一定程度的容错率，并且性能也可以得到提高。

另一方面，我们默认在自动挂载时所有可选的文件系统都是相同的。由于 NFS 不提供对文件或目录复制的支持，用户需要自己确保这些所有的文件系统都是相同的。因此，大部分的自动挂载都只应用于二进制文件和很少改动的只读的文件系统。

第二个 NFS 协议是为文件和目录的访问而设计的。客户端能够通过向服务器发送消息来操作目录和读写文件。客户端也可以访问文件属性，比如文件模式、大小、上次修改时间。NFS 支持大多数的 Linux 系统调用，但是 open 和 close 系统调用却不支持。

> 不支持 open 和 close 并不是一种疏忽，而是一种刻意的设计，完全没有必要在读一个文件之前对其进行打开，也没有必要在读完时对其进行关闭。

NFS 使用了标准的 UNIX 保护机制，使用 `rwx` 位来标示`所有者(owner)`、`组(groups)`、`其他用户` 。最初，每个请求消息都会携带调用者的 groupId 和 userId，NFS 会对其进行验证。事实上，它会信任客户端不会发生欺骗行为。可以使用公钥密码来创建一个安全密钥，在每次请求和应答中使用它验证客户端和服务器。

#### NFS 实现

即使客户端和服务器的代码实现是独立于 NFS 协议的，大部分的 Linux 系统会使用一个下图的三层实现，顶层是系统调用层，系统调用层能够处理 open 、 read 、 close 这类的系统调用。在解析和参数检查结束后调用第二层，`虚拟文件系统 (VFS)` 层。

<img src="https://s1.ax1x.com/2020/09/29/0Z3evF.png" alt="linuxfilesystem014" border="0">

VFS 层的任务是维护一个表，每个已经打开的文件都在表中有一个表项。VFS 层为每一个打开的文件维护着一个`虚拟i节点 `，简称为 v - node。v 节点用来说明文件是本地文件还是远程文件。如果是远程文件的话，那么 v - node 会提供足够的信息使客户端能够访问它们。对于本地文件，会记录其所在的文件系统和文件的 i-node ，因为现代操作系统能够支持多文件系统。虽然 VFS 是为了支持 NFS 而设计的，但是现代操作系统都会使用 VFS，而不管有没有 NFS。



关注公众号 程序员cxuan 回复 cxuan 领取优质资料。

我自己写了六本 PDF ，非常硬核，链接如下

我自己写了六本 PDF ，非常硬核，链接如下

我自己写了六本 PDF ，非常硬核，链接如下

![](https://img2020.cnblogs.com/blog/1515111/202009/1515111-20200928073258556-2142265096.png)

[cxuan 呕心沥血肝了四本 PDF。](https://mp.weixin.qq.com/s?__biz=MzI0ODk2NDIyMQ==&mid=2247485329&idx=1&sn=673f306bb229e73e8f671443488b42d4&chksm=e999f283deee7b95a3cce247907b6557bf5f228c85434fc6cbadf42b2ec4c64443742a8bea7a&token=581641926&lang=zh_CN#rd)

[cxuan 又肝了两本 PDF。](https://mp.weixin.qq.com/s?__biz=MzU2NDg0OTgyMA==&mid=2247494165&idx=1&sn=4e0247006bef89701529d765e6ce32a4&chksm=fc4617e6cb319ef0991ff70c8a769b92f59cf92122f27785b848604493653fdcc206d6830a23&token=794467841&lang=zh_CN#rd)














