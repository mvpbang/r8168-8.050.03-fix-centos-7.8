# r8168-8.050.03-fix-centos-7.8
修复编译错误

# env
- centos 7.8

```
[root@sy03 ~]# cat /etc/*release
CentOS Linux release 7.8.2003 (Core)
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"

CentOS Linux release 7.8.2003 (Core)
CentOS Linux release 7.8.2003 (Core)
```

```
lspci -v 
01:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller (rev 06)
	Subsystem: Acer Incorporated [ALI] Device 102a
	Flags: bus master, fast devsel, latency 0, IRQ 125
	I/O ports at e000 [size=256]
	Memory at ef000000 (64-bit, non-prefetchable) [size=4K]
	Memory at e0000000 (64-bit, prefetchable) [size=16K]
	Capabilities: [40] Power Management version 3
	Capabilities: [50] MSI: Enable+ Count=1/1 Maskable- 64bit+
	Capabilities: [70] Express Endpoint, MSI 01
	Capabilities: [b0] MSI-X: Enable- Count=4 Masked-
	Capabilities: [d0] Vital Product Data
	Capabilities: [100] Advanced Error Reporting
	Capabilities: [140] Virtual Channel
	Capabilities: [160] Device Serial Number 01-00-00-00-68-4c-e0-00
	Kernel driver in use: r8168
	Kernel modules: r8168
```

- release-centos7.8-r8168-ko/r8168.ko
> 基于centos7.8构建的ko

## buiding 
```
//requirement
yum install -y gcc gcc-c++ kernel-devl

//make
make modules

//cp ko install && mv old ko
cp src/r8168.ko /usr/lib/modules/3.10.0-1127.el7.x86_64/kernel/drivers/net/ethernet/realtek/
mv /usr/lib/modules/3.10.0-1127.el7.x86_64/kernel/drivers/net/ethernet/realtek/r8169.ko.xz{,_bak}
depmod -a
modprobe r8168

//update img
cp initramfs-3.10.0-1127.el7.x86_64.img initramfs-3.10.0-1127.el7.x86_64.img_bak
dracut -f /boot/initramfs-3.10.0-1127.el7.x86_64.img $(uname -r)

//reboot && testing
reboot
ethtool -i enp1s0

[root@sy03 ~]# ethtool -i enp1s0
driver: r8168
version: 8.050.03-NAPI
firmware-version: 
expansion-rom-version: 
bus-info: 0000:01:00.0
supports-statistics: yes
supports-test: no
supports-eeprom-access: no
supports-register-dump: yes
supports-priv-flags: no
```
