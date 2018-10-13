# 标准I/O和管道  
 
## 目录  
1. 标准输入和输出  
2. Linux给程序提供三种I/O设备  
3. 把输出和错误重新定向到文件   
4. 标准输入重定向  
5. 多行重定向  
6. tr命令   
7. 管道  
8.  管道相关命令、符号



## 标准输入和输出  

&ensp;&ensp;&ensp;&ensp;程序：指令+数据  
&ensp;&ensp;&ensp;&ensp;读入数据：Input&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp; I  
&ensp;&ensp;&ensp;&ensp;输出数据：Output &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;O    

## Linux给程序提供三种I/O设备
打开的文件都有一个文件描述符fd: file descriptor (是个数字)  
标准输入（STDIN） &ensp;&ensp;  －0  默认接受来自键盘的输入  
标准输出（STDOUT） &ensp;－1  默认输出到当前终端窗口  
标准错误（STDERR）&ensp; －2  默认输出到当前终端窗口  
I/O重定向（redirect）：改变默认位置

## 把输出和错误重新定向到文件（单行）  
1. STDOUT和STDERR可以被重定向到文件  
+ 用法：命令 操作符号 文件名  
+ 支持的操作符号包括：  
&ensp;&ensp;&ensp;&ensp;1> 把STDOUT重定向到文件或其他终端窗口（1可省略，默认1）  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;如：ls /etc/ -R > /data/etclist.txt 把etc下文件详情重定向到文件里，可后续分析命令结果  
&ensp;&ensp;&ensp;&ensp;ls > /dev/pts/2 把标准输出重定向到pts2终端窗口  
&ensp;&ensp;&ensp;&ensp;1>> 原有内容基础上，追加内容（1可省略，默认1）  
&ensp;&ensp;&ensp;&ensp;2> 把STDERR重定向到文件  
&ensp;&ensp;&ensp;&ensp;2>> 追加错误重定向到文件  
&ensp;&ensp;&ensp;&ensp;&> 把所有输出重定向到文件  
&ensp;&ensp;&ensp;&ensp;> 如果文件存在，文件内容会被覆盖  
&ensp;&ensp;&ensp;&ensp;>> 如果文件存在，原有内容基础上，追加内容  
&ensp;&ensp;&ensp;&ensp;set –C 禁止将内容覆盖已有文件,但可追加  
&ensp;&ensp;&ensp;&ensp;>| file 强制覆盖（在set -C情况下）  
&ensp;&ensp;&ensp;&ensp;set +C 允许覆盖  

2. 标准输出和错误输出各自定向至不同位置  
 + 错误可以重定向到null。  
如：2> /dev/null  
&ensp;&ensp;&ensp;&ensp;错误不会显示在终端窗口  
如：COMMAND  > /path/to/file.out  2> /path/to/error.out    
 &ensp;&ensp;&ensp;&ensp;命令如果是标准输出重定向到file中，如果是标准错误重定向到error中。   
3. 合并标准输出和错误输出为同一个数据流进行重定向  
+ &> 覆盖重定向  
如：ls /boot  /dir  &> /root/all.log  
+ &>> 追加重定向  
+ COMMAND  > /path/to/file.out  2>&1   
如： ls /boot  /dir  > /root/all2.log  2>&1  
&ensp;&ensp;&ensp;&ensp;标准输出重定向到文件，标准错误转成标准输出重定向到文件中，顺序不可颠倒    
+ COMMAND  >> /path/to/file.out  2>&1  
+ ERROR COMMAND  > /path/to/file.out  1>&2  
如：ls /data  /noexist  2> /root/all3.log  1>&2  
&ensp;&ensp;&ensp;&ensp;标准错误重定向到文件，标准输出转成标准错误重定向到文件中，顺序不可颠倒  


4. &ensp;&ensp;&ensp;&ensp;()：合并多个程序的STDOUT  
如：( cal 2007 ; cal 2008 ) > all.txt  

## 标准输入重定向   
+ 把需要输入的内容放到文件中，用操作符号把文件内容代替键盘输入。  
+ 操作符号：<  
如：nano  f1.txt     
&ensp;&ensp;&ensp;&ensp;2*3  
&ensp;&ensp;&ensp;&ensp;bc  < f1.txt     
&ensp;&ensp;&ensp;&ensp;6  
+ 有标准输入的命令：bc cat tr mail  
如： cat  <f1.txt  >f2.txt &ensp;&ensp;&ensp;&ensp;相当于复制f1内容到f2  

