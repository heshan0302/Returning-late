## 目录

## 计算机基础
一 计算机系统组成  
二 冯·诺伊曼体系  
三 服务器   


## Linux基础
一 Linux哲学思想  
二 获取CentOS发行版  
三 虚拟机  
四 用户及命令提示符  
五 终端  
六 交互式接口  
七 Shell  
八 执行命令  
九 Linux日期和时间  
十 获得帮助  
十一 ASCII和Unicode  

## 备注




# 计算机基础
一 计算机系统组成  
&ensp;&ensp;&ensp;&ensp;由硬件系统和软件系统组成。  
1. 硬件系统（Hardware）  
&ensp;&ensp;&ensp;&ensp;主机：  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;中央处理器CPU：运算器和控制器  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;内存储器：ROM（存储内存）和RAM（运行内存）  
&ensp;&ensp;&ensp;&ensp;外部设备：  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;外部存储器：如硬盘、软盘、光盘、磁带等  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;输入设备：如键盘、鼠标、扫描仪等  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;输出设备：如显示器、打印机、绘图仪等  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;其他设备：调制解调器等  
2. 软件系统（Software）  
&ensp;&ensp;&ensp;&ensp;系统软件：  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;操作系统  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;程序语言处理系统  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;数据库管理系统  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;服务程序——检查、诊断、调试程序  
&ensp;&ensp;&ensp;&ensp;应用软件：  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;通用应用软件：如office办公软件包  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;专用应用软件：用户程序    

二 冯·诺伊曼体系 

 &ensp;&ensp;&ensp;&ensp;1946年数学家冯·诺依曼于提出，计算机硬件由运算器、 控制器、存储器、输入设备和输出设备五大部分组成。  

 三 服务器  
 1. 简介：服务器Server是计算机的一种，是网络中为客户端计算机提供各种服务的高性 能的计算机，服务器在网络操作系统的控制下，将与其相连的硬盘、磁带、打 印机及昂贵的专用通讯设备提供给网络上的客户站点共享，也能为网络用户提 供集中计算、信息发布及数据管理等服务。  
 2. 功能分类：Web服务器、数据库服务器、文件服务器、中间件应用服务器、日志服务器、 监控服务器，程序版本控制服务器、虚拟机服务器、邮件服务器、打印服务器、 域控制服务器、多媒体服务器、通讯服务器、ERP服务器等 。  
 3. 硬件组成：电源、CPU、网卡、PCI、硬盘、内存、风扇。  
 4. 三大操作系统：Windows；Linux；Unix。     

 # Linux基础  
 1. 哲学思想：  
 &ensp;&ensp;&ensp;&ensp;一切皆文件  
 &ensp;&ensp;&ensp;&ensp;小型，单一用途的程序  
 &ensp;&ensp;&ensp;&ensp;链接程序，共同完成复杂的任务  
 &ensp;&ensp;&ensp;&ensp;避免令人困惑的用户界面  
 &ensp;&ensp;&ensp;&ensp;配置数据存储在文本中  
 2. 发行版  
 *  slackware:&ensp;&ensp;SUSE Linux Enterprise Server (SLES) &ensp;&ensp;&ensp;&ensp; OpenSuse桌面 
