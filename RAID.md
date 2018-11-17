## RAID  
### 简介    
RAID：Redundant Arrays of Inexpensive（Independent） Disks，有“独立磁盘构成的具有冗余能力的阵列”之意。加利福尼亚大学伯克利分校在1988年，发表的文章：“A Case for Redundant Arrays of Inexpensive Disks”。文章中，谈到了RAID这个词汇，而且定义了RAID的5层级。        
简单来说是多个磁盘合成一个“阵列”，来提供更好的性能、冗余。早期应用为廉价的磁盘，后期随着发展意为独立的磁盘。     
### 优点  
磁盘可并行读写，提高IO能力；  
磁盘冗余，提高耐用性。  
### 分类  
外接式磁盘阵列：通过扩展卡提供适配能力  
内接式RAID：主板集成RAID控制器，安装OS前在BIOS里配置  
软件RAID：通过软件方式实现；生产中广泛应用硬RAID    
### 级别  
+ RAID-0  
以位或字节为单位连续分割数据，并行读/写于多个磁盘上  
读写性能提升（理论上N个硬盘提升N倍，实际会低）  
最少由2个硬盘组成   
磁盘利用率100%  
无容错能力，任何一块硬盘出现故障，所有数据损坏   
![RAID 0:无容错的数据条带](https://gss2.bdstatic.com/9fo3dSag_xI4khGkpoWK1HF6hhy/baike/c0%3Dbaike80%2C5%2C5%2C80%2C26/sign=847812b8b9389b502cf2e800e45c8eb8/ac4bd11373f08202e80699994bfbfbedaa641bfb.jpg)  
+ RAID-1  
又称镜像，将数据完全一致地分别写到g工作磁盘和镜像磁盘  
读性能提升（系统繁忙时可直接拷贝镜像），写性能略有下降   
最少由2个硬盘组成  
磁盘利用率为50%   
有冗余能力,只允许每组的两个磁盘之一损坏  
![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1542450148000&di=4da40e7a07db7f32bdcc883e8afd7eec&imgtype=0&src=http%3A%2F%2Fimg1.ph.126.net%2FDf_wqrfQUtDCPigVWwPDZw%3D%3D%2F6598168178772822111.jpg)

+ RAID-5  
在所有磁盘上交叉地存取数据及奇偶校验信息  
读写性能提升  
最少由3个硬盘组成
磁盘利用率为n-1/n   
有容错能力，允许最多1个磁盘损坏    
![](https://gss2.bdstatic.com/9fo3dSag_xI4khGkpoWK1HF6hhy/baike/c0%3Dbaike92%2C5%2C5%2C92%2C30/sign=71a5484ab74543a9e116f29e7f7ee1e7/5243fbf2b211931372c2690e65380cd790238d8f.jpg)   
+ RAID-6  
与RAID 5相比，RAID 6增加了第二个独立的奇偶校验信息块。两个独立的奇偶系统使用不同的算法，数据的可靠性非常高，即使两块磁盘同时失效也不会影响数据的使用  
读写性能提升  
最少由4个硬盘组成  
磁盘利用率为n-2/n  
有容错能力，允许最多2个磁盘损坏  
![](https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=3636853386,1150207581&fm=26&gp=0.jpg)  
+ RAID-10  
RAID 1+0是先镜像再分区数据，再将所有硬盘分为两组，视为是RAID-0的最低组合，然后将这两组各自视为RAID-1运作  
读写性能提升  
最少由4个硬盘组成  
有容错能力，每组镜像最多只能损坏1个    
![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1542449993554&di=33e522e937cc14b81df39a00812a9f37&imgtype=0&src=http%3A%2F%2Faliyunzixunbucket.oss-cn-beijing.aliyuncs.com%2Fjpg%2F593aaf8432611178153c8ebc974c48e5.jpg%3Fx-oss-process%3Dimage%2Fresize%2Cp_100%2Fauto-orient%2C1%2Fquality%2Cq_90%2Fformat%2Cjpg%2Fwatermark%2Cimage_eXVuY2VzaGk%3D%2Ct_100)

+ RAID-01  
RAID 0+1则是跟RAID 1+0的程序相反，是先分区再将数据镜像到两组硬盘。它将所有的硬盘分为两组，变成RAID-1的最低组合，而将两组硬盘各自视为RAID-0运作   
读写性能提升  
最少由4个硬盘组成  
有容错能力,最多坏一组RAID-0   
![](https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=3336949431,1051474642&fm=26&gp=0.jpg)

+ RAID-50
RAID50是RAID5与RAID0的结合。  
此配置在RAID5的子磁盘组的每个磁盘上进行包括奇偶信息在内的数据的剥离。每个RAID5子磁盘组要求三个硬盘。RAID50具备更高的容错能力，因为它允许某个组内有一个磁盘出现故障，而不会造成数据丢失。   
![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1542450053385&di=2acc0588103c41ab5e750c7189360a98&imgtype=0&src=http%3A%2F%2Fimage.mamicode.com%2Finfo%2F201801%2F20180130214444897366.jpg)

### 软RAID的实现  
实现工具：mdadm  
mdadm是一个用于创建、管理、监控RAID设备的工具，它使用linux中的md驱动；mdadm程序是一个独立的程序，能完成所有软件RAID的管理功能，主要有7中使用模式。   
+ 模式：  
create：使用空闲的设备创建一个新的阵列，每个设备具有元数据块（超级块）  
assemble：将以前定义的某个阵列加入当前在用阵列  
build：创建或组装不需要元数据的阵列，每个设备没有元数据块   
manage：管理已存在阵列，比如add或remove  
misc：报告或者修改阵列中相关设备的信息，比如查询阵列或者设备的状态信息  
grow：改变阵列中每个设备被使用的容量或阵列中的设备的数目，改变阵列属性（不能改变阵列的级别）   
monitor：监控一个或多个阵列  
+ 选项：  
-C：创建一个新的阵列  
-F：监控模式   
-D：显示RAID设备的详细信息  
+ create模式选项：  
-n：创建RAID的设备个数  
-l：指定创建的RAID级别  
-a {yes|no}：自动创建目标RAID设备的设备文件  
-x：指定空闲硬盘的个数   
-c：指定chunk的大小，单位k  
+ 管理模式选项：  
-f：标记指定磁盘为损坏  
-a：添加硬盘  
-r：移除硬盘   
-R：强制启动  
-S：停止设备  
-A：启用设备   
--zero-superblock：删除raid信息  
+ 相关文件：  
/proc/mdstat：当前软RAID的状态信息  
/etc/mdadm.conf：mdadm的配置文件    
+ 配置示例：  
使用mdadm创建并定义RAID设备：  
mdadm -C /dev/md0 -a yes -l 5 -n 3 -x 1 /dev/sd{b,c,d,e}1  
格式化，创建文件系统：  
mkfs.xfs /dev/md0  
查看RAID设备详情：  
mdadm -D /dev/md0  
增加新的成员：  
mdadm -G /dev/md0 -a /dev/sdf1  
模拟硬盘故障：  
mdadm /dev/md0 -f /dev/sda1  
移除硬盘：  
mdadm /dev/md0 -r /dev/sda1   
保存配置文件：  
mdadm -D -s /etc/mdadm.conf  












