# 用户、组和权限  
目录  
1. 资源分派：安全3A  
2. 用户和组   
2.1 用户user   
2.2 组group   
2.3 安全上下文    
2.4 Linux组的类别 
3. Linux用户和组的主要配置文件  
3.1 passwd文件   
3.2 shadow文件   
3.3 密码加密   
3.4 group文件  
3.5 gshadow文件   
3.6 文件操作 
3. 用户和组管理命令   
4.1 useradd   
4.2 usermod  
4.3 userdel   
4.4 id   
4.5 su   
4.6 passwd   
4.7 chage   
4.8 其他   
4.9 groupadd   
4.10  groupmod  
4.11  grouddel  
4.12  gpasswd  
4.13  newgrp  
4.14  groupmems  
4.15  groups
4. 文件权限   
5.1 基本内容  
5.2 相关命令   
5.3 umask   
5. 特殊文件权限  
6.1 SUID  
6.2 SGID  
6.3 Sticky  
6.4 特定属性  
6. 访问控制列表



## 资源分派：安全3A   
&ensp;&ensp;&ensp;&ensp; Authentication：认证（验证身份）  
&ensp;&ensp;&ensp;&ensp; Authorization：授权 （根据身份授权）  
&ensp;&ensp;&ensp;&ensp; Accouting|Audition：审计 （监控）   

## 用户和组
+ 用户user  
令牌token，ID号identity  
Linux用户：Username和UID  
管理员：root；管理员的ID：0  
普通用户：1-60000 ID号自动分配  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;系统用户：1-499（CentOS6）, 1-999 （CentOS7）  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;对守护进程获取资源进行权限分配  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;登录用户：500+（CentOS6）, 1000+（CentOS7） 
交互式登录   

+ 组group  
Linux组：Groupname和GID（组ID号）  
管理员组：root；GID：0  
普通组：  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;系统组：1-499（CentOS6）, 1-999（CentOS7）  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;普通组：500+（CentOS6）, 1000+（CentOS7）  
+ 安全上下文  
安全上下文指的是一类定义某个进程允许做什么的许可和权限的集合。  
Linux安全上下文  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;运行中的程序：进程 (process)  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;以进程发起者的身份运行：  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;root: /bin/cat  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;mage: /bin/cat  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;进程所能够访问资源的权限取决于进程的运行者的身份   

+ Linux组的类别   
分主组（primary group）和附加组(supplementary group)  
用户必须属于一个且只有一个主组   
一个用户可以属于零个或多个辅加组  
如：id username :可以显示出用户的UID，GID，和所属组。  


