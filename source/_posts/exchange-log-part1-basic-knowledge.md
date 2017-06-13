---
title: Exchange 日志文件详解（上）：基础知识
tags:
  - Exchange
  - Log
  - 日志
id: 48
categories:
  - Exchange
  - 沟通协作
date: 2011-10-11 08:52:45
---

**1\. ESE - Extensible Storage Engine 可扩展存储引擎**

众所周知，Exchange 的邮件数据库文件是 .edb，实际上是ESE 数据库，它采用 B+ tree 平衡树结构来存储数据，如下图所示，树的根级别的节点个数与树的叶级别的节点个数总是相同的。因此，树是平衡的。详情这里不再展开，感兴趣的可以自行查找相关资料。

[![Exchange-2010-Btree](http://winotes.net/wp-content/uploads/Exchange-2010-Btree-300x144.jpg)](http://winotes.net/wp-content/uploads/Exchange-2010-Btree.jpg)

&nbsp;

Figure 1 - 平衡树结构示意图

目前，有四个正在使用的 ESE 版本：
• **ESE97** Exchange Server 5.5 中的数据库引擎。
• **ESENT** 用于 Active Directory 和许多其他 Microsoft Windows 组件的数据库引擎。与 ESE 的其他版本（它们使用 5 MB 日志文件和 4 KB 页大小）不同，ESENT 的 Active Directory 实现使用 10 MB 日志文件和 8 KB 页大小。
• **ESE98** Exchange 2000 Server 和 Exchange Server 2003 （32位）的数据库引擎，使用 5MB的日志文件和 4KB 页大小，
• **ESE for Exchange 2007/2010** Exchange Server 2007/2010 的数据库引擎，ESE98的改进版本。Exchange 2007 使用1MB的日志文件和8 KB 页大小。而在 Exchange 2010 中，使用 1MB 的日志文件和32 KB 页大小。

越小的日志文件和页大小可以提供更快的读写访问速度和更好的灾难容忍度。

<!--more-->

**2\. Transaction Log 事务日志文件**
除了 .edb 的数据库文件以外，ESE还有其它的组成部分，这里主要介绍和事务日志相关的文件类型。

**ESE基于事务的ACID特征**

ESE 是复杂的、基于事务的数据库引擎。事务是一系列被视为原子（不可划分）单位的操作。对于事务中的操作，要么完成并永久保存所有操作，要么不执行任何操作。例如，将邮件从"收件箱"移动到"已删除邮件"文件夹时需要执行相应的操作。邮件从一个文件夹中删除，然后被添加到另一个文件夹中，并更新文件夹属性。如果发生故障，您并不希望让邮件出现两个副本，或者根本没有副本，或者出现与实际文件夹内容不一致的文件夹属性值（例如，项目数）。

为了防止发生类似这样的问题，ESE 将操作捆绑到事务中。ESE 确保除非事务被提交到数据库，否则不会永久应用任何操作。事务被提交到数据库文件时，将永久应用所有操作。

万一发生崩溃，ESE 还会在启动时自动处理恢复，并回滚任何未提交的事务。如果在提交事务之前 ESE 出现故障，则整个事务都会回滚，就好像事务从未发生过。如果在提交事务之后 ESE 崩溃，则整个事务被永久性保存，并且客户端可以看见更改。

**ESE 的处理机制**

这里以 Exchange Server 2010 为例，简单讲讲 ESE 的处理机制，了解了这个可以帮助我们判断将来 Exchange 灾难恢复的时候所应该采取的措施。

因为事物的原子性，为了保证数据库的一致和完整，事务必须全部成功或全部失败，如果事务中途失败，则必须回滚到事务开始的状态。当用户要修改邮箱中的内容时，被修改的内容首先被提取出来放到内存中，实际的修改是发生在内存里的，当修改完成后，这些内容必须被尽快写回硬盘，这样才表示一个事务成功完成了，这也是事务的持久性要求。

当内存的修改完成后，这些结果就会立即写入日志(E0x.log)中，而不是直接读写.edb 文件，因为可能这个文件会变得非常巨大，直接的写入会影响系统性能。当成功写入日志后，该事务就成功完成了。接下来，ESE引擎会在后台慢慢将这些日志里的修改记录写回真正的 .edb 数据库里去（对用户来讲是透明的）。

因此，ESE至少需要三种类型的数据：
1\. 在内存中已经更改但还未写入日志的数据 (称之为 Dirty Page - 脏页)。
2\. 硬盘上的 .log 日志文件 (包括已经写入和暂未写入的 .edb 数据库的两种)。
3\. 硬盘上的 .edb 邮件数据库文件。

具体步骤来说：
1\. 需要修改的邮件数据在内存中被初始化，并且按页大小被分成多页。
2\. 已经修改完成的部分会被写入到日志文件(先写入 E0x.log，写满之后会将E0x.log重新命名为 E0x00000001.log，然后再创建个新的E0x.log，再写满之后会将其重命名为E0x00000002.log，以此类推)。
3\. 如果Exchange认为该页面内容已经不再需要的话，这些修改将会被写入到 .edb 的数据库中。
4\. 更新检查点文件(E0x.chk)以记录目前的 .edb 数据库已经将多少日志文件的内容写入到了数据库。
下面的这幅图大概展现了这个过程。实际上还有E00tmp.log 以及 .JRS 等文件也需要用到，这个下面再说。

[![Exchange_2010_Storage](http://winotes.net/wp-content/uploads/Exchange_2010_Storage-300x188.jpg)](http://winotes.net/wp-content/uploads/Exchange_2010_Storage.jpg)

&nbsp;

Figure 2 - Exchange 2010存储步骤示意图**
**
<div></div>
<div>ESE数据库相关的文件

*   .edb - ESE主数据库文件，又称之为 MAPI 数据库文件
*   Tmp.edb -临时数据库文件，在邮件数据库卸载或者Exchange Information Store 服务停止的时候会被自动删除。
*   .stm - .stm 文件也是 ESE 数据库，它被称为流式数据库文件，用来存储本机 Internet 内容。这里再啰嗦两句: 因为在Exchange 5.5以及更早的版本里，只有单个的EDB文件，那时微软主推的是内部邮件系统，因此其主要协议为MAPI，这是微软的私有邮件协议。Exchange 5.5中， Internet 邮件连接器在收到 MIME 格式的邮件时，会转换成 MAPI 或者 MDBEF 的平衡树结构再存储成EDB；而当用户通过类似 POP3/IMAP协议来收取邮件时，又要将其从 MAPI 格式转换成原生 Internet 格式 (MIME) 之后才能传回给用户，这显然是设计所限带来了性能的损失。在Exchange 2000里，微软加大了对Internet邮件的支持，这就是STM文件的来源。MAPI格式是RPC和二进制标准的，而STM是纯文本加上一些MIME编码格式，这样的设计使得非MAPI客户端可以直接使用 .stm 文件来处理邮件而无需转换。

    在MAPI客户端环境中.edb 文件中存储了所有MAPI客户端访问所需要的数据。
而在非MAPI客户端情形下， .stm 文件存储了实际的数据，但是其对应的指针和头信息是存在 .edb文件中的。所以两个文件之间又引用和关联，缺一不可。

    这个 .stm 和 .edb 文件共存的情况一直被沿用到了 Exchange 2003.
*   Edb.log - 存在于Exchange Server 4.0, 5.0, and 5.5中。存放当前的日志文件，大小为5MB。
*   Edbtmp.log - 存在于Exchange Server 4.0, 5.0, and 5.5中。当 Edb.log到达5MB以后，Edb.log文件将被重命名为Edb00001.log，在这期间如果有新数据需要写入怎么办，于是就有了这个Edbtmp.log临时日志文件用来接收新的需要写入日志的内容，在 Edb.log重命名Edb00001.log完成后，Edbtmp.log也将被改名为Edb.log。
*   Edb00001.log - 存在于Exchange Server 4.0, 5.0, and 5.5中。日志序列文件。
*   E0x.chk - 存在于所有Exchange 版本中。文件名中的x 按邮件存储组的创建编号来命名，从0起。检查点文件，用来标记哪些日志已经被写入数据库，哪些还没有。
*   E0x.log -存在于Exchange 2000之后的版本中。用来存放目前最新的邮件更改信息,在到达日志文件目标大小后，将会被重命名为下一个带有lGeneration 数字序列的日志文件，比如从 E0000000001.log 到 E000000000E.log，这里的数字是16进制。 在重命名完成后，会创建新的 E0x.log 文件。
*   E0Xtmp.log - 存在于Exchange 2000之后的版本中。 每当 E0x.log 文件被写满之后，这个临时的 E0xtmp.log 文件将被用来接收新的内容，最后将会被改名为新的E0x.log 。
*   E0xxxxxxxxx.log -存在于Exchange 版本中，序列日志文件，16进制递增。
*   Res1.log 和 Res2.log -存在于 Exchange 2000/2003 中。日志保留文件。主要的作用是防止日志所在硬盘被写满的时候，还能保证数据库文件能被干净的卸载下来。什么意思？当邮件数据库一被挂载的时候，系统会自动创建这2个文件，每个5MB，加起来10MB。当磁盘即将写满的时候(比如剩余空间小于5MB - Excahnge 2003)，E0x.log 会到不了5MB就已写满，此时已经写入日志的数据也没法提交应用到EDB数据库中，可能会导致文件丢失，而有了这两个文件以后，在磁盘即将写满的时候，系统会自动将 Res2.log作为下一个新的日志文件，以保证以前的事务数据能够正常写入，然后再自动卸载数据库以保证数据的一致性。有必要的话，Res1.log 也将被用上。就是说，在挂载数据库的时候，系统已经为你预留了10MB的空间来预防数据的丢失。
*   .jrs - 类似于 Exchange 2003, 在Exchange 2007 和 Exchange 2010 中的日志预留文件，每个大小1MB。Excahnge 2007 只有2个，而 Excahnge 2010 有10个。
Excahnge 2007 ：E0xres00001.jrs 和 E0xres00002.jrs (2个，共计2MB)
Excahnge 20010 ：E0xres00001.jrs ... E0xres00009.jrs (10个，共计10MB)
[![Exchange-2010-JRS-Files](http://winotes.net/wp-content/uploads/Exchange-2010-JRS-Files-300x218.jpg)](http://winotes.net/wp-content/uploads/Exchange-2010-JRS-Files.jpg)

&nbsp;

Figure 3 - Exchange 2010 的日志预留文件

<span style="color: blue;">未完待续：下篇是Exchange 日志文件详解（下）：灾难恢复</span>

</div>