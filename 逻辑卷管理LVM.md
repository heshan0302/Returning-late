## 逻辑卷管理器  
### 简介   
LVM(Logical Volume Manager)逻辑卷管理是在Linux2.4内核以上实现的磁盘管理技术。它是Linux环境下对磁盘分区进行管理的一种机制，LVM是建立在硬盘和 分区之上的一个逻辑层，来提高磁盘分区管理的灵活性。通过LVM系统管理员可以轻松管理磁盘分区，如：将若干个磁盘分区连接为一个整块的卷组 （volume group），形成一个存储池。管理员可以在卷组上随意创建逻辑卷组（logical volumes），并进一步在逻辑卷组上创建文件系 统。管理员通过LVM可以方便的调整存储卷组的大小，并且可以对磁盘存储按照组的方式进行命名、管理和分配，例如按照使用用途进行定义：“development”和“sales”，而不是使用物理磁盘名“sda”和“sdb”。而且当系统添加了新的磁盘，通过LVM管理员就不必将磁盘的 文件移动到新的磁盘上以充分利用新的存储空间，而是直接扩展文件系统跨越磁盘即可。  
  
### 基本术语  
1. 物理存储介质（PhysicalStorageMedia）  
指系统的物理存储设备：磁盘，如：/dev/hda、/dev/sda等，是存储系统最底层的存储单元。  
2. 物理卷（Physical Volume，PV）  
指磁盘分区或从逻辑上与磁盘分区具有同样功能的设备（如RAID），是LVM的基本存储逻辑块，但和基本的物理存储介质（如分区、磁盘等）比较，却包含有与LVM相关的管理参数。  
3. 卷组（Volume Group，VG）  
类似于非LVM系统中的物理磁盘，其由一个或多个物理卷PV组成。可以在卷组上创建一个或多个LV（逻辑卷）。  
4. 逻辑卷（Logical Volume，LV）  
类似于非LVM系统中的磁盘分区，逻辑卷建立在卷组VG之上。在逻辑卷LV之上可以建立文件系统（比如/home或者/usr等）。  
5. 物理块（Physical Extent，PE）  
每一个物理卷PV被划分为称为PE（Physical Extents）的基本单元，具有唯一编号的PE是可以被LVM寻址的最小单元。PE的大小是可配置的，默认为4MB。所以物理卷（PV）由大小等同的基本单元PE组成。  
6. 逻辑块（Logical Extent，LE）  
逻辑卷LV也被划分为可被寻址的基本单位，称为LE。在同一个卷组中，LE的大小和PE是相同的，并且一一对应。  

### 工作流程图   
+ 将设备指定为物理卷  
+ 用一个或者多个物理卷来创建一个卷组  
+ 在卷组上创建逻辑卷  
+ 在逻辑卷上创建文件系统   

![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1542382221089&di=41658a23ccfa545e0e42ff944a9eb0ac&imgtype=0&src=http%3A%2F%2Fa1.att.hudong.com%2F45%2F30%2F01300000089793120686306318932.jpg)   
### 相关命令  
1. pv管理工具  
+ pvs:显示物理卷简要信息  
+ pvdisplay:显示物理卷的详细信息  
+ pvcreate:创建物理卷  
+ pvremove:删除物理卷  
+ pvchange:修改物理卷属性  
+ pvscan:扫描磁盘上的物理卷   
+ pvrename:更改物理卷的名字   
+ pvmove:迁移PE    
2. vg管理工具  
+ vgs:显示卷组简要信息  
+ vgdisplay:显示卷组详细信息  
+ vgcreate:创建卷组  
+ vgremove:删除卷组  
+ vgextend:扩展卷组  
+ vgreduce:压缩卷组  
+ vgchange:改变卷组属性  
+ vgscan:扫描磁盘上的卷组  
+ vgrename:更改卷组的名字  
+ vgexport:导出卷组    
+ vgimport:导入卷组  
+ vgmerge:合并卷组   
+ vgsplit:拆分卷组      
3. lv管理工具  
+ lvs:显示逻辑卷的简要信息  
+ lvdisplay:显示逻辑卷的详情信息  
+ lvcreate:创建逻辑卷  
+ lvremove:删除逻辑卷  
+ lvextend:扩展逻辑卷  
+ lvreduce:缩减逻辑卷  
+ lvchange:改变逻辑卷的属性  
+ lvscan:扫描磁盘上的逻辑卷  
+ lvrename:更改逻辑卷的名字    
4. 其他  
+ resize2fs:同步文件系统     