## Linux用户和组的主要配置文件  
&ensp;&ensp;&ensp;&ensp;/etc/passwd：用户及其属性信息(名称、UID、主组ID等）  
&ensp;&ensp;&ensp;&ensp;/etc/group：组及其属性信息  
&ensp;&ensp;&ensp;&ensp;/etc/shadow：用户密码及其相关属性  
&ensp;&ensp;&ensp;&ensp;/etc/gshadow：组密码及其相关属性  

+ passwd文件格式：（以：分割7个域）  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;root:&ensp;x&ensp;:0:0:root:/root:/bin/bash  
1.login name：登录用名（root）  
2.passwd：密码加密 (x)  
3.UID：用户ID编号 (0)   
4.GID：登录默认所在组ID编号 (0)  
5.GECOS：用户全名或注释  
&ensp;&ensp;&ensp;&ensp;如：chfn username 可以改注释信息：f全名，o办公室，p家电话，h办公室电话。  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;finger username 可以查看用户的详细注释信息  
6.home directory：用户家目录 (/root)  
7.shell：用户默认使用shell (/bin/bash)   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;shell：/sbin/nologin 不能登陆，一般给系统用户使用  
&ensp;&ensp;&ensp;&ensp;如：chsh -s /bin/bash username  给用户改shell类型  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;getent passwd = cat /etc/passwd       
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;getent passwd username 看某一用户的passwd信息（可多个用户）  

+ shadow文件格式（管理员才能查看，以：分割9个域）  
root&ensp;:$6$9wmi4ukKmdLtWX21$gDpGc4dBYTnBRxZjlyW/QQuFDis4ujbXYFrVK9PKCRvGFx.k13Hd989aUrOODbBcyY3Qvf8GCckq5pnQzG9eT1&ensp;:&ensp;:&ensp;0&ensp;:&ensp;99999&ensp;:&ensp;7&ensp;:&ensp;:&ensp;:   
**_备注：为方便看出9个域，冒号前额外加空格。_**  
&ensp;1.登录用户名 （root）  
&ensp;2.用户密码:$6指sha512加密  
&ensp;3.从1970年1月1日起到密码最近一次被更改的时间   
&ensp;4.密码再过几天可以被变更（0表示随时可被变更）  
&ensp;5.密码再过几天必须被变更（99999表示永不过期）  
&ensp;6.密码过期前几天系统提醒用户（默认为一周，7）  
&ensp;7.密码过期几天后帐号会被锁定账号有效期  
&ensp;8从1970年1月1日算起，多少天后帐号失效，账号有效期  
&ensp;9.预留字段，为将来使用  
&ensp;&ensp;&ensp;&ensp;&ensp;如：chage username : 改用户的口令信息：    
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;minimum passwd adg：最小口令有效期  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;maximum passwd age：最大口令有效期  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;last passwd change：最近一次密码更改的时间  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;passwd expiration waraning：密码过期前几天提醒   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;chage -d0 username  把last 该口令时间变成0  让用户立即过期  

+ 密码加密  
1.加密机制：  
&ensp;&ensp;&ensp;&ensp;加密：明文--> 密文  
&ensp;&ensp;&ensp;&ensp;解密：密文--> 明文  
2.md5: message digest, 128bits  
&ensp;&ensp;sha1: secure hash algorithm, 160bits  
&ensp;&ensp;sha224: 224bits  
&ensp;&ensp;sha256: 256bits  
&ensp;&ensp;sha384: 384bits  
&ensp;&ensp;sha512: 512bits   
&ensp;&ensp;位数越长，密码越安全。   
3.更改加密算法：  
&ensp;&ensp;authconfig --passalgo=sha256 --update  
4.密码的复杂性策略  
&ensp;&ensp;使用数字、大写字母、小写字母及特殊字符中至少3种  
&ensp;&ensp;足够长  
&ensp;&ensp;使用随机密码  
&ensp;&ensp;定期更换,不要使用最近曾经使用过的密码  

+ group文件格式(以：分割4个域)  
  &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;root:&ensp;x&ensp;:0:  
1.组名称：就是组名称  （root）  
2.组密码：通常不需要设定，密码是被记录在 /etc/gshadow （x）    
3.GID：就是组的ID   （0）  
4.以当前组为附加组的用户列表(分隔符为逗号)   

+ gshdow文件格式（以：分割4个域）  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;root:::  
1.组名称：（root）  
2.组密码：空密码  
3.组管理员列表：组管理员的列表，可更改组密码和成员  
4.以当前组为附加组的用户列表：(分隔符为逗号)  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;如：groupmems -a username -g groupname 把用户加到组  -d 移除  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;getent group groupname 看组的情况  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;gpasswd username/groupname  给组或者用户加口令  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;newgrp group 可以临时切换主组，退出后失效。除非附加在组里，否则需要知道密码  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;gpasswd -r groupname 删除组的口令（空，无！！无锁定）  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;gpasswd -a username groupname 把用户加到组里  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;gpasswd -d username groupname 把用户从组里删除  
&ensp;备注：group和gshadow 两个文件最后面的内容应该是一样的  

+ 文件操作  
vipw和vigr  有语法检查功能  
  vipw=vi /etc/passwd  
  vigr=vi /etc/group  
pwck和grpck  检查passwd和group的内容格式  

## 用户和组管理命令  

+ 用户创建：useradd  
1.语法：useradd [options] LOGIN    
2.选项：-u 指定UID  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-o 配合-u 选项，不检查UID的唯一性  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-g GID：指明用户所属组，可为组名，也可以GID  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-G GROUP1[,GROUP2,...]：为用户指明附加组，组须事先存在  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-c "COMMENT"：用户的注释信息  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-d HOME_DIR: 以指定的路径(不存在)为家目录  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-s SHELL: 指明用户的默认shell程序，可用列表在/etc/shells文件中  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-N 不创建私用组做主组，使用users组做主组  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-r: 创建系统用户，不生成家目录 CentOS 6: ID<500，CentOS 7: ID<1000  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-m 创建家目录，用于系统用户  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-M 不创建家目录，用于非系统用户  
3.创建用户： useradd username  没有口令，需要设置   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;默认值设定：/etc/default/useradd文件中     
4.显示或更改默认设置  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;useradd -D   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;useradd –D -s SHELL  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;useradd –D –b BASE_DIR  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;useradd –D –g GROUP  
5.批量处理  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;newusers passwd格式文件 批量创建用户  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;chpasswd 批量修改用户口令    

+ 用户属性修改：usermod  
1.语法：usermod [OPTION] login  
2.选项：-u UID: 新UID  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-g GID: 新主组  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-G GROUP1[,GROUP2,...[,GROUPN]]]：新附加组，原来的附加组将会被覆盖；  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-aG 若保留原有，则要同时使用-a选项  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-s SHELL：新的默认SHELL  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-c 'COMMENT'：新的注释信息  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-d HOME: 新家目录不会自动创建；若要创建新家目录并移动原家数据，同时使用-m选项
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-l login_name: 新的名字；  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-L: lock指定用户,在/etc/shadow 密码栏的增加 !   
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-U: unlock指定用户,将 /etc/shadow 密码栏的 ! 拿掉  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-e YYYY-MM-DD: 指明用户账号过期日期  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-f INACTIVE: 设定非活动期限   

