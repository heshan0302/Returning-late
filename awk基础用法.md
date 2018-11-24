# AWK基本用法  
目录  
+ awk介绍   
+ awk工作原理  
+ awk中的域和记录  
+ awk基本用法  
+ awk变量     
+ awk操作符  


## awk介绍  
awk：Aho, Weinberger, Kernighan，是一个优良的文本处理工具，Linux及Unix环境中现有的功能最强大的数据处理引擎之一。 awk基于行处理，对数据进行分析，格式化文本输出，并生成报告。  
版本：New awk（nawk），GNU awk（ gawk）；现在默认linux系统下日常使用的是gawk，用命令可以查看正在应用的awk的来源（ls -l /bin/awk ）  

## awk工作原理   
awk [options] 'BEGIN{action;… }pattern{action;… }END{action;… }' file ...    
第一步：执行BEGIN{action;… }语句块中的语句  
第二步：从文件或标准输入(stdin)读取一行，然后执行pattern{ action;… }语句块，它逐行扫描文件，从第一行到最后一行重复这个过程，直到文件全部被读取完毕。  
第三步：当读至输入流末尾时，执行END{action;…}语句块  
备注：  
BEGIN语句块在awk开始从输入流中读取行之前被执行，这是一个可选的语句块，比如变量初始化、打印输出表格的表头等语句通常可以写在BEGIN语句块中  
END语句块在awk从输入流中读取完所有的行之后即被执行，比如打印所有行的分析结果这类信息汇总都是在END语句块中完成，它也是一个可选语句块  
pattern语句块中的通用命令是最重要的部分，也是可选的。如果没有提供pattern语句块，则默认执行{ print }，即打印每一个读取到的行，awk读取的每一行都会执行该语句块  

## awk中的域和记录    
域：awk执行时，由分隔符分隔的字段（域）标记$1,$2...$n称为域标识。$0
为所有域。此时和shell中变量$符含义不同。awk中默认空格（不限个数）为域分隔符，可以指定其他域分隔符。      
记录：文件的每一行称为记录。awk中默认换行符为记录分隔符，可以指定其他记录分隔符。      


## awk基本用法：  
awk [options] 'program' file…    
选项：  
-F：指定域分隔符     
-v var=value:变量赋值      
-f:调用文件    

### program  
Program：pattern{action statements;..}，通常放在单引号内,{}内自动识别变量，若是字符串，需要加""，{}里数值自动运算。    

#### action  
action statements对数据进行处理，放在{}内指明    
&ensp;&ensp;&ensp;&ensp;print：打印   
&ensp;&ensp;&ensp;&ensp;printf：打印，功能更丰富  
&ensp;&ensp;&ensp;&ensp;省略action，则默认执行 print $0 的操作  
&ensp;&ensp;&ensp;&ensp;算术，比较表达式，if，while。组合语句等，后续会有详细介绍     
一、print  
print格式：print item1, item2, ...  
要点：  
(1) 逗号分隔符  
(2) 输出item可以字符串，也可是数值；当前记录的字段、变量或awk的表达式  
(3) 如省略item，相当于print $0  
示例：   
字符串以逗号分隔  
 ```bash
[root@centos7 ~]# awk 'BEGIN{print "hello,awk"}'
hello,awk
```
item为字符串  
```bash
[root@centos7 ~]# awk  '{print "wang"}' /etc/fstab
wang
wang
wang
wang
wang
wang
wang
wang
wang
wang
wang
wang
```
item为记录的字段(截取部分结果)    
```bash
[root@centos7 ~]# awk -F: '{print $1}' /etc/passwd  
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
operator
games
ftp
nobody
systemd-network
```
省略item，即全部打印     
```bash
[root@centos7 ~]# awk  '{print}' /etc/fstab

#
# /etc/fstab
# Created by anaconda on Sun Oct  7 15:08:27 2018
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=b5c092fb-2469-4781-94c1-15f49ba9b32d /                       xfs     defaults        0 0
UUID=d95a44a1-eac7-4d8e-a200-f0fe35651534 /boot                   xfs     defaults        0 0
UUID=81bc72d7-aa6a-4638-83f1-ed4c1d282992 /data                   xfs     defaults        0 0
UUID=43c94a02-e827-4d61-b398-a6854ef04c6c swap                    swap    defaults        0 0
```   

