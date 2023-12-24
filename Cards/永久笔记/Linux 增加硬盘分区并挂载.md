---
title: Linux 增加硬盘并挂载
date created: 2023-12-23
date modified: 2023-12-23
---

## 背景

需要插入一块 **8T** 的机械硬盘

系统信息：

|CPU|系统|
|---|---|
|i5-8500T|Debian 12|

## 查看硬盘信息

首先需要使用 `lsblk` 查看当前硬盘信息，找到新增的硬盘：

```bash
norah1to@norah1to-nas-debian:~$ lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda           8:0    0   7.3T  0 disk
nvme0n1     259:0    0 238.5G  0 disk
├─nvme0n1p1 259:1    0   512M  0 part /boot/efi
├─nvme0n1p2 259:2    0   237G  0 part /
└─nvme0n1p3 259:3    0   977M  0 part [SWAP]
```

从输出信息中可以得知，当前有两块硬盘：

- sda
- nvme0n1

**nvme0n1** 下有分区信息，说名这块是已有的硬盘，这里是我的系统盘

**sda** 下空空如也，并且容量也是 **8T**，可以断定是新插入的硬盘

## 分区

硬盘**小于 2T** 时，一般使用 `fdisk` 进行分区

**大于 2T** 时，则需要使用 `parted`

> [!question] 为什么
> 容量大于 2T 时，需要使用 **GPT** 格式分区，而 `fdisk` 并不支持
> 
> GPT 格式分区最大支持 **18EB** 容量

先安装 `parted`：

```bash
sudo apt-get install parted -y
```

进入 `parted` 命令行交互环境：

```bash
sudo parted /dev/sda
```

> [!question] /dev/sda
> `/dev/sd* `目录为所有 scsi 磁盘驱动程序的接口，因此 `/dev/sda` 就是我们的磁盘的具体位置
> 
> 更详细的目录说明可以参考：[Linux基础一：各目录及介绍 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/647466955)

执行 `p` 指令查看当前磁盘分区信息：

```bash
(parted) p
Error: /dev/sda: unrecognised disk label
Model: ATA ST8000VN004-3CP1 (scsi)
Disk /dev/sda: 8002GB
Sector size (logical/physical): 512B/4096B
Partition Table: unknown
Disk Flags:
```

`Partition Table: unknown` ，说明该磁盘并没有分区表

执行 `mklabel gpt` 将磁盘分区表类型设置为 `gpt`，再次执行 `p`：

```bash
(parted) p
Model: ATA ST8000VN004-3CP1 (scsi)
Disk /dev/sda: 8002GB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags:

Number  Start  End  Size  File system  Name  Flags
```

`Partition Table: gpt` ，磁盘分区表类型设置完毕

最后依次执行 `unit s`，`mkpart opt 2048s 100%` 新建分区：

```bash
(parted) unit s
(parted) mkpart opt 2048s 100%
```

> [!question] 这两个命令的作用
> `unit` 用于指定磁盘的计量单位，`unit s` 表示使用磁柱为单位，一般都使用这个
> 
> `mkpart` 用于新建分区，具体参数为 `mkpart {分区类型} {文件类型} {分区起点} {分区终点}`

再次执行 `p` 检查分区信息：

```bash
(parted) p
Model: ATA ST8000VN004-3CP1 (scsi)
Disk /dev/sda: 15628053168s
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags:

Number  Start  End           Size          File system  Name  Flags
 1      2048s  15628052479s  15628050432s               opt
```

执行 `q` 退出 `parted`，接着执行 `lsblk` 检查磁盘分区信息：

```bash
norah1to@norah1to-nas-debian:~$ lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda           8:0    0   7.3T  0 disk
└─sda1        8:1    0   7.3T  0 part
nvme0n1     259:0    0 238.5G  0 disk
├─nvme0n1p1 259:1    0   512M  0 part /boot/efi
├─nvme0n1p2 259:2    0   237G  0 part /
└─nvme0n1p3 259:3    0   977M  0 part [SWAP]
```

可以看到 `sda` 磁盘新增了一个分区 `sda1`，`1` 对应我们上面的分区号，至此分区成功

## 格式化分区

执行 `mkfs -t {格式} /dev/{分区名}` 即可

```bash
norah1to@norah1to-nas-debian:~$ sudo mkfs -t ext4 /dev/sda1
mke2fs 1.47.0 (5-Feb-2023)
Creating filesystem with 1953506304 4k blocks and 244191232 inodes
Filesystem UUID: b0039d64-0828-493f-a4e2-b748e38067f9
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968,
        102400000, 214990848, 512000000, 550731776, 644972544, 1934917632

Allocating group tables: done
Writing inode tables: done
Creating journal (262144 blocks): done
Writing superblocks and filesystem accounting information: done
```

## 挂载分区

先新建一个文件夹作为挂载点：

```bash
sudo mkdir /media/8ta
```

挂载：

```bash
sudo mount /dev/sda1 /media/8ta
```

查看挂载结果：

```bash
norah1to@norah1to-nas-debian:~$ df -TH
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs   17G     0   17G   0% /dev
tmpfs          tmpfs     3.4G  812k  3.4G   1% /run
/dev/nvme0n1p2 ext4      250G  2.4G  235G   2% /
tmpfs          tmpfs      17G     0   17G   0% /dev/shm
tmpfs          tmpfs     5.3M     0  5.3M   0% /run/lock
/dev/nvme0n1p1 vfat      536M  6.2M  530M   2% /boot/efi
tmpfs          tmpfs     3.4G     0  3.4G   0% /run/user/1000
/dev/sda1      ext4      8.0T   29k  7.6T   1% /media/8ta # 成功
```

## 开机自动挂载

先获取磁盘分区的 **UUID**：

```bash
sudo blkid /dev/sda1 | grep -o UUID[^\s]*
UUID="xxxx-xxxx-xxxx-xxxx" BLOCK_SIZE="4096" TYPE="ext4" PARTLABEL="opt" PARTUUID="xxxx-xxxx-xxxx-xxxx"
```

然后在 `/etc/fstab` 中添加一行挂载信息：

```bash
UUID=xxxx-xxxx-xxxx-xxxx /media/8ta ext4 defaults 0 2
```

`fstab` 的说明：

<file system>    <mount point>    <type>    <options>    <dump>    <pass>   
        1                      2                 3             4               5             6

1. 指代文件系统的设备名，最初，该字段只包含待挂载分区的设备名（如/dev/sda1）。现在，除设备名外，还可以包含LABEL或UUID
2. 文件系统挂载点，文件系统包含挂载点下整个目录树结构里的所有数据，除非其中某个目录又挂载了另一个文件系统
3. 文件系统类型，下面是多数常见文件系统类型（ext3,tmpfs,devpts,sysfs,proc,swap,vfat）
4. mount命令选项，mount选项包括noauto（启动时不挂载该文件系统）和ro（只读方式挂载文件系统）等。在该字段里添加用户或属主选项，即可允许该用户挂载文件系统。多个选项之间必须用逗号隔开。其他选项的相关信息可参看mount命令手册页（-o选项处）
5. 转储文件系统，该字段只在用dump备份时才有意义。数字1表示该文件系统需要转储，0表示不需要转储
6. 文件系统检查，该字段里的数字表示文件系统是否需要用fsck检查。0表示不必检查该文件系统，数字1示意该文件系统需要先行检查（用于根文件系统）。数字2则表示完成根文件系统检查后，再检查该文件系统