## 多行重定向：  
+ 把多行发送给STDIN  
+ 使用“<<终止词”命令从键盘把多行重导向给STDIN，直到“终止词”位置的所有文本都发送给STDIN，时被称为就地文本（heretext）。  
如：mail -s "Please Call"  admin@magedu.com <<**_END_**   
&ensp;&ensp;&ensp;&ensp;\> Hi Wang,  
&ensp;&ensp;&ensp;&ensp;\>  
&ensp;&ensp;&ensp;&ensp;\> Please give me a call when you get in. We may need   
&ensp;&ensp;&ensp;&ensp;\> to do some maintenance on server1.   
&ensp;&ensp;&ensp;&ensp;\>  
&ensp;&ensp;&ensp;&ensp;\> Details when you're on-site  
&ensp;&ensp;&ensp;&ensp;\> Zhang  
&ensp;&ensp;&ensp;&ensp;\> **_END_**  
说明：终止词必须一致，不能加任何符号如空格；建议用EOF（end of file）。  


## tr命令  
+ tr 转换和删除字符  
+ 用法：tr [OPTION]... SET1 [SET2]  
+ 选项：  
-c  -C --complement：取字符集的补集（取反）  
-d  --delete：删除所有属于第一字符集的字符  
-s  --squeeze-repeats：把连续重复的字符以单独一个字符表示  
-t  --truncate-set1：将第一个字符集对应字符转化为第二字符集对应的字符  
[:alnum:]：字母和数字    &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;      [:alpha:]：字母           
[:cntrl:]：控制（非打印）    &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;    [:digit:]：数字          
[:graph:]：图形字符        &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;     [:lower:]：小写字母       
[:print:]：可打印字符       &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;     [:punct:]：标点符号         
[:space:][:upper:]：大写字母   &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;   [:xdigit:]：十六进制字符  
ctrl +d 提交  
+ 如：tr ‘a-z’ ‘A-Z’< /etc/issue  
&ensp;&ensp;&ensp;&ensp; 该命令会把/etc/issue中的小写字符都转换成写写字符  
&ensp;&ensp;&ensp;&ensp;tr –d abc < /etc/fstab   
&ensp;&ensp;&ensp;&ensp; 删除fstab文件中的所有abc中任意字符  
  如：1+2+3+...100=？  
&ensp;&ensp;&ensp;&ensp;echo {1..100} >bc.txt  
&ensp;&ensp;&ensp;&ensp;tr “ ” + <bc.txt >tr.txt  
&ensp;&ensp;&ensp;&ensp;bc <tr.txt  
     
## 管道  
+ 管道（使用符号“|”表示）用来连接命令  
+ 用法：命令1 | 命令2 | 命令3 | …  
+ 说明：  
将命令1的STDOUT发送给命令2的STDIN，命令2的STDOUT发送到命令3的STDIN  
STDERR默认不能通过管道转发，可利用2>&1 或 |& 实现  
&ensp;&ensp;&ensp;&ensp;如：errorcmd | tr ‘a-z’ ‘A-Z’  
最后一个命令会在当前shell进程的子shell进程中执行用来  
&ensp;&ensp;&ensp;&ensp;如：ls | tr ‘a-z’ ‘A-Z  
&ensp;&ensp;&ensp;&ensp;如：echo {1..100} |tr “ ” + |bc  


## 管道相关命令、符号  
1. 命令   
+ less ：一页一页地查看输入  
如：ls -l /etc | less   
+ mail： 通过电子邮件发送输入  
如：echo "test email" | mail -s "test" user@example.com  
+ lpr：把输入发送给打印机  
如：echo "test print" | lpr -P printer_name  
+ tee:重定向到多个目标,既可以有标准输出也重定向到文件中  
&ensp;&ensp;&ensp;&ensp;用法：命令1 | tee [-a ] 文件名 | 命令2   
&ensp;&ensp;&ensp;&ensp;说明：把命令1的STDOUT保存在文件中，做为命令2的输入  
&ensp;&ensp;&ensp;&ensp;-a 追加  
&ensp;&ensp;&ensp;&ensp;使用：保存不同阶段的输出  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;复杂管道的故障排除  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;同时查看和记录输出   

2. 符号：-   
如:将 /home 里面的文件打包，但打包的数据不是记录到文件，而是传送到 stdout，经过管道后，将 tar -cvf - /home 传送给后面的 tar -xvf - , 后面的这个 - 则是取前一个命令的 stdout， 因此，就不需要使用临时file了  
&ensp;&ensp;&ensp;&ensp;tar -cvf - /home | tar -xvf -  测试打包和解包过程   
