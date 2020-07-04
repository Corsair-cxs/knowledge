# FileSystem - 文件系统

文件系统，也就是将物理磁盘抽象为一个文件树，提供给用户使用的一个磁盘管理系统。

目前主流的三大操作系统，主流的文件系统都不同。

1. Linux: 主流是 ext4，另外

## Union File System / Disk Arrays

一块磁盘的容量总是有限的，而且也有单点问题——万一磁盘坏了，数据就完蛋了。

为了获得更大的存储空间，或者实现磁盘的高可用，就需要用到磁盘阵列（Disk Arrays）。

磁盘阵列是一种将多个磁盘合并成一个文件系统来使用的技术，最常用于 NAS(Network Attached Storage) 技术。
通过数据的分割写入并发读取等技术，磁盘阵列能获得比单块磁盘更快的读写速度。
通过增设冗余，磁盘阵列可以容忍一定的磁盘损坏。

代表性的技术有：

1. RAID
2. mergefs


### 参考文档

- [raid5 磁盘阵列真的不安全么？ - 知乎](https://www.zhihu.com/question/20164654/answer/348274179)


## Distributed File System

磁盘阵列是在一台主机上插多个磁盘，通过磁盘阵列卡或者软件的方式将多个磁盘合并成一个存储空间。

但是规模再增大，我们会发现一台主机的存储空间仍然不够，性能可能也无法满足要求。
另外我们发现主机或磁盘阵列卡本身，也存在单点问题。

为了解决这个问题，就需要将数据存储到多台主机，多个磁盘阵列中。使用网络连接这些主机，得到的文件系统被称作「分布式文件存储系统」。

代表性的开源技术：

1. Ceph
2. GlusterFS


## 文件传输协议

有了上述文件系统后，远程的应用如果想要方便地使用这些存储资源，就需要用到文件传输协议。

日常接触得最多的，肯定是 HTTP/FTP 协议。但是 HTTP 只是能提供单纯的文件传输功能，FTP 也只提供有限的文件查询功能。

我们希望的，是像使用本地的存储空间一样地去使用远程存储资源。这种技术叫网络文件系统。

使用的最多的网络文件协议有：

1. NFS 协议：Linux 世界（服务器）使用广泛，比如 Kubernetes 就支持挂载 NFS 存储。
1. SMB 协议：Windows 世界使用广泛，各种 Windows 共享文件夹，都是使用的这个协议。
    - linux发行版/macos/windows 的文件管理器，都支持直接访问 `smb://` 存储。