### 实验示例  
1. 查看分区信息，准备两个磁盘或分区（类型8e）。以/dev/sda6和/dev/sdb为例创建逻辑卷，并测试读写能力。    
+ 先查看     
```bash
[root@centos6 ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sr0     11:0    1  3.7G  0 rom  /media/CentOS_6.10_Final
sda      8:0    0  200G  0 disk 
├─sda1   8:1    0    1G  0 part /boot
├─sda2   8:2    0 48.8G  0 part /
├─sda3   8:3    0 29.3G  0 part /data
├─sda4   8:4    0    1K  0 part 
└─sda5   8:5    0    2G  0 part [SWAP]
sdb      8:16   0   20G  0 disk 
[root@centos6 ~]#  
```  
+ 将硬盘/dev/sda分区，为/dev/sda6，大小10G，分区类型为8e（Linux LVM）；若为整个硬盘，则不需要修改类型     
```bash
[root@centos6 ~]# fdisk /dev/sda

WARNING: DOS-compatible mode is deprecated. It’s strongly recommended to
         switch off the mode (command 'c') and change display units to
         sectors (command 'u').

Command (m for help): n
First cylinder (10591-26109, default 10591): 
Using default value 10591
Last cylinder, +cylinders or +size{K,M,G} (10591-26109, default 26109): +10G

Command (m for help): t
Partition number (1-6): 6 
Hex code (type L to list codes): 8e
Changed system type of partition 6 to 8e (Linux LVM)

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
[root@centos6 ~]#
```
+ 同步分区（虽然显示已经同步，可再次操作确保生效）  
```bash
[root@centos6 ~]# partx -a /dev/sda
BLKPG: Device or resource busy
error adding partition 1
BLKPG: Device or resource busy
error adding partition 2
BLKPG: Device or resource busy
error adding partition 3
BLKPG: Device or resource busy
error adding partition 4
BLKPG: Device or resource busy
error adding partition 5
[root@centos6 ~]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sr0     11:0    1  3.7G  0 rom  /media/CentOS_6.10_Final
sda      8:0    0  200G  0 disk 
├─sda1   8:1    0    1G  0 part /boot
├─sda2   8:2    0 48.8G  0 part /
├─sda3   8:3    0 29.3G  0 part /data
├─sda4   8:4    0    1K  0 part 
├─sda5   8:5    0    2G  0 part [SWAP]
└─sda6   8:6    0   10G  0 part 
sdb      8:16   0   20G  0 disk 
[root@centos6 ~]#
```
备注：centos7中同步分区命令：partprobe /dev/sda  
+ 将/dev/sda6和/dev/sdb变为物理卷pv  
```bash
[root@centos6 ~]# pvcreate /dev/sd{a6,b}
  Physical volume "/dev/sda6" successfully created
  Physical volume "/dev/sdb" successfully created
```
+ 查看创建的物理卷信息   
pvs: 简要信息   
pvdisplay: 详细信息   

