课后作业 (测量)

本作业向您介绍了一个新工具 vmstat，如何使用它来了解内存，CPU 和 I/O 的使用情况。 在继续进行下面的练习和问题之前，请阅读 README 文件并查看 mem.c
中的代码。


问题:

<br/>
<br/>

1.首先，打开同一台计算机的两个单独的终端，以便您可以轻松地在两个窗口运行不同的程序。 
现在，在一个窗口中运行 `vmstat 1`，它每秒显示一次计算机资源使用情况信息。 
阅读 man 手册，相关的 README 文件以及您需要的其他信息，以便您了解程序输出。 在下面的其余练习中，请使此窗口运行 vmstat。 
现在我们将运行程序 mem.c，但使用少量内存。 这可以通过输入 ./mem 1 来完成（仅使用 1 MB 的内存）。 运行 mem 程序时，CPU 使用率统计信息变化情况是怎样的？ 
user time 列中的数字有意义吗？ 一次运行多个 mem 实例时，变化情况如何？

<pre>
man vmstat

Procs
   r: 可运行进程(正在运行或等待运行时)的数量。
   b: 处于不可中断休眠状态的进程数。

Memory
   它受 --unit 参数的影响。
   swpd: 所使用的虚拟内存大小。(单位为 kb)
   free: 空闲内存的大小。
   buff: 用作缓冲区的内存大小
   cache: 用作缓存的内存大小。
   inact: 非活动内存的大小。(-a 选项)
   active: 活动内存的大小。(-a 选项)

Swap
   它受 --unit 参数的影响。
   si: 从磁盘交换的内存大小(/s)
   so: 交换到磁盘的内存大小(/s)

IO
   bi: 从块设备接收的块 (块/s).
   bo: 发送到块设备的块 (blocks/s).

System
   in: 每秒中断的数，包括时钟中断。
   cs: 每秒上下文切换的次数。

CPU
   一下都是是占总CPU时间的百分比。
   us: 运行非内核代码所花费的时间  (user time, including nice time) 。
   sy: 运行内核代码所花费的时间.  (系统时间)
   id: 空闲的时间。在 Linux 2.5.4 1之前，这包括 IO 等待时间。
   wa: 等待 IO 所花费的时间。在 Linux 2.5.41 之前，包括在 idle 中。
   st: 从虚拟机中窃取的时间。在Linux 2.6.11之前，未知。
</pre>

```shell script
make && ./mem 1 
```

<br/>
<br/>

2.现在让我们开始查看运行 mem 程序时的一些内存统计信息。 我们将重点关注两列：swpd（使用的虚拟内存量）和 free（空闲的内存量）。 
运行 ./mem 1024（分配 1024 MB）并观察这些值如何变化。 然后终止正在运行的程序（通过输入 control-c），然后再次观察值的变化。 
您如何看待这些值？ 特别是程序退出时，free 列会如何变化？ 内存退出时，可用内存量是否会增加预期的数量？

<pre>
 0  0      0 3532508 193440 1949732    0    0     0    38  494 1379  1  0 99  0  0
 2  0      0 2482668 193472 1949716    0    0     0   280 1615 3650 13  3 85  0  0
 1  0      0 2482684 193480 1949744    0    0     0    10  839 1321 14  1 85  0  0
 0  0      0 3532248 193504 1949656    0    0     0    96  878 2413  6  1 93  0  0
</pre>

运行后空闲内存从 3532508 变成 2482668， （3532508 - 2482668）/1024 = 1025M,

结束运行后空闲内存从 2482684 变成 3532248



<br/>
<br/>

3.接下来，我们将查看交换列(si 和 so)，它们表示与磁盘之间发生了多少交换。
当然，要激活它们，您需要使用大量内存运行 mem。
首先，检查您的 Linux 系统上有多少空闲内存(例如，输入`cat /proc/meminfo`; 
输入 man proc 以获得关于 /proc 文件系统的详细信息以及您可以在其中找到的信息类型)。
`/proc/meminfo` 中的第一个条目是系统中的内存总量。
假设有 8 GB 的内存;如果是这样，首先运行 mem 4000(大约 4 GB)并观察 in/out 列。
它们会给出非零值吗?然后，尝试 5000、6000，等等。
与第一次循环相比，当程序进入第二次循环(及以后)时，这些值会发生什么变化?
在第二次、第三次和随后的循环中，有多少数据(总计)被交换进和交换出?(这些数字有意义吗?)

<pre>
❯ cat /proc/meminfo
MemTotal:        8023128 kB
</pre>

