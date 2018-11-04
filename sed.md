## 文本处理三剑客之SED

### 目录  
1. Sed介绍  
2. Sed用法  
3. Sed高级用法  

### sed介绍    
+ Stream EDitor, 行编辑器  
+ &ensp;&ensp;&ensp;&ensp;sed是一种流（行）编辑器，它一次处理一行内容。处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space），接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。然后读入下行，执行下一个循环。  
&ensp;&ensp;&ensp;&ensp;如果没有使诸如‘D’的特殊命令，那会在两个循环之间清空模式空间，但不会清空保留空间。这样不断重复，直到文件末尾。文件内容并没有改变，除非你使用重定向存储输出。  
+ 功能：主要用来自动编辑一个或多个文件,简化对文件的反复操作,编写转换程序等。  
+ 参考： http://www.gnu.org/software/sed/manual/sed.html   官方文档说明手册  

### sed用法  
#### 用法：sed [ option ]... 'script' inputfile...  
+ 常用选项：  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-n 不输出模式空间内容到屏幕，即不自动打印  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-e 多点编辑  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-f /PATH/SCRIPT_FILE 从指定文件中读取编辑脚本  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-r 支持使用扩展正则表达式  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-i.bak 备份文件并原处编辑，可以直接修改文件  

+ script: '地址命令'  
地址定界：单引号之内 ‘ ’  
(1) 不给地址：对全文进行处理  
(2) 单地址：  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;#: 指定的行，第几行  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;$：最后一行  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;/pattern/：被此处模式所能够匹配到的每一行  
(3) 地址范围：  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;#,#:指定第几行到第几行        
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;#,+#：指定第#行到#+#行  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;/pat1/,/pat2/：从part1匹配到的行到part2匹配到的行  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;#,/pat1/：从第#行到part匹配到的行  
(4) ~：步进  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;1~2 奇数行  从第1行开始，递增2，就是奇数行  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;2~2 偶数行  从第2行开始，递增2，就是偶数行  

+ 编辑命令：  ‘地址+命令’  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;d 删除模式空间匹配的行，并立即启用下一轮循环  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;p 打印当前模式空间内容，追加到默认输出之后  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;a [\\]text 在指定行后面追加文本，支持使用\n实现多行追加 地址可以指定行  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;i [\\]text 在行前面插入文本   \分界，可加空格  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;c [\\]text 替换行为单行或多行文本  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;w /path/file 保存模式匹配的行至指定文件  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;r /path/file 读取指定文件的文本至模式空间中匹配到的行后  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;= 为模式空间中的行打印行号 匹配模式的行号  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;! 模式空间中匹配行取反处理  

+ 查找替换
s/// 查找替换,支持使用其它分隔符，s@@@，s###  
替换标记：  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;g 行内全局替换  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;p 显示替换成功的行  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;w /PATH/FILE 将替换成功的行保存至文件中  


+ sed示例  
sed ‘2p’ /etc/passwd       第二行重复两遍    
sed –n ‘2p’ /etc/passwd   只显示第二行  
sed –n ‘1,4p’ /etc/passwd   只显示1-4行  
sed –n ‘/root/p’ /etc/passwd  只显示包括root的行  
sed –n ‘2,/root/p’ /etc/passwd  从2行开始，到包含root的行  
sed -n ‘/^$/=’ file 显示空行行号  
sed –n –e ‘/^$/p’ –e ‘/^$/=’ file  打印空行和空行行号  
sed ‘/root/a\superman’ /etc/passwd行后追加  
sed ‘/root/i\superman’ /etc/passwd 行前插入  
sed ‘/root/c\superman’ /etc/passwd 代替行  
sed ‘/^$/d’ file  删除空行  
sed ‘1,10d’ file  删除文件中第一到第十行      


### sed高级用法  
+ P： 打印模式空间开端至\n内容，并追加到默认输出之前  
+ h: 把模式空间中的内容覆盖至保持空间中  
+ H：把模式空间中的内容追加至保持空间中  
+ g: 从保持空间取出数据覆盖至模式空间  
+ G：从保持空间取出内容追加至模式空间  
+ x: 把模式空间中的内容与保持空间中的内容进行互换  
+ n: 读取匹配到的行的下一行覆盖至模式空间  
+ N：读取匹配到的行的下一行追加至模式空间  
+ d: 删除模式空间中的行  
+ D：如果模式空间包含换行符，则删除直到第一个换行符的模式空间中的文本，并不会读取新的输入行，而使用合成的模式空间重新启动循环。如果模式空间不包含换行符，则会像发出d命令那样启动正常的新循环  

+ sed高级用法示例  
sed -n 'n;p' FILE          打印偶数行  
sed '1!G;h;$!d' FILE       倒序 
