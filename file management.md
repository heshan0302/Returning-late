## 目录

1. 目录结构
2. 文件系统
3. 文件名规则
4. Linux的应用程序的组成部分
1. Linux的文件类型
1. Centos6和Centos7的目录区别
1. 绝对路径和相对路径
1. 显示当前工作目录
1. 基名和目录名
1. cd:更改目录
1. ls:列出目录内容
1. stat:查看文件状态
1. 文件通配符
1. 文件通配符:预定义的字符类
1. touch：创建空文件和刷新3个时间戳
1. cp：复制文件和目录
1. mv:移动和重命名文件
1. rm：删除
1. 目录操作(tree,mkdir,rmdir)
1. 索引节点
1. 硬链接和软链接
1. file：确定文件内容







   
### 目录结构  
+ /boot：引导文件存放目录，内核文件(vmlinuz)、引导加载器(bootloader, 
grub)都存放于此目录  
+ /run 运行  
+ /bin：供所有用户使用的基本命令；不能关联至独立分区，OS启动即会用到的
程序binary  
+ /sbin：管理类（root）的基本命令；不能关联至独立分区，OS启动即会用到的程序  
+ /tmp：临时文件存储位置  
+ /dev：设备文件及特殊文件存储位置（sda，sr0，random随机数，zero，null空值，黑洞）  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;b: block device，随机访问  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;c: character device，线性访问  
+ /media：便携式移动设备挂载点  
+ /mnt：临时文件系统挂载点  
+ /etc：配置文件目录（相当于windows注册表）  
+ /home/USERNAME：普通用户家目录  
+ /root：管理员的家目录  
+ /lib：启动时程序依赖的基本共享库文件以及内核模块文件(/lib/modules)  
+ /lib64：专用于x86_64系统上的辅助共享库文件存放位置  
+ /opt：第三方应用程序的安装位置  
+ /srv：系统上运行的服务用到的数据  
+ /usr: universal shared, read-only data 操作系统软件资源存放位置   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;bin: 保证系统拥有完整功能而提供的应用程序  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;sbin:  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;lib：32位使用  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;lib64：只存在64位系统  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;include: C程序的头文件(header files)  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;share：结构化独立的数据，例如doc, man等  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;local：第三方应用程序的安装位置：bin, sbin, lib, lib64, etc, share  
+ /var: variable data files 经常发生变化的文件的存放位置   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;cache: 应用程序缓存数据目录  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;lib: 应用程序状态信息数据  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;local：专用于为/usr/local下的应用程序存储可变数据；  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;lock: 锁文件  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;log: 日志目录及文件  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;opt: 专用于为/opt下的应用程序存储可变数据；  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;run: 运行中的进程相关数据,通常用于存储进程pid文件  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;spool: 应用程序数据池  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;tmp: 保存系统两次重启之间产生的临时数据  
+ /proc: 用于输出内核与进程信息相关的虚拟文件系统  
+ /sys：用于输出当前系统上硬件设备相关信息虚拟文件系统  
+ /selinux: security enhanced Linux，selinux相关的安全策略等信息的存储位置 

