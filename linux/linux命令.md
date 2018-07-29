# Linux

## 一、Linux文件系统

- Linux将文件存储在单个目录结构中，这个目录称为虚拟目录。虚拟目录将安装在PC上的所有存储设备的文件路径纳入单个目录结构中。
- 在Linux安装的第一块硬盘称为根驱动器。
- Linux会在根驱动器上创建一些特别的目录，我们称之为挂载点(mount point)。磁盘设备会挂载在某个指定的虚拟目录上。

### 1. Linux常见目录

目录|用途
---|---
/|虚拟目录的根目录。通常不会在这里存储文件。
/bin|二进制目录，存放许多用户级的GNU工具
/boot|启动目录，存放启动文件
/dev|设备目录，Linux在这里创建设备节点
/etc|系统配置文件目录
/home|主目录,Linux在这里创建用户目录
/lib|库目录，存放系统和应用程序文件
/meida|媒体目录，可移动媒体设备常用挂载点
/mnt|挂载目录，另一个可移动媒体设备的常用挂载点
/opt|可选目录，常用于存放第三方软件包和数据文件
/proc|进程目录，存放现有硬件及当前进程的相关信息
/root|root用户的主目录
/sbin|系统二进制目录，存放许多GNU管理员级工具
/run|运行目录，存放系统运作时的运行时数据
/srv|系统目录，存放本地服务的相关文件
/sys|系统目录，存放系统硬件信息的相关文件
/tmp|临时目录，可以在改目录中创建和删除临时工作文件
/usr|用户二进制目录，大量用户级的GNU工具和数据文件，软件安装目录
/var|可变目录，用以存放经常变化的文件，比如日志文件

### 2. 文件和目录列表

命令 | 作用
---|---
ls -a | 查看文件，隐藏
ls -l | 格式化输出
which ll|
type ll|

- lrwxrwxrwx.   1 root root         7 Jul  4  2016 bin -> usr/bin
- dr-xr-xr-x.   4 root root      4096 Nov  7  2017 boot
- drwxrwxrwx    6 root root        60 Nov 10  2017 data
- drwxr-xr-x   19 root root      2940 Feb 27 15:19 dev
- drwxr-xr-x.  80 root root      8192 Jul 21 13:34 etc
- drwxr-xr-x.   5 root root        45 Nov  7  2017 home
- lrwxrwxrwx.   1 root root         7 Jul  4  2016 lib -> usr/lib
- lrwxrwxrwx.   1 root root         9 Jul  4  2016 lib64 -> usr/lib64
- drwxr-xr-x.   2 root root         6 Aug 12  2015 media
- drwxr-xr-x.   2 root root         6 Aug 12  2015 mnt
- drwxr-xr-x.   2 root root         6 Aug 12  2015 opt
- dr-xr-xr-x  138 root root         0 Apr  8  2015 proc
- dr-xr-x---.   4 root root      4096 Jul 26 10:32 root
- drwxr-xr-x   21 root root       660 Jun 21 06:10 run
- lrwxrwxrwx.   1 root root         8 Jul  4  2016 sbin -> usr/sbin
- drwxr-xr-x.   2 root root         6 Aug 12  2015 srv
- -rw-------.   1 root root 536870912 Jul  4  2016 swapfile
- dr-xr-xr-x   13 root root         0 Feb 27 15:19 sys
- drwxrwxrwt.   9 root root      4096 Jul 28 03:31 tmp
- drwxr-xr-x.  14 root root      4096 Sep  6  2017 usr
- drwxr-xr-x.  19 root root      4096 Feb 27 15:19 var

1. 文件类型 目录(d)、文件(-)、字符型文件(c)、块设备(b)、链接文件(l)
2. 文件的权限
3. 文件的硬链接总数
4. 文件属主的用户名
5. 文件属组的组名
6. 文件的大小(以字节为单位)
7. 文件上次修改的时间
8. 文件名或目录名

### 2. 文件处理

命令|功能
---|---
touch|创建文件
mkdir|创建目录 -p 可以递归创建
cp source destination|-r 递归复制
mv source target|重命名
mv source ... directory|移动文件到目录下,追加名字可以移动到目录下并且重命名
rm|-r 递归删除 -i 提示删除确认

### 3. 链接文件

命令|功能
---|---
ln source_file target_file|-s 符号链接
ln source_file ... target_dir|-s 符号链接

- **符号链接**: 是一个实实在在的文件，它指向存放在虚拟目录结构中某个地方的另一个文件。这两个通过符号链接在一起的文件，彼此的内容并不相同。
- **硬链接**: 会创建独立的虚拟文件，其中包含原始文件的信息及位置。

### 4. 查看文件

命令|功能
---|---
cat|-n 显示行号 -b有文本加行号
more|启动时加载整个文件
less|不会加载整个文件 -N显示每行的行号
head|-n 显示头部几行
tail|-n 显示尾部几行 -f追踪

### 5.监测程序

