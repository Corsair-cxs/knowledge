# OpenWRT 路由专用系统


## 在 PVE 配置 OpenWRT 虚拟机

首先是虚拟机安装流程：

1. 下载最新的 OpenWRT 镜像
   1. 不熟悉 OpenWRT 的话建议在恩山论坛下载高排名的小白固件，比如 [sirpdboy/openwrt](https://github.com/sirpdboy/openwrt)
   2. 如果打算使用官方固件，为了避免踩坑，建议回退至少一个大版本。比如说现在最新版为 22.03.2，那就下载 [21.02.3//targets/x86/64/](https://downloads.openwrt.org/releases/21.02.3//targets/x86/64/) 或者更早的版本
   3. 请下载其中第一个镜像文件 `generic-ext4-combined-efi.img.gz`，此镜像支持 EFI 启动，也带了一个 BIOS 分区。
2. 在 PVE 中手动创建虚拟机，BIOS 使用 OVMF(UEFI) ，然后把默认磁盘卸载并删除
   1. OVMF 模式下会自动生成一个 uefi 硬盘，不需要去动它
   2. 为了确保性能，cpu 请选用 host 模式，配置建议给到 1C/0.5G - 2c/1G
3. 使用命令将下载的 `img` 文件导入到新建的虚拟机并挂载，然后将该磁盘增大 1G
   1. 导入命令（需要将 106 修改为虚拟机 ID）：`qm importdisk 106 openwrt-22.03.2-x86-64-generic-ext4-combined-efi.img local-lvm`
4. 修改启动顺序为仅从新硬盘启动：scsi0
5. 启动主机
6. 一开始会启动失败进入 UEFI shell，因为 openwrt 不支持「secure boot」
   1. 解决方法：使用 `exit` 退出 UEFI shell 后会进入 UEFI 界面，将「secure boot」关掉，保存并重启，即可正常启动 OpenWRT


虚拟机安装完成后，通过 `ip link` 能看到相应的端口，但是会发现并未使用 DHCP 自动配置任何 IP 地址，也就无法通过网络连接。需要先手动配置网络：

1. 修改 `/etc/config/network` 位置文件，找到 `lan` 配置，将其参数设置为与本地局域网一致，就能实现加入到局域网中。

示例配置：

```shell
root@OpenWrt:~# cat /etc/config/network

config interface 'loopback'
	option device 'lo'
	option proto 'static'
	option ipaddr '127.0.0.1'
	option netmask '255.0.0.0'

config globals 'globals'
	option ula_prefix 'fd0c:702e:10d6::/48'

config device
	option name 'br-lan'
	option type 'bridge'
	list ports 'eth0'

# 修改这部分配置，其中 ipaddr 为 openwrt 自身的 IP，其他参数要与整个局域网一致
config interface 'lan'
	option device 'br-lan'
	option proto 'static'
	option ipaddr '192.168.5.201'
	option netmask '255.255.255.0'
	option ip6assign '60'
   # 如下两行需要手动添加
	option gateway 192.168.5.1
	option dns 114.114.114.114 8.8.8.8

config interface 'wan'
	option device 'eth1'
	option proto 'dhcp'

config interface 'wan6'
	option device 'eth1'
	option proto 'dhcpv6'
```

修改完成后 `/etc/init.d/network restart` 重启网络服务，然后就能直接通过 `http://192.168.5.201` 访问此 OpenWRT 虚拟机啦，命令行有提示初始密码为 `passwd`，进入 Web 页面后需要自行修改。 

## 常用插件

使用 OpenWRT 作为软路由系统，好像唯二的优势就在于：

1. 它对网络的支持比较好
2. 拥有丰富的网络相关插件
3. 提供一个比较完善的 Web UI 控制台，能方便地配置各种网络与安全相关参数。
4. 如果你熟悉 openwrt 的使用，可以复用之前的经验

列举一些常用插件如下：

- 一款漂亮的 OpenWRT Web 皮肤：[luci-theme-argon](https://github.com/jerrykuku/luci-theme-argon)
- OpenClash 网络代理工具：[OpenClash](https://github.com/vernesong/OpenClash)
- 解锁网易云灰色歌曲：[luci-app-unblockneteasemusic](https://github.com/cnsilvan/luci-app-unblockneteasemusic)


>画外：额好像上述这几个插件都没什么特点，换个操作系统照样能搞...考虑拿 Ubuntu Server 取代掉它...

>画外：自己用 Ubuntu 搞，要装个东西太多了，最后还是恩山论坛上找了个预装了各种插件的流行固件...特别省心 emmm