+ 删除用户：userdel   
1.语法：userdel [OPTION]... login  
2.选项：-r: 删除用户家目录及邮箱信息   

+ 查看用户相关的ID信息：id  
1.语法：id [OPTION]... [USER]  
2.选项：-u: 显示UID  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-g: 显示GID  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-G: 显示用户所属的组的ID  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-n: 显示名称，需配合ugG使用   

+ 切换用户或以其他用户身份执行命令：su  
1.语法：su [options...] [-] [user [args...]]  
2.切换用户的方式：  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;su UserName：非登录式切换，即不会读取目标用户的配置文件，不改变
当前工作目录  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;su - UserName：登录式切换，会读取目标用户的配置文件，切换至家目录，完全切换  
3.root su至其他用户无须密码；非root用户切换时需要密码  
4.换个身份执行命令：su [-] UserName -c 'COMMAND'  
5.选项：-l --login  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;如：su -l UserName 相当于 su - UserName   

+ 设置密码：passwd   
1.语法：passwd [OPTIONS] UserName: 修改指定用户的密码  
2.常用选项：  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-d：删除指定用户密码  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-l：锁定指定用户  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-u：解锁指定用户  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-e：强制用户下次登录修改密码  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-f：强制操作  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-n mindays：指定最短使用期限  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-x maxdays：最大使用期限  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-w warndays：提前多少天开始警告  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-i inactivedays：非活动期限  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;--stdin：从标准输入接收用户密码  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;如：echo "PASSWORD" | passwd --stdin USERNAME   

+ 修改用户密码：chage  
1.语法：chage [OPTION]... LOGIN  
2.选项：-d LAST_DAY  修改最后一次更改密码的时间  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-E --expiredate EXPIRE_DATE  设置账户失效日期    
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-I --inactive INACTIVE 设置密码过期几天账号被锁定  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-m --mindays MIN_DAYS 设置密码最短使用时间  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-M --maxdays MAX_DAYS 设置密码最长使用时间  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-W --warndays WARN_DAYS 设置密码过期报警时间  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;–l 显示密码策略  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;如：chage -d 0 tom 下一次登录强制重设密码  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;chage -m 0 –M 42 –W 14 –I 7 tom Tom用户的密码可随时更改，42天到期后必须更改，到期前14天会提醒，到期后7天账号会被锁定，不能登录。  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;chage -E 2016-09-10 tom 设置Tom用户20160910失效   