```bash
[root@centos6 ~]# pvs
  PV         VG   Fmt  Attr PSize  PFree 
  /dev/sda6       lvm2 ---- 10.00g 10.00g
  /dev/sdb        lvm2 ---- 20.00g 20.00g
[root@centos6 ~]# pvdisplay
  "/dev/sda6" is a new physical volume of "10.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sda6
  VG Name               
  PV Size               10.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               ijc10B-hK3u-VDEx-mLNu-KF1n-byNe-ptZoec
   
  "/dev/sdb" is a new physical volume of "20.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb
  VG Name               
  PV Size               20.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               AkCYCW-koFG-Y4Sr-MgIF-UvhD-Fe0d-eoTBvL
   
[root@centos6 ~]#
```
+ 创建卷组，并把/dev/sd{a6,b}加到卷组里  
```bash
[root@centos6 ~]# vgcreate vg0 /dev/sd{a6,b}
  Volume group "vg0" successfully created
[root@centos6 ~]#
```
+ 查看创建的卷组信息  
vgs:简要信息  
vgdisplay:详细信息  
```bash
[root@centos6 ~]#vgs
  VG   #PV #LV #SN Attr   VSize  VFree 
  vg0    2   0   0 wz--n- 29.99g 29.99g
[root@centos6 ~]#vgdisplay
  --- Volume group ---
  VG Name               vg0
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               29.99 GiB
  PE Size               4.00 MiB
  Total PE              7678
  Alloc PE / Size       0 / 0   
  Free  PE / Size       7678 / 29.99 GiB
  VG UUID               ArM0fz-ef1x-aCZ7-8Bpu-40sp-G3EN-4Ik3pR
   
[root@centos6 ~]#
```
+ 创建逻辑卷    
lvcreate:创建逻辑卷  
常用选项：-n:指定逻辑卷的名字  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-l:指定逻辑卷的大小，以PE数；或以%{VG|PVS|FREE}形式指定  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-L:指定逻辑卷的大小，单位为KMGT等  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;-s:创建快照  
```bash
[root@centos6 ~]# lvcreate -n lv_ss -L 15G vg0
  Logical volume "lv_ss" created.
[root@centos6 ~]#
```
+ 查看逻辑卷信息  
lvs:简要信息    
lvdisplay:详情信息   
在逻辑卷中，LE对应着物理卷中的PE，分配的最小单位
```bash
 [root@centos6 ~]# lvs
  LV    VG   Attr       LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lv_ss vg0  -wi-a----- 15.00g                                                    
[root@centos6 ~]# lvdisplay
  --- Logical volume ---
  LV Path                /dev/vg0/lv_ss
  LV Name                lv_ss
  VG Name                vg0
  LV UUID                12sW8v-3do4-VfwO-F3j5-Op5O-UORq-Ht0b6j
  LV Write Access        read/write
  LV Creation host, time centos6.localdomain, 2018-11-17 11:08:57 +0800
  LV Status              available
  # open                 0
  LV Size                15.00 GiB
  Current LE             3840
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0
   
[root@centos6 ~]#
```
备注： 
逻辑卷的名字/dev/vg0/lv_ss为软链接，逻辑卷的设备名为/dev/dm-#  
```bsah
[root@centos6 ~]#ll /dev/vg0/lv_ss
lrwxrwxrwx. 1 root root 7 Nov 17 11:08 /dev/vg0/lv_ss -> ../dm-0
[root@centos6 ~]#ll /dev/mapper/vg0-lv_ss 
lrwxrwxrwx. 1 root root 7 Nov 17 11:08 /dev/mapper/vg0-lv_ss -> ../dm-0
[root@centos6 ~]#
```
+ 给逻辑卷创建文件系统  
```bash
[root@centos6 ~]# mkfs.ext4 /dev/vg0/lv_ss 
mke2fs 1.41.12 (17-May-2010)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
983040 inodes, 3932160 blocks
196608 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=4026531840
120 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208

Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done

This filesystem will be automatically checked every 39 mounts or
180 days, whichever comes first.  Use tune2fs -c or -i to override.
[root@centos6 ~]# blkid
/dev/sda1: UUID="0727938b-e4f0-4456-a9b6-3ed497ba7236" TYPE="ext4" 
/dev/sda2: UUID="2a9267f3-e012-4de1-84a0-0b7f2cc7039d" TYPE="ext4" 
/dev/sda3: UUID="a9b5f593-18e4-4e79-91a5-ccbc6e9bc705" TYPE="ext4" 
/dev/sda5: UUID="48bb56a1-f227-4232-aeef-5ecff7bbf782" TYPE="swap" 
/dev/sda6: UUID="ijc10B-hK3u-VDEx-mLNu-KF1n-byNe-ptZoec" TYPE="LVM2_member" 
/dev/mapper/vg0-lv_ss: UUID="067a2514-6c82-4ea1-89d0-016a098bbbd2" TYPE="ext4" 
/dev/sdb: UUID="AkCYCW-koFG-Y4Sr-MgIF-UvhD-Fe0d-eoTBvL" TYPE="LVM2_member" 
[root@centos6 ~]#
```
+ 挂载  
```bash
[root@centos6 ~]# mkdir /mnt/ss
[root@centos6 ~]# mount /dev/vg0/lv_ss  /mnt/ss
[root@centos6 ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/sda2              48G  4.4G   42G  10% /
tmpfs                 490M   80K  490M   1% /dev/shm
/dev/sda1             976M   34M  892M   4% /boot
/dev/sda3              29G   44M   28G   1% /data
/dev/sr0              3.8G  3.8G     0 100% /media/CentOS_6.10_Final
/dev/mapper/vg0-lv_ss
                       15G   38M   14G   1% /mnt/ss
[root@centos6 ~]#
```
+ 测试逻辑卷的读写性能  
```bash
[root@centos6 ~]# dd if=/dev/zero of=/mnt/ss/f2 bs=1M count=1024
1024+0 records in
1024+0 records out
1073741824 bytes (1.1 GB) copied, 1.56219 s, 687 MB/s
```
```bash
[root@centos6 ~]# dd if=/mnt/ss/f2 of=/dev/null 
2097152+0 records in
2097152+0 records out
1073741824 bytes (1.1 GB) copied, 3.39986 s, 316 MB/s
```
2. 卷组有空余时，扩展逻辑卷  
+ 先将卷组剩余空间扩展到逻辑卷中(可在线扩展，不需要取消挂载)  
```bash
 [root@centos6 ~]#lvextend -l +100%FREE /dev/vg0/lv_ss 
  Size of logical volume vg0/lv_ss changed from 15.00 GiB (3840 extents) to 29.99 GiB (7678 extents).
  Logical volume lv_ss successfully resized.
[root@centos6 ~]#
```
+ 同步文件系统  
```bash
[root@centos6 ~]#resize2fs /dev/vg0/lv_ss 
resize2fs 1.41.12 (17-May-2010)
Filesystem at /dev/vg0/lv_ss is mounted on /mnt/ss; on-line resizing required
old desc_blocks = 1, new_desc_blocks = 2
Performing an on-line resize of /dev/vg0/lv_ss to 7862272 (4k) blocks.
The filesystem on /dev/vg0/lv_ss is now 7862272 blocks long.
```
备注：  
resize2fs只适用于centos6，且后面直接加设备名即可；    
centos7中需要用xfs_growfs，且后面需要加挂载点。   
适合两个系统的命令：lvextend -r -l +100%FREE /dev/vg0/lv_ss,扩展和同步   