![文件系统目录结构](https://ss0.baidu.com/6LVYsjip0QIZ8Aqbn9fN2DC/timg?pa&quality=100&size=b640_10000&sec=1538879468&di=562d10680a7f7c07b36ba9cf0aab60e4&ref=http%3A%2F%2Fwww%2E05935%2Ecom%2Fbc%2F1277681%2F&src=http%3A%2F%2Fs3%2E51cto%2Ecom%2Fwyfs02%2FM02%2F84%2FF9%2FwKiom1eYtoDxgf5RAADC6uZOe44346%2Epng)

### 文件系统 
+ Centos6:ext4;  
+ Centos7:xfs; 
+ 文件和目录被组织成一个单根倒置树结构  
+ 文件系统从根目录下开始，用“/”表示  
+ 根文件系统(rootfs)：root filesystem  
+ 标准Linux文件系统（如ext4），文件名称大小写敏感  
+ 以.开头的文件为隐藏文件  
+ 路径分隔的 /  
+ 文件有两类数据：元数据：metadata；数据：data  
+ 文件系统分层结构：LSB Linux Standard Base  
+ FHS: (Filesystem Hierarchy Standard)  
[介绍&ensp;&ensp;http://www.pathname.com/fhs/](http://www.pathname.com/fhs/)   

### 文件名规则  
+ 文件名最长255个字节  
+ 包括路径在内文件名称最长4095个字节  
+ 蓝色-->目录  
  绿色-->可执行文件   
  红色-->压缩文件   
  浅蓝色-->软链接文件   
  灰色-->其他文件    
+ 后缀对应的颜色,路径存放在/etc/DTR_COLOR     
+ 除了斜杠和NUL,所有字符都有效.但使用特殊字符的目录名和文件不推荐使用，
有些字符需要用引号来引用它们  

### Linux的应用程序的组成部分  
+ 二进制程序：/bin, /sbin, /usr/bin, /usr/sbin, /usr/local/bin, /usr/local/sbin  
+ 库文件：/lib, /lib64, /usr/lib, /usr/lib64, /usr/local/lib, /usr/local/lib64  
+ 配置文件：/etc, /etc/DIRECTORY, /usr/local/etc  
+ 帮助文件：/usr/share/man, /usr/share/doc, /usr/local/share/man, 
/usr/local/share/doc  

### Linux的文件类型  
+ -：普通文件  
+ d: 目录文件  
+ b: 块设备  
+ c: 字符设备  
+ l: 符号链接文件  
+ p: 管道文件pipe  
+ s: 套接字文件socket  

### Centos6和Centos7的目录区别  
+ /bin 和 /usr/bin   
+ /sbin 和 /usr/sbin   
+ /lib 和/usr/lib   
+ /lib64 和 /usr/lib64  
CentOS7中目录是一样的（软链接）;Centos6中是不同的目录。

### 绝对路径和相对路径  
**绝对路径**  
以正斜杠开始  
完整的文件的位置路径  
可用于任何想指定一个文件名的时候  
**相对路径**  
不以斜线开始  
指定相对于当前工作目录或某目录的位置  
可以作为一个简短的形式指定一个文件名（.. 表示当前目录的父目录；. 表示当前目录）  

For example：两个文件分别位于/data/dir1/f1  和 /data/f2 ，当前工作目录为dir1  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;若访问f2：绝对路径→/data/f2  ；相对路径→ ../f2   

### 显示当前工作目录  
每个shell和系统进程都有一个当前的工作目录  
CWD:current work directory  
显示当前shell CWD的绝对路径  
pwd: printing working directory  
&ensp;&ensp;&ensp;&ensp;-P 显示真实物理路径  
&ensp;&ensp;&ensp;&ensp;-L 显示链接路径（默认） 

### 基名和目录名
+ 基名：basename  可作为命令取出路径的基名    
+ 目录名：dirname  可作为命令取出路径的目录名  
For example: /etc/issue  
 基名：issue  
 目录名：/etc/  

### **cd**:更改目录  
cd: 改变目录,使用绝对或相对路径： cd /home/wang/ ;cd home/wang  
&ensp;&ensp;&ensp;切换至父目录： cd ..  
&ensp;&ensp;&ensp;切换至当前用户家目录： cd /cd ~ /cd ~ wang  
&ensp;&ensp;&ensp;切换至以前的工作目录： cd -  
+ 选项：-P  显示实际路径  
+ 相关的环境变量：PWD：当前目录路径  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;OLDPWD：上一次目录路径  

### **ls**:列出目录内容  
ls:列出当前目录的内容或指定目录  
+ 用法：ls [options] [files_or_dirs]  
+ 选项:  
ls -a 包含隐藏文件  
ls -l 显示额外的信息   
ls -R 目录递归通过  (recusive)  
ls -ld 目录和符号链接信息  
ls -1 文件分行显示（1列）  
ls –S 按从大到小排序(默认顺序竖着显示：1 2 3 a -a A b B...)  
      -r  反序  
ls –t 按mtime排序，修改时间  
ls –u 配合-t选项，显示并按atime从新到旧排序  
ls –U 按目录存放顺序显示  
ls –X 按文件后缀排序  
+ 别名：  
l.  只显示隐藏文件  

### **stat**:查看文件状态  
文件数据：metadata, data  
三个时间戳：  
access time：访问时间，atime，读取文件内容  
modify time: 修改时间, mtime，改变文件内容（数据）  
change time: 改变时间, ctime，元数据发生改变  

### 文件通配符  
\* 匹配零个或多个字符  
? 匹配任何单个字符  
~ 当前用户家目录  
~mage 用户mage家目录  
~+ 当前工作目录  
~- 前一个工作目录  
[0-9] 匹配数字范围  
[a-Z]：字母   
[wang] 匹配列表中的任何的一个字符  
[^wang] 匹配列表中的所有字符以外的字符  

### 文件通配符:预定义的字符类(man 7 glob)  
[:digit:]：任意数字，相当于0-9  
[:lower:]：任意小写字母  
[:upper:]: 任意大写字母  
[:alpha:]: 任意大小写字母  
[:alnum:]：任意数字或字母  
[:blank:]：水平空白字符  
[:space:]：水平或垂直空白字符  
[:punct:]：标点符号  
[:print:]：可打印字符  
[:cntrl:]：控制（非打印）字符  
[:graph:]：图形字符  
[:xdigit:]：十六进制字符  

### **touch**：创建空文件和刷新3个时间戳  
+ 用法：touch [OPTION]... FILE...  
+ 选项：-a 仅改变 atime和ctime  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-m 仅改变 mtime和ctime  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-t [[CC]YY]MMDDhhmm[.ss] 指定atime和mtime的时间戳  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-c 如果文件不存在，则不予创建  

### **cp**：复制文件和目录  
+ 用法；cp [OPTION]... [-T] SOURCE DEST  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;cp [OPTION]... SOURCE... DIRECTORY  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;cp [OPTION]... -t DIRECTORY SOURCE...  

 
|&ensp;&ensp;目标&ensp;&ensp;源|不存在  |存在且为文件 |存在且为目录 |  
|:------:|:------:|:----------:|:----------:|  
|一个文件 |新建DEST，并将SRC中内容填充至DEST中|将SRC中的内容覆盖至DEST中注意数据丢失风险！建议用 –i 选项|在DEST下新建与原文件同名的文件，并将SRC中内容填充至新文件中|  
|多个文件|提示错误|提示错误|在DEST下新建与原文件同名的文件，并将原文件内容复制进新文件中|  
|目录 须使用-r选项|创建指定DEST同名目录，复制SRC目录中所有文件至DEST下|提示错误|DEST下新建与原目录同名的目录,并将SRC中内容复制至新目录中|  

+ 选项  
-i：覆盖前提示 –n:不覆盖，注意两者顺序  
-r, -R: 递归复制目录及内部的所有内容  
-a: 归档，相当于-dR --preserv=all  
-d：--no-dereference --preserv=links 不复制原文件，只复制链接名  
-p:same as --preserv=mode,ownership,timestamps  
-v: --verbose 看到过程  
-f: --force 强制  
-u:--update 只复制源比目标更新文件或目标不存在的文件  
-b: 目标存在，覆盖前先备份  
--backup=numbered 目标存在，覆盖前先备份加数字后缀  

### **mv**:移动和重命名文件  
+ 用法：mv [OPTION]... [-T] SOURCE DEST  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;mv [OPTION]... SOURCE... DIRECTORY  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;mv [OPTION]... -t DIRECTORY SOURCE...  
+ 选项：  
-i: 交互式  
-f: 强制  
-b: 目标存在，覆盖前先备份   
+ 重命名  
mv f1 f2 :把f1改名为f2

### **rm**：删除  
+ 用法：rm [OPTION]... FILE...    
+ 选项：  
-i 交互式 提醒  
-f 强制删除  
-r 递归  
+ 自杀操作：rm -rf --no-preserve-root 删除/   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;rm -rf /*     

### 目录操作  
1. tree 显示目录树  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-d: 只显示目录  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-L level：指定显示的层级数目  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-P pattern: 只显示由指定pattern匹配到的路径  
2. mkdir 创建目录  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-p: 不存在不报错，且可自动创建所需的各目录  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-v: 显示详细信息，过程  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-m MODE: 创建目录时直接指定权限  
3. rmdir 删除空目录  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-p: 递归删除父空目录  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-v: 显示详细信息  
4. rm -r 递归删除目录树  

### 索引节点  
&ensp;&ensp;&ensp;&ensp;inode（index node）表中包含文件系统所有文件列表.  
&ensp;&ensp;&ensp;&ensp;一个节点 （索引节点）是在一个表项，包含有关文件的信息（ 元数据 ），包
括：文件类型，权限，UID，GID;链接数（指向这个文件名路径名称个数）;该文件的大小和不同的时间戳;指向磁盘上文件的数据块指针;有关文件的其他数据.  
&ensp;&ensp;&ensp;&ensp;df -i 看节点编号  

![inode表结构](https://raw.githubusercontent.com/heshan0302/Returning-late/master/picture/inode%E8%A1%A8%E7%BB%93%E6%9E%841.png) 

![inode表结构](https://raw.githubusercontent.com/heshan0302/Returning-late/master/picture/inode%E8%A1%A8%E7%BB%93%E6%9E%842.png)

![inode表](https://raw.githubusercontent.com/heshan0302/Returning-late/master/picture/inode%E8%A1%A8%E7%BB%93%E6%9E%843.png)

+ cp和inode  
CP命令底层操作：
分配一个空闲的inode号，在inode表中生成新条目→ 
在目录中创建一个目录项，将名称与inode编号关联→
拷贝数据生成新的文件  

+ rm和inode  
rm 命令底层操作：
链接数递减，从而释放的inode号可以被重用→
把数据块放在空闲列表中→
删除目录项→
数据实际上不会马上被删除，但当另一个文件使用数据块时将被覆盖  

+ mv和inode  
1. 如果mv命令的目标和源在相同的文件系统，作为mv命令用新的文件名创建对应新的目录项→删除旧目录条目对应的旧的文件名→不影响inode表（除时间戳）或磁盘上的数据位置：没有数据被移动！  
2. 如果目标和源在一个不同的文件系统，mv相当于cp和rm  

### 硬链接和软链接
+ 硬链接（对一个文件起多个名字）  
用法：ln filename [linkname ]    
特点：同一个文件，所有属性相同，所占空间大小为一个文件的大小  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;创建时链接数递增  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;节点编号相同  每个目录引用相同的inode号  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;删除原始文件，链接文件仍可继续访问  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;创建硬链接会增加额外的记录项以引用文件（指针）  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;不能跨分区或驱动器创建硬链接  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;硬链接支持文件，不支持目录  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;目录链接数至少为2  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;文件要存在，至少有一个链接数  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;当链接数为零时，该文件被删除  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;硬链接相对路径是相对于当前目录的路径  

+ 软链接  
用法：ln -s filename [linkname]  
特点：不是同一个文件，属性不同  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;链接数不增长  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;节点编号不同  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;删除原始文件后，链接文件不可访问  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;一个符号链接指向另一个文件（指针）一个符号链接的内容是它引用文件的名称  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;可以跨分区  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;可以对目录进行   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;指向的是另一个文件的路径；其大小为指向的路径字符串的长度；不增加或减少目标文件inode的引用计数  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;软链接原始文件的相对路径是相对于软链接文件的路径   
+ ls - l 显示链接的名称和引用的文件 
  
### **file**：确定文件内容  
+ 文件可以包含多种类型的数据。file检查文件的类型，然后确定适当的打开命令或应用程序使用  
+ 用法：file [options] <文件名>...    
+ 选项:  
-b 列出文件辨识结果时，不显示文件名称  
-f filelist 列出文件filelist中文件名的文件类型  
-F 使用指定分隔符号替换输出文件名后默认的”:”分隔符  
-L 查看对应软链接对应文件的文件类型  
--help 显示命令在线帮助 
