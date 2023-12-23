
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
> 容量大于 2T 时，需要使用 **GPT** 分区方式，而 `fdisk` 并不支持
> 
> GPT 分区最大支持 **18EB** 容量

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

执行 `mklabel gpt` 将磁盘分区类型设置为 `gpt`，再次执行 `p`：

```bash
(parted) p
Model: ATA ST8000VN004-3CP1 (scsi)
Disk /dev/sda: 8002GB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags:

Number  Start  End  Size  File system  Name  Flags
```

`Partition Table: gpt` ，磁盘分区类型设置完毕

最后依次执行 `unit s`，`mkpart opt 2048s 100%` 新建分区：

```bash
(parted) unit s
(parted) mkpart opt 2048s 100%
```

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

``