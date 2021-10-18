# System.map文件的作用

2019-05-25阅读 6980

**什么是符号(Symbols)?**

> 在编程中，一个符号(symbol)是一个程序的创建块：它是一个变量名或一个函数名。 正如你自己编制的程序一样，内核具有各种符号也是不应该感到惊奇的。当然，区别在 于内核是一非常复杂的代码块，并且含有许多、许多的全局符号。

## 内核符号表(Kernel Symbol Table)是什么东西?

> 内核并不使用符号名。它是通过变量或函数的地址(指针)来使用变量或函数的，而 不是使用size\_t BytesRead，内核更喜欢使用(例如)c0343f20来引用 这个变量。 而另一方面，人们并不喜欢象c0343f20这样的名字。我们跟喜欢使用象 size\_t BytesRead这样的表示。通常，这并不会带来什么问题。内核主要 是用C语言写成的，所以在我们编程时编译器/连接程序允许我们使用符号名，并且使 内核在运行时使用地址表示。这样大家都满意了。 然而，存在一种情况，此时我们需要知道一个符号的地址（或者一个地址对应的 符号）。这是通过符号表来做到的，与gdb能够从一个地址给出函数名（或者给出一个 函数名的地址）的情况很相似。符号表是所有符号及其对应地址的一个列表。这里是 一个符号表例子： c03441a0 B dmi\_broken c03441a4 B is\_sony\_vaio\_laptop c03441c0 b dmi\_ident c0344200 b pci\_bios\_present c0344204 b pirq\_table c0344208 b pirq\_router c034420c b pirq\_router\_dev c0344220 b ascii\_buffer c0344224 b ascii\_buf\_bytes 你可以看出名称为dmi_broken的变量位于内核地址c03441a0处。

## 什么是System.map文件?

> 有两个文件是用作符号表的:

1.  /proc/ksyms
2.  System.map

这里，你现在可以知道System.map文件是干什么用的了。 每当你编译一个新内核时，各种符号名的地址定会变化。 /proc/ksyms 是一个 "proc文件" 并且是在内核启动时创建的。实际上 它不是一个真实的文件；它只是内核数据的简单表示形式，呈现出象一个磁盘文件似 的。如果你不相信我，那么就试试找出/proc/ksyms的文件大小来。因此， 对于当前运行的内核来说，它总是正确的.. 然而，System.map却是文件系统上的一个真实文件。当你编译一个新内核时，你原 来的System.map中的符号信息就不正确了。随着每次内核的编译，就会产生一个新的 System.map文件，并且需要用该文件取代原来的文件。

## 什么是一个Oops?

> 在自己编制的程序中最常见的出错情况是什么?是段出错(segfault)，信号11。 Linux内核中最常见的bug是什么?也是段出错。除此，正如你想象的那样，段出 错的问题是非常复杂的，而且也是非常严重的。当内核引用了一个无效指针时，并不 称其为段出错 -- 而被称为"oops"。一个oops表明内核存在一个bug，应该总是提出 报告并修正该bug。 请注意，一个oops与一个段出错并不是一回事。你的程序并不能从段出错中恢复 过来，当出现一个oops时，并不意味着内核肯定处于不稳定的状态。Linux内核是非常 健壮的；一个oops可能仅杀死了当前进程，并使余下的内核处于一个良好的、稳定的 状态。 一个oops并非是内核死循环(panic)。在内核调用了panic()函数后，内核就不能 继续运行了；此时系统就处于停顿状态并且必须重启。如果系统中关键部分遭到破坏 那么一个oops也可能会导致内核进入死循环(panic)。例如，设备驱动程序中 出现的oops就几乎不会导致系统进行死循环。 当出现一个oops时，系统就会显示出用于调试问题的相关信息，比如所有CPU寄存器 中的内容以及页描述符表的位置等，尤其会象下面那样打印出EIP(指令指针)的内容： EIP: 0010:\[&lt;00000000&gt;\] Call Trace: \[\]

## 一个Oops与System.map文件有什么关系呢?

> 我想你也会认为EIP和Call Trace所给出的信息并不多，但是重要 的是，对于内核开发人员来说这些信息也是不够的。由于一个符号并没有固定的地址， c010b860可以指向任何地方。 为了帮助我们使用oops含糊的输出，Linux使用了一个称为klogd(内核日志后台程序)的 后台程序，klogd会截取内核oops并且使用syslogd将其记录下来，并将某些象c010b860 的信息转换成我们可以识别和使用的信息。换句话说，klogd是一个内核消息记录器(logger)， 它可以进行名字-地址之间的解析。一旦klogd开始转换内核消息，它就使用手头的记录器， 将整个系统的消息记录下来，通常是使用syslogd记录器。 为了进行名字-地址解析，klogd就要用到System.map文件。我想你现在 知道一个oops与System.map的关系了。 深入说明： 其实klogd会执行两类地址解析活动。

- 静态转换，将使用System.map文件。
- 动态转换，该方式用于可加载模块，不使用System.map，因此与本讨论没有关系，但我仍然对其加以简单说明。

