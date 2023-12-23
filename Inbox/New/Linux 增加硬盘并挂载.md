
## 系统信息

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

**nvme0n1** 下有分区信息，说名这块是已有的系统盘