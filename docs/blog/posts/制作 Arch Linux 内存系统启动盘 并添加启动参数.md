---
date: 2026-01-28
categories:
  - arch
draft: false
comments: true
---

# 制作 Arch Linux 内存系统启动盘 并添加启动参数
<!-- more -->
首先安装**archiso**包
```sudo pacman -Syy archiso```
它提供了两种配置方案，一种是只包含基本系统的 baseline，一种是可以制作定制 ISO 的 releng。要制作维护用 ISO，当然是复制 releng 配置啦。
```cp -r /usr/share/archiso/configs/releng/ archlive && cd archlive```
修改文件 syslinux/archiso_sys-linux.cfg 文件，在启动参数后加 copytoram，像这样：
> APPEND archisobasedir=%INSTALL_DIR% archisosearchuuid=%ARCHISO_UUID% nvidia nvidia_drm.modeset=1 nouveau.modeset=0

同样，还需要修改 efiboot/loader/entries/archiso-linuxconf 和 archiso-speech-linux.conf的启动参数。在 options 行添加
> options  archisobasedir=%INSTALL_DIR% archisosearchuuid=%ARCHISO_UUID% nvidia nvidia_drm.modeset=1 nouveau.modeset=0

创建工作目录和输出目录
```sudo mkdir -p /mnt/work /mnt/out```
开始制作镜像文件
```sudo mkarchiso -v -w /mnt/work -o /mnt/out / ./```

