---
date: 2026-01-28
categories:
  - arch
draft: false
comments: true
---

# 制作 Arch Linux 系统启动盘
<!-- more -->
首先安装 archiso 包
``` bash
sudo pacman -Syy archiso
```
它提供了两种配置方案，一种是只包含基本系统的 baseline，一种是可以制作定制 ISO 的 releng。要制作维护用 ISO，当然是复制 releng 配置啦。
``` bash
cp -r /usr/share/archiso/configs/releng/ archlive
cd archlive
```
定制
先来了解下各个文件的用途:

- build.sh - 用于制作镜像的自动化脚本，可以在这里修改一些名称变量或制作过程的逻辑。
- packages.x86_64 - 一份要安装的包列表，一行一个。
- pacman.conf - pacman 的配置文件
- airootfs - Live 系统的 rootfs，除了安装的包之外，其他的定制（以及启动执行脚本等）都在这里。遵循 rootfs 的目录规则。
- efiboot / syslinux / isolinux 用于设置 BIOS / EFI 启动的配置。

将 [archlinuxcn] 仓库加入 pacman.conf：
``` bash
[archlinuxcn]
Server = https://cdn.repo.archlinuxcn.org/$arch
```
然后修改 packages.x86_64，加入 archlinuxcn-keyring 和其他需要预安装的包：
``` bash
archlinuxcn-keyring
htop
iftop
iotop
ipmitool
```
要添加内核参数，需要加启动参数 nvidia nvidia_drm.modeset=1 nouveau.modeset=0

修改文件 syslinux/archiso_sys-linux.cfg 文件，在启动参数后加 nvidia nvidia_drm.modeset=1 nouveau.modeset=0，像这样：
``` bash
 APPEND archisobasedir=%INSTALL_DIR% archisosearchuuid=%ARCHISO_UUID% nvidia nvidia_drm.modeset=1 nouveau.modeset=0
```
同样，还需要修改 efiboot/loader/entries/archiso-linuxconf 和 archiso-speech-linux.conf 的启动参数。在 options 行添加
``` bash
options  archisobasedir=%INSTALL_DIR% archisosearchuuid=%ARCHISO_UUID% nvidia nvidia_drm.modeset=1 nouveau.modeset=0
```
创建工作目录和输出目录
``` bash
sudo mkdir -p /mnt/work /mnt/out
```
开始制作镜像文件
``` bash
sudo mkarchiso -v -w /mnt/work -o /mnt/out / ./
```