3. 卷组没有空余时，先扩展卷组，在扩展逻辑卷  
+ 增加一块硬盘/dev/sdc  
```bash
[root@centos6 ~]# echo "- - -" > /sys/class/scsi_host/host2/scan
[root@centos6 ~]# lsblk
NAME                 MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sr0                   11:0    1  3.7G  0 rom  /media/CentOS_6.10_Final
sda                    8:0    0  200G  0 disk 
├─sda1                 8:1    0    1G  0 part /boot
├─sda2                 8:2    0 48.8G  0 part /
├─sda3                 8:3    0 29.3G  0 part /data
├─sda4                 8:4    0    1K  0 part 
├─sda5                 8:5    0    2G  0 part [SWAP]
└─sda6                 8:6    0   10G  0 part 
  └─vg0-lv_ss (dm-0) 253:0    0   30G  0 lvm  /mnt/ss
sdb                    8:16   0   20G  0 disk 
└─vg0-lv_ss (dm-0)   253:0    0   30G  0 lvm  /mnt/ss
sdc                    8:32   0    5G  0 disk 
[root@centos6 ~]#
```
+ 创建物理卷  
```bash
[root@centos6 ~]# pvcreate /dev/sdc
  Physical volume "/dev/sdc" successfully created
[root@centos6 ~]#
```
+ 扩展卷组  
```bash
[root@centos6 ~]# vgextend vg0 /dev/sdc
  Volume group "vg0" successfully extended
[root@centos6 ~]#
```
+ 在执行扩展逻辑卷操作即可  
4. 缩减逻辑卷  
+ 取消挂载  
```bash
[root@centos6 ~]# umount /mnt/ss
[root@centos6 ~]#
```
+ 缩减文件系统（需先检查文件系统完整性）  
```bash
[root@centos6 ~]# resize2fs /dev/vg0/lv_ss 20G
resize2fs 1.41.12 (17-May-2010)
Please run 'e2fsck -f /dev/vg0/lv_ss' first.
[root@centos6 ~]# e2fsck -f /dev/vg0/lv_ss
e2fsck 1.41.12 (17-May-2010)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/vg0/lv_ss: 11/1966080 files (0.0% non-contiguous), 166705/7862272 blocks
[root@centos6 ~]# resize2fs /dev/vg0/lv_ss 20G
resize2fs 1.41.12 (17-May-2010)
Resizing the filesystem on /dev/vg0/lv_ss to 5242880 (4k) blocks.
The filesystem on /dev/vg0/lv_ss is now 5242880 blocks long.
[root@centos6 ~]#
```
+ 缩减逻辑卷  
```bash
[root@centos6 ~]# lvreduce -L 20G /dev/vg0/lv_ss 
  WARNING: Reducing active logical volume to 20.00 GiB.
  THIS MAY DESTROY YOUR DATA (filesystem etc.)
Do you really want to reduce vg0/lv_ss? [y/n]: y
  Size of logical volume vg0/lv_ss changed from 29.99 GiB (7678 extents) to 20.00 GiB (5120 extents).
  Logical volume lv_ss successfully resized.
[root@centos6 ~]#
```
备注：缩减逻辑卷，可能会数据丢失，可先做备份。  
+ 挂载  
```bash
[root@centos6 ~]# mount /dev/vg0/lv_ss  /mnt/ss
[root@centos6 ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/sda2              48G  4.4G   42G  10% /
tmpfs                 490M   80K  490M   1% /dev/shm
/dev/sda1             976M   34M  892M   4% /boot
/dev/sda3              29G   44M   28G   1% /data
/dev/sr0              3.8G  3.8G     0 100% /media/CentOS_6.10_Final
/dev/mapper/vg0-lv_ss
                       20G   42M   19G   1% /mnt/ss
[root@centos6 ~]#
```
5. 跨主机迁移卷组  
+ 源计算机上，取消挂载所有卷组上的逻辑卷
+ 源计算机上，禁用卷组  
+ 源计算机上，导出卷组  
+ 拆旧硬盘，装到目标主机上   
+ 目标计算机上，导入卷组，并激活启用，挂载即可  


### 逻辑卷管理器快照  
1. 简介  
快照是特殊的逻辑卷，将当时的系统信息记录下来，就好像照相一般，若将来有任何数据改动了，则原始数据会被移动到快照区，没有改动的区域则由快照区和文件
系统共享。  
由于快照区与原本的LV共用很多PE的区块，因此快照与被快照的LV必须在同
一个VG中.系统恢复的时候的文件数量不能高于快照区的实际容量。  

2. 应用  
+ 创建快照  
lvcreate -n /dev/vg0/lv_ss_snap -s -p r -L 1G /dev/vg0/lv_ss  
给lv_ss逻辑卷做快照，名字为lv_ss_snap，只读，大小为1G  
+ 挂载快照  
mkdir /mnt/snap  
mount -o ro /dev/vg0/lv_ss_snap /mnt/snap    
备注：  
centos7中挂载，需要加选项-o nouuid，因为快照和逻辑卷UUID相同  
+ 恢复快照  
umount /mnt/ss  
umount /mnt/snap  
lvconvert --merge /dev/vg0/lv_ss_snap  
+ 删除快照（先取消挂载）  
lvremove /dev/vg0/lv_ss_snap  