二、printf  
printf格式：printf “FORMAT”, item1, item2, ...    
要点：  
(1) 必须指定FORMAT  
(2) 不会自动换行，需要显式给出换行控制符，\n  
(3) FORMAT中需要分别为后面每个item指定格式符  
+ 格式符：与item一一对应  
%c：显示字符的ASCII码  
%d, %i：显示十进制整数  
%e, %E：显示科学计数法数值  
%f：显示为浮点数  
%g, %G：以科学计数法或浮点形式显示数值  
%s：显示字符串  
%u：无符号整数  
%%：显示%自身  
+ 修饰符：格式符%和字母之间插入  
\#\[.#] 第一个数字控制显示的宽度；第二个#表示小数点后精度，如%3.1f  
\- 左对齐（默认右对齐） %-15s  
\+ 显示数值的正负符号 %+d 

示例（拷贝/etc/passwd为/root/passwd，并保留前十行）  
```bash
[root@centos7 ~]#awk -F: '{printf "%s",$1}' passwd  
rootbindaemonadmlpsyncshutdownhaltmailoperator
[root@centos7 ~]#awk -F: '{printf "%s\n",$1}' passwd
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
operator
```

```bash
[root@centos7 ~]#awk -F: '{printf "%-20s %-10s\n",$1,$3}' passwd
root                 0         
bin                  1         
daemon               2         
adm                  3         
lp                   4         
sync                 5         
shutdown             6         
halt                 7         
mail                 8         
operator             11        
[root@centos7 ~]#awk -F: '{printf "%20s %10s\n",$1,$3}' passwd  
                root          0
                 bin          1
              daemon          2
                 adm          3
                  lp          4
                sync          5
            shutdown          6
                halt          7
                mail          8
            operator         11
```
```bash
[root@centos7 ~]#awk -F: '{printf "Username: %-15sUID:%d\n",$1,$3}' passwd 
Username: root           UID:0
Username: bin            UID:1
Username: daemon         UID:2
Username: adm            UID:3
Username: lp             UID:4
Username: sync           UID:5
Username: shutdown       UID:6
Username: halt           UID:7
Username: mail           UID:8
Username: operator       UID:11
```  
 
#### pattern       
PATTERN:根据pattern条件，过滤匹配的行，再做处理；决定动作语句何时触发及触发事件    
(1)如果未指定：空模式，匹配每一行  
(2) /regular expression/：正则表达式，仅处理能够模式匹配到的行，需要用/ /括起来   
```bash
[root@centos7 ~]#awk '/^UUID/{print $1}' /etc/fstab
UUID=b5c092fb-2469-4781-94c1-15f49ba9b32d
UUID=d95a44a1-eac7-4d8e-a200-f0fe35651534
UUID=81bc72d7-aa6a-4638-83f1-ed4c1d282992
UUID=43c94a02-e827-4d61-b398-a6854ef04c6c
```
(3) relational expression: 关系表达式，结果为“真”才会被处理  
&ensp;&ensp;&ensp;&ensp;真：结果为非0值，非空字符串；真则处理    
&ensp;&ensp;&ensp;&ensp;假：结果为空字符串或0值；假则不处理    
```bash
[root@centos7 ~]#awk -F: 'i=1;j=1{print i,j}' passwd
root:x:0:0:root:/root:/bin/bash
1 1
bin:x:1:1:bin:/bin:/sbin/nologin
1 1
daemon:x:2:2:daemon:/sbin:/sbin/nologin
1 1
adm:x:3:4:adm:/var/adm:/sbin/nologin
1 1
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
1 1
sync:x:5:0:sync:/sbin:/bin/sync
1 1
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
1 1
halt:x:7:0:halt:/sbin:/sbin/halt
1 1
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
1 1
operator:x:11:0:operator:/root:/sbin/nologin
1 1
[root@centos7 ~]#awk -F: 'i=0;j=1{print i,j}' passwd 
0 1
0 1
0 1
0 1
0 1
0 1
0 1
0 1
0 1
0 1
```  
备注：本题中i=1后面省略了{print $0}，i和j分别做了两次处理。  
```bah
[root@centos7 ~]#awk '!0' passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
[root@centos7 ~]#awk '!1' passwd
[root@centos7 ~]#
```
(4) line ranges：行范围  
startline,endline：/pat1/,/pat2/ 不支持直接给出数字格式，可用内置变量NR来实现    
```bash
 [root@centos7 ~]#awk -F: '/^root/,/^nobody/{print $1}' /etc/passwd
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
operator
games
ftp
nobody
[root@centos7 ~]#awk -F: 'NR>=10 && NR<=20{print $1}' /etc/passwd
operator
games
ftp
nobody
systemd-network
dbus
polkitd
libstoragemgmt
rpc
colord
saslauth
``` 
(5) BEGIN/END模式    
BEGIN{}：仅在开始处理文件中的文本之前执行一次    
END{}：仅在文本处理完成之后执行一次   
```bash
[root@centos7 ~]#awk -F: 'BEGIN{print "USER USERID"}{print $1":"$3}END{print "END FILE"}' passwd     
USER USERID
root:0
bin:1
daemon:2
adm:3
lp:4
sync:5
shutdown:6
halt:7
mail:8
operator:11
END FILE
```
(6) 特殊示例(真即处理，假即不处理)    
```bash
[root@centos7 ~]#seq 10 |awk 'i=0'
[root@centos7 ~]#seq 10 |awk 'i=1'
1
2
3
4
5
6
7
8
9
10
[root@centos7 ~]#seq 10 |awk 'i=!i'
1
3
5
7
9
[root@centos7 ~]#seq 10 |awk '{i=!i;print i}'
1
0
1
0
1
0
1
0
1
0
[root@centos7 ~]#seq 10 |awk -v i=1 'i=!i'          
2
4
6
8
10
```