**Klogd动态转换** 假设你加载了一个产生oops的内核模块。于是就会产生一个oops消息，klogd就会截获它，并发现该oops发生在d00cf810处。由于该地址属于动态加载模块，因此在System.map文件中没有对应条目。klogd将会在其中寻找并会毫无所获，于是断定是一个可加载模块产生了oops。此时klogd就会向内核查询该可加载模块输出的符号。即使该模块的编制者没有输出其符号，klogd也起码会知道是哪个模块产生了oops，这总比对一个oops一无所知要好。 还有其它的软件会使用System.map，我将在后面作一说明。

## System.map应该位于什么地方?

> System.map应该位于使用它的软件能够寻找到的地方，也就是说，klogd会在什么地方寻找它。在系统启动时，如果没有以一个参数的形式为klogd给出System.map的位置，则klogd将会在三个地方搜寻System.map。依次为：

1.  /boot/System.map
2.  /System.map
3.  /usr/src/linux/System.map

System.map 同样也含有版本信息，并且klogd能够智能化地搜索正确的map文件。例如，假设你正在运行内核2.4.18并且相应的map文件位于/boot/System.map。现在你在目录/usr/src/linux中编译一个新内核2.5.1。在编译期间，文件 /usr/src/linux/System.map就会被创建。当你启动该新内核时，klogd将首先查询 /boot/System.map，确认它不是启动内核正确的map文件，就会查询 /usr/src/linux/System.map, 确定该文件是启动内核正确的map文件并开始读取其中的符号信息。 几个注意点:

- 在2.5.x系列内核的某个版本，Linux内核会开始untar成linux-version，而非只是linux (请举手表决 -- 有多少人一直等待着这样做?)。我不知道klogd是否已经修改为在/usr/src/linux-version/System.map中搜索。TODO：查看klogd源代码。
- 在线手册上对此也没有完整描述，请看： strace -f /sbin/klogd | grep 'System.map' 31208 open("/boot/System.map-2.4.18", O\_RDONLY|O\_LARGEFILE) = 2 显然，不仅klogd在三个搜索目录中寻找正确版本的map文件，klogd也同样知道寻找名字为 "System.map" 后加"-内核版本"，象 System.map-2.4.18. 这是klogd未公开的特性。

有一些驱动程序将使用System.map来解析符号(因为它们与内核头连接而非glibc库等)，如果没有System.map文件，它们将不能正确地工作。这与一个模块由于内核版本不匹配而没有得到加载是两码事。模块加载是与内核版本有关，而与即使是同一版本内核其符号表也会变化的编译后内核无关。

## 还有谁使用了System.map?

> 不要认为System.map文件仅对内核oops有用。尽管内核本身实际上不使用System.map，其它程序，象klogd，lsof， satan# strace lsof 2>&1 1> /dev/null | grep System readlink("/proc/22711/fd/4", "/boot/System.map-2.4.18", 4095) = 23 ps, satan# strace ps 2>&1 1> /dev/null | grep System open("/boot/System.map-2.4.18", O\_RDONLY|O\_NONBLOCK|O_NOCTTY) = 6 以及其它许多软件，象dosemu，需要有一个正确的System.map文件。

## 如果我没有一个好的System.map，会发生什么问题?

> 假设你在同一台机器上有多个内核。则每个内核都需要一个独立的 System.map文件！如果所启动的内核没有对应的System.map文件，那么你将定期地看到这样一条信息： System.map does not match actual kernel (System.map与实际内核不匹配) 不是一个致命错误，但是每当你执行ps ax时都会恼人地出现。有些软件，比如dosemu，可能不会正常工作。最后，当出现一个内核oops时，klogd或ksymoops的输出可能会不可靠。

## 我如何对上述情况进行补救?

> 方法是将你所有的System.map文件放在目录/boot下，并使用内核版本号重新对它们进行命名。假设你有以下多个内核：

- /boot/vmlinuz-2.2.14
- /boot/vmlinuz-2.2.13

那么，只需对应各内核版本对map文件进行改名，并放在/boot下，如： /boot/System.map-2.2.14 /boot/System.map-2.2.13 如果你有同一个内核的两个拷贝怎么办？例如：

- /boot/vmlinuz-2.2.14
- /boot/vmlinuz-2.2.14.nosound

最佳解决方案将是所有软件能够查找下列文件： /boot/System.map-2.2.14 /boot/System.map-2.2.14.nosound 但是说实在的，我并不知道这是否是最佳情况。我曾经见到搜寻"System.map-kernelversion"，但是对于搜索"System.map-kernelversion.othertext"的情况呢? 我不太清楚。此时我所能做的就是利用这样一个事实：/usr/src/linux是标准map文件的搜索路径，所以你的map文件将放在：

- /boot/System.map-2.2.14
- /usr/src/linux/System.map (对于nosound版本)

你也可以使用符号连接： System.map-2.2.14 System.map-2.2.14.sound System.map -> System.map-2.2.14.sound

举报