+ 用户相关的其它命令  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;chfn 指定个人信息  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;chsh 指定shell  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;finger 查看个人信息  

+ 创建组：groupadd  
1.语法：groupadd [OPTION]... group_name  
2.选项：-g GID: 指明GID号；[GID_MIN, GID_MAX]  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-r: 创建系统组  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;CentOS 6: ID<500  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;CentOS 7: ID<1000   

+ 组属性修改：groupmod  
1.语法：groupmod [OPTION]... group  
2.选项：-n group_name: 新名字  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-g GID: 新的GID  

+ 组删除：groupdel （不能删除主组）  
语法：groupdel GROUP  

+ 组密码：gpasswd  
1.语法：gpasswd [OPTION] GROUP  
2.选项：-a user 将user添加至指定组中  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-d user 从指定组中移除用户user  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-A user1,user2,... 设置有管理权限的用户列表  

+ newgrp：临时切换主组  
如果用户本不属于此组，则需要组密码  

+ 更改和查看组成员：groupmems  
1.语法：groupmems [options] [action]  
2.options： -g, --group groupname 更改为指定组 (只有root)  
3.Actions:  &ensp;&ensp; -a, --add username 指定用户加入组  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-d, --delete username 从组中删除用户  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-p, --purge 从组中清除所有成员  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-l, --list 显示组成员列表  

+ groups  
语法：groups [OPTION].[USERNAME]... 查看用户所属组列表  

## 文件权限  

### 一、基础内容  
示例：-rw-r--r-- 1 root root 26195 Dec 17 10:42 install.log  
文件权限   &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;    -rw-r--r--   
计数  &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp; 1  
所有者  &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp; &ensp;&ensp;&ensp;&ensp;  root  
所属组  &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp; &ensp;&ensp;&ensp;&ensp;  root  
大小    &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp; &ensp;&ensp;&ensp;&ensp; &ensp; 26195  
创建时间  &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp; &ensp;&ensp;Dec 17 10:42  
文件名   &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;  install.log  
 
#### 1.三类对象  
文件的权限主要针对三类对象进行定义：  
owner: 所有者，用 u 表示  
group: 所属组，用 g 表示  
other: 其他，&ensp;&ensp;  用 o 表示 

#### 2.三种权限
每个文件针对每类访问者都定义了三种权限：  
r: Readable，读，一般应用于文本文件     
w: Writable，写，一般应用于文本文件  
x: eXcutable，执行，一般应用于二进制执行程序   
+ 对于文件：  
r: 可使用文件查看类工具获取其内容  
w: 可修改其内容  
x: 可以把此文件提请内核启动为一个进程  
+ 对于目录：  
r: 可以使用ls查看此目录中文件列表  
w: 可在此目录中创建文件，也可删除此目录中的文件  
x: 可以使用ls -l查看此目录中文件元数据（须配合r），可以cd进入此目录  
X:只给目录x权限，不给文件x权限（配合R）；若文件本身有执行权限，仍会+x权限。  

#### 3.文件权限操作  

| 文件权限 | 文件类型 | 读写执行 | 读写执行 | 读写执行 |  
|:-------:|:-------:|:--------:|:-------:|:--------:|  
|权限分配|        |文件所有者|文件所属组用户|其他用户|  
|字符表示(模式法)|d/l/c/s/p| r w x| r w x| r w x|    
|数字表示(数字法)|  |4 2 1| 4 2 1| 4 2 1|  

#### 4.模式法与数字法  

|权限类型|模式权限|二进制|数字权限|  
|:-----:|:----:|:-----:|:-----:|
|无权限|--- |000 |  0 | 
| 执行 |--x |001 |  1 | 
|  写  |-w- |010 |  2 | 
|写执行|-wx |011 |  3 | 
|  读  |r-- |100 |  4 |
|读执行|r-x |101 |  5 | 
| 读写 |rw- |110 |  6 | 
|读写执行|rwx |111 |  7 | 