## awk变量  
### 内置变量  
1. FS：输入字段分隔符，默认为空白字符    
```bash
[root@centos7 ~]#awk -v FS=":" '{print $1,FS,$3}' passwd
root : 0
bin : 1
daemon : 2
adm : 3
lp : 4
sync : 5
shutdown : 6
halt : 7
mail : 8
operator : 11
[root@centos7 ~]#awk -F ":" '{print $1":"$3}' passwd  root:0
bin:1
daemon:2
adm:3
lp:4
sync:5
shutdown:6
halt:7
mail:8
operator:11
```

2. OFS：输出字段分隔符，默认为空白字符  
```bash
[root@centos7 ~]#awk -v FS=":" -v OFS="+++" '{print $1,$3}' passwd 
root+++0
bin+++1
daemon+++2
adm+++3
lp+++4
sync+++5
shutdown+++6
halt+++7
mail+++8
operator+++11
```
 
3. RS：输入记录分隔符，指定输入时的换行符  
```bash
[root@centos7 ~]#awk -v RS=":" '{print}' passwd |head
root
x
0
0
root
/root
/bin/bash
bin
x
1
```

4. ORS：输出记录分隔符，输出时用指定符号代替换行符   
```bash
[root@centos7 ~]#awk -v ORS="+++" '{print}' passwd   
root:x:0:0:root:/root:/bin/bash+++bin:x:1:1:bin:/bin:/sbin/nologin+++daemon:x:2:2:daemon:/sbin:/sbin/nologin+++adm:x:3:4:adm:/var/adm:/sbin/nologin+++lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin+++sync:x:5:0:sync:/sbin:/bin/sync+++shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown+++halt:x:7:0:halt:/sbin:/sbin/halt+++mail:x:8:12:mail:/var/spool/mail:/sbin/nologin+++operator:x:11:0:operator:/root:/sbin/nologin+++[root@centos7 ~]#
```
 
5. NF：字段数量    
```bash
[root@centos7 ~]#awk -F: '{print NF}' passwd
7
7
7
7
7
7
7
7
7
7
[root@centos7 ~]#awk -F: '{print $NF}' passwd
/bin/bash
/sbin/nologin
/sbin/nologin
/sbin/nologin
/sbin/nologin
/bin/sync
/sbin/shutdown
/sbin/halt
/sbin/nologin
/sbin/nologin
[root@centos7 ~]#awk -F: '{print $(NF-1)}' passwd
/root
/bin
/sbin
/var/adm
/var/spool/lpd
/sbin
/sbin
/sbin
/var/spool/mail
/root
```
 
6. NR：记录编号    
```bash
[root@centos7 ~]#awk '{print NR}' passwd
1
2
3
4
5
6
7
8
9
10
[root@centos7 ~]#awk -F: '{print NR,$1}' passwd 
1 root
2 bin
3 daemon
4 adm
5 lp
6 sync
7 shutdown
8 halt
9 mail
10 operator
```

