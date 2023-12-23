
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
> 所有新增的