结果：
<pre>
                                       si    so   bi    bo
 0  0      0 3012568 174148 1987024    0     0  3398   161  690 1749 25  6 69  0  0
 0  0      0 3012928 174148 1987064    0     0     0  2628 1699 3134  2  1 97  0  0
 0  0      0 3008628 174416 1987300    0     0   348   556 1630 5423  6  2 92  0  0
 2  0      0 2170632 174832 2029960    0     0 42924     0 1359 2665  7  3 90  0  0
 2  0   3584 124180 160052 1483396     0  3416  5716  8652 2793 6551 15 13 72  0  0
 1  0  21504 148832  74756 852412    376 17724 13760 18036 2219 4463 11  7 82  0  0
 1  0  21504 143996  76564 854296    168     0  2764   968 1244 2259 13  1 86  0  0
 1  0  21504 143832  76700 854576     4      0   328     0  838 1231 13  0 86  0  0
 1  0  21504 150196  76704 855612     0      0  1304     0 1035 1455 13  1 86  0  0
 1  0  21504 150220  76704 857568     0      0  1628     0  894 1412 13  1 86  0  0
 1  0  21504 150220  76704 857568     0      0     0     0  753 1124 13  0 87  0  0
 0  0  21504 4245960  81500 860252    80     0  6712   172 1485 3743 10  3 87  0  0
^C
</pre>
meme 运行后，换入 376 KB，换出 3416 KB，第二次循环，换入 376 KB， 换出 17724 KB

程序运行后，换出 3579KB， 换入 16KB


<br/>
<br/>

4.进行与上述相同的实验，但是现在查看其他统计信息（例如 CPU 利用率和块 I/O 统计信息）。当 mem 运行时，它们如何变化？

<pre>
                                               bi    bo            us sy id wa st
 0  0 506112 4934352  40928 515860    4    0   236   300  990 2748  3  1 96  0  0
 0  0 506112 4934132  40928 515756    0    0     0     0  720 1992  2  1 98  0  0
 0  0 506112 4933880  40928 515756    0    0     0   172  825 2365  2  1 97  0  0
 0  0 506112 4933880  40928 515756    0    0     0     0  792 2351  2  1 97  0  0
 1  0 506112 3651192  40932 516628    0    0    20     4  661 1500  4  3 93  0  0
 2  0 506112 829296  40932 516628     0    0     0     0 1216 2797  6  7 87  0  0
 1  0 506112 828540  40940 516628     0    0     0   128 1186 2383 13  1 87  0  0
 1  0 506112 828572  40940 516628     0    0     0  1236  705 1060 13  0 87  0  0
 1  0 506112 828540  40940 516628     0    0     0     0  678 1068 13  0 87  0  0
 1  0 506112 828288  40940 516628     0    0     0     0  591  988 13  0 87  0  0
 0  0 506112 4929424  40940 516708   28    0    72   116 1553 4308  8  3 89  0  0
</pre>

user time 增加， system time 先增后减， 空阿咸时间减少

<br/>
<br/>

5.现在让我们查看性能。为 mem 选择一个适合内存大小并运行(如果系统上的内存是 8 GB，那么就选 4000)。循环 0 需要多长时间(以及随后的循环 1、2 等等)?
现在选择一个超出内存大小的尺寸(假设内存为 8 GB，那么选 12000)。这里的循环需要多长时间?如何比较带宽数?不断地交换内存和合适的内存大小的性能有什么不同?
你能不能画个图，x 轴是 mem 使用的内存的大小，y 轴是访问内存的带宽

<pre>
❯ ./mem 4000
allocating 4194304000 bytes (4000.00 MB)
  number of integers in array: 1048576000
loop 0 in 1448.84 ms (bandwidth: 2760.83 MB/s)
loop 1 in 561.08 ms (bandwidth: 7129.06 MB/s)
loop 2 in 560.32 ms (bandwidth: 7138.78 MB/s)
loop 3 in 561.33 ms (bandwidth: 7125.97 MB/s)
loop 4 in 559.16 ms (bandwidth: 7153.59 MB/s)
loop 5 in 559.24 ms (bandwidth: 7152.63 MB/s)
loop 6 in 558.28 ms (bandwidth: 7164.92 MB/s)
loop 7 in 559.73 ms (bandwidth: 7146.35 MB/s)
</pre>

```shell script
./mem 12000
```
第一次循环 bandwidth： 500 MB/s, 然后系统卡死，直接关机了

<br/>
<br/>

6.交换空间不是无限的。您可以 swapon 工具带上 -s 参数来查看有多少交换空间可用。如果您尝试使用越来越大的值运行 mem，超出了交换中可用的值，会发生什么?在什么情况下内存分配失败

```shell script
swapon -s
```

```shell script
free -h
```

```shell script
./mem 12000
```
循环一次后直接卡死，具体发生了什么也没法查看了


<br/>
<br/>

7.最后，如果你想进阶使用，使用 swapon 和 swapoff 配置您的系统，来使用不同的交换设备。
有关详细信息，请阅读 man 手册。如果您可以访问不同的硬件，请查看在交换到到经典硬盘驱动器、基于闪存的 SSD 甚至 RAID 阵列时，切换性能的变化。
通过更新的设备可以提高多少交换性能?您能在多大程度上接近内存性能