如：640: rw-r-----  
&ensp;&ensp;&ensp;&ensp;755: rwxr-xr-x    

### 二、相关命令

+ 修改文件的属主：chown  
语:1：chown [OPTION]... [OWNER][:[GROUP]] FILE...  
用法：&ensp;&ensp;OWNER: &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;改主和组  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;OWNER:GROUP   改主和组  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;:GROUP   只改组  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;备注：命令中的冒号可用.替换  
选项：-R: 递归  
语法2：chown [OPTION]... --reference=RFILE FILE... 仿照文件改另一个文件  

  
+ 修改文件的属组：chgrp  
语法：chgrp [OPTION]... GROUP FILE...  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;chgrp [OPTION]... --reference=RFILE FILE...  
选项：-R 递归  

+ 修改文件权限：chmod    
语法1：chmod [OPTION]... OCTAL-MODE FILE...  
选项：-R: 递归修改权限  
语法2：chmod [OPTION]... MODE[,MODE]... FILE...  
MODE：修改一类用户的所有权限：u= g= o= ug= a= u=,g=  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;修改一类用户某位或某些位权限：u+ u- g+ g- o+ o- a+ a- + -  
语法3：chmod [OPTION]... --reference=RFILE FILE...  ：参考RFILE文件的权限，将FILE的修改为同RFILE  

+ 权限设置示例  
chgrp sales testfile ：把文件的所属组改为销售组    
chown root:admins testfile ：把文件的所有者改为root，所属组改为admins   
chmod u+wx,g-r,o=rx file ：把文件所有者加上写和执行，所属组去掉读权限，其他为读执行权限    
chmod -R g+rwX /testdir ：把目录以及子目录的所有组加上读写执行权限    
chmod 600 file ：给文件设置为600权限，只有所有者有读写权限  
chown mage testfile ：把文件的所有者改为mage  

### 三、umask  

+ umask值决定了新建文件和目录的默认权限。    
1.新建FILE权限: 666-umask  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;如果所得结果某位存在执行（奇数）权限，则将其权限+1；偶数则不变。  
2.新建DIR权限: 777-umask  
+ 普通用户umask是 0002  
+ 管理员root的umask 是 0022  
+   umask: 查看值  
umask #: 设定umask值  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;如：umask 022  
umask –S 模式方式显示  
umask –p 输出可被调用  
+ 全局设置： /etc/bashrc   
用户设置：~/.bashrc  

## 特殊文件权限

### 一、SUID  

+ 模式法用S表示（在所有者的执行权限位上显示，若本身有x，显示s;若本身没有x，显示S）。  
+ 数字法用4表示。  
+ SUID只对二进制可执行程序有效，设置在目录上无意义。  
+ 功能：当用户执行此文件时，会继承此文件所有者的权限。    
+ 权限设定：  
chmod u+s FILE...   
chmod u-s FILE...  

### 二、SGID  

+ 模式法用S表示（在所属组的执行权限位上显示，若本身有x，显示s;若本身没有x，显示S）。   
+ 数字法用2表示。  
+ SGID可设置在二进制可执行程序和目录上。
+ 功能：1.作用于二进制可执行的文件时，当用户执行此文件时，会继承此文件所属组的权限  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp; 
2.作用于目录上,默认情况下，用户创建文件时，其属组为此用户所属的主组。一旦某目录被设定了SGID，则对此目录有写权限的用户在此目录中创建的文件所属的组为此目录的属组。  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;通常用于创建一个协作目录   
+ 权限设定：文件  
chmod g+s FILE...   
chmod g-s FILE...   
权限设定：目录    
chmod g+s DIR...  
chmod g-s DIR...  

### 三、Sticky

+ 模式法用t表示（在other的执行权限位上显示，若本身有x，显示t;若本身没有x，显示T）。  。  
+ 数字法用1表示。   
+ 功能：具有写权限的目录通常用户可以删除该目录中的任何文件，无论该文件的权限或拥有权。在目录设置Sticky 位，只有文件的所有者或root可以删除该文件  
+ sticky 设置在文件上无意义  
+ 权限设定：  
chmod o+t DIR...  
chmod o-t DIR...  
 
 ### 四、特定属性设置  
 