7. FNR：各文件分别计数,记录号  
```bash
[root@centos7 ~]#cat f1 passwd
1
2
3
4
5
6
7
8
9
10
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
[root@centos7 ~]#awk -F: '{print NR,$1}' f1 passwd 
1 1
2 2
3 3
4 4
5 5
6 6
7 7
8 8
9 9
10 10
11 root
12 bin
13 daemon
14 adm
15 lp
16 sync
17 shutdown
18 halt
19 mail
20 operator
[root@centos7 ~]#awk -F: '{print FNR,$1}' f1 passwd
1 1
2 2
3 3
4 4
5 5
6 6
7 7
8 8
9 9
10 10
1 root
2 bin
3 daemon
4 adm
5 lp
6 sync
7 shutdown
8 halt
9 mail
10 operator
```
备注：NR全部计数，共20；FNR分别计数，每个文件各10。  

8. FILENAME：当前文件名   
```bash
[root@centos7 ~]#awk  '{print FILENAME}' passwd   
passwd
passwd
passwd
passwd
passwd
passwd
passwd
passwd
passwd
passwd
```

9. ARGC：命令行参数的个数   
ARGV：数组，保存的是命令行所给定的各参数  
```bash
[root@centos7 ~]#awk 'BEGIN{print ARGC}' f1 passwd
3
[root@centos7 ~]#awk 'BEGIN{print ARGV[0]}' f1 passwd 
awk
[root@centos7 ~]#awk 'BEGIN{print ARGV[1]}' f1 passwd 
f1
[root@centos7 ~]#awk 'BEGIN{print ARGV[2]}' f1 passwd 
passwd
``` 

### 自定义变量  
(1) -v var=value 定义变量  
(2) 也可以在program中直接定义     
(3) 变量先定义后使用
(4) 区分字符大小写      
示例：  
```bash
[root@centos7 ~]#awk -v test="return late" 'BEGIN{print test}'
return late
[root@centos7 ~]#awk 'BEGIN{test="return late";print test}'
return late
[root@centos7 ~]#awk 'BEGIN{test="return late";print test,test1;test1="hs"}'
return late
[root@centos7 ~]#awk 'BEGIN{test="return late";test1="hs";print test,test1;}'       
return late hs
```

### awk操作符  
1. 算术操作符:  
+, -, *, /, ^, %, -x(转换为负数),+x(将字符串转换为数值)    
字符串操作符：  
没有符号的操作符，字符串连接    
赋值操作符：    
=, +=, -=, *=, /=, %=, ^=，++, --    
比较操作符：    
==, !=, >, >=, <, <=    
模式匹配符：    
~：左边是否和右边匹配，包含    
!~：是否不匹配        
示例：   
```bash
[root@centos7 ~]#awk 'BEGIN{i=0;print ++i,i}'
1 1
[root@centos7 ~]#awk 'BEGIN{i=0;print i++,i}'
0 1
```
```bash
[root@centos7 ~]#awk -F: '$0 ~ /^root/{print $0}' passwd
root:x:0:0:root:/root:/bin/bash
[root@centos7 ~]#awk -F: '$0 !~ /^root/{print $1}' passwd
bin
daemon
adm
lp
sync
shutdown
halt
mail
operator
[root@centos7 ~]#awk -F: '$3==0{print $1}' passwd      
root   
```

2. 逻辑操作符：  
与&&，或||，非!(后面条件需要用（）括起来)     
示例：  
```bash
[root@centos7 ~]#awk -F: 'NR>=2 && NR<=4 {print $1}' passwd
bin
daemon
adm
[root@centos7 ~]#awk -F: 'NR<=2 || NR>=8 {print $1}' passwd
root
bin
halt
mail
operator
[root@centos7 ~]#awk -F: 'NR==5 {print $1}' passwd    lp
[root@centos7 ~]#awk -F: '!(NR==5) {print $1}' passwd
root
bin
daemon
adm
sync
shutdown
halt
mail
operator
```
3. 条件表达式（三目表达式）  
selector?if-true-expression:if-false-expression   
在{}内，数据处理之前，判断  
示例：  
```bash
[root@centos7 ~]#awk -F: '{$3==0?user="admin":user="general";printf "%15s %-s\n",$1,user}' passwd     
           root admin
            bin general
         daemon general
            adm general
             lp general
           sync general
       shutdown general
           halt general
           mail general
       operator general
```

