# 其他问题

## 日志存储路径更换

在生产环境的时候，一般服务器的磁盘是500G，大概几个月之后，磁盘就会wazuh的告警日志给占满了，所以需要日志存储跟管理端运行的程度分隔开，那么就需要对日志存储的路径进行更换。

首先，虚拟机新建一个20G的硬盘。

![](../.gitbook/assets/image%20%28213%29.png)

查看硬盘信息，发现有一个20G的硬盘/dev/sdb。

```text
[root@wazuh-manager ~]# fdisk -l

Disk /dev/sda: 42.9 GB, 42949672960 bytes, 83886080 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000c8e8e

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    83886079    40893440   8e  Linux LVM

Disk /dev/sdb: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-root: 39.7 GB, 39720058880 bytes, 77578240 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-swap: 2147 MB, 2147483648 bytes, 4194304 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

格式化硬盘。

```text
[root@wazuh-manager ~]# mkfs.xfs /dev/sdb
meta-data=/dev/sdb               isize=512    agcount=4, agsize=1310720 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=5242880, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

```

挂载硬盘到/data/目录

```text
[root@wazuh-manager ~]# mkdir /data
[root@wazuh-manager ~]# mount /dev/sdb /data
[root@wazuh-manager ~]# df -h /data
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdb         20G   33M   20G   1% /data
```

查询硬盘文件系统类型。

```text
[root@wazuh-manager ~]# blkid
/dev/sda1: UUID="02e56204-27c0-4fd5-ba63-a460226512aa" TYPE="xfs" 
/dev/sda2: UUID="8EECXG-g8EF-6cM5-15cC-mzTa-sUUN-egLDCp" TYPE="LVM2_member" 
/dev/sdb: UUID="6ee922a9-02e2-4da5-a7ed-87c8a317f49a" TYPE="xfs" 
/dev/mapper/centos-root: UUID="816354ce-6621-4170-9470-52a0cb3eabb3" TYPE="xfs" 
/dev/mapper/centos-swap: UUID="b716a11f-9f6c-4066-bf2f-40cbff71d1eb" TYPE="swap" 
```

设置开机挂载硬盘，接着重启服务器，就会发现自动挂载硬盘上去。

```text
[root@wazuh-manager ~]# cat /etc/fstab 
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=02e56204-27c0-4fd5-ba63-a460226512aa /boot                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
#修改自己的UUID
UUID=6ee922a9-02e2-4da5-a7ed-87c8a317f49a /data    xfs  defaults  1  1

```

接着在/data目录新建wazuh文件夹。

```text
[root@wazuh-manager ~]# mkdir /data/wazuh
```

迁移logs文件夹里面的日志内容到/data/wazuh目录下面。

```text
[root@wazuh-manager ~]# mv /var/ossec/logs/* /data/wazuh/
```

mount --bind命令是将前一个目录挂载到后一个目录上，所有对后一个目录的访问其实都是对前一个目录的访问。

```text
[root@wazuh-manager ~]# mount --bind /data/wazuh/ /var/ossec/logs
```

设置将日志开机挂载/data/wazuh

```text
[root@wazuh-manager ~]# echo "/data/wazuh /var/ossec/logs/ none defaults,bind 0 0" >> /etc/fstab
```

重启管理端服务器，查看`/data/wazuh/ossec.log`，可以看到日志正常收集。

![](../.gitbook/assets/image%20%28212%29.png)