#### 5.1 ps

命令|功能
---|---
ps|-e显示所有进程 -f显示完整格式的输出 -l显示长列表

- **UID**: 启动这些进程的用户
- **PID**: 进程ID
- **PPID**: 父进程的进程号（如果该进程为另外一个进程创建的）
- **STIME**: 进程启动时的系统时间
- **TTY**: 进程启动时的终端设备
- **TIME**: 运行进程需要的累积CPU时间
- **CMD**: 启动的程序名称

#### 5.2 top

- top - 20:09:41 up 52 days, 23:09,  1 user,  load average: 0.09, 0.09, 0.06
- Tasks: 238 total,   1 running, 237 sleeping,   0 stopped,   0 zombie
- %Cpu(s):  0.3 us,  0.2 sy,  0.0 ni, 99.6 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
- KiB Mem : 32780936 total,   785152 free, 13577796 used, 18417988 buff/cache
- KiB Swap:   524284 total,   524284 free,        0 used. 18546912 avail Mem

PID|USER|PR|NI|VIRT|RES|SHR|S|%CPU|%MEM|TIME+|COMMAND
---|---|---|---|---|---|---|---|---|---|---|---|---
23987|databas+|20|0|10.291g|1.569g|17448|S|25.2|5.0|2880:56|java
19693|databas+|20|0|3229712|1.369g|19644|S| 1.0|4.4|1072:33|java

- 第一行显示了当前时间，系统的运营时间，登录的用户数以及系统的平局负载
  - 系统负载，即任务队列的平均长度，3个数值分别统计最近1，5，15分钟的系统平均负载
  - 系统平均负载：单核CPU情况下，0.00 表示没有任何负荷，1.00表示刚好满负荷，超过1侧表示超负荷，理想值是0.7
  - 多核CPU负载：CPU核数 * 理想值0.7 = 理想负荷，例如：4核CPU负载不超过2.8何表示没有出现高负载
- 第2行：Tasks 进程相关信息
  - 第1段：进程总数，例如：Tasks: 231 total, 表示总共运行231个进程
  - 第2段：正在运行的进程数，例如：1 running,
  - 第3段：睡眠的进程数，例如：230 sleeping,
  - 第4段：停止的进程数，例如：0 stopped,
  - 第5段：僵尸进程数，例如：0 zombie
- 第3行：Cpus CPU相关信息，如果是多核CPU，按数字1可显示各核CPU信息，此时1行将转为Cpu核数行，数字1可以来回切换
  - 第1段：us 用户空间占用CPU百分比，例如：Cpu(s): 12.7%us,
  - 第2段：sy 内核空间占用CPU百分比，例如：8.4%sy,
  - 第3段：ni 用户进程空间内改变过优先级的进程占用CPU百分比，例如：0.0%ni,
  - 第4段：id 空闲CPU百分比，例如：77.1%id,
  - 第5段：wa 等待输入输出的CPU时间百分比，例如：0.0%wa,
  - 第6段：hi CPU服务于硬件中断所耗费的时间总额，例如：0.0%hi,
  - 第7段：si CPU服务软中断所耗费的时间总额，例如：1.8%si,
  - 第8段：st Steal time 虚拟机被hypervisor偷去的CPU时间（如果当前处于一个hypervisor下的vm，实际上hypervisor也要消耗一部分CPU处理时间的）
- 第4行：Mem 内存相关信息（Mem: 12196436k total, 12056552k used, 139884k free, 64564k buffers）
  - 第1段：物理内存总量，例如：Mem: 12196436k total,
  - 第2段：使用的物理内存总量，例如：12056552k used,
  - 第3段：空闲内存总量，例如：Mem: 139884k free,
  - 第4段：用作内核缓存的内存量，例如：64564k buffers
- 第5行：Swap 交换分区相关信息（Swap: 2097144k total, 151016k used, 1946128k free, 3120236k cached）
  - 第1段：交换区总量，例如：Swap: 2097144k total,
  - 第2段：使用的交换区总量，例如：151016k used,
  - 第3段：空闲交换区总量，例如：1946128k free,
  - 第4段：缓冲的交换区总量，3120236k cached
- 进程信息
  - A: PID = (Process Id) 进程Id；
  - E: USER = (User Name) 进程所有者的用户名；
  - H: PR = (Priority) 优先级
  - I: NI = (Nice value) nice值。负值表示高优先级，正值表示低优先级
  - O: VIRT = (Virtual Image (kb)) 进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES
  - Q: RES = (Resident size (kb)) 进程使用的、未被换出的物理内存大小，单位kb。RES=CODE+DATA
  - T: SHR = (Shared Mem size (kb)) 共享内存大小，单位kb
  - W: S = (Process Status) 进程状态。D=不可中断的睡眠状态,R=运行,S=睡眠,T=跟踪/停止,Z=僵尸进程
  - K: %CPU = (CPU usage) 上次更新到现在的CPU时间占用百分比
  - N: %MEM = (Memory usage (RES)) 进程使用的物理内存百分比
  - M: TIME+ = (CPU Time, hundredths) 进程使用的CPU时间总计，单位1/100秒