*  debian:&ensp;&ensp; ubuntu，mint 
* redhat： RHEL(RedHat Enterprise Linux);&ensp;&ensp;**CentOS**(兼容RHEL的格式);&ensp;&ensp;中标麒麟；&ensp;&ensp;Fedora  
* ArchLinux：轻量简洁   
* Gentoo：极致性能，不提供传统意义的安装程序  
我们学习CentOS版本。  
获取CentOS发行版：  
&ensp;&ensp;&ensp;&ensp;[官网 https://wiki.centos.org/Download ](https://wiki.centos.org/Download)  
&ensp;&ensp;&ensp;&ensp;[阿里云&ensp;&ensp;http://mirrors.aliyun.com](http://mirrors.aliyun.com)  
&ensp;&ensp;&ensp;&ensp;[搜狐&ensp;&ensp;http://mirrors.sohu.com](http://mirrors.sohu.com)  
&ensp;&ensp;&ensp;&ensp;[163&ensp;&ensp; http://mirrors.163.com ]( http://mirrors.163.com )  
过程：点开任何网站，找到需要的centos版本，点击ISOS文件，X86-64版本。centos6.10下载DVD1和DVD2；centos7.5下载Everything。  
3. 虚拟机：用软件（如：vmware,virtualbox等）模拟硬件。
&ensp;&ensp;&ensp;&ensp;硬件模拟完毕，安装CentOS操作系统。
4. 用户登录及命令提示符  
a.root用户：特殊的管理账户，也被成为超级用户。root已接近完整的系统控制 对系统损害几乎有无限的能力 除非必要,不要登录为 root。命令提示符（Prompt）为#，且ID号为0。  
b.普通用户（非特权）：权限有限，造成损害的能力比较有限。命令提示符（Prompt）为$，ID号非0。  
&ensp;&ensp;&ensp;&ensp;显示提示符格式：echo $PS1（变量）  
&ensp;&ensp;&ensp;&ensp;修改提示符格式：PS1="\[\e[1;5;41;33m\][\u@\h \W]\\$\[\e[0m\]"（表示提示符黄色，背景红色且闪烁）   
1：颜色加强；5：背景颜色闪烁；41-47：背景颜色；31-37：字体颜色；\u：当前用户；\h:主机名简称； \H：主机名；\w:当前工作目录；\W:当前工作目录基名；\\\\$\[\e[0m\\]:表示只修改提示符的格式。  
5. 终端terminal （查看当前终端：tty） 
+ 设备终端：键盘鼠标显示器；  
+ 物理终端：控制台console；  
+ 虚拟终端：teletypewrites，tty可有n个，Ctrl+Alt+F[1-6]可以切换；  
+ 图形终端：CentOS6用Ctrl+Alt+F7可切换；CentOS7位于启动时的终端；  
+ 串行终端：ttyS；  
+ 伪终端：远程连接。  
6. 交互式接口  
启动终端后，在终端设备附加一个交互式应用程序。  
GUI：图形界面。Desktop:GNOME;KDE;XFCE。  
GLI:&ensp;&ensp;字符界面。利用SHELL程序完成。
7. Shell  
&ensp;&ensp;&ensp;&ensp;Shell 是Linux系统的用户界面，提供了用户与内核进行交互操作的一种接口。 它接收用户输入的命令并把它送入内核去执行。也被称为LINUX的命令解释器。  
&ensp;&ensp;&ensp;&ensp;CentOS默认使用的Shell类型是bash。  
&ensp;&ensp;&ensp;&ensp;显示当前系统使用的所有shell：cat /etc/shells  
&ensp;&ensp;&ensp;&ensp;显示当前使用的shell：echo $SHELL  
8. 执行命令  
输入命令后回车即可执行。  
+ 内部命令：由shell自带的，通过某命令形式提供。  &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;help 内部命令列表  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;enable cmd 启用内部命令  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;enable -n cmd 禁用内部命令  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;enable -n 查看所有禁用的内部命令  
+ 外部命令：在文件系统路径下有对应的可执行程序文件。  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;查看路径：which；whereis。  
+ Hash缓存:系统初始hash表为空，当外部命令执行时，默认会从PATH路径下寻找该命令，找到后会将这条命令的路径记录到hash表中。当再次使用该命令时，shell解释器首先会查看hash表，存在将执行之，如果不存在，将会去PATH路径下寻找。  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;hash 显示hash缓存  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;hash –l 显示hash缓存，可作为输入使用     
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;hash –t name 打印缓存中name的路径   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;hash –d name 清除name缓存   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;hash –r 清除缓存  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;hash –p path name 将命令全路径起别名   
+ 别名（alias）：可以给某些常用且长的命令起别名，但仅对当前shell进程有效。若想永久有效，需保存在配置文件中，即.bashrc。  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;alise 显示当前shell进程所有可用的命令别名  
+ 简单命令：  
halt poweroff:关机  
reboot：重启  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-f: 强制  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-p:切断电源  
关机或重启：shutdown  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-r: reboot   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-h: halt  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-c: cancel  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;+m:相对时间表示法  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;hh:mm:绝对时间表示，指具体时间  
whoanmi：显示当前登录有效用户   
who: 系统当前所有的登录会话   
w: 系统当前所有的登录会话及所做的操作   
nano:文本编辑，写到文件里  
screen:会话  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-S[aa]:创建新的会话aa  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-x[aa]:加入会话aa  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;exit:全部退出并关闭会话  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;Ctrl+a,d:剥离当前会话  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-ls:显示所有已经打开的会话  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-r[aa]:恢复aa会话  
echo：显示字符  显示变量   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-n:不自动换行  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-e:启用\字符的解释功能   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;\a：发出警告声  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;\b：退格键  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;\c：最后不加上换行符号  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;\n：换行且光标移至行首  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;\r：光标移至行首，但不换行  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;\t：插入tab  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;\\\\:插入\字符  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;\0aaa：表示aaa（八进制）代表的ASCII数字  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;\xaa:表示aa(十六进制)代表的ASCII数字   
+ 组合命令：  
&ensp;&ensp;&ensp;&ensp;双引号"":可识别变量不识别命令  
&ensp;&ensp;&ensp;&ensp;单引号'':不识别变量不识别命令  
&ensp;&ensp;&ensp;&ensp;反向单引号``:可识别变量可识别命令  同$()功能一样  
如：输入 echo "i am $(whoami)"   
&ensp;&ensp;&ensp;&ensp;显示：i am root  
+ tab键：命令补全/路径补全  
&ensp;&ensp;&ensp;&ensp;如果给出的字符或路径惟一，直接补全；否则，再次tab给出列表。  
+ 命令history  
&ensp;&ensp;&ensp;&ensp;系统会保存你输入的命令历史。可以用它来重复执行命令。登录进shell后新执行的命令只会记录在缓存中；这些命令会用户退出时“追 加”至命令历史文件~/.bash_history中。查看历史列表可用history来执行。  
history -c: 清空命令历史  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-d aa:删除历史中指定的第aa个命令  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-a:追加本次会话新执行的命令历史列表至历史文件  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-r: 读历史文件附加到历史列表   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-w: 保存历史列表到指定的历史文件   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-n: 读历史文件中未读过的行到历史列表   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-p: 展开历史参数成多行，但不存在历史列表中   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-s: 展开历史参数成一行，附加在历史列表后  
+ 命令历史相关环境变量  
HISTSIZE：命令历史记录的条数    
HISTFILE：指定历史文件，默认为~/.bash_history   
HISTFILESIZE：命令历史文件记录历史的条数   HISTTIMEFORMAT=“%F %T “   显示时间   HISTIGNORE=“str1:str2*:… “ 忽略str1命令，str2开头的历史  
HISTCONTROL=ignoredups 默认，忽略连续相同的命令  
HISTCONTROL=ignorespace 忽略所有以空白开头的命令   
HISTCONTROL=ignoreboth 相当于前两个的组合   
HISTCONTROL=erasedups 删除重复命令   
保存路径：/etc/profile 或 ~/.bash_profile  
+ bash快捷键：  
Ctrl + l 清屏，=clear   
Ctrl + o 执行当前命令，并重新显示本命令   
Ctrl + s 阻止屏幕输出，锁定   
Ctrl + q 允许屏幕输出   
Ctrl + c 终止命令   
Ctrl + z 挂起命令  
Ctrl + a 光标移到命令行首，相当于Home   
Ctrl + e 光标移到命令行尾，相当于End   
Ctrl + f 光标向右移动一个字符   
Ctrl + b 光标向左移动一个字符   
Alt + f 光标向右移动一个单词尾   
Alt + b 光标向左移动一个单词首   
Ctrl + xx 光标在命令行首和光标之间移动   
Ctrl + u 从光标处删除至命令行首   
Ctrl + k 从光标处删除至命令行尾   
Alt + r  删除当前整行  
Ctrl + w 从光标处向左删除至单词首  
Alt + d 从光标处向右删除至单词尾  
Ctrl + d 删除光标处的一个字符   
Ctrl + h 删除光标前的一个字符   
Ctrl + y 将删除的字符粘贴至光标后  
Alt + c 从光标处开始向右更改为首字母大写的单词  
Alt + u 从光标处开始，将右边一个单词更改为大写  
Alt + l 从光标处开始，将右边一个单词更改为小写  
Ctrl + t 交换光标处和之前的字符位置  
Alt + t 交换光标处和之前的单词位置  
Alt + N 提示输入指定字符后，重复显示该字符N次  

执行命令总顺序：先看是否是alias→是否为内部命令→hash表是否有缓存→按照变量PATH路径查看。  
9. 日期和时间  
Linux两种时钟  
&ensp;&ensp;&ensp;&ensp;系统时钟：由Linux内核通过CPU的工作频率进行的。date查看。    
&ensp;&ensp;&ensp;&ensp;硬件始终：主板。clock查看。  
时区：/etc/localtime   
显示日历：cal –y   
10. 获得帮助   

+ whatis cmd:使用数据库显示命令的简短描述，刚安装后不可立即使用。makewhatis|mandb可制作数据库。  
+ cmd --help/-h  
+ man cmd:使用手册，常用于命令参考。先查看章节号，再man -w cmd。  
+ info cmd:适合通用文档参考。  
+ 本地文档：  
&ensp;&ensp;&ensp;&ensp;System->help（centos6）  
&ensp;&ensp;&ensp;&ensp;Applications -> documentation->help（centos7） 官方使用指南和发行注记  
&ensp;&ensp;&ensp;&ensp;/usr/share/doc目录：相关软件的原理说明  
+ 在线文档：官网下载官方文档。  
11. ASCII和Unicode  
+ ASCII码：计算机内部，所有信息最终都是一个二进制值。上个世纪60年代，美国制定了一套字符编码，对英语字符与二进制位之间的关系，做了统一规定。ASCII 码一共规定了128个字符的编码，占用了一个字节的后面7位，最前面的 一位统一规定为0 。  
+ Unicode：用于表示世界上所有语言中的所有字符。每一个符号都给予一个独一无二的编码数字，Unicode是一个很大的集合，现在的规模可以容纳100多万个符号。Unicode仅仅只是一个字符集，规定了每个字符对应的二进制代码， 至于这个二进制代码如何存储则没有规定。常用：UTF-8.  
+ UTF-8编码规则：对于单个字节的字符，第一位设为0，后面的7位对应这个字符的 Unicode码点。因此，对于英文中的0-127 号字符，与ASCII码完全相同。这意味着 ASCII码的文档可UTF-8编码打开。  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;对于需要使用N个字节来表示的字符（N>1），第一个字节的前N位都设为1，第N+1位设为0，剩余的N-1个字节的前两位都设位10，剩下的二进制位则使用这个字符的Unicode码点来填充。  
+ example：  
“汉”，网址转换出Unicode编码，为6C49； 6C49转成二进制，得到110110001001001；  
6C49用hexdump查看磁盘保存，为E6B189；十六进制的E6B189为3个字节，按照规则第一个字节的前3位为111，第4位为0，剩余2，3字节前两位都设为10，即显示1110nnnn 10nnnnnn 10nnnnnn;  
把6C49对应的二进制110110001001001按照规则从第3字节到第一字节顺序补齐，空位补0，得到11100110 10110001 10001001；  
把磁盘保存E6B189，转成二进制查对，与上述一致。  


# 备注  
根据课件计算机基础和Linux基础简单总结，无操作截图。课件时间20180917/0919/0921。