设定文件特定属性，避免root误操作  
+ chattr +i 不能删除，改名，更改，其文件所在的目录也不可删除  
+ chattr +a 只能追加内容  
+ lsattr 显示特定属性  

## 访问控制列表
+ ACL：Access Control List，实现灵活的权限管理  
+ 功能：除了文件的所有者，所属组和其它人，可以对更多的用户设置权限。  
CentOS7 默认创建的xfs和ext4文件系统具有ACL功能。  
CentOS7 之前版本，默认手工创建的ext4文件系统无ACL功能,需手动增加（装好系统后人为分配分区）。  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;tune2fs –o acl /dev/sdb1  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;mount –o acl /dev/sdb1 /mnt/test    

+ 设置文件的ACL权限：setfacl  
语法：setfacl [-bkndRLP] { -m|-M|-x|-X ... } file ...  
选项：  -m， - modify = acl修改文件的当前ACL  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-M， - modify-file = file读取要从文件修改的ACL条目  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-x， - remove = acl从文件的ACL中删除条目  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-X， - remove-file = file读取要从文件中删除的ACL条目  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-b， - remove-all删除所有扩展ACL条目  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-n， - no-mask不重新计算有效权限掩码  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-d， - default操作适用于默认ACL，作用于目录，该目录内新建的文件默认带有ACL权限  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-k， - remove-default删除默认ACL  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;--set = acl设置文件的ACL，替换当前的ACL  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;--set-file = file读取要从文件设置的ACL条目  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;--mask会重新计算有效的权限掩码  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-R， - 递归递归到子目录  

+ getfacl filename :查看该文件的acl权限  
+ 示例：  
setfacl -m u:wang:rwx f1 设置wang对f1有读写执行的权限  
setfacl -m u:wang:0 f1 设置wang对f1没有任何权限   
setfacl -m -g:webs:rw f1 设置webs组对f1有读写权限  
setfacl -x u:wang f1  
setfacl -x g:webs f1  
getfacl file1 | setfacl --set-file=- file2 复制file1的acl权限给file2  
setfacl --set u::rw,u:wang:rw,g::r,o::- file1 （--set选项会把原有的ACL项都删除，用新的替代，需要注意的是一定要包含UGO的设置，不能象-m一样只是添加ACL就可以）  
setfacl -b f1 把f1的所有ACL权限都删除，清空  
+ ACL生效顺序：所有者，自定义用户，自定义组，其他人。  
当一个成员属于多个组的时候，成员的ACL权限是多个组的累加权限。  


+ ACL文件上显示的group权限是mask 值（自定义用户，自定义组，拥有组的最大权限）,而非传统的组权限。但执行时按照实际的组权限执行。  
mask只影响除所有者和other的之外的人和组的最大权限。  
Mask需要与用户的权限进行逻辑与运算后，才能变成有限的权限(Effective Permission)；用户或组的设置必须存在于mask权限设定范围内才会生效   
如：setfacl -m mask::rx file  对文件设置mask最大为rx权限，若自定义用户对文件有rwx权限，有效的权限为rx。  
+ 备份和恢复ACL  
主要的文件操作命令cp和mv都支持ACL，只是cp命令需要加上-p 参数。但是tar等常见的备份工具是不会保留目录和文件的ACL信息  
如：getfacl -R /tmp/dir1 > acl.txt  备份目录和子文件的ACL权限  
&ensp;&ensp;&ensp;&ensp;setfacl -R -b /tmp/dir1  清空目录下所有的ACL权限  
&ensp;&ensp;&ensp;&ensp;setfacl -R --set-file=acl.txt /tmp/dir1   还原ACL权限  
&ensp;&ensp;&ensp;&ensp;setfacl --restore acl.txt（还原，要求文件还来原来的位置）  
&ensp;&ensp;&ensp;&ensp;getfacl -R /tmp/dir1   查看是否还原  