PID|USER|PR|NI|VIRT|RES|SHR|S|%CPU|%MEM|TIME+|COMMAND
---|---|---|---|---|---|---|---|---|---|---|---|---
23987|databas+|20|0|10.291g|1.569g|17448|S|25.2|5.0|2880:56|java
19693|databas+|20|0|3229712|1.369g|19644|S| 1.0|4.4|1072:33|java

- 基础操作
  - 1：显示CPU详细信息，每核显示一行
  - d / s ：修改刷新频率，单位为秒
  - h：可显示帮助界面
  - n：指定进程列表显示行数，默认为满屏行数
  - q：退出top
- 面板隐藏显示
  - l：隐藏/显示第1行负载信息；
  - t：隐藏/显示第2~3行CPU信息；
  - m：隐藏/显示第4~5行内存信息；
- 进程列表排序
  - M：根据驻留内存大小进行排序；
  - P：根据CPU使用百分比大小进行排序；
  - T：根据时间/累计时间进行排序；

### 6.监测磁盘空间

#### 6.1 df

Filesystem|1K-blocks|Used|Available|Use%|Mounted on
---|---|---|---|---|---
/dev/vda1|20960256|6338596|14621660|31%|/
devtmpfs|16381240|0|16381240|0%|/dev
tmpfs|16390468|0|16390468|0%|/dev/shm
/dev/vdb|524032000|73169820|450862180|14%|/data

- 设备的设备文件位置
- 能容纳多少个1024字节大小的块
- 已用了多少个1024字节大小的块
- 还有多少个1024字节大小的块可用
- 已用空间所占比例
- 设备挂载了哪个挂载点上

#### 6.1 du

du可以显示某个特定目录的磁盘使用情况

命令|功能
---|---
du -hm --max-depth=2 \| sort -n \| head -5|查找大文件目录

### 7.处理数据文件

命令|功能
---|---
sort|-n 按数值排序 -r倒序
grep|-n 输出行号
gzip|-<压缩效率> 压缩效率是一个介于1~9的数值，预设值为6 -d解压缩
tar| -zcvf归档并压缩文件 -zxvf解压并提取文件 -cvf归档 -xvf提取

## 二、Linux网络系统

### 1. netstat

命令|功能
---|---
netstat -a|Shows all sockets , both listening and non-listening, all protocols like tcp, udp etc
netstat -at|Shows only tcp connections (-au shows only udp connections)
netstat -ant|Shows all TCP connections with no dns resolution (show ip numbers instead).
netstat -al|Shows only listening sockets
sudo netstat -aep|Also show pid and to which program each socket belongs, e add extra info like the user. Run as root to see all pids.
netstat -s > file2.txt|Shows network statistics
netstat -r|Shows kernel routing information. This is the same output as route -e
netstat -i|Displays a table of all network interfaces.Add -e to get output similar to ifconfig
netstat -ct|displays tcp connections continuously
netstat -g|Display multicast group membership information for IPv4 and IPv6
netstat -lntu|Display all services listening for tcp and udp, all free open ports on the local machine
netstat -atnp | grep ESTA|Displays all current Established TCP connections

### 2. TCP

>> https://en.wikipedia.org/wiki/Transmission_Control_Protocol

>> https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol

>> https://en.wikipedia.org/wiki/HTTP_persistent_connection

>> https://juejin.im/post/5ad4094e6fb9a028d7011069

- **LISTEN**: (server) represents waiting for a connection request from any remote TCP and port.
- **SYN-SENT**: (client) represents waiting for a matching connection request after having sent a connection request.
- **SYN-RECEIVED**: (server) represents waiting for a confirming connection request acknowledgment after having both received and sent a connection request.
- **ESTABLISHED**: (both server and client) represents an open connection, data received can be delivered to the user. The normal state for the data transfer phase of the connection.
- **FIN-WAIT-1**: (both server and client) represents waiting for a connection termination request from the remote TCP, or an acknowledgment of the connection termination request previously sent.
- **FIN-WAIT-2**: (both server and client) represents waiting for a connection termination request from the remote TCP.
- **CLOSE-WAIT**: (both server and client) represents waiting for a connection termination request from the local user.
- **CLOSING**: (both server and client) represents waiting for a connection termination request acknowledgment from the remote TCP.
- **LAST-ACK**: (both server and client) represents waiting for an acknowledgment of the connection termination request previously sent to the remote TCP (which includes an acknowledgment of its connection termination request).
- **TIME-WAIT**: (either server or client) represents waiting for enough time to pass to be sure the remote TCP received the acknowledgment of its connection termination request. [According to RFC 793 a connection can stay in TIME-WAIT for a maximum of four minutes known as two MSL (maximum segment lifetime).
- **CLOSED**: (both server and client) represents no connection state at all.